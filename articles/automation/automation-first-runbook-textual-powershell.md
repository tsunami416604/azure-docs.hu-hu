---
title: Az első PowerShell-runbook Azure Automation
description: Ez az oktatóanyag bemutatja egy egyszerű PowerShell-forgatókönyv létrehozását, tesztelését és közzétételét.
keywords: azure powershell, powershell-szkript oktatóanyag, powershell automation
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9718185b41795da6d95486972441ee20bc250316
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850699"
---
# <a name="my-first-powershell-runbook"></a>Az első PowerShell-forgatókönyvem

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Ez az oktatóanyag bemutatja, hogyan hozhat létre az Azure Automationben egy [PowerShell-forgatókönyvet](automation-runbook-types.md#powershell-runbooks). A runbook-feladatok állapotának nyomon követése során megtudhatja, hogy milyen egyszerű runbook tesztel és tesz közzé. Ezután módosítja a runbookot, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure-beli virtuális gépet. Végül pedig runbook paraméterek hozzáadásával hatékonyabbá teheti a runbook.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-quickstart-create-account.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. A gép leállítása és elindítása így nem lehet éles virtuális gép.
* Előfordulhat, hogy az Ön által használt parancsmagok alapján frissítenie kell [Az Azure](automation-update-azure-modules.md) -modulokat.

## <a name="create-new-runbook"></a>Új runbook létrehozása

Első lépésként hozzon létre egy egyszerű runbook, amely kiírja a *"Helló világ!" alkalmazás*szöveget.

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A runbookok listájának megnyitásához kattintson a **runbookok** elemre a **folyamat automatizálása** alatt.
3. Hozzon létre egy új runbook a **+ Runbook hozzáadása** gombra kattintva, majd **hozzon létre egy új runbook**.
4. Adja a forgatókönyvnek a *MyFirstRunbook-PowerShell* nevet.
5. Ebben az esetben hozzon létre egy [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) , és válassza a **PowerShell** lehetőséget a **runbook típushoz**.
6. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="add-code-to-the-runbook"></a>Kód hozzáadása a runbook

Beírhat közvetlenül a forgatókönyvbe kódot, vagy választhat parancsmagokat, forgatókönyveket és adategységeket a Könyvtár vezérlőből, majd hozzáadhatja őket a forgatókönyvhöz a kapcsolódó paraméterekkel együtt. Ebben az útmutatóban közvetlenül a runbook írja be a következőt:.

1. A runbook jelenleg üres, írja be a következőt: *Write-output ""Helló világ!"alkalmazás."* karaktersort a szkript törzsébe.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a> A runbook tesztelése

Mielőtt közzéteszi a runbookot, hogy éles üzemben is elérhető legyen, tesztelnie kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.
2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
3. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), és megjelenik annak állapota.

   A feladatok állapota várólistára kerül, ami azt jelzi, hogy a felhőben lévő runbook-feldolgozó elérhetővé válására vár. A művelet akkor *indul* el, amikor egy feldolgozó kiállítja a feladatot, majd *fut* , amikor a runbook ténylegesen elindul.

4. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Ebben az esetben *"Helló világ!" alkalmazás*kell látnia.

   ![Teszt panel kimenete](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="publish-and-start-the-runbook"></a>A runbook közzététele és elindítása

A létrehozott runbook még mindig Piszkozat módban van. Az éles környezetben való futtatás előtt közzé kell tenni.  Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nem rendelkezik közzétett verzióval, mert most létrehozta a runbook.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
1. Ha balra görgetve megtekinti a runbook a **runbookok** panelen, a **közzétett**szerzői **állapotot** jeleníti meg.
1. Görgessen vissza jobbra a **MyFirstRunbook-PowerShell** panel megtekintéséhez.
   A felül látható lehetőségekkel elindíthatjuk és megtekinthetjük a runbookot, ütemezhetjük egy későbbi időpontban való indításra, vagy létrehozhatunk egy [webhookot](automation-webhooks.md), amely segítségével elindítható a runbook egy HTTP-hívással.
1. El szeretné indítani a runbook, kattintson a **Start** gombra, majd kattintson az **OK** gombra a runbook indítása Lap megnyitásakor.
1. Megnyílik a létrehozott runbook-feladatokhoz tartozó feladatok lapja. Lezárhatja ezt a panelt, de ebben az esetben hagyja megnyitva, hogy megtekintse a feladatok állapotát.
1. A feladatok állapota megjelenik a **feladatok összegzése** területen, és megfelel a runbook tesztelésekor megtekintett állapotoknak.

   ![Feladat összegzése](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Miután a runbook állapota megjelenik, az **Áttekintés** területen kattintson a **kimenet**elemre. Megnyílik a kimenet panel, és megtekintheti a *"Helló világ!" alkalmazás*.

   ![Feladat kimenete](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. A kimeneti oldal bezárásához.
1. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. Csak *"Helló világ!" alkalmazás* jelenik meg a kimeneti adatfolyamban, de ez a kimenet más streameket is megjeleníthet egy runbook-feladatokhoz, például a részletes és a hibaüzenetet, ha a runbook ír rájuk.

   ![Az összes napló](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. A MyFirstRunbook-PowerShell lapra való visszatéréshez zárjuk be a Streams oldalt és a feladatokhoz tartozó lapot.
1. A **részletek**területen kattintson a **feladatok** elemre a runbook feladatok panelének megnyitásához. Ezen a lapon a runbook által létrehozott összes feladat szerepel. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtatta a feladatot.

   ![Feladatlista](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már látott a runbook elindításakor. Ez a művelet lehetővé teszi az idő visszalépését, és megtekintheti az adott runbook létrehozott feladatok részleteit.

## <a name="add-authentication-to-manage-azure-resources"></a>Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Ez azonban nem teszi lehetővé, hogy az Automation-fiók létrehozásakor automatikusan létrehozott futtató kapcsolatok használatával hitelesítse magát. A futtató kapcsolatot a **Csatlakoztatás-AzureRmAccount** parancsmaggal használhatja. Ha több előfizetésen keresztül felügyel erőforrásokat, a [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext)mellett a **-AzureRmContext** paramétert kell használnia.

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. A MyFirstRunbook-PowerShell oldalon a **Szerkesztés** gombra kattintva nyissa meg a szöveges szerkesztőt.
1. Már nincs szüksége a **Write-output** sorra, ezért ugorjon, és törölje azt.
1. Írja be, vagy másolja és illessze be a következő kódot, amely kezeli a hitelesítést az Automation futtató fiókjával:

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > A **Add-AzureRmAccount** és a **login-AzureRmAccount** a következő aliasokat használják a **csatlakozási-AzureRmAccount**. Ha a **AzureRMAccount** parancsmag nem létezik, használhatja a **Add-AzureRMAccount** vagy a **login-AzureRMAccount**, vagy frissítheti a modulokat az Automation-fiókban a legújabb verzióra.

1. Kattintson a **teszt panelre** , hogy tesztelni tudja a runbook.
1. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, a kimenetnek a fiókja alapvető adatait kell megjelenítenie, a következőhöz hasonló módon. Ez a kimenet megerősíti, hogy a futtató fiók érvényes.

   ![Hitelesítés](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Kód hozzáadása a virtuális gép indításához

Most, hogy a runbook hitelesítést végez az Azure-előfizetésében, kezelheti az erőforrásokat. Adjon hozzá egy parancsot a virtuális gép elindításához. Bármelyik virtuális gépet kiválaszthatja az Azure-előfizetésében, és most már parancsmagba ezt a nevet a runbook.

1. A *Kapcsolódás*után a AzureRmAccount írja be a *Start-AzureRmVM-Name "VMName"-ResourceGroupName "NameofResourceGroup"* parancsot, amely megadja a virtuális gép nevét és erőforráscsoport-nevét.

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Mentse a runbook, majd kattintson a **teszt panelre** , hogy tesztelni tudja.
1. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="add-an-input-parameter"></a>Bemeneti paraméter hozzáadása

A runbook jelenleg a runbook hardcoded elindítja a virtuális gépet, de hasznos lehet, ha a runbook indításakor megadta a virtuális gépet. A runbook bemeneti paramétereket adhat hozzá a funkció megadásához.

1. Adja hozzá a *VMName* és a *ResourceGroupName* paramétert a runbook, és használja ezeket a változókat a **Start-AzureRmVM** parancsmaggal az alábbi példában látható módon.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a teszt során használt két bemeneti változóhoz.
1. Zárja be a Teszt panelt.
1. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.
1. Állítsa le az előző lépésben elindított virtuális gépet.
1. A runbook elindításához kattintson **az OK** gombra. Írja be a **VMName** és a **ResourceGroupName** értéket az elindítani kívánt virtuális géphez.<br><br> ![Paraméter átadása](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Ha a forgatókönyv kész, ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="differences-from-powershell-workflow"></a>Eltérések a PowerShell-munkafolyamattól

A PowerShell-runbookok életciklusa, képességei és felügyelete megegyezik a PowerShell-alapú munkafolyamat-runbookokéval, de van néhány eltérés és korlátozás:

1. A PowerShell-forgatókönyvek a munkafolyamat-forgatókönyvekhez képest gyorsabban futnak, mert nem szerepel bennük a fordítási lépés.
2. A PowerShell-munkafolyamatok runbookok támogatja az ellenőrzőpontokat, az ellenőrzőpontok használatával a PowerShell-munkafolyamatok runbookok a runbook bármely pontjáról folytatódhat. A PowerShell-runbookok csak az elejétől folytatható.
3. A PowerShell-munkafolyamat runbookok támogatja a párhuzamos és a soros végrehajtást. A PowerShell-runbookok csak soros parancsokat futtathatnak.
4. A PowerShell-munkafolyamatok runbook egy tevékenység, egy parancs vagy egy parancsfájl-blokk saját RunSpace rendelkezhet. Egy PowerShell-runbook a parancsfájlok minden funkciója egyetlen RunSpace fut. Van néhány [szintaktikai eltérés](https://technet.microsoft.com/magazine/dn151046.aspx) is a natív PowerShell-forgatókönyvek és a PowerShell-alapú munkafolyamat-forgatókönyvek között.

## <a name="next-steps"></a>További lépések

* A PowerShell-lel kapcsolatos további információkért, beleértve a nyelvi referenciákat és a tanulási modulokat, tekintse át a [PowerShell-dokumentumokat](/powershell/scripting/overview).
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* További információk a PowerShell-parancsprogramok támogatásáról: [PowerShell-parancsprogramok natív támogatása az Azure Automationben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
