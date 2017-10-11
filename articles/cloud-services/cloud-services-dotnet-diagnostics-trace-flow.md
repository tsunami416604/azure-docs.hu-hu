---
title: "A folyamat a egy felhőalapú szolgáltatások alkalmazást az Azure diagnosztikai nyomkövetési |} Microsoft Docs"
description: "Nyomkövetési üzenet hozzáadása érdekében a hibakeresés méri a teljesítményt, figyelés, forgalom elemzése és további Azure-alkalmazásfejlesztő."
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: 
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: robb
ms.openlocfilehash: 35b4a4270846c54a1ca760e803ef7adba60cf03b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>A folyamatot az Azure Diagnostics Felhőszolgáltatások alkalmazás nyomkövetési
Nyomkövetés módja a figyelheti a végrehajtása az alkalmazás futtatása során. Használhatja a [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx), és [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) hibák adatainak rögzítésére osztályok és alkalmazás végrehajtási naplók, szövegfájlok vagy más eszközök későbbi elemzés céljából. További információ a nyomkövetési: [nyomkövetés és tagolása alkalmazások](https://msdn.microsoft.com/library/zs6s4h68.aspx).

## <a name="use-trace-statements-and-trace-switches"></a>Nyomkövetési utasítások és nyomkövetési kapcsolók használata
Adja hozzá a Felhőszolgáltatások alkalmazás nyomkövetési megvalósítása a [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) az alkalmazás konfigurációját, és a System.Diagnostics.Trace vagy System.Diagnostics.Debug a hívások a alkalmazás kódja. A konfigurációs fájl *app.config* feldolgozói szerepkörök és a *web.config* webes szerepkörök. Amikor létrehoz egy új üzemeltetett szolgáltatást, a Visual Studio-sablonnal, Azure Diagnostics automatikusan hozzáadódik a projektet, és a DiagnosticMonitorTraceListener hozzáadódik a megfelelő konfigurációs fájlt ad hozzá a szerepkörök számára.

Információ a nyomkövetési utasítások elhelyezéséhez: [hogyan: nyomkövetési utasítások adja hozzá az alkalmazás kódjában](https://msdn.microsoft.com/library/zd83saa2.aspx).

Ha kialakít [nyomkövetési kapcsolók](https://msdn.microsoft.com/library/3at424ac.aspx) nyomkövetés következik be, és hogyan kiterjedt szabályozhatja a kódban. Ez lehetővé teszi az éles környezetben az alkalmazás állapotának figyelése. Ez különösen fontos a több számítógépen futnak több összetevőt használ üzleti alkalmazást. További információkért lásd: [hogyan: nyomkövetési kapcsolók konfigurálása](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>A nyomkövetés-figyelő konfigurálása az Azure alkalmazásban
Nyomkövetési, a hibakeresési és TraceSource, ehhez állítsa be "figyelői" összegyűjtésére, és jegyezze fel a küldött üzenetek. Figyelők gyűjtése, tárolására és nyomkövetés üzenetek. A nyomkövetési kimeneti egy megfelelő célhoz, például a napló, ablakot vagy szövegfájl közvetlen azokat. Az Azure Diagnostics használja a [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) osztály.

A következő eljárás végrehajtása előtt inicializálni kell az Azure diagnosztikai figyelő. Ehhez tekintse meg a [diagnosztika engedélyezése a Microsoft Azure-ban](cloud-services-dotnet-diagnostics.md).

Vegye figyelembe, hogy ha a Visual Studio által biztosított sablonokat használ, a figyelő a konfigurációs kerül automatikusan meg.

### <a name="add-a-trace-listener"></a>Adja hozzá a nyomkövetés-figyelő
1. Nyissa meg az adott szerepkörhöz a web.config vagy az app.config fájlt.
2. Adja hozzá a következő kódot a fájlt. Módosítja annak verzióattribútumát, a hivatkozott szerelvény verziószáma használatára. A szerelvény verziója nem feltétlenül módosíthatja az egyes Azure SDK kiadások kivéve, ha frissítések érkeztek azt.
   
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
   > Győződjön meg arról, hogy a projektbe egy hivatkozást a Microsoft.WindowsAzure.Diagnostics szerelvényre. A verziószám, a fenti XML-kódban a hivatkozott Microsoft.WindowsAzure.Diagnostics szerelvény verziójának megfelelő frissítéséhez.
   > 
   > 
3. Mentse a konfigurációs fájlt.

Figyelők kapcsolatos további információkért lásd: [nyomkövetésfigyelők](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Miután elvégezte a figyelő felvételének lépéseiről, adhat hozzá nyomkövetési utasítások a kódot.

### <a name="to-add-trace-statement-to-your-code"></a>A kód nyomkövetési utasítás hozzáadása
1. Az alkalmazás a forrásfájl megnyitásakor. Például a <RoleName>.cs fájlt a feldolgozói szerepkör, illetve a webes szerepkör.
2. Adja hozzá a következő using utasítást, ha már nincs hozzáadva:
    ```
        using System.Diagnostics;
    ```
3. Adja hozzá a nyomkövetési utasítások hol kívánja rögzíteni az alkalmazás állapotára vonatkozó adatokat. Számos módszer segítségével formázza a nyomkövetési utasítás kimenete. További információkért lásd: [hogyan: nyomkövetési utasítások adja hozzá az alkalmazás kódjában](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. A forrás-fájl mentése.

