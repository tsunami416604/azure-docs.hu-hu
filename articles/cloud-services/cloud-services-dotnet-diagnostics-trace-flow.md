---
title: A folyamat egy Cloud Services-alkalmazás az Azure Diagnostics segítségével nyomon követése |} A Microsoft Docs
description: A nyomkövetési üzeneteket hozzá Azure-alkalmazások hibakeresése, teljesítmény, figyelés, forgalomelemzéshez és egyéb mérési segítségével.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: jeconnoc
ms.openlocfilehash: f0724fd6e5f08f3e09bcb147c12d1657235dc704
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916886"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>A folyamat egy Cloud Services-alkalmazás az Azure Diagnostics segítségével nyomon követése
Nyomkövetés módja, hogy az alkalmazás végrehajtását a figyelése, futás közben. Használhatja a [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace), [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug), és [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) hibák adatainak rögzítésére osztályok és alkalmazás végrehajtása a naplókat, szöveges fájlok vagy más eszközök későbbi elemzés céljából. Nyomkövetés kapcsolatos további információkért lásd: [nyomkövetés és alkalmazásokhoz való Műszerezéséről](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Nyomkövetési utasításokat és nyomkövetési kapcsolók használata
Adja hozzá a Cloud Services-alkalmazás nyomkövetési megvalósítása a [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) az alkalmazás konfigurációját és a System.Diagnostics.trace keretrendszert használja, vagy a System.Diagnostics.Debug meghívni a alkalmazáskód. A konfigurációs fájl *app.config* a feldolgozói szerepkörök és a *web.config* webes szerepkörök esetében. Amikor létrehoz egy új üzemeltetett szolgáltatást, a Visual Studio-sablon használatával, az Azure Diagnostics automatikusan hozzáadja a projekthez, és a DiagnosticMonitorTraceListener bekerül a szerepkörökhöz adja hozzá a megfelelő konfigurációs fájl.

Információ a nyomkövetési utasításokat elhelyezése: [hogyan: Adja hozzá a nyomkövetési utasításokat alkalmazáskód](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Elhelyezésével [nyomkövetési kapcsolók](/dotnet/framework/debug-trace-profile/trace-switches) nyomkövetés következik be, és hogyan kiterjedt szabályozhatja a kódba. Ez lehetővé teszi az alkalmazás éles környezetben állapotának monitorozásához. Ez különösen fontos a több számítógépen futó több összetevőből egy üzleti alkalmazás. További információkért lásd: [hogyan: Konfigurálja a nyomkövetési kapcsolókat](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>A nyomkövetés-figyelő konfigurálása az Azure-alkalmazások
A nyomkövetést, a hibakeresési és TraceSource, szükséges, állítsa be a "figyelő" gyűjteni, és jegyezze fel a küldött üzenetek. Figyelők gyűjtése, tárolása és irányíthatja a nyomkövetési üzeneteket. Ezek a közvetlen a nyomkövetés egy megfelelő célt, például egy naplófájl, ablakban vagy szövegfájl. Az Azure Diagnostics használja a [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) osztály.

Mielőtt a következő eljárással inicializálnia kell az Azure diagnosztikai figyelő. Ehhez lásd [diagnosztika engedélyezése a Microsoft Azure-ban](cloud-services-dotnet-diagnostics.md).

Vegye figyelembe, hogy a Visual Studio által biztosított sablonok használatakor a figyelő a konfigurációs van hozzá automatikusan az Ön számára.

### <a name="add-a-trace-listener"></a>Adjon hozzá egy nyomkövetés-figyelő
1. Nyissa meg a web.config vagy az app.config fájlt a szerepkörhöz.
2. Adja hozzá a következő kódot a fájl. Módosítja annak verzióattribútumát, a hivatkozott szerelvényt verziószáma használatára. A szerelvény verziója nem feltétlenül változik az egyes Azure SDK kibocsátási, kivéve, ha frissítések érhetők el azt.
   
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
   > Győződjön meg arról, hogy a Microsoft.WindowsAzure.Diagnostics projekt hivatkozást. A hivatkozott szerelvényt Microsoft.WindowsAzure.Diagnostics verziójának megfelelő XML-fenti verziószám frissítéséhez.
   > 
   > 
3. Mentse a konfigurációs fájlt.

Figyelők kapcsolatos további információkért lásd: [nyomkövetési figyelői](/dotnet/framework/debug-trace-profile/trace-listeners).

Miután végrehajtotta a lépéseket adja hozzá a figyelőt, a kód nyomkövetési utasításokat is hozzáadhat.

### <a name="to-add-trace-statement-to-your-code"></a>A kód híváslánc-utasítás hozzáadása
1. Nyissa meg az alkalmazás egy forrásfájl. Ha például a <RoleName>.cs fájlt a feldolgozói szerepkör, illetve a webes szerepkör.
2. Adja hozzá a következő using utasítást, ha már nincs hozzáadva:
    ```
        using System.Diagnostics;
    ```
3. Adja hozzá a nyomkövetési utasításokat hol kívánja rögzíteni a az alkalmazás állapotával kapcsolatos információk. Többféle módszerrel használatával formázhatja a Híváslánc-utasítás kimenetét. További információkért lásd: [hogyan: Adja hozzá a nyomkövetési utasításokat alkalmazáskód](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Mentse a forrásfájl.

