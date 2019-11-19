---
title: Skálázás több példányban – Azure Signaler szolgáltatás
description: Számos skálázási helyzetben a vásárlónak gyakran több példányt kell kiépítenie, és úgy kell konfigurálnia, hogy együtt használja őket a nagyméretű központi telepítés létrehozásához. A horizontális skálázás például több példány támogatását igényli.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158161"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Hogyan méretezheti a Signaler szolgáltatást több példányon?
A legújabb Signaler Service SDK támogatja a Signaler szolgáltatás példányainak több végpontját. Ezzel a szolgáltatással méretezheti az egyidejű kapcsolatokat, vagy felhasználhatja a régiók közötti üzenetkezeléshez.

## <a name="for-aspnet-core"></a>For ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hogyan lehet több végpontot felvenni a konfigurációból?

A konfigurációt a Key `Azure:SignalR:ConnectionString` vagy `Azure:SignalR:ConnectionString:` a Signal Service-kapcsolatok karakterláncához.

Ha a kulcs `Azure:SignalR:ConnectionString:`rel kezdődik, akkor formátum `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`kell lennie, ahol a `Name` és a `EndpointType` a `ServiceEndpoint` objektum tulajdonságai, és elérhetők a kódból.

A következő `dotnet` paranccsal adhat hozzá több példányos kapcsolatok karakterláncot:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hogyan lehet több végpontot felvenni a kódból?

Egy `ServicEndpoint` osztály van bevezetve, amely leírja az Azure Signaler szolgáltatás végpontjának tulajdonságait.
Ha az Azure Signaler Service SDK-t használja a használatával, több példány-végpontot is beállíthat:
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

### <a name="how-to-customize-endpoint-router"></a>Az Endpoint router testreszabása

Alapértelmezés szerint az SDK a [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) használja a végpontok felvételéhez.

#### <a name="default-behavior"></a>Alapértelmezett viselkedés 
1. Ügyfél-kérelem útválasztása

    Ha az ügyfél `/negotiate` az App Serverrel. Az SDK alapértelmezés szerint **véletlenszerűen kiválaszt** egy végpontot az elérhető szolgáltatási végpontok készletéről.

2. Kiszolgálói üzenet útválasztása

    Ha a *üzenetet küld egy adott **kapcsolat*** x és a cél kapcsolat továbbítja a rendszer az aktuális kiszolgáló, az üzenet közvetlenül csatlakoztatott, hogy a végpont kerül. Ellenkező esetben az üzenetek az összes Azure Signaler-végpontra továbbítva lesznek.

#### <a name="customize-routing-algorithm"></a>Útválasztási algoritmus testreszabása
Saját útválasztót is létrehozhat, ha speciális ismeretekkel rendelkezik annak azonosításához, hogy mely végpontoknak kell megjelenniük.

Az alábbi példa egy egyéni útválasztót határoz meg, ha a `east-` kezdődő csoportok mindig a `east`nevű végpontra kerülnek:

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

Egy másik példa alább, amely felülbírálja az alapértelmezett egyeztetési viselkedést, a végpontok kiválasztásához attól függ, hogy hol található az App Server.

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

Ne felejtse el regisztrálni az útválasztót a DI Container használatával:

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

## <a name="for-aspnet"></a>ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hogyan lehet több végpontot felvenni a konfigurációból?

A konfigurációt a Key `Azure:SignalR:ConnectionString` vagy `Azure:SignalR:ConnectionString:` a Signal Service-kapcsolatok karakterláncához.

Ha a kulcs `Azure:SignalR:ConnectionString:`rel kezdődik, akkor formátum `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`kell lennie, ahol a `Name` és a `EndpointType` a `ServiceEndpoint` objektum tulajdonságai, és elérhetők a kódból.

Több példányos kapcsolódási karakterláncot is hozzáadhat a `web.config`hoz:

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

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hogyan lehet több végpontot felvenni a kódból?

Egy `ServicEndpoint` osztály van bevezetve, amely leírja az Azure Signaler szolgáltatás végpontjának tulajdonságait.
Ha az Azure Signaler Service SDK-t használja a használatával, több példány-végpontot is beállíthat:

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

### <a name="how-to-customize-router"></a>Az útválasztó testreszabása

Az egyetlen különbség a ASP.NET-jelző és a ASP.NET Core-jelző között a `GetNegotiateEndpoint`a http-környezet típusa. A ASP.NET-jelző esetében [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) típusú.

Alább látható a ASP.NET-jelzőhöz tartozó egyéni egyeztetési példa:

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

Ne felejtse el regisztrálni az útválasztót a DI Container használatával:

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

## <a name="configuration-in-cross-region-scenarios"></a>Konfigurálás régiók közötti forgatókönyvekben

A `ServiceEndpoint` objektumhoz `primary` vagy `secondary`értékű `EndpointType` tulajdonság tartozik.

`primary` végpontok előnyben részesített végpontok az ügyfél forgalmának fogadására, és megbízhatóbb hálózati kapcsolatnak tekintendők. `secondary`-végpontok kevésbé megbízható hálózati kapcsolatoknak minősülnek, és csak a kiszolgálók közötti adatforgalomhoz, például szórási üzenetekhez használatosak, és nem az ügyfél és a kiszolgáló közötti adatforgalomhoz.

Régiók közötti esetekben a hálózat instabil lehet. Az *USA keleti*régiójában található egyik app Server esetében a signaler szolgáltatás végpontja az *USA keleti* régiójában található, `primary`ként és végpontként is konfigurálható más régiókban `secondary`ként megjelölve. Ebben a konfigurációban a más régiókban lévő szolgáltatási végpontok **fogadhatnak** e-maileket erről az USA-beli *East* app Server-kiszolgálóról, de nem lesznek átirányítva **régiók közötti** ügyfelek az adott app Serverhez. Az architektúra az alábbi ábrán látható:

![Földrajzilag átnyúló infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Amikor egy ügyfél megpróbál `/negotiate` az App Serverrel, az alapértelmezett útválasztóval, az SDK **véletlenszerűen kiválaszt** egy végpontot az elérhető `primary` végpontok készletéről. Ha az elsődleges végpont nem érhető el, az SDK **véletlenszerűen kiválasztja** az összes rendelkezésre álló `secondary` végpontot. A végpont **elérhetőként** van megjelölve, ha a kiszolgáló és a szolgáltatási végpont közötti kapcsolat él.

Régiók közötti forgatókönyv esetén, amikor egy ügyfél megpróbál `/negotiate` az *USA keleti*régiójában üzemeltetett app Serverrel, alapértelmezés szerint mindig az ugyanabban a régióban található `primary` végpontot adja vissza. Ha az összes *kelet-amerikai* végpont nem érhető el, a rendszer átirányítja az ügyfelet más régiókban lévő végpontokra. Az alábbi feladatátvételi szakasz részletesen ismerteti a forgatókönyvet.

![Normál egyeztetés](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Feladatátvétel

Ha az összes `primary` végpont nem érhető el, az ügyfél `/negotiate` a rendelkezésre álló `secondary` végpontok közül. Ez a feladatátvételi mechanizmus megköveteli, hogy mindegyik végpontnak `primary` végpontnak kell lennie legalább egy app Server-kiszolgálónak.

![Feladatátvétel](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Következő lépések

Ebből az útmutatóból megtudhatta, hogyan konfigurálhat több példányt ugyanabban az alkalmazásban a méretezés, a horizontális skálázás és a régiók közötti helyzetekhez.

Több végpont is támogatott magas rendelkezésre állású és vész-helyreállítási helyzetekben.

> [!div class="nextstepaction"]
> [A riasztási szolgáltatás beállítása a vész-helyreállításhoz és a magas rendelkezésre álláshoz](./signalr-concept-disaster-recovery.md)
