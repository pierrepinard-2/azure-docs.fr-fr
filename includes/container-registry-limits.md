---
title: Fichier Include
description: Fichier Include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/30/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 844e4a0d2715799b808f2c7630c201f6e792bd63
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60582797"
---
| Resource | De base | standard | Premium |
|---|---|---|---|
| Stockage<sup>1</sup> | 10 Go | 100 Go| 500 Go |
| Taille de couche d’image maximale | 20 Gio | 20 Gio | 50 GiB |
| ReadOps par minute<sup>2, 3</sup> | 1 000 | 3 000 | 10 000 |
| WriteOps par minute<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Bande passante de téléchargement en Mbits/s<sup>2</sup> | 30 | 60 | 100 |
| Bande passante de chargement en Mbits/s<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Géoréplication | S.O. | S.O. | [Pris en charge][geo-replication] |
| Approbation du contenu (préversion) | S.O. | S.O. | [Pris en charge][content-trust] |

<sup>1</sup>les limites de stockage spécifié sont la quantité de *inclus* stockage pour chaque niveau. Vous êtes facturé un tarif journalier supplémentaire pour chaque Gio de stockage d’images utilisé au-dessus de ces limites. Pour plus d’informations taux, consultez [tarification Azure Container Registry][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*, et *la bande passante* sont des estimations minimales. Azure Container Registry s’efforce d’améliorer les performances nécessite l’utilisation.

<sup>3</sup>A [par extraction de docker](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduit par plusieurs opérations de lecture en fonction du nombre de couches dans l’image, ainsi que la récupération du manifeste.

<sup>4</sup>A [push de docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduit par plusieurs opérations d’écriture, en fonction du nombre de couches qui doivent être envoyées. Un `docker push` inclut des *ReadOps* pour récupérer un manifeste pour une image existante.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
