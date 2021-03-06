---
title: Fichier Include
description: Fichier Include
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: a3a43c56a49c243390eac964d31988b7d30fbb56
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58408006"
---
| Ressource | Limite par défaut |
| --- | --- |
| Ressources de Service de la porte d’entrée Azure par abonnement | 100 |
| Hôtes front-end, qui inclut des domaines personnalisés par ressource | 100 |
| Règles de routage par ressource | 100 |
| Pools principaux par ressource | 50 |
| Serveurs principaux par pool back-end | 100 |
| Modèles de chemin à utiliser pour une règle de routage | 25 |
| Règles personnalisées de pare-feu d’applications web par stratégie | 10 |
| Stratégie de pare-feu d’applications web par ressource | 100 |

### <a name="timeout-values"></a>Valeurs de délai d’attente
#### <a name="client-to-front-door"></a>Du client à Front Door
- Front Door a un délai de 61 secondes pour les connexions TCP inactives.

#### <a name="front-door-to-application-back-end"></a>Porte d’entrée pour l’application back-end
- Si la réponse est une réponse mémorisé en bloc, une réponse 200 est retournée si ou lorsque le premier segment est reçu.
- Une fois que la requête HTTP est transférée vers le serveur principal, porte d’entrée attend 30 secondes pour le premier paquet depuis le serveur principal. Ensuite, elle retourne une erreur 503 au client.
- Une fois le premier paquet est reçu à partir du backend, porte d’entrée attend 30 secondes dans un délai d’inactivité. Ensuite, elle retourne une erreur 503 au client.
- Porte d’entrée à l’expiration de la session TCP principal est de 30 minutes.

### <a name="upload-and-download-data-limit"></a>Charger et télécharger des données limite

|  | Avec le codage (CTE) de transfert mémorisé en bloc | Sans segmentation HTTP |
| ---- | ------- | ------- |
| **Télécharger** | Il n’existe aucune limite sur la taille de téléchargement. | Il n’existe aucune limite sur la taille de téléchargement. |
| **Charger** |  Il n’existe aucune limite tant que chaque chargement de l’expression de table commune est inférieure à 2 Go. | La taille ne peut pas être supérieure à 2 Go. |
