---
title: "Írásvédett redundáns tárolás az Azure-ban elérésével hiba szimulálása |} Microsoft Docs"
description: "Írásvédett georedundáns tárolás elérésével hiba szimulálása"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/05/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: f709e216f9308c4405776b25ca44b0aaddd3d3f8
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Írásvédett redundáns tárolás elérésével hiba szimulálása

Ez az oktatóanyag a két egy sorozat része. Ebben az oktatóanyagban meg el egy hibás válasz Fiddler irányuló kérelmek esetén a [írásvédett georedundáns](../common/storage-redundancy.md#read-access-geo-redundant-storage) storage-fiók (RA-GRS) segítségével szimulálhatja hibát, és az alkalmazást a másodlagos végponti olvasni.

![A forgatókönyv-alkalmazás](media/storage-simulate-failure-ragrs-account-app/scenario.png)

A második rész a sorozat, megismerheti, hogyan:

> [!div class="checklist"]
> * Futtassa, és az alkalmazás felfüggesztése
> * Hiba szimulálása
> * Elsődleges végpont visszaállítás szimulálása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Töltse le és telepítse [Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Az oktatóanyag teljesítéséhez végrehajtotta az előző tárolási oktatóanyag: [legyen az alkalmazás adataihoz az Azure storage magas rendelkezésre állású][previous-tutorial].

## <a name="launch-fiddler"></a>Indítsa el a fiddler

Nyissa meg Fiddler, válassza ki **szabályok** és **testreszabása szabályok**.

![Testre szabhatja a Fiddler szabályok](media/storage-simulate-failure-ragrs-account-app/figure1.png)

A Fiddler ScriptEditor ábrázoló elindítja a **SampleRules.js** fájlt. Ez a fájl segítségével testre szabhatja a Fiddler. Illessze be a következő példakód a a `OnBeforeResponse` függvény. Az új kódot annak érdekében, hogy a létrehozott logikai nincs megvalósítva azonnal megjegyzésként szerepel. Válassza ki a befejezést **fájl** és **mentése** menti a módosításokat.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![Illessze be az egyéni szabály](media/storage-simulate-failure-ragrs-account-app/figure2.png)

## <a name="start-and-pause-the-application"></a>Indítsa el, és az alkalmazás felfüggesztése

A Visual Studio, nyomja le a **F5** , vagy válasszon **Start** az alkalmazás hibakeresését végzi el. Az alkalmazás az elsődleges végpont a olvasásakor megkezdése után nyomja le az **egyik billentyűre** a konzolablakban felfüggeszti az alkalmazást.

## <a name="simulate-failure"></a>Hiba szimulálása

Az alkalmazással felfüggesztve most állítsa vissza az egyéni szabály mentettük a Fiddler egy előző lépést. Ez a kód minta keresni kezdi a RA-GRS-tárfiókot kérelmek, és hogy az elérési út tartalmazza-e a lemezkép nevét `HelloWorld`, adja vissza, válaszkód `503 - Service Unavailable`.

Navigáljon a Fiddler, és válassza ki **szabályok** -> **testreszabása szabályok...** .  Állítsa vissza a következő sorokat ki, hogy lecseréli `STORAGEACCOUNTNAME` a tárfiók nevével. Válassza ki **fájl** -> **mentése** menti a módosításokat.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Az alkalmazás folytatásához nyomja le az **egyik billentyűre** .

Miután elindul az alkalmazás fut újra, a kérelmeket az elsődleges végpont veszik át a sikertelen. Az alkalmazás csatlakozni próbál az elsődleges végpont 5 alkalommal. Után a hiba küszöbértékét, az öt kísérletek a másodlagos csak olvasható végpontról mindent lekér a lemezképet. Után az alkalmazás sikeresen átveszi a kép 20 alkalommal a másodlagos végponti, az alkalmazás megkísérli az elsődleges végponthoz kapcsolódni. Ha az elsődleges végpont még nem érhető el, az alkalmazás folytatja a másodlagos végponti olvasásakor. Ez a minta a [áramköri megszakító](/azure/architecture/patterns/circuit-breaker.md) az előző oktatóanyag leírtak mintában.

![Illessze be az egyéni szabály](media/storage-simulate-failure-ragrs-account-app/figure3.png)

## <a name="simulate-primary-endpoint-restoration"></a>Elsődleges végpont visszaállítás szimulálása

A Fiddler egyéni szabályt, állítsa be az előző lépésben az elsődleges végpont a kérelem sikertelen lesz. Ahhoz, hogy szimulálása az elsődleges végpont újra működik, szúrjon logika eltávolítása a `503` hiba.

Az alkalmazás szüneteltethető, nyomja le az **egyik billentyűre**.

### <a name="remove-the-custom-rule"></a>Az egyéni szabály eltávolítása

Navigáljon a Fiddler, és válassza ki **szabályok** és **testreszabása szabályok...** .  Comment, vagy távolítsa el az egyéni logika a `OnBeforeResponse` függvény, hagyja az alapértelmezett függvény. Válassza ki **fájl** és **mentése** menti a módosításokat.

![Egyéni szabály eltávolítása](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Amikor végzett, nyomja le az **egyik billentyűre** folytatni az alkalmazást. Az alkalmazás továbbra is az elsődleges végpont a olvasásakor, amíg a találatok száma a 999 olvasási.

![Alkalmazás folytatása](media/storage-simulate-failure-ragrs-account-app/figure4.png)

## <a name="next-steps"></a>Következő lépések

Az adatsorozat két részén megismerte szimulálva írásvédett georedundáns tárolás például hogyan teszteléséhez hiba:

> [!div class="checklist"]
> * Futtassa, és az alkalmazás felfüggesztése
> * Hiba szimulálása
> * Elsődleges végpont visszaállítás szimulálása

Kövesse a hivatkozásra kattintva megtekintheti az előre elkészített tárolási minták.

> [!div class="nextstepaction"]
> [Az Azure storage parancsfájl minták](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md