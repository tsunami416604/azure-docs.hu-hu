---
title: Az első PowerShell-munkafolyamat runbook a Azure Automation
description: Ez az oktatóanyag bemutatja egy egyszerű szöveges forgatókönyv PowerShell-munkafolyamattal való létrehozását, tesztelését és közzétételét.
keywords: powershell-munkafolyamat, powershell-munkafolyamat példák, munkafolyamat powershell
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 09/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ab6d213e83c2d7eba95c6c9a6dca5edc1f0f2215
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996524"
---
# <a name="my-first-powershell-workflow-runbook"></a>Az első PowerShell-alapú munkafolyamat-forgatókönyvem

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Egy Azure Automation [PowerShell-alapú munkafolyamat-forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks) létrehozását bemutató oktatóanyag. A runbook-feladatok állapotának nyomon követéséhez egy egyszerű runbook kell kezdenie, amelyet tesztelni és közzétennie kell. Ezután módosítja a runbookot, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure-beli virtuális gépet. Végül a runbook paraméterek hozzáadásával hatékonyabbá teheti a runbook.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez.  Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. a gép leállítása és elindítása így nem lehet éles virtuális gép.

## <a name="step-1---create-new-runbook"></a>1\. lépés – Új forgatókönyv létrehozása

Első lépésként hozzon létre egy egyszerű runbook, amely kiírja a *"Helló világ!" alkalmazás*szöveget.

1. Az Azure Portalon nyissa meg az Automation-fiókját.

   Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.

1. A runbookok listájának megnyitásához kattintson a **runbookok** elemre a **folyamat automatizálása** alatt.
1. Hozzon létre egy új runbook a **+ Runbook hozzáadása** gombra kattintva, majd **hozzon létre egy új runbook**.
1. Adja a forgatókönyvnek a *MyFirstRunbook-Workflow* nevet.
1. Ebben az esetben létre kell hoznia egy PowerShell- [munkafolyamati runbook](automation-runbook-types.md#powershell-workflow-runbooks) , ezért válassza a **PowerShell-munkafolyamat** lehetőséget a **runbook típushoz**.
1. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-code-to-the-runbook"></a>2\. lépés – Kód hozzáadása a forgatókönyvhöz

Beírhat közvetlenül a forgatókönyvbe kódot, vagy választhat parancsmagokat, forgatókönyveket és adategységeket a Könyvtár vezérlőből, majd hozzáadhatja őket a forgatókönyvhöz a kapcsolódó paraméterekkel együtt. Ebben az útmutatóban közvetlenül a runbook írja be a következőt:.

1. A runbook jelenleg csak a szükséges *munkafolyamat* -kulcsszóval, a runbook nevével és a teljes munkafolyamatot tartalmazó kapcsos zárójelekkel van ellátva.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Írja be a következőt: *Write-Output "Hello World."* a zárójelek közé.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a>3\. lépés – A forgatókönyv tesztelése

Mielőtt közzéteszi a runbookot, hogy éles üzemben is elérhető legyen, tesztelnie kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.
1. Kattintson az **Indítás** gombra a teszt elindításához. Ez a beállítás csak az egyetlen engedélyezett beállítás lehet.
1. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), és megjelenik annak állapota.

   A feladatok állapota a *várólistára* kerül, amely azt jelzi, hogy a felhőben lévő runbook-feldolgozó elérhetővé válására vár. A művelet akkor *indul* el, amikor egy feldolgozó kiállítja a feladatot, majd *fut* , amikor a runbook ténylegesen elindul.

1. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Ebben az esetben *"Helló világ!" alkalmazás*kell látnia.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4\. lépés: Közzététel és a forgatókönyv indítása

A létrehozott runbook még mindig Piszkozat módban van. Az éles környezetben való futtatás előtt közzé kell tennie. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nem rendelkezik közzétett verzióval, mert most létrehozta a runbook.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
1. Ha balra görgetve megtekinti a runbook a **runbookok** panelen, a **közzétett** **szerzői állapotot** jeleníti meg.
1. Görgessen vissza jobbra a **MyFirstRunbook-Workflow** panel megtekintéséhez.
   A felül látható lehetőségekkel elindíthatjuk a forgatókönyvet, ütemezhetjük egy későbbi időpontban való indításra, vagy létrehozhatunk egy [webhookot](automation-webhooks.md), amely segítségével elindítható a forgatókönyv egy HTTP-hívással.
1. csak szeretné elindítani a runbook, ezért kattintson a **Start** gombra, majd az **Igen** gombra, amikor a rendszer kéri.

   ![Forgatókönyv indítása](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Megnyílik a létrehozott runbook-feladatokhoz tartozó feladatok ablaktábla. lezárhatja ezt a panelt, de ebben az esetben hagyja megnyitva, hogy megtekintse a feladatok állapotát.
1. A feladatok állapota megjelenik a **feladatok összegzése** területen, és megfelel a runbook tesztelésekor megtekintett állapotoknak.

   ![Feladat összegzése](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Ha a forgatókönyv a *Befejezve* állapotot mutatja, kattintson a **Kimenet** lehetőségre. Megnyílik a kimenet panel, és megtekintheti a *"Helló világ!" alkalmazás*.

   ![Feladat összegzése](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Zárja be a Kimenet panelt.
1. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. csak *"Helló világ!" alkalmazás* jelenik meg a kimeneti adatfolyamban, de ez a nézet más streameket is megjeleníthet egy runbook-feladatokhoz, például a részletes és a hibaüzenetet, ha a runbook ír rájuk.

   ![Feladat összegzése](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. A MyFirstRunbook lapra való visszatéréshez zárjuk be a Streams oldalt és a feladatok lapot.
1. Kattintson a **feladatok** lehetőségre a runbook feladatok lapjának megnyitásához. Ezen a lapon a runbook által létrehozott összes feladat szerepel. csak egyszer kell látnia egy feladatot, mert csak egyszer futtatta a feladatot.

   ![Feladatok](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. A feladatokra kattintva megnyithatja ugyanazt a feladatot, amelyet a runbook indításakor megtekintett. Ez a művelet lehetővé teszi az idő visszalépését, és megtekintheti az adott runbook létrehozott feladatok részleteit.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5\. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Ez azonban csak akkor hajtható végre, ha az [előfeltételekben](#prerequisites)hivatkozott hitelesítő adatok használatával hitelesíti magát. Ezt a **kapcsolatot a AzAccount** parancsmaggal teheti meg.

1. Kattintson a MyFirstRunbook-Workflow panel **Szerkesztés** gombjára a szöveges szerkesztő megnyitásához.
2. már nincs szüksége a **Write-output** sorra, ezért ugorjon, és törölje azt.
3. Vigye a kurzort egy üres sorra a zárójelek között.
4. Írja be, vagy másolja és illessze be a következő kódot, amely kezeli a hitelesítést az Automation futtató fiókjával.

   ```powershell-interactive
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

   > [!IMPORTANT]
   > A **Add-AzAccount** és a **login-AzAccount** a következő aliasokat használják a **csatlakozási-AzAccount**. Ha a **AzAccount** parancsmag nem létezik, használhatja a **Add-AzAccount** vagy a **login-AzAccount**, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban a legújabb verzióra.

> [!NOTE]
> Előfordulhat, hogy [frissítenie kell a modulokat](automation-update-azure-modules.md) annak ellenére, hogy nemrég létrehozott egy új Automation-fiókot.

1. Kattintson a **teszt panelre** , hogy tesztelni tudja a runbook.
1. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, a kimenetnek a fiókja alapvető adatait kell megjelenítenie, a következőhöz hasonló módon. Ez a művelet megerősíti, hogy a hitelesítő adat érvényes.

   ![Hitelesítés](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6\. lépés: Kód hozzáadása a virtuális gép indításához

Most, hogy a runbook hitelesítést végez az Azure-előfizetésében, kezelheti az erőforrásokat. Adjon hozzá egy parancsot a virtuális gép elindításához. Bármelyik virtuális gépet kiválaszthatja az Azure-előfizetésében, és most már rögzítjük ezt a nevet a runbook. Ha több előfizetésen keresztül kezeli az erőforrásokat, akkor a [Get-AzContext](/powershell/module/az.accounts/get-azcontext)mellett a **-AzContext** paramétert kell használnia.

1. A *Kapcsolódás*után a AzAccount írja be a *Start-AzVM-Name "VMName"-ResourceGroupName "NameofResourceGroup"* parancsot, amely megadja a virtuális gép nevét és erőforráscsoport-nevét.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Mentse a runbook, majd kattintson a **teszt panelre** , hogy tesztelni tudja.
1. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7\. lépés – Bemeneti paraméter hozzáadása a forgatókönyvhöz

a runbook jelenleg a runbook hardcoded elindítja a virtuális gépet, de hasznos lehet, ha a runbook indításakor megadhatja a virtuális gépet. A runbook bemeneti paramétereket adhat hozzá a funkció megadásához.

1. Adja hozzá a *VMName* és a *ResourceGroupName* paramétert a runbook, és használja ezeket a változókat a **Start-AzVM** parancsmaggal az alábbi példában látható módon.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a tesztben szereplő két bemeneti változóhoz.
3. Zárja be a Teszt panelt.
4. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.
5. Állítsa le az előző lépésben elindított virtuális gépet.
6. Kattintson az **Indítás** gombra a forgatókönyv elindításához. Írja be a **VMName** és a **ResourceGroupName** értéket az elindítani kívánt virtuális géphez.

   ![Forgatókönyv indítása](media/automation-first-runbook-textual/automation-pass-params.png)

7. Ha a forgatókönyv kész, ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="next-steps"></a>További lépések

* A PowerShell-lel kapcsolatos további információkért, beleértve a nyelvi referenciákat és a tanulási modulokat, tekintse át a [PowerShell-dokumentumokat](https://docs.microsoft.com/en-us/powershell/scripting/overview).
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-forgatókönyvem](automation-first-runbook-textual-powershell.md).
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* További információk a PowerShell-parancsprogramok támogatásáról: [PowerShell-parancsprogramok natív támogatása az Azure Automationben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
