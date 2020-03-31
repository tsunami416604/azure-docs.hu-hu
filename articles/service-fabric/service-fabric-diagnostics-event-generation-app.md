---
title: Azure Service Fabric alkalmazásszint figyelése
description: Ismerje meg az Azure Service Fabric-fürtök figyeléséhez és diagnosztizálásához használt alkalmazás- és szolgáltatásszintű eseményeket és naplókat.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 97c3be391dfbee7301ea47bf7234a9549d373370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464720"
---
# <a name="application-logging"></a>Alkalmazásnaplózás

A kód instrumentálása nem csak a felhasználókkal kapcsolatos információk megszerzésének egyik módja, hanem az egyetlen módja annak is, hogy megtudja tudni, hogy valami nincs-e rendben az alkalmazásban, és diagnosztizálhatja, hogy mit kell javítani. Bár technikailag lehetséges egy hibakereső t éles szolgáltatáshoz csatlakoztatni, ez nem általános gyakorlat. Tehát fontos, hogy részletes műszerezési adatokkal rendelkezzünk.

Egyes termékek automatikusan beiktatják a kódot. Bár ezek a megoldások jól működnek, a manuális műszerezésszinte mindig szükséges, hogy az üzleti logikára jellemző legyen. Végül elegendő információval kell rendelkeznie ahhoz, hogy kriminalisztikailag debugaz alkalmazás. A Service Fabric-alkalmazások bármilyen naplózási keretrendszerrel műszerezhetők. Ez a dokumentum néhány különböző megközelítést ír le a kód instrumenting, és mikor kell választani az egyik megközelítés a másikkal szemben. 

A javaslatok használatával kapcsolatos példákról a [Naplózás hozzáadása a Service Fabric-alkalmazáshoz című témakörben](service-fabric-how-to-diagnostics-log.md)talál.

## <a name="application-insights-sdk"></a>Application Insights SDK

Az Application Insights gazdag integrációt biztosít a Service Fabric-tel a dobozból. A felhasználók hozzáadhatják az AI Service Fabric nuget csomagokat, és fogadhatnak az Azure Portalon megtekinthető és összegyűjtött adatokat és naplókat. Emellett a felhasználók számára javasoljuk, hogy adja hozzá a saját telemetriai annak érdekében, hogy diagnosztizálják és hibakeresés az alkalmazások és a nyomon követése, amely szolgáltatások és részei az alkalmazás a leggyakrabban használt. Az SDK [Telemetriai-ügyfélosztályszámos](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) lehetőséget kínál az alkalmazások telemetriai adatainak nyomon követésére. Tekintse meg a példát arra, hogyan lehet eszköz, és add alkalmazás betekintést az alkalmazás a mi tutorial [nyomon követésére és diagnosztizálására a .NET alkalmazás](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>EventSource (Eseményforrás)

Amikor egy Service Fabric-megoldást hoz létre egy sablonból a Visual Studio-ban, egy **EventSource**-derived osztály (**ServiceEventSource** vagy **ActorEventSource**) jön létre. Létrejön egy sablon, amelyben eseményeket adhat hozzá az alkalmazáshoz vagy a szolgáltatáshoz. Az **EventSource** névnek egyedinek **kell** lennie, és át kell&lt;&gt;-&lt;nevezni&gt;az alapértelmezett sablonkarakterláncról, a MyCompany- solution projektből. Ha **több,** ugyanazt a nevet használó EventSource-definícióval rendelkezik, az futási időben problémát okoz. Minden definiált eseménynek egyedi azonosítóval kell rendelkeznie. Ha egy azonosító nem egyedi, futásidejű hiba lép fel. Egyes szervezetek előre hozzárendelik az azonosítók értéktartományait, hogy elkerüljék a különálló fejlesztői csoportok közötti ütközéseket. További információ: [Vance blogja](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) vagy az [MSDN dokumentációja.](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)

## <a name="aspnet-core-logging"></a>ASP.NET Core naplózás

Fontos, hogy gondosan tervezze meg, hogyan fogja eszköz a kódot. A megfelelő műszerezési terv segítségével elkerülheti a kódbázis potenciális destabilizálását, majd a kód újraprogramozását. A kockázat csökkentése érdekében választhat egy instrumentation könyvtárat, például a [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)könyvtárat, amely a Microsoft ASP.NET Core része. ASP.NET Core egy [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) felület, amely segítségével a szolgáltató az Ön által kiválasztott, miközben minimalizálja a hatása a meglévő kódot. Használhatja a kódot ASP.NET Core Windows és Linux, és a teljes .

## <a name="next-steps"></a>További lépések

Miután kiválasztotta a naplózási szolgáltatót az alkalmazások és szolgáltatások eszközének, a naplókat és eseményeket összesíteni kell, mielőtt bármilyen elemzési platformra elküldhetők lennének. Olvassa el [az Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) és az [EventFlow,](service-fabric-diagnostics-event-aggregation-eventflow.md) hogy jobban megértsék az Azure Monitor ajánlott lehetőségeket.
