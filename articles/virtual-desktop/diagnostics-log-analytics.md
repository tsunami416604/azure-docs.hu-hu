---
title: Windows Virtual Desktop diagnosztikai naplóelemzés - Azure
description: A naplóelemzés használata a Windows Virtuális asztal diagnosztikai szolgáltatásával.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 355acb081afef8c78cdf971c7a82acdb91ab5593
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127973"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>A Log Analytics használata a diagnosztikai funkcióhoz

A Windows virtuális asztal diagnosztikai szolgáltatást kínál, amely lehetővé teszi a rendszergazda számára a problémák azonosítását egyetlen felületen keresztül. Ez a szolgáltatás naplózza a diagnosztikai információkat, amikor valaki windowsos virtuális asztal szerepkörrel rendelkezik, és használja a szolgáltatást. Minden napló információkat tartalmaz arról, hogy melyik Windows virtuális asztal szerepkör vett részt a tevékenységben, a munkamenet során megjelenő hibaüzeneteket, a bérlői adatokat és a felhasználói adatokat. A diagnosztikai szolgáltatás tevékenységnaplókat hoz létre mind a felhasználói, mind a felügyeleti műveletekhez. Minden tevékenységnapló három fő kategóriába tartozik: 

- Előcsatorna-előfizetési tevékenységek: amikor egy felhasználó a Microsoft Remote Desktop alkalmazásokon keresztül próbál csatlakozni a hírcsatornához.
- Csatlakozási tevékenységek: amikor a felhasználó microsoft távoli asztali alkalmazásokon keresztül próbál csatlakozni egy asztalhoz vagy RemoteApp-hoz.
- Felügyeleti tevékenységek: ha a rendszergazda felügyeleti műveleteket hajt végre a rendszeren, például gazdagépkészleteket hoz létre, felhasználókat rendel az alkalmazáscsoportokhoz, és szerepkör-hozzárendeléseket hoz létre.

Azok a kapcsolatok, amelyek nem érik el a Windows virtuális asztalt, nem jelennek meg a diagnosztikai eredményekben, mert maga a diagnosztikai szerepkör-szolgáltatás része a Windows virtuális asztalnak. A Windows virtuális asztal csatlakozási problémái akkor fordulhatnak elő, ha a felhasználó hálózati kapcsolattal kapcsolatos problémákat tapasztal.

## <a name="why-you-should-use-log-analytics"></a>Miért érdemes a Log Analytics szolgáltatást használni?

Azt javasoljuk, hogy a Log Analytics segítségével elemezze a diagnosztikai adatokat az Azure-ügyfél, amely túlmutat az egyfelhasználós hibaelhárítás. A virtuális gépek teljesítményszámlálóinak a Log Analytics behívása során egyetlen eszközzel rendelkezik a központi telepítéshez szükséges információk összegyűjtéséhez.

## <a name="before-you-get-started"></a>A kezdés előtt

Mielőtt a Log Analytics a diagnosztikai funkcióval, létre kell [hoznia egy munkaterületet.](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace)

A munkaterület létrehozása után kövesse a [Windows-számítógépek csatlakoztatása](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) az Azure Monitorhoz című útmutató utasításait a következő információk bekerüléséhez: 

- A munkaterület azonosítója
- A munkaterület elsődleges kulcsa

Erre az információra a beállítási folyamat későbbi részében lesz szüksége.

## <a name="push-diagnostics-data-to-your-workspace"></a>Diagnosztikai adatok leküldése a munkaterületre 

A Windows virtuális asztal bérlőjéből diagnosztikai adatokat lelökhet a munkaterület naplóanalytics szolgáltatásába. Ezt a funkciót azonnal beállíthatja, amikor először hozza létre a bérlőt a munkaterület nek a bérlőhöz való összekapcsolásával, vagy később egy meglévő bérlővel állíthatja be.

Ha a bérlőt az új bérlő beállítása közben a Log Analytics-munkaterülethez szeretné kapcsolni, futtassa a következő parancsmast a Windows virtuális asztalra való bejelentkezéshez a TenantCreator felhasználói fiókjával: 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

Ha egy meglévő bérlőt szeretne összekapcsolni új bérlő helyett, futtassa inkább ezt a parancsmast: 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Ezeket a parancsmagokat minden bérlőhöz futtatnia kell, amelyet a Log Analytics-hez szeretne kapcsolni. 

>[!NOTE]
>Ha nem szeretné összekapcsolni a Log Analytics munkaterületet, amikor `New-RdsTenant` létrehoz egy bérlőt, futtassa inkább a parancsmacst. 

## <a name="cadence-for-sending-diagnostic-events"></a>Ütemszám diagnosztikai események küldéséhez

A diagnosztikai eseményeket a rendszer elküldi a Log Analytics szolgáltatásnak, ha befejeződött.  

## <a name="example-queries"></a>Példa lekérdezésekre

A következő példalekérdezések azt mutatják be, hogy a diagnosztikai szolgáltatás hogyan hoz létre jelentést a rendszer leggyakoribb tevékenységeiről:

Ez az első példa a támogatott távoli asztali ügyfelekkel rendelkező felhasználók által kezdeményezett csatlakozási tevékenységeket mutatja be:

```powershell
WVDActivityV1_CL 

| where Type_s == "Connection" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```

Ez a következő példa lekérdezés a bérlők rendszergazdái által végzett felügyeleti tevékenységeket jeleníti meg:

```powershell
WVDActivityV1_CL 

| where Type_s == "Management" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```
 
## <a name="stop-sending-data-to-log-analytics"></a>Adatok küldésének leállítása a Log Analytics szolgáltatásba 

Ha le szeretné állítani az adatok küldését egy meglévő bérlőtől a Log Analytics szolgáltatásba, futtassa a következő parancsmagot, és állítson be üres karakterláncokat:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Ezt a parancsmamot minden bérlőhöz futtatnia kell, amelyről le szeretné állítani az adatok küldését. 

## <a name="next-steps"></a>További lépések 

A diagnosztikai szolgáltatás által azonosítható gyakori hibaforgatókönyvek áttekintéséhez olvassa [el a Problémák azonosítása és diagnosztizálása című témakört.](diagnostics-role-service.md#common-error-scenarios)
