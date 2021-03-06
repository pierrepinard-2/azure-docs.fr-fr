---
title: Topologies de réseau pour des migrations d’Azure SQL Database Managed Instance à l’aide de Azure Database Migration Service | Microsoft Docs
description: Découvrez les configurations de la source et de la cible pour Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: d12d6b1274a756bfb13761ab999a1539bcee3657
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61097790"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Topologies de réseau pour des migrations d’instances gérées de bases de données SQL Azure à l’aide de Azure Database Migration Service
Cet article traite de différentes topologies de réseau avec lesquelles Azure Database Migration Service peut fonctionner pour offrir une expérience de la migration complète vers Azure SQL Database Managed Instance à partir de serveurs SQL locaux.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL Database Managed Instance configuré pour les charges de travail hybrides 
Utilisez cette topologie si votre Azure SQL Database Managed Instance est connecté à votre réseau local. Cette approche fournit le routage réseau le plus simplifié et génère le débit de données maximale lors de la migration.

![Topologie de réseau pour les charges de travail hybrides](media/resource-network-topologies/hybrid-workloads.png)

**Configuration requise**
- Dans ce scénario, Azure SQL Database Managed Instance et Azure Database Migration Service Instance sont créés sur le même réseau virtuel Azure, mais ils utilisent des sous-réseaux différents.  
- Le réseau virtuel utilisé dans ce scénario est également connecté au réseau local à l’aide [d’ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou de [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL Database Managed Instance isolé du réseau local
Utilisez cette topologie de réseau si votre environnement requiert un ou plusieurs des scénarios suivants :
- Azure SQL Database Managed Instance est isolé de la connectivité locale, mais votre Azure Database Migration Service Instance est connecté au réseau local.
- Si les stratégies RBAC (Role Based Access Control) sont en place et que vous devez limiter l’accès utilisateur au même abonnement que celui qui héberge Azure SQL Database Managed Instance.
- Les réseaux virtuels utilisés pour Azure SQL Database Managed Instance et Azure Database Migration Service Instance se trouvent dans des abonnements différents.

![Topologie de réseau pour l’instance gérée isolée du réseau local](media/resource-network-topologies/mi-isolated-workload.png)

**Configuration requise**
- Le réseau virtuel utilisé par Azure Database Migration Service pour ce scénario doit également être connecté au réseau local à l’aide de https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou de [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Configurez [VNET Peering réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre le réseau virtuel utilisé pour Azure SQL Database Managed Instance et Azure Database Migration Service.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Migrations de cloud à cloud : réseau virtuel partagé

Utilisez cette topologie si le serveur source SQL Server est hébergé dans une machine virtuelle Azure et partage le même réseau virtuel avec Azure SQL Database Managed Instance et Azure Database Migration Service.

![Topologie de réseau pour les migrations de cloud à cloud avec réseau virtuel partagé](media/resource-network-topologies/cloud-to-cloud.png)

**Configuration requise**
- Aucune exigence supplémentaire.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Migrations de cloud à cloud : réseau virtuel isolé

Utilisez cette topologie de réseau si votre environnement requiert un ou plusieurs des scénarios suivants :
- Azure SQL Database Managed Instance est configuré dans un réseau virtuel isolé.
- Si les stratégies RBAC (Role Based Access Control) sont en place et que vous devez limiter l’accès utilisateur au même abonnement que celui qui héberge Azure SQL Database Managed Instance.
- Les réseaux virtuels utilisés pour Azure SQL Database Managed Instance et Azure Database Migration Service Instance se trouvent dans des abonnements différents.

![Topologie de réseau pour les migrations de cloud à cloud avec un réseau virtuel partagé](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Configuration requise**
- Configurez [VNET Peering réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre le réseau virtuel utilisé pour Azure SQL Database Managed Instance et Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Règles de sécurité de trafic entrant

| **NAME**   | **PORT** | **PROTOCOLE** | **SOURCE** | **DESTINATION** | **ACTION** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Quelconque      | Quelconque          | DMS SUBNET | Quelconque             | AUTORISER      |

## <a name="outbound-security-rules"></a>Règles de sécurité de trafic entrant

| **NAME**                  | **PORT**                                              | **PROTOCOLE** | **SOURCE** | **DESTINATION**           | **ACTION** | **Raison de la règle**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| gestion                | 443,9354                                              | TCP          | Quelconque        | Quelconque                       | AUTORISER      | Communication du plan de gestion via Service Bus et Stockage Blob Azure. <br/>(Si l’appairage Microsoft est activé, vous n’avez peut-être pas besoin de cette règle.)                                                             |
| Diagnostics               | 12 000                                                 | TCP          | Quelconque        | Quelconque                       | AUTORISER      | DMS utilise cette règle pour collecter des informations de diagnostic à des fins de dépannage.                                                                                                                      |
| Serveur source SQL         | 1433 (ou port TCP IP que SQL Server écoute) | TCP          | Quelconque        | Espace d’adressage local | AUTORISER      | Connectivité source SQL Server à partir de DMS <br/>(Si vous disposez d’une connectivité de site à site, vous n’aurez peut-être pas besoin de cette règle.)                                                                                       |
| Instance nommée SQL Server | 1434                                                  | UDP          | Quelconque        | Espace d’adressage local | AUTORISER      | Connectivité source de l’instance nommée SQL Server à partir de DMS <br/>(Si vous disposez d’une connectivité de site à site, vous n’aurez peut-être pas besoin de cette règle.)                                                                        |
| Partage SMB                 | 445                                                   | TCP          | Quelconque        | Espace d’adressage local | AUTORISER      | Partage réseau SMB pour DMS afin de stocker les fichiers de sauvegarde des bases de données pour les migrations vers Azure SQL Database MI et SQL Server sur Azure VM <br/>(Si vous disposez d’une connectivité de site à site, vous n’aurez peut-être pas besoin de cette règle). |
| DMS_subnet                | Quelconque                                                   | Quelconque          | Quelconque        | DMS_subnet                | AUTORISER      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Voir aussi
- [Migrer SQL Server vers Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Vue d’ensemble de la configuration requise pour l’utilisation d’Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Créer un réseau virtuel au moyen du portail Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Étapes suivantes
- Pour une vue d’ensemble d’Azure Database Migration Service, consultez l’article [Présentation d’Azure Database Migration Service](dms-overview.md).
- Pour obtenir des informations actuelles sur la disponibilité régionale d’Azure Database Migration Service, consultez la page [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).