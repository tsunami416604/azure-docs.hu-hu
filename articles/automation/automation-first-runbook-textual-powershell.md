---
title: Az Azure Automationben az első PowerShell-forgatókönyvem
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
ms.openlocfilehash: 1680a7d9fae9f8b8c04383c9f199e4c2ec8b8dfe
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275231"
---
# <a name="my-first-powershell-runbook"></a>Az első PowerShell-forgatókönyvem

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Ez az oktatóanyag bemutatja, hogyan hozhat létre az Azure Automationben egy [PowerShell-forgatókönyvet](automation-runbook-types.md#powershell-runbooks). Először egy egyszerű runbookot, amely akkor tesztelünk és közzé ismerteti a runbook-feladat állapotának nyomon követése. Ezután módosítja a runbookot, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure-beli virtuális gépet. Végül akkor a runbook még robusztusabbá runbook-paramétereket adunk hozzá.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Ezt a gépet leállítja és elindítja, tehát ne olyan virtuális gépet használjon, amely élesben működik.

## <a name="step-1---create-new-runbook"></a>1. lépés – Új forgatókönyv létrehozása
Először hozzon létre egy egyszerű runbookot, amely szöveget adja vissza *Hello World*.

1. Az Azure Portalon nyissa meg az Automation-fiókját.

   Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.

1. Kattintson a **Runbookok** alatt **Folyamatautomatizálás** forgatókönyvek listájának megnyitásához.
2. Hozzon létre egy új runbookot kattintva a **+ forgatókönyv hozzáadása** gombra, majd **hozzon létre egy új runbookot**.
3. Adja a forgatókönyvnek a *MyFirstRunbook-PowerShell* nevet.
4. Ebben az esetben fog létrehozni egy [PowerShell-forgatókönyv](automation-runbook-types.md#powershell-runbooks) ezért válassza **Powershell** a **Runbook típusa**.
5. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-code-to-the-runbook"></a>2. lépés – Kód hozzáadása a forgatókönyvhöz
Beírhat közvetlenül a forgatókönyvbe kódot, vagy választhat parancsmagokat, forgatókönyveket és adategységeket a Könyvtár vezérlőből, majd hozzáadhatja őket a forgatókönyvhöz a kapcsolódó paraméterekkel együtt. Ebben a bemutatóban írja a közvetlenül a runbookot.

1. A runbook jelenleg üres, akkor írja be *Write-Output "Hello World."* karaktersort a szkript törzsébe.<br><br> ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a>3. lépés – A forgatókönyv tesztelése
Mielőtt közzéteszi a runbookot, hogy éles üzemben is elérhető legyen, tesztelnie kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.
2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
3. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), és megjelenik annak állapota.

   A feladat állapota kezdetben *Várólistán*. Ez azt jelöli, hogy egy felhőben lévő runbook-feldolgozó elérhetővé válására vár. Elérésűből *kezdő* Ha egy feldolgozó elvállalja a feladatot, majd *futó* amikor a runbook elkezd futni.  

1. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Megjelenik az Ön esetében *Hello World*.<br><br> ![Teszt panel kimenete](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
2. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4. lépés: Közzététel és a forgatókönyv indítása
A létrehozott runbook még mindig Piszkozat módban van. Üzemi környezetben való futtatás előtt közzé kell.  Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Az Ön esetében még nem rendelkezik közzétett verzió, mert az újonnan létrehozott, a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
2. Ha balra görgetve megtekinti a forgatókönyvet a a **Runbookok** panelen most megjelenít egy **szerzői állapot** , **közzétett**.
3. Görgessen vissza jobbra a **MyFirstRunbook-PowerShell** panel megtekintéséhez.  
   A felül látható lehetőségekkel elindíthatjuk és megtekinthetjük a runbookot, ütemezhetjük egy későbbi időpontban való indításra, vagy létrehozhatunk egy [webhookot](automation-webhooks.md), amely segítségével elindítható a runbook egy HTTP-hívással.
4. El szeretné indítani a runbookot, tehát kattintson **Start** majd **Ok** a Runbook indítása lap megnyitásakor.
5. A feladat oldalát a létrehozott runbook-feladat számára van nyitva. Zárja be ezt a panelt, de ebben az esetben, nyitva hagyja, így megtekintheti a feladat előrehaladását.
6. A feladat állapota a **feladat összegzése** és megegyezik-e az állapot, a tesztelt, amikor a runbook vonatkozott.<br><br> ![Feladat összegzése](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. Egyszer a runbook állapota látható *befejezve*alatt **áttekintése** kattintson **kimeneti**. A Kimenet panel megnyílik, és láthatja a *Hello World*.<br><br> ![Job Output](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png) (Feladat kimenete)<br> 
8. A kimeneti oldal bezárásához.
9. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. A kimeneti streamben csak a *Hello World* eredményt látja, de itt megjelenhetnek egyéb streamek is egy runbook-feladatból, mint például a Részletes vagy a Hiba, ha a forgatókönyv ezekbe ír.<br><br> ![Minden napló](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. Zárja be a Streamek lapon és a feladat oldalát a MyFirstRunbook-PowerShell lapra való visszatéréshez.
11. A **részletek**, kattintson a **feladatok** Ez a forgatókönyv feladatok paneljének megnyitásához. Ez felsorolja az összes, a forgatókönyv által létrehozott feladatot. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtatta a feladatot.<br><br> ![Feladatlista](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már látott a runbook elindításakor. Ez lehetővé teszi, hogy az időben visszamenve megtekintse egy adott forgatókönyvhöz létrehozott összes feladat részleteit.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez
Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Nem sikerül, kivéve, ha végez hitelesítést az között említett hitelesítő adatok használatával, ha ehhez a [Előfeltételek](#prerequisites). megteheti, hogy a a **Connect-AzureRmAccount** parancsmagot. Ha több előfizetésre kiterjedő felügyel erőforrásokat kell használnia a **- AzureRmContext** mentén paraméterrel [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. A szöveges szerkesztő megnyitásához **szerkesztése** a MyFirstRunbook-PowerShell oldalon.
2. nem kell a **Write-Output** sorra már nincs szükségünk, ezért lépjen tovább, és törölje azt.
3. Írja be, vagy másolja és illessze be a következő kódot, amely kezeli a hitelesítést az Automation futtató fiókjával:

   ```powershell
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** és **Login-AzureRmAccount** aliasai most **Connect-AzureRMAccount**. Ha a **Connect-AzureRMAccount** parancsmag nem létezik, akkor használhatja **Add-AzureRmAccount** vagy **Login-AzureRmAccount**, vagy frissítheti az a modulokat az Automation A fiók a legújabb verzióra.

4. Kattintson a **teszt panel** úgy, hogy a runbook teszteléséhez.
5. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, a kimenetnek a fiókja alapvető adatait kell megjelenítenie, a következőhöz hasonló módon. Ez ellenőrzi, hogy érvényes-e a hitelesítő adat.<br><br> ![Hitelesítés](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6. lépés: Kód hozzáadása a virtuális gép indításához
Most, hogy a runbook hitelesíti az Azure-előfizetéshez, erőforrások is kezelhetők. hozzáadhat egy parancs egy virtuális gép elindításához. Választhat bármelyik virtuális gépet az Azure-előfizetésében, valamint a most parancsmagba, amely nevet a runbookban.

1. Miután *Connect-AzureRmAccount*, típus *Start-AzureRmVM-Name 'VMName' - ResourceGroupName 'NameofResourceGroup'* biztosítása és az erőforráscsoport nevét a virtuális gép elindításához.  
   
   ```powershell
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Mentse a forgatókönyvet, és kattintson a **teszt panel** így tesztelheti.
3. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7. lépés – Bemeneti paraméter hozzáadása a forgatókönyvhöz
A runbook jelenleg elindítja a virtuális gépet, hogy Ön szoftveresen kötött a runbook, de azt hasznosabb lenne, ha a virtuális gép adja meg a forgatókönyv elindulásakor. A bemeneti paramétereket adhat hozzá a runbook funkció.

1. Paraméterek hozzáadása *VMName* és *ResourceGroupName* a runbookhoz, és használja ezeket a változókat a **Start-AzureRmVM** parancsmag az alábbi példában látható módon.

   ```powershell
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

