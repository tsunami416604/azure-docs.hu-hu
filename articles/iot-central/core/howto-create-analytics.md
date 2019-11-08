---
title: Az eszköz adatai elemzése az Azure IoT Central alkalmazásban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan elemezheti az eszköz adatait az Azure IoT Central-alkalmazásban lekérdezések és vizualizációk használatával.
author: lmasieri
ms.author: lmasieri
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a467e0e6e8967cf963ad099f83de6718330aa43f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827981"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Az eszköz adatai elemzése az elemzések használatával

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Az Azure IoT Central gazdag elemzési funkciókat kínál az eszközök nagy mennyiségű adatainak megismeréséhez. Első lépésként látogasson el az **elemzésre** a bal oldali ablaktáblán.

## <a name="querying-your-data"></a>Az adatai lekérdezése

Ki kell választania egy **eszközt**, hozzá kell adnia egy **szűrőt** (nem kötelező), majd ki kell választania egy **időszakot** a kezdéshez. Ha elkészült, válassza az **eredmények megjelenítése** lehetőséget az adatai megjelenítésének megkezdéséhez.

* **Eszközök készletei:** Az [eszközbeállítások](howto-use-device-sets.md) az eszközök felhasználó által meghatározott csoportja. Például az összes hűtőszekrény a Oaklandben, vagy az összes rev 2,0 szélturbinák.

* **Szűrők:** A kereséshez szűrőket is hozzáadhat az adataihoz. Egyszerre legfeljebb 10 szűrőt adhat hozzá. Például a Oaklandban található összes hűtőszekrényben megtalálhatja, hogy a hőmérséklet a 60 fok fölé esik.
* Időtartam **:** Alapértelmezés szerint az elmúlt 10 percből fogjuk beolvasni az adatait. Ezt az értéket módosíthatja az előre meghatározott időtartományok egyikére, vagy kijelölhet egy egyéni időszakot is.

  ![Elemzési lekérdezés](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Az adataik megjelenítése

Miután lekérdezte az adatait, megkezdheti a megjelenítését. Megtekintheti/elrejtheti a méréseket, módosíthatja az adatösszesítési módot, és az adatokat az eszközök különböző tulajdonságai alapján további módon feloszthatja.  

* **Felosztás:** Az adatfelosztás az eszköz tulajdonságai alapján további részletezést tesz lehetővé az adataiban. Például megoszthatja az eredményeket az eszköz azonosítója vagy helye alapján.

* **Mérések:** Kiválaszthatja, hogy egyszerre legfeljebb 10 különböző telemetria jelenjen meg, amelyeket az eszközei jelentettek. A mérések olyan dolgok, mint például a hőmérséklet és a páratartalom.

* **Összesítés:** Alapértelmezés szerint az adatokat az átlag alapján összesítjük, de úgy is dönthet, hogy az adatösszesítést úgy változtatja meg, hogy az megfeleljen az igényeinek.

   ![Elemzési vizualizáció felosztási alapja](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interakció az adataival

A lekérdezés eredményeinek többféleképpen is módosíthatók, hogy megfeleljenek a vizualizáció igényeinek. A diagram nézet és a rács nézet, a nagyítás és a kicsinyítés, az adathalmaz frissítése és a sorok megjelenítésének módosítása is megadható.

* **Rács megjelenítése:** Az eredmények táblázatos formában érhetők el, így megtekintheti az egyes adatpontok adott értékét. Ez a nézet a kisegítő lehetőségeket is kielégíti.
* **Diagram megjelenítése:** Az eredmények sor formátumban jelennek meg, így könnyebben azonosíthatók a felfelé vagy lefelé irányuló trendek és rendellenességek.

  ![Az elemzések rács nézetének megjelenítése](media/howto-create-analytics/analytics-showgrid.png)

A nagyítás lehetővé teszi az Ön adatait. Ha olyan időszakot talál, amelyet szeretne összpontosítani az eredményhalmazban, használja a kurzort a nagyítani kívánt terület nagyításához, és a rendelkezésre álló vezérlők használatával hajtsa végre az alábbi műveletek egyikét:

* **Nagyítás:** Miután kiválasztotta az időszakot, a nagyítás engedélyezve van, és lehetővé teszi az adatnagyítást.
* **Kicsinyítés:** Ez a vezérlő lehetővé teszi, hogy az utolsó nagyításból egy szintet nagyítson. Ha például háromszor nagyítja fel az adatait, a kicsinyítéskor egy lépéssel egyszerre egy lépést is végrehajthat.
* **Nagyítás alaphelyzetbe állítása:** Miután elvégezte a különböző nagyítási szinteket, a nagyítás-vezérlés lehetőséggel visszatérhet az eredeti eredményhalmaz-készlethez.

  ![Az adatok nagyításának elvégzése](media/howto-create-analytics/analytics-zoom.png)

Az igényeinek megfelelően módosíthatja a vonal stílusát. Négy lehetőség közül választhat:

* **Vonal:** Az egyes adatpontok közötti egyenes vonal.
* **Zökkenőmentes:** Az egyes pontok közötti görbe vonal.
* **Lépés:** A diagram egyes pontjai közötti vonal egy lépés.
* **Scatter:** Az összes pont ábrázolása a diagramon sorok nélkül történik.

  ![Az elemzésekben elérhető különböző típusú sorok](media/howto-create-analytics/analytics-linetypes.png)

Végül a következő három mód egyikének kiválasztásával rendezheti az adatait az Y tengelyen:

* **Halmozott:** Minden méréshez egy gráf van halmozva, és mindegyik gráf saját Y tengellyel rendelkezik. A halmozott gráfok akkor hasznosak, ha több mérés van kiválasztva, és a mérések eltérő nézetét szeretnék használni.
* Nem **halmozott:** Minden mérték gráfja egy Y tengelyre van rajzolva, az Y tengely értékei azonban a Kiemelt mérték alapján változnak. A nem halmozott gráfok akkor hasznosak, ha több mértéket szeretne átfedésben, és az adott időszakra vonatkozó mintákat szeretné látni.
* **Megosztott Y tengely:** Az összes gráf ugyanazt az Y tengelyt használja, és a tengely értékei nem változnak. A megosztott Y tengelyes diagramok akkor hasznosak, ha egyetlen mértéket szeretne megtekinteni az adatdarabolás során.

  ![Adatelrendezés az y tengelyen különböző vizualizációs módokkal](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre egyéni elemzéseket az Azure IoT Central alkalmazáshoz, itt a javasolt következő lépés:

> [!div class="nextstepaction"]
> [Node. js-alkalmazás előkészítése és összekapcsolása](howto-connect-nodejs.md)