---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: b275a86f8fd35c43865fd920d1bfc9994a796a9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557141"
---
>[!NOTE]
>Pour les ressources qui ne sont pas fixes, ouvrez un ticket de support pour demander une augmentation des quotas. Ne créez pas des comptes Azure Media Services supplémentaires dans une tentative d’obtention des limites plus élevées.

| Resource | Limite par défaut | 
| --- | --- | 
| Comptes Media Services Azure dans un seul abonnement | 25 (fixe) |
| Unités par compte Media Services réservées de média |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Travaux par compte Media Services | 50 000<sup>2</sup> |
| Tâches chaînées par travail | 30 (fixe) |
| Actifs par compte Media Services | 1 000 000|
| Actifs par tâche | 50 |
| Actifs par travail | 100 |
| Localisateurs uniques associés à un actif à un moment donné | 5<sup>4</sup> |
| Canaux en direct par compte Media Services |5.|
| Programmes dans un état Arrêté par canal  |50|
| Programmes en cours d’exécution par canal  |3|
| Diffusion en continu de points de terminaison qui sont arrêtées ou en cours d’exécution par compte Media Services|2|
| Unités de diffusion en continu par point de terminaison de diffusion en continu  |10 |
| Comptes de stockage | 1 000<sup>5</sup> (fixe) |
| Stratégies | 1,000,000<sup>6</sup> |
| Taille du fichier| Dans certains scénarios, il existe une limite sur la taille de fichier maximale prise en charge pour le traitement dans Media Services. <sup>7</sup> |

<sup>1</sup>si vous changez le type, par exemple, de S2 à S1, les limites de capacité maximale en unités réservées sont réinitialisées.

<sup>2</sup>ce nombre inclut les travaux en file d’attente, terminés, actifs et annulés. Il n’inclut pas travaux supprimés. Vous pouvez supprimer les anciens travaux à l’aide de **IJob.Delete** ou **supprimer** requête HTTP.

Depuis le 1er avril 2017, les enregistrements de travaux dans votre compte plu de 90 jours sont automatiquement supprimé, ainsi que leurs enregistrements de tâches associée. La suppression automatique se produit même si le nombre total d’enregistrements est inférieur au quota maximal. Pour archiver les informations de projet et la tâche, utilisez le code décrit dans [gérer les ressources avec le SDK Media Services .NET](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup>lorsque vous faites une demande pour les entités de travail de liste, un maximum de 1 000 travaux est retourné par la demande. Pour effectuer le suivi de tous les travaux soumis, utilisez la partie supérieure ou ignorer les requêtes comme décrit dans [options de requête système OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup>localisateurs ne sont pas conçus pour gérer le contrôle d’accès par utilisateur. Pour accorder différents droits d’accès à des utilisateurs individuels, utilisez les solutions digital rights management (DRM). Pour plus d’informations, consultez [protéger votre contenu avec Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup>les comptes de stockage doivent provenir du même abonnement Azure.

<sup>6</sup>il existe une limite de 1 000 000 pour les différentes stratégies Media Services. Un exemple est pour la stratégie de localisateur ou pour ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Si vous toujours utilisez le même nombre de jours et les autorisations d’accès, utilisez le même ID de stratégie. Pour plus d’informations et un exemple, consultez [gérer les ressources avec le SDK Media Services .NET](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup>si vous téléchargez le contenu à un élément multimédia dans Media Services à traiter avec des processeurs multimédias dans le service, notez la taille maximale de fichier pris en charge. Les ressources incluent des encodeurs comme Media Encoder Standard et les moteurs de Media Encoder Premium Workflow ou l’analyse comme Face Detector.

La taille maximale prise en charge pour un seul objet blob est actuellement jusqu'à 5 To de stockage d’objets Blob Azure. Limites supplémentaires s’appliquent dans Media Services basée sur les tailles de machines virtuelles qui sont utilisés par le service. Le tableau suivant indique les limites sur réservées multimédia unités S1, S2 et S3. Si votre fichier source est supérieur à la limite définie dans la table, votre travail d’encodage échoue. Si vous encodez des sources de résolution 4K de longue durée, vous devez utiliser des unités réservées multimédia de S3 pour optimiser les performances nécessité. Si vous avez du contenu 4K qui dépasse la limite de 260 Go sur les unités réservées multimédia S3, contactez-nous à l’adresse amshelp@microsoft.com pour les solutions d’atténuation possibles prendre en charge votre scénario.

| Type d’unité réservée de média | Taille maximale en entrée (Go)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
