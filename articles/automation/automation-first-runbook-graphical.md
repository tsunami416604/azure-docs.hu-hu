---
title: Az első grafikus runbook a Azure Automation
description: Ez az oktatóanyag bemutatja egy egyszerű grafikus forgatókönyv létrehozását, tesztelését és közzétételét.
keywords: runbook, runbook-sablon, runbook automation, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: 8b836ebc0adc6f0616d28b16bfb743dfc4553d1a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367415"
---
# <a name="my-first-graphical-runbook"></a>Az első grafikus forgatókönyvem

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Egy Azure Automation [grafikus forgatókönyv](automation-runbook-types.md#graphical-runbooks) létrehozását bemutató oktatóanyag. Kezdje egy egyszerű runbook, amelyet tesztelni és közzétenni, miközben megtudhatja, hogyan követheti nyomon a runbook-feladatok állapotát. Ezután módosítsa a runbook, hogy ténylegesen kezelhesse az Azure-erőforrásokat, ebben az esetben egy Azure-beli virtuális gépet indítson el. Fejezze be az oktatóanyagot, hogy a runbook robusztusabb legyen a runbook paraméterek és feltételes hivatkozások hozzáadásával.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. A gép leállítása és elindítása óta nem lehet üzemi virtuális gép.

## <a name="step-1---create-runbook"></a>1\. lépés – Runbook létrehozása

Először hozzon létre egy egyszerű runbook, amely kiírja a `Hello World`szöveget.

1. Az Azure Portalon nyissa meg az Automation-fiókját. 

    Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezeknek az eszközöknek a túlnyomó része automatikusan egy új Automation-fiókba kerül. Az előfizetéshez társított hitelesítőadat-eszközt is meg kell adni.
2. A runbookok listájának megnyitásához válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.
3. Hozzon létre egy új runbook a **Runbook létrehozása**lehetőség kiválasztásával.
4. Adja a forgatókönyvnek a **MyFirstRunbook-Graphical** nevet.
5. Ebben az esetben egy [grafikus runbook](automation-graphical-authoring-intro.md)fog létrehozni. A **Runbook típushoz**válassza a **grafikus** lehetőséget.<br> ![Új runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. A forgatókönyv létrehozásához és a grafikus szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-activities"></a>2\. lépés – tevékenységek hozzáadása

A szerkesztő bal oldalán levő Könyvtárvezérlés segítségével kiválaszthatja a forgatókönyvhöz hozzáadni kívánt tevékenységeket. Egy `Write-Output` parancsmagot fog hozzáadni a runbook lévő szöveg kimenetéhez.

1. A könyvtár vezérlőelemben kattintson a Keresés mezőre, és írja be a következőt: `write-output`. A keresési eredmények az alábbi képen láthatók. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Görgessen le a lista aljához. Kattintson a jobb gombbal a **Write-output** elemre, és válassza **a Hozzáadás a vászonhoz**lehetőséget. Azt is megteheti, hogy rákattint a parancsmag neve melletti három pontra (...), majd kiválasztja a **Hozzáadás a vászonhoz**lehetőséget.
1. A vásznon kattintson a **Write-Output** tevékenységre. Ez a művelet megnyitja a konfigurációs vezérlő lapot, amely lehetővé teszi a tevékenység konfigurálását.
1. A **label (címke** ) mező alapértelmezett értéke a parancsmag neve, de megváltoztathatja valami barátságosabbra. Módosítsa `Write Hello World to output`ra.
1. A **Paraméterek** elemre kattintva megadhatja a parancsmag paramétereinek értékét.

   Néhány parancsmagnak több paramétere is van, és ki kell választania, hogy melyiket szeretné használni. Ebben az esetben a `Write-Output`nak csak egy paramétere van beállítva.

1. Válassza ki a `InputObject` paramétert. Ez az a paraméter, amelyet a kimeneti adatfolyamnak küldendő szöveg megadására használ.
1. Az **adatforrás** legördülő menüje olyan forrásokat biztosít, amelyek segítségével feltöltheti a paraméter értékét. Ebben a menüben válassza a **PowerShell-kifejezés**lehetőséget. 

   Az ilyen forrásokból származó kimeneteket egy másik tevékenységként, egy Automation-eszközként vagy egy PowerShell-kifejezésként is használhatja. Ebben az esetben a kimenet csak `Hello World`. Megadhat egy sztringet egy PowerShell-kifejezéssel is.<br>

1. A **kifejezés** mezőbe írja be a `Hello World`, majd kattintson kétszer az **OK gombra** a vászonra való visszatéréshez.
1. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a>3\. lépés – A forgatókönyv tesztelése

Mielőtt közzéteszi a runbook, hogy az éles környezetben elérhető legyen, tesztelje, hogy megfelelően működik-e. A runbook tesztelése a Piszkozat verzióját futtatja, és lehetővé teszi a kimenet interaktív megjelenítését.

1. A teszt panel megnyitásához válassza a **teszt panelt** .
1. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
1. Vegye figyelembe, hogy létrejön egy [runbook-feladatok](automation-runbook-execution.md) , és az állapota megjelenik a panelen.

   A feladatok állapota `Queued`ként indul el, ami azt jelzi, hogy a feladatot a felhőben lévő runbook-feldolgozó elérhetővé válására vár. Az állapot úgy változik, hogy `Starting`, amikor egy feldolgozó a feladatot kéri. Végül az állapot `Running` válik, amikor a runbook ténylegesen elindul.

1. Ha a runbook-feladatok befejeződik, a teszt ablaktábla megjeleníti a kimenetet. Ebben az esetben a `Hello World`jelenik meg.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4\. lépés: Közzététel és a forgatókönyv indítása

A létrehozott runbook még mindig Piszkozat módban van. Az üzemi környezetben való futtatás előtt közzé kell tennünk. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nincs Közzétett verzió, mivel még csak most hozta létre a runbookot.

1. Válassza a **Közzététel** lehetőséget a runbook közzétételéhez, majd az **Igen** gombra, amikor a rendszer kéri.
1. Görgessen balra a runbook megtekintéséhez a Runbookok lapon, és vegye figyelembe, hogy a **szerzői állapot** értéke **közzétételre**van állítva.
1. Görgessen vissza jobbra a **MyFirstRunbook-grafikus**oldal megtekintéséhez.

   A felső beállítások lehetővé teszik a runbook elindítását, a jövőbeli kezdési idő beírását, vagy [webhook](automation-webhooks.md) létrehozását, hogy a runbook http-hívással is elindítható legyen.

1. Kattintson a **Start** gombra, majd az **Igen** gombra, amikor a rendszer felszólítja a runbook elindítására.
1. Megnyílik a létrehozott runbook-feladatokhoz tartozó feladatok ablaktábla. Ellenőrizze, hogy a feladatok állapota mező **kész** **állapotú** -e.
1. Kattintson a **kimenet** elemre a kimenet lap megnyitásához, ahol megtekintheti `Hello World` megjeleníthető.
1. A kimeneti oldal bezárásához.
1. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. A kimeneti adatfolyamban csak `Hello World` jelenik meg. 

    Vegye figyelembe, hogy a streamek panel más streameket jeleníthet meg egy runbook-feladatokhoz, például a részletes és a hiba-adatfolyamokhoz, ha a runbook ezeket írja.
1. A **MyFirstRunbook-grafikus** laphoz való visszatéréshez zárjuk be a streamek panelt és a feladatok panelt.
1. A runbook összes feladatának megtekintéséhez válassza az **erőforrások**alatt lévő **feladatok** lehetőséget. A feladatok lap felsorolja a runbook által létrehozott összes feladatot. Csak egy feladattípus jelenik meg, mert csak egyszer futtatta a feladatot.
1. Kattintson a feladatokra, hogy megnyissa a runbook elindításakor megtekintett feladatok ablaktábláját. Ezen ablaktábla használatával megtekintheti a runbook létrehozott feladatok részleteit.

## <a name="step-5---create-variable-assets"></a>5\. lépés: Változó adategységek létrehozása

Megvizsgálta és közzétette a runbook, de eddig nem csinál semmi hasznosat az Azure-erőforrások kezeléséhez. Mielőtt konfigurálja a runbook a hitelesítéshez, létre kell hoznia egy változót az előfizetés-azonosító tárolásához, egy tevékenységet kell beállítania a hitelesítéshez, majd hivatkoznia kell a változóra. Az előfizetési környezetre mutató hivatkozással könnyedén dolgozhat több előfizetéssel is.

1. Másolja az előfizetés-azonosítót a navigációs ablaktábla **előfizetések** lehetőségével.
1. Az Automation-fiókok lapon válassza a **változók** elemet a **megosztott erőforrások**területen.
1. Válassza **a változó hozzáadása**lehetőséget.
1. Az új változó lapon végezze el a következő beállításokat a megadott mezőkben.

    * **Név** – adja meg a `AzureSubscriptionId`.
    * **Érték** – adja meg az előfizetés-azonosítóját. 
    * **Típus** – hagyja kiválasztva a karakterláncot.
    * **Titkosítás** – az alapértelmezett értéket használja.
1. A változó létrehozásához kattintson a **Létrehozás** gombra.

## <a name="step-6---add-authentication"></a>6\. lépés – hitelesítés hozzáadása

Most, hogy már rendelkezik egy változóval az előfizetés-azonosító tárolásához, beállíthatja, hogy a runbook az előfizetés futtató hitelesítő adataival hitelesítse magát. Ezt úgy teheti meg, hogy hozzáad egy eszközként az Azure-beli futtató összekötőt. Emellett hozzá kell adnia a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) parancsmagot és a [set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) parancsmagot a vászonhoz.

>[!NOTE]
>A PowerShell-runbookok esetében a `Add-AzAccount` és az `Add-AzureRMAccount` a `Connect-AzAccount`aliasa. Vegye figyelembe, hogy ezek az aliasok nem érhetők el a grafikus runbookok. A grafikus runbook csak önmagát `Connect-AzAccount`használhatják.

1. Navigáljon a runbook, és válassza a **Szerkesztés** lehetőséget a **MyFirstRunbook-grafikus** oldalon.
1. Nincs szükség további `Write Hello World to output` bejegyzésre. Csak kattintson a három pontra, majd válassza a **Törlés**lehetőséget.
1. A könyvtár vezérlőben bontsa ki az **eszközök**, majd a **kapcsolatok**elemet. Adja hozzá `AzureRunAsConnection` a vászonhoz a **Hozzáadás a vászonhoz**lehetőség kiválasztásával.
1. A könyvtár vezérlőelemben írja be a `Connect-AzAccount` kifejezést a Keresés mezőbe.
1. `Connect-AzAccount` hozzáadása a vászonhoz.
1. Vigye a kurzort a **Futtató kapcsolat létesítése** fölé, és várja meg, amíg megjelenik az alakzat alján egy kör. Kattintson a körre, és húzza a nyilat `Connect-AzAccount` egy hivatkozás létrehozásához. A runbook a `Get Run As Connection`, majd a `Connect-AzAccount`futtatásával kezdődik.<br> ![Hivatkozás létrehozása a tevékenységek között](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. A vásznon válassza a `Connect-AzAccount`lehetőséget. A konfigurálási vezérlő ablaktáblán írja be a **login az Azure** -ba a **label (címke** ) mezőbe.
1. Kattintson a **Paraméterek**elemre, és megjelenik a tevékenység paraméterének konfigurációja lap.
1. A `Connect-AzAccount` parancsmagnak több paramétere is van, és a paraméterérték megadása előtt ki kell választania egyet. Kattintson a **paraméter beállítása** elemre, majd válassza a **serviceprincipalcertificate paraméterkészletet**lehetőséget.
1. A paraméterhez tartozó paraméterek a tevékenység paraméterének konfiguráció lapján jelennek meg. Kattintson az **APPLICATIONID** elemre.<br> ![Azure-fiók paramétereinek hozzáadása](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. A paraméter értéke lapon végezze el a következő beállításokat, majd kattintson az **OK**gombra.

   * **Adatforrás** – válassza a **tevékenység kimenete**elemet.
   * Adatforrás-lista – válassza ki **a futtató kiszolgáló beolvasása**lehetőséget.
   * **Mező elérési útja** – típus `ApplicationId`. A mező elérési útjához tartozó tulajdonság nevét adja meg, mert a tevékenység több tulajdonsággal rendelkező objektumot ad eredményül.
1. Kattintson a **CERTIFICATETHUMBPRINT**elemre, majd a paraméter értéke lapon végezze el a következő beállításokat, majd kattintson az **OK**gombra.

    * **Adatforrás** – válassza a **tevékenység kimenete**elemet.
    * Adatforrás-lista – válassza ki **a futtató kiszolgáló beolvasása**lehetőséget.
    * **Mező elérési útja** – típus `CertificateThumbprint`.
1. Kattintson a **SERVICEPRINCIPAL**elemre, majd a paraméter értéke lapon válassza a **ConstantValue** lehetőséget az **adatforrás** mezőhöz; kattintson a **true (igaz**) lehetőségre. majd kattintson **az OK**gombra.
1. Kattintson a **TENANTID**elemre, és végezze el a következő beállításokat a paraméter értéke lapon. Ha elkészült, kattintson kétszer **az OK gombra** .

    * **Adatforrás** – válassza a **tevékenység kimenete**elemet. 
    * Adatforrás-lista – válassza ki **a futtató kiszolgáló beolvasása**lehetőséget.
    * **Mező elérési útja** – típus `TenantId`. 
1. A könyvtár vezérlőelemben írja be a `Set-AzContext` kifejezést a Keresés mezőbe.
1. `Set-AzContext` hozzáadása a vászonhoz.
1. Válassza ki `Set-AzContext` a vásznon. A konfigurációs vezérlő ablaktáblán írja be a `Specify Subscription Id` értéket a **label (címke** ) mezőbe.
1. Kattintson a **Paraméterek** elemre, és megjelenik a tevékenység paraméterének konfigurációja lap.
1. A `Set-AzContext` parancsmagnak több paramétere is van, és a paraméterérték megadása előtt ki kell választania egyet. Kattintson a **paraméter beállítása** elemre, majd válassza a **SubscriptionId**lehetőséget.
1. A paraméterhez tartozó paraméterek a tevékenység paraméterének konfiguráció lapján jelennek meg. Kattintson a **SubscriptionID**elemre.
1. A paraméter értéke lapon válassza a **változó eszköz** lehetőséget az **adatforrás** mezőben, majd válassza a **AzureSubscriptionId** lehetőséget a forrás listából. Ha elkészült, kattintson kétszer **az OK gombra** .
1. Vigye az egérmutatót `Login to Azure`ra, amíg az alakzat alján egy kör nem jelenik meg. Kattintson a körre, és húzza a nyilat `Specify Subscription Id`. 

A forgatókönyvnek ezen a ponton az alábbi kódhoz kell hasonlítania: <br>![Forgatókönyv-hitelesítés konfigurálása](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>7\. lépés – Virtuális gépet elindító tevékenység hozzáadása

Most hozzá kell adnia egy `Start-AzVM` tevékenységet a virtuális gép elindításához. Bármelyik virtuális gépet kiválaszthatja az Azure-előfizetésében, és most már rögzítjük a nevét a [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) parancsmagba.

1. A könyvtár vezérlőelemben írja be a `Start-Az` kifejezést a Keresés mezőbe.
2. Vegyen fel `Start-AzVM`t a vászonra, majd kattintson rá, és húzza a `Specify Subscription Id`alá.
1. Vigye az egérmutatót `Specify Subscription Id`ra, amíg az alakzat alján egy kör nem jelenik meg. Kattintson a körre, és húzza a nyilat `Start-AzVM`.
1. Válassza a(z) `Start-AzVM` lehetőséget. Kattintson a **Parameters (paraméterek** ), majd a **paraméter beállítása** elemre a tevékenység készletének megtekintéséhez.
1. Válassza a **ResourceGroupNameParameterSetName** lehetőséget. A **ResourceGroupName** és a **Name (név** ) mezők mellett felkiáltójelek is szerepelnek, így jelezve, hogy kötelező paraméterek. Vegye figyelembe, hogy mindkét mező sztring értékeket vár.
1. Válassza ki a **Name** paramétert. Válassza a **PowerShell-kifejezés** lehetőséget az **adatforrás** mezőhöz. A runbook elindításához használt virtuális gép esetében írja be a nevet idézőjelek közé. Kattintson az **OK** gombra.
1. Válassza a **ResourceGroupName** elemet. Használja az érték **PowerShell-kifejezést** az **adatforrás** mezőhöz, és írja be az erőforráscsoport nevét idézőjelek közé. Kattintson az **OK** gombra.
1. Kattintson a **teszt panelre** , hogy tesztelni tudja a runbook.
1. A teszt **elindításához** kattintson a Start gombra. A művelet befejezését követően győződjön meg arról, hogy a virtuális gép elindult. 

A forgatókönyvnek ezen a ponton az alábbi kódhoz kell hasonlítania: <br>![Forgatókönyv-hitelesítés konfigurálása](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>8\. lépés – további bemeneti paraméterek hozzáadása

A runbook jelenleg a `Start-AzVM` parancsmaghoz megadott erőforráscsoporthoz indítja el a virtuális gépet. A runbook akkor hasznos, ha a runbook indításakor megadja a nevet és az erőforráscsoportot is. Adja hozzá a runbook bemeneti paramétereit a funkció megadásához.

1. A **MyFirstRunbook-grafikus** panel **Szerkesztés** gombjára kattintva nyissa meg a grafikus szerkesztőt.
1. Válassza a **bemenet és kimenet** lehetőséget, majd **adja hozzá a bemenet** elemet a Runbook bemeneti paraméter panel megnyitásához.
1. Hajtsa végre a következő beállításokat a megadott mezőkben, majd kattintson az **OK**gombra.
   * **Név** – adja meg `VMName`.
   * **Típus** – megtartja a karakterlánc-beállítást.
   * **Kötelező** – módosítsa az értéket **Igen**értékre.
1. Hozzon létre egy második kötelező bemeneti paramétert `ResourceGroupName` néven, majd kattintson az **OK** gombra a bemeneti és a kimeneti ablaktábla bezárásához.<br> ![Runbook bemeneti paraméterei](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Válassza ki a `Start-AzVM` tevékenységet, majd kattintson a **Parameters (paraméterek**) elemre.
1. Módosítsa az **adatforrás** mezőjét a **Runbook bemenetre**. Ezután válassza a **VMName**lehetőséget.
1. Módosítsa a **ResourceGroupName** **adatforrás** mezőjét a **Runbook bemenetre** , majd válassza a **ResourceGroupName**lehetőséget.<br> ![Start-AzVM paraméterek](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a teszt során használt két bemeneti változóhoz.
1. Zárja be a Teszt panelt.
1. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.
1. Állítsa le a korábban elindított virtuális gépet.
1. Kattintson az **Indítás** gombra a forgatókönyv elindításához. Írja be `VMName` és `ResourceGroupName` értékeit az elindítani kívánt virtuális géphez.
1. A runbook befejezését követően győződjön meg arról, hogy a virtuális gép elindult.

## <a name="step-9---create-a-conditional-link"></a>9\. lépés – Feltételes hivatkozás létrehozása

Mostantól módosíthatja a runbook, hogy csak akkor próbálja meg elindítani a virtuális gépet, ha még nem indult el. Ezt úgy teheti meg, hogy hozzáad egy [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) parancsmagot, amely lekéri a virtuális gép példány-szintű állapotát. Ezután hozzáadhat egy `Get Status` nevű PowerShell munkafolyamat-modult egy PowerShell-kódrészlettel, amely megállapítja, hogy a virtuális gép állapota fut vagy leállt. A `Get Status` modul feltételes hivatkozása csak akkor fut `Start-AzVM`, ha az aktuálisan futó állapot leáll. Az eljárás végén a runbook a `Write-Output` parancsmaggal küldi el az üzenetet, amely tájékoztatja arról, ha a virtuális gép sikeresen elindult.

1. Nyissa meg a **MyFirstRunbook** a grafikus szerkesztőben.
1. A `Specify Subscription Id` és `Start-AzVM` közötti kapcsolat eltávolításához kattintson rá, majd nyomja le a **delete**billentyűt.
1. A könyvtár vezérlőelemben írja be a `Get-Az` kifejezést a Keresés mezőbe.
1. `Get-AzVM` hozzáadása a vászonhoz.
1. Válassza a `Get-AzVM` lehetőséget, majd kattintson a **set paraméterre** a parancsmag készletének megtekintéséhez. 
1. Válassza ki a **GetVirtualMachineInResourceGroupNameParamSet** paraméterkészletet. A **ResourceGroupName** és a **Name** mezőhöz felkiáltójelek tartoznak, ami azt jelzi, hogy a kötelező paramétereket határozzák meg. Vegye figyelembe, hogy mindkét mező sztring értékeket vár.
1. A **név** **adatforrás** területén válassza a **Runbook bemenet**, majd a **VMName**lehetőséget. Kattintson az **OK** gombra.
1. A **ResourceGroupName** **adatforrás** területén válassza a **Runbook bemenet**, majd a **ResourceGroupName**lehetőséget. Kattintson az **OK** gombra.
1. Az **állapot**elemnél az **adatforrás** területen válassza az **állandó érték**, majd az **igaz**értéket. Kattintson az **OK** gombra.
1. Hozzon létre egy hivatkozást `Specify Subscription Id`ról `Get-AzVM`ra.
1. A könyvtár vezérlőben bontsa ki a **Runbook vezérlő** elemet, és adjon hozzá egy **kódot** a vászonhoz.  
1. Hozzon létre egy hivatkozást `Get-AzVM`ról `Code`ra.  
1. Kattintson a `Code` elemre, és a konfiguráció ablaktáblán módosítsa a címkét az **állapot lekéréséhez**.
1. Válassza a `Code` lehetőséget, és megjelenik a Kódszerkesztő oldal.  
1. Illessze be a következő kódrészletet a szerkesztő lapra.

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Hozzon létre egy hivatkozást `Get Status`ról `Start-AzVM`ra.<br> ![Runbook kódmodullal](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Válassza ki a hivatkozást, és a konfiguráció panelen módosítsa a **feltétel alkalmazása** **beállítást igen**értékre. Vegye figyelembe, hogy a hivatkozás szaggatott vonalba kerül, ami azt jelzi, hogy a célként megadott tevékenység csak akkor fut, ha a feltétel igaz értékre van feloldva.  
1. A **feltétel kifejezésnél**írja be a következőt: `$ActivityOutput['Get Status'] -eq "Stopped"`. `Start-AzVM` most csak akkor fut, ha a virtuális gép le van állítva.
1. A Könyvtár vezérlőben bontsa ki a **Parancsmagok** elemet, és válassza a **Microsoft.PowerShell.Utility** lehetőséget.
1. Kétszer adja hozzá a `Write-Output`t a vászonhoz.
1. Az első `Write-Output` vezérlőnél kattintson a **Paraméterek** elemre, és módosítsa a **címke** értéket a **virtuális gép értesítéséhez**.
1. A **inputobject elemnél**módosítsa az **adatforrást** a **PowerShell-kifejezésre**, és írja be a kifejezést `$VMName successfully started.`.
1. A második `Write-Output` vezérlőn kattintson a **Paraméterek** elemre, és módosítsa a **címke** értékét a **virtuális gép indításának megkezdéséhez**.
1. A **inputobject elemnél**módosítsa az **adatforrást** a **PowerShell-kifejezésre**, és írja be a kifejezést `$VMName could not start.`.
1. `Start-AzVM` hivatkozásokat hozhat létre a `Notify VM Started` és a `Notify VM Start Failed`.
1. Válassza ki a `Notify VM Started` mutató hivatkozást, és módosítsa a **feltétel alkalmazása** beállítást igaz értékre.
1. A **feltétel kifejezésnél**írja be a következőt: `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`. Ez a `Write-Output` vezérlés most csak akkor fut, ha a virtuális gép sikeresen elindul.
1. Válassza ki a `Notify VM Start Failed` mutató hivatkozást, és módosítsa a **feltétel alkalmazása** beállítást igaz értékre.
1. A **feltétel kifejezés** mezőjébe írja be a következőt: `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`. Ez a `Write-Output` vezérlés most csak akkor fut, ha a virtuális gép nem indult el sikeresen. A runbook a következő képhez hasonlóan kell kinéznie: <br> ![Runbook Write-Output parancsmaggal](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt.
1. Indítsa el a runbook a virtuális gép leállításával, és a számítógépnek el kell indulnia.

## <a name="next-steps"></a>Következő lépések

* További információ a grafikus létrehozásról: [grafikus létrehozás Azure Automationban](automation-graphical-authoring-intro.md).
* A PowerShell-forgatókönyvek használatának megismeréséhez tekintse meg a következőt: [Az első PowerShell-runbookom](automation-first-runbook-textual-powershell.md).
* A PowerShell-munkafolyamat runbookok megkezdéséhez tekintse meg [az első PowerShell-munkafolyamat runbook](automation-first-runbook-textual.md).