---
title: Ajouter des invités B2B sans un lien d’invitation ou l’e-mail - Azure Active Directory | Microsoft Docs
description: Vous pouvez permettre à un utilisateur invité d’ajouter d’autres utilisateurs invités à votre Azure AD sans échanger d’invitation dans Azure Active Directory B2B Collaboration.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dca03f84b821d20ee6fecbaec24a1aa840836b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60357135"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Ajouter les utilisateurs invités B2B collaboration sans un lien d’invitation ou l’e-mail

Vous pouvez désormais inviter des utilisateurs en leur envoyant un lien direct vers une application partagée. Grâce à cette méthode, les utilisateurs invités n’ont plus besoin d’utiliser l’e-mail d’invitation, sauf dans certains cas spéciaux. Un utilisateur invité clique sur le lien de l’application, passe en revue et accepte les termes du contrat de confidentialité et accède ensuite en toute transparence à l’application. Pour plus d’informations, consultez [Utilisation d'invitations B2B Collaboration](redemption-experience.md).   

Avant la mise à disposition de cette nouvelle méthode, vous pouviez inviter des utilisateurs sans avoir recours à l’e-mail d’invitation en ajoutant un inviteur (à partir de votre organisation ou d’une organisation partenaire) au rôle du répertoire **Inviteur d’invités**, puis en faisant en sorte que l’inviteur ajoute des utilisateurs invités au répertoire, aux groupes ou aux applications par le biais de l’interface utilisateur ou de PowerShell. Si vous utilisez PowerShell, vous pouvez supprimer en même temps l’e-mail d’invitation. Par exemple : 

1. Un utilisateur de l’organisation hôte (par exemple, WoodGrove) invite un utilisateur d’une organisation partenaire (par exemple, Sam@litware.com) en tant qu’invité.
2. L’administrateur de l’organisation hôte [définit les stratégies](delegate-invitations.md) qui permettent à Sam d’identifier et d’ajouter d’autres utilisateurs de l’organisation partenaire (Litware). Sam doit être ajouté au rôle **Inviteur d’invités**.
3. Maintenant, Sam peut ajouter d’autres utilisateurs Litware au répertoire, à des groupes et à des applications WoodGrove sans avoir besoin d’utiliser des invitations. Si Sam possède les privilèges d’énumération appropriés de Litware, cela se produit automatiquement.
 
Cette méthode d’origine fonctionne toujours. Toutefois, elle se comporte désormais différemment. Si vous utilisez PowerShell, vous remarquerez qu’un compte d’invité a désormais l’état **En attente d’acceptation** au lieu de passer immédiatement à **Accepté**. Bien que l’état soit en attente, l’utilisateur invité peut toujours se connecter et accéder à l’application sans cliquer sur un lien d’invitation figurant dans un courrier électronique. L’état en attente signifie que l’utilisateur n’a pas encore donné son [consentement](redemption-experience.md#privacy-policy-agreement), là où il a accepté les conditions de confidentialité de l’organisation à l’origine de l’invitation. L’utilisateur invité voit cet écran de consentement quand il se connecte pour la première fois. 

Si vous invitez un utilisateur dans le répertoire, l’utilisateur invité doit accéder directement à l’URL du portail Azure propre au client des ressources (tel que https://portal.azure.com/ *resourcetenant*.onmicrosoft.com) pour afficher et accepter les conditions de confidentialité.

## <a name="next-steps"></a>Étapes suivantes

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
- [Utilisation d’une invitation B2B Collaboration](redemption-experience.md)
- [Déléguer des invitations pour Azure Active Directory B2B Collaboration](delegate-invitations.md)
- [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](add-users-information-worker.md)

