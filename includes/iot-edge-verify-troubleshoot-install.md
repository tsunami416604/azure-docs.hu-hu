---
title: A telepítés és a kiépítés után ellenőrizze a sikerességet és a hibakeresést
description: fájlbefoglalás
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/06/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 295769e5d4924e529b296dbb0b9d405ee197c1db
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979540"
---
## <a name="verify-successful-setup"></a>A sikeres telepítés ellenőrzése

Ellenőrizze az IoT Edge-szolgáltatás állapotát. A lista futtatásaként kell szerepelnie.  

# <a name="linux"></a>[Linux](#tab/linux)

```bash
systemctl status iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
Get-Service iotedge
```

---

A szolgáltatási naplók vizsgálata.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
journalctl -u iotedge --no-pager --no-full
```

# <a name="windows"></a>[Windows](#tab/windows)

Ha befejezte a IoT Edge futtatókörnyezet telepítését, akkor előfordulhat, hogy az **üzembe helyezés-IoTEdge** és az **inicializálás-IoTEdge**futtatása között elérkezett hibák listája látható. A rendszer ezeket a hibákat várta, mivel a szolgáltatás a konfigurálás előtt megpróbálja elindítani a szolgáltatást.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

---

A [hibaelhárítási eszköz](../articles/iot-edge/troubleshoot.md#run-the-check-command) futtatásával keresse meg a leggyakoribb konfigurációs és hálózati hibákat.

```powershell
iotedge check
```

Amíg nem telepíti az első modult az eszközön való IoT Edgere, a rendszer nem telepíti a **$edgeHub** rendszermodult az eszközre. Ennek eredményeképpen az automatikus ellenőrzés hibaüzenetet ad vissza a `Edge Hub can bind to ports on host` kapcsolat ellenőrzésekor. Ez a hiba figyelmen kívül hagyható, kivéve, ha egy modulnak az eszközre történő telepítése után következik be.

Végül a futó modulok listázása:

```powershell
iotedge list
```

Új telepítés után az egyetlen modulnak kell megjelennie a **edgeAgent**.

## <a name="tips-and-troubleshooting"></a>Tippek és hibaelhárítás

Az erőforrás által korlátozott eszközök esetében erősen ajánlott a *OptimizeForPerformance* környezeti változót *hamis* értékre állítani a [hibaelhárítási útmutató](../articles/iot-edge/troubleshoot.md)utasításai szerint.

Ha az eszköz nem tud csatlakozni a IoT Hubhoz, és a hálózat proxykiszolgálót tartalmaz, kövesse az [IoT Edge eszköz konfigurálása a proxykiszolgálón keresztül történő kommunikációhoz](../articles/iot-edge/how-to-configure-proxy-support.md)című szakasz lépéseit.

# <a name="linux"></a>[Linux](#tab/linux)

Linux-eszközökön emelt szintű jogosultságok szükségesek a parancsok futtatásához `iotedge` . A futtatókörnyezet telepítése után jelentkezzen ki a gépről, és jelentkezzen be ismét az engedélyek automatikus frissítéséhez. Addig a paranccsal `sudo` futtathat parancsokat emelt szintű jogosultságokkal.

# <a name="windows"></a>[Windows](#tab/windows)

Windows-tárolókat futtató Windows-eszközökön a Moby Container Engine IoT Edge részeként települt. A Moby Engine a Docker Desktoptal párhuzamosan fut. A parancsokat akkor használhatja, `docker` Ha az eszközön lévő tárolókkal közvetlenül szeretne kommunikálni. Azonban kifejezetten meg kell céloznia a Moby Engine-t abban az esetben, ha a Docker Desktop is telepítve van az eszközön.

Ha például az összes Docker-lemezképet szeretné listázni, használja a következő parancsot:

```powershell
docker images
```

Az összes Moby-lemezkép listázásához módosítsa ugyanazt a parancsot, és mutasson rá a Moby Engine-re:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

A motor URI-ja megjelenik a telepítési parancsfájl kimenetében, vagy megkeresheti a config. YAML fájl Container Runtime Settings szakaszában.

![moby_runtime URI a config. YAML](./media/iot-edge-verify-troubleshoot-install/moby-runtime-uri.png)

---
