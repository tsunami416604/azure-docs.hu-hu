---
title: Az első PowerShell-munkafolyamat runbook a Azure Automation
description: Ez az oktatóanyag bemutatja egy egyszerű szöveges forgatókönyv PowerShell-munkafolyamattal való létrehozását, tesztelését és közzétételét.
keywords: powershell-munkafolyamat, powershell-munkafolyamat példák, munkafolyamat powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: 4c816ade5ae6b416ba716433f385b75e1b32eb92
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226504"
---
# <a name="my-first-powershell-workflow-runbook"></a>Az első PowerShell-alapú munkafolyamat-forgatókönyvem

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Egy Azure Automation [PowerShell-alapú munkafolyamat-forgatókönyv](automation-runbook-types.md#powershell-workflow-runbooks) létrehozását bemutató oktatóanyag. A runbook-feladatok állapotának nyomon követése során kezdjen el egy olyan egyszerű runbook, amelyet tesztel és tesz közzé. Ezután módosítsa a runbook az Azure-erőforrások tényleges kezelésére, amely egy Azure-beli virtuális gép indításával illusztrált. A runbook-paraméterek hozzáadásával a runbook robusztusabb lehet.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. A gép leállítása és elindítása óta nem lehet üzemi virtuális gép.

## <a name="step-1---create-new-runbook"></a>1\. lépés – Új forgatókönyv létrehozása

Először egy egyszerű runbookot hozunk létre, amelynek a kimenete a **Hello World** szöveg.

1. Az Azure Portalon nyissa meg az Automation-fiókját.

   Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezeknek az eszközöknek a túlnyomó része automatikusan egy új Automation-fiókba kerül. Az előfizetéshez társított hitelesítőadat-eszközt is meg kell adni.
 
1. A runbookok listájának megnyitásához válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.
1. Hozzon létre egy új runbook a **Runbook létrehozása**lehetőség kiválasztásával.
1. Adja a forgatókönyvnek a **MyFirstRunbook-Workflow** nevet.
1. Ebben az esetben létre fog hozni egy [PowerShell munkafolyamat-runbook](automation-runbook-types.md#powershell-workflow-runbooks). Ezért válassza a **PowerShell-munkafolyamatot** a **Runbook típushoz**.
1. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-code-to-the-runbook"></a>2\. lépés – Kód hozzáadása a forgatókönyvhöz

Közvetlenül is írhat kódot a runbook, vagy kijelölhet parancsmagokat, runbookok és eszközöket a könyvtár vezérlőelemből, és hozzáadhatja őket a runbook a kapcsolódó paraméterekkel. Ebben az oktatóanyagban közvetlenül a runbook írja be a kódot.

1. A runbook jelenleg csak a szükséges **munkafolyamat** -kulcsszóval, a runbook nevével és a teljes munkafolyamatot tartalmazó kapcsos zárójelekkel van ellátva.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Írja be a `Write-Output "Hello World"`t a kapcsos zárójelek közé.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a>3\. lépés – A forgatókönyv tesztelése

Mielőtt közzéteszi a runbook, hogy az éles környezetben elérhető legyen, tesztelje, hogy megfelelően működik-e. A runbook tesztelése a Piszkozat verzióját futtatja, és lehetővé teszi a kimenet interaktív megjelenítését.

1. A teszt panel megnyitásához válassza a **teszt panelt** .
1. Kattintson a **Start** gombra a teszt elindításához, az egyetlen engedélyezett lehetőség tesztelésével.
1. Vegye figyelembe, hogy létrejön egy [runbook-feladatok](automation-runbook-execution.md) , és az állapota megjelenik a panelen.

   A feladatok állapota a **várólistára**kerül, ami azt jelzi, hogy a runbook-feldolgozó a felhőben elérhetővé válására vár. Az **állapot akkor változik, ha egy** feldolgozó a feladatot állítja be. Végül az állapot akkor **fut** le, amikor a runbook ténylegesen elindul.

1. Ha a runbook-feladatok befejeződik, a teszt ablaktábla megjeleníti a kimenetet. Ebben az esetben a **Hello World** szöveg jelenik meg.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4\. lépés: Közzététel és a forgatókönyv indítása

A létrehozott runbook még mindig Piszkozat módban van. Az éles környezetben való futtatás előtt közzé kell tennie. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nincs Közzétett verzió, mivel még csak most hozta létre a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
1. Görgessen balra, hogy megtekintse a runbook a **runbookok** lapon, és figyelje meg, hogy a **szerzői állapot** mező **közzé**van-e téve.
1. Görgessen vissza jobbra a **MyFirstRunbook-workflow**oldal megtekintéséhez.

   A felső beállítások lehetővé teszik a runbook elindítását, a jövőbeli kezdési idő beírását, vagy [webhook](automation-webhooks.md) létrehozását, hogy a runbook http-hívással is elindítható legyen.

1. Kattintson a **Start** gombra, majd az **Igen** gombra, amikor a rendszer felszólítja a runbook elindítására.

   ![Forgatókönyv indítása](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Megnyílik a létrehozott runbook-feladatokhoz tartozó feladatok ablaktábla. Ebben az esetben hagyja megnyitva a panelt, hogy megtekintse a feladatok állapotát.

1. Vegye figyelembe, hogy a feladatok állapota a **feladatok összegzése**területen látható. Ez az állapot megegyezik a runbook tesztelésekor meglátott állapotokkal.

   ![Feladat összegzése](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Ha a forgatókönyv a **Befejezve** állapotot mutatja, kattintson a **Kimenet** lehetőségre. Megnyílik a kimeneti oldal, ahol megtekintheti **"Helló világ!" alkalmazás** üzenetét.

   ![Feladat összegzése](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. A kimeneti oldal bezárásához.

1. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. A kimeneti adatfolyamban csak **"Helló világ!" alkalmazás** jelenik meg. Vegye figyelembe, hogy a streamek panel más streameket jeleníthet meg egy runbook-feladatokhoz, például a részletes és a hiba-adatfolyamokhoz, ha a runbook ezeket írja.

   ![Feladat összegzése](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. A **MyFirstRunbook** lapra való visszatéréshez zárjuk be a streamek panelt és a feladatok panelt.
1. A runbook feladatok lapjának megnyitásához kattintson az **erőforrások** alatt lévő **feladatok** elemre. Ezen az oldalon a runbook által létrehozott összes feladat szerepel. Csak egy feladatot kell látnia, mert csak egyszer futtatta a feladatot.

   ![Feladatok](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Kattintson a feladatokra, hogy megnyissa a runbook elindításakor megtekintett feladatok ablaktábláját. Ezen ablaktábla használatával megtekintheti a runbook létrehozott feladatok részleteit.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5\. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Ez a művelet csak akkor hajtható végre, ha az előfizetés hitelesítő adataival hitelesíti magát. A hitelesítés a **kapcsolat-AzAccount** parancsmagot használja.

>[!NOTE]
>PowerShell-runbookok esetében a **Add-AzAccount** és a **Add-AzureRMAccount** aliasok a **csatlakozási-AzAccount**. Ezeket a parancsmagokat használhatja, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban a legújabb verzióra. Előfordulhat, hogy frissítenie kell a modulokat akkor is, ha nemrég létrehozott egy új Automation-fiókot.

1. Navigáljon a **MyFirstRunbook – munkafolyamat** lapra, és nyissa meg a szöveges szerkesztőt a **Szerkesztés**gombra kattintva.
2. Törölje a `Write-Output` sort.
3. Vigye a kurzort egy üres sorra a zárójelek között.
4. Írja be vagy másolja és illessze be a következő kódot, amely kezeli a hitelesítést az Automation futtató fiókkal.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Kattintson a **teszt panelre** , hogy tesztelni tudja a runbook.
1. Kattintson az **Indítás** gombra a teszt elindításához. Ha befejeződik, a következőhöz hasonló kimenetnek kell megjelennie, amely a fiók alapvető információit jeleníti meg. Ez a művelet megerősíti, hogy a hitelesítő adat érvényes.

   ![Hitelesítés](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6\. lépés: Kód hozzáadása a virtuális gép indításához

Most, hogy a runbook hitelesítést végez az Azure-előfizetésben, kezelheti az erőforrásokat. Hozzunk létre egy parancsot egy virtuális gép indításához. Bármelyik virtuális gépet kiválaszthatja az Azure-előfizetésében, és most már rögzítjük ezt a nevet a runbook. Ha több előfizetésen keresztül kezeli az erőforrásokat, akkor a *AzContext* paramétert kell használnia a [Get-AzContext](/powershell/module/az.accounts/get-azcontext) parancsmaggal.

1. Adja meg a kezdéshez a virtuális gép nevét és erőforráscsoport-nevét, majd írja be a [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) parancsmag meghívását az alábbi ábrán látható módon. 

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

1. Mentse a runbook, majd kattintson a **teszt panelre** , hogy tesztelni tudja.
1. Kattintson az **Indítás** gombra a teszt elindításához. Miután befejeződött, győződjön meg arról, hogy a virtuális gép elindult.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7\. lépés – Bemeneti paraméter hozzáadása a forgatókönyvhöz

A runbook jelenleg elindítja azt a virtuális gépet, amelyet a runbook hardcoded. Hasznos lehet, ha megadhatja a virtuális gépet a runbook indításakor. Adja hozzá a runbook bemeneti paramétereit a funkció megadásához.

1. Adja hozzá a *VMName* és a *ResourceGroupName* értékeit a runbook, majd használja a társított változókat a **Start-AzVM** parancsmaggal az alábbi ábrán látható módon.

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
5. Állítsa le az elindított virtuális gépet.
6. Kattintson az **Indítás** gombra a forgatókönyv elindításához. 
7. Írja be a **VMNAME** és a **RESOURCEGROUPNAME** értékeit az elindítani kívánt virtuális gépre vonatkozóan.

   ![Forgatókönyv indítása](media/automation-first-runbook-textual/automation-pass-params.png)

8. A runbook befejezésekor ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="next-steps"></a>Következő lépések

* A PowerShell-lel kapcsolatos további információkért, beleértve a nyelvi referenciákat és a tanulási modulokat, tekintse át a [PowerShell-dokumentumokat](https://docs.microsoft.com/powershell/scripting/overview).
* A grafikus runbookok megkezdéséhez tekintse meg [az első grafikus runbook](automation-first-runbook-graphical.md).
* A PowerShell-forgatókönyvek használatának megismeréséhez tekintse meg a következőt: [Az első PowerShell-runbookom](automation-first-runbook-textual-powershell.md).
* További információ a runbook típusairól és azok előnyeiről és korlátairól: [Azure Automation runbook-típusok](automation-runbook-types.md).
* A PowerShell-parancsfájlok támogatásával kapcsolatos további információkért lásd: [natív PowerShell-parancsfájl-támogatás Azure Automationban](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
