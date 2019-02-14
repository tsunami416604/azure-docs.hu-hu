---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: aa93fb9779e6cda41df52f7979e42348948544ea
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246932"
---
A `Logging` beállítások kezelése a tároló az ASP.NET Core-naplózás támogatása. A tárolót használja az ASP.NET Core alkalmazás használhatja az ugyanazon konfigurációs beállításokat és értékeket. 

A következő naplózási szolgáltatók támogatottak a tárolót:

|Szolgáltató|Cél|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|Az ASP.NET Core `Console` naplózási szolgáltató. Az ASP.NET Core-konfigurációs beállításokat és a naplózás szolgáltatóhoz tartozó alapértelmezett értékeket támogatottak.|
|[Hibakeresés](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|Az ASP.NET Core `Debug` naplózási szolgáltató. Az ASP.NET Core-konfigurációs beállításokat és a naplózás szolgáltatóhoz tartozó alapértelmezett értékeket támogatottak.|
|[Lemez](#disk-logging)|A JSON-naplózás szolgáltató. A naplózás szolgáltató naplóadatokat ír a kimeneti csatlakoztatási.|

### <a name="disk-logging"></a>Lemez naplózása

A `Disk` naplózási szolgáltató támogatja-e a következő beállításokat:  

| Name (Név) | Adattípus | Leírás |
|------|-----------|-------------|
| `Format` | Karakterlánc | A kimeneti formátum a rendszernapló fájljaiban.<br/> **Megjegyzés:** Ezt az értéket kell beállítani `json` a naplózás szolgáltatónak. Ezt az értéket egy kimeneti csatlakoztatási közben hárítható el egy tároló megadása nélkül, ha hiba történik. |
| `MaxFileSize` | Egész szám | A maximális méretét megabájtban (MB), a naplófájlok. Ha az aktuális naplófájl méretét megfelel-e vagy meghaladja ezt az értéket, egy új naplófájl indítja el a naplózás szolgáltató. Ha meg van adva a -1, a naplófájl méretét csak korlátozza a maximális méretet, ha bármely, a kimeneti csatlakoztatási. Az alapértelmezett érték az 1. |

ASP.NET Core-naplózás támogatást konfigurálásával kapcsolatos további információkért lásd: [fájl konfigurációs beállítások](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).
