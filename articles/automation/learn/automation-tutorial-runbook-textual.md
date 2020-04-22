---
title: PowerShell-munkafolyamat-runbook létrehozása az Azure Automationben
description: Oktatóanyag, amely bemutatja, hogyan hozhat létre, tesztel, és közzé tehet egy egyszerű PowerShell-munkafolyamat-runbook.
keywords: powershell-munkafolyamat, powershell-munkafolyamat példák, munkafolyamat powershell
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: cdf43bb82baf28ba21e00d0f58dc8bafe84fbe42
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726332"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>Oktatóanyag: PowerShell-munkafolyamat-runbook létrehozása

Egy Azure Automation [PowerShell-alapú munkafolyamat-forgatókönyv](../automation-runbook-types.md#powershell-workflow-runbooks) létrehozását bemutató oktatóanyag. A PowerShell-munkafolyamat runbookok a Windows PowerShell-munkafolyamaton alapuló tankönyvek. A runbook kódját az Azure Portalon található szövegszerkesztővel hozhatja létre és szerkesztheti. 

> [!div class="checklist"]
> * Egyszerű PowerShell-munkafolyamat-runbook létrehozása
> * A runbook tesztelése és közzététele
> * A runbook-feladat állapotának futtatása és nyomon követése
> * Frissítse a runbookot egy Azure-beli virtuális gép runbook-paraméterekkel való indításához

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](../automation-update-azure-modules.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](../automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Mivel megáll, és indítsa el ezt a gépet, nem kell egy éles virtuális gép.

## <a name="step-1---create-new-runbook"></a>1. lépés – Új forgatókönyv létrehozása

Első lépésként hozzon létre egy `Hello World`egyszerű runbookot, amely a szöveget adja ki.

1. Az Azure Portalon nyissa meg az Automation-fiókját.

   Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nek az eszközöknek a többsége az új Automation-fiókban automatikusan szereplő modulok. Az előfizetéshez társítva kell lennie a Hitelesítő adatokkal.
 
2. Válassza **a Runbookok** **a Folyamatautomatizálás** csoportban a runbookok listájának megnyitásához.

3. Hozzon létre egy új runbookot **a Runbook létrehozása**lehetőség kiválasztásával.

4. Adja a forgatókönyvnek a **MyFirstRunbook-Workflow** nevet.

5. Ebben az esetben egy [PowerShell-munkafolyamat-runbookot](../automation-runbook-types.md#powershell-workflow-runbooks)fog létrehozni. Válassza ki a **PowerShell-munkafolyamatot** a **Runbook-típushoz.**

6. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-code-to-the-runbook"></a>2. lépés – Kód hozzáadása a forgatókönyvhöz

Írhat be kódot közvetlenül a runbookba, vagy kijelölhet parancsmagokat, runbookokat és eszközöket a Könyvtár vezérlőből, és hozzáadhatja őket a runbookhoz bármely kapcsolódó paraméterrel. Ebben az oktatóanyagban közvetlenül a runbookba írja be a kódot.

1. A runbook jelenleg üres, `Workflow` csak a szükséges kulcsszót, a runbook nevét, és a kapcsos zárójelek, amelyek betegyétek a teljes munkafolyamatot.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. Írja `Write-Output "Hello World"` be a fogszabályzók között.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a>3. lépés – A forgatókönyv tesztelése

Mielőtt közzétenné a runbookot, hogy elérhetővé tegye éles környezetben, tesztelje, hogy megfelelően működik-e. A runbook tesztelése futtatja a Piszkot verziója, és lehetővé teszi, hogy interaktívan tekintse meg a kimenetet.

1. Válassza **a Teszt ablaktábla** lehetőséget a Teszt ablaktábla megnyitásához.

2. Kattintson a **Start** gombra a teszt elindításához, az egyetlen engedélyezett beállítás tesztelésével.

3. Vegye figyelembe, hogy létrejön egy [runbook-feladat,](../automation-runbook-execution.md) és állapota megjelenik az ablaktáblán.

   A feladat állapota várólistára kerülése, jelezve, hogy a feladat arra vár, hogy a felhőben lévő runbook-dolgozó elérhetővé váljon. Az állapot akkor változik, amikor egy dolgozó igényt tart a feladatra. Végül az állapot fut, amikor a runbook ténylegesen elindul.

4. Amikor a runbook-feladat befejeződik, a Teszt ablaktábla megjeleníti a kimenetét. Ebben az esetben, `Hello World`látod .

   ![Hello World](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4. lépés: Közzététel és a forgatókönyv indítása

A létrehozott runbook továbbra is Vázlat módban van. A termelésben való futtatás előtt közzé kell tennie. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nincs Közzétett verzió, mivel még csak most hozta létre a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.

2. Görgessen balra a **Runbook oklapon** lévő runbook megtekintéséhez, és vegye figyelembe, hogy a **Szerzői állapot** mező **Közzétéve**.

3. Görgessen vissza jobbra a **MyFirstRunbook-Workflow**lap megtekintéséhez.

   A beállítások a felső lehetővé teszi, hogy indítsa el a runbook most, ütemezése egy jövőbeli kezdési időpont, vagy hozzon létre egy [webhook,](../automation-webhooks.md) hogy a runbook lehet indítani egy HTTP-hívás.

4. Válassza **a Start,** majd **az Igen** lehetőséget, amikor a runbook indítására kéri.

   ![Forgatókönyv indítása](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. Meg van nyitva egy feladat ablaktábla a létrehozott runbook-feladathoz. Ebben az esetben hagyja nyitva az ablaktáblát, hogy megfigyelhesse a feladat előrehaladását.

6. Ne feledje, hogy a feladat állapota megjelenik a **Feladat összegzése területen.** Ez az állapot megegyezik a runbook tesztelése során látott állapotokkal.

   ![Feladat összegzése](../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png)

7. Miután a runbook állapota látható, kattintson **a Kimenet**gombra. Megnyílik a Kimenet lap, ahol `Hello World` megjelenik az üzenet.

   ![Feladat összegzése](../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png)

8. Zárja be a Kimenet lapot.

9. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. Csak a `Hello World` kimeneti adatfolyamban jelenik meg. Vegye figyelembe, hogy a Streams ablaktábla más streameket is megjeleníthet egy runbook-feladathoz, például részletes és hibastreameket, ha a runbook ír nekik.

   ![Feladat összegzése](../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png)

10. Zárja be a Streams és a Feladat ablaktáblát a MyFirstRunbook lapra való visszatéréshez.

11. Kattintson **a Feladatok** az **Erőforrások** csoportban a Runbook Feladatok lapjának megnyitásához. Ez a lap a runbook által létrehozott összes feladatot megjeleníti. Csak egy feladat jelenik meg a listában, mivel csak egyszer futtatta a feladatot.

   ![Feladatok](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. Kattintson a feladat nevére, ha meg szeretné nyitni ugyanazt a feladat ablaktáblát, amelyet a runbook indításakor megtekintett. Ezen az ablaktáblán megtekintheti a runbookhoz létrehozott feladat részleteit.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Ezt csak akkor teheti meg, ha hitelesíti az előfizetés hitelesítő adatait. A hitelesítés a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) parancsmagát használja.

>[!NOTE]
>A PowerShell runbookok, `Add-AzAccount` és `Connect-AzAccount` `Add-AzureRMAccount` aliasok. Használhatja ezeket a parancsmagokat, vagy [frissítheti a modulokat](../automation-update-azure-modules.md) az Automation-fiókban a legújabb verziókra. Előfordulhat, hogy frissítenie kell a modulokat, még akkor is, ha nemrég létrehozott egy új Automation-fiókot.

1. Nyissa meg a MyFirstRunbook-Workflow lapot, és nyissa meg a szöveges szerkesztőt a **Szerkesztés**gombra kattintva.

2. Törölje `Write-Output` a sort.

3. Vigye a kurzort egy üres sorra a zárójelek között.

4. Írja be vagy másolja be a következő kódot, amely kezeli a hitelesítést az Automation Run As fiókkal.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. Kattintson **a Teszt ablaktábla** elemre, hogy tesztelhesse a runbookot.

6. Kattintson az **Indítás** gombra a teszt elindításához. Miután befejeződött, a következőhöz hasonló kimenetet kell látnia, amely a fiókjából származó alapvető információkat jeleníti meg. Ez a művelet megerősíti, hogy a hitelesítő adat érvényes.

   ![Hitelesítés](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6. lépés: Kód hozzáadása a virtuális gép indításához

Most, hogy a runbook hitelesítése az Azure-előfizetés, kezelheti az erőforrásokat. Adjunk hozzá egy parancsot egy virtuális gép indításához. Az Azure-előfizetésben bármelyik virtuális gép kiválaszthat, és most már hardcoding ezt a nevet a runbookban. Ha több előfizetésben kezeli az erőforrásokat, a `AzContext` paramétert a [Get-AzContext](/powershell/module/az.accounts/get-azcontext) parancsmaggal kell használnia.

1. Adja meg a virtuális gép nevét és erőforráscsoportnevét a [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) parancsmag hívásának megadásával az alábbiak szerint. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

2. Mentse a runbookot, majd kattintson a **Teszt ablaktáblára,** hogy tesztelhesse.

3. Kattintson az **Indítás** gombra a teszt elindításához. Miután befejeződött, ellenőrizze, hogy a virtuális gép elindult.Once it completes, check that the VM has started.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7. lépés – Bemeneti paraméter hozzáadása a forgatókönyvhöz

A runbook jelenleg elindítja a virtuális gép, amely a runbook ban kódolt. Hasznosabb lesz, ha megadhatja a virtuális gép, amikor a runbook elindul. Adjunk hozzá bemeneti paramétereket a runbookhoz, hogy ezt a funkciót biztosítsuk.

1. Adja hozzá a `VMName` `ResourceGroupName` változók at és paramétereket a runbookhoz, és használja a változókat a parancsmaggal az `Start-AzVM` alábbiak szerint.

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

2. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a tesztben lévő két bemeneti változóhoz.

3. Zárja be a Teszt panelt.

4. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.

5. Állítsa le az elindított virtuális gép leállítását.

6. Kattintson az **Indítás** gombra a forgatókönyv elindításához. 

7. Írja be a **VMNAME** és **a RESOURCEGROUPNAME** értékeit a virtuális gép, hogy a meg fog kezdeni.

   ![Forgatókönyv indítása](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. Amikor a runbook befejeződik, ellenőrizze, hogy a virtuális gép elindult.When the runbook completes, verify that the VM has started.

## <a name="next-steps"></a>További lépések

* A PowerShellről további információt, beleértve a nyelvi referencia- és tanulási modulokat, olvassa el a [PowerShell-dokumentumok című dokumentumban.](https://docs.microsoft.com/powershell/scripting/overview)
* A PowerShell-parancsmag referencia, lásd: [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
* A grafikus runbookok első [lépései: Grafikus runbook létrehozása.](automation-tutorial-runbook-graphical.md)
* A PowerShell-runbookok első [lépései: PowerShell-runbook létrehozása.](automation-tutorial-runbook-textual-powershell.md)
* Ha többet szeretne megtudni a runbook-típusokról, azok előnyeiről és korlátairól, olvassa el az [Azure Automation runbook-típusok című témakört.](../automation-runbook-types.md)
* A PowerShell-parancsfájlok támogatási funkcióiról az [Azure Automation natív PowerShell-parancsfájl-támogatása című témakörben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)talál további információt.