---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
origin.date: 09/13/2018
ms.date: 11/05/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: b8f961ad3fe4550b915253746d0f4f677c593a8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300567"
---
## <a name="add-the-applications-registration-to-your-code"></a>Ajouter l’inscription de l’application à votre code

À cette étape, vous devez ajouter l’ID de l’application/du client à votre projet.

1. Ouvrez `MainActivity` (sous `app` > `java` > *`{host}.{namespace}`*).
2. Remplacez la ligne commençant par `final static String CLIENT_ID` par :
    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
3. Ouvrez `app` > `manifests` > `AndroidManifest.xml`.
4. Ajoutez l’activité suivante à `manifest\application`. L’activité `BrowserTabActivity` permet à Microsoft de rappeler votre application une fois l’authentification terminée :

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after Sign In-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />

            <!--Add in your scheme/host from registered redirect URI-->
            <!--By default, the scheme should be similar to 'msal[appId]' -->
            <data android:scheme="msal[Enter the application Id here]"
                android:host="auth" />
        </intent-filter>
    </activity>
    ```

<!-- ms.date: 11/05/2018 -->
