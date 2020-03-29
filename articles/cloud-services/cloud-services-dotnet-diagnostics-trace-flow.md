---
title: A folyamat nyomon követése a Felhőszolgáltatások alkalmazásában az Azure Diagnostics segítségével
titleSuffix: Azure Cloud Services
description: Nyomkövetési üzenetek hozzáadása egy Azure-alkalmazáshoz a hibakeresés, a teljesítmény mérése, a figyelés, a forgalomelemzés és egyebek elősegítése érdekében.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: tagore
ms.openlocfilehash: 47a33ba27dd6d2df626d93695c421303bace6a0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386510"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Felhőalapú szolgáltatások alkalmazás folyamatának nyomon követése az Azure Diagnostics segítségével
A nyomkövetés lehetővé teszi az alkalmazás futás közbeni végrehajtásának figyelését. A [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace), [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug)és [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) osztályok segítségével rögzítheti a hibákkal és az alkalmazás végrehajtással kapcsolatos információkat naplókban, szövegfájlokban vagy más eszközökön későbbi elemzésre. A nyomkövetésről további információt a [Nyomkövetési és az alkalmazások műszerezése című témakörben talál.](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications)

## <a name="use-trace-statements-and-trace-switches"></a>Nyomkövetési utasítások és nyomkövetési kapcsolók használata
Valósítsa meg a nyomkövetést a Cloud Services-alkalmazásban úgy, hogy hozzáadja a [DiagnosticMonitorTraceListener-t](/previous-versions/azure/reference/ee758610(v=azure.100)) az alkalmazás konfigurációjához, és hívásokat kezdeményez a System.Diagnostics.Trace vagy system.Diagnostics.Debug alkalmazáskódban. Használja az *app.config* konfigurációs fájlt a feldolgozói szerepkörökhöz és a *web.config-ot* a webes szerepkörökhöz. Amikor egy Visual Studio-sablon használatával új üzemeltetett szolgáltatást hoz létre, az Azure Diagnostics automatikusan hozzáadódik a projekthez, és a DiagnosticMonitorTraceListener hozzáadódik a hozzáadott szerepkörök megfelelő konfigurációs fájljához.

A nyomkövetési utasítások elhelyezése a [Hogyan történhet: Nyomkövetési kimutatások hozzáadása az alkalmazáskódhoz](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code)című témakörben talál.

Ha [nyomkövetési kapcsolókat](/dotnet/framework/debug-trace-profile/trace-switches) helyez el a kódban, szabályozhatja, hogy a nyomkövetés bekövetkezzen-e, és hogy mennyire kiterjedt. Ez lehetővé teszi az alkalmazás állapotának figyelését éles környezetben. Ez különösen fontos egy olyan üzleti alkalmazásban, amely több számítógépen futó több összetevőt használ. További információ: [Hogyan konfigurálható nyomkövetési kapcsolók](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>A nyomfigyelő konfigurálása egy Azure-alkalmazásban
Trace, Debug és TraceSource, megköveteli, hogy állítsa be a "hallgatók" gyűjteni és rögzíteni az elküldött üzeneteket. A figyelők gyűjtik, tárolják és irányítják a nyomkövetési üzeneteket. A nyomkövetési kimenetet egy megfelelő célra, például egy naplóra, ablakra vagy szöveges fájlra irányítják. Az Azure Diagnostics a [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) osztályt használja.

A következő eljárás befejezése előtt inicializálnia kell az Azure diagnosztikai figyelőt. Ehhez olvassa el a [Diagnosztika engedélyezése a Microsoft Azure-ban című témakört.](cloud-services-dotnet-diagnostics.md)

Vegye figyelembe, hogy ha a Visual Studio által biztosított sablonokat használja, a figyelő konfigurációja automatikusan hozzáadódik.

### <a name="add-a-trace-listener"></a>Nyomkövetési figyelő hozzáadása
1. Nyissa meg a web.config vagy app.config fájlt a szerepköréhez.
2. Adja hozzá a következő kódot a fájlhoz. Módosítsa a Version attribútumot a hivatkozott szerelvény verziószámának használatához. A szerelvény verziója nem feltétlenül változik az egyes Azure SDK-kiadások, kivéve, ha vannak frissítések.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Győződjön meg arról, hogy rendelkezik a Microsoft.WindowsAzure.Diagnostics szerelvényre vonatkozó projekthivatkozással. Frissítse a fenti xml verziószámát, hogy megfeleljen a hivatkozott Microsoft.WindowsAzure.Diagnostics szerelvény verziójának.
   > 
   > 
3. Mentse a konfigurációs fájlt.

A figyelőkről további információt a [Figyelők nyomkövetése című témakörben talál.](/dotnet/framework/debug-trace-profile/trace-listeners)

Miután elvégezte a figyelő hozzáadásának lépéseit, nyomkövetési kimutatásokat adhat a kódhoz.

### <a name="to-add-trace-statement-to-your-code"></a>Nyomkövetési utasítás hozzáadása a kódhoz
1. Nyisson meg egy forrásfájlt az alkalmazáshoz. Például a \<szerepkörnév>.cs fájlt a feldolgozói vagy webes szerepkörhöz.
2. Ha még nem adta hozzá, adja hozzá a következő irányelvet az irányelv használatával:
    ```
        using System.Diagnostics;
    ```
3. Adja hozzá a Nyomkövetési utasítások, ahol meg szeretné örökíteni az alkalmazás állapotát. A Nyomkövetési utasítás kimenetének formázásához számos módszer használható. További információ: [Hogyan lehet nyomkövetési nyilatkozatokat hozzáadni az alkalmazáskódhoz.](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code)
4. Mentse a forrásfájlt.




