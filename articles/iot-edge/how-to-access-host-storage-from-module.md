---
title: IoT Edge-eszköz helyi tárolójának használata egy modulból - Azure IoT Edge | Microsoft dokumentumok
description: Használjon környezeti változókat, és hozzon létre beállításokat az IoT Edge-eszköz helyi tárolójához való modulhozzáférés engedélyezéséhez.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079d5845917e63fadcf0466e5a744ed637d704ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434528"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Hozzáférés biztosítása modulok számára egy eszköz helyi tárterületéhez

Az adatok Azure storage-szolgáltatások használatával vagy az eszköz tárolótárolójában való tárolása mellett a gazdaszervezet IoT Edge-eszközén is feloszthatja a tárhelyet a nagyobb megbízhatóság érdekében, különösen offline módban.

## <a name="link-module-storage-to-device-storage"></a>Modultárolás összekapcsolása az eszköztárolóval

Ha engedélyezni szeretné a modultárolóés a gazdarendszer tárolójának közötti kapcsolatot, hozzon létre egy környezeti változót a modulszámára, amely a tárolóban lévő tárolómappára mutat. Ezután a létrehozási beállításokkal kötheti a tárolómappát a gazdagép egyik mappájához.

Ha például engedélyezni szeretné, hogy az IoT Edge hub üzeneteket tároljon az eszköz helyi tárolójában, és később lekérje őket, konfigurálhatja a környezeti változókat és a létrehozási beállításokat az Azure Portalon a **Futásidejű beállítások** szakaszban.

1. Az IoT Edge hub és az IoT Edge-ügynök egy-egy **storageFolder** nevű környezeti változót is hozzáadhat, amely a modul egy könyvtárára mutat.
1. Az IoT Edge hub és az IoT Edge-ügynök, adja hozzá a helyi könyvtár at a gazdagép egy könyvtár a modulban. Példa:

   ![Létrehozási beállítások és környezeti változók hozzáadása a helyi tároláshoz](./media/how-to-access-host-storage-from-module/offline-storage.png)

Vagy konfigurálhatja a helyi tárolót közvetlenül a központi telepítési jegyzékben. Példa:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Cserélje `<HostStoragePath>` `<ModuleStoragePath>` le és válassza ki a gazdagép és a modul tárolási útvonalát; mindkét értéknek abszolút elérési útnak kell lennie.

Linux rendszeren például azt `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` jelenti, hogy a gazdarendszer **/etc/iotedge/storage** könyvtára a tárolóban lévő **/iotedge/storage könyvtárhoz** van rendelve. Windows rendszerben, egy másik `"Binds":["C:\\temp:C:\\contemp"]` példa, azt jelenti, hogy a **gazdarendszer C:\\temp** könyvtára a C könyvtárhoz van rendelve: **\\a** tárolóban.

Emellett Linux-eszközökön győződjön meg arról, hogy a modul felhasználói profilja rendelkezik a szükséges olvasási, írási és végrehajtási engedélyekkel a gazdarendszer könyvtárához. Visszatérve a korábbi példa, amely lehetővé teszi az IoT Edge hub üzenetek tárolására az eszköz helyi tárolójában, engedélyeket kell adnia a felhasználói profil, UID 1000. (Az IoT Edge-ügynök gyökérként működik, így nincs szüksége további engedélyekre.) A Linux rendszerek címtárengedélyeinek kezelésének `chown` számos módja van, `chmod` például a címtár tulajdonosának módosítása, majd az engedélyek módosítása, például:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

A [docker-dokumentumokból](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)további részleteket talál a létrehozási lehetőségekről.

## <a name="next-steps"></a>További lépések

További példa a gazdagép-tároló modulból való elérésére: [Adatok tárolása a peremhálózaton az IoT Edge-en az Azure Blob Storage](how-to-store-data-blob.md)használatával.
