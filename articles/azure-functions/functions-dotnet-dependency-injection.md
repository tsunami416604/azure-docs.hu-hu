---
title: Függőségi beszúrást használata a .NET Azure Functions szolgáltatásban
description: Megtudhatja, hogyan használja a függőségi beszúrást regisztrálásához és a szolgáltatások használata a .NET-es függvényeket
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: az Azure functions, függvények, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2019
ms.author: jehollan
ms.openlocfilehash: 2044718d2ec7a7acc58e1e7ba9ba04ec5caf16b3
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408450"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Függőségi beszúrást használata a .NET Azure Functions szolgáltatásban

Az Azure Functions támogatja a függőségi injektálási (DI) szoftver tervezési mintát, amely a technika eléréséhez [vezérlő invertálásának (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) osztályok és a függőségek között.

Az Azure Functions az ASP.NET Core függőségi beszúrást funkciók épül.  Tisztában kell lennie a szolgáltatások, élettartam és tervezési mintáinak [ASP.NET Core függőségi beszúrást](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) függvények a használatuk előtt.

## <a name="installing-dependency-injection-packages"></a>Függőségi injektálási csomagok telepítése

Függőségi injektálási funkcióinak használatához szüksége lesz a NuGet-csomagot, amely elérhetővé teszi azokat az API-kat tartalmazza.

```powershell
Install-Package Microsoft.Azure.Functions.Extensions
```

## <a name="registering-services"></a>Szolgáltatás regisztrálása

Szolgáltatások regisztrálásához konfigurálása metódus létrehozása és -összetevők hozzáadása egy `IFunctionsHostBuilder` példány.  Az Azure Functions állomás létrehoz egy `IFunctionsHostBuilder` és továbbadja azt közvetlenül a beállított módszer.

Regisztrálja a módszer konfigurálása, hozzá kell adnia egy szerelvény attribútum típusát megadó a módszer használatával konfigurálja a `FunctionsStartup` attribútum.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="service-lifetimes"></a>Szolgáltatás-élettartam

Az Azure Function apps adja meg, az azonos szolgáltatás élettartamának [ASP.NET függőségi beszúrást](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes), átmeneti, hatókörrel rendelkező, és egypéldányos.

A függvényalkalmazás egy hatókörrel rendelkező szolgáltatás élettartamának megegyezik a függvény végrehajtási idejének magyarázata. Hatókörön belüli szolgáltatások végrehajtásonkénti egyszer létrehozni.  A végrehajtás során, hogy a szolgáltatás újabb kérelmek újrafelhasználását teszi lehetővé annak a példánynak.  A singleton szolgáltatás élettartama megegyezik a gazdagép élettartama, és a függvénykivételek azon a példányon keresztül újrahasznosított.

Egyszeres élettartama szolgáltatások ajánlott kapcsolatok és az ügyfeleknek, például egy `SqlConnection`, `CloudBlobClient`, vagy `HttpClient`.

Megtekintése vagy letöltése egy [különböző élettartam-minta](https://aka.ms/functions/di-sample).

## <a name="logging-services"></a>Naplózási szolgáltatások

Ha saját naplózási szolgáltatói van szüksége, ajánlott módja regisztrálni egy `ILoggerProvider`.  Az Application Insights, a Functions az Application Insights automatikusan hozzáadja az Ön számára.  

> [!WARNING]
> Ne adjon hozzá `AddApplicationInsightsTelemetry()` a szolgáltatások gyűjtemény, szolgáltatások, amelyek ütköznek egymással, regisztrálja a rendszer a környezet által biztosított. 
 
## <a name="function-app-provided-services"></a>Függvény a megadott app servicesben

A függvény gazdagépen fognak regisztrálni számos szolgáltatás magát.  Az alábbiakban a szolgáltatásokat, amelyek függőség biztonságosak.  Egyéb szolgáltatások működtetéséhez nem támogatottak regisztrálásához, vagy függenek.  Ha más szolgáltatások szeretné-e függőséget, kérjük [hozzon létre egy probléma és vitafórum a Githubon](https://github.com/azure/azure-functions-host).

|Szolgáltatás típusa|Élettartam|Leírás|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Egypéldányos|Futásidejű konfigurációja|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Egypéldányos|Biztosítja a gazdagép-példány azonosítója|

### <a name="overriding-host-services"></a>Szolgáltatások működtetéséhez felülbírálása

Felülbírálja a gazdagép által nyújtott szolgáltatások jelenleg nem támogatott.  Ha van olyan szolgáltatásokat felülbírálása kívánja, [hozzon létre egy probléma és vitafórum a Githubon](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [A függvényalkalmazás figyelése](functions-monitoring.md)
* [Functions – ajánlott eljárások](functions-best-practices.md)