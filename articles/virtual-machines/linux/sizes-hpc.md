---
title: Tailles des machines virtuelles Linux Azure - Calcul haute performance | Microsoft Docs
description: Répertorie les différentes tailles disponibles pour les machines virtuelles de calcul haute performance Linux dans Azure. Répertorie des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 44b965bd60d976d4d28dc5e31d78a1c838d4ee02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542258"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Tailles de machine virtuelle de calcul haute performance

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Seules les versions Intel MPI 5.x sont prises en charge.

> [!NOTE]
> Versions ultérieures (2017, 2018) du runtime Intel MPI library peut ou ne peut pas être compatible avec les pilotes Azure Linux RDMA.

### <a name="distributions"></a>Distributions
 
Déployez une machine virtuelle nécessitant beaucoup de ressources système à partir d’une des images disponibles dans la Place de marché Microsoft Azure qui prend en charge la connectivité RDMA :
  
* **Ubuntu** - Ubuntu Server 16.04 LTS. Configurez les pilotes RDMA sur la machine virtuelle et inscrivez-vous auprès d’Intel pour télécharger Intel MPI :

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** - SLES 12 SP3 pour HPC, SLES 12 SP3 pour HPC (Premium), SLES 12 SP1 pour HPC, SLES 12 SP1 pour HPC (Premium). Les pilotes RDMA sont installés et les packages Intel MPI sont distribués sur la machine virtuelle. Installez MPI en exécutant la commande suivante :

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **HPC basé sur centOS** : HPC 6.5 basé sur CentOS ou une version ultérieure (pour la série H, version 7.1 ou version ultérieure est recommandée). Les pilotes RDMA et Intel MPI 5.1 sont installés sur la machine virtuelle.  
 
  > [!NOTE]
  > Sur les images HPC basées sur CentOS, les mises à jour du noyau sont désactivées dans le fichier de configuration **yum** . Cela s’explique par le fait que les pilotes RDMA Linux sont distribués sous forme de package RPM, et que les mises à jour du pilote peuvent ne pas fonctionner si le noyau est mis à jour.
  > 
 
### <a name="cluster-configuration-options"></a>Options de configuration de cluster

Azure fournit plusieurs options pour créer des clusters de machines virtuelles HPC Linux qui peuvent communiquer via le réseau RDMA, notamment : 

* **Machines virtuelles** : Déployez les machines virtuelles HPC compatibles RDMA dans le même groupe à haute disponibilité (quand vous utilisez le modèle de déploiement Azure Resource Manager). Si vous utilisez le modèle de déploiement classique, déployez les machines virtuelles dans le même service cloud. 

* **Groupes de machines virtuelles identiques** : Dans un groupe de machines virtuelles identiques, veillez à limiter le déploiement à un seul groupe de placements. Par exemple, dans un modèle Resource Manager, définissez la propriété `singlePlacementGroup` avec la valeur `true`. 

* **Azure CycleCloud** : Créez un cluster HPC dans [Azure CycleCloud](/azure/cyclecloud/) pour exécuter des travaux MPI sur des nœuds Linux.

* **Azure Batch** : Créez un pool [Azure Batch](/azure/batch/) pour exécuter des charges de travail MPI sur des nœuds de calcul Linux. Pour plus d’informations, consultez [Utiliser des instances compatibles RDMA ou GPU dans les pools Batch](../../batch/batch-pool-compute-intensive-sizes.md). Consultez également le projet [Batch Shipyard](https://github.com/Azure/batch-shipyard) pour l’exécution de charges de travail basées sur des conteneurs sur Batch.

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) prend en charge l’exécution de plusieurs distributions Linux sur les nœuds de calcul déployés dans des machines virtuelles Azure compatibles RDMA et gérés par un nœud principal Windows Server. Pour un exemple de déploiement, consultez [Créer un cluster RDMA HPC Pack Linux dans Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

Selon l’outil de gestion de clusters que vous choisissez, une configuration système supplémentaire peut être nécessaire pour exécuter des travaux MPI. Par exemple, sur un cluster de machines virtuelles, vous devrez peut-être établir une approbation entre les nœuds de cluster en générant des clés SSH ou en établissant une confiance SSH sans mot de passe.

### <a name="network-topology-considerations"></a>Considérations sur la topologie réseau
* Sur des machines virtuelles Linux compatibles RDMA dans Azure, Eth1 est réservé au trafic réseau RDMA. Ne modifiez aucun paramètre Eth1 ou des informations du fichier de configuration faisant référence à ce réseau. Eth0 est réservé au trafic réseau Azure normal.

* Le réseau RDMA dans Azure réserve l'espace d’adressage 172.16.0.0/16. 




## <a name="other-sizes"></a>Autres tailles
- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

- Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.




