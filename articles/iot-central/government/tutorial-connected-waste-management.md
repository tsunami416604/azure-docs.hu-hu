---
title: Csatlakoztatott hulladékgazdálkodási alkalmazás létrehozása az Azure IoT Central | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy csatlakoztatott hulladékgazdálkodási alkalmazást az Azure IoT Central alkalmazás-sablonok használatával.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9af8a94953d7893c71cfc413f9259ec8582a8899
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72955150"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Oktatóanyag: csatlakoztatott hulladékgazdálkodási alkalmazás létrehozása IoT Central
Ez az oktatóanyag bemutatja, hogyan hozhat létre Azure IoT Central csatlakoztatott hulladékgazdálkodási alkalmazást a IoT Central **csatlakoztatott hulladékkezelési** alkalmazás sablonból. 

Az oktatóanyag a következőket ismerteti: 

> [!div class="checklist"]
> * A csatlakoztatott hulladékgazdálkodási alkalmazás létrehozásához használja az Azure IoT Central **csatlakoztatott hulladékgazdálkodási** sablont
> * Az operátor irányítópultjának megismerése és testreszabása 
> * A csatlakoztatott hulladékgyűjtő tároló eszköz sablonjának megismerése
> * Szimulált eszközök megismerése
> * Szabályok megismerése és konfigurálása
> * Feladatok konfigurálása
> * Az alkalmazás arculatának testreszabása a címek megjelölése megbízhatóként használatával

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:
-  Az Azure-előfizetés használata ajánlott. Igény szerint ingyenes 7 napos próbaverziót is használhat. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription).

## <a name="create-connected-waste-management-app-in-iot-central"></a>Csatlakoztatott hulladékgazdálkodási alkalmazás létrehozása IoT Central

Ebben a szakaszban az Azure IoT Central **csatlakoztatott hulladékgazdálkodási sablont** használjuk a csatlakoztatott hulladékgazdálkodási alkalmazás létrehozásához IoT Centralban.

Új Azure IoT Central csatlakoztatott hulladékgazdálkodási alkalmazás létrehozása:  

1. Navigáljon az [Azure IoT Central Kezdőlap](https://aka.ms/iotcentral) webhelyére.

    Ha rendelkezik Azure-előfizetéssel, jelentkezzen be az eléréséhez használt hitelesítő adatokkal, máskülönben jelentkezzen be Microsoft-fiók használatával:

    ![Lépjen a céges fiókjába](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Kattintson a **Létrehozás** elemre a bal oldali navigációs menüből, és válassza a **kormányzat** fület. A kormányzat oldalon számos kormányzati alkalmazás-sablon látható.

    ![Kormányzati alkalmazás-sablonok készítése](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Válassza ki a **csatlakoztatott hulladékkezelési** alkalmazás sablonját. Ez a sablon magában foglalja a minta csatlakoztatott hulladékgyűjtő tároló eszköz sablonját, a szimulált eszközt, az operátor irányítópultját és az előre konfigurált figyelési szabályokat.    

2. Kattintson az alkalmazás **létrehozása**elemre, amely megnyitja az **új alkalmazás** -létrehozási űrlapot a következő mezőkkel:
    * Az **alkalmazás neve**. Alapértelmezés szerint az alkalmazás *csatlakoztatott hulladékgazdálkodást* használ, majd egy egyedi azonosító karakterláncot, amelyet IoT Central generál. Igény szerint válasszon egy felhasználóbarát nevet. Az alkalmazás neve később is módosítható.
    * **URL** – opcionálisan megadhatja a kívánt URL-címet. Az URL-címet később is megváltoztathatja. 
    * Ha Azure-előfizetéssel rendelkezik, adja meg a *címtárat, az Azure-előfizetést és a régiót*. Ha nem rendelkezik előfizetéssel, engedélyezheti a **7 napos ingyenes próbaidőszakot** , és elvégezheti a szükséges kapcsolattartási adatokat.  

    A könyvtárakkal és előfizetésekkel kapcsolatban további információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

5. Kattintson a lap alján található **Létrehozás** gombra. 

    ![Azure IoT Central csatlakoztatott hulladék alkalmazás létrehozása lap](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)

6. Ezzel létrehozott egy csatlakoztatott hulladékgazdálkodási alkalmazást az Azure IoT Central **csatlakoztatott hulladékgazdálkodási sablon**használatával. 

Gratulálunk! Az újonnan létrehozott alkalmazás előre konfigurálva van:
* Minta operátor irányítópultok
* Minta előre definiált csatlakoztatott hulladéktároló-eszközök sablonjai
* Szimulált csatlakoztatott hulladékgyűjtő tároló eszközök
* Előre konfigurált szabályok és feladatok
* Minta-védjegyezés a fehér címkézés használatával 

Ez az alkalmazás, és bármikor módosítható. Most Ismerkedjen meg az alkalmazással, és végezze el a testreszabást.  

## <a name="explore-and-customize-operator-dashboard"></a>Az operátor irányítópultjának megismerése és testreszabása 
Miután létrehozta az alkalmazást, a **széles körben csatlakoztatott hulladékgazdálkodási felügyeleti irányítópulton**kell lennie.

   ![Csatlakoztatott hulladékgazdálkodási irányítópult](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Építőként létrehozhat és testre szabhatja a nézeteket az irányítópulton a kezelők számára. Mielőtt megpróbálja testreszabni a testreszabást, ismerkedjen meg az irányítópulttal. 

> [!NOTE]
> Az irányítópulton megjelenő összes érték szimulált eszközön alapul, amely a következő szakaszban lesz felderítve. 

Az irányítópult különböző típusú csempéket tartalmaz:

* ***Wide World hulladék segédprogram képcsempe***: az irányítópult első csempéje egy fiktív hulladékkezelési segédprogram "Wide World hulladék" nevű képcsempe. Testreszabhatja a csempét, és elhelyezheti saját képét, vagy eltávolíthatja azt. 

* ***Hulladék bin képcsempe***: a kép és a tartalom csempék segítségével vizuálisan jelenítheti meg a figyelt eszközt a leíró szöveggel együtt. 

* ***Kitöltési szint KPI-csempe***: a csempe egy *kitöltési szintű* érzékelő által jelentett értéket jelenít meg egy adattárolóban. A *kitöltési szint* és az egyéb érzékelők (például a *szag-mérő* vagy a *súlyozás* ) távolról is megfigyelhetők. Az operátorok olyan műveleteket végezhetnek, mint például a trash Collection Truck elküldése. 

*  A ***hulladék megfigyelési területének leképezése***: a Térkép Azure Maps használ, amelyet közvetlenül az Azure IoT Central konfigurálhat. A Térkép csempén az eszköz helye jelenik meg. Próbáljon rámutatni a térképre, és próbálja ki a vezérlőket a térképen, például a nagyításhoz, a nagyításhoz vagy a kibővítéshez.

     ![Csatlakoztatott hulladékgazdálkodási irányítópult-Térkép](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***kitöltés, szag, súlyozási szint sávdiagram**: megjelenítheti egy vagy több telemetria-adategységet. A sávdiagram kibontása is megadható.  

  ![Csatlakoztatott hulladékgazdálkodási irányítópult oszlopdiagram](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Field Services-tartalom csempéje**: az irányítópult hivatkozást tartalmaz, amely bemutatja, hogyan integrálható a Dynamics 365 Field Services Azure IoT Central alkalmazásával. A Field Services használatával például jegyek hozhatók létre a trash-gyűjtési szolgáltatások elküldéséhez. 


### <a name="customize-dashboard"></a>Irányítópult testreszabása 

Építőként testreszabhatja a nézeteket az irányítópulton a kezelők számára. A következőket teheti meg:
1. Kattintson a **Szerkesztés** gombra a **Wide World Connected hulladékgazdálkodási irányítópult**testreszabásához. A **Szerkesztés** menüre kattintva testreszabhatja az irányítópultot. Ha az irányítópult **szerkesztési** módban van, új csempéket adhat hozzá, vagy beállíthatja 

    ![Irányítópult szerkesztése](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. Az **+ új** lehetőségre kattintva új irányítópultot hozhat létre, és a konfigurációt teljesen megadhatja. Több irányítópulttal is rendelkezhet, és az irányítópultok menüjéből is megnyithatja az irányítópultokat. 

## <a name="explore-connected-waste-bin-device-template"></a>A csatlakoztatott hulladékgyűjtő tároló eszköz sablonjának megismerése

Az Azure IoT Central egy eszköz, amely a telemetria, a Properties vagy a Command típusú eszköz képességeit határozza meg. Építőként megadhatja azokat az eszközöket, amelyek a csatlakoztatott eszközök képességeit jelölik. 
 

A **csatlakoztatott hulladékkezelési** alkalmazáshoz egy minta csatlakoztatott hulladéktároló-eszköz sablon tartozik.

Az eszköz sablonjának megtekintése:

1. Az alkalmazás bal oldali navigációs paneljén kattintson az **eszközök sablonok** elemre IoT Central. 

    ![Eszköz sablonja](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. Az eszközök listájában megjelenik a **csatlakoztatott hulladék tároló**. Megnyitásához kattintson a névre.

3. Familirize az eszköz sablonjának képességeihez. Láthatja, hogy meghatározza az érzékelőket, például a *kitöltési szintet*, a *szag mérőjét*, a *súlyozást*, a *helyet* stb.

   ![Eszköz sablonja](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>Az eszköz sablonjának testreszabása

Próbálja testre szabni a következőket:
1. Navigáljon a **Testreszabás** elemre az eszköz sablon menüjében
2. A `Odor meter` telemetria típusának megkeresése
3. `Odor meter` **megjelenítendő nevének** frissítése `Odor level`
4. Megpróbálkozhat a mérték frissítése lehetőséggel, vagy megadhatja a *minimális* és a *maximális értéket* is
5. Módosítások **mentése** 

### <a name="add-a-cloud-property"></a>Felhőbeli tulajdonság hozzáadása 

1. Navigáljon a **Cloud tulajdonságra** az eszköz sablonja menüben
2. Vegyen fel egy új Felhőbeli tulajdonságot a **+ Cloud Property**elemre kattintva. A IoT Centralban hozzáadhat egy olyan tulajdonságot, amely az eszközhöz szükséges, de nem várható, hogy az eszköz elküldje őket. A Felhőbeli tulajdonság például lehet a telepítési területre, az adategységre vagy a karbantartási információkra jellemző riasztási küszöbérték. 
3. Módosítások **mentése** 
 
### <a name="views"></a>Nézetek 
* A csatlakoztatott hulladéktároló eszköz sablonja előre definiált nézeteket tartalmaz. Ismerkedjen meg a nézetekkel, és frissítheti a frissítéseket. A nézetek határozzák meg, hogy a operátorok hogyan látják az eszköz információit, de a felhő tulajdonságai is megmaradnak. 

  ![Eszköz sablon nézetei](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Közzététel 

* Ha módosította a módosításokat, győződjön meg arról, hogy az eszköz sablonját **közzéteszi** . 

### <a name="create-a-new-device-template"></a>Új eszközsablon létrehozása 

* Válassza az **+ új** lehetőséget egy új sablon létrehozásához, és kövesse a létrehozási folyamatot. Létrehozhat egy teljesen új egyéni sablont, vagy kiválaszthat egy sablont az Azure-eszköz katalógusában. 

## <a name="explore-simulated-devices"></a>Szimulált eszközök megismerése

A IoT Central szimulált eszközöket hozhat létre az eszköz sablonjának és alkalmazásának teszteléséhez. 

A **csatlakoztatott hulladékkezelési** alkalmazáshoz két szimulált eszköz van hozzárendelve a csatlakoztatott hulladéktároló eszköz sablonhoz. 

### <a name="to-view-the-devices"></a>Az eszközök megtekintése:

1. Navigáljon az **eszközre** IoT Central bal oldali navigációs ablaktáblán. 

   ![Eszközök](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Válassza ki és kattintson a csatlakoztatott hulladéktároló eszköz elemre.  

     ![1\. eszköz](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Navigáljon a **felhő tulajdonságai** lapra, és próbálja meg frissíteni a `Bin full alert threshold` értéket `95`ról `100`re. 
* Fedezze fel az **eszköz tulajdonságai** lapot és az **eszköz irányítópultját** . 

> [!NOTE]
> Vegye figyelembe, hogy az összes lapot konfigurálta az **eszköz sablon nézeteiből**.

### <a name="add-new-devices"></a>Új eszközök hozzáadása

* Az **eszközök** lapon az **+ új** elemre kattintva adhat hozzá új eszközöket. 

## <a name="explore-and-configure-rules"></a>Szabályok megismerése és konfigurálása

Az Azure IoT Central-ban létrehozhat olyan szabályokat, amelyek automatikusan figyelik az eszköz telemetria, és elindítják a műveleteket, ha egy vagy több feltétel teljesül. A műveletek közé tartozhatnak az e-mail-értesítések küldése, egy Microsoft Flow művelet elindítása vagy egy webhook művelet, amely adatokat küld más szolgáltatásoknak.

A **csatlakoztatott hulladékkezelési** alkalmazásnak négy minta szabálya van.

### <a name="to-view-rules"></a>Szabályok megtekintése:
1. IoT Central bal oldali navigációs ablaktáblában navigáljon a **szabályokhoz**

   ![Szabályok](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. Válassza ki a **teljes raktárhely riasztást**

     ![Raktárhely teljes riasztása](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. A `Bin full alert` ellenőrzi, hogy a **feltétel** `Fill level is greater than or equal to Bin full alert threshold`-e.

    A `Bin full alert threshold` a `Connected waste bin`-eszköz sablonjában definiált *Felhőbeli tulajdonság* . 

Most hozzon létre egy e-mail-műveletet.

### <a name="create-an-email-action"></a>E-mail művelet létrehozása
E-mail művelet beállítása a szabály műveletek listájában:
1. Válassza a **+ e-mail**lehetőséget. 
2. Adja meg a *magas pH-riasztást* a művelet felhasználóbarát **megjelenítendő neveként** .
3. Adja meg a **IoT Central-** fiókjához tartozó e-mail-címet a alkalmazásban. 
4. Szükség esetén megadhat egy, az e-mail-szövegbe felvenni kívánt megjegyzést is.
5. A művelet befejezéséhez kattintson a **kész** gombra.
6. A Save ( **Mentés** ) gombra kattintva mentse és aktiválja az új szabályt. 

A beállított **feltétel** teljesülése esetén e-mailt kell kapnia.

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

### <a name="to-change-the-application-theme"></a>Az alkalmazás témájának módosítása:

1. Nyissa meg az **adminisztráció > az alkalmazás testreszabása**lehetőséget.
3. A **módosítás** gombbal kiválaszthatja az **alkalmazás emblémájának**feltöltendő képet.
4. A **módosítás** gomb használatával kiválaszthatja a böngésző lapjain megjelenő **böngészőablak-ikont** .
5. Az alapértelmezett **böngésző színeit** HTML hexadecimális színkódok hozzáadásával is lecserélheti.

   ![Az Azure IoT Central az alkalmazás testreszabása](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. Az alkalmazás lemezképeit az **adminisztráció > az Alkalmazásbeállítások** lehetőségre kattintva, majd a **kép** gomb kiválasztásával kiválaszthatja az alkalmazási képként feltölteni kívánt rendszerképet.
7. Végül az alkalmazás fejlécén található **Beállítások** elemre kattintva módosíthatja a **témát** is.

  
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az alkalmazást az alábbi lépésekkel:

1. Nyissa meg az adminisztráció lapot a IoT Central alkalmazás bal oldali navigációs menüjében. 
2. Válassza az Alkalmazásbeállítások lehetőséget, majd kattintson a lap alján található Törlés gombra. 

  

## <a name="next-steps"></a>Következő lépések

* További információ a [csatlakoztatott hulladékgazdálkodási fogalmakról](./concepts-connectedwastemanagement-architecture.md)
