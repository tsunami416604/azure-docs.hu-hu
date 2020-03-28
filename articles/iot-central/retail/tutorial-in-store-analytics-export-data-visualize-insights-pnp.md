---
title: Oktatóanyag – Adatok exportálása és elemzések megjelenítése az Azure IoT Centralban
description: Ebben az oktatóanyagban megtudhatja, hogyan exportálhat adatokat az IoT Centralból, és hogyan jelenítheti meg az elemzéseket egy Power BI-irányítópulton.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: 9dcb185ab8375d46c75a12e6adaeeae2358c13ac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77022086"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Oktatóanyag: Adatok exportálása az Azure IoT Centralból és elemzési adatok megjelenítése a Power BI-ban



Az előző két oktatóanyagban létrehozott és testreszabott egy IoT Central alkalmazást az **in-store analytics - checkout** alkalmazássablon használatával. Ebben az oktatóanyagban konfigurálja az IoT Central-alkalmazást az eszközökről gyűjtött telemetriai adatok exportálására. Ezután a Power BI segítségével hozzon létre egy egyéni irányítópultot az üzletkezelő számára a telemetriai adatokból származó elemzések megjelenítéséhez.

Az oktatóanyag során a következőket fogja elsajátítani:
> [!div class="checklist"]
> * Konfigurálja az IoT Központi alkalmazást telemetriai adatok exportálásához egy eseményközpontba.
> * A Logic Apps segítségével adatokat küldhet egy eseményközpontból egy Power BI streamelési adatkészletbe.
> * Hozzon létre egy Power BI-irányítópultot a streamelési adatkészlet ben lévő adatok megjelenítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Az előző két oktatóanyag befejezéséhez [hozzon létre egy üzleten belüli elemzési alkalmazást az Azure IoT Centralban,](./tutorial-in-store-analytics-create-app-pnp.md) és [szabja testre az operátorirányítópultot, és kezelje az eszközöket az Azure IoT Centralban.](./tutorial-in-store-analytics-customize-dashboard-pnp.md)
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.
* Egy Power BI-fiók. Ha nem rendelkezik Power BI-fiókkal, a kezdés előtt regisztráljon egy [ingyenes Power BI Pro-próbaverzióra.](https://app.powerbi.com/signupredirect?pbi_source=web)

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az eseményközpont és a logikai alkalmazás létrehozása előtt létre kell hoznia egy erőforráscsoportot azok kezeléséhez. Az erőforráscsoportnak ugyanazon a helyen kell lennie, mint az **in-store analytics - pénztár** IoT Central alkalmazás. Erőforráscsoport létrehozása:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldali navigációs sávon válassza az **Erőforráscsoportok**lehetőséget. Ezután válassza **a Hozzáadás**lehetőséget.
1. **Az Előfizetés**hez válassza ki az IoT Central alkalmazás létrehozásához használt Azure-előfizetés nevét.
1. Az **Erőforráscsoport** nevéhez adja meg a _kiskereskedelmi üzletelemzés_*.
1. A **régió**ban válassza ki ugyanazt a régiót, amelyet az IoT Central alkalmazáshoz választott.
1. Válassza **a Véleményezés + Létrehozás lehetőséget.**
1. A **Véleményezés + Létrehozás** lapon válassza a **Létrehozás lehetőséget.**

Most már rendelkezik egy **kiskereskedelmi-áruház-elemzés** nevű erőforráscsoporttal az előfizetésében.

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Mielőtt konfigurálhatja a kiskereskedelmi figyelési alkalmazást a telemetriai adatok exportálására, létre kell hoznia egy eseményközpontot az exportált adatok fogadásához. Az alábbi lépések bemutatják, hogyan hozhatja létre az eseményközpontot:

1. Az Azure Portalon válassza **az erőforrás létrehozása** a képernyő bal felső részén.
1. A **Keresés a piactéren**mezőbe írja be az _Eseményközpontok kifejezést,_ majd nyomja **le az Enter billentyűt.**
1. Az **Eseményközpontok** lapon válassza a **Létrehozás gombot.**
1. A **Névtér létrehozása** lapon tegye a következő lépéseket:
    * Adjon meg egy egyedi nevet a névtérnek, például _a name-retail-store-analysis_nevet. A rendszer ellenőrzi, hogy ez a név elérhető-e.
    * Válassza ki az **alapszintű** tarifacsomagot.
    * Válassza ki ugyanazt az **előfizetést,** amelyet az IoT Central alkalmazás létrehozásához használt.
    * Válassza ki a **kiskereskedelmi-elemző** erőforráscsoportot.
    * Válassza ki ugyanazt a helyet, amelyet az IoT Central alkalmazáshoz használt.
    * Kattintson a **Létrehozás** gombra. Előfordulhat, hogy várnia kell néhány percet, amíg a rendszer kiépíti az erőforrásokat.
1. A portálon keresse meg a **kiskereskedelmi-áruház-elemzés** erőforráscsoportot. Várjon, amíg az üzembe helyezés befejeződik. Előfordulhat, hogy a telepítési állapot frissítéséhez válassza a **Frissítés** lehetőséget. Az eseményközpont névterének létrehozását az **Értesítések**területen is ellenőrizheti.
1. A **retail-store-analysis** erőforráscsoportban válassza az **Eseményközpontok névterét.** Az **Eseményközpontok névterének** kezdőlapja látható a portálon.

Most már rendelkezik egy **Event Hubs névtérrel,** létrehozhat egy **Event Hub-ot** az IoT Central alkalmazással való használatra:

1. A portálon az **Eseményközpontok névterének** kezdőlapján válassza a **+ Event Hub**lehetőséget.
1. Az **Event Hub létrehozása** lapon adja meg az _üzlettelemetriát_ névként, majd válassza a **Létrehozás lehetőséget.**

Most már rendelkezik egy eseményközponttal, amelyet az IoT Central alkalmazásból történő adatexportálás konfigurálásakor használhat:

![Eseményközpont](./media/tutorial-in-store-analytics-visualize-insights-pnp/event-hub.png)

## <a name="configure-data-export"></a>Adatexportálás konfigurálása

Most már rendelkezik egy eseményközponttal, konfigurálhatja az **in-store analytics - pénztár** alkalmazás exportálja a telemetriai adatokat a csatlakoztatott eszközökről. A következő lépések bemutatják, hogyan konfigurálható az exportálás:

1. Jelentkezzen be az **in-store analytics - pénztár** IoT Central alkalmazás.
1. Válassza **az Adatexportálás lehetőséget** a bal oldali ablaktáblában.
1. Válassza **az Új > Az Azure Event Hubs lehetőséget.**
1. Adja meg _a Telemetriai exportálást_ **megjelenítendő névként.**
1. Válassza ki az **Event Hubs névterét.**
1. Válassza ki a **tároló-telemetriai** eseményközpont.
1. Kapcsolja ki **az Eszközök** és **eszközsablonokat** az **Adatok exportáláshoz** szakaszban.
1. Kattintson a **Mentés** gombra.

Az adatexportálás néhány percet is igénybe vehet a telemetriai adatok üzenetküldésének megkezdése az eseményközpontba. Az exportálás állapotát az Adatexportálás oldalon **láthatja:**

![Folyamatos adatexportálási konfiguráció](./media/tutorial-in-store-analytics-visualize-insights-pnp/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>A Power BI-adatkészletek létrehozása

A Power BI irányítópultja megjeleníti a kiskereskedelmi figyelőalkalmazás adatait. Ebben a megoldásban a Power BI streamelési adatkészleteit használhatja a Power BI irányítópultjának adatforrásaként. Ebben a szakaszban határozza meg a séma a streamelési adatkészletek, hogy a logikai alkalmazás továbbíthatja az adatokat az eseményközpontból. A következő lépések bemutatják, hogyan hozhat létre két streamelési adatkészletet a környezeti érzékelőkhöz és egy streamelési adatkészletet az foglaltságérzékelőhöz:

1. Jelentkezzen be a **Power BI**-fiókjába.
1. Válassza **a Munkaterületek**lehetőséget, majd a **Munkaterület létrehozása**lehetőséget.
1. A **Munkaterület létrehozása** lapon adja meg az _üzleten belüli elemzést – a kivételt_ **munkaterület névként.**
1. Görgessen az **Áruházon belüli elemzés üdvözlése – pénztár munkaterület** lap aljára, és válassza a **Kihagyás**lehetőséget.
1. A munkaterület lapon válassza a **> Streamelési adatkészlet létrehozása lehetőséget.**
1. Az **Új adatfolyam-adatfolyam-adatkészlet** lapon válassza az **API**lehetőséget, majd a **Tovább**gombot.
1. Írja be az **1.** _Zone 1 sensor_
1. Írja be az **adatfolyam** három értékét a következő táblázatba:

    | Érték neve  | Érték típusa |
    | ----------- | ---------- |
    | Időbélyeg   | DateTime   |
    | Páratartalom    | Szám     |
    | Hőmérséklet | Szám     |

1. Kapcsolja be **a korábbi adatelemzést.**
1. Válassza **a Létrehozás,** majd **a Kész lehetőséget.**
1. Hozzon létre egy másik streamelési **adatkészletet, a Zone 2 érzékelőt** ugyanazzal a sémával és beállításokkal, mint a Zone 1 **érzékelő** streamelési adatkészletét.

Most már két streamelési adatkészletek. A logikai alkalmazás a telemetriai adatokat az **in-store analytics - pénztár** alkalmazáshoz csatlakoztatott két környezeti érzékelőről továbbítja a következő két adatkészlethez:

![Zóna adatkészletei](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-1.png)

Ez a megoldás minden érzékelőhöz egy streamelési adatkészletet használ, mivel nem lehet szűrőket alkalmazni a Power BI-ban lévő adatfolyam-adatokra.

A használtsági telemetriai adatokhoz is szüksége van egy adatfolyam-adatkészletre:

1. A munkaterület lapon válassza a **> Streamelési adatkészlet létrehozása lehetőséget.**
1. Az **Új adatfolyam-adatfolyam-adatkészlet** lapon válassza az **API**lehetőséget, majd a **Tovább**gombot.
1. Az **Adatkészlet neveként**adja meg _a Használtság érzékelőt._
1. Írja be az **adatfolyam** öt értékét a következő táblázatba:

    | Érték neve     | Érték típusa |
    | -------------- | ---------- |
    | Időbélyeg      | DateTime   |
    | Várólista hossza 1 | Szám     |
    | Várólista hossza 2 | Szám     |
    | Lakat idő 1   | Szám     |
    | Lakat idő 2   | Szám     |

1. Kapcsolja be **a korábbi adatelemzést.**
1. Válassza **a Létrehozás,** majd **a Kész lehetőséget.**

Most már rendelkezik egy harmadik streamelési adatkészlettel, amely a szimulált foglaltságérzékelő értékeit tárolja. Ez az érzékelő jelenti a várólista hosszát a két pénztárnál az üzletben, és azt, hogy az ügyfelek mennyi ideig várakoznak ezekben a várólistákban:

![Foglaltsági adatkészlet](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-2.png)

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Ebben a megoldásban a logikai alkalmazás beolvassa a telemetriai adatokat az eseményközpontból, elemzi az adatokat, majd elküldi azokat a létrehozott Power BI streamelési adatkészletek.

A logikai alkalmazás létrehozása előtt szüksége van az IoT Central-alkalmazáshoz csatlakoztatott két RuuviTag-érzékelő eszközazonosítóira az [Azure IoT Central oktatóanyagának létrehozása](./tutorial-in-store-analytics-create-app-pnp.md) című oktatóalkalmazásban:

1. Jelentkezzen be az **in-store analytics - pénztár** IoT Central alkalmazás.
1. Válassza az **Eszközök** lehetőséget a bal oldali ablaktáblában. Ezután válassza **a RuuviTag lehetőséget.**
1. Jegyezze fel az **eszközazonosítókat.** A következő screenshot, az azonosítók **f5dcf4ac32e8** és **e29ffc8d5326**:

    ![Eszközazonosítók](./media/tutorial-in-store-analytics-visualize-insights-pnp/device-ids.png)

A következő lépések bemutatják, hogyan hozhat létre a logikai alkalmazást az Azure Portalon:

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és válassza az **erőforrás létrehozása** a képernyő bal felső részén.
1. A **Keresés a piactéren**mezőbe írja be a _Logikai alkalmazást_, majd nyomja le az Enter **billentyűt.**
1. A **Logikai alkalmazás** lapon válassza a **Létrehozás gombot.**
1. A **Logikai alkalmazás** létrehozási lapján:
    * Adjon meg egy egyedi nevet a logikai alkalmazásnak, például _a name-retail-store-analysis_nevet.
    * Válassza ki ugyanazt az **előfizetést,** amelyet az IoT Central alkalmazás létrehozásához használt.
    * Válassza ki a **kiskereskedelmi-elemző** erőforráscsoportot.
    * Válassza ki ugyanazt a helyet, amelyet az IoT Central alkalmazáshoz használt.
    * Kattintson a **Létrehozás** gombra. Előfordulhat, hogy várnia kell néhány percet, amíg a rendszer kiépíti az erőforrásokat.
1. Az Azure Portalon keresse meg az új logikai alkalmazást.
1. A **Logic Apps Designer** lapon görgessen le, és válassza az **Üres logikai alkalmazás lehetőséget.**
1. A **Keresési összekötők és eseményindítók mezőbe**írja be az _Eseményközpontok kifejezést._
1. Az **Eseményindítók csoportban**válassza a **When events are available (Mikor érhetők el az események az Eseményközpontban)** lehetőséget.
1. Adja meg az _Áruház telemetriai adatait_ **kapcsolatnévként,** és válassza ki az **Eseményközpontok névterét.**
1. Jelölje ki a **RootManageSharedAccess** házirendet, majd kattintson **a Létrehozás gombra.**
1. A **When események érhetők el az Event Hub műveletben:**
    * Az **Event Hub neve**területen válassza az **üzlet-telemetriai adatok**lehetőséget.
    * A **Tartalomtípus**ban válassza az **alkalmazás/json**lehetőséget.
    * Állítsa az **intervallumot** háromra, a **gyakoriságot** pedig másodpercre
1. A logikai alkalmazás mentéséhez válassza a **Mentés** lehetőséget.

Ha hozzá szeretné adni a logikát a logikai alkalmazás tervéhez, válassza a **Kód nézet lehetőséget:**

1. Cserélje `"actions": {},` ki a következő JSON. Cserélje ki a `[YOUR RUUVITAG DEVICE ID 1]` `[YOUR RUUVITAG DEVICE ID 2]` két helyőrzőt és a két RuuviTag eszközazonosítóját:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. Válassza a **Mentés,** majd a **Tervező** lehetőséget a hozzáadott logika vizuális verziójának megtekintéséhez:

    ![Logikai alkalmazás tervezése](./media/tutorial-in-store-analytics-visualize-insights-pnp/logic-app.png)

1. A művelet kibontásához válassza a **Kapcsoló eszközazonosító szerint** lehetőséget. Ezután válassza **az 1.** **Add an action**
1. A **Keresés összekötők és műveletek**mezőbe írja be a Power **BI**, majd nyomja le az **Enter billentyűt.**
1. Jelölje ki a **Sorok hozzáadása adatkészlethez (előnézet)** műveletet.
1. Válassza **a Bejelentkezés** lehetőséget, és kövesse az utasításokat a Power BI-fiókba való bejelentkezéshez.
1. A bejelentkezési folyamat befejezése után a **Sorok hozzáadása adatkészlethez** műveletben:
    * Válassza az **Üzleten belüli elemzés - pénztár,** mint a munkaterület.
    * Adatkészletként válassza az **1.**
    * Válaszd a **RealTimeData táblát.**
    * Válassza **az Új paraméter hozzáadása** lehetőséget, majd jelölje be az **Időbélyeg**, **A páratartalom**és a **Hőmérséklet** mezőt.
    * Jelölje ki az **Időbélyeg** mezőt, majd válassza az **x-opt-enqueuedtime** lehetőséget a **Dinamikus tartalom** listából.
    * Jelölje be a **Páratartalom** mezőt, majd válassza a **Tovább lehetőséget** az **Elemzési telemetria ielemetási mező**mellett. Ezután válassza a **páratartalmat**.
    * Jelölje be a **Hőmérséklet** mezőt, majd válassza a **Tovább pont** lehetőséget az **Elemzési telemetria ielemtria**mellett. Ezután válassza ki a **hőmérsékletet**.
    * A módosítások mentéséhez válassza a **Mentés** gombot. A **Zone 1 környezeti** művelet a ![következő képernyőképhez hasonlóan néz ki: 1. zóna környezet](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-1-action.png)
1. Jelölje ki a **Zone 2 környezeti** műveletet, és válassza a Művelet **hozzáadása**lehetőséget.
1. A **Keresés összekötők és műveletek**mezőbe írja be a Power **BI**, majd nyomja le az **Enter billentyűt.**
1. Jelölje ki a **Sorok hozzáadása adatkészlethez (előnézet)** műveletet.
1. A **Sorok hozzáadása adatkészlethez 2** műveletben:
    * Válassza az **Üzleten belüli elemzés - pénztár,** mint a munkaterület.
    * Adatkészletként válassza a **2- es zónaérzékelőt.**
    * Válaszd a **RealTimeData táblát.**
    * Válassza **az Új paraméter hozzáadása** lehetőséget, majd jelölje be az **Időbélyeg**, **A páratartalom**és a **Hőmérséklet** mezőt.
    * Jelölje ki az **Időbélyeg** mezőt, majd válassza az **x-opt-enqueuedtime** lehetőséget a **Dinamikus tartalom** listából.
    * Jelölje be a **Páratartalom** mezőt, majd válassza a **Tovább lehetőséget** az **Elemzési telemetria ielemetási mező**mellett. Ezután válassza a **páratartalmat**.
    * Jelölje be a **Hőmérséklet** mezőt, majd válassza a **Tovább pont** lehetőséget az **Elemzési telemetria ielemtria**mellett. Ezután válassza ki a **hőmérsékletet**.
    A módosítások mentéséhez válassza a **Mentés** gombot.  A **Zone 2 környezeti** művelet a ![következő képernyőképre néz ki: 2. zóna környezet](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-2-action.png)
1. Jelölje ki a **Használtság** műveletet, majd válassza a **Váltás felületazonosító szerint** műveletet.
1. Jelölje ki a **Dwell Time felület** műveletet, majd a **Művelet hozzáadása**lehetőséget.
1. A **Keresés összekötők és műveletek**mezőbe írja be a Power **BI**, majd nyomja le az **Enter billentyűt.**
1. Jelölje ki a **Sorok hozzáadása adatkészlethez (előnézet)** műveletet.
1. A **Sorok hozzáadása adatkészlethez** műveletben:
    * Válassza az **Üzleten belüli elemzés - pénztár,** mint a munkaterület.
    * Az adatkészletként válassza az **Occupancy Sensor** lehetőséget.
    * Válaszd a **RealTimeData táblát.**
    * Válassza **az Új paraméter hozzáadása lehetőséget,** majd válassza az **Időbélyeg**, **A dagadt idő 1**és a **Dwell Time 2** mezőt.
    * Jelölje ki az **Időbélyeg** mezőt, majd válassza az **x-opt-enqueuedtime** lehetőséget a **Dinamikus tartalom** listából.
    * Jelölje be a **Dwell Time 1** mezőt, majd válassza a **Tovább lehetőséget** az **Elemzési telemetriai adatok**mellett. Ezután válassza **a DwellTime1 lehetőséget.**
    * Jelölje be a **Dwell Time 2** mezőt, majd válassza a **Tovább lehetőséget** az **Elemzési telemetriai adatok**mellett. Ezután válassza **a DwellTime2 lehetőséget.**
    * A módosítások mentéséhez válassza a **Mentés** gombot. A **Dwell Time felület** művelet a ![következő képernyőképhez hasonlóan néz ki: Kihasználtsági művelet](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-1.png)
1. Jelölje ki a **Személyek száma felületet,** és válassza **a Művelet hozzáadása**lehetőséget.
1. A **Keresés összekötők és műveletek**mezőbe írja be a Power **BI**, majd nyomja le az **Enter billentyűt.**
1. Jelölje ki a **Sorok hozzáadása adatkészlethez (előnézet)** műveletet.
1. A **Sorok hozzáadása adatkészlethez** műveletben:
    * Válassza az **Üzleten belüli elemzés - pénztár,** mint a munkaterület.
    * Az adatkészletként válassza az **Occupancy Sensor** lehetőséget.
    * Válaszd a **RealTimeData táblát.**
    * Válassza **az Új paraméter hozzáadása lehetőséget,** majd válassza az **Időbélyeg**, **A várólista hossza 1**és a **Várólista hossza 2** mezőt.
    * Jelölje ki az **Időbélyeg** mezőt, majd válassza az **x-opt-enqueuedtime** lehetőséget a **Dinamikus tartalom** listából.
    * Jelölje be a **Várólista hossza 1** mezőt, majd válassza a **Tovább lehetőséget** az **Elemzési telemetria ielemetási mező**mellett. Ezután válassza **a darabszám1**lehetőséget.
    * Jelölje be a **Várólista hossza 2** mezőt, majd válassza a **Tovább lehetőséget** az **Elemzési telemetria ielemetási mező**mellett. Ezután válassza **a darabszám2**lehetőséget.
    * A módosítások mentéséhez válassza a **Mentés** gombot. A **Személyek száma felület** művelet a ![következő képernyőképhez hasonlóan néz ki: Kihasználtsági művelet](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-2.png)

A logikai alkalmazás automatikusan fut. Az egyes futtatások állapotának megtekintéséhez keresse meg a logikai alkalmazás **áttekintése** lapot az Azure Portalon:

## <a name="create-a-power-bi-dashboard"></a>Power BI-irányítópult létrehozása

Most az IoT Central-alkalmazásból az eseményközponton keresztül áramló telemetriai adatokkal rendelkezik. Ezután a logikai alkalmazás elemzi az eseményközpont-üzeneteket, és hozzáadja őket egy Power BI streamelési adatkészlethez. Most létrehozhat egy Power BI-irányítópultot a telemetriai adatok megjelenítéséhez:

1. Jelentkezzen be a **Power BI**-fiókjába.
1. Válassza **a Workspaces > In-store analytics – checkout lehetőséget.**
1. Válassza **> irányítópult létrehozása**lehetőséget.
1. Adja meg az **Áruház-elemzést** irányítópult névként, és válassza a **Létrehozás gombot.**

### <a name="add-line-charts"></a>Vonaldiagramok hozzáadása

Adjon hozzá négy vonaldiagram-csempét a két környezeti érzékelő hőmérsékletének és páratartalmának megjelenítéséhez. A csempék létrehozásához használja az alábbi táblázatban található információkat. Az egyes csempék hozzáadásához először válassza ki **a ... (További lehetőségek) > Mozaik hozzáadása**. Válassza **az Egyéni adatfolyam-adatok**lehetőséget , majd a **Tovább**gombot.

| Beállítás | Diagram #1 | Diagram #2 | Diagram #3 | Diagram #4 |
| ------- | -------- | -------- | -------- | -------- |
| Adatkészlet | 1. zóna érzékelő | 1. zóna érzékelő | 2. zóna érzékelő | 2. zóna érzékelő |
| Képi megjelenítés típusa | Vonaldiagram | Vonaldiagram | Vonaldiagram | Vonaldiagram |
| Tengely | Időbélyeg | Időbélyeg | Időbélyeg | Időbélyeg |
| Értékek | Hőmérséklet | Páratartalom | Hőmérséklet | Páratartalom |
| Időablak | 60 perc | 60 perc | 60 perc | 60 perc |
| Cím | Hőmérséklet (1 óra) | Páratartalom (1 óra) | Hőmérséklet (1 óra) | Páratartalom (1 óra) |
| Alcím | 1. zóna | 1. zóna | 2. zóna | 2. zóna |

A következő képernyőkép az első diagram beállításait mutatja be:

![Vonaldiagram beállításai](./media/tutorial-in-store-analytics-visualize-insights-pnp/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Kártyák hozzáadása a környezeti adatok megjelenítéséhez

Adjon hozzá négy kártyacsempét a két környezeti érzékelő legfrissebb hőmérséklet- és páratartalom-értékeinek megjelenítéséhez. A csempék létrehozásához használja az alábbi táblázatban található információkat. Az egyes csempék hozzáadásához először válassza ki **a ... (További lehetőségek) > Mozaik hozzáadása**. Válassza **az Egyéni adatfolyam-adatok**lehetőséget , majd a **Tovább**gombot.

| Beállítás | Kártya #1 | Kártya #2 | Kártya #3 | Kártya #4 |
| ------- | ------- | ------- | ------- | ------- |
| Adatkészlet | 1. zóna érzékelő | 1. zóna érzékelő | 2. zóna érzékelő | 2. zóna érzékelő |
| Képi megjelenítés típusa | Kártya | Kártya | Kártya | Kártya |
| Mezők | Hőmérséklet | Páratartalom | Hőmérséklet | Páratartalom |
| Cím | Hőmérséklet (F) | Páratartalom (%) | Hőmérséklet (F) | Páratartalom (%) |
| Alcím | 1. zóna | 1. zóna | 2. zóna | 2. zóna |

A következő képernyőkép az első kártya beállításait mutatja be:

![Kártyabeállítások](./media/tutorial-in-store-analytics-visualize-insights-pnp/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Csempék hozzáadása a kijelentkezési foglaltsági adatok megjelenítéséhez

Adjon hozzá négy kártyacsempét, hogy megjelenítse a várólista hosszát és a két pénztár idejét az üzletben. A csempék létrehozásához használja az alábbi táblázatban található információkat. Az egyes csempék hozzáadásához először válassza ki **a ... (További lehetőségek) > Mozaik hozzáadása**. Válassza **az Egyéni adatfolyam-adatok**lehetőséget , majd a **Tovább**gombot.

| Beállítás | Kártya #1 | Kártya #2 | Kártya #3 | Kártya #4 |
| ------- | ------- | ------- | ------- | ------- |
| Adatkészlet | Kihasználtságérzékelő | Kihasználtságérzékelő | Kihasználtságérzékelő | Kihasználtságérzékelő |
| Képi megjelenítés típusa | Fürtözött oszlopdiagram | Fürtözött oszlopdiagram | Kijelző | Kijelző |
| Tengely    | Időbélyeg | Időbélyeg | N/A | N/A |
| Érték | Lakat idő 1 | Lakat idő 2 | Várólista hossza 1 | Várólista hossza 2 |
| Időablak | 60 perc | 60 perc |  N/A | N/A |
| Cím | Dwell idő | Dwell idő | Processzor-várólista hossza | Processzor-várólista hossza |
| Alcím | Pénztár 1 | Pénztár 2 | Pénztár 1 | Pénztár 2 |

Az irányítópult csempéinek átméretezése és átrendezése a következő képernyőképhez hasonlóan:

![Power BI-irányítópult](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard.png)

Az irányítópult további testreszabásához hozzáadhat néhány további grafikus erőforrást:

![Power BI-irányítópult](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte az IoT Central alkalmazást, törölheti azt az alkalmazásba bejelentkezve, és a **Felügyelet** szakasz **Alkalmazásbeállítások** lapjára navigálva.

Ha meg szeretné tartani az alkalmazást, de csökkenteni szeretné a vele kapcsolatos költségeket, tiltsa le az adatexportálást, amely telemetriai adatokat küld az eseményközpontba.

Törölheti az eseményközpontot és a logikai alkalmazást az Azure **Portalon a retail-store-analysis**nevű erőforráscsoport törlésével.

A Power BI-adatkészletek és az irányítópult törléséhez törölje a munkaterületet a munkaterület Power BI-beállítások lapjáról.

## <a name="next-steps"></a>Következő lépések

Ez a három oktatóanyag egy végpontok között megoldást mutatott be, amely az **in-store analytics - checkout** IoT Central alkalmazássablont használja. Eszközöket csatlakoztatott az alkalmazáshoz, az IoT Central tap-ját használta az eszközök figyelésére, és a Power BI segítségével irányítópultot hozhat létre az eszköz telemetriai adatainak megtekintéséhez. A következő ajánlott lépés az IoT Central alkalmazássablonok egyikének feltárása:

> [!div class="nextstepaction"]
> * [Energiaipari megoldások létrehozása az IoT Centrallal](../energy/overview-iot-central-energy.md)
> * [Kormányzati megoldások létrehozása az IoT Centrallal](../government/overview-iot-central-government.md)
> * [Egészségügyi megoldások létrehozása az IoT Centrallal](../healthcare/overview-iot-central-healthcare.md)
