---
title: Új Azure IoT Edge eszköz típusának meghatározása az Azure IoT Centralban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre új Azure IoT Edge eszközt az Azure IoT Central alkalmazásban. Megadhatja a típus telemetria, állapotát, tulajdonságait és parancsait.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c205b4dd4871ed53e32dce72f12cc2dcfb3baf41
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893052"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Oktatóanyag: új Azure IoT Edge eszköz típusának meghatározása az Azure IoT Central alkalmazásban (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ebből az oktatóanyagból megtudhatja, hogyan használhat egy új típusú Azure IoT Edge-eszközt az Azure IoT Central-alkalmazásban, mint építőként. 

A Azure IoT Edge áttekintéséhez [tekintse meg ezt a cikket](overview-iot-central.md). 

Az Azure IoT Edge három összetevőből áll:
* **IoT Edge modulok** olyan tárolók, amelyek Azure-szolgáltatásokat, harmadik féltől származó szolgáltatásokat vagy saját kódot futtatnak. A modulok üzembe helyezése IoT Edge eszközökön történik, és az eszközökön helyileg végrehajtható.
* A **IoT Edge futtatókörnyezet** minden IoT Edge eszközön fut, és az egyes eszközökön üzembe helyezett modulokat kezeli.
* A **felhőalapú felület** lehetővé teszi IoT Edge-eszközök távoli figyelését és kezelését. A IoT Central a felhő felülete lesz.

Az **Azure IoT Edge** eszköz lehet átjáró-eszköz, amely a Azure IoT Edge eszközhöz csatlakozó alsóbb rétegbeli eszközökkel rendelkezik. Ebben az oktatóanyagban az alsóbb rétegbeli eszközök kapcsolódási mintáit fogjuk tárgyalni.

Az **eszköz-sablonok** az eszköz & IoT Edge moduljainak képességeit határozzák meg. A képességek közé tartozik a modul által küldött telemetria, a modul tulajdonságai, valamint az a parancs, amelyet a modul válaszol.

Ebben az oktatóanyagban egy **környezeti Sensor** -eszköz sablonját hozza létre. Környezeti érzékelő eszköz:

* Telemetria, például hőmérsékletet küld.
* A felhőben, például a telemetria küldési intervallumban frissülő, írható tulajdonságokra válaszol.
* Válaszol a parancsokra, például a hőmérséklet alaphelyzetbe állítására.

Ebben az oktatóanyagban egy **környezeti átjáró** -eszköz sablonját is létrehozhatja. Környezeti átjáró eszköz:

* Telemetria, például hőmérsékletet küld.
* A felhőben, például a telemetria küldési intervallumban frissülő, írható tulajdonságokra válaszol.
* Válaszol a parancsokra, például a hőmérséklet alaphelyzetbe állítására.
* Más eszköz-képesség modellek kapcsolatainak engedélyezése


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy új Azure IoT Edge eszköz-eszköz sablont.
> * Töltse fel az üzembe helyezési jegyzéket.
> * Képességek létrehozása, beleértve a telemetria, a tulajdonságokat és a parancsokat az egyes modulokhoz
> * Adjon meg egy vizualizációt a modul telemetria.
> * Kapcsolatok hozzáadása az alsóbb rétegbeli eszközök sablonjaihoz
> * Tegye közzé az eszköz sablonját.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure IoT Central-alkalmazásra. Ezt a rövid útmutatót követve [hozzon létre egy Azure IoT Central alkalmazást](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-gateway--modules"></a>Alsóbb rétegbeli eszközök kapcsolatai az átjáró & moduljaival

Az alsóbb rétegbeli eszközök a $edgeHub modulon keresztül csatlakozhatnak Azure IoT Edge átjáró eszközhöz. Ez a Azure IoT Edge-eszköz átlátszó átjáró lesz ebben a forgatókönyvben

![Központi alkalmazás lapja](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Az alsóbb rétegbeli eszközök egy egyéni modul segítségével csatlakozhatnak Azure IoT Edge átjáróhoz. Az alábbi forgatókönyvben az alsóbb rétegbeli eszközök egy Modbus egyéni modulon keresztül csatlakoznak, és az alsóbb rétegbeli eszközök az $edgeHub modulon keresztül csatlakozhatnak Azure IoT Edge Gateway-eszközhöz.

![Központi alkalmazás lapja](./media/tutorial-define-edge-device-type/gateway-module.png)

Az alsóbb rétegbeli eszközök egy egyéni modul segítségével csatlakozhatnak Azure IoT Edge átjáróhoz. Az alábbi forgatókönyvben az alsóbb rétegbeli eszközök egy Modbus egyéni modulon keresztül csatlakoznak. 

![Központi alkalmazás lapja](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Az alsóbb rétegbeli eszközök több egyéni modulon keresztül csatlakozhatnak Azure IoT Edge Gateway-eszközhöz. Az alábbi forgatókönyvben az alsóbb rétegbeli eszközök egy egyéni Modbus-modulon keresztül csatlakoznak, az egyedi modul és az alsóbb rétegbeli eszközök pedig az $edgeHub modulon keresztül csatlakozhatnak Azure IoT Edge átjáró eszközéhez. 

![Központi alkalmazás lapja](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Sablon létrehozása

Építőként létrehozhat és szerkeszthet Azure IoT Edge-sablonokat az alkalmazásban. Miután közzétett egy sablont, összekapcsolhat valódi eszközöket, amelyek implementálják az eszköz sablonját.

### <a name="select-device-template-type"></a>Válassza ki az eszköz sablonjának típusát 

Új sablon az alkalmazáshoz való hozzáadásához nyissa meg az **eszközök sablonjai** lapot. Ehhez válassza ki a bal oldali ablaktábla **eszközök sablonjai** lapot.

![Központi alkalmazás lapja](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Új sablon létrehozásához kattintson az **+ új** elemre.

![Eszközök sablonjai – új](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

Az eszköz sablon típusa kiválasztási oldalon fog megjelenni. Válassza ki **Azure IoT Edge** csempét, és kattintson a **Tovább gombra: testreszabás** gomb alul

![Eszközök sablonjainak kiválasztása – Azure IoT Edge](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Eszköz sablonjának testreszabása

Azure IoT Edge lehetővé teszi az üzleti logika üzembe helyezését és kezelését modulok formájában. A **Azure IoT Edge modulok** a IoT Edge által kezelt számítási egységek legkisebb egységei, és tartalmazhatnak Azure-szolgáltatásokat (például Azure stream Analytics) vagy a saját megoldásra vonatkozó kódokat. A modulok fejlesztésének, üzembe helyezésének és karbantartásának megismeréséhez olvassa el [IoT Edge modulokat](../../iot-edge/iot-edge-modules.md).

Az üzembe helyezési jegyzék magas szinten az olyan modulok listája, amelyek a kívánt tulajdonságokkal vannak konfigurálva. Az üzembe helyezési jegyzék egy IoT Edge eszközt (vagy az eszközök egy csoportját) adja meg, mely modulokat kell telepíteni és konfigurálni. Az üzembe helyezési jegyzékek tartalmazzák a különálló modulok kívánt tulajdonságait. IoT Edge az eszközök jelentést készítenek az egyes modulok jelentett tulajdonságairól.

A Visual Studio Code használatával hozzon létre egy üzembe helyezési jegyzéket. Az [üzembe helyezési jegyzék](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)létrehozásához kövesse a dokumentációt.

Itt található egy alapszintű üzembe helyezési jegyzék, amely egy modult mutat be példaként ehhez az oktatóanyaghoz. Másolja az alábbi JSON-t, és mentse. JSON-fájlként. 

   ```JSON
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {
              "SendData": true,
              "SendInterval": 10
         }
       }
     }
   }
   ```

**Azure IoT Edge telepítési jegyzék feltöltése**

Kattintson a **Tallózás** gombra 

![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Ha azt tervezi, hogy létrehoz egy Azure IoT Edge Gateway-eszköz sablonját, ügyeljen arra, hogy a **alsóbb rétegbeli eszközök** jelölőnégyzetet jelölje ki.

![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-upload.png)

Ekkor megjelenik egy fájl-kiválasztási párbeszédpanel. Válassza ki a központi telepítési jegyzékfájlt, és kattintson a **Megnyitás** gombra.

Az üzembe helyezési jegyzékfájl a séma alapján lesz érvényesítve. Sikeres ellenőrzés után kattintson a **felülvizsgálat** gombra

![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

Az alábbi folyamat az üzembe helyezési jegyzék életciklusát mutatja IoT Centralban.

![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/dmflow.png)

A felülvizsgálati oldal az üzembe helyezési jegyzék részleteivel jelenik meg. Az üzembe helyezési jegyzékből származó modulok listája a felülvizsgálati oldalon jelenik meg. Ebben az oktatóanyagban a felsorolt SimulatedTemperatureSensor-modul jelenik meg. Kattintson a **Létrehozás** gombra.

![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Ha az átjáró eszközét választotta, akkor ez a felülvizsgálati oldal jelenik meg.

![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


A rendszer létrehoz egy új dokumentumsablon-léptetőt, amely az eszköz sablonját IoT Centralban hozza létre.

Az eszközök modul-képességi modellel jönnek létre. Ebben az oktatóanyagban látni fogja a SimulatedTemperatureSensor modul képesség modell létrehozását. 

Módosítsa az eszköz sablonjának nevét a környezeti érzékelő eszköz sablonjában.

![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Azure IoT Edge Device plug n play modellezés a következőképpen történik
* Minden Azure IoT Edge eszköz-sablonhoz tartozik egy **eszköz-képesség modell**
* Az üzembe helyezési jegyzékben felsorolt összes egyéni modulhoz a rendszer létrehoz egy **modul-képesség modellt** .
* A rendszer létrehoz egy **kapcsolatot** az egyes modulok képességeinek modellje és az eszköz képességeinek modellje között
* Modul-képesség modell implementálja a **modul felületeit**
* Minden modul felülete tartalmaz
   - Telemetria
   - Tulajdonságok
   - Parancsok

![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

**Képességek hozzáadása a modul-képesség modellhez**

Íme egy minta kimenet a SimulatedTemperatureSensor modulból
```json
{

    "machine": {

        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

Vegyen fel képességeket a SimulatedTemperatureSensor modulba, amely a fenti JSON-t tükrözi. 

* Kattintson a SimulatedTemperatureSensor modul képesség modell felületének **kezelése** lehetőségre. Kattintson a **képesség hozzáadása**lehetőségre. 

    ![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
* Adja hozzá a gépet objektumtípus típusú objektumként, mivel ez egy összetett típus
  
    ![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

    Kattintson a **Definiálás**gombra. Az előugró ablak módosítása objektum neve mezőben válassza a számítógép lehetőséget, és hozza létre a tulajdonságok hőmérsékletét, nyomást, és kattintson az **alkalmaz** gombra.
  
    ![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
* Környezeti elem hozzáadása objektumtípus, mivel ez egy összetett típus

    Kattintson a **Definiálás**gombra. Az előugró ablak módosítása objektum neve a környezeti értékre, és a tulajdonságok hőmérséklete, a páratartalom és az **alkalmaz** gombra.
  
    ![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
* TimeCreated hozzáadása DateTime típusú típusként, majd kattintson a **Mentés** gombra.
  
    ![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Kapcsolatok hozzáadása

Ha bejelölte Azure IoT Edge eszköz legyen átjáró-eszköz, hozzáadhat alsóbb rétegbeli kapcsolatokat az eszköz képességi modelljeihez az átjáró-eszközhöz csatlakozó eszközökhöz.
  
  ![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

A kapcsolat hozzáadhatók egy eszközhöz vagy egy modulhoz.
  
  ![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


Kiválaszthat egy alsóbb rétegbeli eszköz képességeinek modelljét vagy kiválaszthatja az Asterix elemet. 
  
  ![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  Ebben az oktatóanyagban kiválasztjuk az Asterix-t, ami azt jelenti, hogy minden alárendelt kapcsolat engedélyezve lesz. Kattintson a **Mentés** gombra.

  ![Eszköz sablonja – Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Felhő tulajdonságainak hozzáadása

Az eszközök tartalmazhatnak Felhőbeli tulajdonságokat is. A felhő tulajdonságai csak a IoT Central alkalmazásban érhetők el, és a rendszer soha nem továbbítja, vagy nem fogadja az eszközt.

1. Válassza a **felhő tulajdonságai** lehetőséget, majd a **+ felhő tulajdonságot**. A következő táblázatban található információk használatával hozzáadhat egy Felhőbeli tulajdonságot az eszköz sablonhoz.

    | Megjelenítendő név      | Szemantikai típus | Séma |
    | ----------------- | ------------- | ------ |
    | Utolsó szervizelés dátuma | None          | Dátum   |
    | Ügyfél neve     | None          | Sztring |

2. A módosítások mentéséhez kattintson a **Save (Mentés** ) gombra:

  
    ![Felhő tulajdonságai – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Testreszabások hozzáadása

Ha módosítania kell egy felületet, vagy IoT Central-specifikus funkciókat kell hozzáadnia egy olyan funkcióhoz, amely nem igényli az eszköz képességeinek megadását, használja a testreszabásokat. A mezőket testreszabhatja, ha a képesség modell Piszkozat vagy közzétett állapotban van. Csak olyan mezőket szabhat testre, amelyek nem bontják le az illesztőfelületek kompatibilitását. Megteheti például a következőket:

- Testreszabhatja egy képesség megjelenített nevét és egységeit.
- Adja meg az alapértelmezett színt, amelyet akkor kell használni, ha az érték megjelenik a diagramon.
- Egy tulajdonság kezdeti, minimális és maximális értékének megadása.

Nem szabhatja testre a képesség nevét vagy a képesség típusát. Kattintson a **Mentés** gombra.
  
![Testreszabások – Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Nézetek létrehozása

Építőként testreszabhatja az alkalmazást, hogy a megfelelő információkat jelenítse meg a környezeti érzékelő eszközéről egy operátorra. A testreszabások lehetővé teszik, hogy az operátor felügyelje az alkalmazáshoz kapcsolódó környezeti érzékelők eszközeit. Kétféle nézetet hozhat létre az operátorok számára az eszközök használatához:

* Az eszközök és a felhő tulajdonságainak megtekintésére és szerkesztésére szolgáló űrlapok.
* Irányítópultok az eszközök megjelenítéséhez.

### <a name="configure-a-view-to-visualize-devices"></a>Nézet konfigurálása eszközök megjelenítéséhez

Az eszközök irányítópultja lehetővé teszi, hogy az operátor diagramok és metrikák használatával megjelenítse az eszközt. Szerkesztőként meghatározhatja, milyen információk jelenjenek meg egy eszköz irányítópultján. Több irányítópultot is megadhat az eszközökhöz. Ha létre szeretne hozni egy irányítópultot a környezeti érzékelők telemetria megjelenítéséhez, válassza a **nézetek** lehetőséget, majd **jelenítse meg az eszközt**:

  
![Nézetek – Azure IoT Edge](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


A környezeti telemetria és a gépi telemetria összetett objektumok, a diagramok létrehozásához tegye a következőket

Húzza a környezeti telemetria, és válassza a vonal diagram lehetőséget. 
  
![Nézetek – Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambientchart.png)

Kattintson a configure (Konfigurálás) ikonra, és válassza a hőmérséklet és a páratartalom lehetőséget az adatmegjelenítéshez, majd kattintson a **konfiguráció frissítése** gombra. 
  
![Nézetek – Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

A nézet mentéséhez válassza a **Mentés** lehetőséget:

További csempéket is hozzáadhat, amelyek további tulajdonságokat vagy telemetria értékeket jelenítenek meg. Hozzáadhat statikus szöveget, hivatkozásokat és képeket is. Az irányítópulton lévő csempe áthelyezéséhez vagy átméretezéséhez vigye az egérmutatót a csempére, és húzza a csempét egy új helyre, vagy méretezze át.
  
![Nézetek – Azure IoT Edge](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Eszköz űrlapjának hozzáadása

Az eszközök űrlapja lehetővé teszi, hogy az operátor az írható eszköz tulajdonságait és a felhő tulajdonságait szerkessze. Építőként több űrlapot is meghatározhat, és kiválaszthatja, hogy mely eszközök és felhő-tulajdonságok jelenjenek meg az egyes űrlapokon. Az űrlap írásvédett eszközének tulajdonságait is megjelenítheti.

Űrlap létrehozása a környezeti érzékelő tulajdonságainak megtekintéséhez és szerkesztéséhez:

Navigáljon a **nézetekhez** a **környezeti érzékelő** sablonban. Új nézet hozzáadásához válassza az **eszköz és a Felhőbeli** adatcsempe szerkesztése lehetőséget.
  
![Nézetek – Azure IoT Edge](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

Adja meg az űrlap neve **környezeti érzékelő tulajdonságait**.

Húzza az **ügyfél neve** és az **utolsó szolgáltatás dátumának** Felhőbeli tulajdonságai elemet az űrlap meglévő szakaszára.
  
![Nézetek – Azure IoT Edge](./media/tutorial-define-edge-device-type/views-properties.png)

A nézet mentéséhez válassza a **Mentés** lehetőséget.

### <a name="generate-default-views"></a>Alapértelmezett nézetek előállítása

Azure IoT Edge sablonok nem támogatják az alapértelmezett nézetek használatát. 

## <a name="publish-device-template"></a>Eszköz sablonjának közzététele

Szimulált környezeti érzékelő létrehozása vagy valódi környezeti érzékelő csatlakoztatása előtt közzé kell tennie az eszköz sablonját.

Eszköz sablonjának közzététele:

1. Nyissa meg az eszköz sablonját az **eszközök sablonjai** lapon.

2. Kattintson a **Publish** (Közzététel) elemre.
  
    ![Nézetek – közzététel](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. Az **eszközbeállítások közzététele** párbeszédpanelen válassza a **Közzététel**lehetőséget:
  
    ![Nézetek – közzététel](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Egy sablon közzététele után az eszköz megjelenik az **eszközök** lapon és a kezelőben. Egy közzétett eszköz sablonjában nem szerkesztheti az eszköz képességeinek modelljét új verzió létrehozása nélkül. A közzétett eszközön azonban verziószámozás nélkül is frissítheti a felhő tulajdonságait, testreszabásait és nézeteit. A módosítások elvégzése után válassza a **Közzététel** lehetőséget a módosítások elküldéséhez az operátornak.
  
![Nézetek – közzététel](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Új Edge létrehozása levélként szolgáló eszköz sablonként
* Modulok létrehozása feltöltött üzembe helyezési jegyzékből
* Összetett típusú telemetria és tulajdonságok hozzáadása
* Felhő tulajdonságainak létrehozása
* Testreszabások létrehozása.
* Adjon meg egy vizualizációt az eszköz telemetria.
* Tegye közzé az Edge-eszköz sablonját.

Most, hogy létrehozott egy eszközt az Azure IoT Central-alkalmazásban, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Eszköz csatlakoztatása](./tutorial-connect-pnp-device.md)
