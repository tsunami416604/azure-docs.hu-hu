---
title: Eseményalapú eseményindítók létrehozása az Azure Data Factoryban
description: Ismerje meg, hogyan hozhat létre egy eseményindítót az Azure Data Factoryban, amely egy eseményre adott válaszként futó folyamatot futtat.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: e4301afafb48fb9a1b0c9e36dde9800e2b8390f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443924"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Eseményre válaszul futó folyamatot futtató eseményindító létrehozása

Ez a cikk ismerteti az eseményalapú eseményindítók, amelyek a Data Factory folyamatok hozhat létre.

Az eseményvezérelt architektúra (EDA) egy gyakori adatintegrációs minta, amely magában foglalja az események et, észlelést, felhasználást és reakciót. Az adatintegrációs forgatókönyvek gyakran megkövetelik a Data Factory-ügyfelektől, hogy folyamatokat indítsanak el események, például az Azure Storage-fiókban lévő fájl érkezése vagy törlése alapján. A Data Factory mostantól integrálva van az [Azure Event Griddel,](https://azure.microsoft.com/services/event-grid/)amely lehetővé teszi a folyamatok eseményen való aktiválását.

A funkció tízperces bemutatásához és bemutatásához tekintse meg az alábbi videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> Az ebben a cikkben ismertetett integráció az [Azure Event Gridtől](https://azure.microsoft.com/services/event-grid/)függ. Győződjön meg arról, hogy az előfizetés regisztrálva van az Event Grid erőforrás-szolgáltatónál. További információ: [Resource providers and types](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete

Ez a szakasz bemutatja, hogyan hozhat létre eseményindítót az Azure Data Factory felhasználói felületén belül.

1. Ugrás a **szerzői vászonra**

1. A bal alsó sarokban kattintson az **Eseményindítók gombra**

1. Kattintson **a + Új** gombra, amely megnyitja a trigger oldalnavigációs eszköz létrehozását

1. **Esemény** eseménytípus kiválasztása

    ![Új eseményindító létrehozása](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Válassza ki a tárfiókot az Azure-előfizetés legördülő vagy manuálisan a Storage-fiók erőforrás-azonosító használatával. Válassza ki, hogy melyik tárolóban szeretné az eseményeket belátni. A tárolókiválasztása nem kötelező, de vegye figyelembe, hogy az összes tároló kiválasztása események nagy számához vezethet.

   > [!NOTE]
   > Az eseményindító jelenleg csak az Azure Data Lake Storage Gen2 és az általános célú 2-es verziójú storage-fiókokat támogatja. Az Azure Event Grid korlátozása miatt az Azure Data Factory csak legfeljebb 500 eseményindítót támogat tárfiókonként.

1. A **Blob elérési útja kezdődik,** és blob elérési út **végződik** tulajdonságok lehetővé teszik, hogy adja meg a tárolók, mappák és blob nevek, amelyek hez eseményeket szeretne fogadni. Az eseményindítóhoz legalább egy ilyen tulajdonságot meg kell határozni. Használhatja a különböző minták mindkét **Blob elérési út kezdődik,** és **blob elérési út végződik** tulajdonságokkal, amint azt a példák at később a cikkben látható.

    * **A blob elérési útja a következővel kezdődik:** A blob elérési útjának egy mappa elérési útjával kell kezdődnie. Az érvényes `2018/` `2018/april/shoes.csv`értékek közé tartozik a és a. Ez a mező nem jelölhető ki, ha nincs kijelölve tároló.
    * **A blob elérési útja a következővel végződik:** A blob elérési útjának fájlnévvel vagy kiterjesztéssel kell végződnie. Az érvényes `shoes.csv` `.csv`értékek közé tartozik a és a. A tároló- és mappanév megadása nem kötelező, de `/blobs/` ha meg van adva, akkor egy szegmensnek kell elválasztania őket. A "rendelések" nevű tároló értéke például a. `/orders/blobs/2018/april/shoes.csv` Ha bármelyik tárolóban meg szeretne adni egy mappát, hagyja ki a kezdő "/" karaktert. Például `april/shoes.csv` egy eseményt indít el `shoes.csv` a mappában megnevezett bármely fájlban, bármely tárolóban "április" néven. 

1. Válassza ki, hogy az eseményindító válaszol-e egy **Blob által létrehozott** eseményre, a Blob által törölt **eseményre** vagy mindkettőre. A megadott tárolási helyen minden esemény elindítja az eseményindítóhoz társított Data Factory folyamatok.

    ![Az eseményindító konfigurálása](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Válassza ki, hogy az eseményindító figyelmen kívül hagyja-e a blobokat nulla bájttal.

1. Az aktiválás konfigurálása után kattintson a **Tovább: Adatelőnézet gombra.** Ez a képernyő a meglévő blobokat jeleníti meg, amelyek megfelelnek az eseményindító konfigurációjának. Győződjön meg arról, hogy adott szűrőkkel van el. A túl tág szűrők konfigurálása nagy számú létrehozott/törölt fájlnak felelhet meg, és jelentősen befolyásolhatja a költségeket. A szűrési feltételek ellenőrzése után kattintson a **Befejezés**gombra.

    ![Eseményindító adatainak előnézete](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Ha egy folyamatot csatolni szeretne ehhez az eseményindítóhoz, lépjen a folyamatvászonra, és kattintson az **Eseményindító hozzáadása** gombra, és válassza az **Új/Szerkesztés lehetőséget.** Amikor megjelenik az oldalsó navigációs eszköz, kattintson a **Trigger kiválasztása...** legördülő menüre, és válassza ki a létrehozott eseményindítót. Kattintson a **Tovább: Az adatok előnézete gombra** kattintva ellenőrizze, hogy a konfiguráció helyes-e, majd a **Tovább gombra** az adatelőnézet helyes ellenőrzéséhez.

1. Ha a folyamat paraméterekkel rendelkezik, megadhatja őket az eseményindító futtatja paraméter oldali navigációs eszköz. Az eseményindító rögzíti a mappa elérési útját és `@triggerBody().folderPath` a `@triggerBody().fileName`fájl nevét a blob a tulajdonságok és. Ezen tulajdonságok értékeinek egy folyamatban való használatához le kell képeznie a tulajdonságokat a folyamat paramétereihez. Miután leképezi a tulajdonságokat a paraméterekhez, hozzáférhet `@pipeline().parameters.parameterName` az eseményindító által rögzített értékekhez a kifejezésen keresztül a folyamat teljes területén. Ha elkészült, kattintson a **Befejezés** gombra.

    ![Tulajdonságok hozzárendelése csővezeték-paraméterekhez](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Az előző példában az eseményindító úgy van konfigurálva, hogy a rendszer akkor aktiválódik, amikor egy .csv végződő blobelérési út jön létre a tárolóminta-adatok ban a mappa eseménytesztelésében. A **folderPath** és **fileName** tulajdonságok rögzítik az új blob helyét. Ha például a MoviesDB.csv fájl hozzáadódik az elérési `@triggerBody().folderPath` úthoz mintaadat-adat/eseménytesztelés, értéke `sample-data/event-testing` `@triggerBody().fileName` `moviesDB.csv`és értéke. Ezek az értékek a példában a folyamat `sourceFolder` `sourceFile` paramétereihez vannak rendelve, és amelyek a folyamat `@pipeline().parameters.sourceFolder` `@pipeline().parameters.sourceFile` teljes folyamatában használhatók.

## <a name="json-schema"></a>JSON-séma

Az alábbi táblázat áttekintést nyújt az eseményalapú eseményindítókhoz kapcsolódó sémaelemekről:

| **JSON elem** | **Leírás** | **Típus** | **Engedélyezett értékek** | **Szükséges** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Hatókör** | A tárfiók Azure Resource Manager erőforrásazonosítója. | Sztring | Azure erőforrás-kezelőazonosítója | Igen |
| **események** | Az esemény típusa, amely az eseményindítót tüzelésre készteti. | Tömb    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Igen, ezeknek az értékeknek bármilyen kombinációja. |
| **blobPathBeginsWith** | A blob elérési útjának az eseményindító indításához megadott mintával kell kezdődnie. Például `/records/blobs/december/` csak a `december` `records` tároló alatti mappában lévő blobok eseményindítóját indítja el. | Sztring   | | A tulajdonságok közül legalább az egyikhez meg `blobPathBeginsWith` `blobPathEndsWith`kell adnia egy értéket: vagy . |
| **blobPathEndsWith** | A blob elérési útja véget kell vetni a minta az eseményindító a tűz. Például `december/boxes.csv` csak egy `boxes` `december` mappában elnevezett blobok eseményindítóját indítja el. | Sztring   | | A tulajdonságok közül legalább az egyikhez meg `blobPathBeginsWith` `blobPathEndsWith`kell adnia egy értéket: vagy . |
| **ignoreEmptyBlobs** | Azt jelzi, hogy a nulla bájtos blobok elindítják-e a folyamatfuttatást. Alapértelmezés szerint ez igaz értékre van állítva. | Logikai | true (igaz) vagy false (hamis) | Nem |

## <a name="examples-of-event-based-triggers"></a>Példák eseményalapú eseményindítókra

Ez a szakasz példákat mutat be az eseményalapú eseményindító-beállításokra.

> [!IMPORTANT]
> Az elérési út `/blobs/` szegmensét az alábbi példákban látható módon kell megadnia, amikor tárolót és mappát, tárolót és fájlt, illetve tárolót, mappát és fájlt ad meg. **BlobPathBeginsWith**esetén a Data Factory felhasználói `/blobs/` felülete automatikusan hozzáadódik a mappa és a tároló neve között az eseményindító JSON-ban.

| Tulajdonság | Példa | Leírás |
|---|---|---|
| **A blob elérési útja a** | `/containername/` | A tárolóban lévő bármely blob eseményeit fogadja. |
| **A blob elérési útja a** | `/containername/blobs/foldername/` | A tárolóban és `containername` `foldername` a mappában lévő blobok eseményeinek fogadása. |
| **A blob elérési útja a** | `/containername/blobs/foldername/subfoldername/` | Almappára is hivatkozhat. |
| **A blob elérési útja a** | `/containername/blobs/foldername/file.txt` | A `file.txt` `containername` tároló alatti `foldername` mappában megnevezett blob eseményeinek fogadása. |
| **A blob elérési útja** | `file.txt` | Események fogadása egy `file.txt` adott útvonalon elnevezett blobhoz. |
| **A blob elérési útja** | `/containername/blobs/file.txt` | A tároló `file.txt` `containername`alatt elnevezett blob eseményeinek fogadása. |
| **A blob elérési útja** | `foldername/file.txt` | Események fogadása egy `file.txt` mappában `foldername` nevű blob bármely tároló alatt. |

## <a name="next-steps"></a>További lépések
Az eseményindítókról a [Folyamat végrehajtása és az eseményindítók](concepts-pipeline-execution-triggers.md#triggers)című témakörben talál részletes információt.
