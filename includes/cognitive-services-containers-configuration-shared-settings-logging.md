---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 873fd8cbc211f098c93b8fb3fbe701e4a34d8487
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68320518"
---
A `Logging` beállítások ASP.NET a tároló Core naplózási támogatásának kezelése. Ugyanazokat a konfigurációs beállításokat és értékeket használhatja a tárolóhoz, amelyeket egy ASP.NET Core alkalmazáshoz használ. 

A tároló a következő naplózási szolgáltatókat támogatja:

|Szolgáltató|Cél|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|A ASP.NET `Console` Core naplózási szolgáltató. A naplózási szolgáltató összes ASP.NET alapkonfigurációs beállítása és alapértelmezett értéke támogatott.|
|[Hibakeresés](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|A ASP.NET `Debug` Core naplózási szolgáltató. A naplózási szolgáltató összes ASP.NET alapkonfigurációs beállítása és alapértelmezett értéke támogatott.|
|[Disk](#disk-logging)|A JSON naplózási szolgáltató. Ez a naplózási szolgáltató naplóadatokat ír a kimeneti csatlakoztatásba.|

Ez a tárolóparancs JSON formátumban tárolja a naplózási adatokat a kimeneti csatlakoztatáshoz:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Ez a tárolóparancs a kapcsolattal `dbug`ellátott hibakeresési adatokat jeleníti meg, amíg a tároló fut:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Lemeznaplózás

A `Disk` naplózási szolgáltató a következő konfigurációs beállításokat támogatja:

| Név | Adattípus | Leírás |
|------|-----------|-------------|
| `Format` | Sztring | A naplófájlok kimeneti formátuma.<br/> **Megjegyzés:** Ezt az értéket `json` úgy kell beállítani, hogy engedélyezze a naplózási szolgáltatót. Ha ezt az értéket anélkül adja meg, hogy egy tároló példánya közben is megadna egy kimeneti csatlakoztatást, hiba történik. |
| `MaxFileSize` | Egész szám | A naplófájl maximális mérete megabájtban (MB). Ha az aktuális naplófájl mérete eléri vagy meghaladja ezt az értéket, a naplózási szolgáltató új naplófájlt indít el. Ha a -1 van megadva, a naplófájl méretét csak a kimeneti csatlakoztatás maximális fájlmérete korlátozza, ha van ilyen. Az alapértelmezett érték az 1. |

A ASP.NET Core naplózási támogatás konfigurálásáról a [Beállítások fájlkonfiguráció](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)című témakörben olvashat bővebben.

