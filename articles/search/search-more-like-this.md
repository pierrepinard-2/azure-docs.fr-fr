---
title: moreLikeThis dans la Recherche Azure (préversion) - Recherche Azure
description: Documentation préliminaire pour la fonctionnalité moreLikeThis (préversion) exposée dans l’API REST Recherche Azure.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d55a6d883e0dcd5ad4b1c1584b76bae06e6c742a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283337"
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis dans la Recherche Azure (préversion)

`moreLikeThis=[key]` est un paramètre de requête dans l’[API Recherche](https://docs.microsoft.com/rest/api/searchservice/search-documents). En spécifiant le paramètre `moreLikeThis` dans une requête de recherche, vous pouvez trouver des documents qui sont similaires au document spécifié par la clé de document. Lorsqu’une requête de recherche est formulée avec `moreLikeThis`, une demande est générée en utilisant les termes de recherche extraits du document donné et qui décrivent le mieux ce document. La demande générée est ensuite utilisée pour effectuer la requête de recherche. Par défaut, le contenu de tous les champs `searchable` est pris en compte, sauf si le paramètre `searchFields` est utilisé pour restreindre ces champs. Le paramètre `moreLikeThis` ne peut pas être utilisé avec le paramètre de recherche, `search=[string]`.

## <a name="examples"></a>Exemples 

Voici l’exemple d’une requête moreLikeThis. La requête recherche les documents dont les champs de description sont les plus proches du champ du document source, tel que spécifié par le paramètre `moreLikeThis`.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```

## <a name="feature-availability"></a>Disponibilité des fonctionnalités

La fonctionnalité moreLikeThis est actuellement en préversion et uniquement prise en charge dans les versions d’API en préversion, `2015-02-28-Preview` et `2016-09-01-Preview`. Comme la version de l’API est spécifiée dans la requête, il est possible de combiner les API mises à la disposition générale (GA) et les API en version préliminaire disponibles dans la même application. Cependant, les API de versions préliminaires ne sont pas soumises à un contrat SLA, et les fonctionnalités peuvent changer ; par conséquent, nous ne recommandons pas de les utiliser dans des applications de production.
