---
title: Inscrire un nouvel appareil depuis la ligne de commande - Azure IoT Edge | Microsoft Docs
description: Utiliser l’extension IoT pour Azure CLI afin d’inscrire un nouvel appareil IoT Edge et de récupérer la chaîne de connexion
author: kgremban
manager: philmea
ms.author: v-yiso
origin.date: 01/03/2019
ms.date: 01/28/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 45b05498702042c931df3765b9e1bd79489dbb6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595080"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Inscrire un nouvel appareil Azure IoT Edge avec Azure CLI

Avant d’utiliser vos appareils IoT avec Azure IoT Edge, vous devez les inscrire auprès de votre hub IoT. Une fois que vous avez inscrit un appareil, vous recevez une chaîne de connexion qui vous permet de le configurer pour les charges de travail Edge. 

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) est un outil en ligne de commande open source et multiplateforme, destiné à la gestion des ressources Azure, telles que IoT Edge. Il vous permet de gérer les ressources Azure IoT Hub, les instances de service Device Provisioning et les hubs liés dès l’installation. La nouvelle extension IoT enrichit Azure CLI avec des fonctionnalités telles que la gestion des appareils, et toutes les fonctionnalités IoT Edge.

Cet article explique comment inscrire un nouvel appareil IoT Edge avec Azure CLI.

## <a name="prerequisites"></a>Conditions préalables

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) dans votre abonnement Azure. 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) dans votre environnement. Vous devez utiliser Azure CLI version 2.0.24 ou ultérieure. Utilisez `az –-version` pour valider. Cette version prend en charge les commandes d’extension az et introduit l’infrastructure de la commande Knack. 
* [Extension IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Créer un appareil

Utilisez la commande suivante pour créer une identité d’appareil dans votre hub IoT : 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Cette commande inclut trois paramètres :
* **device-id** : fournissez un nom descriptif unique à votre hub IoT.
* **hub-name** : indiquez le nom de votre hub IoT.
* **edge-enabled** : ce paramètre déclare que l’appareil est destiné à être utilisé avec IoT Edge.

   ![az iot hub device-identity create output](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Voir tous les appareils

Utilisez la commande suivante pour voir tous les appareils dans votre hub IoT :

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Tout appareil inscrit en tant qu’appareil IoT Edge a la propriété **capabilities.iotEdge** définie sur **true**. 

## <a name="retrieve-the-connection-string"></a>Récupérer la chaîne de connexion

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT. Utilisez la commande suivante pour retourner la chaîne de connexion d’un appareil spécifique :

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name] 
   ```

La valeur du paramètre `device-id` respecte la casse. Ne copiez pas les guillemets qui entourent la chaîne de connexion.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer des modules sur un appareil avec Azure CLI](how-to-deploy-modules-cli.md).
