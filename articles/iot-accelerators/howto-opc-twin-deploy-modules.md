---
title: Az OPC Twin modul üzembe helyezése az Azure-ban a semmiből | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan helyezhet üzembe az OPC-ket a semmiből a Azure Portal IoT Edge paneljén, valamint az AZ parancssori felület használatával.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
ms.custom: devx-track-azurecli
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9ae3e9b4bb69bf0c85054b5d6144633923cac947
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91282068"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>OPC Twin modul és függőségek üzembe helyezése a semmiből

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

Az OPC Twin modul IoT Edge fut, és több peremhálózati szolgáltatást biztosít az OPC-eszközök Twin és a Registry Services számára. 

Több lehetőség is van a modulok üzembe helyezésére a [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) -átjárón, köztük

- [Üzembe helyezés a Azure Portal IoT Edge paneljéről](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Üzembe helyezés AZ AZ CLI használatával](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Az üzembe helyezés részleteiről és az utasításokról a GitHub- [tárházban](https://github.com/Azure/azure-iiot-components)talál további információt.

## <a name="deployment-manifest"></a>Üzembehelyezési jegyzék

Az összes modul központi telepítési jegyzékfájl használatával van telepítve.  Alább látható egy példa az [OPC-közzétevő](https://github.com/Azure/iot-edge-opc-publisher) és az [OPC Twin](https://github.com/Azure/azure-iiot-opc-twin-module) üzembe helyezésére.

```json
{
  "content": {
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
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Üzembe helyezés Azure Portal

A modulok Azure IoT Edge átjáró eszközre történő központi telepítésének legegyszerűbb módja a Azure Portal.  

### <a name="prerequisites"></a>Előfeltételek

1. Telepítse az OPC Twin- [függőségeket](howto-opc-twin-deploy-dependencies.md) , és szerezte be az eredményül kapott `.env` fájlt. Figyelje meg a változó üzembe helyezését `hub name` `PCS_IOTHUBREACT_HUB_NAME` az eredményül kapott `.env` fájlban.

2. Regisztráljon és indítson el egy [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) -vagy [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge-átjárót, és jegyezze fel `device id` .

### <a name="deploy-to-an-edge-device"></a>Üzembe helyezés peremhálózati eszközön

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és navigáljon az IoT hubhoz.

2. A bal oldali menüben válassza a **IoT Edge** lehetőséget.

3. Kattintson a céleszköz AZONOSÍTÓJÁRA az eszközök listájából.

4. Válassza a **modulok beállítása**lehetőséget.

5. A lap **központi telepítési modulok** szakaszában válassza a **Hozzáadás** és **IoT Edge modul elemet.**

6. A **IoT Edge egyéni modul** párbeszédpanelen használja `opctwin` a modul nevét, majd adja meg a tároló *rendszerképének URI-ját*

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   A *tároló létrehozása lehetőségnél*használja a következő JSON-t:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Szükség esetén töltse ki az opcionális mezőket. További információ a tároló létrehozási lehetőségeiről, az újraindítási szabályzatról és a kívánt állapotról: [EdgeAgent kívánt tulajdonságai](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). További információ a modul Twin-ről: a [kívánt tulajdonságok meghatározása vagy frissítése](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Válassza a **Mentés** lehetőséget, és ismételje meg az **5**. lépést  

8. A IoT Edge egyéni modul párbeszédpanelen használja `opcpublisher` a modul nevét és a tároló *rendszerképének URI-ját* 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   A *tároló létrehozása lehetőségnél*használja a következő JSON-t:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Válassza a **Mentés** lehetőséget **, majd kattintson a Tovább gombra az** útvonalak szakaszhoz.

10. Az útvonalak lapon illessze be a következőt: 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    és válassza a **tovább** lehetőséget.

11. Tekintse át az üzembe helyezési adatokat és a jegyzékfájlt.  A fenti üzembe helyezési jegyzékhez hasonlóan kell kinéznie.  Válassza a **Küldés** lehetőséget.

12. Miután telepítette a modulokat az eszközre, megtekintheti az összeset a portál **eszköz adatai** lapján. Ez a lap megjeleníti az egyes telepített modulok nevét, valamint a hasznos információkat, például a telepítési állapotot és a kilépési kódot.

## <a name="deploying-using-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

### <a name="prerequisites"></a>Előfeltételek

1. Telepítse az [Azure Command Line Interface (az)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) legújabb verzióját [innen.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

### <a name="quickstart"></a>Gyorsútmutató

1. Mentse a fenti telepítési jegyzéket egy `deployment.json` fájlba.  

2. A következő parancs használatával alkalmazza a konfigurációt egy IoT Edge eszközre:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   A `device id` paraméter megkülönbözteti a kis-és nagybetűket. A Content paraméter a mentett telepítési jegyzékfájlra mutat. 
    ![az IoT Edge set-modules output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Miután telepítette a modulokat az eszközre, a következő paranccsal tekintheti meg az összeset:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   A Device ID paraméter megkülönbözteti a kis-és nagybetűket. ![az IOT hub modul-Identity List output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan helyezhet üzembe az OPC Twin-et, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Az OPC Twin üzembe helyezése egy meglévő projektben](howto-opc-twin-deploy-existing.md)
