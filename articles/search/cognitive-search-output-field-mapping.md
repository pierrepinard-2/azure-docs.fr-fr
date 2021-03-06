---
title: Mapper des champs d’entrée enrichis avec la recherche cognitive sur des champs de sortie - Recherche Azure
description: Extrayez et enrichissez des champs de données sources, puis mappez-les à des champs de sortie dans un index Recherche Azure.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: dd62119b01465392a92c7e68231fed8027b04da2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342608"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Guide pratique pour mapper des champs enrichis sur un index pouvant faire l’objet d’une recherche

Dans cet article, vous allez apprendre à mapper des champs d’entrée enrichis sur des champs de sortie dans un index pouvant faire l’objet d’une recherche. Une fois que vous avez [défini un ensemble de compétences](cognitive-search-defining-skillset.md), vous devez mapper les champs de sortie de n’importe quelle compétence qui fournit directement des valeurs sur un champ donné dans votre index de recherche. Les mappages de champs sont requis pour le déplacement du contenu depuis les documents enrichis vers l’index.


## <a name="use-outputfieldmappings"></a>Utiliser outputFieldMappings
Pour mapper les champs, ajoutez `outputFieldMappings` à la définition de l’indexeur comme indiqué ci-dessous :

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

Le corps de la demande est structuré comme suit :

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```
Pour chaque mappage de champ de sortie, définissez le nom du champ enrichi (sourceFieldName) et le nom du champ tel que référencé dans l’index (targetFieldName).

Le chemin dans un sourceFieldName peut représenter un élément ou plusieurs éléments. Dans l’exemple ci-dessus, ```/document/content/sentiment``` représente une valeur numérique unique, tandis que ```/document/content/organizations/*/description``` représente plusieurs descriptions d’organisation. Dans les cas où il existe plusieurs éléments, ceux-ci sont « aplatis » en un tableau qui contient chacun des éléments. Plus concrètement, si nous considérons l’exemple ```/document/content/organizations/*/description```, les données du champ *descriptions* ressembleraient à un tableau de descriptions aplati avant d’être indexées :

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez mappé les champs enrichis sur des champs pouvant faire l’objet d’une recherche, vous pouvez définir les attributs de chacun des champs pouvant faire l’objet d’une recherche [dans le cadre de la définition de l’index](search-what-is-an-index.md).

Pour en savoir plus sur le mappage de champs, consultez [Mappages de champs dans les indexeurs Recherche Azure](search-indexer-field-mappings.md).
