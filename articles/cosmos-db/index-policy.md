---
title: Stratégies d’indexation d’Azure Cosmos DB
description: Découvrez comment configurer et modifier la valeur par défaut pour l’indexation automatique et de meilleures performances dans Azure Cosmos DB la stratégie d’indexation.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: thweiss
ms.openlocfilehash: 67bc3076be91ade140b39b7dd8037299902546a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61046316"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Stratégies d’indexation dans Azure Cosmos DB

Dans Azure Cosmos DB, chaque conteneur possède une stratégie d’indexation qui détermine la façon dont les éléments du conteneur doivent être indexées. La valeur par défaut pour la stratégie d’indexation qui vient d’être créé des index de conteneurs chaque propriété de chaque élément, en appliquant des index de plage pour toute chaîne ou un nombre, et les index spatiaux pour n’importe quel objet GeoJSON de type Point. Ainsi, vous permettant d’obtenir des performances de requête sans avoir à réfléchir à l’indexation et de gestion des index dès le départ.

Dans certaines situations, vous souhaiterez remplacer ce comportement automatique pour mieux répondre à vos besoins. Vous pouvez personnaliser la stratégie d’indexation d’un conteneur en définissant son *mode d’indexation*et inclure ou exclure *chemins d’accès de la propriété*.

## <a name="indexing-mode"></a>Mode d’indexation

Azure Cosmos DB prend en charge deux modes d’indexation :

- **Cohérent** : Si la stratégie d’indexation d’un conteneur est défini sur cohérent, l’index est mis à jour synchrone lorsque vous créez, mettre à jour ou supprimez des éléments. Cela signifie que la cohérence de vos requêtes de lecture sera le [cohérence configurée pour le compte](consistency-levels.md).

- **Aucun** : Si la stratégie d’indexation d’un conteneur est définie sur None, l’indexation est désactivée sur ce conteneur. Elle est couramment utilisée lorsqu’un conteneur est utilisé comme un magasin clé-valeur pur sans la nécessité d’index secondaires. Il peut également aider à accélérer en bloc les opérations d’insertion.

## <a name="including-and-excluding-property-paths"></a>Inclusion et exclusion de chemins d’accès de propriété

Une stratégie d’indexation personnalisée peut spécifier des chemins d’accès de propriété qui sont explicitement inclus ou exclus de l’indexation. En optimisant le nombre de chemins d’accès qui sont indexés, vous pouvez réduire la quantité de stockage utilisé par votre conteneur et améliorer la latence des opérations d’écriture. Ces chemins d’accès sont définies à l’aide [la méthode décrite dans la section vue d’ensemble d’indexation](index-overview.md#from-trees-to-property-paths) avec les ajouts suivants :

- un chemin d’accès menant à une valeur scalaire (chaîne ou nombre) se termine par `/?`
- éléments à partir d’un tableau sont traités ensemble par le biais du `/[]` notation (au lieu de `/0`, `/1` etc..)
- le `/*` générique peut être utilisé pour correspondre à tous les éléments sous le nœud

En prenant l’exemple même à nouveau :

    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }

- le `headquarters`du `employees` chemin d’accès est `/headquarters/employees/?`
- le `locations`' `country` chemin d’accès est `/locations/[]/country/?`
- le chemin d’accès d’une autre manière sous `headquarters` est `/headquarters/*`

Lorsqu’un chemin d’accès est explicitement inclus dans la stratégie d’indexation, il doit également définir les types d’index doivent être appliquées à ce chemin d’accès et pour chaque type d’index, le type de données que cet index s’applique à :

| Type d’index | Types de données cible autorisés |
| --- | --- |
| Plage | Chaîne ou nombre |
| spatial | Point, LineString ou polygones |

Par exemple, nous pourrions inclure la `/headquarters/employees/?` chemin d’accès et spécifier un `Range` index doit être appliqué sur ce chemin d’accès pour les deux `String` et `Number` valeurs.

### <a name="includeexclude-strategy"></a>Inclure/exclure la stratégie

Toute stratégie d’indexation doit inclure le chemin d’accès racine `/*` comme un inclus ou un chemin d’accès exclu.

- Inclure le chemin d’accès racine pour exclure sélectivement des chemins d’accès que vous n’avez pas besoin d’être indexés. Il s’agit de l’approche recommandée car elle permet à Azure Cosmos DB proactivement toute nouvelle propriété qui peut être ajoutée à votre modèle d’index.
- Exclure le chemin d’accès racine pour inclure sélectivement des chemins d’accès qui doivent être indexés.

Consultez [cette section](how-to-manage-indexing-policy.md#indexing-policy-examples) pour des exemples de stratégie d’indexation.

## <a name="modifying-the-indexing-policy"></a>Modification de la stratégie d’indexation

Stratégie d’indexation d’un conteneur peut être mis à jour à tout moment [à l’aide du portail Azure ou un des kits de développement logiciel pris en charge](how-to-manage-indexing-policy.md). Une mise à jour la stratégie d’indexation déclenche une transformation de l’ancien index vers le nouveau, ce qui est effectuée en ligne et localement (aucun espace de stockage supplémentaire n’est consommé pendant l’opération). Les index de l’ancienne stratégie sont transformée efficacement à la nouvelle stratégie sans affecter la disponibilité de l’écriture ou le débit approvisionné sur le conteneur. Transformation d’index est une opération asynchrone, et le temps que nécessaire pour terminer varie selon le débit approvisionné, le nombre d’éléments et leur taille. 

> [!NOTE]
> Tandis que la réindexation est en cours d’exécution, les requêtes ne peuvent pas retourner tous les résultats correspondants et le fait sans retourner des erreurs. Cela signifie que les résultats de la requête ne soient pas cohérentes avant la fin de la transformation d’index. Il est possible de suivre la progression de la transformation d’index [à l’aide d’un des kits de développement logiciel](how-to-manage-indexing-policy.md).

Si le mode de l’indexation de la nouvelle stratégie est défini sur cohérent, aucune autre modification de stratégie d’indexation ne sont applicables lorsque la transformation d’index est en cours. Une transformation d’index en cours d’exécution peut être annulée en définissant le mode de la stratégie d’indexation sur None (ce qui supprime immédiatement l’index).

## <a name="indexing-policies-and-ttl"></a>Stratégies d’indexation et de durée de vie

Le [Time-to-Live (TTL) de fonctionnalité](time-to-live.md) nécessite l’indexation afin d’être actives sur le conteneur, il est allumé. Cela signifie que :

- Il n’est pas possible d’activer la TTL sur un conteneur dans lequel le mode d’indexation est défini sur None,
- Il n’est pas possible de définir le mode d’indexation sur None sur un conteneur dans lequel la durée de vie est activée.

Pour les scénarios où aucun chemin d’accès de propriété ne doit être indexée, mais TTL est requise, vous pouvez utiliser une stratégie d’indexation avec :

- un mode d’indexation défini sur cohérent, et
- Aucun chemin d’accès inclus, et
- `/*` comme le seul chemin d’accès exclus.

## <a name="obsolete-attributes"></a>Attributs obsolètes

Lorsque vous travaillez avec des stratégies d’indexation, vous pouvez rencontrer les attributs suivants sont désormais obsolètes :

- `automatic` une valeur booléenne est définie à la racine d’une stratégie d’indexation. Il est désormais ignorée et peut être définie sur `true`, lorsque l’outil que vous utilisez l’exige.
- `precision` un nombre est défini au niveau des index pour les chemins d’accès inclus. Il est désormais ignorée et peut être définie sur `-1`, lorsque l’outil que vous utilisez l’exige.
- `hash` est un type d’index qui est désormais remplacé par le type de plage.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’indexation, consultez les articles suivants :

- [Vue d’ensemble de l’indexation](index-overview.md)
- [Guide pratique pour gérer la stratégie d’indexation](how-to-manage-indexing-policy.md)
