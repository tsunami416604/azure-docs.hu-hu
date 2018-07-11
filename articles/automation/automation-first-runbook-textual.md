---
title: Az Azure Automationben az első PowerShell-alapú munkafolyamat-forgatókönyvem
description: Ez az oktatóanyag bemutatja egy egyszerű szöveges forgatókönyv PowerShell-munkafolyamattal való létrehozását, tesztelését és közzétételét.
keywords: powershell-munkafolyamat, powershell-munkafolyamat példák, munkafolyamat powershell
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 32ad5ef4f297a64f0925febb247ccb117dffedf0
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928590"
---
# <a name="my-first-powershell-workflow-runbook"></a>Az első PowerShell-alapú munkafolyamat-forgatókönyvem

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Egy Azure Automation [PowerShell-alapú munkafolyamat-forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks) létrehozását bemutató oktatóanyag. Először egy egyszerű runbookot, amely tesztelése, és közzé is teszünk a runbook-feladat állapotának nyomon követését. Ezután módosítja a runbookot, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure-beli virtuális gépet. Végül, a runbook még robusztusabbá runbook-paramétereket adunk hozzá.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez.  Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Állítsa le és indítsa el ezt a gépet, hogy egy éles környezetben lévő virtuális gép nem lehet.

## <a name="step-1---create-new-runbook"></a>1. lépés – Új forgatókönyv létrehozása
Először hozzon létre egy egyszerű runbookot, amely szöveget adja vissza *Hello World*.

1. Az Azure Portalon nyissa meg az Automation-fiókját.

   Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.

1. Kattintson a **Runbookok** alatt **Folyamatautomatizálás** forgatókönyvek listájának megnyitásához.
2. Hozzon létre egy új runbookot kattintva a **+ forgatókönyv hozzáadása** gombra, majd **hozzon létre egy új runbookot**.
3. Adja a forgatókönyvnek a *MyFirstRunbook-Workflow* nevet.
4. Ebben az esetben fog létrehozni egy [PowerShell-munkafolyamati forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks) ezért válassza **Powershell-munkafolyamat** a **Runbook típusa**.
5. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-code-to-the-runbook"></a>2. lépés – Kód hozzáadása a forgatókönyvhöz
Beírhat közvetlenül a forgatókönyvbe kódot, vagy választhat parancsmagokat, forgatókönyveket és adategységeket a Könyvtár vezérlőből, majd hozzáadhatja őket a forgatókönyvhöz a kapcsolódó paraméterekkel együtt. Ez a forgatókönyv azt írja be közvetlenül a forgatókönyvbe.

1. A runbook jelenleg üres, eltekintve a kötelező *munkafolyamat* kulcsszó, a runbook, és a zárójelek között, amelyek a teljes munkafolyamatot encases nevére.

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

   A feladat állapota kezdetben *Várólistán*. Ez azt jelöli, hogy egy felhőben lévő forgatókönyv-feldolgozó elérhetővé válására vár. Elérésűből *kezdő* Ha egy feldolgozó elvállalja a feladatot, majd *futó* amikor a runbook elkezd futni.  

1. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Megjelenik az Ön esetében *Hello World*.<br><br> ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
2. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4. lépés: Közzététel és a forgatókönyv indítása
A létrehozott runbook még mindig Piszkozat módban van. Üzemi környezetben való futtatás előtt közzé kell. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Az Ön esetében még nem rendelkezik közzétett verzió, mert az újonnan létrehozott, a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
2. Ha balra görgetve megtekinti a forgatókönyvet a a **Runbookok** panelen most megjelenít egy **szerzői állapot** , **közzétett**.
3. Görgessen vissza jobbra a **MyFirstRunbook-Workflow** panel megtekintéséhez.  
   A felül látható lehetőségekkel elindíthatjuk a forgatókönyvet, ütemezhetjük egy későbbi időpontban való indításra, vagy létrehozhatunk egy [webhookot](automation-webhooks.md), amely segítségével elindítható a forgatókönyv egy HTTP-hívással.
4. csak át szeretné a runbook elindításához kattintson **Start** , majd **Igen** amikor a rendszer kéri.<br><br> ![Runbook indítása](media/automation-first-runbook-textual/automation-runbook-controls-start.png)
5. Egy feladatpanel a létrehozott runbook-feladathoz. Zárja be ezt a panelt, de ebben az esetben, nyitva hagyja, így megtekintheti a feladat előrehaladását.
6. A feladat állapota a **feladat összegzése** és megegyezik-e az állapot, a tesztelt, amikor a runbook vonatkozott.<br><br> ![Feladat összegzése](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)
7. Ha a forgatókönyv a *Befejezve* állapotot mutatja, kattintson a **Kimenet** lehetőségre. A Kimenet panel megnyílik, és láthatja a *Hello World*.<br><br> ![Feladat összegzése](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  
8. Zárja be a Kimenet panelt.
9. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. csak akkor jelenik meg *Hello World* a kimeneti stream, de ez megjeleníthet egyéb streameket is egy forgatókönyv feladatból, mint például a részletes vagy a hiba Ha a forgatókönyv ezekbe ír.<br><br> ![Feladat összegzése](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)
10. A MyFirstRunbook panelre való visszatéréshez zárja be a Streamek és a Feladat panelt.
11. A forgatókönyv Feladatok paneljének megnyitásához kattintson a **Feladatok** lehetőségre. Ez felsorolja az összes, a forgatókönyv által létrehozott feladatot. megjelenik a listán, mert Ön csak egyszer futtatta a feladatot egy feladat csak.<br><br> ![Feladatok](media/automation-first-runbook-textual/runbook-control-job-tile.png)
12. Kattintson a ezt a feladatra kattintva megnyithatja ugyanazt a feladat panelt, amelyet már látott a runbook elindításakor. Ez lehetővé teszi, hogy az időben visszamenve megtekintse egy adott forgatókönyvhöz létrehozott összes feladat részleteit.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez
már teszteltük és a runbook közzétett, de eddig még nem csinál semmi hasznosat. azt szeretnénk, hogy az Azure-erőforrások kezeléséhez. Nem lesz képes megtenni, kivéve, ha végez hitelesítést az között említett hitelesítő adatok használatával, ha a [Előfeltételek](#prerequisites). megteheti, hogy a a **Connect-AzureRmAccount** parancsmagot.

1. Kattintson a MyFirstRunbook-Workflow panel **Szerkesztés** gombjára a szöveges szerkesztő megnyitásához.
2. nem kell a **Write-Output** sorra már nincs szükségünk, ezért lépjen tovább, és törölje azt.
3. Vigye a kurzort egy üres sorra a zárójelek között.
4. Írja be, vagy másolja és illessze be a következő kódot, amely kezeli a hitelesítést az Automation futtató fiókjával.

   ```powershell-interactive
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** és **Login-AzureRmAccount** aliasai most **Connect-AzureRMAccount**. Ha a **Connect-AzureRMAccount** parancsmag nem létezik, akkor használhatja **Add-AzureRmAccount** vagy **Login-AzureRmAccount**, vagy beállíthatja a [a modulok frissítése ](automation-update-azure-modules.md) az Automation-fiókban a legújabb verzióra. 

> [!NOTE]
> Szüksége lehet [a modulok frissítése](automation-update-azure-modules.md) annak ellenére, hogy az imént létrehozott egy új automation-fiókot.

5. Kattintson a **teszt panel** úgy, hogy a runbook teszteléséhez.
6. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, a kimenetnek a fiókja alapvető adatait kell megjelenítenie, a következőhöz hasonló módon. Ez ellenőrzi, hogy érvényes-e a hitelesítő adat.<br><br> ![Hitelesítés](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6. lépés: Kód hozzáadása a virtuális gép indításához
Most, hogy a runbook hitelesíti az Azure-előfizetéshez, erőforrások is kezelhetők. hozzáadhat egy parancs egy virtuális gép elindításához. Válassza ki bármelyik virtuális gépet az Azure-előfizetésben, és most ezt a nevet a runbook hardcoding áll.

1. Miután *Connect-AzureRmAccount*, típus *Start-AzureRmVM-Name 'VMName' - ResourceGroupName 'NameofResourceGroup'* biztosítása és az erőforráscsoport nevét a virtuális gép elindításához.  

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   }
   ```
2. Mentse a forgatókönyvet, és kattintson a **teszt panel** így tesztelheti.
3. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7. lépés – Bemeneti paraméter hozzáadása a forgatókönyvhöz
a runbook jelenleg elindítja a virtuális gépet, hogy Ön szoftveresen kötött a runbook, de azt hasznosabb lenne, ha a virtuális gép határozhatja meg a forgatókönyv elindulásakor. A bemeneti paramétereket adhat hozzá a runbook funkció.

1. Adja hozzá a *VMName* és a *ResourceGroupName* paramétereket a forgatókönyvhöz, és használja ezeket a változókat a **Start-AzureRmVM** parancsmaggal, amint az az alábbi példában látható.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```
2. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a teszt lévő két bemeneti változóhoz.
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
