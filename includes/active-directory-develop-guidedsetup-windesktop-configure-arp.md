---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
origin.date: 09/17/2018
ms.date: 11/05/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 5c2bce5635dfe488c1725efdd2954ecd81cf8e79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300580"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Ajouter les informations d’inscription de l’application à votre application
Dans cette étape, vous devez ajouter l’ID d’application à votre projet.

1. Ouvrez `App.xaml.cs` et remplacez la ligne contenant `ClientId` par :

    ```csharp
    private static string ClientId = "[Enter the application Id here]";
    ```

<!-- ms.date: 11/05/2018 -->