---
title: Az Azure IoT Central alkalmazásban az adatok elemzése |} A Microsoft Docs
description: Az Azure IoT Central alkalmazásban az adatok elemzéséhez.
author: lmasieri
ms.author: lmasieri
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 83225af7cb2cbdff4a264693e30b2a813f6ec9c7
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214928"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Az adatok elemzéséhez analytics használata

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Az Azure IoT Central jelentéssel bírnak az eszközök adatainak nagy mennyiségű megoldásban gazdag analitikai funkciókat biztosít. Első lépésként látogasson el a **Analytics** a bal oldali navigációs menüben.

## <a name="querying-your-data"></a>Az adatok lekérdezése

Választania kell egy **eszköz beállítása**, adja hozzá egy **szűrő** (nem kötelező), és válassza ki egy **időszakban** a kezdéshez. Miután elkészült, kattintson a *találat megjelenítése a* elindítani az adatai megjelenítéséhez.

* **Eszköz beállítása:** A [eszköz set](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) az eszközök felhasználó által definiált csoportjai. Ha például Oakland az összes hűtőszekrények, vagy az összes rev 2.0-s szél csatlakozó.

* **Szűrők:** Igény szerint hozzáadhat szűrők a keresést, hogy az adatok alaposabb vizsgálatát. Egyszerre legfeljebb 10 szűrőket is hozzáadhat. Például Oakland az összes hűtőszekrények belül található, amelyek hőmérséklet kellett volna nyissa meg a fenti 60 fok.
* **Időszak:** Alapértelmezés szerint azt fogja lekérdezni az adatok az elmúlt 10 percben. Egy előre meghatározott időpontra tartományok módosítani ezt az értéket, vagy válasszon egy egyéni időszakot.

 ![Analytics-lekérdezés](media/howto-create-analytics-experimental/analytics-query.png)

## <a name="visualizing-your-data"></a>Az adatok megjelenítése

Az adatok már kérdezhető le, miután fogja jelenítenek meg, akkor indul el. Megjelenítése/elrejtése mértékek is, adatokat a rendszer összesített, és további módjának módosítása másik eszköztulajdonságok alapján felosztani az adatokat.  

* **Felosztási szempont:** Adatok felosztása eszköztulajdonságok alapján lehetővé teszi további részletes le betekintést az adatokba. Például akkor is feloszthatja az eszköz Azonosítóját vagy a hely által az eredményeket.

* **Mértékek:** Kiválaszthatja, hogy az eszközök által jelentett egyszerre legfeljebb 10 különféle telemetriai elemek megjelenítése/elrejtése. Mértékek például hőmérséklettel és páratartalommal kapcsolatos dolgot.

* **Összesítés:** Alapértelmezés szerint azt összesített adatok alapján az átlagos, de az adatok összesítése módosítsa valami mást saját igényei szerint választhatja.

   ![Felosztási szempont Analytics vizualizációs](media/howto-create-analytics-experimental/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interakció adataival

Számos lehetőséget kínál a lekérdezési eredmények képi megjelenítés igény szerint módosítani kell. A diagram nézet és a egy rácsnézet között, nagyíthat és, frissítse az adatkészletet, és módosítsa a hogyan sorok jelennek meg.

* **Rács megjelenítése:** Az eredmények táblázatos formátumban, lehetővé téve a megtekintheti az egyes konkrét értékét érhetők el. Ez a nézet is megfelel az akadálymentességi sztenderdeknek.
* **Diagram megjelenítése:** Az eredmények megjelennek egy sor formátuma segít azonosítani felfelé vagy lefelé trendeket és rendellenességeket.

 ![A rácsnézet elemzési megjelenítése](media/howto-create-analytics-experimental/analytics-showgrid.png)

Nagyítás teszi lehetővé az adatokon a kezdőlapjára. Ha azt tapasztalja, hogy egy adott időszakban, összpontosítson az eredményhalmaz belül szeretné, használja a kurzort megnyitása a terület, amelyeket szeretne nagyítása, és a rendelkezésre álló vezérlők segítségével hajtsa végre az alábbi műveletek egyikét:

* **Nagyítás:** Miután kiválasztotta, hogy egy adott időszakban, a Nagyítás engedélyezve van, és lehetővé teszi, hogy az adatokhoz a nagyításhoz.
* **Kicsinyítés:** Ez a vezérlő lehetővé teszi, hogy az utolsó nagyítás kicsinyítés egy szinttel. Például nagyítás, ha az az adatokhoz háromszor lép kicsinyítés biztonsági egy lépéssel egyszerre.
* **Nagyítás alaphelyzetbe állítása:** Nagyítás különböző szintjeit végzett el, miután a Nagyítás visszaállítási vezérlő használatával térjen vissza az eredeti eredményhalmaz.

 ![Hajtsa végre az adatokon a Nagyítás](media/howto-create-analytics-experimental/analytics-zoom.png)

A vonal stílusának igény szerint módosíthatja. Négy lehetőségek állnak rendelkezésére:

* **Vonal:** Az adatpontok közötti egybesimított sor.
* **Zökkenőmentes:** Görbe minden pont között.
* **. Lépés:** A diagramon minden pont közötti sor esetében egy lépésben.
* **Pontdiagram:** Minden pont a diagram ábrázolási őket sorok nélkül.

 ![Különböző típusa érhető el az Analytics](media/howto-create-analytics-experimental/analytics-linetypes.png)

Végül, mi számít az adatok között az y tengely kiválasztásával három mód közül:

* **Halmozott:** Minden mérték grafikon halmozott, és a gráfok mindegyike saját y tengely rendelkezik. A halmozott diagramok akkor hasznos, ha a kiválasztott több mérések rendelkezik, és szeretné, hogy ezeknek a méréseknek különböző nézetét.
* **Trendvonalat:** Minden mérték grafikon egyik y tengely van korrelálnak, de az y tengely értékei változnak a kijelölt mérték alapján. Trendvonalat diagramok akkor hasznos, ha átfedő több mértéket, és szeretné látni a minták között ezek az intézkedések ugyanazon időtartományra.
* **Megosztott y tengely:** Összes gráf megoszthatja az azonos y tengely, és ne módosítsa az értékeket a tengelyen. Megosztott y tengely diagramok akkor hasznos, ha egyetlen tekintse meg az adatokat a felosztás által szeletelés közben szeretne.

 ![Az adatok elrendezése y tengely között a különböző vizualizációs módok](media/howto-create-analytics-experimental/analytics-yaxis.png)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan hozhat létre egyéni analytics az Azure IoT Central alkalmazáshoz, a javasolt következő lépésre a következő:

> [!div class="nextstepaction"]
> [Készítse elő és a egy Node.js-alkalmazás csatlakoztatása](howto-connect-nodejs-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)