---
title: Configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters HDInsight basés sur Linux - Azure
description: Découvrez comment configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters HDInsight sous Linux.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: a887d6c69b9fa80f3144434e72a097e80d123a1b
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766468"
---
# <a name="os-patching-for-hdinsight"></a>Mise à jour corrective du système d’exploitation pour HDInsight 

> [!IMPORTANT]
> Les images Ubuntu sont accessibles pour la création d'un nouveau cluster HDInsight dans les 3 mois qui suivent leur publication. Depuis janvier 2019, **plus aucun** correctif automatique n'est disponible pour les clusters en cours d'exécution. Les clients doivent utiliser des actions de script ou d'autres mécanismes pour corriger un cluster en cours d'exécution. Les clusters nouvellement créés disposeront toujours des mises à jour et des correctifs de sécurité les plus récents.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Comment configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters HDInsight sous Linux
Les machines virtuelles d’un cluster HDInsight doivent être occasionnellement redémarrées pour assurer l’installation des correctifs de sécurité importants. 

À l’aide de l’action de script décrite dans cet article, vous pouvez modifier la planification de la mise à jour corrective du système d’exploitation comme suit :
1. Activez ou désactivez les redémarrages automatiques.
2. Définissez la fréquence de redémarrage (en jours entre les redémarrages).
3. Définissez le jour de la semaine auquel s’effectue le redémarrage.

> [!NOTE]  
> Cette action de script fonctionne uniquement avec les clusters HDInsight sous Linux créés après le 1er août 2016. Les correctifs seront appliqués une fois les machines virtuelles redémarrées. 

## <a name="how-to-use-the-script"></a>Utilisation du script 

L’utilisation de ce script requiert les informations suivantes :
1. L’emplacement du script : https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh.  HDInsight utilise cet URI pour rechercher et exécuter le script sur toutes les machines virtuelles du cluster.
  
2. Les types de nœud de cluster auxquels s’applique le script : headnode, workernode, zookeeper. Ce script doit être appliqué à tous les types de nœud du cluster. S’il n’est pas appliqué à un type de nœud, les machines virtuelles associées au type de nœud concerné continueront d’utiliser la planification de mise à jour corrective précédente.


3.  Paramètre : ce script accepte trois paramètres numériques :

    | Paramètre | Définition |
    | --- | --- |
    | Activation ou désactivation des redémarrages automatiques |0 ou 1. La valeur 0 désactive les redémarrages automatiques et la valeur 1 les active. |
    | Fréquence |7 à 90 (valeur inclusive). Le nombre de jours d’attente avant le redémarrage des machines virtuelles pour les correctifs qui nécessitent un redémarrage. |
    | Jour de semaine |1 à 7 (valeur inclusive). La valeur 1 indique que le redémarrage doit s’effectuer un lundi et la valeur 7 indique qu’il doit s’effectuer un dimanche. Par exemple, avec les paramètres 1 60 2, les redémarrages automatiques s’effectueront tous les 60 jours (au plus) le mardi. |
    | Persistance |Lorsque vous appliquez une action de script à un cluster existant, vous pouvez marquer le script comme persistant. Les scripts persistants sont appliqués lorsque de nouveaux nœuds de type workernode sont ajoutés au cluster lors d’opérations de mise à l’échelle. |

> [!NOTE]  
> Vous devez marquer ce script comme persistant lorsqu’il s’applique à un cluster existant. Sinon, les nœuds créés lors d’opérations de mise à l’échelle utilisent la planification de mise à jour corrective par défaut.  Si vous appliquez le script dans le cadre du processus de création du cluster, il est automatiquement défini comme persistant.

## <a name="next-steps"></a>Étapes suivantes

Pour connaître les étapes spécifiques à l’utilisation de l’action de script, consultez les sections suivantes de l’article [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) :

* [Utiliser une action de script lors de la création du cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Appliquer une action de script sur un cluster en cours d’exécution](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
