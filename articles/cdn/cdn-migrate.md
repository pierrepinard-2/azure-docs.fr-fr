---
title: Migrer un profil Azure CDN depuis Verizon standard vers Verizon Premium | Microsoft Docs
description: Découvrez en détail la migration d’un profil depuis Verizon standard vers Verizon Premium.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 7768dde424aedc295b53512db50c9dfc9db9ab8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635633"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrer un profil Azure CDN depuis Verizon standard vers Verizon Premium

Quand vous créez un profil Azure Content Delivery Network (CDN) pour gérer vos points de terminaison, Azure CDN met à votre disposition quatre produits différents. Pour plus d’informations sur les différents produits et leurs fonctionnalités, consultez [Comparer les caractéristiques du produit CDN Azure](cdn-features.md).

Si vous avez créé un profil **Azure CDN standard de Verizon** et que vous l’utilisez pour gérer vos points de terminaison CDN, vous pouvez le mettre à niveau vers un profil **Azure CDN Premium de Verizon**. Quand vous effectuez la mise à niveau, vos points de terminaison CDN et toutes vos données sont conservés. 

> [!IMPORTANT]
> Une fois que la mise à niveau est effectuée avec un profil **Azure CDN Premium de Verizon**, vous ne pouvez plus reconvertir ce dernier en profil **Azure CDN standard de Verizon**.
> 

Pour mettre à niveau un profil **Azure CDN standard de Verizon**, contactez le [Support Microsoft](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Comparaison des profils
Les profils **Azure CDN Premium de Verizon** présentent les caractéristiques suivantes par rapport aux profils **Azure CDN standard de Verizon** :
- Pour certaines fonctionnalités Azure CDN comme la [compression](cdn-improve-performance.md), la [mise en cache des règles](cdn-caching-rules.md) et le [géofiltrage](cdn-restrict-access-by-country.md), vous ne pouvez pas utiliser l’interface d’Azure CDN ; vous devez utiliser le portail de Verizon par le biais du bouton **Gérer**.
- API : Contrairement à Verizon Standard, vous ne pouvez pas utiliser l’API pour contrôler ces fonctionnalités sont accessibles à partir du portail de Verizon Premium. Toutefois, vous pouvez utiliser l’API pour contrôler d’autres fonctionnalités courantes, telles que la création/suppression d’un point de terminaison, le vidage/le chargement de ressources mises en cache et l’activation/la désactivation d’un domaine personnalisé.
- Tarification : Verizon Premium a une structure de tarification différents pour les transferts de données à Verizon Standard. Pour plus d’informations, consultez [Tarifs Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

Les profils **Azure CDN Premium de Verizon** ont les fonctionnalités supplémentaires suivantes :
- [L’authentification par jeton](cdn-token-auth.md): Permet aux utilisateurs d’obtenir et d’utiliser un jeton pour extraire des ressources sécurisées.
- [Moteur de règles](cdn-rules-engine.md): Vous permet de personnaliser comment sont gérées les requêtes HTTP.
- Outils d’analytique avancée :
   - [Analytique HTTP Détaillée](cdn-advanced-http-reports.md)
   - [Analytique des performances de périmètre](cdn-edge-performance.md)
   - [Analytique en temps réel](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur le moteur de règles, consultez [Référence du moteur de règles Azure CDN](cdn-rules-engine-reference.md).

