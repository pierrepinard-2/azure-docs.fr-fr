---
title: Concevoir des tables scalables et performantes dans le stockage Table Azure. | Microsoft Docs
description: Concevoir des tables scalables et performantes dans le stockage Table Azure.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8387e41d57edfa0e54ac930c9462714aca571f2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848280"
---
# <a name="design-scalable-and-performant-tables"></a>Conception de tables extensibles et performantes

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Pour concevoir des tables scalables et performantes, vous devez prendre en compte un certain nombre de facteurs, comme les performances, la scalabilité et le coût. Si vous avez déjà conçu des schémas pour des bases de données relationnelles, ces considérations doivent vous être familières, mais bien qu’il existe quelques similitudes entre le modèle de stockage du service de Table Azure et les modèles relationnels, il existe également plusieurs différences importantes. Ces différences conduisent généralement à des conceptions différentes qui peuvent sembler absurdes ou incorrectes à une personne ayant une bonne connaissance des bases de données relationnelles, mais qui sont logiques pour une personne menant une conception pour un magasin de paires clé/valeur NoSQL comme celui du service de Table Azure. Un grand nombre des différences de conception reflètent le fait que le service de Table est conçu pour prendre en charge des applications à l’échelle du cloud qui peuvent contenir des milliards d’entités (ou lignes dans la terminologie de base de données relationnelle) de données ou pour des jeux de données devant prendre en charge des volumes de transactions très élevés. Par conséquent, vous devez concevoir différemment la façon dont vous stockez vos données et comprendre comment fonctionne le service de Table. Un magasin de données NoSQL bien conçu améliore la scalabilité de votre solution, pour un coût inférieur à celui d’une solution qui utilise une base de données relationnelle. Ce guide fournit une aide relative à ces sujets.  

## <a name="about-the-azure-table-service"></a>À propos du service de Table Azure
Cette section présente certaines des principales fonctionnalités du service de Table qui sont particulièrement adaptées aux conceptions orientées vers l'amélioration des performances et de l'extensibilité. Si vous ne connaissez pas le Stockage Azure et le service de Table, lisez d’abord [Présentation du Stockage Microsoft Azure](../../storage/common/storage-introduction.md) et [Bien démarrer avec le stockage Table Azure à l’aide de .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) avant de lire le reste de cet article. Bien que ce guide porte sur le service de Table, il aborde également les services de File d’attente et Blob Azure, en expliquant comment les utiliser avec le service de Table.  

Qu'est-ce que le service de Table ? Comme le laisse entendre son nom, le service de Table utilise un format tabulaire pour stocker des données. Selon la terminologie standard, chaque ligne de la table représente une entité et les colonnes stockent les différentes propriétés de cette entité. Chaque entité a une paire de clés qui permet de l’identifier de manière unique et une colonne d’horodatage que le service de Table utilise pour suivre les dernières mises à jour de l’entité. L’horodatage est appliqué automatiquement et vous ne pouvez pas le remplacer manuellement par une valeur arbitraire. Le service de Table utilise le dernier horodatage modifié (ou LMT, pour Last Modified Timestamp) afin de gérer l’accès concurrentiel optimiste.  

> [!NOTE]
> Les opérations d’API REST du service de Table retournent également une valeur **ETag** dérivée du dernier horodatage modifié (LMT). Ce document utilise indifféremment les termes ETag et LMT, car ils font référence aux mêmes données sous-jacentes.  
> 
> 

L'exemple suivant présente la conception d'une table simple pour stocker des entités relatives à des employés (Employee) ainsi qu'à leurs services (Department). Plusieurs des exemples présentés ultérieurement dans ce guide sont basés sur cette conception simple.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Jun</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Département</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Jusqu’ici, ces données ressemblent à une table de base de données relationnelle, les principales différences étant les colonnes obligatoires et la possibilité de stocker plusieurs types d’entité dans la même table. Par ailleurs, chacune des propriétés définies par l’utilisateur, comme **FirstName** ou **Age**, est caractérisée par un type de données, par exemple, un nombre entier ou une chaîne, tout comme une colonne dans une base de données relationnelle. Bien que, contrairement à une base de données relationnelle, la nature sans schéma du service de Table signifie qu'une propriété n'a pas nécessairement besoin d'avoir les mêmes types de données pour chaque entité. Pour stocker des types de données complexes dans une seule propriété, vous devez utiliser un format sérialisé comme JSON ou XML. Pour plus d’informations sur les plages de dates et les types de données pris en charge, les règles d’affectation de noms et les contraintes de taille, consultez l’article [Présentation du modèle de données du service de Table](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Le choix de la valeur de **PartitionKey** et de **RowKey** est fondamental pour une bonne conception de table. Toutes les entités stockées dans une table doivent avoir une combinaison unique de **PartitionKey** et **RowKey**. Comme avec les clés dans une table de base de données relationnelle, les valeurs de **PartitionKey** et **RowKey** sont indexées pour créer un index cluster afin de permettre des recherches rapides. Toutefois, le service de Table ne crée pas d’index secondaire, **PartitionKey** et **RowKey** sont par conséquent les seules propriétés indexées. Certains des modèles décrits dans [Modèles de conception de table](table-storage-design-patterns.md) illustrent comment contourner cette limitation apparente.  

Une table est constituée d’une ou plusieurs partitions et la plupart des décisions de conception que vous prenez consistent à choisir une valeur de **PartitionKey** et **RowKey** convenable pour optimiser votre solution. Une solution peut être constituée d’une seule table contenant toutes vos entités organisées en partitions, mais généralement, une solution a plusieurs tables. Les tables vous permettent d'organiser vos entités logiquement, de gérer l'accès aux données en utilisant des listes de contrôle d'accès, et de supprimer une table entière à l'aide d'une opération de stockage unique.  

## <a name="table-partitions"></a>Partitions de table
Le nom du compte, le nom de la table et la valeur de **PartitionKey** identifient la partition dans le service de stockage où le service de Table stocke l’entité. Tout en appartenant au schéma d’adressage des entités, les partitions définissent une étendue pour les transactions (pour en savoir plus, consultez [Transactions de groupe d’entités](#entity-group-transactions) ci-dessous) et constituent la base de la méthode de mise à l’échelle du service de Table. Pour plus d’informations sur les partitions, consultez [Objectifs de scalabilité et de performances du Stockage Azure](../../storage/common/storage-scalability-targets.md).  

Dans le service de Table, un nœud individuel traite une ou plusieurs partitions complètes et le service se met à l’échelle en équilibrant la charge des partitions de manière dynamique sur les nœuds. Si un nœud est en sous-charge, le service de Table peut *fractionner* la plage de partitions traitées par ce nœud en différents nœuds. En cas de réduction du trafic, le service peut *fusionner* les plages de partitions à partir des nœuds silencieux en un nœud unique.  

Pour plus d’informations sur les détails internes du service de Table et notamment la façon dont le service gère les partitions, voir le document [Stockage Microsoft Azure : un service de stockage cloud hautement disponible à cohérence forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Transactions de groupe d’entités
Dans le service de Table, les transactions de groupe d'entités (EGT) constituent l'unique mécanisme intégré pour effectuer des mises à jour atomiques entre plusieurs entités. Les EGT sont également parfois appelées *transactions par lots*. Les EGT peuvent uniquement fonctionner sur des entités stockées dans la même partition (autrement dit, elles partagent la même clé de partition dans une table donnée). Par conséquent, chaque fois que vous avez besoin d’un comportement transactionnel atomique sur plusieurs entités, vous devez vérifier que ces entités sont dans la même partition. Ceci justifie souvent la conservation de plusieurs types d'entité dans la même table (et partition) au lieu de l'utilisation de plusieurs tables pour différents types d'entité. Une seule EGT peut traiter jusqu'à 100 entités.  Si vous envoyez plusieurs EGT simultanées pour traitement, vérifiez bien que ces EGT ne fonctionnent pas sur des entités communes aux différentes EGT. Sinon, le traitement risque d’être retardé.

Les EGT représentent également un éventuel compromis à prendre en compte dans votre conception. Autrement dit, l’utilisation de plusieurs partitions augmente la scalabilité de votre application, car Azure a plus d’occasions d’équilibrer la charge des requêtes sur les nœuds. Toutefois, l’utilisation de plusieurs partitions peut limiter la capacité de votre application à effectuer des transactions atomiques et à assurer une cohérence forte pour vos données. Par ailleurs, il s’agit d’objectifs de scalabilité spécifiques au niveau d’une partition qui peuvent limiter le débit de transactions attendu pour un nœud unique. Pour plus d’informations sur les objectifs de scalabilité pour les comptes de stockage et le service de Table Azure, consultez [Scalabilité du Stockage Azure et objectifs de performances](../../storage/common/storage-scalability-targets.md).   

## <a name="capacity-considerations"></a>Considérations relatives à la capacité
Le tableau suivant décrit certaines valeurs clés à connaître quand vous concevez une solution de service de Table :  

| Capacité totale d'un compte de stockage Azure | 500 To |
| --- | --- |
| Nombre de tables dans un compte de stockage Azure |Limité uniquement par la capacité du compte de stockage |
| Nombre de partitions dans une table |Limité uniquement par la capacité du compte de stockage |
| Nombre d'entités dans une partition |Limité uniquement par la capacité du compte de stockage |
| Taille d'une entité individuelle |Jusqu’à 1 Mo, avec un maximum de 255 propriétés (y compris **PartitionKey**, **RowKey** et **Timestamp**) |
| Taille de la **PartitionKey** |Chaîne jusqu'à 1 Ko |
| Taille de la **RowKey** |Chaîne jusqu'à 1 Ko |
| Taille d'une transaction ETG |Une transaction peut inclure au plus 100 entités et la charge utile doit être inférieure à 4 Mo. Une transaction EGT ne peut mettre à jour une entité qu'une seule fois. |

Pour plus d'informations, consultez la rubrique [Présentation du modèle de données du service de Table](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

## <a name="cost-considerations"></a>Considérations relatives au coût
Le stockage Table est relativement peu coûteux, mais vous devez ajouter les estimations de coût de l’utilisation de capacité et de la quantité de transactions quand vous envisagez une solution qui utilise le service de Table. Toutefois, dans de nombreux scénarios, le stockage de données dénormalisées ou dupliquées pour améliorer les performances ou la scalabilité de votre solution est une approche appropriée. Pour plus d’informations sur la tarification, consultez la page [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Étapes suivantes

- [Modèles de conception de table](table-storage-design-patterns.md)
- [Modélisation des relations](table-storage-design-modeling.md)
- [Conception pour l’interrogation](table-storage-design-for-query.md)
- [Chiffrement des données de table](table-storage-design-encrypt-data.md)
- [Conception pour la modification de données](table-storage-design-for-modification.md)
