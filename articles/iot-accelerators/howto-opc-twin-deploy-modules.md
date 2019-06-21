---
title: OPC Ikereszköz modul üzembe helyezése az Azure-ban teljesen új |} A Microsoft Docs
description: Hogyan helyezheti üzembe az OPC-Twin sablon nélkül.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 41d544fd23d258393cc83ea09371332655223581
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203930"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>OPC Ikereszköz modul és függőségei előzmények üzembe helyezése

Az OPC-Twin-modul az IoT Edge-ben fut, és számos biztonsági szolgáltatások az OPC-ikereszközön és beállításjegyzék-szolgáltatásokhoz biztosít. 

Több lehetőség-modulok üzembe helyezéséhez a [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) átjárót, többek között

- [Az Azure Portalon az IoT Edge panelről üzembe helyezése](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [AZ parancssori felület használata](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Üzembe helyezés részleteit és az utasítások további információkért lásd: a GitHub [tárház](https://github.com/Azure/azure-iiot-components).

## <a name="deployment-manifest"></a>Üzembehelyezési jegyzék

Minden modul egy manifest nasazení használatával helyezi üzembe.  Egy példa jegyzékének mindkettőt üzembe helyezheti [az OPC-közzétevő](https://github.com/Azure/iot-edge-opc-publisher) és [OPC Ikereszköz](https://github.com/Azure/azure-iiot-opc-twin-module) alább találja.

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
                "createOptions": "{\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"CapAdd\":[\"NET_ADMIN\"]}}"
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

## <a name="deploying-from-azure-portal"></a>Az Azure Portalról üzembe helyezése

A modulok üzembe helyezése az Azure IoT Edge-átjáróeszköz legegyszerűbben az Azure Portalon keresztül.  

### <a name="prerequisites"></a>Előfeltételek

1. Az OPC-Twin üzembe [függőségek](howto-opc-twin-deploy-dependencies.md) és az eredményül kapott `.env` fájlt. Vegye figyelembe az üzembe helyezett `hub name` , a `PCS_IOTHUBREACT_HUB_NAME` változót a létrejövő `.env` fájlt.

2. Regisztráljon, és indítsa el a [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) vagy [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge-átjáró, és jegyezze fel annak `device id`.

### <a name="deploy-to-an-edge-device"></a>Edge-eszköz üzembe helyezése

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) , és keresse meg az IoT hubot.

2. Válassza ki **IoT Edge** elemet a bal oldali menüben.

3. Kattintson az eszközök a listából a célként megadott eszköz Azonosítóját.

4. Válassza a **Modulok beállítása** lehetőséget.

5. Az a **üzembe helyezési modulok** című oldalon válassza **Hozzáadás** és **IoT Edge-modul.**

6. Az a **IoT Edge-modul egyéni** párbeszédpanel használata `opctwin` , a modul neve, majd adja meg a tároló *kép URI* ,

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Mint *lehetőségei* használja a következő JSON-ra:

   ```json
   {"HostConfig":{"NetworkMode":"host","CapAdd":["NET_ADMIN"]}}
   ```

   Töltse ki az opcionális mezőket, ha szükséges. További információ a tároló-létrehozási beállítások, újraindítási szabályzata, és tekintse meg a kívánt állapot [EdgeAgent kívánt tulajdonságok](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). További információ az ikermodul: [meghatározása vagy a frissítés kívánt tulajdonságok](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Válassza ki **mentése** , és ismételje meg **5**.  

8. Használja az IoT Edge-modul egyéni párbeszédpanelen `opcpublisher` a modul és a tároló számára *kép URI* , 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Mint *lehetőségei* használja a következő JSON-ra:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Válassza ki **mentése** , majd **tovább** továbbra is az útvonalak szakaszban.

10. Az útvonalak lapon illessze be a következő 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
      }
    }
    ```

    Válassza ki **tovább**

11. Tekintse át a telepítési adatokat, és a jegyzékfájl.  A fenti manifest nasazení hasonlónak kell lennie.  Válassza ki **elküldése**.

12. Miután telepítette a modulokat az eszközön, megtekintheti azokat a **eszközadatok** a portál. Ezen a lapon minden egyes telepített modul, valamint a hasznos információk, például a központi telepítési állapot és a kilépési kód nevét jeleníti meg.

## <a name="deploying-using-azure-cli"></a>Üzembe helyezése az Azure CLI használatával

### <a name="prerequisites"></a>Előfeltételek

1. Telepítse a legújabb verzióját a [Azure parancssori felület (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) a [Itt](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Első lépések

1. Az a fenti telepítési a jegyzék mentése egy `deployment.json` fájlt.  

2. A következő parancsot használja a alkalmazni a konfigurációt egy IoT Edge-eszközön:

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   A `device id` paraméter a kis-és nagybetűket. A tartalom paraméter mutat az üzembe helyezés manifest mentett fájlt. 
    ![az IoT Edge set-modules output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Miután telepítette a modulokat az eszközön, megtekintheti azokat a következő paranccsal:

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Az eszköz azonosító paraméter értéke a kis-és nagybetűket. ![az iot hub modul-identity list kimeneti](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan helyezhet üzembe az OPC-Twin teljesen új, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az OPC-Ikereszköz egy meglévő projekt üzembe helyezése](howto-opc-twin-deploy-existing.md)