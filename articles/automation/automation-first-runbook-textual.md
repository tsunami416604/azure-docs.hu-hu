---
title: "Az első PowerShell-munkafolyamati forgatókönyv az Azure Automationben"
description: "Ez az oktatóanyag bemutatja egy egyszerű szöveges forgatókönyv PowerShell-munkafolyamattal való létrehozását, tesztelését és közzétételét."
keywords: "powershell-munkafolyamat, powershell-munkafolyamat példák, munkafolyamat powershell"
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: 61191e78ebd2f0d7a960dfb0c74a1d8260331212
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="my-first-powershell-workflow-runbook"></a>Az első PowerShell-alapú munkafolyamat-forgatókönyvem

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Egy Azure Automation [PowerShell-alapú munkafolyamat-forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks) létrehozását bemutató oktatóanyag. Egy egyszerű runbookot, tesztelése és közzététele során ismertető a runbook-feladatok állapotának nyomon követése a kiindulási pont. Ezután módosítja a runbookot, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure-beli virtuális gépet. Végül, ellenőrizze a runbook robusztusabb runbook paraméterek hozzáadásával.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez.  Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Állítsa le és indítsa el ezt a gépet, így azt nem szabad a virtuális gép éles.

## <a name="step-1---create-new-runbook"></a>1. lépés – Új forgatókönyv létrehozása
Ön először hozzon létre egy egyszerű runbookot, amely a szöveg *Hello World*.

1. Az Azure Portalon nyissa meg az Automation-fiókját.

   Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.

1. Kattintson a **Runbookok** alatt **folyamat** runbookok listájának megnyitásához.
2. Hozzon létre egy új runbookot parancsával a **+ Hozzáadás runbook** gombra, majd **hozzon létre egy új runbookot**.
3. Adja a forgatókönyvnek a *MyFirstRunbook-Workflow* nevet.
4. Ebben az esetben fog létrehozni egy [PowerShell-munkafolyamati forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks) ezért **Powershell munkafolyamat** a **runbooktípusba**.
5. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-code-to-the-runbook"></a>2. lépés – Kód hozzáadása a forgatókönyvhöz
Beírhat közvetlenül a forgatókönyvbe kódot, vagy választhat parancsmagokat, forgatókönyveket és adategységeket a Könyvtár vezérlőből, majd hozzáadhatja őket a forgatókönyvhöz a kapcsolódó paraméterekkel együtt. Ennél a bemutatónál beírása közvetlenül a runbookot.

1. A runbook állapota jelenleg üres, csak a szükséges *munkafolyamat* kulcsszó, a runbookot, és a kapcsos zárójelek, amely a teljes munkafolyamat encases nevét.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```
2. Írja be a következőt: *Write-Output "Hello World."* a zárójelek közé.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```
3. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a>3. lépés – A forgatókönyv tesztelése
Mielőtt közzéteszi a runbookot, hogy éles üzemben is elérhető legyen, tesztelnie kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.
2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
3. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), és megjelenik annak állapota.

   A feladat állapota kezdetben *Várólistán*. Ez azt jelöli, hogy egy felhőben lévő forgatókönyv-feldolgozó elérhetővé válására vár. Kerül át *indítása* dolgozó jogcímeket a feladatot, amikor, majd *futtató* amikor a runbook ténylegesen elindul.  

1. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Abban az esetben kell megjelennie *Hello World*.<br><br> ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
2. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4. lépés: Közzététel és a forgatókönyv indítása
A létrehozott runbook még mindig Piszkozat módban van. Éles környezetben futtatása előtt tegye közzé kell. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Az Ön esetében még nem rendelkezik egy közzétett változata, mert a runbook újonnan létrehozott.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
2. Ha a runbook megtekintése balra a **Runbookok** ablaktáblán, akkor megjelenik egy **szerzői állapot** a **közzétett**.
3. Görgessen vissza jobbra a **MyFirstRunbook-Workflow** panel megtekintéséhez.  
   A felül látható lehetőségekkel elindíthatjuk a forgatókönyvet, ütemezhetjük egy későbbi időpontban való indításra, vagy létrehozhatunk egy [webhookot](automation-webhooks.md), amely segítségével elindítható a forgatókönyv egy HTTP-hívással.
4. szeretné elindítani a runbookot, kattintson a **Start** , majd **Igen** megjelenésekor.<br><br> ![Runbook indítása](media/automation-first-runbook-textual/automation-runbook-controls-start.png)
5. A feladatok ablaktábla a runbook-feladat létrehozott van megnyitva. Zárja be az ezen az ablaktáblán, de ebben az esetben hagyja nyitva, figyelheti a feladat előrehaladását.
6. A feladat állapota látható **feladat összegzése** és az állapot, amikor Ön tesztelése a runbook látott egyezik.<br><br> ![Feladat összegzése](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)
7. Ha a forgatókönyv a *Befejezve* állapotot mutatja, kattintson a **Kimenet** lehetőségre. A kimeneti ablaktábla már meg van nyitva, és megtekintheti a *Hello World*.<br><br> ![Feladat összegzése](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  
8. Zárja be a Kimenet panelt.
9. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. Csak akkor jelenik meg *Hello World* a kimeneti adatfolyam, de ez lehet megjelenítése más adatfolyamok, például a Verbose és a hiba a runbook-feladat Ha a runbook írja őket.<br><br> ![Feladat összegzése](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)
10. A MyFirstRunbook panelre való visszatéréshez zárja be a Streamek és a Feladat panelt.
11. A forgatókönyv Feladatok paneljének megnyitásához kattintson a **Feladatok** lehetőségre. Ez felsorolja az összes, a forgatókönyv által létrehozott feladatot. Csak megtekintheti egy feladat csak futtatása óta a feladat többször szerepel.<br><br> ![Feladatok](media/automation-first-runbook-textual/runbook-control-job-tile.png)
12. A feladat a azonos feladat panelen, amely runbook indításakor megtekintett elemre kattinthat. Ez lehetővé teszi, hogy az időben visszamenve megtekintse egy adott forgatókönyvhöz létrehozott összes feladat részleteit.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez
Hogy tesztelni, és a runbook közzétett, de eddig semmi különöset nem hasznos. Szeretné az Azure-erőforrások kezeléséhez. Nem lehet majd használni, ha nincs, hitelesítéshez, amelyekre a hitelesítő adatok használatával, ha ehhez a [Előfeltételek](#prerequisites). megteheti, hogy a a **Add-AzureRMAccount** parancsmag.

1. Kattintson a MyFirstRunbook-Workflow panel **Szerkesztés** gombjára a szöveges szerkesztő megnyitásához.
2. Nincs szükség a **Write-Output** többé. sor, úgy lépjen tovább, és törölje azt.
3. Vigye a kurzort egy üres sorra a zárójelek között.
4. Írja be, vagy másolja és illessze be a következő kódot, amely kezeli a hitelesítést az Automation futtató fiókjával.

   ```powershell-interactive
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
5. Kattintson a **teszt ablaktábla** , hogy a runbook tesztelheti.
6. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, a kimenetnek a fiókja alapvető adatait kell megjelenítenie, a következőhöz hasonló módon. Ez ellenőrzi, hogy érvényes-e a hitelesítő adat.<br><br> ![Hitelesítés](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6. lépés: Kód hozzáadása a virtuális gép indításához
Most, hogy a runbook hitelesíti az Azure-előfizetéshez, kezelheti az erőforrásokat. A virtuális gép elindításához-utasítást adni. Ki tudja választani a virtuális gépek az Azure-előfizetése, és most, hogy a runbook neve hardcoding.

1. Az *Add-AzureRmAccount* után írja be a következőt: *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'*. Ezzel megadja az elindítani kívánt virtuális gép nevét, valamint az erőforráscsoportjának nevét.  

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   }
   ```
2. Mentse el a runbookot, és kattintson a **teszt ablaktábla** , hogy a teszteléshez le.
3. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7. lépés – Bemeneti paraméter hozzáadása a forgatókönyvhöz
a runbook jelenleg elindul a virtuális gépet, hogy Ön szoftveresen kötött a runbookokban, de lenne hasznos, ha a virtuális gép megadhatja a runbook indításakor. Bemeneti paramétereket ad hozzá a runbook funkció.

1. Adja hozzá a *VMName* és a *ResourceGroupName* paramétereket a forgatókönyvhöz, és használja ezeket a változókat a **Start-AzureRmVM** parancsmaggal, amint az az alábbi példában látható.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```
2. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. A két bemeneti változók a vizsgálat most biztosítható értékeket.
3. Zárja be a Teszt panelt.
4. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.
5. Állítsa le az előző lépésben elindított virtuális gépet.
6. Kattintson az **Indítás** gombra a forgatókönyv elindításához. Írja be a **VMName** és a **ResourceGroupName** értéket az elindítani kívánt virtuális géphez.<br><br> ![Runbook indítása](media/automation-first-runbook-textual/automation-pass-params.png)<br>  
7. Ha a forgatókönyv kész, ellenőrizze, hogy a virtuális gép elindult-e.  

## <a name="next-steps"></a>További lépések
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-forgatókönyvem](automation-first-runbook-textual-powershell.md).
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* További információk a PowerShell-parancsprogramok támogatásáról: [PowerShell-parancsprogramok natív támogatása az Azure Automationben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
