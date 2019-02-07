---
title: Az Azure IoT Central alkalmazásban az adatok elemzése |} A Microsoft Docs
description: Az Azure IoT Central alkalmazásban az adatok elemzéséhez.
author: lmasieri
ms.author: lmasieri
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 851a509421924a2ea6607020e7749c10dcbec01d
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773612"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Az adatok elemzéséhez analytics használata


*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*


Az Azure IoT Central jelentéssel bírnak az eszközök adatainak nagy mennyiségű megoldásban gazdag analitikai funkciókat biztosít. Első lépésként látogasson el a **Analytics** a bal oldali navigációs menüben. 

## <a name="querying-your-data"></a>Az adatok lekérdezése

Választania kell egy **eszköz beállítása**, adja hozzá egy **szűrő** (nem kötelező), és válassza ki egy **időszakban** a kezdéshez. Miután elkészült, kattintson a *találat megjelenítése a* elindítani az adatai megjelenítéséhez.


* **Eszköz beállítása:** A [eszköz set](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) az eszközök felhasználó által definiált csoportjai. Ha például Oakland az összes hűtőszekrények, vagy az összes rev 2.0-s szél csatlakozó.

<!---
to-do: confirm if 10 is the max number of filters
to-do: do we need to explain how fiters work?
--->

* **Szűrők:** Igény szerint hozzáadhat szűrők a keresést, hogy az adatok alaposabb vizsgálatát. Egyszerre legfeljebb 10 szűrőket is hozzáadhat. Például Oakland az összes hűtőszekrények belül található, amelyek hőmérséklet kellett volna nyissa meg a fenti 60 fok. 
* **Időszak:** Alapértelmezés szerint azt fogja lekérdezni az adatok az elmúlt 10 percben. Egy előre meghatározott időpontra tartományok módosítani ezt az értéket, vagy válasszon egy egyéni időszakot. 

 ![Analytics-lekérdezés](media/howto-create-analytics-experimental/analytics-query.png)

## <a name="visualizing-your-data"></a>Az adatok megjelenítése

Az adatok már kérdezhető le, miután fogja jelenítenek meg, akkor indul el. Megjelenítése/elrejtése mértékek is, adatokat a rendszer összesített, és további módjának módosítása másik eszköztulajdonságok alapján felosztani az adatokat.  

* **Felosztási szempont:** Adatok felosztása eszköztulajdonságok alapján lehetővé teszi további részletes le betekintést az adatokba. Például akkor is feloszthatja az eszköz Azonosítóját vagy a hely által az eredményeket.
<!---
to-do: confirm if 10 is the max number of measurements
--->
* **Mértékek:** Kiválaszthatja, hogy az eszközök által jelentett egyszerre legfeljebb 10 különféle telemetriai elemek megjelenítése/elrejtése. Mértékek például hőmérséklettel és páratartalommal kapcsolatos dolgot. 
* **Összesítés:** Alapértelmezés szerint azt összesített adatok alapján az átlagos, de az adatok összesítése módosítsa valami mást saját igényei szerint választhatja. 

   ![Felosztási szempont Analytics vizualizációs](media/howto-create-analytics-experimental/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interakció adataival

Hogy különféle módokon, amelyben további módosíthatja a lekérdezés eredményeit, a Vizualizáció igényeinek. A diagram nézet és a egy rácsnézet között, bejövő és kimenő nagyítás, frissítse az adatkészletet, és módosítsa a hogyan sorok jelennek meg.

* **Rács megjelenítése:** Az eredmények táblázatos formátumban, lehetővé téve a megtekintheti az adott érték az egyes elérhető lesz. Ez a nézet is megfelel az akadálymentességi sztenderdeknek. 
* **Diagram megjelenítése:** Az eredményeket könnyen kritikus pontok elkerülése érdekében felfelé vagy lefelé trendek és rendellenességek sor formátumban jelennek meg. 

 ![A rácsnézet elemzési megjelenítése](media/howto-create-analytics-experimental/analytics-showgrid.png)

Nagyítás lehetővé teszi az adatok alaposabb vizsgálatát. Ha azt tapasztalja, hogy egy adott időszakban, összpontosítson az eredményhalmaz belül szeretné, használja a kurzort megnyitása a terület, amelyeket szeretne nagyítása, és a rendelkezésre álló vezérlők segítségével hajtsa végre az alábbi műveletek egyikét:
* **Nagyítás:** Miután kiválasztotta, hogy egy adott időszakban, Nagyítás a engedélyezve lesz, és lehetővé teszi az adatokhoz a nagyításhoz.
* **Kicsinyítés:** Ez a vezérlő lehetővé teszi, hogy az utolsó nagyítás kicsinyítés egy szinttel. Például, ha már ráközelít az adatokhoz három alkalommal, kicsinyítés vesz igénybe egy lépésben egy biztonsági másolatot.
* **Nagyítás alaphelyzetbe állítása:** Nagyítás különböző szintjeit végzett el, miután a Nagyítás visszaállítási vezérlő használatával térjen vissza az eredeti eredményhalmaz. 

 ![Hajtsa végre az adatokon a Nagyítás](media/howto-create-analytics-experimental/analytics-zoom.png)


A vonal stílusának igény szerint módosíthatja. Rendelkezésére a négy beállítás közül választhat:
* **Vonal:** Az adatpontok közötti egy egybesimított sor lesz összeállítva. 
* **Zökkenőmentes:** Minden pont között olyan görbe lesz összeállítva.
* **. Lépés:** A diagramon minden pont közötti hoz létre egy lépés diagram
* **Pontdiagram:** Minden pont lesz ábrázolható a diagram őket sorok nélkül. 

 ![Különböző típusa érhető el az Analytics](media/howto-create-analytics-experimental/analytics-linetypes.png)

Végül rendezheti az adatokat az y tengely között válassza ki a három mód közül:

* **Halmozott:** Minden mérték grafikon halmozott, és a gráfok mindegyike saját y tengely rendelkezik. A halmozott diagramok akkor hasznos, ha a kiválasztott több mérések rendelkezik, és szeretné, hogy ezeknek a méréseknek különböző nézetét.
* **Trendvonalat:** Minden mérték grafikon egyik y tengely van korrelálnak, de az y tengely értékei változnak a kijelölt mérték alapján. Trendvonalat diagramok akkor hasznos, ha átfedő több mértéket, és szeretné látni a minták között ezek az intézkedések ugyanazon időtartományra.
* **Megosztott y tengely:** Összes gráf megoszthatja az azonos y tengely, és ne módosítsa az értékeket a tengelyen. Megosztott y tengely diagramok akkor hasznos, ha egyetlen tekintse meg az adatokat a felosztás által szeletelés közben szeretne.

 ![Az adatok elrendezése y tengely között a különböző vizualizációs módok](media/howto-create-analytics-experimental/analytics-yaxis.png)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan hozhat létre egyéni analytics az Azure IoT Central alkalmazáshoz, a javasolt következő lépésre a következő:

> [!div class="nextstepaction"]
> [Készítse elő és a egy Node.js-alkalmazás csatlakoztatása](howto-connect-nodejs-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)