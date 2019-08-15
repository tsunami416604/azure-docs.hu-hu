---
title: Eseményvezérelt eseményindítók létrehozása a Azure Data Factoryban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre eseményindítót olyan Azure Data Factoryban, amely egy adott eseményre reagálva egy folyamatot futtat.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 32edacb7dd66274757359c4eb0e8c169995026ce
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019521"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Olyan eseményindító létrehozása, amely egy adott eseményre válaszul futtat egy folyamatot

Ez a cikk a Data Factory folyamatokban létrehozható eseményvezérelt eseményindítókat ismerteti.

Az eseményvezérelt architektúra (EDA) olyan közös adatintegrációs minta, amely az események gyártását, észlelését, felhasználását és reagálását foglalja magában. Az adatintegrációs forgatókönyvek gyakran megkövetelik Data Factory ügyfeleknek, hogy olyan események alapján indítsák el a folyamatokat, mint például az Azure Storage-fiókban lévő fájl megérkezése vagy törlése. A Data Factory mostantól integrálva van [Azure Event Gridval](https://azure.microsoft.com/services/event-grid/), amely lehetővé teszi, hogy egy eseményen aktiválja a folyamatokat.

A szolgáltatás tíz percen belüli bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> A cikkben ismertetett integráció a Azure Event Gridtól függ [](https://azure.microsoft.com/services/event-grid/). Győződjön meg arról, hogy az előfizetés regisztrálva van a Event Grid erőforrás-szolgáltatónál. További információ: erőforrás- [szolgáltatók és típusok](../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete

Ebből a szakaszból megtudhatja, hogyan hozhat létre egy eseményvezérelt eseményindítót a Azure Data Factory felhasználói felületen belül.

1. Ugrás a **szerzői vászonra**

2. A bal alsó sarokban kattintson az eseményindítók gombra.

3. Kattintson az **+ új** elemre, amely megnyitja a trigger oldal létrehozása NAV-t

4. Eseményindító típusú **esemény** kiválasztása

![Új eseményindító létrehozása](media/how-to-create-event-trigger/event-based-trigger-image1.png)

5. Válassza ki a Storage-fiókját az Azure-előfizetés legördülő menüből, vagy manuálisan használja a Storage-fiókja erőforrás-AZONOSÍTÓját. Válassza ki, hogy melyik tárolón történjen az események betöltése. A tároló kiválasztása nem kötelező, de ne felhagyható, hogy az összes tároló kijelölése nagy számú eseményt eredményezhet.

   > [!NOTE]
   > Az Event trigger jelenleg csak a Azure Data Lake Storage Gen2 és az általános célú 2-es verziójú Storage-fiókokat támogatja. Egy Azure Event Grid korlátozás miatt Azure Data Factory csak a legfeljebb 500 eseményindítót támogatja.

6. A **blob** elérési útja és a **blob elérési** útja a tulajdonságok segítségével megadhatja azokat a tárolókat, mappákat és blob-neveket, amelyekhez eseményeket szeretne kapni. Az esemény-eseményindítónak legalább egy ilyen tulajdonságot meg kell határoznia. Különböző mintákat használhat a **blob elérési útjához** , a **blob elérési útja** pedig tulajdonságok használatával végződik, ahogy az ebben a cikkben szereplő példákban is látható.

    * **A blob elérési útja a következőket veszi kezdettel:** A blob elérési útnak a mappa elérési útjával kell kezdődnie. Az érvényes értékek `2018/` a `2018/april/shoes.csv`következők: és. Ez a mező nem választható ki, ha nincs kiválasztva tároló.
    * **A blob elérési útja az alábbiakkal végződik:** A blob elérési útjának fájlnevet vagy kiterjesztést kell végződnie. Az érvényes értékek `shoes.csv` a `.csv`következők: és. A tároló és a mappa neve nem kötelező, de ha meg van adva, egy `/blobs/` szegmensnek kell elválasztani őket. Egy "Orders" nevű tároló például a következő értékkel `/orders/blobs/2018/april/shoes.csv`rendelkezhet:. Ha bármilyen tárolóban szeretne megadni egy mappát, hagyja ki a kezdő "/" karaktert. Például `april/shoes.csv` egy eseményt indít el bármely `shoes.csv` , a mappában található "April" nevű fájlon bármely tárolóban. 

7. Válassza ki, hogy az eseményindító válaszol-e a **blob által létrehozott** eseményre, a **blob törölt** eseményére vagy mindkettőre. A megadott tárolási helyen minden esemény elindítja az eseményindítóhoz társított Data Factory folyamatokat.

    ![Az eseményindító konfigurálása](media/how-to-create-event-trigger/event-based-trigger-image2.png)

8. Miután konfigurálta az aktiválást, kattintson a **Next (tovább) gombra: Az adatelőnézet**. Ezen a képernyőn láthatók azok a meglévő Blobok, amelyek megfelelnek az esemény-eseményindító konfigurációjának. Győződjön meg arról, hogy adott szűrőket adott meg. A túl széles szűrők konfigurálása nagy számú, létrehozott/törölt fájlhoz is tartozhat, és jelentősen befolyásolhatja a költségeket. A szűrési feltételek ellenőrzése után kattintson a **Befejezés**gombra.

    ![Esemény-eseményindító adatelőnézete](media/how-to-create-event-trigger/event-based-trigger-image3.png)

9. Ha csatlakoztatni szeretne egy folyamatot ehhez az triggerhez, lépjen a folyamat vászonra, és kattintson az **trigger hozzáadása** lehetőségre, és válassza az **új/szerkesztés**lehetőséget. Amikor megjelenik az oldalsó NAV, kattintson az **trigger kiválasztása...** legördülő listára, és válassza ki a létrehozott triggert. Kattintson **a Tovább gombra: Az adatok** előnézete a konfiguráció megerősítéséhez, majd az adatelőnézet érvényesítése elem melletti helyes.

10. Ha a folyamat paraméterekkel rendelkezik, akkor megadhatja őket az trigger futtatási paraméterének navigációs oldalán. Az eseményindító rögzíti a blob mappájának elérési útját és fájlnevét a tulajdonságok `@triggerBody().folderPath` és `@triggerBody().fileName`a között. Ezen tulajdonságok értékének egy folyamaton való használatához a tulajdonságokat a folyamat paramétereinek kell képeznie. Miután a tulajdonságokat hozzárendelte a paraméterekhez, az trigger által rögzített értékeket a folyamat során a `@pipeline().parameters.parameterName` kifejezésen keresztül érheti el. Ha elkészült, kattintson a **Befejezés** gombra.

    ![Tulajdonságok megfeleltetése a folyamat paramétereinek](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Az előző példában az eseményindító úgy van beállítva, hogy a. csv fájlban végződő blob-elérési út a tárolóban, a Container Sample---ban a mappa esemény-tesztelés területén jön létre. A **folderPath** és a **filename** tulajdonság rögzíti az új blob helyét. Ha például a MoviesDB. csv hozzá van adva az elérési út mintája – az adatelemzési `@triggerBody().folderPath` és az esemény- `sample-data/event-testing` tesztelés `@triggerBody().fileName` , a értéke `moviesDB.csv`és a értéke. Ezek az értékek a példában `sourceFolder` a folyamat paramétereinek megfelelően vannak leképezve, és `sourceFile` a folyamat során a és `@pipeline().parameters.sourceFolder` `@pipeline().parameters.sourceFile` a szolgáltatásban is használhatók.

## <a name="json-schema"></a>JSON-séma

Az alábbi táblázat áttekintést nyújt az eseményvezérelt eseményindítókkal kapcsolatos séma-elemekről:

| **JSON-elem** | **Leírás** | **Típus** | **Megengedett értékek** | **Kötelező** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **hatókör** | A Storage-fiók Azure Resource Manager erőforrás-azonosítója. | Sztring | Azure Resource Manager azonosítója | Igen |
| **események** | A triggert tüzet kiváltó események típusa. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Igen, az értékek bármely kombinációja. |
| **blobPathBeginsWith** | A blob elérési útjának a triggerhez megadott mintázattal kell kezdődnie. Például `/records/blobs/december/` csak a `records` tárolóban lévő `december` mappában lévő Blobok eseményindítóját kell kiváltani. | Sztring   | | Meg kell adnia egy értéket a következő tulajdonságok közül legalább egy számára: `blobPathBeginsWith` vagy. `blobPathEndsWith` |
| **blobPathEndsWith** | A blob elérési útjának a triggerhez megadott mintázattal kell végződnie. Például `december/boxes.csv` csak a `december` mappában `boxes` lévő Blobok eseményindítóját kell kiváltani. | Sztring   | | Meg kell adnia egy értéket a következő tulajdonságok közül legalább egy számára: `blobPathBeginsWith` vagy. `blobPathEndsWith` |

## <a name="examples-of-event-based-triggers"></a>Példák eseményvezérelt eseményindítóra

Ez a szakasz az eseményvezérelt eseményindítók beállításait mutatja be.

> [!IMPORTANT]
> Az elérési út szegmensét az alábbi példákban látható módon kell megadnia, amikor tárolót, mappát, tárolót, fájlt vagy tárolót, mappát és fájlt ad meg. `/blobs/` A **blobPathBeginsWith**esetében a Data Factory felhasználói felület automatikusan hozzáadja `/blobs/` a mappa és a tároló nevét az trigger JSON-ban.

| Tulajdonság | Példa | Leírás |
|---|---|---|
| **A blob elérési útja** | `/containername/` | Eseményeket fogad a tárolóban lévő összes blobhoz. |
| **A blob elérési útja** | `/containername/blobs/foldername/` | Eseményeket fogad a tárolóban és `containername` `foldername` a mappában található összes blobhoz. |
| **A blob elérési útja** | `/containername/blobs/foldername/subfoldername/` | Egy almappára is hivatkozhat. |
| **A blob elérési útja** | `/containername/blobs/foldername/file.txt` | A `file.txt` `containername` tárolóban lévő `foldername` mappában található blob eseményeinek fogadása. |
| **A blob elérési útja véget ér** | `file.txt` | A bármely elérési úton megnevezett `file.txt` blob eseményeinek fogadása. |
| **A blob elérési útja véget ér** | `/containername/blobs/file.txt` | A tárolóban `file.txt` `containername`megnevezett blob eseményeinek fogadása. |
| **A blob elérési útja véget ér** | `foldername/file.txt` | Eseményeket fogad a mappában `file.txt` lévő `foldername` Blobok bármelyik tárolóban. |

## <a name="next-steps"></a>További lépések
Az eseményindítókkal kapcsolatos részletes információkért lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md#triggers).
