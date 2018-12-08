---
title: Állítsa be az Azure Time Series Insights – előzetes környezet oktatóanyag |} A Microsoft Docs
description: Ismerje meg, hogyan állítsa be a környezetet az Azure Time Series Insights előzetes verziója.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/26/2018
ms.openlocfilehash: 20cec1305f84bd1ff7e01f2e1d38f374aa17bc6f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106676"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Oktatóanyag: Azure Time Series Insights – előzetes környezet beállításával.

Ez az oktatóanyag végigvezeti egy Azure Time Series Insights – előzetes környezet, amely fel van töltve a szimulált eszközökről származó adatokkal történő létrehozásának folyamatán. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* Egy Time Series Insights előzetes verziója környezetet hozhat létre.
* A Time Series Insights előzetes környezet csatlakozhat az Azure Event Hubs-eseményközpontba.
* A szél farm szimuláció futtatása az adatok streamelése a Time Series Insights előzetes verziója környezetbe.
* Alapvető adatok elemzését, a.
* Egy Idősorozat-modell típusa és a hierarchia megadása, és társíthatja azt az üzemelő példányok.

## <a name="create-a-time-series-insights-preview-environment"></a>Egy Time Series Insights előzetes környezet létrehozása

Ez a szakasz ismerteti, hogyan hozhat létre a Time Series Insights előzetes környezet használatával a [az Azure portal](https://portal.azure.com/).

1. Jelentkezzen be az Azure Portalra az előfizetéses fiókba.

1. Válassza ki **erőforrás létrehozása**.

1. Válassza ki a **IOT-** kategóriát, és válassza ki **Time Series Insights**.

  ![Hozzon létre egy erőforrást, majd válassza ki az IOT-, majd válassza ki és a Time Series Insights][1]

1. Az a **alapjai** lapot, adja meg a szükséges paramétereket, majd **tovább: forrás**

  ![A Time Series Insights-környezet alapvető beállítások lapon, a következő: eseményforrás gomb][2]

1. Az a **eseményforrás** lapot, adja meg a szükséges paramétereket, majd **felülvizsgálat + létrehozás**.

  ![A forrás és a felülvizsgálat + Létrehozás gomb látható][3]

1. Az a **összefoglalás** lapon tekintse át a részletes adatait, és válassza ki **létrehozás** a környezet kiépítésének megkezdéséhez.

  ![Az összefoglaló lapon és a Létrehozás gombra][4]

1. Ha üzembe helyezés sikeres, megjelenik egy értesítés.

  ![Üzembe helyezés sikeres értesítés][5]

## <a name="send-events-to-your-time-series-insights-environment"></a>Események küldése Time Series Insights-környezete

Ebben a szakaszban egy Szélmalom készülékszimulátort küldhet eseményeket egy eseményközpontba keresztül a Time Series Insights-környezet használata.

  1. Az Azure Portalon nyissa meg az event hub-erőforrás, és csatlakoztathatja azt a Time Series Insights-környezet. További információ [az erőforrás csatlakozik egy meglévő eseményközponton](./time-series-insights-how-to-add-an-event-source-eventhub.md).

  1. Az event hub erőforrás-oldalon, Ugrás **megosztott hozzáférési házirendek** > **RootManageSharedAccessKey**. Másolja az értéket a **kapcsolati karakterlánc – elsődleges kulcs**.

      ![Másolja az elsődleges kulcs kapcsolati karakterlánc értéke][6]

  1. Nyissa meg a következőt: [https://tsiclientsample.azurewebsites.net/windFarmGen.html]( https://tsiclientsample.azurewebsites.net/windFarmGen.html). Ez a webalkalmazás URL-címen Szélmalom eszközök szimulálja.

  1. Az a **az Eseményközpont kapcsolati Sztringje** a weblapon mezőbe illessze be az előző lépésben kimásolt kapcsolati karakterláncot.

      ![Az Eseményközpont kapcsolati Sztringje mezőbe illessze be az elsődleges kulcs kapcsolati karakterlánca][7]

  1. Válassza ki **elindításához kattintson** leküldéses eseményeket az eseményközpontjába. Nevű fájl *instances.json* van letölt a számítógépre. Mentse a fájlt későbbi használat céljából.

  1. Lépjen vissza az event hubs az Azure Portalon. Az eseményközpontban **áttekintése** új események az event hub által fogadott lapon látható.

     ![Event hub áttekintő oldala, amely bemutatja az event hubs-mérőszámai][8]

## <a name="analyze-data-in-your-environment"></a>A környezetben lévő adatok elemzése

Ebben a szakaszban alapszintű analitika az idősoros adatokat, az a Time Series Insights explorer frissítése idő hajt végre.

  1. Nyissa meg az URL-cím (erőforrás) lapján az Azure Portalon kattintson a Time Series Insights frissítés explorer.

      ![A Time Series Insights explorer URL-címe][9]

  1. A explorer alatt **fizikai hierarchia**, jelölje be a **fölérendelt objektum nélküli példányok** csomópontok a környezetben az összes idő sorozatú példányok megtekintéséhez.

     ![A fizikai hierarchia panelen fölérendelt objektum nélküli példányok listája][10]

  1. Ebben az oktatóanyagban az elmúlt nap során elküldött adatok tudjuk elemezni. Válassza ki **Gyorshivatkozások**, majd válassza ki **az elmúlt 24 órából**.

     ![A Gyorshivatkozások legördülő mezőben válassza ki az elmúlt 24 órából][11]

  1. Válassza ki **Sensor_0**, majd válassza ki **átlagos érték megjelenítése** a Time Series Insights-példányból küldött adatok megjelenítéséhez.

     ![Válassza ki a Sensor_0 átlagos érték megjelenítése][12]

  1. Ehhez hasonlóan más Time Series Insights-példányok alapszintű analitika végrehajtásához származó is dolgozunk.

     ![Egy Time Series Insights-adatok diagram][13]

## <a name="define-a-type-and-hierarchy"></a>Adja meg a típust és a hierarchia 

Ebben a szakaszban a típust és a hierarchia létrehozásához és a majd társítsa a Time Series Insights-példányok típusát és hierarchia. További információ [Time Series modellek](./time-series-insights-update-tsm.md).

  1. A Explorerben válassza ki a **modell** fülre.

     ![A modell fülre a explorer menü][14]

  1. Az a **típusok** szakaszban jelölje be **Hozzáadás** hozhat létre egy új Idősorozat-modell típusa.

     ![A Hozzáadás gombra a típusok oldalon][15]

  1. Írja be a szerkesztő, adjon meg értéket a **neve** és **leírás**. Változók létrehozása **átlagos**, **Min**, és **maximális** értékek a következő ábrán látható módon. Válassza ki **létrehozás** menteni a típusát.

     ![A típus hozzáadása panelen, és a Létrehozás gombra][16]

     ![A minta Szélmalom típusok][17]

  1. Az a **hierarchiák** szakaszban jelölje be **Hozzáadás** egy Idősorozat-modell új hierarchia.

     ![A Hozzáadás gombra a hierarchiák oldalon][18]

  1. A hierarchia-szerkesztőben adjon meg egy értéket **neve** , és adja hozzá a hierarchiaszintek. Válassza ki **létrehozás** menteni a hierarchiában.

     ![A hierarchia hozzáadása panelen, és a Létrehozás gombra][19]

     ![A fizikai hierarchia mezőbe][20]

  1. Az a **példányok** szakaszt, válasszon ki egy példányt, és válassza ki **szerkesztése** típusa és a hierarchia társítandó ezt a példányt.

     ![Példányok listája][21]

  1. A példány-szerkesztőben válassza ki a típusát és a hierarchiában, amelyet a 3. és 5 megadott.

     ![A Szerkesztés-példány panel][22]

  1. Másik lehetőségként válassza ki egyszerre a típusa és a hierarchia minden példány esetében, szerkesztheti a *instances.json* korábban letöltött fájlra. Ebben a fájlban cserélje le az összes **typeId** és **hierarchyId** Azonosítóval rendelkező mezők kapott a 3. és 5.

  1. Az a **példányok** szakaszban jelölje be **feltöltése JSON** , és töltse fel a szerkesztett *instances.json* fájlt.

     ![A JSON feltöltés gombot][23]

  1. Válassza ki a **Analytics** lapra, és frissítse a böngészőt. A típus és a hierarchia meghatározott társított összes példányát kell megjelennie.

     ![Egy Time Series Insights-adatok diagram][24]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:  

* Egy Time Series Insights előzetes verziója környezetet hozhat létre.
* A Time Series Insights előzetes környezet csatlakozhat egy eseményközpontba.
* A szél farm szimuláció futtatása az adatok streamelése a Time Series Insights előzetes környezet.
* Az adatok egy alapszintű elemzéseket végezhet.
* Egy Idősorozat-modell típusa és a hierarchia megadása, és rendelje azokat a példányokat.

Most, hogy tudja, hogyan hozhat létre a saját Time Series Insights frissítési környezet, tudjon meg többet a Time Series Insightsban kapcsolatos főbb fogalmakat.

Olvassa el a Time Series Insights tárolási konfigurációt:

> [!div class="nextstepaction"]
> [Az Azure Time Series Insights előzetes verziója storage és a bejövő forgalom](./time-series-insights-update-storage-ingress.md)

További tudnivalók a Time Series modellek:

> [!div class="nextstepaction"]
> [Az Azure Time Series Insights előzetes verziója az adatmodellezés](./time-series-insights-update-tsm.md)

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