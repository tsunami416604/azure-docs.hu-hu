---
title: Hozzon létre egyéni analytics az Azure IoT Central alkalmazáshoz |} A Microsoft Docs
description: Kezelőként hogyan hozhat létre egyéni analytics az Azure IoT Central alkalmazáshoz.
author: lmasieri
ms.author: lmasieri
ms.date: 08/26/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0a78c534605b6eab08d5b12674689f0459e80b26
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247096"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Az adatok elemzéséhez analytics használata

A Microsoft Azure IoT Central jelentéssel bírnak az eszközök adatainak nagy mennyiségű megoldásban gazdag analitikai funkciókat biztosít. Első lépésként látogasson el a **Analytics** a bal oldali navigációs menüben. 

  ![IoT-központ navigációs analyticshez](media\howto-create-analytics\analytics-navigation.png)

## <a name="querying-your-data"></a>Az adatok lekérdezése

Választania kell egy **eszköz beállítása**, adja hozzá egy **szűrő** (nem kötelező), és válassza ki egy **időszakban** a kezdéshez. Miután elkészült, kattintson a *találat megjelenítése a* elindítani az adatai megjelenítéséhez.


* **Eszköz beállítása:** A [eszköz set](howto-use-device-sets.md) az eszközök felhasználó által definiált csoportjai. Ha például Oakland az összes hűtőszekrények, vagy az összes rev 2.0-s szél csatlakozó.

<!---
to-do: confirm if 10 is the max number of filters
to-do: do we need to explain how fiters work?
--->

* **Szűrők:** igény szerint hozzáadhat szűrők a keresést, hogy az adatok alaposabb vizsgálatát. Egyszerre legfeljebb 10 szűrőket is hozzáadhat. Például Oakland az összes hűtőszekrények belül található, amelyek hőmérséklet kellett volna nyissa meg a fenti 60 fok. 
* **Adott időszakban:** alapértelmezés szerint azt fogjuk-adatokat lekérni az elmúlt 10 percben. Egy előre meghatározott időpontra tartományok módosítani ezt az értéket, vagy válasszon egy egyéni időszakot. 

 ![Elemzési lekérdezés](media\howto-create-analytics\analytics-query.png)

## <a name="visualizing-your-data"></a>Az adatok megjelenítése

Az adatok már kérdezhető le, miután fogja jelenítenek meg, akkor indul el. Megjelenítése/elrejtése mértékek is, adatokat a rendszer összesített, és további módjának módosítása másik eszköztulajdonságok alapján felosztani az adatokat.  

* **Felosztási szempont:** felosztása a adatokat eszköztulajdonságok alapján lehetővé teszi, hogy további részletes le azokat az adatokat. Például akkor is feloszthatja az eszköz Azonosítóját vagy a hely által az eredményeket.
<!---
to-do: confirm if 10 is the max number of measurements
--->
* **Mértékek:** lehet váltani, az eszközök által jelentett egyszerre legfeljebb 10 különféle telemetriai elemek megjelenítése. Mértékek például hőmérséklettel és páratartalommal kapcsolatos dolgot. 
* **Összesítés:** alapértelmezés szerint azt összesített adatok alapján az átlagos, de az adatok összesítése módosítsa valami mást saját igényei szerint választhatja. 

   ![Analytics-Vizualizáció](media\howto-create-analytics\analytics-visualize.png) <br/><br/>
   ![Felosztási szempont Analytics vizualizációs](media\howto-create-analytics\analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interakció adataival

Hogy különféle módokon, amelyben további módosíthatja a lekérdezés eredményeit, a Vizualizáció igényeinek. A diagram nézet és a egy rácsnézet között, bejövő és kimenő nagyítás, frissítse az adatkészletet, és módosítsa a hogyan sorok jelennek meg.

* **Rács megjelenítése:** az eredmények táblázatos formában való mutatják az adott érték az egyes elérhető lesz. Ez a nézet is megfelel az akadálymentességi sztenderdeknek. 
* **Diagram megjelenítése:** egyszerűen a kritikus pontok elkerülése érdekében felfelé vagy lefelé trendek és rendellenességek sor formátumban jelennek meg az eredményeket. 

 ![A rácsnézet elemzési megjelenítése](media\howto-create-analytics\analytics-showgrid.png)

Nagyítás lehetővé teszi az adatok alaposabb vizsgálatát. Ha úgy találja, hogy egy adott időszakban, szeretné belül az eredményhalmazban összpontosítanak használatával a kurzor fogd a terület, amelyeket szeretne nagyítása, és a rendelkezésre álló vezérlők segítségével hajtsa végre az alábbi műveletek egyikét:
* **Nagyítás:** nagyítás Miután kiválasztotta, hogy egy adott időszakban, a engedélyezve lesz, és lehetővé teszi az adatokhoz a nagyításhoz.
* **Kicsinyítés:** Ez a vezérlő lehetővé teszi, hogy az utolsó nagyítás kicsinyítés egy szinttel. Például, ha már ráközelít az adatokhoz három alkalommal, kicsinyítés vesz igénybe egy lépésben egy biztonsági másolatot.
* **Nagyítás visszaállítási:** különböző szintjeit nagyításához végzett el, miután a Nagyítás visszaállítási vezérlő térjen vissza az eredeti eredményhalmaz használhatja. 

 ![Hajtsa végre az adatokon a Nagyítás](media\howto-create-analytics\analytics-zoom.png)


A vonal stílusának igény szerint módosíthatja. Rendelkezésére a négy beállítás közül választhat:
* **Vonal:** egymástól az adatpontok egy egybesimított sor lesz összeállítva. 
* **Zökkenőmentes:** lesz összeállítva a olyan görbe minden pont között
* **. Lépés:** a diagramon minden pont közötti hoz létre egy lépés diagram
* **Pontdiagram:** őket sorok nélkül összes pont lesz ábrázolható a diagramra. 

 ![Különböző típusa érhető el az Analytics](media\howto-create-analytics\analytics-linetypes.png)

Végül rendezheti az adatokat az y tengely között válassza ki a három mód közül:

* **Halmozott:** minden mérték grafikon halmozott, és a gráfok mindegyike rendelkezik, saját y tengely. A halmozott diagramok akkor hasznos, ha a kiválasztott több mérések rendelkezik, és szeretné, hogy ezeknek a méréseknek különböző nézetét.
* **Trendvonalat:** minden mérték van korrelálnak egy y tengely, de az y tengely értékei változnak a a kijelölt mérték alapján grafikon. Trendvonalat diagramok akkor hasznos, ha átfedő több mértéket, és szeretné látni a minták között ezek az intézkedések ugyanazon időtartományra.
* **Megosztott y tengely:** összes gráf megosztani az azonos y tengely és a tengely értékeit ne módosítsa. Megosztott y tengely diagramok akkor hasznos, ha egyetlen tekintse meg az adatokat a felosztás által szeletelés közben szeretne.

 ![Az adatok elrendezése y tengely között a különböző vizualizációs módok](media\howto-create-analytics\analytics-yaxis.png)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan hozhat létre egyéni analytics az Azure IoT Central alkalmazáshoz, a javasolt következő lépésre a következő:

> [!div class="nextstepaction"]
> [Készítse elő és a egy Node.js-alkalmazás csatlakoztatása](howto-connect-nodejs.md)