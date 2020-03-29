---
title: A felhőszolgáltatások életciklus-eseményeinek kezelése | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatók a felhőszolgáltatás-szerepköréletciklus-kezelési módszerei a .NET-ben
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 0a9c32affc50a6d357d4160e00486c896d762e3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75385813"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Webes vagy feldolgozói szerepkör életciklusának testreszabása a .NET szolgáltatásban
Amikor létrehoz egy feldolgozói szerepkört, kiterjeszti a [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) osztályt, amely olyan módszereket biztosít, amelylehetővé teszi az életciklus-eseményekre való válaszadást. Webes szerepkörök esetében ez az osztály nem kötelező, ezért életciklus-események megválaszolásához kell használnia.

## <a name="extend-the-roleentrypoint-class"></a>A RoleEntryPoint osztály kiterjesztése
A [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) osztály olyan metódusokat tartalmaz, amelyeket az Azure **az indításkor**hív meg, **futtat,** vagy **leállít** egy webes vagy feldolgozói szerepkört. Ezeket a módszereket felülírhatja a szerepkör-inicializálás, a szerepkörleállítási sorozatok vagy a szerepkör végrehajtási szálának kezeléséhez. 

A **RoleEntryPoint**bővítésekor tisztában kell lennie a módszerek alábbi viselkedésével:

* Az [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) és [az OnStop](/previous-versions/azure/reference/ee772844(v=azure.100)) metódus logikai értéket ad vissza, így ezekből a módszerekből **hamis** értéket lehet visszaadni.
  
   Ha a kód **hamis**értéket ad vissza, a szerepkörfolyamat hirtelen leáll, anélkül, hogy bármilyen leállítási sorrendet futtatna. Általában ne adja vissza a **hamis** az **OnStart** metódusból.
* A **RoleEntryPoint** metódus túlterhelésén belüli nem fel nem fogott kivételt a kezelt kivételként kezeli a kezelővel nem kezelt kivétel.
  
   Ha kivétel történik az életciklus-módszerek egyikén belül, az Azure a [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) eseményt hozza meg, majd a folyamat leáll. Miután a szerepkör offline állapotba került, az Azure újraindítja. Nem kezelt kivétel esetén a [leállítási](/previous-versions/azure/reference/ee758136(v=azure.100)) esemény nem következik be, és az **OnStop** metódus nem lesz meghívva.

Ha a szerepkör nem indul el, vagy az inicializálási, foglalt és leállítási állapotok közötti újrahasznosítás, a kód előfordulhat, hogy egy nem kezelt kivétel takarásban van az életciklus-események egyikén belül minden alkalommal, amikor a szerepkör újraindul. Ebben az esetben használja a [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) eseményt a kivétel okának meghatározásához és megfelelő kezeléséhez. Előfordulhat, hogy a szerepkör a [Futtatás](/previous-versions/azure/reference/ee772746(v=azure.100)) metódusból is visszatér, ami a szerepkör újraindítását okozza. A telepítési állapotokról a [Gyakori problémák, amelyek az újrahasznosítási szerepköröket okozzák,](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)című témakörben talál további információt.

> [!NOTE]
> Ha az **Azure Tools for Microsoft Visual Studio** alkalmazást használja az alkalmazás fejlesztéséhez, a szerepkörprojekt-sablonok automatikusan kiterjesztik a **RoleEntryPoint-osztályt** az *WebRole.cs* és *WorkerRole.cs* fájlokban.
> 
> 

## <a name="onstart-method"></a>OnStart metódus
Az **OnStart** metódus neve akkor történik, ha a szerepkörpéldányt az Azure online állapotba hozhatja. Amíg az OnStart kód végrehajtás alatt van, a szerepkörpéldány **foglaltként** van megjelölve, és a terheléselosztó nem irányít külső forgalmat. Ezt a módszert felülbírálhatja az inicializálási munka elvégzéséhez, például az eseménykezelők megvalósításához és az [Azure Diagnostics elindításához.](cloud-services-how-to-monitor.md)

Ha **az OnStart** **igaz**értéket ad vissza, a példány inicializálása sikeresen megtörtént, és az Azure meghívja a **RoleEntryPoint.Run** metódust. Ha **az OnStart** **hamis**értéket ad vissza, a szerepkör azonnal leáll, anélkül, hogy bármilyen tervezett leállítási sorozatot végrehajtana.

A következő példa kód bemutatja, hogyan lehet felülírni az **OnStart** metódust. Ez a módszer konfigurálja és elindítja a diagnosztikai figyelőt, amikor a szerepkörpéldány elindul, és beállítja a naplózási adatok átvitelét egy tárfiókba:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>OnStop metódus
Az **OnStop** metódus elnevezése után egy szerepkörpéldány offline állapotba került az Azure-ban, és a folyamat kilépése előtt. Ezt a módszert felülbírálhatja a szerepkörpéldány tisztításához szükséges kód hívásához.

> [!IMPORTANT]
> **Az OnStop** metódusban futó kódnak korlátozott ideje van befejezni, ha a felhasználó által kezdeményezett leállítástól eltérő okok miatt hívják meg. Ezen idő eltelte után a folyamat leáll, ezért meg kell győződnie arról, hogy az **OnStop** metódusban lévő kód gyorsan futtatható, vagy nem futtatható a befejezésig. Az **OnStop** metódus neve a **Leállítási** esemény után történik.
> 
> 

## <a name="run-method"></a>Metódus futtatása
Felülbírálhatja a **Futtatás** metódust egy hosszú ideig futó szál megvalósításához a szerepkörpéldányhoz.

A **Futtatás** metódus felülbírálása nem szükséges; az alapértelmezett implementáció egy örökálomban alvó szálat indít el. Ha felülírja a **Futtatás** metódust, a kódnak határozatlan ideig blokkolnia kell. Ha a **Futtatás** metódus visszatér, a szerepkör automatikusan szabályosan újrahasznosítható; más szóval az Azure megemeli a **Leállítási** eseményt, és meghívja az **OnStop** metódust, hogy a leállítási szekvenciák végrehajthatók legyenek a szerepkör offline állapotba helyezése előtt.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Webes szerepkör ASP.NET életciklus-metódusának megvalósítása
A ASP.NET életciklus-módszerek et a **RoleEntryPoint** osztály által biztosított módszereken kívül használhatja a webes szerepkör inicializálási és leállítási sorozatainak kezelésére. Ez kompatibilitási célokra hasznos lehet, ha egy meglévő ASP.NET alkalmazást az Azure-ba portol. A ASP.NET életciklus-metódusok a **RoleEntryPoint** metódusokon belül vannak meghívva. Az **\_Alkalmazás indítása** metódus neve a **RoleEntryPoint.OnStart** metódus befejezése után történik. Az **\_Alkalmazás vége** metódus neve a **RoleEntryPoint.OnStop** metódus megnevezése előtt történik.

## <a name="next-steps"></a>További lépések
További információ a [felhőalapú szolgáltatáscsomagok létrehozásáról.](cloud-services-model-and-package.md)




