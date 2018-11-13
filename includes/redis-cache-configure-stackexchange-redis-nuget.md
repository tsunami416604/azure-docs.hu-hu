---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: ba73c481dace43dc72f6740a94bca362763dabac
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572262"
---
A .NET-alkalmazások képesek használni a **StackExchange.Redis** gyorsítótárügyfelet, amely a Visual Studióban konfigurálható a gyorsítótár-ügyfélalkalmazások konfigurálását leegyszerűsítő NuGet-csomagokkal. 

> [!NOTE]
> További információkat a [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) github-oldalon és a [StackExchange.Redis gyorsítótárügyfél dokumentációjában](http://github.com/StackExchange/StackExchange.Redis#documentation) talál.
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

A NuGet-csomag letölti és hozzáadja az ügyfélalkalmazás számára szükséges szerelvényhivatkozásokat az Azure Redis Cache a StackExchange.Redis gyorsítótárügyféllel történő eléréséhez.

> [!NOTE]
> Ha a projektet korábban a StackExchange.Redis használatára konfigurálta, a **NuGet-csomagkezelőben** ellenőrizheti, hogy elérhető-e új frissítés a csomaghoz. Keressen, és telepítheti a StackExchange.Redis NuGet-csomag frissített verzióját, kattintson a **frissítések** a a **NuGet-Csomagkezelő** ablak. Ha a StackExchange.Redis NuGet-csomaghoz elérhetővé válik egy frissítés, frissítheti a projektjét is, hogy az a csomag frissített verzióját használja.
> 
> 

Úgy is telepítheti a StackExchange.Redis NuGet csomagot, hogy a **NuGet Package Manager** (NuGet-csomagkezelő) > **Package Manager Console** (Csomagkezelő konzol) elemre kattint a **Tools** (Eszközök) menüben, és a következő parancsot futtatja a **Package Manager Console** (Csomagkezelő konzol) ablakból.
    
```
Install-Package StackExchange.Redis
```
