---
title: Comprendre la remise Azure Reserved VM Instances | Microsoft Docs
description: Découvrez comment la remise d’instance de machine virtuelle réservée Azure est appliquée aux machines virtuelles en cours d’exécution.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: b112dd881d4b2e87e617111d00bc82c6151d7750
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370070"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Comment la remise de réservation Azure est appliquée aux machines virtuelles

Quand vous achetez une instance de machine virtuelle réservée Azure, la remise de réservation est automatiquement appliquée aux machines virtuelles qui correspondent aux attributs et à la quantité de la réservation. Une réservation couvre les coûts de calcul de vos machines virtuelles.

Une remise de réservation s’applique à la machine virtuelle de base que vous achetez à partir de la place de marché Azure.

Pour la capacité réservée SQL Database, consultez [Comprendre la remise des instances réservées Azure](billing-understand-reservation-charges.md).

Le tableau suivant montre les coûts de votre machine virtuelle après l’achat d’une instance de machine virtuelle réservée. Dans tous les cas, les frais de stockage et de mise en réseau vous sont facturés aux tarifs normaux.

| Type de machine virtuelle  | Frais avec instance de machine virtuelle réservée |
|-----------------------|--------------------------------------------|
|Machines virtuelles Linux sans logiciel supplémentaire | La réservation couvre les coûts d’infrastructure des machines virtuelles.|
|Machines virtuelles Linux avec frais de logiciel (par exemple, Red Hat) | La réservation couvre les coûts d’infrastructure. Les logiciels supplémentaires vous sont facturés.|
|Machines virtuelles Windows sans logiciel supplémentaire |La réservation couvre les coûts d’infrastructure. Les logiciels Windows vous sont facturés.|
|Machines virtuelles Windows avec logiciels supplémentaires (par exemple, SQL server) | La réservation couvre les coûts d’infrastructure. Les logiciels Windows et les autres logiciels vous sont facturés.|
|Machines virtuelles Windows avec [Azure Hybrid Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | La réservation couvre les coûts d’infrastructure. Les coûts des logiciels Windows sont couverts par Azure Hybrid Benefit. Tout logiciel supplémentaire est facturé séparément.|

## <a name="how-reservation-discount-is-applied"></a>Comment la remise de réservation est appliquée

Une remise de réservation est «*utilisation-it-ou-perdre-it*». Par conséquent, si vous n’avez pas les ressources correspondantes pour toutes les heures, puis vous perdez une quantité de réservation pour cette heure. Vous ne pouvez pas effectuer transférer heures réservées inutilisées.

Lorsque vous arrêtez une ressource, la remise de réservation s’applique automatiquement à une autre ressource correspondante dans la portée spécifiée. Si aucune ressource correspondante ne se trouvent dans la portée spécifiée, les heures réservées sont *perdues*.

## <a name="reservation-discount-for-non-windows-vms"></a>Remise de réservation pour les machines virtuelles non Windows

 La remise de réservation Azure est appliquée aux instances de machine virtuelle en cours d’exécution sur une base horaire. Les réservations que vous avez achetées sont mises en correspondance avec l’utilisation émise par les machines virtuelles en cours d’exécution pour appliquer la remise de réservation. Pour les machines virtuelles qui ne peuvent pas s’exécuter pendant une heure entière, la réservation est remplie à partir d’autres machines virtuelles n’utilisant pas de réservation, incluant des machines virtuelles s’exécutant simultanément. À la fin de l’heure, l’application de réservation pour les machines virtuelles dans l’heure est verrouillée. Quand une machine virtuelle ne s’exécute pas pendant une heure ou quand des machines virtuelles s’exécutant simultanément dans l’heure ne remplissent pas l’heure de réservation, la réservation est sous-utilisée pour cette heure. Le graphique suivant illustre l’application d’une réservation à l’utilisation de machines virtuelles facturables. La représentation est basée sur l’achat d’une réservation et sur deux instances de machine virtuelle correspondantes.

![Capture d’écran montrant une réservation appliquée et deux instances de machine virtuelle correspondantes](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Toute utilisation qui est au-dessus de la ligne réservation obtient facturée au tarif de paiement à l’utilisation. Toute utilisation sous la ligne de réservation n’est pas facturée, car elle a déjà été payée dans le cadre de l’achat de la réservation.
2. Dans l’heure 1, l’instance 1 s’exécute pendant 0,75 heure, et l’instance 2 pendant 0,5 heure. L’utilisation totale de l’heure 1 est 1,25 heure. Vous payez 0,25 heure restante au tarif du paiement à l’utilisation.
3. Pour les heures 2 et 3, les deux instances se sont exécutées chacune pendant 1 heure. Une instance est couverte par la réservation, et l’autre est facturée au tarif du paiement à l’utilisation.
4. Pour l’heure 4, l’instance 1 s’exécute pendant 0,5 heure tandis que l’instance 2 s’exécute pendant 1 heure. L’instance 1 est entièrement couverte par la réservation, et la durée de 0,5 heure de l’instance 2 est couverte. Vous êtes facturé au tarif du paiement à l’utilisation pour cette durée de 0,5 heure restante.

Pour comprendre et voir l’application de vos réservations Azure dans les rapports d’utilisation pour la facturation, voir [Comprendre l’utilisation des réservations](billing-understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Remise de réservation pour les machines virtuelles Windows

Quand vous exécutez des instances de machine virtuelle Windows, la réservation est appliquée pour couvrir les coûts d’infrastructure. L’application de la réservation aux coûts d’infrastructure de machine virtuelle pour les machines virtuelles Windows est identique à celle qui est appliquée pour les machines virtuelles non Windows. Les logiciels Windows vous sont facturés séparément, par processeur virtuel. Vous [Coûts des logiciels Windows avec les réservations](billing-reserved-Instance-windows-software-costs.md). Vous pouvez supporter les coûts de licence Windows avec [Azure Hybrid Benefit pour Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Remise peut être appliquée à différentes tailles

Quand vous achetez une instance de machine virtuelle réservée, si vous sélectionnez **Optimisé pour** : **Flexibilité de taille d’instance**, l’étendue de la remise dépend de la taille de machine virtuelle que vous sélectionnez. La réservation peut s’appliquer aux tailles des machines virtuelles dans le même groupe de gammes de tailles. Pour plus d’informations, consultez [Flexibilité en termes de taille de machine virtuelle avec des instances de machines virtuelles réservées](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>Remise s’applique à la mise en correspondance ServiceType uniquement

Une remise de réservation s’applique uniquement à l’utilisation de la machine virtuelle où la `ServiceType` valeur dans `AdditionalInfo` correspond à la réservation est achetée. Application de remise de réservation ignore le compteur utilisé pour les machines virtuelles et évalue uniquement `ServiceType`. Savoir quel service type que vous avez acheté la machine virtuelle. Vous pouvez échanger une réservation de machine virtuelle pour une réservation de stockage premium, ou à l’inverse de stockage non premium.

## <a name="classic-vms-and-cloud-services"></a>Machines virtuelles classiques et les services cloud

Instances de machine virtuelle réservée s’appliquent aux deux machines virtuelles classiques et services de cloud lors de la flexibilité de taille d’instance est activée automatiquement. Pour les services cloud, la remise de réservation s’applique uniquement au coût du calcul. Lors de la remise de réservation est appliquée aux services cloud, les frais d’utilisation sont divisées en frais de calcul (compteur de Linux) et frais (compteur de gestion de services cloud) des services cloud. Pour plus d’informations, consultez [comment la remise de réservation s’applique aux Services de cloud computing](billing-reserved-instance-windows-software-costs.md#cloud-services-software-meters-not-included-in-reservation-cost).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créer une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Quelles sont les réservations pour Azure ?](billing-save-compute-costs-reservations.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Gérer les réservations pour Azure](billing-manage-reserved-vm-instance.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Comprendre l’utilisation d’une réservation pour les abonnements CSP](/partner-center/azure-reservations)
- [Coûts des logiciels Windows non inclus dans les réservations](billing-reserved-instance-windows-software-costs.md)
