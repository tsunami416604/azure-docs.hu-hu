---
title: IoT Edge eszköz helyi tárterületének használata modulból – Azure IoT Edge | Microsoft Docs
description: Használjon környezeti változókat, és hozzon létre beállításokat a modul IoT Edge eszköz helyi tárolóhoz való hozzáférésének engedélyezéséhez.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/12/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2526f33f0053b5805394a4a898af88d86187066c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301281"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Modulok elérésének biztosítása az eszköz helyi tárolójához

Az adatok Azure Storage-szolgáltatásokkal vagy az eszköz tároló-tárolóban való tárolása mellett a tárolót is kioszthatja a gazdagépen IoT Edge eszközön is, így a megbízhatóságot, különösen a kapcsolat nélküli üzemmódban.

Ha a gazdagép rendszerén szeretné beállítani a tárolót, hozzon létre egy környezeti változót a modulhoz, amely a tároló egyik tárolási mappájára mutat. Ezt követően a létrehozási beállításokkal társítsa a Storage mappát a gazdaszámítógép egyik mappájához.

Ha például engedélyezni szeretné az IoT Edge hub számára az üzenetek tárolását az eszköz helyi tárolójában, és később lekéri őket, a környezeti változókat és a létrehozási beállításokat a Azure Portal a **speciális Edge-futtatókörnyezet beállításainak konfigurálása szakaszában állíthatja be.** szakasz.

1. A IoT Edge hub és a IoT Edge Agent esetében is adjon hozzá egy **storageFolder** nevű környezeti változót, amely a modul egyik könyvtárára mutat.
1. A IoT Edge hub és a IoT Edge ügynök esetében adja hozzá a kötéseket, hogy a gazdagépen lévő helyi könyvtárat összekapcsolja a modul egyik könyvtárába. Példa:

   ![Létrehozási beállítások és környezeti változók hozzáadása a helyi tárolóhoz](./media/how-to-access-host-storage-from-module/offline-storage.png)

A helyi tárolót közvetlenül a telepítési jegyzékben is konfigurálhatja. Példa:

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

Cserélje le a `<HostStoragePath>` és a `<ModuleStoragePath>` értéket a gazdagép és a modul tárolási útvonalára; mindkét értéknek abszolút elérési útnak kell lennie.

Linux rendszeren például a `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` érték azt jelenti, hogy a gazdagépen lévő **/etc/iotedge/Storage** a tárolóban lévő **/iotedge/Storage/** van leképezve. Egy Windows rendszerű rendszeren a `"Binds":["C:\\temp:C:\\contemp"]` érték azt jelenti, hogy a (z) **c: \\temp** a gazdagépen a (z) **c: \\contemp** könyvtárba van leképezve a tárolóban.

Emellett a Linux-eszközökön ellenőrizze, hogy a modul felhasználói profilja rendelkezik-e a gazdagép rendszerkönyvtárához szükséges olvasási, írási és végrehajtási engedélyekkel. Visszatérve a korábbi példához, amely lehetővé teszi, hogy IoT Edge hub üzeneteket tároljon az eszköz helyi tárolójában, engedélyeket kell adnia a felhasználói profiljához, az UID 1000-hez. (A IoT Edge ügynök root-ként működik, ezért nincs szükség további engedélyekre.) A Linux rendszereken több módon is kezelhetők a címtár-engedélyek, például a `chown` használatával módosíthatja a könyvtár tulajdonosát, majd `chmod` az engedélyek módosításához, például:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

További részleteket a [Docker docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)szolgáltatásbeli létrehozási lehetőségekről talál.
