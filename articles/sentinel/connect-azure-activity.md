---
title: Connecter des données d’activité Azure vers Azure Sentinel Preview | Microsoft Docs
description: Découvrez comment connecter les données d’activité Azure à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d0cc13227bfe02594a57a7fb0ba8ee1cb3383d56
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125185"
---
# <a name="connect-data-from-azure-activity-log"></a>Connectez des données à partir du journal d’activité Azure

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez diffuser des journaux à partir de [journal des activités Azure](../azure-monitor/platform/activity-logs-overview.md) dans Azure Sentinel avec un seul clic. Le journal d’activité est un journal d’abonnement qui fournit des informations sur les événements de niveau d’abonnement qui se sont produites dans Azure. Cela inclut une plage de données, à partir de données opérationnelles d’Azure Resource Manager pour les mises à jour des événements de l’état d’intégrité du service. Avec le journal d’activité, vous pouvez déterminer le « quoi, qui, quand et ' pour toute opération (PUT, POST, DELETE) effectuée sur les ressources dans votre abonnement d’écriture. Vous pouvez également comprendre l’état de l’opération et d’autres propriétés pertinentes. Le journal d’activité n’inclut pas les opérations de lecture (GET) ou des opérations pour les ressources qui utilisent le modèle Classic / modèle de « RDFE ». 


## <a name="prerequisites"></a>Conditions préalables

- Utilisateur avec l’administrateur général ou d’autorisations d’administrateur de sécurité


## <a name="connect-to-azure-activity-log"></a>Se connecter au journal d’activité Azure

1. Dans Azure Sentinel, sélectionnez **connecteurs de données** puis cliquez sur le **journal des activités Azure** vignette.

2. Dans le volet de journal d’activité Azure, sélectionnez les abonnements que vous souhaitez diffuser en continu dans Azure Sentinel. 

3. Cliquez sur **Connecter**.

4. Pour utiliser le schéma pertinent dans Analytique de journal pour les alertes d’activité Azure, recherchez **AzureActivity**.


 

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter le journal d’activité Azure pour Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).
