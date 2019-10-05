---
title: Exportálja adatait az Azure Blob Storageba | Microsoft Docs
description: Adatok exportálása az Azure IoT Central alkalmazásból az Azure-ba Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 9ac650273a53da715b89e3233b30cf50710cfcfd
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973261"
---
# <a name="export-your-data-to-azure-blob-storage-preview-features"></a>Exportálja adatait az Azure Blob Storageba (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Ez a témakör a rendszergazdákra vonatkozik.*

Ez a cikk azt ismerteti, hogyan használható az Azure IoT Central folyamatos adatexportálás funkciója az **Azure Blob Storage-fiókba** vagy **Azure Data Lake Storage Gen2 Storage-fiókba**való rendszeres adatexportáláshoz. **Telemetria**, **eszközöket** **és eszközöket is exportálhatunk** JSON formátumú fájlokra. Az exportált adatokat felhasználhatja a ritkán használt módszerekhez, például a Azure Machine Learning vagy a Microsoft Power BI hosszú távú trendek elemzéséhez.

> [!Note]
> Ha bekapcsolja a folyamatos adatexportálást, a rendszer csak az adott pillanattól kezdve kapja meg az adott adatot. Jelenleg nem lehet lekérni az adatgyűjtési időt, amikor a folyamatos adatexportálás ki lett kapcsolva. Több korábbi adat megtartásához kapcsolja be a folyamatos adatexportálást.


## <a name="prerequisites"></a>Előfeltételek

- A IoT Central-alkalmazásban rendszergazdának kell lennie

## <a name="create-storage-account"></a>Storage-fiók létrehozása
Ha nem rendelkezik meglévő tárolóval az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Storage-fiókot a Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). További információ: új [Azure Blob Storage-fiókok](https://aka.ms/blobdocscreatestorageaccount) létrehozása vagy [Azure Data Lake Storage v2 Storage-fiókok](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

    > [!Note] 
    > Ha **úgy dönt,** hogy ADLS v2 Storage-fiókba exportálja az adatexportálást, akkor a **BlobStorage**lehetőséget kell választania. 

    > [!Note] 
    > Az egyes előfizetésekhez tartozó Storage-fiókokba exportálhatja az adatait, mint az utólagos elszámolású IoT Central alkalmazás esetében. Ebben az esetben kapcsolati sztringet fog használni.

2. Hozzon létre egy tárolót a Storage-fiókban. Nyissa meg a Storage-fiókját. A **blob szolgáltatás**alatt válassza a **Tallózás Blobok**lehetőséget. Egy új tároló létrehozásához kattintson a felül található **+ tároló** elemre.


## <a name="set-up-continuous-data-export"></a>Folyamatos adatexportálás beállítása

Most, hogy van egy tárolási célhelye az adatexportáláshoz, kövesse az alábbi lépéseket a folyamatos adatexportálás beállításához. 

1. Jelentkezzen be IoT Central alkalmazásba.

2. A bal oldali menüben válassza az **adatexportálás**elemet.

    > [!Note]
    > Ha nem látja az adatexportálást a bal oldali menüben, nem Ön az alkalmazás rendszergazdája. Az adatexportálás beállításához forduljon a rendszergazdához.

3. Kattintson a jobb felső sarokban található **+ új** gombra. Válassza az **Azure Blob Storage** lehetőséget az Exportálás célhelye. 

    > [!NOTE] 
    > Az alkalmazások exportálásának maximális száma öt. 

    ![Új folyamatos adatexportálás létrehozása](media/howto-export-data-pnp/export-new2.png)

4. A legördülő listában válassza ki a **Storage-fiók névterét**. A lista utolsó elemét is kiválaszthatja, amely a **kapcsolatok karakterláncát adja meg**. 

    > [!NOTE] 
    > A Storage-fiókok névtereit a **IoT Central alkalmazással megegyező előfizetésben**fogja látni. Ha az előfizetésen kívüli célhelyre szeretne exportálni, válassza **az adja meg a kapcsolati karakterláncot** , és tekintse meg az 5. lépést.

    > [!NOTE] 
    > A 7 napos próbaverziós alkalmazások esetében az egyetlen módszer a folyamatos adatexportálás konfigurálására egy kapcsolódási karakterláncon keresztül. Ennek az az oka, hogy a 7 napos próbaverziós alkalmazások nem rendelkeznek társított Azure-előfizetéssel.

    ![Új Exportálás létrehozása a Blobba](media/howto-export-data-pnp/export-create-blob2.png)

5. Választható Ha a **kapcsolódási karakterlánc megadása**lehetőséget választotta, a rendszer egy új mezőt jelenít meg a kapcsolódási karakterlánc beillesztéséhez. A Storage-fiókhoz tartozó kapcsolódási karakterlánc beszerzéséhez nyissa meg a Azure Portal Storage-fiókját:
    - A **Beállítások**területen válassza a **hozzáférési kulcsok** elemet.
    - Másolja a key1-vagy a key2-kapcsolatok karakterláncát
 
6. Válasszon egy tárolót a legördülő listából. Ha nem rendelkezik tárolóval, nyissa meg a Storage-fiókját a Azure Portalban:
    - A **blob Service**alatt válassza a **Blobok**elemet. Kattintson a **+ tároló** elemre, és adja meg a tároló nevét. Válasszon egy nyilvános hozzáférési szintet az adataihoz (a folyamatos adatexportálással együtt). További információ az [Azure Storage docs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)-ról.

7.  Az **exportálni kívánt adat**területen adja meg az exportálandó adattípusokat **, ha a**típust be értékre állítja.
   
    > [!NOTE] 
    > Az adatexportálás JSON formátumban történik.

8. A folyamatos adatexportálás bekapcsolásához győződjön meg arról, hogy az **adatexportálási** váltógomb be van kapcsolva. Kattintson a **Mentés** gombra.

   ![Folyamatos adatexportálás konfigurálása](media/howto-export-data-pnp/export-list-blob2.png)

9. Néhány perc elteltével az adatai megjelennek a Storage-fiókban.


## <a name="path-structure"></a>Elérési út szerkezete

A rendszer percenként egyszer exportálja a telemetria, az eszközöket és az eszközök sablonjait a Storage-fiókba, és minden olyan fájllal, amely a legutóbbi exportált fájl óta módosul. Az exportált adatfájlok JSON formátumú három mappába kerülnek. A Storage-fiók alapértelmezett elérési útjai a következők:
- Telemetria: {Container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Eszközök: {Container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Eszközök sablonjai: {Container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

Az exportált fájlok tallózásával tallózhat a Azure Portalban, ha a fájlra navigál, és a **blob szerkesztése** lapot választja.

## <a name="data-format"></a>Adatformátum
### <a name="telemetry"></a>Telemetria

Az exportált telemetria-adatok minden olyan új üzenettel rendelkeznek, amelyet az összes eszköz IoT Central fogadott az adott idő alatt. Az exportált fájlok ugyanazt a formátumot használják, mint a blob Storage-ba [IoT hub üzenet-útválasztás](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) által exportált üzenet-fájlok.

> [!NOTE]
> Győződjön meg arról, hogy az eszközök olyan üzeneteket küldenek, amelyek `contentType: application/JSON` és `contentEncoding:utf-8` (vagy `utf-16`, `utf-32`) rendelkeznek. Példaként tekintse meg [IoT hub dokumentációját](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body) .

> [!NOTE]
> A telemetria küldő eszközöket az eszközök azonosítói jelölik (lásd a következő részeket). Az eszközök nevének lekéréséhez exportálja az eszköz pillanatképeit. Az egyes üzeneteket az **connectionDeviceId** megegyező, az eszközhöz **tartozó rekordra vonatkozó** adatokkal korrelálhatja.

A következő példa JSON formátumú rekordot mutat be.

```json
{ 
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{ 

  },
  "SystemProperties":{ 
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{ 
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

### <a name="devices"></a>Eszközök

Ha a folyamatos adatexportálás be van kapcsolva, az összes eszközön egyetlen pillanatkép lesz exportálva. Minden eszköz a következőket tartalmazza:
- @no__t – az eszköz 0 IoT Central
- az eszköz @no__t – 0
- @no__t – 0 a [Device kiépítési szolgáltatásból](https://aka.ms/iotcentraldocsdps)
- Eszköz sablonjának adatai
- Tulajdonságok értékei

Az új Pillanatképek percenként egyszer írhatók. A pillanatkép a következőket tartalmazza:

- Új eszközök lettek hozzáadva a legutóbbi pillanatkép óta.
- Az utolsó pillanatkép óta megváltozott tulajdonságértékeket tartalmazó eszközök.

> [!NOTE]
> A legutóbbi pillanatkép óta törölt eszközök nem lesznek exportálva. A pillanatképek jelenleg nem rendelkeznek kijelzővel a törölt eszközökhöz.
>
> Az eszközhöz tartozó sablon, amely az egyes eszközökhöz tartozik, a `instanceOf` mező jelöli. Az eszköz sablonjának lekéréséhez exportálja az eszköz sablonjának pillanatképeit.

Az exportált fájlok rekordokban egyetlen sort tartalmaznak. A következő példa JSON formátumú rekordot mutat be.

```json
{ 
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{ 
    "$cloudProperties":{ 
        "Color":"blue"
    },
    "EnvironmentalSensor":{ 
      "thsensormodel":{ 
        "reported":{ 
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{ 
        "reported":{ 
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{ 
      "totalStorage":{ 
        "reported":{ 
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{ 
        "reported":{ 
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates"></a>Eszközsablonok

Ha a folyamatos adatexportálás be van kapcsolva, az összes eszköz sablonja egyetlen pillanatképet exportál. Minden eszköz sablonja a következőket tartalmazza:
- @no__t – 0 az eszköz sablonja
- @no__t – 0 az eszköz sablonja
- @no__t – 0 az eszköz sablonja
- A `capabilityModel` eszköz, beleértve a `interfaces`, valamint a telemetria, a tulajdonságok és a parancsok definícióit
- @no__t – 0 definíció
- Felülbírálások és kezdeti értékek, a `capabilityModel` vonallal

Az új Pillanatképek percenként egyszer írhatók. A pillanatkép a következőket tartalmazza:

- Új, a legutóbbi pillanatkép óta hozzáadott eszközök. Ide tartoznak az eszközök sablonjainak új verziói.
- A legutóbbi pillanatkép óta módosult felülbírálásokkal, kezdeti értékekkel és a felhő tulajdonságaival rendelkező eszközök sablonjai.

> [!NOTE]
> A legutóbbi pillanatkép óta törölt eszközök sablonjai nem lesznek exportálva. Jelenleg a pillanatképek nem rendelkeznek kijelzővel a törölt eszközök sablonjaihoz.

Az exportált fájlok rekordokban egyetlen sort tartalmaznak. A következő példa JSON formátumú rekordot mutat be.

```json
{ 
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{ 
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[ 
      { 
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{ 
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[ 
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{ 
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{ 
                  "@value":"50"
                }
              },
              "visualizationDetail":{ 
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      { 
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{ 
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[ 
            { 
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{ 
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[ 
      { 
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{ 
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{ 
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>További lépések

Most, hogy már tudja, hogyan exportálhatja az adatait, folytassa a következő lépéssel:

> [!div class="nextstepaction"]
> [A IoT Central Device Bridge használata más IoT-felhők csatlakoztatásához](howto-build-iotc-device-bridge.md)
