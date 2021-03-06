---
title: 'Démarrage rapide : Liste voix, Node.js - Services de reconnaissance vocale'
titleSuffix: Azure Cognitive Services
description: Dans ce démarrage rapide, vous allez apprendre à obtenir la liste complète des voix standard et neuronaux pour une région/le point de terminaison que l’aide de Node.js. La liste est retournée au format JSON, et la disponibilité de voix varie selon la région.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: erhopf
ms.openlocfilehash: 62187ddbe587a81038f8424b079e3c0c313d1ae2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620258"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Démarrage rapide : Obtenir la liste des voix de synthèse vocale à l’aide de Node.js

Dans ce démarrage rapide, vous allez apprendre à obtenir la liste complète des voix standard et neuronaux pour une région/le point de terminaison que l’aide de Node.js. La liste est retournée au format JSON, et la disponibilité de voix varie selon la région. Pour obtenir la liste des régions prises en charge, consultez [régions](regions.md).

Ce démarrage rapide nécessite un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource de Services de reconnaissance vocale. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](get-started.md) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Conditions préalables

Ce démarrage rapide nécessite :

* [Node 8.12.x ou version ultérieure](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou l’éditeur de texte de votre choix
* Clé d’abonnement Azure pour les services Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Créer un projet et requérir des dépendances

Créez un projet Node.js à l’aide de votre éditeur ou IDE favori. Copiez cet extrait de code dans votre projet, dans un fichier nommé `get-voices.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languagesto a file
const fs = require('fs');
```

> [!NOTE]
> Si vous n’avez jamais utilisé ces modules auparavant, vous devrez les installer avant d’exécuter votre programme. Pour installer ces packages, exécuter `npm install request request-promise`.

## <a name="get-an-access-token"></a>Obtention d’un jeton d’accès

L’API REST Synthèse vocale nécessite un jeton d’accès pour l’authentification. Pour obtenir un jeton d’accès, un échange est nécessaire. Cette fonction remplace votre clé d’abonnement de Services de reconnaissance vocale pour un jeton accès à l’aide du `issueToken` point de terminaison.

Cet exemple suppose que votre abonnement aux Services de reconnaissance vocale est dans la région ouest des États-Unis. Si vous utilisez une autre région, mettez à jour la valeur de `uri`. Pour obtenir la liste complète, consultez [Régions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copiez ce code dans votre projet :

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Pour plus d’informations sur l’authentification, consultez [S’authentifier avec un jeton d’accès](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

Dans la section suivante, nous allons créer la fonction pour obtenir la liste des voix et enregistrer la sortie JSON au fichier.

## <a name="make-a-request-and-save-the-response"></a>Effectuer une requête et enregistrer la réponse

Ici, vous allez à la demande de build et enregistrer la liste des voix retourné. Cet exemple part du principe que vous utilisez le point de terminaison USA Ouest. Si votre ressource est inscrite dans une autre région, veillez à mettre à jour la valeur `uri`. Pour plus d’informations, consultez [régions des Services de reconnaissance vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ensuite, ajoutez les en-têtes requis pour la demande. Enfin, vous envoyez une requête au service. Si la demande réussit, et un code de 200 état est retourné, la réponse est écrite dans le fichier.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Assemblage

Vous avez presque terminé. La dernière étape consiste à créer une fonction asynchrone. Cette fonction sera lire votre clé d’abonnement à partir d’une variable d’environnement, obtenir un jeton, attendez que la demande s’achève, puis écrire la réponse JSON au fichier.

Si vous n’êtes pas familiarisé avec les variables d’environnement ou que vous préférez tester avec votre codée en dur clé abonnement sous forme de chaîne, remplacez `process.env.SPEECH_SERVICE_KEY` avec votre clé d’abonnement sous forme de chaîne.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Voilà, vous êtes prêt à exécuter votre exemple d’application. À partir de la ligne de commande (ou d’une session terminal), accédez au répertoire de votre projet, puis exécutez :

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Supprimer des ressources

N’oubliez pas de supprimer toutes les informations confidentielles (telles que les clés d’abonnement) dans le code source de votre exemple d’application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Node.js sur GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Voir aussi

* [Référence sur l’API conversion de texte par synthèse vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Création de polices de voix personnalisée](how-to-customize-voice-font.md)
* [Enregistrer des échantillons vocaux pour créer une voix personnalisée](record-custom-voice-samples.md)
