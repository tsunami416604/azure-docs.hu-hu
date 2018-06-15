---
title: Az első PowerShell-forgatókönyv az Azure Automationben
description: Ez az oktatóanyag bemutatja egy egyszerű PowerShell-forgatókönyv létrehozását, tesztelését és közzétételét.
keywords: azure powershell, powershell-szkript oktatóanyag, powershell automation
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9fe9d98b694b8c42f3342e615d92fae9824dca26
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195148"
---
# <a name="my-first-powershell-runbook"></a>Az első PowerShell-forgatókönyvem

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Ez az oktatóanyag bemutatja, hogyan hozhat létre az Azure Automationben egy [PowerShell-forgatókönyvet](automation-runbook-types.md#powershell-runbooks). Egy egyszerű runbookot, tesztelése és közzététele során megtanulhatja a runbook-feladatok állapotának nyomon követése a kiindulási pont. Ezután módosítja a runbookot, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure-beli virtuális gépet. Végül akkor a runbook robusztusabb runbook paraméterek hozzáadásával.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Ezt a gépet leállítja és elindítja, tehát ne olyan virtuális gépet használjon, amely élesben működik.

## <a name="step-1---create-new-runbook"></a>1. lépés – Új forgatókönyv létrehozása
Ön először hozzon létre egy egyszerű runbookot, amely a szöveg *Hello World*.

1. Az Azure Portalon nyissa meg az Automation-fiókját.

   Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.

1. Kattintson a **Runbookok** alatt **folyamat** runbookok listájának megnyitásához.
2. Hozzon létre egy új runbookot kattintva a **+ Hozzáadás runbook** gombra, majd **hozzon létre egy új runbookot**.
3. Adja a forgatókönyvnek a *MyFirstRunbook-PowerShell* nevet.
4. Ebben az esetben fog létrehozni egy [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) ezért válassza **Powershell** a **runbooktípusba**.
5. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-code-to-the-runbook"></a>2. lépés – Kód hozzáadása a forgatókönyvhöz
Beírhat közvetlenül a forgatókönyvbe kódot, vagy választhat parancsmagokat, forgatókönyveket és adategységeket a Könyvtár vezérlőből, majd hozzáadhatja őket a forgatókönyvhöz a kapcsolódó paraméterekkel együtt. Jelen kalauz használatához írja a közvetlenül a runbookot.

1. A runbook állapota jelenleg üres típus *Write-Output "Hello World".* karaktersort a szkript törzsébe.<br><br> ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a>3. lépés – A forgatókönyv tesztelése
Mielőtt közzéteszi a runbookot, hogy éles üzemben is elérhető legyen, tesztelnie kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.
2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
3. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), és megjelenik annak állapota.

   A feladat állapota kezdetben *Várólistán*. Ez azt jelöli, hogy egy felhőben lévő runbook-feldolgozó elérhetővé válására vár. Kerül át *indítása* dolgozó jogcímeket a feladatot, amikor, majd *futtató* amikor a runbook ténylegesen elindul.  

1. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Abban az esetben kell megjelennie *Hello World*.<br><br> ![Teszt panel kimenete](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
2. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4. lépés: Közzététel és a forgatókönyv indítása
A létrehozott runbook még mindig Piszkozat módban van. Éles környezetben futtatása előtt tegye közzé kell.  Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Az Ön esetében még nem rendelkezik egy közzétett változata, mert a runbook újonnan létrehozott.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
2. Ha a runbook megtekintése balra a **Runbookok** ablaktáblán, akkor megjelenik egy **szerzői állapot** a **közzétett**.
3. Görgessen vissza jobbra a **MyFirstRunbook-PowerShell** panel megtekintéséhez.  
   A felül látható lehetőségekkel elindíthatjuk és megtekinthetjük a runbookot, ütemezhetjük egy későbbi időpontban való indításra, vagy létrehozhatunk egy [webhookot](automation-webhooks.md), amely segítségével elindítható a runbook egy HTTP-hívással.
4. A runbook indításához, kattintson a kívánt **Start** , majd **Ok** a runbook meghívása lap megnyitásakor.
5. A runbook-feladat létrehozott egy feladat lap van megnyitva. Zárja be az ezen az ablaktáblán, de ebben az esetben hagyja nyitva, figyelheti a feladat előrehaladását.
6. A feladat állapota látható **feladat összegzése** és az állapot, amikor Ön tesztelése a runbook látott egyezik.<br><br> ![Feladat összegzése](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. Egyszer a runbook állapota látható *befejezve*a **áttekintése** kattintson **kimeneti**. A kimeneti ablaktábla már meg van nyitva, és megtekintheti a *Hello World*.<br><br> ![Feladat kimenete](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. Zárja be a kimeneti lapot.
9. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. A kimeneti streamben csak a *Hello World* eredményt látja, de itt megjelenhetnek egyéb streamek is egy runbook-feladatból, mint például a Részletes vagy a Hiba, ha a forgatókönyv ezekbe ír.<br><br> ![Minden napló](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. Zárja be az adatfolyamok lapján, a feladatok lapon a MyFirstRunbook-PowerShell lapra való visszatéréshez.
11. A **részletek**, kattintson a **feladatok** ennek a runbooknak a feladatok ablak megnyitásához. Ez felsorolja az összes, a forgatókönyv által létrehozott feladatot. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtatta a feladatot.<br><br> ![Feladatlista](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már látott a runbook elindításakor. Ez lehetővé teszi, hogy az időben visszamenve megtekintse egy adott forgatókönyvhöz létrehozott összes feladat részleteit.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez
Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Már nem tudja használni, ha nincs, hitelesítéshez, amelyekre a hitelesítő adatok használatával, ha ehhez a [Előfeltételek](#prerequisites). Megteheti, hogy a a **Connect-AzureRmAccount** parancsmag.

1. A szöveges-szerkesztő megnyitásához **szerkesztése** MyFirstRunbook-PowerShell lapon.
2. Nincs szükség a **Write-Output** többé. sor, úgy lépjen tovább, és törölje azt.
3. Írja be, vagy másolja és illessze be a következő kódot, amely kezeli a hitelesítést az Automation futtató fiókjával:
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
   <br>
4. Kattintson a **teszt ablaktábla** , hogy a runbook tesztelheti.
5. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, a kimenetnek a fiókja alapvető adatait kell megjelenítenie, a következőhöz hasonló módon. Ez ellenőrzi, hogy érvényes-e a hitelesítő adat.<br><br> ![Hitelesítés](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6. lépés: Kód hozzáadása a virtuális gép indításához
Most, hogy a runbook hitelesíti az Azure-előfizetéshez, kezelheti az erőforrásokat. A virtuális gép elindításához-utasítást adni. Kiválaszthatja a virtuális gépek az Azure-előfizetéssel, valamint a most meg megoldás, amely nevet a runbook.

1. Után *Connect-AzureRmAccount*, típus *Start-AzureRmVM-név (VMName) - ResourceGroupName "NameofResourceGroup"* biztosít, és az erőforráscsoport nevét a virtuális gép elindításához.  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Mentse el a runbookot, és kattintson a **teszt ablaktábla** , hogy a teszteléshez le.
3. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7. lépés – Bemeneti paraméter hozzáadása a forgatókönyvhöz
A runbook jelenleg elindul a virtuális gépet, hogy Ön szoftveresen kötött a runbookokban, de lenne hasznos, ha a virtuális gépet adjon meg a runbook indításakor. Bemeneti paramétereket ad hozzá a runbook funkció.

1. Paraméterek hozzáadása *VMName* és *ResourceGroupName* a runbookhoz, és ezek a változók a **Start-AzureRmVM** parancsmag az alábbi példában látható módon.

   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
1. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a teszt során használt két bemeneti változóhoz.
2. Zárja be a Teszt panelt.
3. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.
4. Állítsa le az előző lépésben elindított virtuális gépet.
5. Kattintson a **OK** a runbook elindításához. Írja be a **VMName** és a **ResourceGroupName** értéket az elindítani kívánt virtuális géphez.<br><br> ![Paraméter átadása](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
6. Ha a forgatókönyv kész, ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="differences-from-powershell-workflow"></a>Eltérések a PowerShell-munkafolyamattól
A PowerShell-runbookok életciklusa, képességei és felügyelete megegyezik a PowerShell-alapú munkafolyamat-runbookokéval, de van néhány eltérés és korlátozás:

1. A PowerShell-forgatókönyvek a munkafolyamat-forgatókönyvekhez képest gyorsabban futnak, mert nem szerepel bennük a fordítási lépés.
2. A PowerShell-alapú munkafolyamat-forgatókönyvek támogatják az ellenőrzőpontokat, és az ellenőrzőpontok segítségével a PowerShell-alapú munkafolyamat-forgatókönyvek a forgatókönyv bármely pontjától folytathatók, ezzel szemben egy PowerShell-forgatókönyv csak az elejéről indítható újra.
3. A PowerShell-alapú munkafolyamat-forgatókönyvek támogatják a párhuzamos és soros végrehajtást is, míg a PowerShell-forgatókönyvek csak sorosan képesek végrehajtani a parancsokat.
4. Egy PowerShell-alapú munkafolyamat-runbookban egy tevékenység-, parancs- vagy parancsfájlblokk rendelkezhet saját futási térrel, míg a PowerShell-runbookokban minden parancsfájl egyetlen futási térben fut. Van néhány [szintaktikai eltérés](https://technet.microsoft.com/magazine/dn151046.aspx) is a natív PowerShell-forgatókönyvek és a PowerShell-alapú munkafolyamat-forgatókönyvek között.

## <a name="next-steps"></a>További lépések
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* További információk a PowerShell-parancsprogramok támogatásáról: [PowerShell-parancsprogramok natív támogatása az Azure Automationben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

