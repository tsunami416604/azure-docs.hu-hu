---
title: 'Oktatóanyag: Hozzon létre egy csatlakoztatott hulladékkezelő alkalmazást az Azure IoT Central szolgáltatással'
description: 'Oktatóanyag: Ismerje meg, hogyan hozhat létre egy csatlakoztatott hulladékkezelő alkalmazást az Azure IoT Central alkalmazássablonjai használatával.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 274d725a0e7987c8cb15363ba276aec7ebc541d5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77426360"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Oktatóanyag: Hozzon létre egy csatlakoztatott hulladékgazdálkodási alkalmazást az IoT Centralban



Ez az oktatóanyag bemutatja, hogy hozzon létre egy Azure IoT Central csatlakoztatott hulladékgazdálkodási alkalmazást az IoT Central **Connected hulladékgazdálkodási** alkalmazássablonból. 

Az oktatóanyag során a következőket fogja elsajátítani: 

> [!div class="checklist"]

> * Az Azure IoT központi **kapcsolattal rendelkező hulladékgazdálkodási** sablonjával létrehozhatja a csatlakoztatott hulladékkezelési alkalmazást
> * Az operátori irányítópult felfedezése és testreszabása 
> * A csatlakoztatott hulladéktároló-eszközsablon felfedezése
> * Szimulált eszközök felfedezése
> * Szabályok feltárása és konfigurálása
> * Feladatok konfigurálása
> * Az alkalmazás márkajelzésének testreszabása a whitelabeling használatával

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:
-  Egy Azure-előfizetés ajánlott. Az ingyenes 7 napos próbaverziót igény szerint használhatja. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán.](https://aka.ms/createazuresubscription)

## <a name="create-connected-waste-management-app-in-iot-central"></a>Csatlakoztatott hulladékgazdálkodási alkalmazás létrehozása az IoT Centralban

Ebben a szakaszban az Azure IoT Central **Connected hulladékgazdálkodási sablon** használatával hozza létre a csatlakoztatott hulladékkezelési alkalmazást az IoT Centralban.

Új Azure IoT Central csatlakoztatott hulladékgazdálkodási alkalmazás létrehozása:  

1. Keresse meg az [Azure IoT Central kezdőlapját.](https://aka.ms/iotcentral)

    Ha Rendelkezik Azure-előfizetéssel, jelentkezzen be az eléréséhez használt hitelesítő adatokkal, ellenkező esetben jelentkezzen be Egy Microsoft-fiókkal:

    ![Lépjen a céges fiókjába](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Kattintson a bal oldali ablaktáblában a **Build** elemre, és válassza a **Kormány** lapot. A kormányzati oldalon több kormányzati alkalmazássablon látható.

    ![Kormányzati alkalmazássablonok létrehozása](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Válassza ki a **Csatlakoztatott hulladékkezelés** alkalmazássablont. Ez a sablon tartalmazza a minta csatlakoztatott hulladéktároló eszköz sablon, szimulált eszköz, üzemeltető iirányítópult, és előre konfigurált figyelési szabályok.    

2. Kattintson **az Alkalmazás létrehozása**gombra, amely megnyitja az Új **alkalmazáslétrehozási** űrlapot a következő mezőkkel:
    * **Alkalmazás neve**. Alapértelmezés szerint az alkalmazás a *Csatlakoztatott hulladékkezelést,* majd az IoT Central által létrehozott egyedi azonosítókarakterláncot használja. Ha meg kell választania egy rövid alkalmazásnevet. Később is módosíthatja az alkalmazás nevét.
    * **URL** - Opcionálisan kiválaszthatja a kívánt URL-t. Később is módosíthatja az URL-címet. 
    * Ha Rendelkezik Azure-előfizetéssel, adja meg *a címtár, az Azure-előfizetés és a régió*. Ha nem rendelkezik előfizetéssel, engedélyezheti a **7 napos ingyenes próbaverziót,** és kivégezheti a szükséges kapcsolattartási adatokat.  

    A könyvtárakkal és előfizetésekkel kapcsolatban további információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](../core/quick-deploy-iot-central.md).

5. Kattintson a lap alján található **Létrehozás** gombra. 

    ![Az Azure IoT központi összekapcsolt hulladékalkalmazás létrehozása lapja](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT Központi összekapcsolt számlázási adatok létrehozása](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
6. Most létrehozott egy csatlakoztatott hulladékkezelő alkalmazást az Azure IoT Central **Connected hulladékgazdálkodási sablon használatával.** 

Gratulálunk! Az újonnan létrehozott alkalmazás előre konfigurált:
* Mintaoperátor-irányítópultok
* Minta előre definiált csatlakoztatott hulladéktároló eszközsablonjai
* Szimulált csatlakoztatott hulladéktároló-eszközök
* Előre konfigurált szabályok és feladatok
* Mintamárka fehér címkézéssel 

Ez az alkalmazás, és módosíthatja azt bármikor. Most vizsgálja meg az alkalmazást, és néhány testreszabást.  

## <a name="explore-and-customize-operator-dashboard"></a>Az operátori irányítópult felfedezése és testreszabása 
Az alkalmazás létrehozása után a **Széles hulladékhoz csatlakoztatott hulladékkezelési irányítópulton**landol.

   ![Csatlakoztatott hulladékgazdálkodási irányítópult](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Szerkesztőként nézeteket hozhat létre és szabhat testre az irányítópulton az operátorok számára. Mielőtt megpróbálna testreszabni, tárja fel az irányítópultot. 

> [!NOTE]
> Az irányítópulton megjelenített összes adat szimulált eszközadatokon alapul, amelyeket a következő szakaszban fognak feltárni. 

Az irányítópult különböző csempékből áll:

* ***Wide World Waste segédprogram kép csempe:*** az első csempe a műszerfalon egy kép csempe egy fiktív hulladék segédprogram "Wide World Waste". Testreszabhatja a csempét, és elhelyezheti saját képét, vagy eltávolíthatja azt. 

* ***Hulladéktároló képmozaikja:*** a kép- és tartalomcsempék segítségével vizuális annektálást hozhat létre a figyelt eszközről egy leíró szöveggel együtt. 

* ***Töltési szint KPI csempe:*** a csempe egy *töltőszint-érzékelő* által jelentett értéket jelenít meg a hulladéktárolóban. *A töltési szint* és más érzékelők, mint a *szagmérő* vagy a *hulladéktárolóban lévő súly* távolról is ellenőrizhetők. Az operátor tehet lépéseket, például a szemétgyűjtő teherautó feladása. 

*  ***Hulladékfigyelési terület térkép:*** a térkép az Azure Maps, amely konfigurálható közvetlenül az Azure IoT Central. A térképcsempe megjeleníti az eszköz helyét. Próbálja meg az egérmutatót a térkép fölé, és próbálja meg a vezérlőket a térkép felett, például a nagyítást, a kicsinyítést vagy a kibontást.

     ![Csatlakoztatott hulladékgazdálkodási irányítópult térképe](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***Kitöltés, szag, súlyszintű sávdiagram:** egy vagy több eszköztelemetriai adatot jeleníthet meg egy sávdiagramon. A sávdiagramot ki is bonthatja.  

  ![Csatlakoztatott hulladékgazdálkodási irányítópult-diagram](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Field Services tartalomcsempe:** az irányítópult hivatkozásokat tartalmaz arra vonatkozóan, hogy miként integrálható a Dynamics 365 Field Services alkalmazással az Azure IoT Central alkalmazásból. Például a Field Services segítségével hozhat létre jegyeket a kukagyűjtési szolgáltatások feladása érdekében. 


### <a name="customize-dashboard"></a>Irányítópult testreszabása 

Szerkesztőként testreszabhatja az irányítópult on-irányítópultjának nézeteit az operátorok számára. Megpróbálhatja:
1. Kattintson a **Szerkesztés** gombra a **Wide World csatlakoztatott hulladékgazdálkodási irányítópultjának**testreszabásához. Az irányítópultot a **Szerkesztés** menüben szabhatja testre. Ha az irányítópult **szerkesztési** módban van, új csempéket adhat hozzá, vagy beállíthatja a 

    ![Irányítópult szerkesztése](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. Azt is kattintson a **+ Új** új irányítópultot, és konfigurálja a semmiből. Több irányítópultja is lehet, és az irányítópult okán navigálhat az irányítópultok között. 

## <a name="explore-connected-waste-bin-device-template"></a>A csatlakoztatott hulladéktároló-eszközsablon felfedezése

Az Azure IoT Central eszközsablonja meghatározza az eszköz képességeit, amely lehet telemetriai, tulajdonság vagy parancs. Szerkesztőként olyan eszközsablonokat határozhat meg, amelyek a csatlakoztatni kívánt eszközök képességeit tükrözik. 
 

A **Csatlakoztatott hulladékgazdálkodási** alkalmazás hoz egy minta csatlakoztatott hulladéktároló eszköz sablont.

Az eszközsablon megtekintése:

1. Kattintson **az Eszközsablonok** ra az alkalmazás bal oldali ablaktáblájában az IoT Central ban. 

    ![Eszközsablon](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. Az Eszközsablonok listában a **Csatlakoztatott hulladéktároló**látható. A megnyitása a névre kattintva.

3. Ismerkedjen meg az eszközsablon képességeivel. Láthatjuk, hogy meghatározza érzékelők, mint *a töltési szint,* *Szag mérő,* *súly,* *hely,* és mások.

   ![Eszközsablon](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>Az eszközsablon testreszabása

Próbálja meg testreszabni a következőket:
1. Navigálás a **Testreszabás** menüből
2. A `Odor meter` telemetriai típus megkeresése
3. A **megjelenítendő név** `Odor meter` frissítése`Odor level`
4. Próbálkozhat mértékegység frissítésével is, vagy beállíthatja a *Min értéket* és *a Maximális értéket*
5. **A** módosítások mentése 

### <a name="add-a-cloud-property"></a>Felhőtulajdonság hozzáadása 

1. Navigálás a **Felhő tulajdonságra** az eszközsablon menüjéből
2. Új felhőtulajdonság hozzáadása **a + Felhőalapú tulajdon hozzáadása**gombra kattintva. Az IoT Central ban hozzáadhat egy tulajdonságot, amely releváns az eszközhöz, de nem várható, hogy egy eszköz által küldött. Például egy felhő tulajdonság lehet egy riasztási küszöbértéket a telepítési terület, az eszköz adatait, vagy karbantartási információkat, és egyéb információkat. 
3. **A** módosítások mentése 
 
### <a name="views"></a>Nézetek 
* A csatlakoztatott hulladéktároló eszközsablon előre meghatározott nézetekkel rendelkezik. Fedezze fel a nézeteket, és frissítéseket is elérhet. A nézetek határozzák meg, hogy az operátorok hogyan fogják látni az eszközadatokat, de a felhőtulajdonságok bevitelét is. 

  ![Eszközsablon-nézetek](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Közzététel 

* Ha módosításokat hajtott végre, győződjön meg arról, hogy **közzéteszi** az eszközsablont. 

### <a name="create-a-new-device-template"></a>Új eszközsablon létrehozása 

* Válassza a **+ Új** lehetőséget új eszközsablon létrehozásához és a létrehozási folyamat követéséhez. Az azure-i eszközkatalógusból létrehozhat egy egyéni eszközsablont, vagy választhat egy eszközsablont. 

## <a name="explore-simulated-devices"></a>Szimulált eszközök felfedezése

Az IoT Centralban szimulált eszközöket hozhat létre az eszközsablon és -alkalmazás teszteléséhez. 

A **Csatlakoztatott hulladékgazdálkodási** alkalmazás két szimulált eszközzel rendelkezik a csatlakoztatott hulladéktároló eszközsablonhoz. 

### <a name="to-view-the-devices"></a>Az eszközök megtekintése:

1. Keresse meg **az Eszközt** az IoT Central bal oldali ablaktáblájából. 

   ![Eszközök](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Válassza ki, és kattintson a Csatlakoztatott hulladéktároló eszköz.  

     ![1. eszköz](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Nyissa meg a **Felhőtulajdonságok** lapot, `95` és `100`próbálja meg frissíteni az `Bin full alert threshold` értéket a-ból. 
* Fedezze fel az **Eszköz tulajdonságai** lapot és az **Eszközirányítópult** lapot. 

> [!NOTE]
> Ne feledje, hogy az összes lap az **Eszközsablon nézetek**nézetei ből lett konfigurálva.

### <a name="add-new-devices"></a>Új eszközök hozzáadása

* Új eszközöket az **Eszközök** lapon a **+ Új** gombra kattintva adhat hozzá. 

## <a name="explore-and-configure-rules"></a>Szabályok feltárása és konfigurálása

Az Azure IoT Centralban létrehozhat szabályokat az eszköz telemetriai adatainak automatikus figyelésére, és műveleteket indíthat el, ha egy vagy több feltétel teljesül. A műveletek közé tartozhat az e-mail értesítések küldése, a Microsoft Flow-művelet aktiválása, vagy egy webhook-művelet, amely adatokat küld más szolgáltatásoknak.

A **Csatlakoztatott hulladékgazdálkodási** alkalmazás négy mintaszabályból rendelkezik.

### <a name="to-view-rules"></a>Szabályok megtekintése:
1. Navigálás a **Szabályok** elemre az IoT Central bal oldali ablaktáblájából

   ![Szabályok](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. A **Raktárhely teljes riasztásának** kiválasztása

     ![Raktárhely teljes riasztása](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. Az `Bin full alert` **ellenőrzések,** `Fill level is greater than or equal to Bin full alert threshold`ha feltétel .

    A `Bin full alert threshold` egy *felhő tulajdonság* az `Connected waste bin` eszközsablonban definiálva. 

Most hozzunk létre egy e-mail műveletet.

### <a name="create-an-email-action"></a>E-mail művelet létrehozása
E-mail művelet konfigurálása a szabály műveletlistájában:
1. Válassza a **+ E-mail lehetőséget.** 
2. Adja meg *a magas pH-riasztást* a művelet rövid **megjelenítendő neveként.**
3. Adja meg az IoT Central-fiókjához társított e-mail címet a **Címzett mezőbe.** 
4. Tetszés szerint írjon be egy megjegyzést, amelyet az e-mail szövegében szeretne szerepelni.
5. A művelet végrehajtásához válassza a **Kész** lehetőséget.
6. Az új szabály mentéséhez és aktiválásához válassza a **Mentés** lehetőséget. 

A beállított **feltétel** teljesülése esetén e-mailt kell kapnia.

> [!NOTE]
> Az alkalmazás e-mailt küld minden alkalommal, amikor egy feltétel teljesül. **Tiltsa le** a szabályt, ha le szeretné állítani az automatikus szabályból érkező e-mailek fogadását. 
  
Új szabály létrehozása: 
1. A bal oldali ablaktáblában válassza az **+Új** lehetőséget a **Szabályok** panelen.

## <a name="configure-jobs"></a>Feladatok konfigurálása

Az IoT Central ban a feladatok lehetővé teszik az eszköz- vagy felhőtulajdonságok frissítéseinek aktiválását több eszközön. A tulajdonságok mellett a feladatok segítségével eszközparancsokat is aktiválhat több eszközön. Az IoT Central automatizálja a munkafolyamatot. 

1. Lépjen a **Feladatok** elemre a bal oldali ablaktáblából. 
2. Kattintson **az +Új** gombra, és konfiguráljon egy vagy több feladatot. 


## <a name="customize-your-application"></a>Az alkalmazás testreszabása 

Szerkesztőként számos beállítást módosíthat az alkalmazás felhasználói élményének testreszabásához.

### <a name="to-change-the-application-theme"></a>Az alkalmazás téma módosítása:

1. Nyissa meg **a Felügyeleti > Az alkalmazás testreszabása**című lapot.
3. A **Módosítás** gombbal kiválaszthatja az **Alkalmazás emblémájaként**feltöltendő képet.
4. A **Módosítás** gombbal választhat ja a böngészőlapokon megjelenő **böngészőikonképet.**
5. Az alapértelmezett **böngészőszíneket** html hexadecimális színkódok hozzáadásával is lecserélheti.

   ![Az Azure IoT Central testreszabhatja az alkalmazást](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. Az alkalmazásképeket úgy is módosíthatja, hogy az **Alkalmazás felügyeleti > alkalmazás beállításai** és a Kép **kiválasztása** gombra kattintva kiválaszthatja az alkalmazásképként feltöltendő képet.
7. Végül a **téma** is módosítható, ha az alkalmazás fejlécén a **Beállítások** gombra kattint.

  
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje az alkalmazást a következő lépésekkel:

1. Nyissa meg a Felügyelet lapot az IoT Central alkalmazás bal oldali ablaktáblájából.
2. Válassza az Alkalmazásbeállítások lehetőséget, majd kattintson a lap alján található Törlés gombra.

  

## <a name="next-steps"></a>További lépések

* További információ a [csatlakoztatott hulladékgazdálkodási fogalmakról](./concepts-connectedwastemanagement-architecture.md)
