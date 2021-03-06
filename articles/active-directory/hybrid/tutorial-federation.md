---
title: 'Didacticiel : Fédérer un environnement de forêt AD unique dans Azure | Microsoft Docs'
description: Montre comment configurer un environnement d’identité hybride à l’aide de la fédération.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/16/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a51175d192a5afb1f84f8d0ed2de9796f198f82d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58102398"
---
# <a name="tutorial-federate-a-single-ad-forest-environment-to-the-cloud"></a>Didacticiel : Fédérer un environnement de forêt AD unique dans le cloud

![Créer](media/tutorial-federation/diagram.png)

Ce didacticiel vous guidera dans la création d’un environnement d’identité hybride à l’aide de la fédération.  Cet environnement peut ensuite servir à des fins de test ou pour se familiariser avec le fonctionnement d’une identité hybride.

## <a name="prerequisites"></a>Prérequis
Voici les conditions préalables requises pour suivre ce didacticiel.
- Un ordinateur où [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) est installé.  Il est recommandé d’utiliser [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) ou [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- Un [abonnement Azure](https://azure.microsoft.com/free)
- - Une [carte réseau externe](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) pour autoriser la machine virtuelle à communiquer avec Internet.
- Une copie de Windows Server 2016
- Un [domaine personnalisé](../../active-directory/fundamentals/add-custom-domain.md) qui peut être vérifié

> [!NOTE]
> Ce didacticiel utilise des scripts PowerShell pour vous permettre de créer l’environnement le plus vite possible.  Chacun des scripts utilise les variables déclarées au début des scripts.  Vous pouvez et devez modifier les variables pour qu’elles reflètent votre environnement.
>
>Les scripts utilisés créent un environnement Active Directory général avant d’installer Azure AD Connect.  Elles sont pertinentes pour l’ensemble des didacticiels.
>
> Des copies des scripts PowerShell utilisés dans ce didacticiel sont disponibles sur GitHub, [ici](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle
Tout d’abord, pour obtenir un environnement d’identité hybride fonctionnel, il faut créer une machine virtuelle qui sera utilisée en tant que serveur Active Directory local.  

>[!NOTE]
>Si vous n’avez jamais exécuté de script dans PowerShell sur votre machine hôte, vous devrez exécuter `Set-ExecutionPolicy remotesigned` et répondre Oui dans PowerShell, avant d’exécuter des scripts.

Effectuez les actions suivantes :

1. Ouvrez PowerShell ISE en tant qu’administrateur.
2. Exécutez le script suivant.

```powershell
#Declare variables
$VMName = 'DC1'
$Switch = 'External'
$InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
$Path = 'D:\VM'
$VHDPath = 'D:\VM\DC1\DC1.vhdx'
$VHDSize = '64424509440'

#Create New Virtual Machine
New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

#Set the memory to be non-dynamic
Set-VMMemory $VMName -DynamicMemoryEnabled $false

#Add DVD Drive to Virtual Machine
Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

#Mount Installation Media
$DVDDrive = Get-VMDvdDrive -VMName $VMName

#Configure Virtual Machine to Boot from DVD
Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
```

## <a name="complete-the-operating-system-deployment"></a>Terminer le déploiement du système d’exploitation
Pour terminer la création de la machine virtuelle, vous devez terminer l’installation du système d’exploitation.

1. Gestionnaire Hyper-V, double-clic sur la machine virtuelle
2. Cliquez sur le bouton Démarrer.
3. Vous êtes invité à appuyer sur n’importe quelle touche pour démarrer à partir du CD ou DVD. Faites-le.
4. Sur l’écran de démarrage Windows Server, sélectionnez votre langue et cliquez sur **Suivant**.
5. Cliquez sur **Installer maintenant**.
6. Saisissez votre clé de licence et cliquez sur **Suivant**.
7. Acceptez les termes du contrat de licence et cliquez sur **Suivant**.
8. Sélectionnez **Personnalisé :  installer Windows uniquement (avancé)**
9. Cliquez sur **Suivant**
10. Une fois l’installation terminée, redémarrez la machine virtuelle, connectez-vous et exécutez les mises à jour Windows pour vous assurer que la machine virtuelle est à jour.  Installez les dernières mises à jour.

## <a name="install-active-directory-pre-requisites"></a>Installer les prérequis pour Active Directory
Maintenant que la machine virtuelle est en cours d’exécution, il faut effectuer quelques opérations avant d’installer Active Directory.  Autrement dit, il faut renommer la machine virtuelle, définir une adresse IP statique et des informations DNS, et installer les outils d’administration de serveur distant.   Effectuez les actions suivantes :

1. Ouvrez PowerShell ISE en tant qu’administrateur.
2. Exécutez `Set-ExecutionPolicy remotesigned` et répondez Oui à tous les [A].  Appuyez sur Entrée.
3. Exécutez le script suivant.

```powershell
#Declare variables
$ipaddress = "10.0.1.117" 
$ipprefix = "24" 
$ipgw = "10.0.1.1" 
$ipdns = "10.0.1.117"
$ipdns2 = "8.8.8.8" 
$ipif = (Get-NetAdapter).ifIndex 
$featureLogPath = "c:\poshlog\featurelog.txt" 
$newname = "DC1"
$addsTools = "RSAT-AD-Tools" 

#Set static IP address
New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

# Set the DNS servers
Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

#Rename the computer 
Rename-Computer -NewName $newname -force 

#Install features 
New-Item $featureLogPath -ItemType file -Force 
Add-WindowsFeature $addsTools 
Get-WindowsFeature | Where installed >>$featureLogPath 

#Restart the computer 
Restart-Computer
```

## <a name="create-a-windows-server-ad-environment"></a>Créer un environnement Windows Server AD
Maintenant que la machine virtuelle a été créée et renommée, et qu’elle dispose d’une adresse IP statique, nous pouvons installer et configurer Active Directory Domain Services.  Effectuez les actions suivantes :

1. Ouvrez PowerShell ISE en tant qu’administrateur.
2. Exécutez le script suivant.

```powershell 
#Declare variables
$DatabasePath = "c:\windows\NTDS"
$DomainMode = "WinThreshold"
$DomainName = "contoso.com"
$DomaninNetBIOSName = "CONTOSO"
$ForestMode = "WinThreshold"
$LogPath = "c:\windows\NTDS"
$SysVolPath = "c:\windows\SYSVOL"
$featureLogPath = "c:\poshlog\featurelog.txt" 
$Password = "Pass1w0rd"

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $Password -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>Créer un utilisateur Windows Server AD
Maintenant que nous avons un environnement Active Directory, nous avons besoin d’un compte de test.  Ce compte sera créé dans l’environnement Active Directory local, puis synchronisé avec Azure AD.  Effectuez les actions suivantes :

1. Ouvrez PowerShell ISE en tant qu’administrateur.
2. Exécutez le script suivant.

```powershell 
#Declare variables
$Givenname = "Allie"
$Surname = "McCray"
$Displayname = "Allie McCray"
$Name = "amccray"
$Password = "Pass1w0rd"
$Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


#Create the user
New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

#Set the password to never expire
Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
```

## <a name="create-a-certificate-for-ad-fs"></a>Créer un certificat pour AD FS
Nous allons maintenant créer un certificat SSL qui sera utilisé par AD FS.  Il s’agira d’un certificat auto-signé, destiné uniquement aux tests.  Microsoft ne recommande pas à l’utilisation d’un certificat auto-signé dans un environnement de production. Effectuez les actions suivantes :

1. Ouvrez PowerShell ISE en tant qu’administrateur.
2. Exécutez le script suivant.

```powershell 
#Declare variables
$DNSname = "adfs.contoso.com"
$Location = "cert:\LocalMachine\My"

#Create certificate
New-SelfSignedCertificate -DnsName $DNSname -CertStoreLocation $Location
```

## <a name="create-an-azure-ad-tenant"></a>Créer un locataire Azure AD
Nous devons maintenant créer un locataire Azure AD pour synchroniser nos utilisateurs vers le cloud.  Pour créer un client Azure AD, procédez comme suit.

1. Accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec un compte qui dispose d’un abonnement Azure.
2. Cliquez sur l’**icône plus (+)** et recherchez **Azure Active Directory**.
3. Dans la liste des résultats, sélectionnez sur **Azure Active Directory**.
4. Sélectionnez **Créer**.</br>
![Créer](media/tutorial-password-hash-sync/create1.png)</br>
5. Indiquez le **nom de l’organisation** avec le **nom de domaine initial**. Sélectionnez ensuite **Créer**. Votre annuaire est alors créé.
6. Une fois cette opération terminée, cliquez sur **ce lien** pour gérer l’annuaire.

## <a name="create-a-global-administrator-in-azure-ad"></a>Créer un administrateur général dans Azure AD
Maintenant que nous avons un locataire Azure AD, nous allons créer un compte d’administrateur général.  Ce compte est utilisé pour créer le compte de connecteur Azure AD lors de l’installation d’Azure AD Connect.  Le compte de connecteur Azure AD sert à écrire des informations dans Azure AD.   Pour créer le compte d’administrateur général, procédez comme suit.

1.  Sous **Gérer**, sélectionnez **Utilisateurs**.</br>
![Créer](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Sélectionnez **Tous les utilisateurs**, puis **+ Nouvel utilisateur**.
3.  Renseignez un nom et un nom d’utilisateur pour cet utilisateur. Il s’agit de votre administrateur général pour le locataire. Vous devez également définir le **rôle d’annuaire** sur **Administrateur général**. Vous pouvez également afficher le mot de passe temporaire. Lorsque vous avez terminé, sélectionnez **Créer**.</br>
![Créer](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Une fois cette opération terminée, ouvrez une nouvelle fenêtre de navigateur web et connectez-vous à myapps.microsoft.com en utilisant le nouveau compte d’administrateur général et le mot de passe temporaire.
5. Remplacez le mot de passe de l’administrateur général par quelque chose de facile à retenir.

## <a name="add-the-custom-domain-name-to-your-directory"></a>Ajoutez le nom de domaine personnalisé à votre annuaire.
Maintenant que nous disposons d’un locataire et d’un administrateur général, nous devons ajouter notre domaine personnalisé afin qu’Azure puisse le vérifier.  Effectuez les actions suivantes :

1. De retour sur le [Portail Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview), veillez à fermer le panneau **Tous les utilisateurs**.
2. Sur la gauche, sélectionnez **Noms de domaine personnalisé**.
3. Sélectionnez **Ajouter un domaine personnalisé**.</br>
![Fédération](media/tutorial-federation/custom1.png)</br>
4. Sous **Noms de domaine personnalisé**, saisissez le nom de votre domaine personnalisé dans la zone, et cliquez sur **Ajouter un domaine**.
5. À l’écran du nom de domaine personnalisé, vous obtiendrez une information MX ou TXT.  Cette information doit être ajoutée aux informations DNS du registre du domaine sous votre domaine.  Vous devez donc aller dans votre registre de domaine, et saisir l’information TXT ou MX dans les réglages DNS de votre domaine.  Cela permettra à Azure de vérifier votre domaine.  Cette opération peut durer jusqu’à 24 heures.  Pour plus d’informations, consultez la documentation [Ajouter un domaine personnalisé](../../active-directory/fundamentals/add-custom-domain.md).</br>
![Fédération](media/tutorial-federation/custom2.png)</br>
6. Pour vous assurer qu’il a été vérifié, cliquez sur le bouton Vérifier.</br>
![Fédération](media/tutorial-federation/custom3.png)</br>

## <a name="download-and-install-azure-ad-connect"></a>Télécharger et installer Azure AD Connect
Il est maintenant temps de télécharger et d’installer Azure AD Connect.  Une fois l’installation terminée, nous aborderons l’installation rapide.  Effectuez les actions suivantes :

1. Télécharger [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Accédez à **AzureADConnect.msi**et double-cliquez sur ce fichier.
3. Sur l'écran d’accueil, sélectionnez la case pour accepter les termes du contrat de licence et cliquez sur **Continuer**.  
4. Sur l’écran Paramètres Express, cliquez sur **Personnaliser**.  
5. Sur l’écran Installer les composants nécessaires. Cliquez sur **Installer**.  
6. Sur l’écran Connexion utilisateur, sélectionnez **Fédération avec AD FS** et cliquez sur **Suivant**.
![Fédération](media/tutorial-federation/fed1.png)

1. Sur l’écran Connexion à Azure AD, entrez le nom d’utilisateur et le mot de passe de l’administrateur général que nous avons créé ci-dessus, et cliquez sur **Suivant**.
2. Sur l’écran Connecter vos répertoires, cliquez sur **Ajouter un répertoire**.  Sélectionnez ensuite **Créer un compte AD** et saisissez le nom d’utilisateur et le mot de passe contoso\Administrator et cliquez sur **OK**.
3. Cliquez sur **Suivant**.
4. Sur l’écran Configuration de la connexion à Azure AD, sélectionnez **Continuer sans faire correspondre tous les suffixes UPN à des domaines vérifiés** et cliquez sur **Suivant**.
5. Sur l’écran Filtrage domaine et unité organisationnelle, cliquez sur **Suivant**.
6. Sur l’écran Identification unique de vos utilisateurs, cliquez sur **Suivant**.
7. Sur l’écran Filtrer les utilisateurs et appareils, cliquez sur **Suivant**.
8. Sur l’écran Fonctionnalités facultatives, cliquez sur **Suivant**.
9. Sur la page Informations d’identification de l’administrateur de domaine, saisissez le nom d’utilisateur et le mot de passe contoso\Administrator, et cliquez sur **Suivant**.
10. Dans l’écran Batterie de serveurs AD FS, assurez-vous que **Configurer une nouvelle batterie de serveurs AD FS** est sélectionné.
11. Sélectionnez **Utiliser un certificat installé sur les serveurs de fédération** et cliquez sur **Parcourir**.
12. Entrez DC1 dans la zone de recherche, et sélectionnez-le lorsque vous l’avez trouvé.  Cliquez sur **OK**.
13. Dans la liste déroulante **Fichier de certificat**, sélectionnez le certificat **adfs.contoso.com** créé précédemment.  Cliquez sur **Suivant**.
![Fédération](media/tutorial-federation/fed2.png)

1. Dans l’écran Serveur AD FS, cliquez sur **Parcourir**. Entrez DC1 dans la zone de recherche et sélectionnez-le lorsque vous l’avez trouvé.  Cliquez sur **OK**.  Cliquez sur **Suivant**.
![Fédération](media/tutorial-federation/fed3.png)

1. Dans l’écran Serveurs proxy d’application Web, cliquez sur **Suivant**.
2. Dans l’écran Compte de service AD FS, saisissez le nom d’utilisateur et le mot de passe contoso\Administrator, puis cliquez sur **Suivant**.
3. Dans l’écran Domaine Azure AD, sélectionnez votre domaine personnalisé vérifié dans la liste déroulante, puis cliquez sur **Suivant**.
4. Sur l’écran Prêt à configurer, cliquez sur **Installer**.
5. Une fois l’installation terminée, cliquez sur **Quitter**.
6. Une fois l’installation terminée, déconnectez-vous puis reconnectez-vous avant d’utiliser le gestionnaire Synchronization Service Manager ou l’éditeur de règles de synchronisation.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Vérifier les utilisateurs créés et l’exécution de la synchronisation
Nous allons maintenant vérifier que les utilisateurs de l’annuaire local ont été synchronisés et existent maintenant dans le locataire Azure AD.  Cette opération peut prendre quelques heures.  Pour vérifier que les utilisateurs sont synchronisés, procédez comme suit.


1. Accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec un compte qui dispose d’un abonnement Azure.
2. À gauche, sélectionnez **Azure Active Directory**
3. Sous **Gérer**, sélectionnez **Utilisateurs**.
4. Vérifiez que les nouveaux utilisateurs apparaissent dans le client ![Synch](media/tutorial-password-hash-sync/synch1.png)

## <a name="test-signing-in-with-one-of-our-users"></a>Tester la connexion avec un des utilisateurs

1. Accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com).
2. Connectez-vous avec un compte d’utilisateur créé dans le nouveau locataire.  Vous devez vous connecter en utilisant le format suivant : (user@domain.onmicrosoft.com). Saisissez le même mot de passe que celui utilisé par l’utilisateur pour se connecter en local.
   ![Vérifier](media/tutorial-password-hash-sync/verify1.png)

Vous avez maintenant configuré un environnement d’identité hybride que vous pouvez utiliser à des fins de test et pour vous familiariser avec les fonctionnalités d’Azure.

## <a name="next-steps"></a>Étapes suivantes

- [Matériel et conditions préalables](how-to-connect-install-prerequisites.md) 
- [Paramètres personnalisés](how-to-connect-install-custom.md)
- [Fédération avec Azure AD Connect](how-to-connect-fed-whatis.md)

