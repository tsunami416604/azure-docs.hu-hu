---
title: 'Oktatóanyag: vízfogyasztást figyelő alkalmazás létrehozása az Azure IoT Central'
description: 'Oktatóanyag: megtudhatja, hogyan hozhat létre vízfelhasználást figyelő alkalmazást az Azure IoT Central alkalmazás-sablonok használatával.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d31ef856103d809fc02a183603bc45f9d94939c8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990554"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-in-iot-central"></a>Oktatóanyag: vízfelhasználást figyelő alkalmazás létrehozása IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ebből az oktatóanyagból megtekintheti, hogyan hozhat létre Azure IoT Central vízfelhasználás-figyelési alkalmazást a IoT Central Water-felhasználás figyelése alkalmazás sablonjában. 

Az oktatóanyag a következőket ismerteti: 

> [!div class="checklist"]
> * Az Azure IoT Central Water-használat **figyelési** sablonjának használatával hozza létre a víz-felhasználási figyelő alkalmazást
> * Az operátor irányítópultjának megismerése és testreszabása 
> * Az eszköz sablonjának megismerése
> * Szimulált eszközök megismerése
> * Szabályok megismerése és konfigurálása
> * Feladatok konfigurálása
> * Az alkalmazás arculatának testreszabása a címek megjelölése megbízhatóként használatával

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:
-  Az Azure-előfizetés használata ajánlott. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription).

## <a name="create-water-consumption-monitoring-app-in-iot-central"></a>Víz-használat figyelési alkalmazásának létrehozása IoT Central

Ebben a szakaszban az Azure IoT Central Water-használat **figyelési sablonját** fogjuk használni a vízfelhasználást figyelő alkalmazás létrehozásához IoT Centralban.

Új Azure IoT Central Water-használat figyelési alkalmazásának létrehozása:  

1. Navigáljon az [Azure IoT Central Kezdőlap](https://aka.ms/iotcentral) webhelyére.

    Ha rendelkezik Azure-előfizetéssel, jelentkezzen be az eléréséhez használt hitelesítő adatokkal, máskülönben jelentkezzen be Microsoft-fiók használatával:

    ![Lépjen a céges fiókjába](media/tutorial-waterconsumptionmonitoring/sign-in.png)

2. Kattintson a **Létrehozás** elemre a bal oldali ablaktáblán, és válassza a **kormányzat** fület. A kormányzat oldalon számos kormányzati alkalmazás-sablon látható.

   ![Kormányzati alkalmazás-sablonok készítése](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Válassza ki a **víz-felhasználás figyelése** alkalmazás sablonját. Ez a sablon magában foglalja a minta vízfogyasztási eszköz sablonját, a szimulált eszközt, az operátor irányítópultját és az előre konfigurált figyelési szabályokat.    

2. Kattintson az alkalmazás **létrehozása**elemre, amely megnyitja az **új alkalmazás** -létrehozási űrlapot a következő mezőkkel:
    * **Alkalmazásnév**: alapértelmezés szerint az alkalmazás a *vízfelhasználás figyelését* , majd egy egyedi azonosító karakterláncot használ, amelyet IoT Central generál. Igény szerint válasszon egy felhasználóbarát nevet. Az alkalmazás neve később is módosítható.
    * **URL**: a IoT Central automatikusan létrehoz egy URL-címet az alkalmazás neve alapján. Megadhatja, hogy az URL-címet saját ízlése szerint frissítse. Az URL-címet később is megváltoztathatja. 
    * Ha Azure-előfizetéssel rendelkezik, adja meg a *címtárat, az Azure-előfizetést és a régiót*. Ha nem rendelkezik előfizetéssel, engedélyezheti a **7 napos ingyenes próbaidőszakot** , és elvégezheti a szükséges kapcsolattartási adatokat.  

    A könyvtárakkal és előfizetésekkel kapcsolatban további információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](../preview/quick-deploy-iot-central.md).

5. Kattintson a lap alján található **Létrehozás** gombra. 

    ![Azure IoT Central Alkalmazás létrehozása oldal](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Azure IoT Central alkalmazás számlázási adatainak létrehozása](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)
6. Ezzel létrehozta a víz-fogyasztás figyelése alkalmazást az Azure IoT Central **Water-használat figyelési** sablonnal.

Gratulálunk! Elkészült a vízminőség-figyelési alkalmazás létrehozásával, amely előre konfigurálva van:
* Minta operátor irányítópultok
* Példa előre definiált víz flow-és Valve-eszközök sablonjaira
* Szimulált vízfolyás és intelligens szelepes eszközök
* Előre konfigurált szabályok és feladatok
* Minta-védjegyezés a fehér címkézés használatával 

Ez az alkalmazás, és bármikor módosítható. Most Ismerkedjen meg az alkalmazással, és végezze el a testreszabást.  

## <a name="explore-and-customize-operator-dashboard"></a>Az operátor irányítópultjának megismerése és testreszabása 
Miután létrehozta az alkalmazást, a minta operátor irányítópultján, a **Wide World Water-felhasználás monitorozási irányítópultján**kell lennie.

   ![Vízfogyasztás figyelése irányítópult](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Építőként létrehozhat és testre szabhatja a nézeteket az irányítópulton a kezelők számára. Mielőtt megpróbálja testreszabni a testreszabást, ismerkedjen meg az irányítópulttal. 

> [!NOTE]
> Az irányítópulton megjelenő összes érték szimulált eszközön alapul, amelyet a következő szakaszban fogunk feltárni. 
  
Az irányítópult különböző típusú csempéket tartalmaz:

* A **globális vízkészletek képcsempéje**: az irányítópult első csempe a "Wide World Water" kitalált víz-segédeszköz képcsempe. Testreszabhatja a csempét, és elhelyezheti saját képét, vagy eltávolíthatja azt. 

* **Átlagos vízfolyás KPI-csempe**: a KPI csempe úgy van konfigurálva, hogy az *átlagot az elmúlt 30 percben*jelenítse meg. Testreszabhatja a KPI-csempéket, és beállíthatja, hogy egy másik típus-és időtartományra legyen beállítva.

* Ezután az irányítópult- *eszköz parancsainak* jobb oldalán a **Valve**, a **Valve**vagy a **Valve pozíciója állítható**be. A parancsokra kattintva megnyithatja a szimulált eszköz eszközének parancssori lapját. IoT Central a *parancs* egy eszköz- *képesség* típusú, amelyet később a jelen oktatóanyag **eszköz sablon szakaszában** fogunk feltárni.

*  **Vízterjesztési terület térképe**: a Térkép Azure Maps használ, amelyet közvetlenül az Azure IoT Central konfigurálhat. A Térkép csempén az eszköz helye jelenik meg. Próbáljon rámutatni a térképre, és próbálja ki a vezérlőket a térképen, például a *nagyításhoz*, a *nagyításhoz* vagy a *kibővítéshez*. 

    ![Vízfogyasztás figyelése irányítópult-Térkép](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* A **víz átlagos folyamatábrájának** és **környezeti feltételeinek**diagramja: a kívánt időtartományon belül egy vagy több telemetriáiról ábrázolhat.  

* Az **átlagos Valve Pressure hő diagramja**: kiválaszthatja azokat az hő-vizualizációk típusát, amelyekre kíváncsi, hogy az adott időtartományon belül egy színindexet használ.

* **Riasztási küszöbérték alaphelyzetbe állítása csempe**: megadhatja, hogy a művelet tartalmát tartalmazó csempék a művelet lapra mutató hivatkozást beágyazva. Ebben az esetben az Alaphelyzetbe állítás riasztási küszöbértéke arra az alkalmazás- **feladatokra** mutat, ahol az eszközök tulajdonságaira vonatkozó frissítéseket futtathat, amelyeket később az oktatóanyag **feladatok konfigurálása** szakaszában fogunk feltárni.

* **Tulajdonságlapok**: az irányítópulton megjelennek a **Valve működési adatai**, a **flow riasztási küszöbértékei**és a **karbantartási adatok** , amelyek az eszköz tulajdonságai.  


### <a name="customize-dashboard"></a>Irányítópult testreszabása 

Építőként testreszabhatja a nézeteket az irányítópulton a kezelők számára. A következőket teheti meg:
1. Kattintson a **Szerkesztés** elemre a **globális vízfelhasználás monitorozási irányítópultjának**testreszabásához. A **Szerkesztés** menüre kattintva testreszabhatja az irányítópultot. Ha az irányítópult **szerkesztési** módban van, új csempéket adhat hozzá, vagy beállíthatja 

     ![Irányítópult szerkesztése](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

2. Kattintson az **+ új** elemre az új irányítópult létrehozásához és a konfiguráláshoz a semmiből. Több irányítópulttal is rendelkezhet, és az irányítópultok menüjéből is megnyithatja az irányítópultokat. 

## <a name="explore-device-template"></a>Az eszköz sablonjának megismerése
Az Azure IoT Central egy eszköz, amely a telemetria, a Property vagy a parancshoz használható eszköz képességeit határozza meg. Építőként megadhat egy vagy több olyan IoT Central, amelyek a csatlakoztatott eszközök képességeit jelölik. 
 

A **víz-felhasználás figyelésére szolgáló** alkalmazás két, a flow- *mérőt* és egy *intelligens szelepet* jelző eszközt jelképező, hivatkozással rendelkező eszközhöz tartozik. 

Az eszköz sablonjának megtekintése:

1. Az alkalmazás bal oldali navigációs paneljén kattintson az **eszközök sablonok** elemre IoT Central. 
    A **Device templates** (eszközök) listában két eszközosztály és egy **intelligens szelep** jelenik meg.

   ![Eszköz sablonja](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

2. Kattintson a **flow Meter** -eszköz sablonra, és ismerkedjen meg az eszköz képességeivel 

     ![Eszköz sablonjának folyamatábrája](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customizing-the-device-template"></a>Az eszköz sablonjának testreszabása

Próbálja testre szabni a következőket:
1. Navigáljon a **Testreszabás** elemre az eszköz sablon menüjében
2. A `Temperature` telemetria típusának megkeresése
3. `Temperature` **megjelenítendő nevének** frissítése `Reported temperature`
4. A mértékegység frissítése vagy a *minimális érték* és a *maximális érték* beállítása
5. Módosítások **mentése** 

### <a name="add-a-cloud-property"></a>Felhőbeli tulajdonság hozzáadása 
1. Navigáljon a **Cloud tulajdonságra** az eszköz sablonja menüben
2. Vegyen fel egy új Felhőbeli tulajdonságot a **+ Cloud Property**elemre kattintva. 
    IoT Centralban hozzáadhat egy, az eszközhöz kapcsolódó tulajdonságot is. A Felhőbeli tulajdonság például lehet a telepítési területre, az adategységre vagy a karbantartási információkra jellemző riasztási küszöbérték. 
3. Módosítások **mentése** 
 
### <a name="views"></a>Nézetek 
A vízfogyasztási figyelő eszköz sablonja előre definiált nézetekkel rendelkezik. Ismerkedjen meg a nézetekkel, és frissítheti a frissítéseket. A nézetek határozzák meg, hogy a operátorok hogyan látják az eszköz információit, de a felhő tulajdonságai is megmaradnak. 

  ![Eszköz sablon nézetei](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Közzététel 
Ha módosította a módosításokat, győződjön meg arról, hogy az eszköz sablonját **közzéteszi** . 

### <a name="create-a-new-device-template"></a>Új eszközsablon létrehozása 
- Válassza az **+ új** lehetőséget egy új sablon létrehozásához, és kövesse a létrehozási folyamatot. Létrehozhat egy teljesen új egyéni sablont, vagy kiválaszthat egy sablont az Azure-eszköz katalógusában. 

## <a name="explore-simulated-devices"></a>Szimulált eszközök megismerése
A IoT Central szimulált eszközöket hozhat létre az eszköz sablonjának és alkalmazásának teszteléséhez. A **vízfelhasználás-figyelési** alkalmazás két szimulált eszközhöz van rendelve a *áramlásmérő* és az *intelligens Valve* -eszközök sablonjaihoz. 

### <a name="to-view-the-devices"></a>Az eszközök megtekintése:
1. Navigáljon az **eszközre** IoT Central bal oldali navigációs ablaktáblán. 

   ![Eszközök](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

2. Kattintson egy **intelligens szelep 1** 

    ![1\. eszköz](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

3.  Az **eszköz parancsainál** megtekintheti a három *eszköz parancsait*, a szelepet, a *szelepet*, és *beállíthatja* az *intelligens Valve* -eszköz sablonjában definiált képességeket. 
4. Fedezze fel az **eszköz tulajdonságai** lapot és az **eszköz irányítópultját** . 

> [!NOTE]
> Vegye figyelembe, hogy az összes lapot konfigurálta az eszköz sablon nézeteiből.

### <a name="add-new-devices"></a>Új eszközök hozzáadása
* Az **eszközök** lapon kattintson az **+ új** elemre az új eszközök hozzáadásához. 

## <a name="explore-and-configure-rules"></a>Szabályok megismerése és konfigurálása

Az Azure IoT Central létrehozhat olyan szabályokat, amelyek automatikusan figyelik az eszköz telemetria, és elindítják a műveleteket, ha egy vagy több feltétel teljesül. A műveletek tartalmazhatják e-mail-értesítések küldését, vagy egy Microsoft Flow művelet vagy egy webhook művelet aktiválását más szolgáltatásoknak való adatküldéshez.

A létrehozott **víz-felhasználási figyelő** alkalmazás három előre konfigurált szabályt tartalmaz.

### <a name="to-view-rules"></a>Szabályok megtekintése:
1. IoT Central bal oldali navigációs ablaktáblában navigáljon a **szabályokhoz** . 

   ![Szabályok](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

2. Válassza ki, majd kattintson a **magas pH-riasztásra** , amely az alkalmazás előre konfigurált szabályainak egyike.

     ![Magas pH-riasztás](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    A `High flow alert` szabály úgy van konfigurálva, hogy a `Acidity (pH)` `greater than` a `Max flow threshold`feltételt vizsgálja. A maximális forgalom küszöbértéke az eszköz *intelligens Valve* -eszköz sablonjában definiált Felhőbeli tulajdonság. `Max flow threshold` értékének beállítása eszközönkénti példány. 

Most hozzon létre egy e-mail-műveletet.

Művelet hozzáadása a szabályhoz:

1. Válassza a **+ e-mail**lehetőséget. 
1. Adja meg a *magas pH-riasztást* a művelet felhasználóbarát **megjelenítendő neveként** .
    * Adja meg a **IoT Central-** fiókjához tartozó e-mail-címet a alkalmazásban. 
1. Szükség esetén megadhat egy, az e-mail-szövegbe felvenni kívánt megjegyzést is.
1. A művelet befejezéséhez kattintson a **kész** gombra.
1. A Save ( **Mentés** ) gombra kattintva mentse és aktiválja az új szabályt. 

Néhány percen belül e-mailt kell kapnia a beállított **feltétel** teljesülése esetén.

> [!NOTE]
> Az alkalmazás minden alkalommal elküld e-mailt, amikor egy feltétel teljesül. **Tiltsa le** a szabályt, hogy ne kapjon e-mailt az automatizált szabálytól. 
  
Új szabály létrehozása: 
- Válassza a bal oldali navigációs ablaktábla **szabályok** elemének **+ új** elemét.

## <a name="configure-jobs"></a>Feladatok konfigurálása

IoT Central a feladatok lehetővé teszik, hogy az eszköz vagy a Felhőbeli tulajdonságok frissítései több eszközön is aktiválva legyenek. A tulajdonságok mellett a feladatok segítségével több eszközön is aktiválhatja az eszközök parancsait. IoT Central automatizálja a munkafolyamatot. 

1. Nyissa meg a **feladatok** elemet a bal oldali navigációs ablaktáblán. 
2. Kattintson az **+ új** elemre, és konfiguráljon egy vagy több feladatot. 


## <a name="customize-your-application"></a>Az alkalmazás testreszabása 
Építőként több beállítást is módosíthat az alkalmazás felhasználói felületének testreszabásához.

1.  Nyissa meg az **adminisztráció > az alkalmazás testreszabása**lehetőséget.
3. A **módosítás** gombbal kiválaszthatja az **alkalmazás emblémájának**feltöltendő képet.
4. A **módosítás** gomb használatával kiválaszthatja a böngésző lapjain megjelenő **böngészőablak-ikont** .
5. Az alapértelmezett **böngésző színeit** HTML hexadecimális színkódok hozzáadásával is lecserélheti.

   ![Az Azure IoT Central az alkalmazás testreszabása](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1.  Az alkalmazás lemezképeit az **adminisztráció > az Alkalmazásbeállítások** lehetőségre kattintva, majd a **kép** gomb kiválasztásával kiválaszthatja az alkalmazási képként feltölteni kívánt rendszerképet. 
2. Végül az alkalmazás fejlécén található **Beállítások** elemre kattintva módosíthatja a **témát** is. 

  
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az alkalmazást az alábbi lépésekkel:

1. Nyissa meg az adminisztráció lapot a IoT Central alkalmazás bal oldali paneljén. 
2. Válassza az Alkalmazásbeállítások lehetőséget, majd kattintson a lap alján található Törlés gombra. 


## <a name="next-steps"></a>Következő lépések

* További információ a [vízfelhasználás figyelésével kapcsolatos fogalmakról](./concepts-waterconsumptionmonitoring-architecture.md)
