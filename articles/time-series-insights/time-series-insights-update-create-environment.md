---
title: Az Azure Time Series Insights (előzetes verzió) oktatóanyaga |} A Microsoft Docs
description: További tudnivalók az Azure Time Series Insights (előzetes verzió)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/26/2018
ms.openlocfilehash: ed25d03f7c592476b9284790ac12f9954661a42b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872305"
---
# <a name="azure-time-series-insights-preview-tutorial"></a>Az oktatóanyag az Azure Time Series Insights (előzetes verzió)

Ez az oktatóanyag végigvezeti egy Azure Time Series Insights (TSI) előzetes verziójú környezet, a szimulált eszközökről származó adatok használatával történő létrehozásának folyamatán. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* A TSI (előzetes verzió) környezetet hozhat létre.
* A TSI (előzetes verzió) környezet csatlakozhat egy Eseményközpontba.
* A szél farm szimuláció futtatása az adatok streamelése a TSI előzetes környezetbe.
* Alapvető adatok elemzését, a.
* Egy Idősorozat-modell típusa és a hierarchia megadása, és társíthatja azt az üzemelő példányok.

## <a name="create-a-time-series-insights-preview-environment"></a>A Time Series Insights (előzetes verzió) környezet létrehozása

Ez a szakasz ismerteti, hogyan hozhat létre az Azure TSI (előzetes verzió) környezet használatával a [az Azure Portal](https://portal.azure.com/).

1. Jelentkezzen be az előfizetés fiók Azure-portálra
1. Válassza az **+ Erőforrás létrehozása** lehetőséget a bal felső sarokban.
1. Válassza az **Eszközök internetes hálózata** kategóriát, majd a **Time Series Insights** elemet.

  ![az oktatóanyag egy az egyhez][1]

1. A Time Series Insights környezetet lapon adja meg a szükséges paramétereket, és kattintson a **tovább: forrás**

  ![az oktatóanyag második][2]

1. Az a **eseményforrás** lapon töltse ki a szükséges paramétereket, kattintson a **felülvizsgálat + létrehozás**.

  ![oktatóanyag – három][3]

1. Tekintse át a részletes adatait, és kattintson a **létrehozás** a környezet kiépítésének megkezdéséhez.

  ![oktatóanyag – 4][4]

1. A telepítés sikeres befejezése után kapni fog egy értesítést.

  ![az oktatóanyag-öt][5]

## <a name="send-events-to-your-tsi-environment"></a>Események küldése a TSI-környezetbe

Ebben a szakaszban egy Szélmalom készülékszimulátort használandó küldhet eseményeket egy Eseményközpontba keresztül a TSI környezet.

  1. Az Azure Portalon keresse meg az event hub-erőforrásra, és csatlakoztathatja azt a TSI-környezetben. Ismerje meg, [az erőforrás összekapcsolása egy meglévő eseményközpontban](./time-series-insights-how-to-add-an-event-source-eventhub.md).

  1. Az Event Hub-erőforrások oldalának, lépjen a **megosztott hozzáférési házirendek** , majd **RootManageSharedAccessKey**. Másolás a **kapcsolati karakterlánc – elsődleges kulcs** itt jelenik meg.

      ![az oktatóanyag-6][6]

  1. Nyissa meg a következőt: [https://tsiclientsample.azurewebsites.net/windFarmGen.html]( https://tsiclientsample.azurewebsites.net/windFarmGen.html). Ez a webalkalmazás Szélmalom eszközök szimulálja.
  1. Illessze be a 3. lépésében másolja a kapcsolati karakterláncot a **az Eseményközpont kapcsolati Sztringje**

      ![oktatóanyag – hét][7]

  1. Kattintson a **kattintson a Start** eseményeket küld az eseményközpontba. Ebben a szakaszban egy fájlt `instances.json` letölti a gépre. Mentse a fájlt, hogy szüksége lesz később.

  1. Lépjen vissza az eseményközpontba. Meg kell jelennie a hub.d fogadja az új események

     ![oktatóanyag – 8][8]

## <a name="analyze-data-in-your-environment"></a>A környezetben lévő adatok elemzése

Ebben a szakaszban végre fogja hajtani alapszintű analitika az idősorozat-adatok használata a Time Series Insights explorer frissítése időben.

  1. Keresse meg az URL-cím az Azure Portal, az erőforrás oldalról kattintva a Time Series Insights frissítés explorer.

      ![oktatóanyag – 9][9]

  1. A Explorerben kattintson a a **fölérendelt objektum nélküli példányok** csomópontok a környezet a Time Series példányok megtekintéséhez.

     ![oktatóanyag – tíz][10]

  1. Ebben az oktatóanyagban fogunk az elmúlt napon belül küldött adatok elemzéséhez. Ehhez kattintson a **Gyorshivatkozások** , és válassza ki a **az elmúlt 24 órából** lehetőséget.

     ![oktatóanyag – tizenegy][11]

  1. Válassza ki **Sensor_0** válassza **átlagos érték megjelenítése** küldését a time series példány adatainak megjelenítéséhez.

     ![oktatóanyag – 12][12]

  1. Hasonlóképpen dolgozunk más idő sorozat példányai alapszintű analitika végrehajtásához származó adatokat is.

     ![oktatóanyag – tizenhárom][13]

## <a name="define-a-type--hierarchy"></a>A típus & hierarchia megadása 

Ebben a szakaszban egy típust, a hierarchiában, szerzői, és rendelje azokat a time series példányok. Tudjon meg többet [Time Series modellek](./time-series-insights-update-tsm.md).

  1. A Explorerben kattintson a a **modell** alkalmazássávon lapján.

     ![oktatóanyag – tizennégy][14]

  1. A típusok területen kattintson a **+ Hozzáadás**. Ez lehetővé teszi, új Time Series modell típus létrehozásához.

     ![az oktatóanyag-tizenöt][15]

  1. Adja meg a típus-szerkesztőben egy **neve**, **leírása**, és a változók létrehozása **átlagos**, **Min**, és **maximális** értékek az alább látható módon. Kattintson a **létrehozás** menteni a típusát.

     ![oktatóanyag – tizenhat][16]

     ![oktatóanyag – seventeen][17]

  1. Az a **hierarchiák** területén kattintson a **+ Hozzáadás**. Ez lehetővé teszi, hozzon létre egy új Time Series modell hierarchia.

     ![oktatóanyag – tizennyolc][18]

  1. Adja meg a hierarchia-szerkesztőben egy **neve** , és adja hozzá a hierarchiaszintek alább látható módon. Kattintson a **létrehozás** menteni a hierarchiában.

     ![oktatóanyag – tizenkilenc][19]

     ![oktatóanyag – erdőtopológia][20]

  1. Az a **példányok** szakaszt, válasszon ki egy példányt, és kattintson a **szerkesztése**. Ez lehetővé teszi, típusa és a hierarchia társítani ezt a példányt.

     ![az oktatóanyag-erdőtopológia-egy][21]

  1. A példány-szerkesztőben válassza ki a típusa és a hierarchia meghatározott lépések 3, 5 fent látható módon.

     ![a második erdőtopológia oktatóanyag][22]

  1. Azt is megteheti, ehhez az összes példány egyidejű, szerkesztheti a `instances.json` korábban letöltött fájlra. Ebben a fájlban cserélje le az összes **typeId** és **hierarchyId** Azonosítóval rendelkező mezők szerzett a fenti 5, 3,. lépést.

  1. Az a **példányok** területén kattintson a **feltöltése JSON** , és töltse fel a szerkesztett `instances.json` fájlt a lent látható módon.

     ![oktatóanyag – 20-három][23]

  1. Keresse meg a **Analytics** lapra, és frissítse a böngészőt. Meg kell jelennie az típusa és a fent meghatározott hierarchiához társított összes példányát.

     ![oktatóanyag – huszonnégy][24]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:  

* A TSI (előzetes verzió) környezetet hozhat létre.
* A TSI (előzetes verzió) környezet csatlakozhat egy Eseményközpontba.
* A szél farm szimuláció futtatása az adatok streamelése a TSI (előzetes verzió) környezetbe.
* Alapvető adatok elemzését, a.
* Meghatározása egy Idősorozat-modell típusa, a hierarchiában, és társíthatja azt az üzemelő példányok.

Most, hogy tudja, hogyan hozhat létre saját TSI frissítési környezet, tudjon meg többet a TSI-ben kapcsolatos főbb fogalmakat.

Olvassa el a TSI tárolási konfigurációt:

> [!div class="nextstepaction"]
> [Az Azure TSI (előzetes verzió) storage és a bejövő forgalom](./time-series-insights-update-storage-ingress.md)

További tudnivalók a Time Series modellek:

> [!div class="nextstepaction"]
> [Az Azure TSI (előzetes verzió) az adatmodellezés](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/tutorial-one.png
[2]: media/v2-update-provision/tutorial-two.png
[3]: media/v2-update-provision/tutorial-three.png
[4]: media/v2-update-provision/tutorial-four.png
[5]: media/v2-update-provision/tutorial-five.png
[6]: media/v2-update-provision/tutorial-six.png
[7]: media/v2-update-provision/tutorial-seven.png
[8]: media/v2-update-provision/tutorial-eight.png
[9]: media/v2-update-provision/tutorial-nine.png
[10]: media/v2-update-provision/tutorial-ten.png
[11]: media/v2-update-provision/tutorial-eleven.png
[12]: media/v2-update-provision/tutorial-twelve.png
[13]: media/v2-update-provision/tutorial-thirteen.png
[14]: media/v2-update-provision/tutorial-fourteen.png
[15]: media/v2-update-provision/tutorial-fifteen.png
[16]: media/v2-update-provision/tutorial-sixteen.png
[17]: media/v2-update-provision/tutorial-seventeen.png
[18]: media/v2-update-provision/tutorial-eighteen.png
[19]: media/v2-update-provision/tutorial-nineteen.png
[20]: media/v2-update-provision/tutorial-twenty.png
[21]: media/v2-update-provision/tutorial-twenty-one.png
[22]: media/v2-update-provision/tutorial-twenty-two.png
[23]: media/v2-update-provision/tutorial-twenty-three.png
[24]: media/v2-update-provision/tutorial-twenty-four.png