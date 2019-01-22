---
title: Az Azure Automationben az első PowerShell-forgatókönyvem
description: Ez az oktatóanyag bemutatja egy egyszerű PowerShell-forgatókönyv létrehozását, tesztelését és közzétételét.
keywords: azure powershell, powershell-szkript oktatóanyag, powershell automation
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f083a5a9fe8027467eb95711a15725859f59e4fa
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438048"
---
# <a name="my-first-powershell-runbook"></a>Az első PowerShell-forgatókönyvem

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Ez az oktatóanyag bemutatja, hogyan hozhat létre az Azure Automationben egy [PowerShell-forgatókönyvet](automation-runbook-types.md#powershell-runbooks). Először egy egyszerű runbookot, amely akkor tesztelünk és közzé ismerteti a runbook-feladat állapotának nyomon követése. Ezután módosítja a runbookot, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure-beli virtuális gépet. Végül akkor a runbook még robusztusabbá runbook-paramétereket adunk hozzá.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-quickstart-create-account.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Állítsa le és indítsa el ezt a gépet, hogy azt nem lehet egy éles környezetben lévő virtuális gép.

## <a name="create-new-runbook"></a>Új runbook létrehozása

Először hozzon létre egy egyszerű runbookot, amely szöveget adja vissza *Hello World*.

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Kattintson a **Runbookok** alatt **Folyamatautomatizálás** forgatókönyvek listájának megnyitásához.
3. Hozzon létre egy új runbookot kattintva a **+ forgatókönyv hozzáadása** gombra, majd **hozzon létre egy új runbookot**.
4. Adja a forgatókönyvnek a *MyFirstRunbook-PowerShell* nevet.
5. Ebben az esetben fog létrehozni egy [PowerShell-forgatókönyv](automation-runbook-types.md#powershell-runbooks) ezért válassza **Powershell** a **Runbook típusa**.
6. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="add-code-to-the-runbook"></a>Adja hozzá a kódot a runbookba

Beírhat közvetlenül a forgatókönyvbe kódot, vagy választhat parancsmagokat, forgatókönyveket és adategységeket a Könyvtár vezérlőből, majd hozzáadhatja őket a forgatókönyvhöz a kapcsolódó paraméterekkel együtt. Ebben a bemutatóban írja a közvetlenül a runbookot.

1. A runbook jelenleg üres, akkor írja be *Write-Output "Hello World."* karaktersort a szkript törzsébe.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  

2. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

##<a name="a-namestep-3---test-the-runbook-test-the-runbook"></a><a name="step-3---test-the-runbook"> A runbook tesztelése

Mielőtt közzéteszi a runbookot, hogy éles üzemben is elérhető legyen, tesztelnie kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.
2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
3. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), és megjelenik annak állapota.

   A feladat állapota kezdetben *várólistán* jelzi, hogy elérhetővé a felhőben lévő forgatókönyv-feldolgozó vár. Elérésűből *kezdő* Ha egy feldolgozó elvállalja a feladatot, majd *futó* amikor a runbook elkezd futni.  

4. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Megjelenik az Ön esetében *Hello World*.

   ![Teszt panel kimenete](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  

5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="publish-and-start-the-runbook"></a>Közzététel és a forgatókönyv indítása

A létrehozott runbook még mindig Piszkozat módban van. Éles környezetben való futtatása előtt kell közzétenni.  Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Az Ön esetében még nem rendelkezik közzétett verzió, mert az újonnan létrehozott, a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
1. Ha balra görgetve megtekinti a forgatókönyvet a a **Runbookok** panelen most megjelenít egy **szerzői állapot** , **közzétett**.
1. Görgessen vissza jobbra a **MyFirstRunbook-PowerShell** panel megtekintéséhez.  
   A felül látható lehetőségekkel elindíthatjuk és megtekinthetjük a runbookot, ütemezhetjük egy későbbi időpontban való indításra, vagy létrehozhatunk egy [webhookot](automation-webhooks.md), amely segítségével elindítható a runbook egy HTTP-hívással.
1. El szeretné indítani a runbookot, tehát kattintson **Start** majd **Ok** a Runbook indítása lap megnyitásakor.
1. A feladat oldalát a létrehozott runbook-feladat számára van nyitva. Zárja be ezt a panelt, de ebben az esetben, nyitva hagyja, így megtekintheti a feladat előrehaladását.
1. A feladat állapota a **feladat összegzése** és megegyezik-e az állapot, a tesztelt, amikor a runbook vonatkozott.

   ![Feladat összegzése](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Egyszer a runbook állapota látható *befejezve*alatt **áttekintése** kattintson **kimeneti**. A Kimenet panel megnyílik, és láthatja a *Hello World*.

   ![Feladat kimenete](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. A kimeneti oldal bezárásához.
1. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. Csak akkor jelenik meg *Hello World* a kimeneti stream, de ez a kimenet megjeleníthet egyéb streameket is egy forgatókönyv feladatból, mint például a részletes vagy a hiba Ha a forgatókönyv ezekbe ír.

   ![Minden napló](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Zárja be a Streamek lapon és a feladat oldalát a MyFirstRunbook-PowerShell lapra való visszatéréshez.
1. A **részletek**, kattintson a **feladatok** Ez a forgatókönyv feladatok paneljének megnyitásához. Ez a lap felsorolja az összes, a runbook által létrehozott feladatot. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtatta a feladatot.

   ![Feladatlista](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  

1. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már látott a runbook elindításakor. Ez a művelet lehetővé teszi, hogy az időben, és a egy adott forgatókönyvhöz létrehozott összes feladat részleteinek megtekintéséhez.

## <a name="add-authentication-to-manage-azure-resources"></a>Hitelesítés hozzáadása Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Ez nem sikerül, ehhez azonban, ha nincsenek végez hitelesítést egy futtató kapcsolatot, amely automatikusan létrejön, amikor az automation-fiók használatával. A futtató kapcsolatot használ a **Connect-AzureRmAccount** parancsmagot. Ha több előfizetéshez erőforrásokat felügyel, szeretné-e használni a **- AzureRmContext** mentén paraméterrel [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. A szöveges szerkesztő megnyitásához **szerkesztése** a MyFirstRunbook-PowerShell oldalon.
1. nem kell a **Write-Output** sorra már nincs szükségünk, ezért lépjen tovább, és törölje azt.
1. Írja be, vagy másolja és illessze be a következő kódot, amely kezeli a hitelesítést az Automation futtató fiókjával:

   ```powershell
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** és **Login-AzureRmAccount** aliasai most **Connect-AzureRMAccount**. Ha a **Connect-AzureRMAccount** parancsmag nem létezik, akkor használhatja **Add-AzureRmAccount** vagy **Login-AzureRmAccount**, vagy frissítheti az a modulokat az Automation A fiók a legújabb verzióra.

1. Kattintson a **teszt panel** úgy, hogy a runbook teszteléséhez.
1. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, a kimenetnek a fiókja alapvető adatait kell megjelenítenie, a következőhöz hasonló módon. Ez a kimenet megerősíti, hogy a futtató fiók érvényességét.

   ![Hitelesítés](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Adja hozzá a kódot egy virtuális gép elindítása

Most, hogy a runbook hitelesíti az Azure-előfizetéshez, erőforrások is kezelhetők. hozzáadhat egy parancs egy virtuális gép elindításához. Választhat bármelyik virtuális gépet az Azure-előfizetésében, valamint a most parancsmagba, amely nevet a runbookban.

1. Miután *Connect-AzureRmAccount*, típus *Start-AzureRmVM-Name 'VMName' - ResourceGroupName 'NameofResourceGroup'* biztosítása és az erőforráscsoport nevét a virtuális gép elindításához.  

   ```powershell
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Mentse a forgatókönyvet, és kattintson a **teszt panel** így tesztelheti.
1. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="add-an-input-parameter"></a>Bemeneti paraméter hozzáadása

A runbook jelenleg elindítja a virtuális gépet, hogy Ön szoftveresen kötött a runbook, de azt hasznosabb lenne, ha a virtuális gép adja meg a forgatókönyv elindulásakor. A bemeneti paramétereket adhat hozzá a runbook funkció.

1. Paraméterek hozzáadása *VMName* és *ResourceGroupName* a runbookhoz, és használja ezeket a változókat a **Start-AzureRmVM** parancsmag az alábbi példában látható módon.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a teszt során használt két bemeneti változóhoz.
1. Zárja be a Teszt panelt.
1. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.
1. Állítsa le az előző lépésben elindított virtuális gépet.
1. Kattintson a **OK** a runbook elindításához. Írja be a **VMName** és a **ResourceGroupName** értéket az elindítani kívánt virtuális géphez.<br><br> ![Paraméter átadása](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
1. Ha a forgatókönyv kész, ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="differences-from-powershell-workflow"></a>Eltérések a PowerShell-munkafolyamattól

A PowerShell-runbookok életciklusa, képességei és felügyelete megegyezik a PowerShell-alapú munkafolyamat-runbookokéval, de van néhány eltérés és korlátozás:

1. A PowerShell-forgatókönyvek a munkafolyamat-forgatókönyvekhez képest gyorsabban futnak, mert nem szerepel bennük a fordítási lépés.
2. PowerShell-munkafolyamati runbookok támogatják az ellenőrzőpontokat, ellenőrzőpontok használata, a PowerShell-munkafolyamati runbookok is bármely pontjától a runbookban. PowerShell-forgatókönyvek a is csak az elejéről indítható újra.
3. PowerShell-munkafolyamati runbookok támogatja a párhuzamos és soros végrehajtást. Sorosan PowerShell-forgatókönyvek csak hajthat végre parancsokat.
4. Egy PowerShell-munkafolyamati forgatókönyv, tevékenységek, egy parancs vagy parancsfájl parancsfájlblokk rendelkezhet saját futási térrel. A PowerShell-runbookokban minden parancsfájl futási térben egyetlen. Van néhány [szintaktikai eltérés](https://technet.microsoft.com/magazine/dn151046.aspx) is a natív PowerShell-forgatókönyvek és a PowerShell-alapú munkafolyamat-forgatókönyvek között.

## <a name="next-steps"></a>További lépések

* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* További információk a PowerShell-parancsprogramok támogatásáról: [PowerShell-parancsprogramok natív támogatása az Azure Automationben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
