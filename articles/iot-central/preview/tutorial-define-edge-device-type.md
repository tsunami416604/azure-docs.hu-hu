---
title: Oktatóanyag – új Azure IoT Edge eszköz típusának definiálása az Azure-ban IoT Central
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre új Azure IoT Edge eszközt az Azure IoT Central alkalmazásban. Megadhatja a típus telemetria, állapotát, tulajdonságait és parancsait.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 97bfd2b1e8b571f44c0b782459567f5677dd36a7
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702790"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Oktatóanyag: új Azure IoT Edge eszköz típusának meghatározása az Azure IoT Central alkalmazásban (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ebből az oktatóanyagból megtudhatja, hogyan használhat egy új típusú Azure IoT Edge-eszközt az Azure IoT Central-alkalmazásban, mint építőként. 

Az áttekintést lásd: [Mi az Azure IoT Central (előzetes verziójú funkciók)?](overview-iot-central.md). 

A IoT Edge három összetevőből áll:
* **IoT Edge modulok** az Azure-szolgáltatásokat, a partneri szolgáltatásokat vagy a saját kódokat futtató tárolók. A modulok IoT Edge eszközökön vannak telepítve, és helyileg futnak ezeken az eszközökön.
* A **IoT Edge futtatókörnyezet** minden IoT Edge eszközön fut, és felügyeli az egyes eszközökön üzembe helyezett modulokat.
* A **felhőalapú felület** lehetővé teszi IoT Edge-eszközök távoli figyelését és kezelését. IoT Central a felhő felülete.

Egy **Azure IoT Edge** eszköz lehet átjáró-eszköz, amely a IoT Edge eszközhöz csatlakozó alsóbb rétegbeli eszközökkel rendelkezik. Ez az oktatóanyag további információkat oszt meg az alsóbb rétegbeli eszközök kapcsolati mintáit illetően.

Az **eszköz-sablonok** határozzák meg az eszköz képességeit és IoT Edge modulokat. A képességek közé tartozik a modul által küldött telemetria, a modul tulajdonságai, valamint az a parancs, amelyet a modul válaszol.

Ebben az oktatóanyagban egy környezeti Sensor-eszköz sablonját hozza létre. Környezeti érzékelő eszköz:

* Telemetria, például hőmérsékletet küld.
* A felhőben való frissítéskor írható tulajdonságokra válaszol, például telemetria küldési időköze.
* Válaszol a parancsokra, például a hőmérséklet alaphelyzetbe állítására.

Ebben az oktatóanyagban egy környezeti átjáró-eszköz sablonját is létrehozhatja. Környezeti átjáró eszköz:

* Telemetria, például hőmérsékletet küld.
* A felhőben való frissítéskor írható tulajdonságokra válaszol, például telemetria küldési időköze.
* Válaszol a parancsokra, például a hőmérséklet alaphelyzetbe állítására.
* Más eszköz-képesség modellek kapcsolatainak engedélyezése.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy új Azure IoT Edge eszköz-eszköz sablont.
> * Töltse fel az üzembe helyezési jegyzéket.
> * Hozzon létre képességeket, beleértve a telemetria, a tulajdonságokat és a parancsokat az egyes modulokhoz.
> * Adjon meg egy vizualizációt a modul telemetria.
> * Kapcsolatok hozzáadása az alsóbb rétegbeli eszközök sablonjaihoz.
> * Tegye közzé az eszköz sablonját.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez [létre kell hoznia egy Azure IoT Central alkalmazást](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Alsóbb rétegbeli eszközök kapcsolatai átjáróval és modulokkal

Az alsóbb rétegbeli eszközök a `$edgeHub` modulon keresztül kapcsolódhatnak egy IoT Edge átjáró eszközhöz. Ez az IoT Edge-eszköz egy transzparens átjáró lesz ebben a forgatókönyvben.

![Transzparens átjáró diagramja](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Az alárendelt eszközök egy egyéni modulon keresztül is csatlakozhatnak egy IoT Edge átjáró eszközéhez. A következő forgatókönyvben az alsóbb rétegbeli eszközök egy Modbus egyéni modulon keresztül csatlakoznak.

![Egyéni modul-kapcsolatok diagramja](./media/tutorial-define-edge-device-type/gateway-module.png)

Az alábbi ábrán egy IoT Edge átjáró-eszközhöz való kapcsolódás látható mindkét típusú modulon keresztül (egyéni és `$edgeHub`).  

![A csatlakozás diagramja mindkét kapcsolati modulon keresztül](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Végül az alsóbb rétegbeli eszközök több egyéni modulon keresztül csatlakozhatnak egy IoT Edge átjáróhoz. Az alábbi ábrán egy Modbus egyéni modul, egy egyedi modul és a `$edgeHub` modul segítségével csatlakozó alsóbb rétegbeli eszközök láthatók. 

![Több egyéni modulon keresztüli csatlakozás diagramja](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Sablon létrehozása

Építőként létrehozhat és szerkeszthet IoT Edge-sablonokat az alkalmazásban. Miután közzétett egy sablont, összekapcsolhat valódi eszközöket, amelyek implementálják az eszköz sablonját.

### <a name="select-device-template-type"></a>Válassza ki az eszköz sablonjának típusát 

Ha új sablont szeretne hozzáadni az alkalmazáshoz, válassza a bal oldali panelen az **eszközök sablonjait** .

![Képernyőkép az előnézeti alkalmazásról, az eszköz sablonjainak kiemelésével](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Válassza az **+ új** lehetőséget az új sablon létrehozásának megkezdéséhez.

![Képernyőkép az eszközbeállítások oldalról, új kiemelve](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

A **sablon típusának kiválasztása** lapon válassza a **Azure IoT Edge**lehetőséget, majd kattintson a **Tovább gombra: testreszabás**.

![A sablon típusának kiválasztása lap képernyőképe](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Eszköz sablonjának testreszabása

IoT Edge az üzleti logikát modulok formájában helyezheti üzembe és kezelheti. A IoT Edge modulok a IoT Edge által kezelt számítási egységek legkisebb egységei, és tartalmazhatnak Azure-szolgáltatásokat (például Azure Stream Analytics) vagy a saját megoldásra vonatkozó kódokat. A modulok fejlesztésének, üzembe helyezésének és karbantartásának megismeréséhez tekintse meg [IoT Edge modulokat](../../iot-edge/iot-edge-modules.md).

Az üzembe helyezési jegyzék magas szinten az olyan modulok listája, amelyek a kívánt tulajdonságokkal vannak konfigurálva. Az üzembe helyezési jegyzék egy IoT Edge eszközt (vagy az eszközök egy csoportját) adja meg, mely modulokat kell telepíteni és konfigurálni. Az üzembe helyezési jegyzékek tartalmazzák a különálló modulok kívánt tulajdonságait. IoT Edge az eszközök jelentést készítenek az egyes modulok jelentett tulajdonságairól.

A Visual Studio Code használatával hozzon létre egy üzembe helyezési jegyzéket. További információk: [Azure IoT Edge a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)-hoz.

Íme egy alapszintű üzembe helyezési jegyzék, amely az oktatóanyaghoz egy modult használ. Másolja a következő JSON-t, és mentse. JSON fájlként. 

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

#### <a name="upload-an-iot-edge-deployment-manifest"></a>IoT Edge telepítési jegyzék feltöltése

Az **eszköz testreszabása** lap **Azure IoT Edge üzembe helyezési jegyzékének feltöltése**területén válassza a **Tallózás**lehetőséget. 

![Képernyőkép az eszköz testreszabása oldalról, a Tallózás kiemelve](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Ha IoT Edge Gateway-eszköz sablonját tervezi létrehozni, ügyeljen arra, hogy a **alsóbb rétegbeli eszközökkel rendelkező átjáró eszközt**válassza.

![Képernyőkép az eszköz testreszabása oldalról, az átjáró eszközzel Kiemelt alsóbb rétegbeli eszközökkel](./media/tutorial-define-edge-device-type/gateway-upload.png)

A fájl kijelölése párbeszédpanelen válassza ki a központi telepítési jegyzékfájlt, és válassza a **Megnyitás**lehetőséget.

IoT Edge ellenőrzi a telepítési jegyzékfájl fájlját egy sémán. Ha az ellenőrzés sikeres, válassza az **Áttekintés**lehetőséget.

![Képernyőkép az eszköz testreszabása oldalról, az üzembe helyezési jegyzék és az Áttekintés kiemelésével](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

A következő folyamatábra egy üzembe helyezési jegyzékfájl életciklusát mutatja IoT Centralban.

![Üzembe helyezési jegyzékfájl életciklusának folyamatábrája](./media/tutorial-define-edge-device-type/dmflow.png)

A következő lépésben megjelenik egy felülvizsgálati oldal, amely tartalmazza az üzembe helyezési jegyzék részleteit. Ezen az oldalon az üzembe helyezési jegyzékből származó modulok listája látható. Ez az oktatóanyag azt jelzi, hogy a `SimulatedTemperatureSensor` modul szerepel a listáján. Kattintson a **Létrehozás** gombra.

![Képernyőkép a felülvizsgálati oldalon, a modullal és a Kiemelt létrehozással](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

Ha kiválasztott egy átjáró-eszközt, a következő felülvizsgálati oldal jelenik meg.

![Képernyőkép a felülvizsgálati oldalról, Azure IoT Edge átjáró kiemelve](./media/tutorial-define-edge-device-type/gateway-review.png)


Létre kell hoznia egy olyan eszközt, amely modul-képességi modellel rendelkezik. Ebben az oktatóanyagban létrehoz egy sablont a `SimulatedTemperatureSensor` modul képességi modellel. 

Módosítsa az eszköz sablonjának nevét a **környezeti érzékelő eszköz sablonjában**.

![Képernyőfelvétel az eszköz sablonról, a frissített címmel kiemelve](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

IoT Edge eszközön a modell IoT Plug and Play a következőképpen:
* Minden IoT Edge eszköz-sablonhoz tartozik egy eszköz-képesség modell.
* Az üzembe helyezési jegyzékben felsorolt összes egyéni modulhoz létrejön egy modul-képesség modell.
* Létrejön egy kapcsolat az egyes modulok képességeinek modellje és az eszköz képességeinek modellje között.
* A modul-képesség modell modul-illesztőfelületeket valósít meg.
* Mindegyik modul felülete telemetria, tulajdonságokat és parancsokat tartalmaz.

![IoT Edge modellezés diagramja](./media/tutorial-define-edge-device-type/edgemodelling.png)

#### <a name="add-capabilities-to-a-module-capability-model"></a>Képességek hozzáadása modul-képesség modellhez

Íme egy példa a `SimulatedTemperatureSensor` modul kimenetére:
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

Hozzáadhat képességeket a `SimulatedTemperatureSensor` modulhoz, amely az előző kimenetet fogja tükrözni. 

1. A `SimulatedTemperatureSensor` modul képesség modell felületének kezeléséhez válassza a **kezelés** > **Hozzáadás képesség**lehetőséget. 

    ![Képernyőfelvétel a környezeti érzékelő sablonról, a Hozzáadás funkció kiemelve](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
1. Adjon hozzá egy gépet objektumtípusként.
  
    ![A környezeti érzékelő sablon képességei lap képernyőképe a sémával kiemelve](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

1. Válassza a **Definiálás**lehetőséget. A megjelenő párbeszédpanelen módosítsa az objektum nevét a **gépre**. Hozza létre a hőmérséklet és a nyomás tulajdonságait, majd kattintson az **alkalmaz**gombra.
  
    ![Az attribútumok párbeszédpanel képernyőképe, különböző lehetőségekkel](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
1. Adja hozzá a **környezeti** értéket objektumtípusként.

1. Válassza a **Definiálás**lehetőséget. A megjelenő párbeszédpanelen módosítsa az objektum nevét a **környezeti**értékre. Hozzon létre hőmérséklet-és páratartalom-tulajdonságokat, majd válassza az **alkalmaz**lehetőséget.
  
    ![Az attribútumok párbeszédpanel képernyőképe, különböző lehetőségekkel](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
1. Adja hozzá a `timeCreated` `DateTime` típusként, majd válassza a **Mentés**lehetőséget.
  
    ![Képernyőkép a környezeti érzékelő sablonról, a Mentés kiemelve](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Kapcsolatok hozzáadása

Ha IoT Edge eszközt jelölt ki egy átjáró eszközként, az eszközhöz csatlakozni kívánó eszközökhöz hozzáadhat alsóbb rétegbeli kapcsolatokat az eszköz képességeinek modelljeihez.
  
  ![Képernyőfelvétel a környezeti átjáró sablonról, a kapcsolat hozzáadása Kiemelt](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

Kapcsolatot adhat hozzá egy eszközhöz vagy egy modulhoz.
  
  ![Képernyőkép a környezeti átjáró sablonról, az eszköz és a modul szintjének Kiemelt kapcsolataival](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


Kiválaszthat egy alsóbb rétegbeli eszköz képességeinek modelljét, vagy kiválaszthatja a csillag szimbólumot is. 
  
  ![Képernyőkép a környezeti átjáró sablonról, a cél kiemelve](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  Ebben az oktatóanyagban válassza a csillagot. Ezzel a beállítással bármely alsóbb rétegbeli kapcsolat engedélyezhető. Ezután válassza a **Save** (Mentés) lehetőséget.

  ![Képernyőkép a környezeti átjáró sablonról, a cél kiemelve](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Felhő tulajdonságainak hozzáadása

Az eszközök tartalmazhatnak Felhőbeli tulajdonságokat is. A felhő tulajdonságai csak a IoT Central alkalmazásban érhetők el, és a rendszer soha nem továbbítja, vagy nem fogadja az eszközt.

1. Válassza a **felhő tulajdonságai** >  **+ felhő tulajdonság hozzáadása**elemet. A következő táblázatban található információk használatával hozzáadhat egy Felhőbeli tulajdonságot az eszköz sablonhoz.

    | Megjelenített név      | Szemantikai típus | Séma |
    | ----------------- | ------------- | ------ |
    | Utolsó szervizelés dátuma | None          | Dátum   |
    | Ügyfél neve     | None          | Sztring |

2. Kattintson a **Mentés** gombra.

  
    ![Képernyőkép a környezeti érzékelő sablonról, a Mentés kiemelve](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Testreszabások hozzáadása

A testreszabások segítségével módosíthatja a felületet, vagy IoT Central-specifikus funkciókat adhat hozzá olyan képességekhez, amelyek nem igénylik az eszköz-képességi modell verziójának megadását. A mezőket testreszabhatja, ha a képesség modell Piszkozat vagy közzétett állapotban van. Csak olyan mezőket szabhat testre, amelyek nem bontják le az illesztőfelületek kompatibilitását. Megteheti például a következőket:

- Testreszabhatja egy képesség megjelenített nevét és egységeit.
- Adja meg az alapértelmezett színt, amelyet akkor kell használni, ha az érték megjelenik a diagramon.
- Egy tulajdonság kezdeti, minimális és maximális értékének megadása.

Nem szabhatja testre a képesség nevét vagy a képesség típusát.

Ha végzett a testreszabással, válassza a **Mentés**lehetőséget.
  
![Képernyőkép a környezeti érzékelő sablonjának testreszabási oldaláról](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Nézetek létrehozása

Építőként testreszabhatja az alkalmazást, hogy a megfelelő információkat jelenítse meg a környezeti érzékelő eszközéről egy operátorra. A testreszabások lehetővé teszik, hogy az operátor felügyelje az alkalmazáshoz kapcsolódó környezeti érzékelők eszközeit. Kétféle nézetet hozhat létre az operátorok számára az eszközök használatához:

* Az eszközök és a felhő tulajdonságainak megtekintésére és szerkesztésére szolgáló űrlapok.
* Irányítópultok az eszközök megjelenítéséhez.

### <a name="configure-a-view-to-visualize-devices"></a>Nézet konfigurálása eszközök megjelenítéséhez

Az eszközök irányítópultja lehetővé teszi, hogy az operátor diagramok és metrikák használatával megjelenítse az eszközt. A Builder segítségével meghatározhatja, hogy milyen adatok jelenjenek meg az eszköz irányítópultján. Több irányítópultot is megadhat az eszközökhöz. Ha irányítópultot szeretne létrehozni a környezeti érzékelők telemetria megjelenítéséhez, válassza a **nézetek** > **az eszköz megjelenítését**:

  
![Képernyőfelvétel a környezeti érzékelők sablonjának nézeteiről lap, az eszköz kiemelésével](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


A környezeti telemetria és a gépi telemetria összetett objektumok. Diagramok létrehozása:

1. Húzza a **környezeti telemetria**, és válassza a **vonal diagram**lehetőséget. 
  
   ![Képernyőkép – a környezeti telemetria és a vonalas diagram kiemelése](./media/tutorial-define-edge-device-type/sensorambientchart.png)

1. Válassza a konfigurálás ikont. Válassza a **hőmérséklet** és a **páratartalom** lehetőséget az adatgyűjtés megjelenítéséhez, majd válassza a **konfiguráció frissítése**lehetőséget. 
  
   ![Képernyőfelvétel a környezeti érzékelő sablonról, különböző lehetőségekkel](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

1. Kattintson a **Mentés** gombra.

További csempéket is hozzáadhat, amelyek további tulajdonságokat vagy telemetria értékeket jelenítenek meg. Hozzáadhat statikus szöveget, hivatkozásokat és képeket is. Az irányítópulton lévő csempe áthelyezéséhez vagy átméretezéséhez vigye az egérmutatót a csempe fölé, és húzza a csempét egy új helyre, vagy méretezze át.
  
![Képernyőfelvétel a környezeti érzékelő sablonjának irányítópult nézetéről](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Eszköz űrlapjának hozzáadása

Az eszközök űrlapja lehetővé teszi, hogy az operátor az írható eszköz tulajdonságait és a felhő tulajdonságait szerkessze. Építőként több űrlapot is meghatározhat, és kiválaszthatja, hogy mely eszközök és felhő-tulajdonságok jelenjenek meg az egyes űrlapokon. Az űrlap írásvédett eszközének tulajdonságait is megjelenítheti.

Űrlap létrehozása a környezeti érzékelő tulajdonságainak megtekintéséhez és szerkesztéséhez:

1. A **környezeti érzékelő sablonjában**lépjen a **nézetek**elemre. Új nézet hozzáadásához válassza az **eszköz és a Felhőbeli** adatcsempe szerkesztése lehetőséget.
  
   ![Képernyőfelvétel a környezeti érzékelők sablonjának nézeteiről lap, az eszköz és a Felhőbeli adatmennyiség szerkesztésével](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

1. Adja meg az űrlap neve **környezeti érzékelő tulajdonságait**.

1. Húzza az **ügyfél neve** és az **utolsó szolgáltatás dátumának** Felhőbeli tulajdonságai elemet az űrlap meglévő szakaszára.
  
   ![Képernyőfelvétel a környezeti érzékelők sablonjának nézeteiről lap, különböző lehetőségekkel](./media/tutorial-define-edge-device-type/views-properties.png)

1. Kattintson a **Mentés** gombra.

## <a name="publish-a-device-template"></a>Eszköz sablonjának közzététele

Szimulált környezeti érzékelő létrehozása vagy valódi környezeti érzékelő csatlakoztatása előtt közzé kell tennie az eszköz sablonját.

Eszköz sablonjának közzététele:

1. Nyissa meg az eszköz sablonját az **eszközök sablonjai** lapon.

2. Kattintson a **Publish** (Közzététel) elemre.
  
    ![Képernyőkép a környezeti érzékelő sablonról, a közzététel kiemelve](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. Az **eszköz sablonjának közzététele** párbeszédpanelen válassza a **Közzététel**lehetőséget.
  
    ![Képernyőkép a sablon közzététele párbeszédpanelről, Kiemelt Közzétételsel](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Egy sablon közzététele után az eszköz megjelenik az **eszközök** lapon és a kezelőben. Egy közzétett eszköz sablonjában nem szerkesztheti az eszköz képességeinek modelljét új verzió létrehozása nélkül. A közzétett eszköz sablonjában azonban frissítheti a felhő tulajdonságait, testreszabásait és nézeteit. Ezek a frissítések nem okozzák új verzió létrehozását. A módosítások elvégzése után válassza a **Közzététel** lehetőséget a módosítások elküldéséhez az operátornak.
  
![A közzétett sablonok eszköz-sablonok listájának képernyőképe](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Új Edge létrehozása levélként szolgáló eszköz sablonként.
* Modulok létrehozása feltöltött üzembe helyezési jegyzékből.
* Komplex típusú telemetria és tulajdonságokat adhat hozzá.
* Felhő tulajdonságainak létrehozása
* Testreszabások létrehozása.
* Adjon meg egy vizualizációt az eszköz telemetria.
* Tegye közzé az Edge-eszköz sablonját.

Most, hogy létrehozott egy eszközt az Azure IoT Central-alkalmazásban, ezt a következő lépésekkel végezheti el:

> [!div class="nextstepaction"]
> [Eszköz csatlakoztatása](./tutorial-connect-pnp-device.md)
