---
title: À propos du kit SDK Speech - Services Speech
titleSuffix: Azure Cognitive Services
description: Le kit de développement logiciel (SDK) Speech fournit à vos applications un accès natif aux fonctions du service Speech, ce qui facilite le développement de logiciels. Cet article fournit des détails supplémentaires sur le kit SDK pour Windows, Linux et Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: wolfma
ms.openlocfilehash: bb28972469ca33a7fb33a50a8ce2e9bf326222b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460409"
---
# <a name="about-the-speech-sdk"></a>À propos du kit SDK Speech

Le Kit de développement logiciel (SDK) de reconnaissance vocale donne vos applications d’accéder aux fonctions des Services de reconnaissance vocale, ce qui facilite développer des logiciels de reconnaissance vocale. Actuellement, les kits SDK fournissent l’accès à **parole-texte**, **synthèse vocale**, **traduction vocale**, et **la reconnaissance des intentions**. Vous trouverez une vue d’ensemble sur les fonctionnalités et les plateformes prises en charge sur la documentation [page entrée](https://aka.ms/csspeech).

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Obtention du Kit de développement logiciel (SDK)

### <a name="windows"></a>Windows

Pour Windows, nous prenons en charge les langages suivants :

* C# (UWP et .NET), C++ : vous pouvez référencer et utiliser la dernière version de notre package NuGet du kit SDK Speech. Le package inclut des bibliothèques clientes 32 bits et 64 bits, ainsi que des bibliothèques managées (.NET). Le SDK peut être installé dans Visual Studio à l’aide de NuGet. Recherchez **Microsoft.CognitiveServices.Speech**.

* Java : vous pouvez référencer et utiliser la dernière version de notre package Maven du SDK Speech, qui prend uniquement en charge Windows x64. Dans votre projet Maven, ajoutez `https://csspeechstorage.blob.core.windows.net/maven/` comme référentiel supplémentaire, puis référencez `com.microsoft.cognitiveservices.speech:client-sdk:1.4.0` comme dépendance.

### <a name="linux"></a>Linux

> [!NOTE]
> Actuellement, nous prenons uniquement en charge Ubuntu 16.04 et 18.04 sur un PC (x86 ou x64 pour le développement C++, et x64 pour .NET Core, Java et Python).

Vérifiez que le compilateur et les bibliothèques requis sont installés en exécutant les commandes shell suivantes :

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

* C# : vous pouvez référencer et utiliser la dernière version de notre package NuGet du kit SDK Speech. Pour référencer le SDK, ajoutez la référence de package suivante à votre projet :

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.4.0" />
  ```

* Java : vous pouvez référencer et utiliser la dernière version de notre package Maven du kit SDK Speech. Dans votre projet Maven, ajoutez `https://csspeechstorage.blob.core.windows.net/maven/` comme référentiel supplémentaire, puis référencez `com.microsoft.cognitiveservices.speech:client-sdk:1.4.0` comme dépendance.

* C++ : téléchargez le SDK sous forme de [package .tar](https://aka.ms/csspeech/linuxbinary) et décompressez les fichiers dans le répertoire de votre choix. Le tableau suivant montre la structure de dossiers du SDK :

  |path|Description|
  |-|-|
  |`license.md`|Licence|
  |`ThirdPartyNotices.md`|Mentions tierces|
  |`include`|Les fichiers d’en-tête pour C et C++|
  |`lib/x64`|Bibliothèque x64 native pour la liaison avec votre application|
  |`lib/x86`|Bibliothèque x86 native pour la liaison avec votre application|

  Pour créer une application, copiez ou déplacez les fichiers binaires (et les bibliothèques) exigés dans votre environnement de développement. Incluez-les dans votre processus de génération en fonction de vos besoins.

### <a name="android"></a>Android

Le Kit de développement logiciel (SDK) Java pour Android est empaqueté au format [AAR (bibliothèque Android)](https://developer.android.com/studio/projects/android-library), qui inclut les bibliothèques nécessaires et les autorisations Android requises. Il est hébergé dans un référentiel Maven dans `https://csspeechstorage.blob.core.windows.net/maven/` sous la forme d’un package `com.microsoft.cognitiveservices.speech:client-sdk:1.4.0`.

Pour utiliser le package dans votre projet Android Studio, effectuez les changements suivants :

* Dans le fichier build.gradle au niveau du projet, ajoutez le texte suivant à la section `repository` :

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* Dans le fichier build.gradle au niveau du module, ajoutez le texte suivant à la section `dependencies` :

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.4.0'
  ```

Le Kit de développement logiciel (SDK) Java fait aussi partie du [Kit de développement logiciel (SDK) Speech Devices](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Découvrir comment utiliser la reconnaissance vocale en C#](quickstart-csharp-dotnet-windows.md)
