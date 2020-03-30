---
title: Méretezés több példánysal – Azure SignalR szolgáltatás
description: Számos skálázási forgatókönyvben az ügyfélnek gyakran több példányt kell kiépítenie, és konfigurálnia kell őket együtt, egy nagyméretű telepítés létrehozásához. Például a szilánkok több példány támogatását igényli.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158161"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Hogyan lehet a SignalR szolgáltatást több példánysal méretezni?
A legújabb SignalR Service SDK támogatja a SignalR Service-példányok több végpontját. Ezzel a funkcióval méretezheti az egyidejű kapcsolatokat, vagy használhatja régióközi üzenetküldéshez.

## <a name="for-aspnet-core"></a>A ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hogyan lehet hozzáadni több végpontot a config?

Konfiguráció a `Azure:SignalR:ConnectionString` kulccsal vagy `Azure:SignalR:ConnectionString:` a SignalR service kapcsolati karakterláncával.

Ha a kulcs `Azure:SignalR:ConnectionString:`a programmal `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`kezdődik, `Name` `EndpointType` annak formátumban `ServiceEndpoint` kell lennie, hol és az objektum tulajdonságai, és a kódból elérhető.

Több példánykapcsolati karakterláncot is `dotnet` hozzáadhat a következő parancsokkal:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hogyan lehet több végpontot hozzáadni a kódból?

Egy `ServicEndpoint` osztály egy Azure SignalR-szolgáltatás végpont tulajdonságainak leírására kerül bevezetésre.
Az Azure SignalR Service SDK használatakor több példányvégpontot is konfigurálhat a következő keresztül:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Hogyan lehet testre végpont router?

Alapértelmezés szerint az SDK a [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) segítségével veszi fel a végpontokat.

#### <a name="default-behavior"></a>Alapértelmezett viselkedés 
1. Ügyfélkérelem-útválasztás

    Amikor `/negotiate` az ügyfél az alkalmazás szerver. Alapértelmezés szerint az SDK **véletlenszerűen kiválaszt** egy végpontot a rendelkezésre álló szolgáltatásvégpontok készletéből.

2. Kiszolgálói üzenetútválasztás

    Amikor az *üzenet küldése egy adott **kapcsolatra***, és a célkapcsolat az aktuális kiszolgálóra kerül, az üzenet közvetlenül az adott csatlakoztatott végponthoz kerül. Ellenkező esetben az üzenetek et minden Azure SignalR-végpontsugározza.

#### <a name="customize-routing-algorithm"></a>Útválasztási algoritmus testreszabása
Létrehozhat saját útválasztót, ha speciális ismeretekkel rendelkezik az üzenetek által az üzenetekhez irányuló végpontok azonosításához.

Az egyéni útválasztók az alábbiakban például `east-` akkor vannak definiálva, amikor a kezdő csoportok mindig a következő végpontra lépnek: `east`

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Egy másik példa az alábbi, amely felülbírálja az alapértelmezett egyeztetési viselkedés, a végpontok kiválasztása attól függ, hogy hol található az alkalmazáskiszolgáló.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Ne felejtsük el, hogy regisztrálja az útválasztó di konténer segítségével:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>A ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hogyan lehet hozzáadni több végpontot a config?

Konfiguráció a `Azure:SignalR:ConnectionString` kulccsal vagy `Azure:SignalR:ConnectionString:` a SignalR service kapcsolati karakterláncával.

Ha a kulcs `Azure:SignalR:ConnectionString:`a programmal `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`kezdődik, `Name` `EndpointType` annak formátumban `ServiceEndpoint` kell lennie, hol és az objektum tulajdonságai, és a kódból elérhető.

Több példánykapcsolati karakterláncot `web.config`is hozzáadhat:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hogyan lehet több végpontot hozzáadni a kódból?

Egy `ServicEndpoint` osztály egy Azure SignalR-szolgáltatás végpont tulajdonságainak leírására kerül bevezetésre.
Az Azure SignalR Service SDK használatakor több példányvégpontot is konfigurálhat a következő keresztül:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Hogyan lehet testre router?

Az egyetlen különbség ASP.NET SignalR és ASP.NET Core SignalR a http környezet típusa `GetNegotiateEndpoint`. A ASP.NET SignalR esetében [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) típusú.

Az alábbiakban az egyéni egyeztetés ipéldát ASP.NET SignalR:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Ne felejtsük el, hogy regisztrálja az útválasztó di konténer segítségével:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Konfiguráció régiók közötti forgatókönyvekben

Az `ServiceEndpoint` objektum `EndpointType` tulajdonsága `primary` vagy `secondary`értéke.

`primary`a végpontok előnyben részesített végpontok az ügyfélforgalom fogadásához, és megbízhatóbb hálózati kapcsolatokkal rendelkeznek; `secondary` a végpontok kevésbé megbízható hálózati kapcsolattal rendelkeznek, és csak a kiszolgáló ügyfélforgalomhoz való átvételére szolgálnak, például műsorszórási üzenetekre, nem pedig az ügyfél kiszolgálói forgalomra való átvételére.

Régiók közötti esetekben a hálózat instabil lehet. Az USA keleti *régiójában*található egyik alkalmazáskiszolgáló esetében az *USA keleti régiójában* található SignalR service végpont konfigurálható végpontként és végpontként `primary` más `secondary`régiókban. Ebben a konfigurációban a más régiókban lévő szolgáltatásvégpontok üzeneteket **fogadhatnak** *az USA keleti régiójában* alkalmazáskiszolgálóról, de nem lesznek **régióközi** ügyfelek az alkalmazáskiszolgálóra. Az architektúra az alábbi ábrán látható:

![Cross-Geo Infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Amikor egy `/negotiate` ügyfél az alkalmazáskiszolgálóval próbálkozik, az alapértelmezett útválasztóval az SDK `primary` **véletlenszerűen kiválaszt** egy végpontot a rendelkezésre álló végpontok készletéből. Ha az elsődleges végpont nem érhető el, az SDK `secondary` **véletlenszerűen kiválasztja** az összes elérhető végpontot. A végpont **elérhetőként** van megjelölve, ha a kiszolgáló és a szolgáltatásvégpont közötti kapcsolat él.

Régiók közötti forgatókönyv esetén, amikor `/negotiate` egy ügyfél az *USA keleti régiójában*üzemeltetett alkalmazáskiszolgálóval próbálkozik, alapértelmezés szerint mindig az ugyanabban a `primary` régióban található végpontot adja vissza. Ha *az USA keleti régióbeli* végpontjai nem érhetők el, az ügyfél más régiók végpontjaira lesz átirányítva. Az alábbi feladatátvételi szakasz részletesen ismerteti a forgatókönyvet.

![Normál egyeztetés](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Feladatátvétel

Ha `primary` az összes végpont nem érhető `/negotiate` el, `secondary` az ügyfél a rendelkezésre álló végpontok közül választ. Ez a feladatátvételi mechanizmus megköveteli, `primary` hogy minden végpont végpontként szolgáljon legalább egy alkalmazáskiszolgáló számára.

![Feladatátvétel](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megtudhatja, hogyan konfigurálhat több példányt ugyanabban az alkalmazásban méretezési, horizontális és régiók közötti forgatókönyvek.

Több végpont támogatja is használható magas rendelkezésre állású és vész-helyreállítási forgatókönyvek.

> [!div class="nextstepaction"]
> [SignalR-szolgáltatás beállítása a vészhelyreállításhoz és a magas rendelkezésre álláshoz](./signalr-concept-disaster-recovery.md)
