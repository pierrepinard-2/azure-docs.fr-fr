---
title: Migrer une instance SQL vers une instance gérée Azure SQL Database | Microsoft Docs
description: Apprenez à migrer une instance SQL Server vers une instance gérée Azure SQL Database Managed.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: 1460b595e8887fc932d5be335ae51b07a000b9fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61315555"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migration d’une instance SQL Server vers une instance gérée Azure SQL Database

Dans cet article, vous allez découvrir les méthodes possibles pour la migration d’une instance SQL Server 2005 (ou version ultérieure) vers un [instance gérée Azure SQL Database](sql-database-managed-instance.md). Pour obtenir des informations sur la migration vers une base de données unique ou un pool élastique, consultez [Migration vers une base de données unique ou mise en pool](sql-database-cloud-migrate.md). Pour obtenir des informations sur la migration à partir d’autres plateformes, consultez [Guide de migration des bases de données Azure](https://datamigration.microsoft.com/).

Le processus général de migration d’une base de données ressemble à ce qui suit :

![processus de migration](./media/sql-database-managed-instance-migration/migration-process.png)

- [Évaluer la compatibilité d’instance gérée](#assess-managed-instance-compatibility)
- [Choisir une option de connectivité des applications](sql-database-managed-instance-connect-app.md)
- [Déployer sur une instance gérée dimensionnée de façon optimale](#deploy-to-an-optimally-sized-managed-instance)
- [Sélectionner une méthode de migration et effectuer la migration](#select-migration-method-and-migrate)
- [Surveillance des applications](#monitor-applications)

> [!NOTE]
> Pour effectuer la migration d’une base de données individuelle vers une base de données unique ou un pool élastique, consultez [Effectuer la migration d’une base de données SQL Server vers Azure SQL Database](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Évaluer la compatibilité d’instance gérée

Tout d’abord, déterminez si l’instance gérée est compatible avec les exigences de base de données de votre application. L’option de déploiement d’instance gérée est conçue pour faciliter la migration « lift-and-shift » de la plupart des applications existantes qui utilisent SQL Server localement ou sur des machines virtuelles. Toutefois, vous risquez parfois d’avoir besoin de fonctionnalités qui ne sont pas encore prises en charge et dont le coût d’implémentation d’une solution de contournement est trop élevé.

Utilisez l’[Assistant Migration de données](https://docs.microsoft.com/sql/dma/dma-overview) pour détecter les éventuels problèmes de compatibilité qui impactent le fonctionnement de la base de données sur Azure SQL Database. Cet Assistant ne prend pas encore en charge une instance gérée en tant que destination de migration, mais il est recommandé d’effectuer une évaluation par rapport à Azure SQL Database et d’examiner attentivement la liste des problèmes de compatibilité et de parité de fonctionnalité signalés par rapport à la documentation du produit. Consultez les [fonctionnalités Azure SQL Database](sql-database-features.md) pour savoir si des problèmes de blocage sont signalés dans l’instance gérée, car la plupart des problèmes de blocage empêchant de migrer vers une base de données Azure SQL Database ont été supprimés avec l’instance gérée. Par exemple, des fonctionnalités comme les requêtes entre plusieurs bases de données, les transactions entre plusieurs bases de données au sein de la même instance, un serveur lié à d’autres sources SQL, la prise en charge du Common Language Runtime, les tables temporaires globales, les affichages au niveau de l’instance, Service Broker, etc. sont disponibles dans les instances gérées.

Si certains problèmes de blocage signalés ne sont pas supprimés avec l’option de déploiement d’instance gérée, vous devrez peut-être envisager une autre solution, comme l’utilisation de [SQL Server sur des machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Voici quelques exemples :

- Si vous avez besoin d’un accès direct au système d’exploitation ou au système de fichiers, par exemple pour installer des agents tiers ou personnalisés sur la même machine virtuelle avec SQL Server.
- Si vous dépendez obligatoirement de fonctionnalités qui ne sont pas encore prises en charge, comme FileStream/FileTable, PolyBase et les transactions entre plusieurs instances.
- Si vous devez absolument conserver une version spécifique de SQL Server (2012, par exemple).
- Si vos exigences de calcul sont beaucoup plus faibles que ce qu’offre l’instance gérée (un seul vCore, par exemple) et que l’option de consolidation de bases de données n’est pas une option acceptable.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Déployer sur une instance gérée dimensionnée de façon optimale

Une instance gérée est adaptée pour des charges de travail locales qui planifient une migration vers le cloud. Un [nouveau modèle d’achat](sql-database-service-tiers-vcore.md) est ainsi présenté. Il offre davantage de flexibilité dans le choix du niveau de ressources pour vos charges de travail. Localement, vous êtes probablement habitué à dimensionner ces charges de travail à l’aide de cœurs physiques et de bandes passantes E/S. Le modèle d’achat pour une instance gérée repose sur des mémoires à tores magnétiques virtuelle, ou « vCore », avec un stockage et des E/S supplémentaires disponibles séparément. Le modèle vCore vous permet de comprendre plus facilement vos exigences de calcul dans le cloud par rapport à ce que vous utilisez localement aujourd’hui. Ce nouveau modèle vous permet de dimensionner au mieux votre environnement de destination dans le cloud.

Vous pouvez sélectionner des ressources de calcul et de stockage au moment du déploiement, puis les modifier plus tard sans temps d’arrêt pour votre application à l’aide du [Portail Azure](sql-database-scale-resources.md) :

![dimensionnement de l’instance managée](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Pour apprendre à créer l’infrastructure de réseau virtuel et une instance gérée, voir [Créer une instance gérée](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Il est important de toujours maintenir votre réseau virtuel de destination et le sous-réseau en adéquation avec la [Configuration requise de réseau virtuel d’instance gérée](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Toute incompatibilité risque de vous empêcher de créer des instances ou d’utiliser celles que vous avez déjà créées. Apprenez-en davantage sur la [création de réseaux](sql-database-managed-instance-create-vnet-subnet.md) et la [configuration de réseaux existants](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="select-migration-method-and-migrate"></a>Sélectionner une méthode de migration et effectuer la migration

L’option de déploiement d’instance gérée cible des scénarios utilisateur exigeant une migration massive de bases de données à partir d’implémentations de base de données locales ou IaaS. Ils constituent le meilleur choix lorsque vous avez besoin d’effectuer une migration « lift-and-shift » du backend des applications qui utilisent régulièrement des fonctionnalités au niveau de l’instance et/ou entre plusieurs bases de données. Si cela correspond à votre scénario, vous pouvez déplacer toute une instance vers un environnement correspondant dans Azure sans avoir à redéfinir l’architecture de vos applications.

Pour déplacer des instances SQL, vous devez planifier avec soin :

- La migration de toutes les bases de données qui ont besoin d’être colocalisées (celles qui s’exécutent sur la même instance)
- La migration des objets au niveau de l’instance dont votre application dépend, notamment les connexions, les informations d’identification, les travaux et opérateurs de l’Agent SQL, ainsi que les déclencheurs au niveau du serveur.

Une instance gérée est un service géré qui vous permet de déléguer certaines des activités courantes d’administration des bases de données à la plateforme puisqu’elles y sont intégrées. Ainsi, certaines données au niveau de l’instance n’ont pas besoin de migrer, notamment les travaux de maintenance pour les sauvegardes régulières ou la configuration Always On, étant donné que la [haute disponibilité](sql-database-high-availability.md) est intégrée.

Une instance gérée prend en charge les options de migration de base de données suivantes (actuellement ce sont les seules méthodes de migration prises en charge) :

- Azure Database Migration Service - Migration ne nécessitant quasiment aucun temps d’arrêt,
- Native `RESTORE DATABASE FROM URL` - Utilise des sauvegardes natives à partir de SQL Server et nécessite un temps d’arrêt.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service (DMS)](../dms/dms-overview.md) est un service entièrement géré conçu pour permettre des migrations transparentes de plusieurs sources de base de données vers des plateformes de données Azure avec un temps d’arrêt minime. Ce service simplifie les tâches nécessaires pour déplacer des bases de données SQL Server tierces existantes vers Azure. Les options de déploiement en préversion publique incluent des bases de données dans Azure SQL Database et des bases de données SQL Server dans une machine virtuelle Azure. DMS est la méthode recommandée de migration pour vos charges de travail d’entreprise.

Si vous utilisez SSIS (SQL Server Integration Services) sur votre serveur SQL Server local, DMS ne prend pas encore en charge la migration du catalogue SSIS (SSISDB) qui stocke les packages SSIS, mais vous pouvez provisionner Azure-SSIS IR (Integration Runtime) dans ADF (Azure Data Factory) pour créer une base de données SSISDB dans une instance gérée. Vous pouvez ensuite y redéployer vos packages. Voir [Créer Azure-SSIS IR dans ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Pour plus d’informations sur ce scénario et les étapes de configuration de DMS, voir [Migrer votre base de données locale vers une instance gérée à l’aide de DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Restauration native à partir d’une URL

La restauration de sauvegardes natives (fichiers .bak) issues de SQL Server local ou de [SQL Server sur les machines virtuelles](https://azure.microsoft.com/services/virtual-machines/sql-server/), disponible sur [Stockage Azure](https://azure.microsoft.com/services/storage/), est une des principales fonctionnalités de l’option de déploiement d’instance gérée qui permet d’effectuer rapidement et facilement une migration de base de données hors connexion.

Le diagramme suivant fournit une vue d’ensemble du processus :

![flux de migration](./media/sql-database-managed-instance-migration/migration-flow.png)

Le tableau suivant fournit des informations supplémentaires sur les méthodes que vous pouvez utiliser en fonction de la version de SQL Server source que vous exécutez :

|Étape|Moteur SQL et version|Méthode de sauvegarde/restauration|
|---|---|---|
|Placer la sauvegarde sur Stockage Azure|Avant SQL 2012 SP1 CU2|Charger le fichier .bak directement sur Stockage Azure|
||2012 SP1 CU2 - 2016|Sauvegarde directe utilisant la syntaxe [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) dépréciée|
||2016 et versions ultérieures|Sauvegarde directe utilisant [WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Restaurer à partir d’un Stockage Azure vers une instance gérée|[RESTORE FROM URL avec SAS CREDENTIAL](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Lorsque vous migrez une base de données protégée par [Transparent Data Encryption](transparent-data-encryption-azure-sql.md) vers une instance gérée à l’aide d’une option de restauration native, le certificat correspondant du serveur SQL Server local ou IaaS doit être migré avant la restauration de la base de données. Pour des instructions détaillées, voir [Migrer un certificat TDE vers une instance gérée](sql-database-managed-instance-migrate-tde-certificate.md).
> - La restauration de bases de données système n’est pas prise en charge. Pour effectuer la migration d’objets au niveau de l’instance (stockés dans des bases de données master et msdb), nous vous recommandons de les scripter et d’exécuter des scripts T-SQL sur l’instance de destination.

Pour obtenir un guide de démarrage rapide sur la restauration d’une sauvegarde de base de données dans une instance gérée à l’aide d’informations d’identification SAP, voir [Restaurer une sauvegarde de base de données dans une instance gérée](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Surveillance des applications

Suivez le comportement et les performances des applications après la migration. Dans une instance gérée, certaines modifications sont activées une fois que le [niveau de compatibilité de la base de données a été modifié](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). La migration de base de données vers Azure SQL Database conserve son niveau de compatibilité d’origine dans la majorité des cas. Si le niveau de compatibilité d’une base de données utilisateur s’élève à 100 voire plus avant la migration, il reste le même après la migration. Si le niveau de compatibilité d’une base de données utilisateur s’élève à 90 avant la migration, dans la base de données mise à niveau, il s’élève à 100, car il s’agit du niveau de compatibilité le plus faible pris en charge dans une instance gérée. Le niveau de compatibilité des bases de données système s’élève à 140.

Pour réduire les risques liés à la migration, modifiez le niveau de compatibilité de la base de données uniquement après l’analyse des performances. Utilisez la fonctionnalité Magasin des requêtes en tant qu’outil optimal pour obtenir des informations sur les performances des charges de travail avant et après la modification du niveau de compatibilité des bases de données, comme indiqué dans [Maintenir la stabilité des performances pendant la mise à niveau vers une version plus récente de SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

Une fois que vous êtes sur une plateforme entièrement gérée, profitez des avantages automatiques du service SQL Database. Par exemple, vous n’êtes pas obligé de créer des sauvegardes sur une instance gérée : le service les effectue pour vous automatiquement. Vous n’avez plus à vous soucier de la planification, de l’exécution et de la gestion des sauvegardes. Une instance gérée vous offre la possibilité d’effectuer une restauration à partir de n’importe quel point dans le temps compris dans cette période de rétention à l’aide d’une [récupération jusqu’à une date et heure](sql-database-recovery-using-backups.md#point-in-time-restore). De plus, vous n’avez pas à vous soucier de la configuration de la [haute disponibilité](sql-database-high-availability.md) car elle est intégrée.

Pour renforcer la sécurité, envisagez d’utiliser certaines des fonctionnalités disponibles :

- Authentification Azure Active Directory au niveau de la base de données
- Utilisez des [fonctionnalités de sécurité avancées](sql-database-security-overview.md) pour sécuriser votre instance : [Audit](sql-database-managed-instance-auditing.md), [Détection des menaces](sql-database-advanced-data-security.md), [Sécurité au niveau des lignes](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) et [Dynamic Data Masking](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les instances gérées, voir [Qu’est-ce qu’une instance gérée ?](sql-database-managed-instance.md).
- Pour un didacticiel qui inclut une restauration à partir d’une sauvegarde, voir [Créer une instance gérée](sql-database-managed-instance-get-started.md).
- Pour un didacticiel présentant une migration à l’aide de DMS, voir [Migrer votre base de données locale vers une instance gérée à l’aide de DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
