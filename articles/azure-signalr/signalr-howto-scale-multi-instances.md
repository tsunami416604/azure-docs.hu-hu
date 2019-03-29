---
title: Több példány az Azure SignalR Service méretezése
description: Számos méretezési esetekben gyakran kell több példány kiépítését és konfigurálását a nagy léptékű üzembe helyezés létrehozása együtt használja őket. Például a horizontális skálázás van szükség, támogatja a több példányt.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: e284a0492774e02cab79db6d9006c1718a7fcfc9
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58623183"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Hogyan méretezése a SignalR Service több példányra?
A legújabb SignalR Service SDK támogatja a több végpontot SignalR Service-példányokhoz. Ez a funkció segítségével méretezheti az egyidejű kapcsolatok, vagy a régiók közötti üzenetkezelés használata.

## <a name="for-aspnet-core"></a>For ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Több végpont hozzáadása a konfiguráció hogyan?

A kulcs konfigurációs `Azure:SignalR:ConnectionString` vagy `Azure:SignalR:ConnectionString:` SignalR Service kapcsolati karakterlánc.

Ha a kulcs kezdődik `Azure:SignalR:ConnectionString:`, formátumúnak kell lennie `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, ahol `Name` és `EndpointType` tulajdonságaiként a `ServiceEndpoint` objektumot, és elérhető kódot.

Hozzáadhat több példány kapcsolati karakterláncok az alábbi `dotnet` parancsokat:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Több végpont hozzáadása a kód hogyan?

A `ServicEndpoint` osztály Azure SignalR Service-ben a végpont tulajdonságainak leírása jelenik meg.
Több példány végpont használata az Azure SignalR Service SDK segítségével konfigurálhatja:
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

### <a name="how-to-customize-endpoint-router"></a>Hogyan szabhatja testre a végpont útválasztó?

Alapértelmezés szerint az SDK-t használja a [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) végpontokat felvenni.

#### <a name="default-behavior"></a>Alapértelmezett viselkedés 
1. Ügyfél alkalmazáskérelmek irányítása

    Ha ügyfél `/negotiate` az app-kiszolgálóval. SDK alapértelmezés szerint **véletlenszerűen kiválaszt** egy végpontja elérhető Szolgáltatásvégpontok készletéből.

2. Kiszolgáló üzenet-útválasztása

    Ha a * üzenetet küld egy adott ** kapcsolat x és a cél kapcsolat továbbítja a rendszer az aktuális kiszolgáló, az üzenet közvetlenül csatlakoztatott, hogy a végpont kerül. Ellenkező esetben az üzenetek vannak küldött minden Azure SignalR-végpontra.

#### <a name="customize-routing-algorithm"></a>Útválasztási algoritmust testreszabása
A saját útválasztó hozhat létre, ha alapos ismeretére azonosításához milyen végpontokat, az üzenetek el kell küldeni.

Egyéni útválasztó van megadva. például ha csoportok, `east-` mindig látogasson el a végpont nevű `east`:

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

Egy másik példa az alábbi, amely felülbírálja az alapértelmezett viselkedés egyeztetni, jelölje be a végpontok attól függ, ahol az alkalmazás-kiszolgáló.

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

Ne felejtse el regisztrálni az útválasztót, hogy DI-tároló használatával:

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

## <a name="for-aspnet"></a>For ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Több végpont hozzáadása a konfiguráció hogyan?

A kulcs konfigurációs `Azure:SignalR:ConnectionString` vagy `Azure:SignalR:ConnectionString:` SignalR Service kapcsolati karakterlánc.

Ha a kulcs kezdődik `Azure:SignalR:ConnectionString:`, formátumúnak kell lennie `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, ahol `Name` és `EndpointType` tulajdonságaiként a `ServiceEndpoint` objektumot, és elérhető kódot.

Hozzáadhat több példány kapcsolati sztringek az `web.config`:

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

### <a name="how-to-add-multiple-endpoints-from-code"></a>Több végpont hozzáadása a kód hogyan?

A `ServicEndpoint` osztály Azure SignalR Service-ben a végpont tulajdonságainak leírása jelenik meg.
Több példány végpont használata az Azure SignalR Service SDK segítségével konfigurálhatja:

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

### <a name="how-to-customize-router"></a>Hogyan szabhatja testre az útválasztó?

Az egyetlen különbség az ASP.NET SignalR és az ASP.NET Core SignalR között nem http környezet típusú `GetNegotiateEndpoint`. Az ASP.NET SignalR, célszerű a [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) típusa.

Az alábbi, az egyéni példa egyeztetni az ASP.NET SignalR:

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

Ne felejtse el regisztrálni az útválasztót, hogy DI-tároló használatával:

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

## <a name="configuration-in-cross-region-scenarios"></a>Konfiguráció a régiók közötti forgatókönyvek

A `ServiceEndpoint` objektum rendelkezik egy `EndpointType` értékkel rendelkező tulajdonság `primary` vagy `secondary`.

`primary` a végpontok olyan előnyben részesített végpontok ügyfél forgalom fogadására és megbízhatóbb hálózati kapcsolatokat; kell tekinteni `secondary` végpontok minősülnek, kevésbé megbízható hálózati kapcsolattal rendelkezik, és csak véve kiszolgáló ügyfél forgalmát, például teszi közzé az üzeneteket, nem pedig a licenckiszolgáló forgalmának véve ügyfél használ.

A régiók közötti esetben hálózati instabil lehet. Egy alkalmazás-kiszolgálóhoz tartozó *USA keleti RÉGIÓJA*, ugyanaz a SignalR Service-végpont található *USA keleti Régiójában* régió beállítható `primary` , más régiókban lévő végpontok van megjelölve `secondary`. Ebben a konfigurációban a Szolgáltatásvégpontok más régiókban is **kap** ez üzeneteit *USA keleti RÉGIÓJA* alkalmazáskiszolgáló, de nem lesz kell nincs **régiók közötti** ügyfeleket átirányítva Ezen alkalmazások kiszolgálói. Az architektúra az alábbi ábrán látható:

![Kereszt-földrajzi infrastruktúra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Amikor egy ügyfél megpróbálja `/negotiate` az app-kiszolgálóval, az alapértelmezett útválasztóval SDK **véletlenszerűen kiválaszt** elérhető a készletből egy végpontot `primary` végpontok. Ha végpont érhető el, majd az SDK-t **véletlenszerűen kiválaszt** az összes rendelkezésre álló `secondary` végpontok. A végpont van megjelölve **elérhető** amikor a kiszolgáló és a szolgáltatásvégpontot közötti kapcsolat működik-e.

Régiók közötti forgatókönyvben, ha egy ügyfél megpróbálja `/negotiate` lévő üzemeltetett alkalmazás-kiszolgálóval *USA keleti Régiójában*, alapértelmezett azt mindig értéket ad vissza a `primary` ugyanabban a régióban található végponti. Ha az összes *USA keleti Régiójában* végpontok nem érhetők el, a rendszer átirányítja az ügyfél más régiókban lévő végpontok. Feladatátvétel az alábbi szakasz részletesen ismertetjük a forgatókönyvet.

![Normál egyeztetése](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Feladatátvétel

Ha az összes `primary` végpontok nem érhetők el, ügyfél `/negotiate` kiválasztja az elérhető `secondary` végpontok. A feladatátvétel mechanizmus megköveteli, hogy minden végpont szolgáljon `primary` legalább egy alkalmazás kiszolgálói végpontot.

![Feladatátvétel](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megismerhette több példány konfigurálása a skálázás, a horizontális skálázás és a régiók közötti forgatókönyvek ugyanazt az alkalmazást.

Több végpont által támogatott magas rendelkezésre állási és vészhelyreállítási forgatókönyvek is használható.

> [!div class="nextstepaction"]
> [A telepítő SignalR Service vészhelyreállítás és magas rendelkezésre állás](./signalr-concept-disaster-recovery.md)
