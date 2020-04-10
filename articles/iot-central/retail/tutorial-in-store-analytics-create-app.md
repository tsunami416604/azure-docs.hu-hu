---
title: Oktatóanyag – Hozzon létre egy üzleten belüli elemzési alkalmazást az Azure IoT Centralban
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy store-on belüli elemzési kiskereskedelmi alkalmazást az IoT Centralban. Létre fogja hozni, testreszabhatja és hozzáadja az érzékelőeszközöket.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 50dd6038a8642f13cea7840fff723a5cf12ce2dd
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000251"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Oktatóanyag: Hozzon létre egy in-store elemzési alkalmazást az Azure IoT Centralban



Az oktatóanyag bemutatja a megoldáskészítőknek, hogyan hozhatnak létre egy Azure IoT Central in store elemzési alkalmazást. A mintaalkalmazás egy kiskereskedelmi üzlet. Ez egy megoldás arra a közös üzleti igényre, hogy figyelemmel kísérjük és alkalmazkodjunk a kihasználtsághoz és a környezeti feltételekhez.

Az Ön által felépített mintaalkalmazás három valódi eszközt tartalmaz: egy Rigado Cascade 500 átjárót és két RuuviTag érzékelőt. Az oktatóanyag azt is bemutatja, hogyan használhatja az alkalmazássablonban található szimulált foglalt foglaltságérzékelőt tesztelési célokra. A Rigado C500 átjáró az alkalmazás kommunikációs központjaként szolgál. Kommunikál az áruház érzékelőivel, és kezeli a felhőhöz fűződő kapcsolatukat. A RuuviTag egy környezeti érzékelő, amely telemetriai, beleértve a hőmérséklet, páratartalom, és a nyomás. A szimulált kihasználtságérzékelő lehetővé teszi a mozgás és a jelenlét nyomon követését az üzlet pénztári területein. 

Ez az oktatóanyag a Rigado és a RuuviTag eszközök alkalmazáshoz való csatlakoztatásához használható. Ha egy másik átjáró és érzékelők, továbbra is követheti az alkalmazás létrehozásához szükséges lépéseket. A bemutató azt is bemutatja, hogyan lehet létrehozni szimulált RuuviTag érzékelők. A szimulált érzékelők lehetővé teszik az alkalmazás felépítését, ha nem rendelkezik valódi eszközökkel. 

A pénztár és az állapotfigyelő megoldás három részből áll:

* Az alkalmazás létrehozása és eszközök csatlakoztatása a feltételek figyeléséhez
* Az irányítópult testreszabása, hogy az operátorok figyelhessék és kezelhessék az eszközöket
* Az adatexportálás konfigurálása, hogy az üzletkezelők elemzéseket futtathassanak és vizualizálhassák az elemzéseket

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az Azure IoT Central **in-store analytics – pénztár** sablon használatával kiskereskedelmi áruházbeli alkalmazást hozhat létre
> * Az alkalmazásbeállításainak testreszabása
> * IoT-eszközsablonok létrehozása és testreszabása
> * Eszközök csatlakoztatása az alkalmazáshoz
> * Szabályok és műveletek hozzáadása a feltételek figyeléséhez

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag-sorozat befejezéséhez a következőkre van szükség:
* Egy Azure-előfizetés ajánlott. Az ingyenes 7 napos próbaverziót igény szerint használhatja. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán.](https://aka.ms/createazuresubscription)
* Hozzáférés egy átjáróeszközhöz és két környezeti érzékelőhöz (opcionálisan használhat szimulált eszközöket az oktatóanyagban leírtak szerint)
* Eszközsablonok a használt eszközökhöz (sablonok az oktatóanyagban használt összes eszközhöz rendelkezésre állnak)

## <a name="create-an-application"></a>Alkalmazás létrehozása
Ebben a szakaszban hozzon létre egy új Azure IoT Central-alkalmazást egy sablonból. Ezt az alkalmazást fogja használni az oktatóanyag-sorozatban a teljes megoldás létrehozásához.

Új Azure IoT Central-alkalmazás létrehozása:

1. Keresse meg az [Azure IoT Central alkalmazáskezelő](https://aka.ms/iotcentral) webhelyét.

1. Ha Rendelkezik Azure-előfizetéssel, jelentkezzen be az eléréséhez használt hitelesítő adatokkal, ellenkező esetben jelentkezzen be Egy Microsoft-fiókkal:

    ![Lépjen a céges fiókjába](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Egy új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **Új alkalmazás** lehetőséget.

1. Válassza a **Kiskereskedelem**lehetőséget.  A kiskereskedelmi oldalon több kiskereskedelmi alkalmazássablon jelenik meg.

Új bolti elemzési kivételalkalmazás létrehozása:  

1. Válassza ki az **in-store analytics - pénztár** alkalmazás sablont. Ez a sablon eszközsablonokat tartalmaz az oktatóanyagban használt összes eszközhöz, kivéve a RuuviTag érzékelőket. A sablon egy kezelői irányítópultot is biztosít a pénztár és a környezeti feltételek, valamint az eszköz állapotának figyelésére. 

1. Tetszés szerint válasszon egy rövid **alkalmazásnevet**. Ez az alkalmazás egy contoso nevű fiktív kiskereskedelmi üzleten alapul. Az oktatóanyag a *Contoso pénztár* **alkalmazásnevét** használja. Az alkalmazássablon a Northwind fiktív cégen alapul. Ebben az oktatóanyagban a Contoso segítségével megtudhatja, hogyan szabhatja testre az alkalmazást.

    > [!NOTE]
    > Ha rövid **alkalmazásnevet**használ, akkor is egyedi értéket kell használnia az alkalmazás **URL-címéhez.**

1. Ha Rendelkezik Azure-előfizetéssel, adja meg *a címtár, az Azure-előfizetés és a régió*. Ha nem rendelkezik előfizetéssel, engedélyezheti a **7 napos ingyenes próbaverziót,** és kivégezheti a szükséges kapcsolattartási adatokat.  

    A könyvtárakkal és előfizetésekkel kapcsolatban további információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](../core/quick-deploy-iot-central.md).

1. Kattintson a **Létrehozás** gombra.

    ![Azure IoT Central Alkalmazás létrehozása oldal](./media/tutorial-in-store-analytics-create-app/preview-application-template.png)

    ![Azure IoT Central Alkalmazásszámlázási adatok létrehozása](./media/tutorial-in-store-analytics-create-app/preview-application-template-billinginfo.png)

## <a name="customize-application-settings"></a>Alkalmazásbeállítások testreszabása

Szerkesztőként számos beállítást módosíthat az alkalmazás felhasználói élményének testreszabásához. Ebben a szakaszban egy előre definiált alkalmazástémát választhat ki. Tetszés szerint megtudhatja, hogyan hozhat létre egyéni témát, és frissítheti az alkalmazásképet. Az egyéni téma lehetővé teszi az alkalmazás böngészőjének színeit, a böngésző ikonját és az alkalmazás emblémáját, amely megjelenik az árbocfejen.

Előre definiált alkalmazástéma kiválasztása:

1. Válassza a **Beállítások lehetőséget** az árbocfejlécen.

    ![Az Azure IoT Central alkalmazásbeállításai](./media/tutorial-in-store-analytics-create-app/settings-icon.png)

2. Válasszon ki egy új **témát**.

3. Kattintson a **Mentés** gombra.

Ahelyett, hogy egy előre definiált témát használna, létrehozhat egy egyéni témát. Ha mintaképeket szeretne használni az alkalmazás testreszabásához és az oktatóanyag befejezéséhez, töltse le a [Contoso mintaképeket.](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail)

Egyéni téma létrehozása:

1. Bontsa ki a bal oldali ablaktáblát, ha még nem bontotta ki.

    ![Az Azure IoT Central bal oldali ablaktáblája](./media/tutorial-in-store-analytics-create-app/dashboard-expand.png)

1. Válassza **a Felügyeleti > Az alkalmazás testreszabása**lehetőséget.

1. A **Módosítás** gombbal kiválaszthatja az **Alkalmazás emblémájaként**feltöltendő képet. Tetszés szerint adjon meg egy értéket az **Embléma helyettesítő szövegéhez.** 

1. A **Módosítás** gombbal választhat ja a böngészőlapokon megjelenő **böngészőikonképet.**

1. Szükség esetén cserélje le az alapértelmezett **böngészőszíneket** HTML hexadecimális színkódok hozzáadásával. A **fejléchez**adja hozzá a *#008575.*  Az **Ékezethez**adja hozzá a *#A1F3EA.* 

1. Kattintson a **Mentés** gombra. 

    ![Az Azure IoT Central testreszabott emblémája](./media/tutorial-in-store-analytics-create-app/select-application-logo.png)

    A mentés után az alkalmazás frissíti a böngésző színeit, az emblémát az árbocfejen és a böngésző ikonját. 

    ![Az Azure IoT Central frissített alkalmazásbeállításai](./media/tutorial-in-store-analytics-create-app/saved-application-settings.png)

Az alkalmazáskép frissítése:

1. Válassza **a Felügyeleti > alkalmazásbeállításait**.

1. A **Kép kiválasztása** gombbal kiválaszthatja az alkalmazásképként feltölteni kívánt képet. Ez a kép az IOt Central alkalmazáskezelő **Saját alkalmazások** lapján jelenik meg az alkalmazáscsempén.

1. Kattintson a **Mentés** gombra.

1. Szükség esetén keresse meg a **Saját alkalmazások** nézetet az [Azure IoT Central alkalmazáskezelő](https://aka.ms/iotcentral) webhelyén. Az alkalmazáscsempe megjeleníti a frissített alkalmazásképet.

    ![Az Azure IoT Central testre szabhatja az alkalmazásképet](./media/tutorial-in-store-analytics-create-app/customize-application-image.png)

## <a name="create-device-templates"></a>Eszközsablonok létrehozása
Szerkesztőként olyan eszközsablonokat hozhat létre, amelyek lehetővé teszik, hogy Ön és az alkalmazás-üzemeltetők konfigurálják és kezeljék az eszközöket. Hozzon létre egy sablont egy egyéni, egy meglévő sablonfájl importálásával, vagy egy sablon importálásával az Azure IoT eszközkatalógusból. Miután létrehozott és testreszabott egy eszközsablont, valós eszközöket csatlakoztathat az alkalmazáshoz. Szükség esetén használjon eszközsablont szimulált eszközök létrehozásához tesztelésre.

Az **in-store analytics - pénztár** alkalmazás sablon eszközsablonok több eszközhöz.  Az alkalmazásban használt három eszköz közül kettőhöz eszközsablonok tartoznak. A RuuviTag eszközsablon nem szerepel az **in-store analytics - pénztár** alkalmazássablonban. Ebben a szakaszban hozzáad egy eszközsablont a RuuviTag érzékelőkhöz az alkalmazáshoz.

RuuviTag eszközsablon hozzáadása az alkalmazáshoz:

1. Válassza **az Eszközsablonok lehetőséget** a bal oldali ablaktáblában.

1. Új eszközsablon létrehozásához válassza a **+ Új** lehetőséget.

1. Keresse meg és válassza ki a **RuuviTag** érzékelő eszközsablont az Azure IoT eszközkatalógusban. 

1. Válassza a **Tovább lehetőséget: Testreszabás**.

    ![Az Azure IoT Központi RuuviTag érzékelőeszköz-sablonja](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png)

1. Kattintson a **Létrehozás** gombra. Az alkalmazás hozzáadja a RuuviTag eszköz sablont.

1. Válassza **az Eszközsablonok lehetőséget** a bal oldali ablaktáblában. Az oldal megjeleníti az alkalmazássablonban található összes eszközsablont, valamint az imént hozzáadott RuuviTag eszközsablont.

    ![Az Azure IoT Központi RuuviTag érzékelőeszköz-sablonja](./media/tutorial-in-store-analytics-create-app/device-templates-list.png)

## <a name="customize-device-templates"></a>Eszközsablonok testreszabása
Az alkalmazásban lévő eszközsablonokat háromféleképpen szabhatja testre. Először testre szabhatja az eszközök natív beépített felületeit az eszköz képességeinek módosításával. A hőmérséklet-érzékelővel például módosíthatja a részleteket, például a hőmérséklet-interfész megjelenítendő nevét, az adattípust, a mértékegységeket, valamint a minimális és maximális működési tartományokat. 

Másodszor, szabja testre az eszközsablonokat felhőalapú tulajdonságok hozzáadásával. A felhőalapú tulajdonságok nem részei a beépített eszközképességeknek. A felhőalapú tulajdonságok olyan egyéni adatok, amelyeket az Azure IoT Central alkalmazás hoz létre, tárol és társít az eszközökhöz. Egy felhőtulajdonság például lehet számított érték, vagy metaadatok, például egy olyan hely, amelyet egy eszközkészlethez szeretne társítani. 

Harmadszor, egyéni nézetek létrehozásával testreszabhatja az eszközsablonokat. A nézetek leteszik az operátorok számára az eszközök telemetriai és metaadatainak megjelenítését, például az eszköz metrikáit és állapotát.

Itt az első két módszert használja a RuuviTag érzékelők eszközsablonjának testreszabásához. Az érzékelők nézeteinek létrehozásáról a [Szimulált eszköz hozzáadása az IoT Central alkalmazás](../core/quick-create-simulated-device.md) hoz rövid útmutató című témakörben talál további információt.

A RuuviTag eszközsablon beépített felületeinek testreszabása:

1. Válassza **az Eszközsablonok lehetőséget** a bal oldali ablaktáblában. 

1. Válassza ki a SablonT a RuuviTag érzékelőkhöz. 

1. A bal oldali ablaktábla elrejtése. A sablon összesítő nézete megjeleníti az eszköz képességeit.

    ![Azure IoT Central RuuviTag eszközsablon összefoglaló nézete](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png)

1. Válassza a RuuviTag eszközsablon menü **Testreszabás parancsát.** 

1. Görgessen a képességek listájában, és keresse meg a `humidity` telemetriai típust. Ez a sor elem a szerkeszthető **Display name** értéke *páratartalom*.

A következő lépésekben testre szabhatja a `humidity` RuuviTag-érzékelők telemetriai típusát. Szükség esetén testre szabhatja a többi telemetriai típust.

A `humidity` telemetriai típushoz hajtsa végre a következő módosításokat:

1. A **Kibontás** vezérlőelem kiválasztásával bontsa ki a sor sémarészleteit.

1. Frissítse a **Megjelenítendő név** értékét *a páratartalomról* egy egyéni értékre, például *a Relatív páratartalomra.*

1. Módosítsa a **Szemantikai típus** beállítást *Nincs* beállításról *páratartalom*ra.  Szükség esetén állítsa be a séma értékeket a páratartalom telemetriai típus a kibontott séma nézetben. A sémabeállítások lehetővé teszik, hogy részletes érvényesítési követelményeket hozzon létre az érzékelők által nyomon követett adatokhoz. Beállíthat például egy adott felület minimális és maximális működési tartományértékeit.

1. A módosítások mentéséhez válassza a **Mentés** gombot.

    ![Az Azure IoT Central RuuviTag eszközsablon testreszabása](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png)

Felhőtulajdonság hozzáadása egy eszközsablonhoz az alkalmazásban:

1. Válassza a RuuviTag eszközsablon **menüjének Felhőtulajdonságok parancsát.**

1. Válassza **a Felhőhozzáadása tulajdonság lehetőséget.** 

Adja meg a következő értékeket az egyes eszközök helyének tárolására egyéni tulajdonság létrehozásához:

1. Adja meg a **Megjelenítendő név** *hely* értékét. Ezt az értéket a program automatikusan a **Név** mezőbe másolja, amely a tulajdonság rövid neve. Használhatja a másolt értéket, vagy módosíthatja azt.

1. Válassza a *Karakterlánc* elemet a **Séma** legördülő menüben. A karakterlánctípus lehetővé teszi, hogy a sablon alapján bármely eszközhöz helynév-karakterláncot társítson. Például minden eszközhöz társíthat egy területet egy üzletben. Szükség esetén beállíthatja a tulajdonság **szemantikai típusát** *Hely*re, és ez automatikusan *Geopoint*ra állítja a **sémát.** Lehetővé teszi a GPS koordináták társítására egy eszközhöz. 

1. Állítsa **a minimális hosszt** *2-re.* 

1. Állítsa **a Szóköz vágása** **beállítását Be**beállításra.

1. Válassza a **Mentés** lehetőséget az egyéni felhőalapú tulajdon mentéséhez.

    ![Az Azure IoT Central RuuviTag eszközsablon testreszabása](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png)

1. Kattintson a **Publish** (Közzététel) elemre. 

    Az eszközsablon közzététele láthatóvá teszi azt az alkalmazásfelelősök számára. Miután közzétett egy sablont, használja azt szimulált eszközök létrehozásához tesztelésre, vagy valós eszközöket csatlakoztathat az alkalmazáshoz. Ha már vannak az alkalmazáshoz csatlakoztatott eszközök, a testreszabott sablon közzététele leküldéses a módosításokat az eszközökre.

## <a name="add-devices"></a>Eszközök felvétele
Miután létrehozta és testreszabta az eszközsablonokat, ideje eszközöket hozzáadni. 

Ebben az oktatóanyagban a következő valós és szimulált eszközök segítségével hozhatja létre az alkalmazást:
- Egy igazi Rigado C500 átjáró
- Két igazi RuuviTag érzékelő
- Egy szimulált **kihasználtsági** érzékelő. A szimulált érzékelő szerepel az alkalmazássablonban, így nem kell létrehozni. 

> [!NOTE]
> Ha nem rendelkezik valódi eszközökkel, akkor is befejezheti ezt az oktatóanyagot szimulált RuuviTag érzékelők létrehozásával. A következő utasítások a szimulált RuuviTag létrehozásának lépéseit tartalmazzák. Nem kell szimulált átjárót létrehoznia.

Hajtsa végre az alábbi két cikklépéseit egy igazi Rigado átjáró és RuuviTag érzékelők csatlakoztatásához. Miután elkészült, térjen vissza az oktatóanyaghoz. Mivel ebben az oktatóanyagban már létrehozott eszközsablonokat, nem kell újra létrehoznia őket a következő irányban.

- Rigado-átjáró csatlakoztatásához lásd: [Rigado Cascade 500 csatlakoztatása az Azure IoT Central alkalmazáshoz.](../core/howto-connect-rigado-cascade-500.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json)
- A RuuviTag-érzékelők csatlakoztatásához olvassa el [a RuuviTag-érzékelő csatlakoztatása az Azure IoT Central alkalmazáshoz](../core/howto-connect-ruuvi.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json). Ezekkel az utasításokkal szükség esetén két szimulált érzékelőt is létrehozhat.

## <a name="add-rules-and-actions"></a>Szabályok és műveletek hozzáadása
Az Azure IoT Central alkalmazás érzékelőinek használatával a feltételek figyelésére szabályokat hozhat létre bizonyos feltételek teljesülése esetén műveletek futtatásához. A szabály egy eszközsablonhoz és egy vagy több eszközhöz van társítva, és olyan feltételeket tartalmaz, amelyeknek az eszköz telemetriai adatai vagy események alapján kell teljesülniük. A szabályhoz egy vagy több társított művelet is tartozik. A műveletek közé tartozhat az e-mail értesítések küldése, vagy egy webhook-művelet aktiválása az adatok más szolgáltatásoknak való küldéséhez. Az **in-store analytics - pénztár** alkalmazás sablon tartalmaz néhány előre meghatározott szabályokat az eszközök az alkalmazásban.

Ebben a szakaszban hozzon létre egy új szabályt, amely ellenőrzi a maximális relatív páratartalom alapján a RuuviTag érzékelő telemetriai adatok alapján. Hozzáad egy műveletet a szabályhoz, hogy ha a páratartalom meghaladja a maximális értéket, az alkalmazás e-mailt küld. 

Szabály létrehozása: 

1. Bontsa ki a bal oldali ablaktáblát.

1. Válassza a **Szabályok**lehetőséget.

1. Válassza **a + Új**lehetőséget.

1. Adja meg a *páratartalom szintjét* a szabály neveként. 

1. Válassza ki a RuuviTag eszközsablont a **Scopes területen.** A megadott szabály az adott sablonon alapuló összes érzékelőre vonatkozik. Szükség esetén létrehozhat egy szűrőt, amely a szabályt csak az érzékelők egy meghatározott részhalmazára alkalmazza. 

1. Válassza `Relative humidity` ki a **Telemetriai adatokat.** Ez az eszköz képessége, amely egy előző lépésben testreszabott.

1. Válassza `Is greater than` ki **az operátort.** 

1. Adja meg a környezetének tipikus felső tartományú beltéri páratartalmát **az Érték .** Írja be például a *65*értéket. Olyan feltételt állított be a szabályhoz, amely akkor következik be, ha bármely RuuviTag valós vagy szimulált érzékelő relatív páratartalma meghaladja ezt az értéket. Előfordulhat, hogy a környezetében szokásos páratartalom-tartománytól függően az értéket fel- vagy lekell állítania.  

   ![Az Azure IoT Central adja hozzá a szabály feltételeit](./media/tutorial-in-store-analytics-create-app/rules-add-conditions.png)

Művelet hozzáadása a szabályhoz:

1. Válassza a **+ E-mail lehetőséget.** 

1. Adja meg *a magas páratartalom értesítés,* mint a rövid **Megjelenítendő nevet** a művelet. 

1. Adja meg a fiókjához társított e-mail címet a **Címzett mezőbe.** Ha más e-mailt használ, a használt címnek az alkalmazáshoz hozzáadott felhasználónak kell lennie. A felhasználónak legalább egyszer be és ki kell jelentkeznie.

1. Tetszés szerint írjon be egy megjegyzést, amelyet az e-mail szövegében szeretne szerepelni.

1. A művelet végrehajtásához válassza a **Kész** lehetőséget.

   ![Az Azure IoT Central műveletek hozzáadása a szabályokhoz](./media/tutorial-in-store-analytics-create-app/rules-add-action.png)

1. Az új szabály mentéséhez és aktiválásához válassza a **Mentés** lehetőséget. 

    Néhány percen belül a megadott e-mail fióknak meg kell kezdenie az e-mailek fogadását. Az alkalmazás e-mailt küld minden alkalommal, amikor egy érzékelő azt jelzi, hogy a páratartalom meghaladja az ön állapotában lévő értéket.

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Az Azure IoT Central **in-store analytics – pénztár** sablon használatával kiskereskedelmi áruházbeli alkalmazást hozhat létre
* Az alkalmazásbeállításainak testreszabása
* IoT-eszközsablonok létrehozása és testreszabása
* Eszközök csatlakoztatása az alkalmazáshoz
* Szabályok és műveletek hozzáadása a feltételek figyeléséhez

Most, hogy létrehozott egy Azure IoT Central állapotfigyelő alkalmazást, az alábbiakat javasolta:

> [!div class="nextstepaction"]
> [Az operátori irányítópult testreszabása](./tutorial-in-store-analytics-customize-dashboard.md)
