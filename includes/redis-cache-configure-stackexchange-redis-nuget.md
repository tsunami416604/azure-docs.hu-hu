---
author: yegu-ms
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: yegu
ms.openlocfilehash: 61e93e3700b9a396d2ac4fdcbb51fc5c874cf9cb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "68286271"
---
A .NET-alkalmazások képesek használni a **StackExchange.Redis** gyorsítótárügyfelet, amely a Visual Studióban konfigurálható a gyorsítótár-ügyfélalkalmazások konfigurálását leegyszerűsítő NuGet-csomagokkal. 

> [!NOTE]
> További információkért tekintse meg a [StackExchange. Redis](https://github.com/StackExchange/StackExchange.Redis) GitHub-oldalt és a [StackExchange. Azure cache-t a Redis-ügyfél dokumentációjában](https://github.com/StackExchange/StackExchange.Redis#documentation).
>
>

Egy ügyfélalkalmazás a Visual Studióban a StackExchange.Redis NuGet-csomag használatával történő konfigurálásához kattintson a jobb gombbal a projektre a **Solution Explorer** (Megoldáskezelő) felületén, majd válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget. 

![NuGet-csomagok kezelése](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Írja be a **StackExchange.Redis** vagy a **StackExchange.Redis.StrongName** kifejezést a keresőmezőbe, az eredmények közül válassza ki a kívánt verziót, majd kattintson az **Install** (Telepítés) gombra.

> [!NOTE]
> Ha inkább a **StackExchange.Redis** ügyfélkönyvtár erős elnevezésű verzióját kívánja használni, válassza a **StackExchange.Redis.StrongName**, ellenkező esetben pedig a **StackExchange.Redis** lehetőséget.
>
>

![StackExchange.Redis NuGet-csomag](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

A NuGet-csomag letölti és hozzáadja az ügyfélalkalmazás számára szükséges szerelvény-hivatkozásokat az Azure cache Redis való eléréséhez a StackExchange. Azure cache Redis-ügyfélhez való hozzáféréséhez.

> [!NOTE]
> Ha a projektet korábban a StackExchange.Redis használatára konfigurálta, a **NuGet-csomagkezelőben** ellenőrizheti, hogy elérhető-e új frissítés a csomaghoz. A StackExchange. Redis NuGet-csomag frissített verzióinak kereséséhez és telepítéséhez kattintson a **frissítések** lehetőségre a **NuGet csomagkezelő** ablakban. Ha a StackExchange.Redis NuGet-csomaghoz elérhetővé válik egy frissítés, frissítheti a projektjét is, hogy az a csomag frissített verzióját használja.
>
>

Úgy is telepítheti a StackExchange.Redis NuGet csomagot, hogy a **NuGet Package Manager** (NuGet-csomagkezelő) > **Package Manager Console** (Csomagkezelő konzol) elemre kattint a **Tools** (Eszközök) menüben, és a következő parancsot futtatja a **Package Manager Console** (Csomagkezelő konzol) ablakból.

```
Install-Package StackExchange.Redis
```
