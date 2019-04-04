---
title: Cloud Service-életciklusesemények kezelése |} A Microsoft Docs
description: Ismerje meg, a Cloud Service szerepkörök életciklusának módszerek hogyan használható a .NET-ben
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 39b30acd-57b9-48b7-a7c4-40ea3430e451
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 13f500b32bb85bdc0f84b812ef4ef9188a257771
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916308"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Webes és feldolgozói szerepkörök életciklusának testreszabása a .NET-ben
Feldolgozói szerepkör létrehozásakor kiterjeszti a [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) osztályt, amelyet itt felülbírálhatja a módszereket, amelyekkel életciklusesemények válaszolni. Webes szerepkörök esetében ez az osztály nem kötelező, így életciklusesemények válaszolni kell használni.

## <a name="extend-the-roleentrypoint-class"></a>A RoleEntryPoint osztály kiterjesztése
A [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) osztály tartalmazza az Azure által meghívott módszerek amikor azt **elindul**, **fut**, vagy **leállítja** egy webes vagy feldolgozói szerepkörben. Ezek a metódusok kezelése a szerepkör inicializálása, a szerepkör leállítása feladatütemezések vagy a szerepkör a végrehajtási szál igény szerint felül lehet bírálni. 

Amikor bővíti **RoleEntryPoint**, vegye figyelembe a módszer a következő viselkedésmódok közül:

* A [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) és [OnStop](/previous-versions/azure/reference/ee772844(v=azure.100)) módszerek egy logikai értéket ad vissza, így érdemes lehet térni **hamis** az ezen módszerek.
  
   Ha a kódot ad vissza **hamis**, a szerepkör-folyamat váratlanul megszakadt, bármely leállítási folyamat futtatása nélkül lehet helyen. Általában kerülje visszaadó **hamis** származó a **OnStart** metódus.
* Bármelyik nem kezelt kivétel belül egy túlterhelésére egy **RoleEntryPoint** metódus nem kezelt kivétel számít.
  
   Ha kivétel történik az életciklus módszerek belül, kiváltja az Azure a [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) esemény, majd a folyamat megszakadt. Miután a szerepkör offline állapotban van, az Azure-ban újraindul. Ha nem kezelt kivétel lép fel, a [leállítása](/previous-versions/azure/reference/ee758136(v=azure.100)) esemény nem jelenik meg, és a **OnStop** metódus nem lett meghívva.

Ha a szerepkör nem indul el, vagy az inicializálás, foglalt, és leállításával állapotok közötti mindig újraindul, előfordulhat, hogy a kód értesítő egyikébe az életciklus-események minden alkalommal, amikor a szerepkör újraindítása nem kezelt kivétel. Ebben az esetben használja a [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) eseményhez, és állapítsa meg a kivétel okát, és megfelelően kezelni. A szerepkör is visszatérésre a [futtatása](/previous-versions/azure/reference/ee772746(v=azure.100)) metódussal, amely a szerepkör újraindítását okozza. További információ a központi telepítési állapotok: [gyakori problémák amely ok szerepkörök újrahasznosításával](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Ha használja a **Microsoft Visual Studióhoz készült Azure-eszközök** és fejlessze alkalmazását, a szerepkör projektsablonok automatikusan kiterjeszti a **RoleEntryPoint** osztály, az  *WebRole.cs* és *WorkerRole.cs* fájlokat.
> 
> 

## <a name="onstart-method"></a>OnStart metódus
A **OnStart** metódus lehívásra kerül, amikor a szerepkörpéldány online állapotba kerül az Azure-ban. Amíg az OnStart kód végrehajtása, role instance be van-e megjelölve **foglalt** és a külső forgalmat irányítja a rendszer azt a terheléselosztó által. Felülbírálhatja ezt a módszert, például eseménykezelők végrehajtási és indítása a inicializálási munka elvégzéséhez [Azure Diagnostics](cloud-services-how-to-monitor.md).

Ha **OnStart** adja vissza **igaz**, a példány sikeresen inicializálva van, és az Azure meghívja a **RoleEntryPoint.Run** metódust. Ha **OnStart** adja vissza **hamis**, a szerepkör végrehajtása megadva bármilyen tervezett leállás nélkül, azonnal leáll.

Az alábbi példakód bemutatja, hogyan bírálja felül a **OnStart** metódust. Ezzel a módszerrel konfigurálja, és egy diagnosztikai figyelő kezdődik, amikor a szerepkörpéldány elindul, és beállítja a naplózási adatok átvitelét egy storage-fiókhoz:

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
A **OnStop** metódus meghívása után egy szerepkörpéldány offline állapotban van az Azure-ban, és mielőtt a folyamat kilép. Ez a metódus hívása a szerepkörpéldány bezárva, szükséges kódot felül lehet bírálni.

> [!IMPORTANT]
> Futó kód a **OnStop** metoda má korlátozott ideig a befejezéshez, amikor egy felhasználó által kezdeményezett leállítási leszámítva bármilyen okból nevezzük. Ez az idő eltelte után a folyamat megszakadt, ezért meg kell győződnie arról, hogy a kód a **OnStop** metódus gyorsan futtathatja, vagy eltűr befejezéséig nem fut. A **OnStop** metódus meghívása után a **leállítása** egy esemény jelenik meg.
> 
> 

## <a name="run-method"></a>Futtatási mód
Felülbírálhatja a **futtatása** metódus megvalósításához a szerepkörpéldány egy hosszú ideig futó szálat.

Felülbírálja a **futtatása** metódus nem kötelező; az alapértelmezett implementációja elindul egy hozzászólásláncot, amely alvó örökre. Ha felülbírálja a **futtatása** metódus, a kód blokkolnia kell határozatlan időre. Ha a **futtassa** metódus adja vissza, a szerepkör automatikusan szabályosan újrahasznosított; más szóval Azure kiváltja a **leállítása** esemény és a hívások a **OnStop** metódus, hogy a Leállítás feladatütemezések előfordulhat, hogy hajtható végre, előtt a szerepkört offline állapotba helyeznek.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Megvalósítása a webes szerepkör ASP.NET életciklus módszerei
Az ASP.NET életciklus módszerekkel által biztosított naplókon felül a **RoleEntryPoint** osztályt egy webes szerepkör inicializálási és leállítás feladatütemezések kezelése. Ez akkor lehet hasznos kompatibilitási célból, ha a rendszer portolása a egy meglévő ASP.NET-alkalmazást az Azure-bA. Az ASP.NET-életciklus módszerek nevezzük belül a **RoleEntryPoint** módszereket. A **alkalmazás\_Start** metódus meghívása után a **RoleEntryPoint.OnStart** metódus befejeződik. A **alkalmazás\_záró** metódus meghívása előtt a **RoleEntryPoint.OnStop** módszert hívja meg.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [hozzon létre egy cloud service-csomag](cloud-services-model-and-package.md).

