---
title: Vízminőség-figyelési alkalmazás létrehozása az Azure IoT Central
description: Ismerje meg, hogyan hozhat létre vízminőség-figyelési alkalmazást az Azure IoT Central alkalmazás-sablonok használatával.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: c2e68e83999ace4094d060a21e91b87347b03e9c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72955108"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-iot-central"></a>Oktatóanyag: a víz minőségének monitorozására szolgáló alkalmazás létrehozása IoT Central

Ez az oktatóanyag végigvezeti egy Azure IoT Central Water minőség-figyelési alkalmazás létrehozásában a IoT Central Water Quality monitoring Application sablonból. 

Ebben az oktatóanyagban a következőket fogja elsajátítani: 

> [!div class="checklist"]
> * A vízminőség-figyelési alkalmazás létrehozása az Azure IoT Central **Water minőség-figyelési** sablonnal
> * Az operátor irányítópultjának megismerése és testreszabása 
> * A vízminőség-figyelő eszköz sablonjának megismerése
> * Szimulált eszközök megismerése
> * Szabályok megismerése és konfigurálása
> * Feladatok konfigurálása
> * Az alkalmazás arculatának testreszabása a címek megjelölése megbízhatóként használatával


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:
-  Az Azure-előfizetés használata ajánlott. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription).


## <a name="create-water-quality-monitoring-app-in-iot-central"></a>Vízminőség-figyelési alkalmazás létrehozása IoT Central

Ebben a szakaszban az Azure IoT Central **Water minőség-figyelési sablont** fogjuk használni a vízminőség-figyelési alkalmazás létrehozásához IoT Centralban.


Új Azure IoT Central Water minőség-figyelési alkalmazás létrehozása:  

1. Navigáljon az [Azure IoT Central Kezdőlap](https://aka.ms/iotcentral) webhelyére.

      Ha rendelkezik Azure-előfizetéssel, jelentkezzen be az eléréséhez használt hitelesítő adatokkal, máskülönben jelentkezzen be Microsoft-fiók használatával:

    ![Lépjen a céges fiókjába](./media/tutorial-waterqualitymonitoring/sign-in.png)

2. Kattintson a **Létrehozás** elemre a bal oldali navigációs menüből, és válassza a **kormányzat** fület. A kormányzat oldalon számos kormányzati alkalmazás-sablon látható.

    ![Kormányzati alkalmazás-sablonok készítése](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)


1. Válassza ki a **víz minőségének figyelése** alkalmazás sablonját. Ez a sablon magában foglalja a minta víz minőségi eszköz sablonját, a szimulált eszközt, az operátor irányítópultját és az előre konfigurált figyelési szabályokat.    

2. Kattintson az alkalmazás **létrehozása**elemre, amely megnyitja az **új alkalmazás** -létrehozási űrlapot a következő mezőkkel:
    * Az **alkalmazás neve**. Alapértelmezés szerint az alkalmazás a *vízminőség-figyelést* , majd a IoT Central által GENERÁLT egyedi azonosító karakterláncot használja. Igény szerint válasszon egy felhasználóbarát nevet. Az alkalmazás neve később is módosítható.
    * **URL** – opcionálisan megadhatja a kívánt URL-címet. Az URL-címet később is megváltoztathatja. 
    * Ha Azure-előfizetéssel rendelkezik, adja meg a *címtárat, az Azure-előfizetést és a régiót*. Ha nem rendelkezik előfizetéssel, engedélyezheti a **7 napos ingyenes próbaidőszakot** , és elvégezheti a szükséges kapcsolattartási adatokat.  

    A könyvtárakkal és előfizetésekkel kapcsolatban további információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

5. Kattintson a lap alján található **Létrehozás** gombra. 

    ![Azure IoT Central Alkalmazás létrehozása oldal](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)


6. Ezzel létrehozott egy vízminőség-figyelési alkalmazást az Azure IoT Central **Water Quality monitoring sablonnal**. 

Az újonnan létrehozott alkalmazás előre konfigurálva van:
* Minta operátor irányítópultok
* Minta előre definiált vízminőség-figyelő eszköz sablonjai
* Szimulált víz minőségének monitorozására szolgáló eszközök
* Előre konfigurált szabályok és feladatok
* Minta-védjegyezés a fehér címkézés használatával 

Ez az alkalmazás, és bármikor módosítható. Most Ismerkedjen meg az alkalmazással, és végezze el a testreszabást.  


## <a name="explore-and-customize-operator-dashboard"></a>Az operátor irányítópultjának megismerése és testreszabása 
Miután létrehozta az alkalmazást, a **víz a vízminőség monitorozási irányítópultján**fog megjelenni.


   ![A víz minőségének monitorozási irányítópultja](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Építőként létrehozhat és testre szabhatja a nézeteket az irányítópulton a kezelők számára. Mielőtt megpróbálja testreszabni a testreszabást, ismerkedjen meg az irányítópulttal. 

Az irányítópulton megjelenő összes érték szimulált eszközön alapul, amely a következő szakaszban lesz felderítve. 

Az irányítópult különböző típusú csempéket tartalmaz:

* A **globális vízkészletek képcsempéje**: az irányítópult első csempe a "Wide World Water" kitalált víz-segédeszköz képcsempe. Testreszabhatja a csempét, és elhelyezheti saját képét, vagy eltávolíthatja azt. 

* **Átlagos pH KPI csempe**: láthatja, hogy az **elmúlt 30 percben az átlagosnál**több KPI-csempe található. Testreszabhatja a KPI-csempéket, és beállíthatja, hogy egy másik típus-és időtartományra legyen beállítva.

*  **Vízfigyelési terület leképezése**: a IoT Central Azure Maps használ, amelyet közvetlenül az alkalmazásban lehet beállítani és megjeleníteni az eszköz helyét. A tartózkodási hely adatait az alkalmazásból is leképezheti az eszközre, és a Azure Maps használatával megjelenítheti azt egy térképen. Vigye a kurzort a Térkép fölé, és próbálja ki a vezérlőket a térképen. 

* **Átlagos pH-eloszlás hő diagramja**: különböző vizualizációs diagramokat választhat, hogy az eszköz telemetria adatait az alkalmazásnak legmegfelelőbb módon jelenítse meg. Hő 

* **Kritikus minőségi mutatók diagramja**: megjelenítheti a kívánt időtartományon belül egy vagy több telemetriáiról ábrázoló diagramot.  

* **Kémiai ágensek sávdiagram-diagramja**: egy vagy több telemetria-adategységet jeleníthet meg egy sávdiagramban a mintában. 

* **Művelet gomb**: az irányítópult egy példát tartalmaz arra, hogy egy operátor közvetlenül a figyelési irányítópultról kezdeményezzen egy műveletet, például az eszköz tulajdonságainak alaphelyzetbe állításához. 

* **Tulajdonságok listája csempék**: az irányítópulton több tulajdonságlap is szerepel, amelyek küszöbértékeket, Eszközállapot-információkat és karbantartási információkat tartalmaznak. 


### <a name="customize-dashboard"></a>Irányítópult testreszabása 

Építőként testreszabhatja a nézeteket az irányítópulton a kezelők számára. 
1. Kattintson a **Szerkesztés** elemre a **globális vízminőség monitorozási irányítópultjának**testreszabásához. A **Szerkesztés** menüre kattintva testreszabhatja az irányítópultot. Ha az irányítópult **szerkesztési** módban van, új csempéket adhat hozzá, vagy beállíthatja

    ![Irányítópult szerkesztése](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

2. Kattintson az **+ új** elemre az új irányítópult létrehozásához és a konfiguráláshoz a semmiből. Több irányítópulttal is rendelkezhet, és az irányítópultok menüjéből is megnyithatja az irányítópultokat.

## <a name="explore-water-quality-monitor-device-template"></a>A vízminőség-figyelő eszköz sablonjának megismerése
Az Azure IoT Central egy eszköz, amely a telemetria, a tulajdonságokat és a parancsokat is meghatározza. Építőként megadhatja azokat az IoT Central, amelyek a csatlakoztatni kívánt eszközök képességeit jelölik. A IoT Centralon szimulált eszközöket is létrehozhat az eszköz sablonjának és alkalmazásának teszteléséhez. 
 

Az alkalmazás sablonjában létrehozott **vízminőség-figyelési** alkalmazáshoz tartozik egy hivatkozási vízminőség-figyelő eszköz sablonja.

Az eszköz sablonjának megtekintése:

1.  Az alkalmazás bal oldali navigációs paneljén kattintson az **eszközök sablonok** elemre IoT Central. 
2. Az eszközök listájában látni fogja a **vízminőség-figyelőt**. Megnyitásához kattintson a névre.

    ![Eszköz sablonja](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customizing-the-device-template"></a>Az eszköz sablonjának testreszabása

Próbálja testre szabni a következőket:
1. Navigáljon a **Testreszabás** elemre az eszköz sablon menüjében
2. A `Temperature` telemetria típusának megkeresése
3. `Temperature` **megjelenítendő nevének** frissítése `Reported temperature`
4. A mértékegység frissítése vagy a *minimális érték* és a *maximális érték* beállítása
5. Módosítások **mentése** 

#### <a name="add-a-cloud-property"></a>Felhőbeli tulajdonság hozzáadása 
1. Navigáljon a **Cloud tulajdonságra** az eszköz sablonja menüben
2. Vegyen fel egy új Felhőbeli tulajdonságot a **+ Cloud Property**elemre kattintva. A IoT Centralban hozzáadhat egy olyan tulajdonságot, amely az eszközhöz szükséges, de nem várható, hogy az eszköz elküldje őket. A Felhőbeli tulajdonság például lehet a telepítési területre, az adategységre vagy a karbantartási információkra jellemző riasztási küszöbérték. 
3. Módosítások **mentése** 
 
### <a name="views"></a>Nézetek 
A vízminőség-figyelő eszköz sablonja előre definiált nézeteket tartalmaz. Ismerkedjen meg a nézetekkel, és frissítheti a frissítéseket. A nézetek határozzák meg, hogy a operátorok hogyan látják az eszköz információit, de a felhő tulajdonságai is megmaradnak. 

  ![Eszköz sablon nézetei](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish"></a>Közzététel 

Ha módosította a módosításokat, győződjön meg arról, hogy az eszköz sablonját **közzéteszi** . 


### <a name="create-a-new-device-template"></a>Új eszközsablon létrehozása 
1. Válassza az **+ új** lehetőséget egy új sablon létrehozásához, és kövesse a létrehozási folyamatot. 
2. Hozzon létre egy egyéni sablont a semmiből, vagy válasszon ki egy sablont az Azure-eszköz katalógusában. 


## <a name="explore-simulated-devices"></a>Szimulált eszközök megismerése

Az alkalmazás sablonjában létrehozott **vízminőség-figyelési** alkalmazáshoz két szimulált eszköz van hozzárendelve a vízminőség-figyelő eszköz sablonhoz. 

### <a name="to-view-the-devices"></a>Az eszközök megtekintése:
1. Navigáljon az **eszközre** IoT Central bal oldali navigációs ablaktáblán. 

   ![Eszközök](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)


2. Válasszon ki egy szimulált eszközt, és kattintson rá 

    ![1\. eszköz](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

3. A **felhő tulajdonságai** lapon próbálja meg frissíteni a `Acidity (pH) Threshold` értéket `8`ról `9`re. 
4. Fedezze fel az **eszköz tulajdonságai** lapot és az **eszköz irányítópultját** . 

> [!NOTE]
> Vegye figyelembe, hogy az összes lapot konfigurálta az **eszköz sablon nézeteiből**.


### <a name="add-new-devices"></a>Új eszközök hozzáadása
Az **eszközök** lapon az **+ új** elemre kattintva adhat hozzá új eszközöket. 


## <a name="explore-and-configure-rules"></a>Szabályok megismerése és konfigurálása

Az Azure IoT Central létrehozhat olyan szabályokat, amelyek automatikusan figyelik az eszköz telemetria, és egy vagy több feltétel teljesülése esetén aktiválnak egy műveletet. A műveletek tartalmazhatják e-mail-értesítések küldését, vagy egy Microsoft Flow művelet vagy egy webhook művelet aktiválását más szolgáltatásoknak való adatküldéshez.

A létrehozott **vízminőség-figyelési** alkalmazásnak két előre konfigurált szabálya van.

### <a name="to-view-rules"></a>Szabályok megtekintése:
1. IoT Central bal oldali navigációs ablaktáblában navigáljon a **szabályokhoz** . 

   ![Szabályok](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)


2. Válassza ki, majd kattintson a **magas pH-riasztásra** , amely az alkalmazás előre konfigurált szabályainak egyike. 

     ![Magas pH-riasztás](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

    A `High pH alert` szabály úgy van konfigurálva, hogy a feltételt `Acidity (pH) is greater than 8`.

Most hozzon létre egy e-mail-műveletet.

Művelet hozzáadása a szabályhoz:

1. Válassza a **+ e-mail**lehetőséget. 
2.  Adja meg a *magas pH-riasztást* a művelet felhasználóbarát **megjelenítendő neveként** .
3. Adja meg a **IoT Central-** fiókjához tartozó e-mail-címet a alkalmazásban. 
4. Szükség esetén megadhat egy, az e-mail-szövegbe felvenni kívánt megjegyzést is.
5. A művelet befejezéséhez kattintson a **kész** gombra.
6. A Save ( **Mentés** ) gombra kattintva mentse és aktiválja az új szabályt. 

Néhány percen belül e-mailt kell kapnia a beállított **feltétel** teljesülése esetén.

> [!NOTE]
> Az alkalmazás minden alkalommal elküld e-mailt, amikor egy feltétel teljesül. **Tiltsa le** a szabályt, hogy ne kapjon e-mailt az automatizált szabálytól. 
  
Új szabály létrehozása: 
1. Válassza a bal oldali navigációs ablaktábla **szabályok** elemének **+ új** elemét.

## <a name="configure-jobs"></a>Feladatok konfigurálása

IoT Central a feladatok lehetővé teszik az eszköz vagy a felhő tulajdonságainak frissítését több eszközön. A tulajdonságok mellett a feladatok segítségével több eszközön is aktiválhatja az eszközök parancsait. IoT Central automatizálja a munkafolyamatot. 

1. Nyissa meg a **feladatok** elemet a bal oldali navigációs ablaktáblán. 
2. Kattintson az **+ új** elemre, és konfiguráljon egy vagy több feladatot. 


## <a name="customize-your-application"></a>Az alkalmazás testreszabása 
Építőként több beállítást is módosíthat az alkalmazás felhasználói felületének testreszabásához.

1. Válassza **az adminisztráció > az alkalmazás testreszabása**lehetőséget.
2. A **módosítás** gombbal kiválaszthatja az **alkalmazás emblémájának**feltöltendő képet.
3.  A **módosítás** gomb használatával kiválaszthatja a böngésző lapjain megjelenő **böngészőablak-ikont** .
4. Az alapértelmezett **böngésző színeit** HTML hexadecimális színkódok hozzáadásával is lecserélheti.
5. Módosítsa a **témát** úgy is, hogy a fejléc **beállításaira** kattint.

   ![Az Azure IoT Central az alkalmazás testreszabása](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="to-update-the-application-image"></a>Az alkalmazás rendszerképének frissítése:

6.  Válassza az **adminisztráció > az Alkalmazásbeállítások**lehetőséget.

7. A **rendszerkép kiválasztása** gomb használatával kiválaszthatja az alkalmazási képként feltölteni kívánt képet. 


  
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az alkalmazást az alábbi lépésekkel:

1. Nyissa meg az adminisztráció lapot a IoT Central alkalmazás bal oldali navigációs menüjében. 
2. Válassza az Alkalmazásbeállítások lehetőséget, majd kattintson a lap alján található Törlés gombra. 

    ![Alkalmazás törlése](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)        



## <a name="next-steps"></a>Következő lépések

* További információ a [vízminőség-figyelési fogalmakról](./concepts-waterqualitymonitoring-architecture.md)