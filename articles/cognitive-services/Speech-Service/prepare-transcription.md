---
title: Instructions de transcription pour l’apprentissage des modèles de Services de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Découvrez comment préparer le texte à personnaliser acoustiques et de modèles de langage et de voix pour les Services de reconnaissance vocale.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: 0d7508ed9cf1807fa05c57a1d60c804af7d2244f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461440"
---
# <a name="transcription-guidelines-for-using-the-speech-service"></a>Instructions concernant la transcription pour l’utilisation du service de reconnaissance vocale

Pour personnaliser la **reconnaissance vocale** ou la **synthèse vocale**, vous devez fournir le texte en même temps que la voix. Chaque ligne du texte correspond à un énoncé unique. Le texte devrait correspondre autant que possible à la reconnaissance vocale. Le texte est appelé une *transcription* et doit être créé dans un format spécifique.

Les Services de reconnaissance vocale normaliser l’entrée pour garantir la cohérence de texte.

Cet article décrit les deux types de normalisations. Les instructions varient légèrement d’une langue à l’autre.

## <a name="us-english-en-us"></a>Anglais (États-Unis, en-US)

Les données de texte doivent être écrites, un énoncé par ligne, en utilisant uniquement le jeu de caractères ASCII.

Évitez l’utilisation des signes de ponctuation étendus (Latin-1) ou Unicode. Ces caractères peuvent être inclus par inadvertance lors de la préparation des données dans un programme de traitement de texte ou lors de la capture de données à partir de pages web. Remplacez les caractères par des substituts ASCII appropriés. Par exemple : 

| Caractères à éviter | Substitution |
|----- | ----- |
| “Hello world” (guillemets doubles ouvrants et fermants) | "Hello world" (guillemets doubles) |
| John’s day (apostrophe courbe) | John’s day (apostrophe) |
| it was good—no, it was great! (tiret long) | it was good--no, it was great! (traits d’union) |

### <a name="text-normalization-rules-for-english"></a>Règles de normalisation de texte pour l’anglais

Les Services de reconnaissance vocale exécuter les règles de normalisation suivants :

* Utilisation des lettres minuscules pour tout le texte
* Suppression de tous les signes de ponctuation, à l’exception des apostrophes à l’intérieur des mots
* Développement des nombres en forme orale, y compris les montants en dollars

Voici quelques exemples :

| Texte d’origine | Après la normalisation |
|----- | ----- |
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman’s sidekick, Robin. | what said batman’s sidekick robin |
| Go get -em! | go get em |
| I’m double-jointed | I'm double jointed |
| 104 Elm Street | one oh four Elm street |
| Tune to 102.7 | tune to one oh two seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

Appliquez la normalisation suivante à vos transcriptions de texte :

* Les abréviations doivent être développées.
* Les chaînes numériques non standard (par exemple, certaines dates ou les formules de comptabilité) doivent être écrites en mots.
* Les mots contenant des caractères non alphabétiques ou des caractères alphanumériques mixtes doivent être retranscrits conformément à leur prononciation.
* Laissez les abréviations prononcées comme des mots inchangées (par exemple, « radar », « laser », « RAM » ou « OTAN »).
* Écrivez les abréviations prononcées comme des lettres distinctes sous forme de lettres séparées par des espaces (par exemple, « IMB », « CPU », «FBI », « TBD » ou «NaN »).

Voici quelques exemples :

| Texte d’origine | Après la normalisation |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr. Bruce Banner | Docteur Bruce Banner |
| James Bond, 007 | James Bond, double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

## <a name="chinese-zh-cn"></a>Chinois (zh-CN)

Les données de texte qui sont téléchargées vers les Services de reconnaissance vocale personnalisé doit utiliser un encodage UTF-8 avec un marqueur d’ordre d’octet. Le fichier doit être écrit un énoncé par ligne.

Évitez l’utilisation des signes de ponctuation de demi-largeur. Ces caractères peuvent être inclus par inadvertance lors de la préparation des données dans un programme de traitement de texte ou lors de la capture de données à partir de pages web. Remplacez-les par les substituts appropriés de largeur normale. Par exemple : 

| Caractères à éviter | Substitution |
|----- | ----- |
| "你好" (guillemets doubles ouvrants et fermants) | "你好" (guillemets doubles) |
| 需要什么帮助? (point d’interrogation) | 需要什么帮助？ |

### <a name="text-normalization-rules-for-chinese"></a>Règles de normalisation de texte pour le chinois

Les Services de reconnaissance vocale exécuter les règles de normalisation suivants :

* Suppression de tous les signes de ponctuation
* Développement des nombres vers la forme orale
* Conversion des lettres pleine largeur en lettres de demi-largeur
* Utilisation de lettres majuscules pour tous les mots anglais

Voici quelques exemples :

| Texte d’origine | Après la normalisation |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

Appliquez la normalisation suivante à votre texte avant de l’importer :

* Les abréviations doivent être développées (sous la forme orale).
* Écrivez les chaînes numériques sous leur forme orale.

Voici quelques exemples :

| Texte d’origine | Après la normalisation |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Autres langages

Les données de texte téléchargées vers le service de **reconnaissance vocale** doivent utiliser l’encodage UTF-8 avec un marqueur d’ordre d’octet. Le fichier doit être écrit un énoncé par ligne.

> [!NOTE]
> Les exemples suivants utilisent l’allemand. Toutefois, ces instructions s’appliquent à toutes les langues autres que l’anglais US ou le chinois.

### <a name="text-normalization-rules-for-german"></a>Règles de normalisation de texte pour l’allemand

Les Services de reconnaissance vocale exécuter les règles de normalisation suivants :

* Utilisation des lettres minuscules pour tout le texte
* Suppression de tous les signes de ponctuation, y compris les différents types de guillemets ("test", ’test’, "test„ et « test » sont acceptés)
* Suppression des lignes contenant l’un des caractères spéciaux suivants ¢ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬
* Développement des nombres en forme textuelle, y compris les montants en dollars ou en euros
* Trémas acceptés uniquement sur les lettres a, o et u ; les autres seront remplacés par « th » ou ignorés

Voici quelques exemples :

| Texte d’origine | Après la normalisation |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

Appliquez la normalisation suivante à votre texte avant de l’importer :

* Le séparateur décimal doit être une virgule et non un point.
* Le séparateur entre les heures et les minutes doit être « : » et non « . » (par exemple, 12:00 Uhr).
* Les abréviations telles que « ca ». ne sont pas remplacées. Nous vous recommandons d’utiliser la forme complète.
* Les quatre principaux opérateurs mathématiques (+, -, \*, /) sont supprimés. Nous vous recommandons de les remplacer par leur forme littérale : « plus », «minus », « mal » et « geteilt ».
* La même règle s’applique aux opérateurs de comparaison (=, < et >). Nous vous recommandons de les remplacer par « gleich », « kleiner als » et « grösser als ».
* Utilisez la forme textuelle des fractions (par exemple, « drei viertel » au lieu de ¾).
* Remplacez le symbole € par la forme textuelle « Euro ».

Voici quelques exemples :

| Texte d’origine | Après la normalisation par l’utilisateur | Après la normalisation par le système |
|--------  | ----- | -------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit à Speech Services](https://azure.microsoft.com/try/cognitive-services/)
- [Reconnaissance vocale dans C#](quickstart-csharp-dotnet-windows.md)
