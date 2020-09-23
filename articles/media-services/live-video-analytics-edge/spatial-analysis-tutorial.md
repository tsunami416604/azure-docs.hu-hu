---
title: Élő videók elemzése Computer Vision térbeli elemzéshez – Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Live Video Analytics szolgáltatást az Azure Cognitive Services Computer Vision térbeli Analysis AI funkciójának használatával egy élő videó-hírcsatorna (szimulált) IP-kamerából való elemzéséhez.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 98ee57d4916ac0a8da8b48a9cdd881468b2d75d5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946605"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Élő videó elemzése a Computer Vision for térbeli Analysis (előzetes verzió)

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az élő videók elemzését [Computer Vision az Cognitive Services Azure-beli térbeli elemzési AI szolgáltatáshoz](https://azure.microsoft.com/services/cognitive-services/computer-vision/) , hogy elemezze a (szimulált) IP-kamera élő videós csatornáját. Láthatja, hogy ez a következtetési kiszolgáló hogyan elemezheti a streaming videót, hogy megértse az emberek és a fizikai térben való mozgás közötti térbeli kapcsolatokat.  A rendszer elküldi a videó-hírcsatornában található keretek egy részhalmazát, és az eredményeket elküldjük IoT Edge hubhoz, és bizonyos feltételek teljesülése esetén a videoklipek rögzítése és tárolása Azure Media Services eszközként történik.

Ebben az oktatóanyagban a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Erőforrások beállítása.
> * Vizsgálja meg a kódot.
> * Futtassa a kódot.
> * Események figyelése.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás

A Kezdés előtt olvassa el a következő cikkeket:

* [Élő videó-elemzések IoT Edge áttekintése](overview.md)
* [Élő videó-elemzések IoT Edge terminológiában](terminology.md)
* [A Media Graph alapfogalmai](media-graph-concept.md)
* [Eseményalapú videófelvétel](event-based-video-recording-concept.md)
* [Oktatóanyag: IoT Edge modul fejlesztése](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Élő video Analytics üzembe helyezése Azure Stack Edge-ben](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>Előfeltételek

Az alábbiak a térbeli elemzési modul élő video Analytics-modulhoz való csatlakoztatásának előfeltételei.

* A [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen. Győződjön meg arról, hogy rendelkezik az [Azure IoT Tools bővítménnyel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
    * Győződjön meg arról, hogy a fejlesztői számítógép hálózata csatlakozik a speciális üzenetsor-kezelési protokoll engedélyezéséhez a 5671-es porton keresztül. Ez a telepítő lehetővé teszi az Azure IoT-eszközök számára az Azure IoT Hub való kommunikációt.
* [Azure stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) GPU-gyorsítással.  
    Azt javasoljuk, hogy a Azure Stack Edge-t GPU-gyorsítással használja, azonban a tároló bármely más eszközön fut egy [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/)-val. 
* Az [Azure kognitív szolgáltatás Computer Vision tároló](https://azure.microsoft.com/services/cognitive-services/computer-vision/) a térbeli elemzéshez.  
    A tároló használatához rendelkeznie kell egy Computer Vision erőforrással a társított **API-kulcs** és egy **végponti URI**beszerzéséhez. Az API-kulcs a Azure Portal Computer Vision áttekintés és kulcsok oldalain érhető el. A tároló indításához a kulcs és a végpont szükséges.

## <a name="overview"></a>Áttekintés

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="Térbeli elemzés – áttekintés":::
 
Ez az ábra az oktatóanyagban szereplő jelek folyamatát mutatja be. Az [Edge-modulok](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) egy valós idejű Streaming Protocol-(RTSP-) kiszolgálót üzemeltető IP-kamerát szimulálnak. Az [RTSP-forrás](media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és a képkockákat a [frame rate szűrő processzor](media-graph-concept.md#frame-rate-filter-processor) -csomópontjára küldi. Ez a processzor korlátozza a MediaGraphCognitiveServicesVisionExtension processzor-csomópontot elérő video stream képkockasebességét.

A MediaGraphCognitiveServicesVisionExtension csomópont egy proxy szerepét játssza le. A képkockákat a megadott képtípusra konvertálja. Ezt követően továbbítja a rendszerképet a **megosztott memórián** keresztül egy másik peremhálózati modulhoz, amely egy gRPC-végpont MÖGÖTTi AI-műveleteket futtat. Ebben a példában ez a peremhálózati modul a térbeli elemzési modul. A MediaGraphCognitiveServicesVisionExtension processzor csomópontja két dolgot tesz:

* Összegyűjti az eredményeket, és közzéteszi az eseményeket a [IoT hub](media-graph-concept.md#iot-hub-message-sink) fogadó csomópontban. A csomópont ezután elküldi ezeket az eseményeket [IoT Edge hubhoz](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub). 
* Egy 30 másodperces videoklipet is rögzít az RTSP-forrásról a [Signal Gate-processzor](media-graph-concept.md#signal-gate-processor) használatával, és Media Services eszközként tárolja azt.

## <a name="create-the-computer-vision-resource"></a>A Computer Vision erőforrás létrehozása

Létre kell hoznia egy Computer Vision típusú Azure-erőforrást [Azure Portal](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal) vagy az Azure CLI-n keresztül. Az erőforrást akkor fogja tudni létrehozni, ha a tárolóhoz való hozzáférésre vonatkozó kérelmét jóváhagyták, és az Azure-előfizetési azonosítót regisztrálták. A https://aka.ms/csgate használati eset és az Azure-előfizetés azonosítójának elküldéséhez nyissa meg a következőt:.  Létre kell hoznia az Azure-erőforrást ugyanazzal az Azure-előfizetéssel, mint amelyet a hozzáférési űrlapra vonatkozó kérelem biztosít.

### <a name="gathering-required-parameters"></a>A szükséges paraméterek összegyűjtése

Három elsődleges paraméter van az összes szükséges Cognitive Services tárolóhoz, beleértve a térbeli elemzési tárolót is. A végfelhasználói licencszerződést (EULA) az Accept értékkel kell megadnia. Emellett a végponti URL-cím és az API-kulcs is szükséges.

### <a name="endpoint-uri-endpoint_uri"></a>Végpont URI azonosítója {ENDPOINT_URI}

A végpont URI-értéke a Cognitive Services erőforrás Azure Portal áttekintés lapján érhető el. Keresse meg az Áttekintés lapot, és keresse meg a végpont URI-JÁT. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Kulcsok és végpont":::

### <a name="keys-api_key"></a>Kulcsok {API_KEY}

Ez a kulcs a térbeli elemzési tároló elindítására szolgál, és a megfelelő kognitív szolgáltatási erőforrás Azure Portal kulcsok lapján érhető el. Keresse meg a kulcsok lapot, és keresse meg a kulcsokat.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/endpoint-uri.png" alt-text="Végpont URI-ja":::

## <a name="set-up-azure-stack-edge"></a>Azure Stack Edge beállítása

Az alábbi [lépéseket](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep) követve állíthatja be az Azure stack Edge-t, és folytassa az alábbi lépések végrehajtásával az élő videó-elemzés és a térbeli elemzési modulok üzembe helyezéséhez.

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

1. A tárház klónozása erről a helyről: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. A Visual Studio Code-ban nyissa meg azt a mappát, ahol a tárház le lett töltve.
1. A Visual Studio Code-ban nyissa meg a src/Cloud-to-Device-Console-app mappát. Ott hozzon létre egy fájlt, és nevezze el appsettings.js. Ez a fájl a program futtatásához szükséges beállításokat fogja tartalmazni.
1. Az alábbi lépéseket követve szerezze be a IotHubConnectionString a Azure Stack szélétől:

    * a bal oldali navigációs panelen kattintson a IoT Hub a Azure Portal és a elemre `Shared access policies` .
    * Kattintson a `iothubowner` közös elérésű kulcsok beolvasása elemre.
    * Másolja a vágólapra,  `Connection String – primary key` és illessze be a VSCode beviteli mezőjébe.
    
        A következőhöz hasonló lesz a kapcsolatok karakterlánca: <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. Másolja az alábbi tartalmat a fájlba. Győződjön meg arról, hogy lecserélte a változókat.
    
    ```json
    {
        "IoThubConnectionString" : " HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>”,
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. Nyissa meg az src/Edge mappát, és hozzon létre egy. env nevű fájlt.
1. Másolja a/clouddrive/LVA-Sample/Edge-Deployment/.env fájl tartalmát. A szövegnek a következő kódhoz hasonlóan kell kinéznie.

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>Telepítési sablon beállítása  

Adja hozzá a térbeli elemzési modult a/src/Edge/deployment.template.js. A sablonban a lvaEdge modul, a rtspsim modul és a térbeli elemzési modul található.

<p>
<details>
<summary>Bontsa ki a következőt, és tekintse meg a minta telepítési sablont.  
Másolja ide a tartalmakat, és illessze be a/src/Edge/deployment.template.jsbe.
</summary>
<pre><code>
{
  "$schema-template": "2.0.0",
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": {}
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "5671/tcp": [
                      {
                        "HostPort": "5671"
                      }
                    ],
                    "8883/tcp": [
                      {
                        "HostPort": "8883"
                      }
                    ],
                    "443/tcp": [
                      {
                        "HostPort": "443"
                      }
                    ]
                  }
                }
              }
            }
          }
        },
        "modules": {
          "lvaEdge": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/media/live-video-analytics:1",
              "createOptions": {
                "HostConfig": {
                  "LogConfig": {
                    "Type": "",
                    "Config": {
                      "max-size": "10m",
                      "max-file": "10"
                    }
                  },
                  "Binds": [
                    "$OUTPUT_VIDEO_FOLDER_ON_DEVICE:/var/media/",
                    "$APPDATA_FOLDER_ON_DEVICE:/var/lib/azuremediaservices"
                  ],
                  "IpcMode": "host",
                  "ShmSize": 1536870912
                }
              }
            },
            "env": {
              "IS_DEVELOPER_ENVIRONMENT": {
                "value": "true"
              }
            }
          },
          "rtspsim": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2",
                "createOptions": {
                  "HostConfig": {
                    "Mounts": [
                      {
                        "Target": "/live/mediaServer/media",
                        "Source": "lvaspatialanalysislocal",
                        "Type": "volume"
                      }
                    ],
                    "PortBindings": {
                      "554/tcp": [
                        {
                          "HostPort": "554"
                        }
                      ]
                    }
                  }
                }
              }
            },
          "spatialAnalysis": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azure-cognitive-services/spatial-analysis:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "50051/tcp": [
                      {
                        "HostPort": "50051"
                      }
                    ]
                  },
                  "IpcMode": "host",
                  "Binds": [
                      "/tmp/.X11-unix:/tmp/.X11-unix"
                  ],
                  "Runtime": "nvidia",
                  "ShmSize": 536870911,
                  "LogConfig": {
                      "Type": "json-file",
                      "Config": {
                          "max-size": "10m",
                          "max-file": "200"
                      }
                  }
                }
              }
            },
            "env": {
              "DISPLAY": {
                "value": ":0"
              },
              "ARCHON_SHARED_BUFFER_LIMIT": {
                "value": "377487360"
              },
              "ARCHON_PERF_MARKER": {
                "value": "false"
              },
              "QT_X11_NO_MITSHM": {
                "value": "1"
              },
              "OMP_WAIT_POLICY": {
                "value": "PASSIVE"
              },
              "EULA": {
                "value": "accept"
              },
              "BILLING_ENDPOINT": {
                "value": "<Use one key from Archon azure resource (keys page)>"
              },
              "API_KEY": {
                "value": "<Use endpoint from Archon azure resource (overview page)>"
              }
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "LVAToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "lvaEdge": {
      "properties.desired": {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/microsoft.media/mediaservices/$AMS_ACCOUNT",
        "aadTenantId": "$AAD_TENANT_ID",
        "aadServicePrincipalAppId": "$AAD_SERVICE_PRINCIPAL_ID",
        "aadServicePrincipalSecret": "$AAD_SERVICE_PRINCIPAL_SECRET",
        "aadEndpoint": "https://login.microsoftonline.com",
        "aadResourceId": "https://management.core.windows.net/",
        "armEndpoint": "https://management.azure.com/",
        "diagnosticsEventsOutputName": "AmsDiagnostics",
        "operationalEventsOutputName": "AmsOperational",        
        "logLevel": "Info",
        "logCategories": "Application,Events,MediaPipeline",
        "allowUnsecuredEndpoints": true,
        "telemetryOptOut": false
      }
    },
    "spatialAnalysis": {
      "properties.desired": {
        "globalSettings": {
          "PlatformTelemetryEnabled": true,
          "CustomerTelemetryEnabled": true
        },
        "graphs": {
            "polygonCross": {
              "version": 2,
              "enabled": true,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0,0],[0.6,0],[0.6,0.9],[0,0.9],[0,0]],\"threshold\":50,\"events\":[{\"type\":\"enter/exit\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personCount": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0.8,0],[1,0],[1,1],[0.8,1],[0.8,0]],\"threshold\":50,\"events\":[{\"type\":\"count\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personDistance": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0,\"gpu_index\": 0,\"do_calibration\": true}",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\": \"distance_zone\", \"polygon\": [[0,0],[0,1],[1,1],[1,0],[0,0]],\"threshold\": 35.00,\"events\":[{\"type\": \"people_distance\",\"config\":{\"trigger\": \"event\",\"output_frequency\":1,\"minimum_distance_threshold\":6.0,\"maximum_distance_threshold\":35.0}}]}]}"
              },
              "nodesloglevel": "info"
            }
        }
      }
    }
  }
}
</code>
</pre>
</details>
</p>

A következőkre van szüksége:

1. Állítsa be a port kötését.
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. A lvaEdge és a térbeli elemzési modul createOptions IpcMode azonosnak kell lennie, és a gazdagépre kell beállítani.
1. A telepítési sablon fájljának tartalmaznia kell a "központi telepítés" kifejezést a fájlnévben, ellenkező esetben nem ismerhető fel és nem hozható nyilvánosságra az üzembe helyezési jegyzék.
1. Az RTSP-szimulátor működéséhez győződjön meg arról, hogy beállította a kötet határait. További információ: [Setup Docker Volume mounts](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts).

    1. [Kapcsolódjon az SMB-megosztáshoz](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-add-shares#connect-to-an-smb-share) , és másolja a [videofájl](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) a helyi megosztásba.
    1. Láthatja, hogy a rtspsim modul a következőt tartalmazza:
        
        ```json
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
## <a name="generate-and-deploy-the-deployment-manifest"></a>Az üzembe helyezési jegyzék előállítása és üzembe helyezése

Az üzembe helyezési jegyzék meghatározza, hogy a rendszer mely modulokat telepíti egy peremhálózati eszközre. Emellett meghatározza a modulok konfigurációs beállításait is.

Kövesse az alábbi lépéseket a jegyzékfájlnak a sablonból való létrehozásához, majd a peremhálózati eszközre történő telepítéséhez.

1. Nyissa meg a Visual Studio Code-ot.
1. Az AZURE IOT HUB panel mellett válassza a további műveletek ikont a IoT Hub kapcsolódási karakterlánc beállításához. A karakterláncot a src/Cloud-to-Device-Console-app/appsettings.jsfájlból másolhatja.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Térbeli elemzés: a kapcsolatok karakterlánca":::
1. Kattintson a jobb gombbal az src/Edge/deployment.template.jselemre, és válassza a IoT Edge üzembe helyezési jegyzék előállítása lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Térbeli elemzés: üzembe helyezés amd64 JSON":::
    
    Ehhez a művelethez létre kell hoznia egy deployment.amd64.jsnevű jegyzékfájlt az src/Edge/config mappában.
1. Kattintson a jobb gombbal az src/Edge/config/deployment.amd64.jselemre, válassza a központi telepítés létrehozása egyetlen eszközhöz lehetőséget, majd válassza ki a peremhálózati eszköz nevét.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Térbeli elemzés: üzembe helyezési sablon JSON":::   
1. Amikor a rendszer rákérdez a IoT Hub eszköz kiválasztására, válassza ki a Azure Stack Edge nevét a legördülő menüből.
1. Körülbelül 30 másodperc elteltével frissítse az Azure IoT Hubt az ablak bal alsó sarkában. A peremhálózati eszköz mostantól a következő központilag telepített modulokat mutatja:
    
    * Élő video Analytics IoT Edge (modul neve lvaEdge).
    * Valós idejű Streaming Protocol (RTSP) szimulátor (modul neve rtspsim).
    * Térbeli elemzés (modul neve spatialAnalysis).
    
Ha a telepítés sikeresen megtörtént, a KIMENETben a következőhöz hasonló üzenet jelenik meg:

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

Ezután megkeresheti `lvaEdge` , `rtspsim` `spatialAnalysis` és `rtspsim` modulokat használhat az eszközök/modulok területen, és az állapotuknak "fut" állapotúnak kell lennie.

## <a name="prepare-to-monitor-events"></a>Felkészülés az események figyelésére

Az események megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Explorer panelt a Visual Studio Code-ban, és keresse meg az Azure IoT Hub a bal alsó sarokban.
1. Bontsa ki az eszközök csomópontot.
1. Kattintson a jobb gombbal az Azure Stack Edge-re, és válassza a figyelés beépített esemény végpontja lehetőséget.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Térbeli elemzés: figyelés indítása":::
     
## <a name="run-the-program"></a>A program futtatása

Létezik egy program.cs, amely a közvetlen metódusokat hívja meg a src/Cloud-to-Device-Console-app/operations.json. Be kell állítania a operations.jst, és meg kell adnia egy topológiát a Media Graph használatához.
operations.json:

Állítsa be az ehhez hasonló topológiát (topologyFile helyi topológiához, topologyUrl az online topológiához):

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyFile": "../edge/spatialAnalysistopology.json"
    }
},
```

Hozzon létre gráf-példányt ehhez hasonló módon, itt állítsa be a paramétereket a topológiában:

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
                },
                {
                    "name": "rtspUserName",
                    "value": "testuser"
                },
                {
                    "name": "rtspPassword",
                    "value": "testpassword"
                }
            ]
        }
    }
},
```

<p>
<details>
<summary>Bontsa ki a spatialAnalysis-modulhoz tartozó minta-topológiai fájl megjelenítéséhez:
</summary>
<pre><code>
{
    "@apiVersion": "1.0",
    "name": "InferencingWithCVExtension",
    "properties": {
      "description": "Analyzing live video using spatialAnalysis Extension to send images to an external inference engine",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        },
        {
          "name": "grpcUrl",
          "type": "String",
          "description": "inferencing Url",
          "default": "tcp://spatialAnalysis:50051"
        },
        {
          "name": "frameRate",
          "type": "String",
          "description": "Rate of the frames per second to be received from LVA.",
          "default": "2"
        },
        {
          "name": "spatialanalysisusername",
          "type": "String",
          "description": "spatialanalysis endpoint username",
          "default": "not-in-use"
        },
        {
          "name": "spatialanalysispassword",
          "type": "String",
          "description": "spatialanalysis endpoint password",
          "default": "not-in-use"  
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphFrameRateFilterProcessor",
          "name": "frameRateFilter",
          "inputs": [
            {
              "nodeName": "rtspSource"
            }
          ],
          "maximumFps": "${frameRate}"
        },
        {
          "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
          "name": "computerVisionExtension",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${grpcUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${spatialanalysisusername}",
              "password": "${spatialanalysispassword}"
            }
          },
          "image": {
            "scale": {
              "mode": "pad",
              "width": "1408",
              "height": "786"
            },
            "format": {
              "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
              "pixelFormat": "bgr24"
            }
          },
          "inputs": [
            {
              "nodeName": "frameRateFilter"
            }
          ]
        },
        {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "computerVisionExtension"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "PT0S",
            "minimumActivationTime": "PT30S",
            "maximumActivationTime": "PT30S"
          }
      ],
      "sinks": [
        {
            "@type": "#Microsoft.Media.MediaGraphAssetSink",
            "name": "assetSink",
            "assetNamePattern": "sampleAssetFromEVR-CV-LVAEdge-${System.DateTime}",
            "segmentLength": "PT30S",
            "LocalMediaCacheMaximumSizeMiB": "200",
            "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
            "inputs": [
                {
                    "nodeName": "signalGateProcessor"
                }
            ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "name": "hubSink",
          "hubOutputName": "inferenceOutput",
          "inputs": [
            {
              "nodeName": "computerVisionExtension"
            }
          ]
        }
      ]
    }
  }
</code>
</pre>
</details>
</p>


MediaGraphRealTimeComputerVisionExtension használata a térbeli elemzési modullal való kapcsolódáshoz. A $ {grpcUrl} beállítása a tcp://spatialAnalysis-ben: <PORT_NUMBER>, azaz tcp://spatialAnalysis:50051

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcUrl}",
    "credentials": {
        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
        "username": "${spatialanalysisusername}",
        "password": "${spatialanalysispassword}"
    }
    },
    "image": {
    "scale": {
        "mode": "pad",
        "width": "1408",
        "height": "786"
    },
    "format": {
        "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
        "pixelFormat": "bgr24"
    }
    },
    "inputs": [
    {
        "nodeName": "frameRateFilter"
    }
    ]
}
```

Futtasson hibakeresési munkamenetet, és kövesse a terminál utasításait, majd állítsa be a topológiát, állítsa be a Graph-példányt, aktiválja a Graph-példányt, és végül törölje az erőforrásokat.

## <a name="interpret-results"></a>Eredmények értelmezése

Ha egy adathordozó-gráfot hoz létre, a "MediaSessionEstablished" eseményt kell megjelennie, itt egy [példa MediaSessionEstablished eseményt](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

A térbeli elemzési modul a mesterséges intelligenciát bemutató eseményeket is küldi az élő videó-elemzésbe, majd a IoTHub a KIMENETben is megjelenik. Az entitás az észlelési objektumok, az esemény pedig spaceanalytics eseményeket. Ezt a kimenetet a rendszer átadja a Live Video Analyticsnek.

Minta kimenete a personZoneEvent (a cognitiveservices. vízió. spatialanalysis-personcrossingpolygon. livevideoanalytics műveletből):

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>Következő lépések

Próbálkozzon a modul által kínált különböző műveletekkel, `spatialAnalysis` például a **personCount** és a **personDistance** . ehhez a telepítési jegyzékfájl Graph csomópontjában az "enabled" jelzőt kell bekapcsolni.
>[!Tip]
> Használjon egy olyan [videofájlokat](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv) , amely egynél több személlyel rendelkezik a keretben.

> [!NOTE]
> Egyszerre csak egy műveletet futtathat. Ezért győződjön meg arról, hogy csak egy jelző van beállítva **igaz** értékre, a többi pedig **false (hamis**) értékre van állítva.
