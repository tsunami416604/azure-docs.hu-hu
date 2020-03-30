---
title: A Windows virtuális gépek ütemezett eseményeinek figyelése az Azure-ban
description: Ismerje meg, hogyan figyelheti az Azure virtuális gépeit az ütemezett eseményekhez.
services: virtual-machines-windows
documentationcenter: ''
author: mysarn
manager: gwallace
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.date: 08/20/2019
ms.author: sarn
ms.topic: conceptual
ms.openlocfilehash: 1cda07c18e4f5ef2a8c00b6a275f22ecc0935751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073318"
---
# <a name="monitoring-scheduled-events"></a>Ütemezett események figyelése

A frissítések minden nap az Azure különböző részeire vonatkoznak, hogy a szolgáltatások biztonságosés naprakész ek legyenek. A tervezett frissítések mellett nem tervezett események is előfordulhatnak. Ha például hardveres romlást vagy hibát észlel, előfordulhat, hogy az Azure-szolgáltatásoknak nem tervezett karbantartást kell végrehajtaniuk. Az élő áttelepítés, a memória megőrzése frissítések és általában a frissítések hatásának szigorú sávja, a legtöbb esetben ezek az események szinte átláthatóak az ügyfelek számára, és nincs hatásuk, vagy legfeljebb néhány másodpercnyi virtuálisgép-lefagyást okoznak. Egyes alkalmazások esetében azonban még néhány másodpercnyi virtuálisgép-fagyasztás is hatással lehet. Fontos, hogy előre megismerjék a közelgő Azure-karbantartást, hogy a lehető legjobb élményt nyújtják ezekhez az alkalmazásokhoz. [Az Ütemezett események szolgáltatás](scheduled-events.md) programozott felületet biztosít a közelgő karbantartásról, és lehetővé teszi a karbantartás kecses kezelését. 

Ebben a cikkben bemutatjuk, hogyan használhatja az ütemezett eseményeket a virtuális gépeket érintő karbantartási eseményekről való értesítéshez, és hozzon létre néhány alapvető automatizálást, amely segíthet a figyelésben és az elemzésben.


## <a name="routing-scheduled-events-to-log-analytics"></a>Ütemezett események útválasztása a Log Analytics szolgáltatásba

Az ütemezett események az [Azure Instance metaadat-szolgáltatásának](instance-metadata-service.md)részeként érhetők el, amely minden Azure virtuális gépen elérhető. Az ügyfelek az automatizálás taséiban lekérdezhetik a virtuális gépek végpontját az ütemezett karbantartási értesítések megkereséséhez és kockázatcsökkentést végezhetnek, például az állapot mentéséhez és a virtuális gép elforgatásából való kiváltásához. Javasoljuk, hogy az ütemezett események et az ütemezett események rögzítéséhez végezze el, így rendelkezhet az Azure karbantartási eseményeinek naplózási naplójával. 

Ebben a cikkben bemutatjuk, hogyan rögzítheti a karbantartási ütemezett eseményeket a Log Analytics szolgáltatásba. Ezután néhány alapvető értesítési műveletet indítunk el, például e-mailt küldünk a csapatának, és történelmi képet kapunk a virtuális gépeket érintett összes eseményről. Az esemény összesítése és automatizálása fogjuk használni [Log Analytics](/azure/azure-monitor/learn/quick-create-workspace), de használhatja a figyelési megoldás gyűjteni ezeket a naplókat, és elindítja az automatizálást.

![Az esemény életciklusát bemutató diagram](./media/notifications/events.png)

## <a name="prerequisites"></a>Előfeltételek

Ebben a példában létre kell hoznia egy [Windows virtuális gépet egy rendelkezésre állási készletben.](tutorial-availability-sets.md) Ütemezett események értesítéseket biztosít a változásokat, amelyek hatással lehetnek a virtuális gépek a rendelkezésre állási csoport, a felhőszolgáltatás, a virtuálisgép-méretezési készlet vagy önálló virtuális gépek. Egy [olyan szolgáltatást](https://github.com/microsoft/AzureScheduledEventsService) fogunk futtatni, amely lekérdezi az ütemezett eseményeket az egyik virtuális gépen, amely gyűjtőként fog működni, hogy eseményeket kapjon a rendelkezésre állási csoportban lévő összes többi virtuális géphez.    

Ne törölje a csoport erőforráscsoportot az oktatóanyag végén.

Emellett létre kell [hoznia egy Log Analytics-munkaterületet,](/azure/azure-monitor/learn/quick-create-workspace) amelyet a rendelkezésre állási csoportban lévő virtuális gépekből származó információk összesítéséhez használunk.

## <a name="set-up-the-environment"></a>A környezet beállítása

Most már 2 kezdeti virtuális gépegy rendelkezésre állási csoportban. Most létre kell hoznunk egy 3rd VM, úgynevezett myCollectorVM, ugyanabban a rendelkezésre állási csoportban. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

Töltse le a projekt telepítési .zip fájlját a [GitHubról.](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip)

Csatlakozzon **a myCollectorVM-hez,** és másolja a .zip fájlt a virtuális gépre, és bontsa ki az összes fájlt. A virtuális gépen nyisson meg egy PowerShell-parancssorba. Helyezze át a kérdést `SchService.ps1`a következő `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`mappába, például: , és állítsa be a szolgáltatást.

```powershell
.\SchService.ps1 -Setup
```

Indítsa el a szolgáltatást.

```powershell
.\SchService.ps1 -Start
```

A szolgáltatás most antól 10 másodpercenként megkezdi a lekérdezést az ütemezett eseményekhez, és jóváhagyja az eseményeket a karbantartás felgyorsítása érdekében.  Freeze, Reboot, Redeploy és Preempt az események ütemezése események által rögzített események.   Vegye figyelembe, hogy az esemény jóváhagyása előtt kiterjesztheti a parancsfájlt bizonyos megoldások aktiválásához.

Ellenőrizze a szolgáltatás állapotát, és győződjön meg arról, hogy fut.

```powershell
.\SchService.ps1 -status  
```

Ennek vissza `Running`kell térnie .

A szolgáltatás most antól 10 másodpercenként megkezdi a lekérdezést az ütemezett eseményekhez, és jóváhagyja az eseményeket a karbantartás felgyorsítása érdekében.  Freeze, Reboot, Redeploy és Preempt az események ütemezése által rögzített események. Kiterjesztheti a parancsfájlt, hogy az esemény jóváhagyása előtt bizonyos megoldásokat váltson ki.

Ha a fenti események bármelyikét a Schedule Event szolgáltatás rögzíti, akkor az megjelenik az Alkalmazás eseménynaplójának eseményállapotában, eseménytípusában, erőforrásokban (virtuálisgép nevek) és NotBefore (minimális értesítési időszak) való bejelentkezése. Az 1234-es azonosítójú eseményeket az alkalmazás eseménynaplójában találhatja meg.

A szolgáltatás beállítása és indítása után naplózza az eseményeket a Windows alkalmazásnaplóiban.   Ennek ellenőrzéséhez indítsa újra az egyik virtuális gépet a rendelkezésre állási csoportban, és meg kell jelennie egy esemény, amely bejelentkezett az Eseménynaplóban a Windows naplók > alkalmazásnapló, amely a virtuális gép újraindítását mutatja. 

![Képernyőkép az eseménynaplóról.](./media/notifications/event-viewer.png)

Amikor az eseményeket az Ütemezési esemény szolgáltatás rögzíti, akkor az eseményállapot, eseménytípus, erőforrások (vm-név) és notbefore (minimális értesítési időszak) naplója is be lesz jelentkezve az alkalmazásba. Az 1234-es azonosítójú eseményeket az alkalmazás eseménynaplójában találhatja meg.

> [!NOTE] 
> Ebben a példában a virtuális gépek egy rendelkezésre állási csoportban voltak, amely lehetővé tette számunkra, hogy egyetlen virtuális gépet jelöljünk ki gyűjtőként, amely figyeli és irányítja az ütemezett eseményeket a naplóelemzési munkaterületünkre. Ha önálló virtuális gépekkel rendelkezik, futtathatja a szolgáltatást minden virtuális gépen, majd külön-külön csatlakoztathatja őket a naplóelemzési munkaterülethez.
>
> A mi létre, úgy döntöttünk, a Windows, de lehet tervezni egy hasonló megoldás linuxos.

Bármikor leállíthatja / eltávolíthatja az Ütemezett esemény szolgáltatást `–stop` `–remove`a kapcsolók és segítségével.

## <a name="connect-to-the-workspace"></a>Csatlakozás a munkaterülethez


Most szeretnénk csatlakoztatni egy Log Analytics-munkaterületet a gyűjtő virtuális géphez. A Log Analytics munkaterület adattárként működik, és az eseménynapló-gyűjteményt úgy állítjuk be, hogy rögzítse az alkalmazásnaplókat a gyűjtő virtuális gépről. 

 Az ütemezett események átirányítása az eseménynaplóba, amelyet a szolgáltatás unk alkalmazásnaplóként ment, csatlakoztatnia kell a virtuális gépet a Log Analytics-munkaterülethez.  
 
1. Nyissa meg a létrehozott munkaterület lapját.
1. A **Csatlakozás adatforráshoz** csoportban válassza az Azure virtuális gépek (VM).Under Connect to a data source select **Azure virtual machines (VM)**.

    ![Csatlakozás virtuális géphez adatforrásként](./media/notifications/connect-to-data-source.png)

1. Keresse meg és válassza **a myCollectorVM elemet.** 
1. A **myCollectorVM**új lapján válassza a **Csatlakozás**lehetőséget.

Ez telepíti a [Microsoft Monitoring agent](/azure/virtual-machines/extensions/oms-windows) a virtuális gép. Néhány percet vesz igénybe, hogy csatlakoztassa a virtuális gép a munkaterülethez, és telepítse a bővítményt. 

## <a name="configure-the-workspace"></a>A munkaterület konfigurálása

1. Nyissa meg a munkaterület lapját, és válassza a **Speciális beállítások lehetőséget**.
1. Válassza a bal oldali menü **Adatok** parancsát, majd a **Windows eseménynaplók lehetőséget.**
1. A **Collect a következő eseménynaplókban**kezdje el beírni az *alkalmazást,* majd válassza az **Alkalmazás** elemet a listából.

    ![Válassza a Speciális beállítások lehetőséget](./media/notifications/advanced.png)

1. Hagyja **a HIBA**, **Figyelem**és **INFORMÁCIÓK** beállítást, majd a Beállítások mentéséhez válassza a **Mentés** gombot.


> [!NOTE]
> Lesz némi késés, és akár 10 percet is igénybe vehet, mielőtt a napló elérhetővé válik. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Riasztási szabály létrehozása az Azure Monitorhasználatával 


Miután az események et lelökték a Log Analytics szolgáltatásba, a következő [lekérdezés](/azure/azure-monitor/log-query/get-started-portal) futtatásával megkeresheti az ütemezési eseményeket.

1. A lap tetején válassza a **Naplók** lehetőséget, és illessze be a következőt a szövegmezőbe:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. Válassza a **Mentés**lehetőséget, majd írja be a *logQuery* parancsot a névbe, hagyja a **Lekérdezés** nevet típusként, írja be a *VMLogs* **parancsot kategóriaként,** majd válassza a **Mentés parancsot.** 

    ![A lekérdezés mentése](./media/notifications/save-query.png)

1. Válassza az **Új riasztási szabály** lehetőséget. 
1. A **Szabály létrehozása** lapon `collectorworkspace` hagyja **erőforrásként.**
1. A **Feltétel csoportban**válassza ki a *bejegyzést, amikor a vevői naplókeresés a. <login undefined> * Megnyílik **a Jellogika konfigurálása** lap.
1. A **Küszöbérték mezőbe**írja be a *0* értéket, majd válassza a **Kész**lehetőséget.
1. A **Műveletek csoportban**válassza **a Műveletcsoport létrehozása lehetőséget.** Megnyílik **a Műveletcsoport hozzáadása** lap.
1. A **Műveletcsoport nevének**mezőbe írja be a *myActionGroup csoportot.*
1. A **Rövid név mezőbe**írja be a **myActionGroup csoportot.**
1. Az **Erőforrás csoportban**válassza a **myResourceGroupAvailability**lehetőséget.
1. A Műveletek **csoportban** az ACTION NAME mezőbe írja be **az E-mail**nevet, és válassza **az E-mail/SMS/Push/Voice lehetőséget.** Megnyílik **az E-mail/SMS/Push/Voice** oldal.
1. Válassza **az E-mail**lehetőséget, írja be az e-mail címét, majd kattintson az **OK gombra.**
1. A **Műveletcsoport hozzáadása** lapon válassza az **OK gombot.** 
1. A **Szabály létrehozása** lap **FIGYELMEZTETÉSRÉSZLETEI csoportjában**írja be a *myAlert* parancsot a **riasztási szabály nevéhez,** majd írja be az *E-mail riasztási szabályt* a Leírás **mezőbe.**
1. Ha végzett, válassza **a Figyelmeztetési szabály létrehozása**lehetőséget.
1. Indítsa újra az egyik virtuális gépet a rendelkezésre állási csoportban. Néhány percen belül e-mailt kell kapnia arról, hogy a riasztás aktiválódott.

A riasztási szabályok kezeléséhez lépjen az erőforráscsoportra, válassza a bal oldali menü **Riasztások** parancsát, majd válassza a **riasztási szabályok kezelése lehetőséget** a lap tetején.

     
## <a name="next-steps"></a>További lépések

További információ: [Az Ütemezett események szolgáltatás](https://github.com/microsoft/AzureScheduledEventsService) lap a GitHubon.
