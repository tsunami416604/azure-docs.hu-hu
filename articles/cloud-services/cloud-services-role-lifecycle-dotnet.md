---
title: "A felhőalapú szolgáltatás életciklus-események kezelésére |} Microsoft Docs"
description: "Ismerje meg, hogyan életciklusának egy felhőalapú szolgáltatás szerepkör is használható a .NET"
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 39b30acd-57b9-48b7-a7c4-40ea3430e451
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: eb78c05df3b3cdf3887334c11bdabd5cebb74747
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Webes és feldolgozói szerepkörök életciklusának testreszabása a .NET-ben
A feldolgozói szerepkör létrehozásakor kiterjeszti a [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) osztály, amely felülbírálhatja használatos metódusok biztosítása, amelyek lehetővé teszik az életciklus-események válaszolni. A webes szerepkörök Ez az osztály nem kötelező, így életciklus-események válaszolni kell használni.

## <a name="extend-the-roleentrypoint-class"></a>A RoleEntryPoint osztály
A [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) osztály tartalmazza a módszereket, amelyek az Azure által meghívott amikor azt **elindul**, **fut**, vagy **leállítja** egy webes vagy feldolgozói szerepkörrel. Ezek a módszerek inicializálási szerepkör, szerepkör leállítási sorozatok vagy a végrehajtási szál a szerepkör felügyeletéhez szükség felülbírálható. 

Amikor bővíti **RoleEntryPoint**, érdemes figyelembe az alábbi viselkedésmódok módszerek:

* A [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) és [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) módszerek egy logikai értéket adja vissza, így a visszaadandó **hamis** az ezen módszerek.
  
   Ha a kódot adja vissza **hamis**, a szerepkör-folyamat váratlanul megszakadt, bármely leállítási folyamat futtatása nélkül előfordulhat, hogy teljesülnek. Általában kerülendő visszaadó **hamis** a a **OnStart** metódust.
* A nem kezelt kivétel belül egy túlterhelése egy **RoleEntryPoint** metódus nem kezelt kivételt a rendszer.
  
   Kivétel az életciklus módszerek belül előfordul, ha az Azure emeli az [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) esemény, és a folyamat megszakadt. A szerepkör offline állapotban van, után újraindul az Azure-ban. Ha nem kezelt kivétel lép, a [leállítása](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) nem egy esemény jelenik meg, és a **OnStop** metódus nem lett meghívva.

Ha a szerepkör nem indul el, vagy az újrahasznosítás inicializálása, foglalt, és leállításával állapotok között, akkor a kód kiváltása az életciklus-események minden alkalommal, amikor a szerepkör újraindul egy nem kezelt kivétel. Ebben az esetben használja a [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) esemény a kivétel a hiba okának megállapításához, és megfelelően kezelje. A szerepkör is visszatérése a [futtatása](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódus, amely azt eredményezi, indítsa újra a szerepkört. További információ a központi telepítési állapotok: [gyakori problémák amely ok szerepkörök újrahasznosítását](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Ha használja a **Azure eszközök a Microsoft Visual Studio** fejlesztéséhez az alkalmazást, a szerepkör projekt sablonok automatikus kiterjesztése a **RoleEntryPoint** osztályt, a  *WebRole.cs* és *WorkerRole.cs* fájlokat.
> 
> 

## <a name="onstart-method"></a>OnStart metódus
A **OnStart** metódus lehívásra kerül, amikor a szerepkör példánya online állapotba az Azure-ban. A OnStart kód végrehajtása folyik, amíg a szerepkör példánya van megjelölve, **foglalt** és külső forgalmat a rendszer kéri, a terheléselosztó által. Ez a metódus inicializálási feladatok, például a végrehajtási eseménykezelők és elindítása végrehajtására felülbírálhatja [Azure Diagnostics](cloud-services-how-to-monitor.md).

Ha **OnStart** adja vissza **igaz**, a példány sikeresen inicializálva, és Azure meghívja a **RoleEntryPoint.Run** metódust. Ha **OnStart** adja vissza **hamis**, a szerepkör leállítása azonnal, minden tervezett leállás sorozatok végrehajtása nélkül.

Az alábbi példakód bemutatja, hogyan bírálja felül a **OnStart** metódust. Ez a módszer konfigurálja, és egy diagnosztikai figyelő akkor kezdődik, amikor a szerepkör példánya elindul, és beállítja a naplózási adatok átvitele a storage-fiókok:

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

## <a name="onstop-method"></a>OnStop módszer
A **OnStop** módszer neve után a szerepkörpéldány offline állapotban van az Azure-ban, és a folyamat kilép, mielőtt. Ha szeretné felülbírálni az ezt a metódust a szerepkörpéldányon bezárva, a szükséges kód hívása.

> [!IMPORTANT]
> Futó kód a **OnStop** metódusnak egy korlátozott ideig ennek oka nem egy felhasználó által kezdeményezett leállítást meghívásakor. Ezen idő eltelte után a folyamat megszakadt, ezért meg kell győződnie arról, hogy a kód a **OnStop** metódus eltűr befejezéséig nem fut, vagy gyorsan futtathatók. A **OnStop** módszer neve után a **leállítása** egy esemény jelenik meg.
> 
> 

## <a name="run-method"></a>Run metódus
Felülírhatja a **futtatása** módszer a hosszan futó szál a szerepkörpéldányhoz végrehajtásához.

Felülbírálja a **futtatása** metódus nincs szükség; az alapértelmezett implementációja elindítja a szál, amelyek örökre alvó állapotban van. Ha felülírják a **futtatása** metódus, a kódot kell blokkolása határozatlan ideig. Ha a **futtatása** metódus ad vissza, a szerepkör automatikusan szabályosan újrahasznosított; más szóval Azure kiváltja a **leállítása** esemény és hívások a **OnStop** metódust, hogy a Leállítás sorozatok is végrehajtható, előtt a szerepkört offline állapotba kerül.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Az ASP.NET életciklus módszerek a webes szerepkör megvalósítása
Az ASP.NET életciklus módszerekkel által biztosított naplókon felül léteznek a **RoleEntryPoint** osztály egy webes szerepkör inicializálási és leállítási feladatütemezések kezelése. Ez kompatibilitási célokra hasznos lehet, ha meg vannak eljárás egy létező ASP.NET-alkalmazás az Azure-bA. Az ASP.NET életciklus módszerek nevezzük belül a **RoleEntryPoint** módszerek. A **alkalmazás\_Start** módszer neve után a **RoleEntryPoint.OnStart** metódus befejeződik. A **alkalmazás\_End** módszer neve előtt a **RoleEntryPoint.OnStop** metódust.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [cloud service-csomag létrehozása](cloud-services-model-and-package.md).

