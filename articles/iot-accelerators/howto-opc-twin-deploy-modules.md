---
title: Az OPC Twin modul telepítése az Azure-hoz a semmiből | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan telepítheti az OPC Twin a semmiből az Azure Portal IoT Edge-panel használatával, valamint az AZ CLI használatával.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6c8ceeaf49d8ebfa15a83118e8b518190f6ff85e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241061"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>OpC Twin modul és függőségek telepítése a semmiből

Az OPC Twin modul az IoT Edge-en fut, és számos peremhálózati szolgáltatást nyújt az OPC-eszköz iker- és beállításjegyzék-szolgáltatásokszámára. 

Számos lehetőség van a modulok üzembe helyezésére az [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) Gateway- ben, köztük

- [Üzembe helyezés az Azure Portal IoT Edge-paneljéről](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Üzembe helyezés az AZ CLI használatával](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> A központi telepítés részleteiről és utasításairól a [GitHub-tárházban](https://github.com/Azure/azure-iiot-components)talál további információt.

## <a name="deployment-manifest"></a>Üzembehelyezési jegyzék

Az összes modul központi telepítési jegyzék használatával van telepítve.  Az [opc-közzétevő](https://github.com/Azure/iot-edge-opc-publisher) és az [OPC Twin](https://github.com/Azure/azure-iiot-opc-twin-module) központi telepítésére az alábbi példa jelenik meg.

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

## <a name="deploying-from-azure-portal"></a>Üzembe helyezés az Azure Portalról

A modulok üzembe helyezésének legegyszerűbb módja egy Azure IoT Edge átjáróeszközön az Azure Portalon keresztül.  

### <a name="prerequisites"></a>Előfeltételek

1. Telepítse az OPC [Twin-függőségeket,](howto-opc-twin-deploy-dependencies.md) és szerezze be az eredményül kapott `.env` fájlt. Vegye figyelembe, `hub name` hogy `PCS_IOTHUBREACT_HUB_NAME` a változó `.env` telepítve van az eredményül kapott fájlban.

2. Regisztráljon és indítson el egy [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) vagy `device id` [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge átjárót, és jegyezze fel a .

### <a name="deploy-to-an-edge-device"></a>Telepítés peremhálózati eszközre

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com/) és keresse meg az IoT-központot.

2. Válassza a bal oldali menü **IoT Edge** parancsát.

3. Kattintson a céleszköz azonosítójára az eszközök listájából.

4. Válassza **a Modulok beállítása**lehetőséget.

5. A **lap Telepítési modulok** szakaszában válassza a **Hozzáadás** és **az IoT Edge-modul lehetőséget.**

6. Az **IoT Edge egyéni** `opctwin` modul párbeszédpanelen használja a modul nevét, majd adja meg a *tárolókép URI-ját*

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   A *Tároló létrehozási beállításaiként*használja a következő JSON-t:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Szükség esetén töltse ki a választható mezőket. A tárolók létrehozásának beállításairól, az újraindítási házirendről és a kívánt állapotról az [EdgeAgent kívánt tulajdonságai](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties)című témakörben talál további információt. Az ikermodulról a [Kívánt tulajdonságok definiálása vagy frissítése](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)című témakörben talál további információt.

7. Válassza a **Mentés** és az **5.**  

8. Az IoT Edge egyéni modul `opcpublisher` párbeszédpanelen használja a modul nevét, és a *tárolókép URI-ját* 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   A *Tároló létrehozási beállításaiként*használja a következő JSON-t:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Válassza a **Mentés,** majd a **Tovább lehetőséget** az útvonalak szakasz folytatásához.

10. Az Útvonalak lapon illessze be a következő 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    és válassza a **Tovább gombot**

11. Tekintse át a telepítési információkat és a jegyzékfájl.  Úgy kell kinéznie, mint a fenti telepítési jegyzékfájl.  Válassza a **Küldés** lehetőséget.

12. Miután telepítette a modulokat az eszközre, megtekintheti az összeset a portál **Eszköz részletek** lapján. Ez a lap megjeleníti az egyes üzembe helyezett modulok nevét, valamint olyan hasznos információkat, mint a központi telepítés állapota és a kilépési kód.

## <a name="deploying-using-azure-cli"></a>Üzembe helyezés az Azure CLI használatával

### <a name="prerequisites"></a>Előfeltételek

1. Telepítse az Azure [parancssori felületének (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) legújabb verzióját [innen.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

### <a name="quickstart"></a>Első lépések

1. Mentse a fenti telepítési `deployment.json` jegyzékfájlt egy fájlba.  

2. A következő paranccsal alkalmazhatja a konfigurációt egy IoT Edge-eszközre:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   A `device id` paraméter ben a kis- és nagybetűket kell figyelembe. A tartalomparaméter a mentett telepítési jegyzékfájlra mutat. 
    ![az IoT Edge set-modulok kimenete](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Miután telepítette a modulokat az eszközre, mindegyiket megtekintheti a következő paranccsal:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Az eszközazonosító paraméter ben a kis- és nagybetűk et kell figyelembe. ![az iot hub modulidentitás-lista kimenete](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan kell telepíteni OPC Twin a semmiből, itt van a javasolt következő lépés:

> [!div class="nextstepaction"]
> [Opc Twin telepítése meglévő projektre](howto-opc-twin-deploy-existing.md)
