---
title: CreateOptions írása modulokhoz - Azure IoT Edge | Microsoft dokumentumok
description: A createOptions használata a központi telepítési jegyzékben a modulok futásidőben történő konfigurálásához
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550343"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>A tárolók létrehozásának beállításai az IoT Edge-modulokhoz

A deployment jegyzékben található **createOptions** paraméter lehetővé teszi a modultárolók futásidőben történő konfigurálását. Ez a paraméter kibővíti a modulok feletti vezérlést, és lehetővé teszi az olyan feladatok elvégzését, mint a modul hozzáférésének engedélyezése vagy korlátozása a gazdaeszköz erőforrásaihoz, vagy a hálózatkonfigurálás.

Az IoT Edge-modulok Docker-kompatibilis tárolókként vannak megvalósítva az IoT Edge-eszközön. A Docker számos lehetőséget kínál a tárolók létrehozására, és ezek a lehetőségek az IoT Edge-modulokra is vonatkoznak. További információ: [Docker container create options](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Létrehozási beállítások formázása

Az IoT Edge központi telepítési jegyzékfájljának elfogadja a JSON formátumú létrehozási beállításokat. Például vegye be a létrehozási beállításokat, amelyek automatikusan szerepelnek minden edgeHub modulhoz:

```json
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
```

Ez az edgeHub-példa a **HostConfig.PortBindings** paramétert használja a tárolóban lévő elérhető portok leképezéséhez a gazdaeszköz portjához.

Ha az Azure IoT Tools-bővítményeket használja a Visual Studio vagy a Visual Studio-kódhoz, a **telepítési.template.json** fájlba írhatja a létrehozási beállításokat JSON formátumban. Ezután a bővítmény használatával hozza létre az IoT Edge-megoldás, vagy a központi telepítési jegyzékfájl létrehozása, akkor stringify a JSON az IoT Edge futásidejű elvárja. Példa:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

A létrehozási beállítások írásának `docker inspect` egyik tippje a parancs használata. A fejlesztési folyamat részeként futtassa `docker run <container name>`a modult helyileg a használatával. Ha már a modul működik, ahogy `docker inspect <container name>`szeretné, fuss . Ez a parancs JSON formátumban adja ki a modul részleteit. Keresse meg a beállított paramétereket, és másolja a JSON-t. Példa:

[![A docker-vizsgálat](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png) eredményei](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

A tárolólétrehozásaszámos forgatókönyvet tesz lehetővé, de az alábbiakban az IoT Edge-megoldások létrehozásakor a leggyakrabban jelennek meg:

* [A modulok hozzáférésének a gazdagép-tároláshoz való hozzáférése](how-to-access-host-storage-from-module.md)
* [Állomásport hozzárendelése modulporthoz](#map-host-port-to-module-port)
* [Modulmemória és processzorhasználat korlátozása](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Állomásport hozzárendelése modulporthoz

Ha a modulnak az IoT Edge-megoldáson kívüli szolgáltatással kell kommunikálnia, és ehhez nem használ üzenet-útválasztást, akkor le kell képeznie egy gazdaportot egy modulporthoz.

>[!TIP]
>Ez a portleképezés nem szükséges a modul-modul kommunikációhoz ugyanazon az eszközön. Ha az A modulnak le kell kérdeznie egy B modulon tárolt API-t, ezt portleképezés nélkül teheti meg. A B modulnak meg kell adnia egy `EXPOSE 8080`portot a dockerfile-ban, például: . Ezután az A modul lekérdezheti az API-t a B modul nevével, például: `http://ModuleB:8080/api`.

Először győződjön meg arról, hogy a modulon belüli port elérhető a kapcsolatok figyeléséhez. Ezt a dockerfile [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) utasításával teheti meg. Például: `EXPOSE 8080`. A tcp protokoll alapértelmezett utasítása, ha nincs megadva, vagy megadhatja az UDP-t.

Ezután használja a **PortBindings** beállítást a [Docker-tároló](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) **HostConfig** csoportjában a create beállításokat a modulban lévő elérhető port leképezéséhez a gazdaeszköz portjához. Ha például a 8080-as portot a modulon belül tette ki, és azt szeretné leképezni a gazdaeszköz 80-as portjára, a template.json fájlban lévő létrehozási beállítások a következő példához hasonlóan fognak kinézni:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

Miután stringified a központi telepítési jegyzékfájl, ugyanaz a konfiguráció a következő példához hasonlóan jelenik meg:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Modulmemória és processzorhasználat korlátozása

Deklarálhatja, hogy a modul a gazdagép erőforrásaiból mennyit használhat. Ez a vezérlő hasznos annak biztosításához, hogy egy modul ne fogyasszon túl sok memóriát vagy processzorhasználatot, és megakadályozza, hogy más folyamatok fussanak az eszközön. Ezeket a beállításokat a [Docker-tároló létrehozása lehetőségeka](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) **HostConfig** csoportban, többek között:

* **Memória**: Memóriakorlát bájtban. Például 268435456 bájt = 256 MB.
* **MemorySwap**: Teljes memóriakorlát (memória + swap). Például 536870912 bájt = 512 MB
* **CpuPeriod**: A CPU-időszak hossza mikroszekundumban. Az alapértelmezett érték 100000, így például egy 25000-es érték a tárolót a PROCESSZOR-erőforrások 25%-ára korlátozza.

A template.json formátumban ezek az értékek a következő példához hasonlóan néznek ki:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Miután a végső központi telepítés jegyzékfájljának karakterláncait, ezek az értékek a következő példához hasonlóan jelennek meg:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>További lépések

További példák a létrehozási beállítások működés közben, lásd az alábbi IoT Edge-minták:

* [Egyéni látás és az Azure IoT Edge egy Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Azure IoT Edge blobtárolási minta](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
