<properties
    pageTitle="DocumentDB-fiók létrehozása | Microsoft Azure"
    description="NoSQL adatbázis felépítése az Azure DocumentDB szolgáltatással. Kövesse ezeket az utasításokat egy DocumentDB-fiók létrehozásához és a rendkívül gyors, globális méretű NoSQL adatbázis felépítésének elkezdéséhez." 
    keywords="build a database"
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/16/2016"
    ms.author="mimig"/>

# DocumentDB-fiók létrehozása az Azure-portál használatával

> [AZURE.SELECTOR]
- [Azure portálra](documentdb-create-account.md)
- [Azure CLI és ARM](documentdb-automation-resource-manager-cli.md)

Egy adatbázisnak a Microsoft Azure DocumentDB szolgáltatással végzett felépítéséhez a következőkre van szükség:

- Azure-fiókkal kell rendelkeznie. Ha még nem rendelkezik fiókkal, beszerezhet egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free). 
- Létre kell hoznia egy DocumentDB-fiókot.  

DocumentDB-fiókot az Azure-portállal, Azure Resource Manager-sablonokkal vagy Azure parancssori felülettel (CLI-vel) hozhat létre. Ez a cikk bemutatja, hogyan hozhat létre adatbázisfiókot az Azure-portállal. Ha az Azure Resource Manager vagy az Azure CLI használatával szeretne fiókot létrehozni: [DocumentDB-adatbázisfiók létrehozásának automatizálása](documentdb-automation-resource-manager-cli.md).

Nem ismeri még a DocumentDB szolgáltatást? Nézze meg [ezt](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) a Scott Hanselman által készített négy perces videót arról, hogyan végezheti el a legáltalánosabb feladatokat az online portálon.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## Következő lépések

Most, hogy már rendelkezik DocumentDB-fiókkal, a következő lépés az adatbázis felépítése. A következő módszerekkel hozhat létre adatbázist:

- Az Azure-portál használatával, amit a [DocumentDB-adatbázis létrehozása az Azure-portállal](documentdb-create-database.md) című szakasz ismertet.
- A GitHub [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) tárában a [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) projektben lévő C# .NET mintákkal.
- Az átfogó oktatóanyagokkal: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) vagy [Python](documentdb-python-application.md).
- A [DocumentDB SDK-kkal](documentdb-sdk-dotnet.md). A DocumentDB .NET, Java, Python, Node.js és JavaScript API SDK-kkal rendelkezik.


Az adatbázis létrehozása után [egy vagy több gyűjteményt kell hozzáadnia](documentdb-create-collection.md) az adatbázishoz, majd [dokumentumokat kell hozzáadnia](documentdb-view-json-document-explorer.md) a gyűjteményekhez.

Miután dokumentumokat adott a gyűjteményhez, a [DocumentDB SQL](documentdb-sql-query.md) eszközzel [lekérdezéseket végezhet](documentdb-sql-query.md#executing-queries) a dokumentumokon a portálban lévő [Query Explorer](documentdb-query-collections-query-explorer.md) szolgáltatással, a [REST API-val](https://msdn.microsoft.com/library/azure/dn781481.aspx) vagy az egyik [SDK-val](documentdb-sdk-dotnet.md).

Az alábbi forrásokból többet is megtudhat a DocumentDB szolgáltatásról:

-   [DocumentDB képzési terv](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-   [A DocumentDB erőforrás-modellje és fogalmai](documentdb-resources.md)



<!--HONumber=Jun16_HO2-->


