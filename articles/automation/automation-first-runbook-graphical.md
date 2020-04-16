---
title: Az első grafikus runbook az Azure Automationben
description: Ez az oktatóanyag bemutatja egy egyszerű grafikus forgatókönyv létrehozását, tesztelését és közzétételét.
keywords: runbook, runbook-sablon, runbook automation, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: bcef0574e16e0b4d28755716c32670b00c65af14
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406092"
---
# <a name="my-first-graphical-runbook"></a>Az első grafikus forgatókönyvem

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Egy Azure Automation [grafikus forgatókönyv](automation-runbook-types.md#graphical-runbooks) létrehozását bemutató oktatóanyag. Kezdje egy egyszerű runbook, amely tesztelheti és közzéteheti, miközben megtanulják, hogyan követheti nyomon a Runbook-feladat állapotát. Ezután módosítsa a runbookot az Azure-erőforrások tényleges kezeléséhez, ebben az esetben egy Azure virtuális gép indítása. Végezze el az oktatóanyagot, hogy a runbook robusztusabbá tegye a runbook-paraméterek és a feltételes hivatkozások hozzáadásával.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Mivel megáll, és indítsa el ezt a gépet, nem kell egy éles virtuális gép.

## <a name="step-1---create-runbook"></a>1. lépés – Runbook létrehozása

Első lépésként hozzon létre egy `Hello World`egyszerű runbookot, amely a szöveget adja ki.

1. Az Azure Portalon nyissa meg az Automation-fiókját. 

    Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nek az eszközöknek a többsége az új Automation-fiókban automatikusan szereplő modulok. Az előfizetéshez társítva kell lennie a Hitelesítő adatokkal.
2. Válassza **a Runbookok** **a Folyamatautomatizálás** csoportban a runbookok listájának megnyitásához.
3. Hozzon létre egy új runbookot **a Runbook létrehozása**lehetőség kiválasztásával.
4. Adja a forgatókönyvnek a **MyFirstRunbook-Graphical** nevet.
5. Ebben az esetben [egy grafikus runbookot](automation-graphical-authoring-intro.md)fog létrehozni. Válassza a Grafikus a **Runbook típushoz választógombot.** **Graphical**<br> ![Új runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. A forgatókönyv létrehozásához és a grafikus szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-activities"></a>2. lépés – Tevékenységek hozzáadása

A szerkesztő bal oldalán levő Könyvtárvezérlés segítségével kiválaszthatja a forgatókönyvhöz hozzáadni kívánt tevékenységeket. A runbook szövegének `Write-Output` kimenetéhez hozzá fog adni egy parancsmatot.

1. A Tár vezérlőben kattintson a `write-output`keresőmezőre, és írja be a szöveget. A keresési eredmények az alábbi képen láthatók. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Görgessen le a lista aljához. Kattintson a jobb gombbal **az Írás-kimenet elemre,** és válassza **a Hozzáadás a vászonhoz parancsot.** Másik lehetőségként kattintson a parancsmag neve melletti három pontra (...), majd válassza a **Hozzáadás a vászonhoz**lehetőséget .
1. A vásznon kattintson a **Write-Output** tevékenységre. Ez a művelet megnyitja a Konfiguráció vezérlőlapot, amely lehetővé teszi a tevékenység konfigurálását.
1. A **Címke** mező alapértelmezés szerint a parancsmag neve, de módosíthatja azt valami barátságosabb. Módosítsa a `Write Hello World to output`.
1. A **Paraméterek** elemre kattintva megadhatja a parancsmag paramétereinek értékét.

   Egyes parancsmagok több paraméterkészlettel rendelkeznek, és ki kell választania, hogy melyiket használja. Ebben az `Write-Output` esetben csak egy paraméter van beállítva.

1. Válassza `InputObject` ki a paramétert. Ez az a paraméter, amelyet a kimeneti adatfolyamba küldő szöveg megadásához használhat.
1. Az **Adatforrás** legördülő menü olyan forrásokat tartalmaz, amelyek segítségével feltöltheti a paraméter értékét. Ebben a menüben válassza a **PowerShell-kifejezés lehetőséget.** 

   Ilyen forrásokból származó kimenetet használhat, például egy automation-eszközt vagy egy PowerShell-kifejezést. Ebben az esetben a `Hello World`kimenet csak . Megadhat egy sztringet egy PowerShell-kifejezéssel is.<br>

1. A **Kifejezés** mezőbe `Hello World` írja be a szöveget, majd kattintson kétszer az **OK** gombra a vászonra való visszatéréshez.
1. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a>3. lépés – A forgatókönyv tesztelése

Mielőtt közzétenné a runbookot, hogy elérhetővé tegye éles környezetben, tesztelje, hogy megfelelően működik-e. A runbook tesztelése futtatja a Piszkot verziója, és lehetővé teszi, hogy interaktívan tekintse meg a kimenetet.

1. Válassza **a Teszt ablaktábla** lehetőséget a Teszt ablaktábla megnyitásához.
1. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
1. Vegye figyelembe, hogy létrejön egy [runbook-feladat,](automation-runbook-execution.md) és állapota megjelenik az ablaktáblán.

   A feladat állapota `Queued`kezdődik , jelezve, hogy a feladat arra vár, hogy a runbook-dolgozó a felhőben elérhetővé válik. Az állapot `Starting` akkor változik, amikor egy dolgozó igényt tart a feladatra. Végül az állapot `Running` akkor válik, amikor a runbook ténylegesen elindul.

1. Amikor a runbook-feladat befejeződik, a Teszt ablaktábla megjeleníti a kimenetét. Ebben az esetben, `Hello World`látod .

    ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4. lépés: Közzététel és a forgatókönyv indítása

A létrehozott runbook továbbra is Vázlat módban van. Az üzemi környezetben való futtatás előtt közzé kell tennünk. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nincs Közzétett verzió, mivel még csak most hozta létre a runbookot.

1. Válassza **a Közzététel** lehetőséget a runbook közzétételéhez, majd az **Igen** lehetőséget, amikor a rendszer kéri.
1. Görgessen balra a Runbook oklapon lévő runbook megtekintéséhez, és vegye figyelembe, hogy a **Szerzői állapot** értéke **Közzétéve**érték.
1. Görgessen vissza jobbra a **MyFirstRunbook-Graphic**oldal megtekintéséhez.

   A beállítások a felső lehetővé teszi, hogy indítsa el a runbook most, ütemezése egy jövőbeli kezdési időpont, vagy hozzon létre egy [webhook,](automation-webhooks.md) hogy a runbook lehet indítani egy HTTP-hívás.

1. Válassza **a Start,** majd **az Igen** lehetőséget, amikor a runbook indítására kéri.
1. Meg van nyitva egy feladat ablaktábla a létrehozott runbook-feladathoz. Ellenőrizze, hogy a **Feladat állapota** mezőben **látható-e a Befejezett**.
1. Kattintson **a Kimenet** gombra a `Hello World` Kimenet lap megnyitásához, ahol megjelenik a megjelenítés.
1. Zárja be a Kimenet lapot.
1. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. Csak a `Hello World` kimeneti adatfolyamban jelenik meg. 

    Vegye figyelembe, hogy a Streams ablaktábla más adatfolyamokat is megjeleníthet egy runbook-feladathoz, például a részletes és a hibaadatfolyamokat, ha a runbook ír nekik.
1. Zárja be a Streams és a Feladat ablaktáblát a MyFirstRunbook-Graphic lapra való visszatéréshez.
1. A runbook összes feladatának megtekintéséhez válassza a **Feladatok** az **Erőforrások**csoportban lehetőséget. A Feladatok lap felsorolja a runbook által létrehozott összes feladatot. Csak egy feladat jelenik meg a listában, mivel csak egyszer futtatta a feladatot.
1. Kattintson a feladat nevére, ha meg szeretné nyitni ugyanazt a feladat ablaktáblát, amelyet a runbook indításakor megtekintett. Ezen az ablaktáblán megtekintheti a runbookhoz létrehozott feladat részleteit.

## <a name="step-5---create-variable-assets"></a>5. lépés: Változó adategységek létrehozása

Tesztelte és közzétette a runbookot, de eddig nem tesz semmi hasznosat az Azure-erőforrások kezeléséhez. Mielőtt konfigurálná a runbookot a hitelesítéshez, létre kell hoznia egy változót az előfizetés-azonosító tárolására, be kell állítania egy tevékenységet a hitelesítéshez, majd hivatkoznia kell a változóra. Az előfizetési környezetre való hivatkozás sal egyszerűen dolgozhat több előfizetéssel.

1. Másolja az előfizetés azonosítóját a navigációs ablak **Előfizetések** beállításából.
1. Az Automation-fiókok lapon válassza a **Változók** lehetőséget a **Megosztott erőforrások csoportban.**
1. Válassza **a Változó hozzáadása**lehetőséget.
1. Az Új változó lapon tegye meg a következő beállításokat a megadott mezőkben.

    * **Név** - `AzureSubscriptionId`írja be .
    * **Érték** - adja meg az előfizetés-azonosítóját. 
    * **Típus** - tartsa a karakterláncot kiválasztva.
    * **Titkosítás** - használja az alapértelmezett értéket.
1. A változó létrehozásához kattintson a **Létrehozás** gombra.

## <a name="step-6---add-authentication"></a>6. lépés – Hitelesítés hozzáadása

Most, hogy rendelkezik egy változóaz előfizetés-azonosító, konfigurálhatja a runbook hitelesítésére a Futtatás másként hitelesítő adatok az előfizetéshez. Ehhez adja hozzá az Azure Run As kapcsolat eszközként. Hozzá kell adnia a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) parancsmagát és a [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) parancsmagát a vászonhoz.

>[!NOTE]
>A PowerShell runbookok, `Add-AzAccount` és `Connect-AzAccount` `Add-AzureRMAccount` aliasok. Vegye figyelembe, hogy ezek az aliasok nem érhetők el a grafikus runbookok. A grafikus runbookcsak `Connect-AzAccount`önmagát használhatja.

1. Nyissa meg a runbookot, és válassza a **Szerkesztés** lehetőséget a MyFirstRunbook-Graphic lapon.
1. Már nincs szükséged `Write Hello World to output` a bejegyzésre. Csak kattintson a három pontra, és válassza **a Törlés**lehetőséget.
1. A Könyvtár vezérlőben bontsa ki az **ASSETS**elemet, majd **a Kapcsolatok elemet.** A `AzureRunAsConnection` Vászonhoz adás a **Hozzáadás a vászonhoz**lehetőséget választva.
1. Nevezze `AzureRunAsConnection` át `Get Run As Connection`a névre.
1. A Könyvtár vezérlőmezőbe `Connect-AzAccount` írja be a keresőmezőbe.
1. Add `Connect-AzAccount` hozzá a vászonra.
1. Mutasson `Get Run As Connection` addig, amíg egy kör meg nem jelenik az alakzat alján. Kattintson a körre, `Connect-AzAccount` és húzza a nyilat a hivatkozás létrehozásához. A runbook `Get Run As Connection` kezdődik, `Connect-AzAccount`majd fut .<br> ![Hivatkozás létrehozása a tevékenységek között](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. A vásznon `Connect-AzAccount`válassza a lehetőséget. A Konfigurációvezérlő ablaktáblájába írja be **a Bejelentkezés az Azure-ba** mezőt a **Címke** mezőbe.
1. Kattintson **a Paraméterek gombra,** és megjelenik a Tevékenységparaméter konfigurációja lap.
1. A `Connect-AzAccount` parancsmag több paraméterkészlettel rendelkezik, és a paraméterértékek megadása előtt ki kell választania egyet. Kattintson **a Paraméterkészlet gombra,** majd válassza a **ServicePrincipalCertificateWithSubscriptionId lehetőséget.**
1. A paraméterkészlet paraméterei a Tevékenységparaméter konfigurációja lapon jelennek meg. Kattintson az **APPLICATIONID** elemre.<br> ![Azure-fiók paramétereinek hozzáadása](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. A Paraméter érték lapján tegye meg a következő beállításokat, majd kattintson az **OK**gombra.

   * **Adatforrás** - válassza **a Tevékenység kimenete**lehetőséget.
   * Adatforráslista – válassza **az Automation Connection beszereznie**lehetőséget.
   * **Mező elérési** útja `ApplicationId`- írja be. A mezőelérési út tulajdonságának nevét adja meg, mert a tevékenység több tulajdonsággal rendelkező objektumot ad ki.

1. Kattintson **a CERTIFICATETHUMBPRINT gombra,** majd a Paraméter értéke lapon tegye meg a következő beállításokat, majd kattintson az **OK**gombra.

    * **Adatforrás** - válassza **a Tevékenység kimenete**lehetőséget.
    * Adatforráslista – válassza **az Automation Connection beszereznie**lehetőséget.
    * **Mező elérési** útja `CertificateThumbprint`- írja be.
1. Kattintson a **SZERVIZÖSSZEG**gombra, majd a Paraméter értéke lapon válassza a **ConstantValue** elemet az **Adatforrás** mezőhöz; kattintson az **Igaz lehetőségre;** majd kattintson **az OK gombra.**
1. Kattintson **a TENANTID gombra,** és a Paraméter értéklapján a következő beállításokat hozza meg. Ha végzett, kattintson kétszer **az OK** gombra.

    * **Adatforrás** - válassza **a Tevékenység kimenete**lehetőséget. 
    * Adatforráslista – válassza **az Automation Connection beszereznie**lehetőséget.
    * **Mező elérési** útja `TenantId`- írja be. 
1. A Könyvtár vezérlőmezőbe `Set-AzContext` írja be a keresőmezőbe.
1. Add `Set-AzContext` hozzá a vászonra.
1. Válassza `Set-AzContext` ki a vásznon. A Konfiguráció vezérlőablakban `Specify Subscription Id` írja be a **Felirat** mezőt.
1. Kattintson **a Paraméterek gombra,** és megjelenik a Tevékenységparaméter konfigurációja lap.
1. A `Set-AzContext` parancsmag több paraméterkészlettel rendelkezik, és a paraméterértékek megadása előtt ki kell választania egyet. Kattintson **a Paraméterkészlet gombra,** majd válassza **a SubscriptionId**lehetőséget.
1. A paraméterkészlet paraméterei a Tevékenységparaméter konfigurációja lapon jelennek meg. Kattintson **az SubscriptionID**elemre.
1. A Paraméter érték lapján válassza a **Változó eszköz** az **adatforrás** mezőben, és válassza **az AzureSubscriptionId** a forráslista. Ha végzett, kattintson kétszer **az OK** gombra.
1. Mutasson `Login to Azure` addig, amíg egy kör meg nem jelenik az alakzat alján. Kattintson a körre, `Specify Subscription Id`és húzza a nyilat a számára. A runbook kell kinéznie a következő ezen a ponton.

    ![Forgatókönyv-hitelesítés konfigurálása](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>7. lépés – Virtuális gépet elindító tevékenység hozzáadása

Most hozzá kell `Start-AzVM` adnia egy tevékenységet a virtuális gép indításához. Az Azure-előfizetésben bármelyik virtuális gép kiválaszthat, és most a nevét a [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) parancsmagba kódolja.

1. A Könyvtár vezérlőmezőbe `Start-Az` írja be a keresőmezőbe.
2. Adja `Start-AzVM` hozzá a vászonhoz, majd `Specify Subscription Id`kattintson rá, és húzza alatta.
1. Mutasson `Specify Subscription Id` addig, amíg egy kör meg nem jelenik az alakzat alján. Kattintson a körre, `Start-AzVM`és húzza a nyilat a számára.
1. Válassza a(z) `Start-AzVM` lehetőséget. Kattintson **a Paraméterek,** majd a **Paraméterkészlet parancsra** a tevékenység készleteinek megtekintéséhez.
1. Válassza **a ResourceGroupNameParameterName paraméterkészletet.** A **ResourceGroupName** és **a Name** mezők mellett felkiáltójelek jelzik, hogy kötelező paraméterek. Ne feledje, hogy mindkét mező karakterláncértékeket vár.
1. Válassza ki a **Name** paramétert. Válassza a **PowerShell-kifejezést** az **Adatforrás** mezőhöz. A runbook elindításához használt virtuális géphez írja be a dupla idézőjelekkel körülvett gépnevet. Kattintson az **OK** gombra.
1. Válassza a **ResourceGroupName** elemet. Használja a **PowerShell-kifejezést** az **Adatforrás** mezőhöz, és írja be a dupla idézőjelekkel körülvett erőforráscsoport nevét. Kattintson az **OK** gombra.
1. Kattintson **a Teszt ablaktábla** elemre, hogy tesztelhesse a runbookot.
1. A teszt megkezdéséhez kattintson a **Start** gombra. Miután befejeződött, győződjön meg arról, hogy a virtuális gép elindult. A runbook kell kinéznie a következő ezen a ponton.

    ![Forgatókönyv-hitelesítés konfigurálása](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>8. lépés – További bemeneti paraméterek hozzáadása

A runbook jelenleg elindítja a virtuális gép az `Start-AzVM` erőforráscsoportban, amely a parancsmaghoz megadott. A runbook hasznosabb lesz, ha a runbook indításakor a nevet és az erőforráscsoportot is megadja. Adjunk hozzá bemeneti paramétereket a runbookhoz, hogy ezt a funkciót biztosítsuk.

1. Nyissa meg a grafikus szerkesztőt a MyFirstRunbook-Graphic lap **Szerkesztés** gombjára kattintva.
1. Válassza **a Bemenet és kimenet lehetőséget,** majd a Bemenet hozzáadása **lehetőséget** a Runbook bemeneti paraméter ablaktábla megnyitásához.
1. Tegye a következő beállításokat a megadott mezőkben, majd kattintson az **OK**gombra.
   * **Név** - `VMName`adja meg.
   * **Írja be** a - tartsa meg a karakterlánc-beállítást.
   * **Kötelező** - módosítsa az értéket **Igen**értékre.
1. Hozzon létre egy `ResourceGroupName` második kötelező bemeneti paramétert, majd kattintson az **OK** gombra a Bemeneti és kimeneti ablaktábla bezárásához.<br> ![Runbook bemeneti paraméterei](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Jelölje `Start-AzVM` ki a tevékenységet, majd kattintson **a Paraméterek gombra.**
1. Módosítsa a **Név** **forrásmezőjét** **Runbook bemenetté.** Ezután válassza **a VMName lehetőséget.**
1. Módosítsa a **ResourceGroupName** **adatforrásmezőjét** **Runbook bemenetre,** majd válassza a **ResourceGroupName lehetőséget.**<br> ![Start-AzVM paraméterek](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a teszt során használt két bemeneti változóhoz.
1. Zárja be a Teszt panelt.
1. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.
1. Állítsa le a korábban elindított virtuális gép.
1. Kattintson az **Indítás** gombra a forgatókönyv elindításához. Írja be az `VMName` `ResourceGroupName` értékeket, és a virtuális gép, hogy a start olni fog.
1. Amikor a runbook befejeződik, győződjön meg arról, hogy a virtuális gép elindult.

## <a name="step-9---create-a-conditional-link"></a>9. lépés – Feltételes hivatkozás létrehozása

Most már módosíthatja a runbookot, hogy csak akkor próbálja elindítani a virtuális gép, ha még nem indult el. Ehhez adjon hozzá egy [Get-AzVM-parancsmaszt,](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) amely lekéri a virtuális gép példányszintű állapotát. Ezután hozzáadhat egy PowerShell-munkafolyamat-kódmodult, amelyet egy PowerShell-kód kódrészlettel hívmeg `Get Status` annak megállapításához, hogy a virtuális gép állapota fut vagy leáll. A modul feltételes `Get Status` kapcsolata `Start-AzVM` csak akkor fut, ha az aktuális futó állapot le van állítva. Az eljárás végén a runbook a `Write-Output` parancsmag segítségével egy üzenetet, hogy tájékoztassa, ha a virtuális gép sikeresen elindult.

1. Nyissa meg a **MyFirstRunbook-Graphic-t** a grafikus szerkesztőben.
1. Távolítsa el `Specify Subscription Id` a `Start-AzVM` kapcsolatot, és kattintson rá, majd nyomja **le a Delete parancsot.**
1. A Könyvtár vezérlőmezőbe `Get-Az` írja be a keresőmezőbe.
1. Add `Get-AzVM` hozzá a vászonra.
1. Jelölje `Get-AzVM` ki a Select and Click **Parameter Set (Paraméterkészlet)** gombra a parancsmag készleteinek megtekintéséhez. 
1. Válassza ki a **GetVirtualMachineInResourceGroupNameParamSet** paraméterkészletet. A **ResourceGroupName** és **a Name** mezők mellett felkiáltójelek vannak, amelyek azt jelzik, hogy kötelező paramétereket adnak meg. Ne feledje, hogy mindkét mező karakterláncértékeket vár.
1. A Név **adatforrása**csoportban válassza a **Runbook-bevitel**lehetőséget, majd **a VMName**lehetőséget. **Data source** Kattintson az **OK** gombra.
1. A **ResourceGroupName** **adatforrás** csoportjában válassza a **Runbook-bemenet**lehetőséget, majd **a ResourceGroupName lehetőséget.** Kattintson az **OK** gombra.
1. Az **Állapot** **adatforrás** a csoportban válassza az **Állandó érték**lehetőséget, majd **az Igaz**lehetőséget. Kattintson az **OK** gombra.
1. Hivatkozás létrehozása `Specify Subscription Id` a `Get-AzVM`ból.
1. A Tár vezérlőben bontsa ki a **Runbook-vezérlőt,** és adja hozzá a **kódot** a vászonhoz.  
1. Hivatkozás létrehozása `Get-AzVM` a `Code`ból.  
1. Kattintson a Gombra, `Code` és a Konfiguráció ablaktáblán módosítsa a címkét Állapot **beírása értékre.**
1. Jelölje `Code` ki, és megjelenik a Kódszerkesztő lap.  
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

1. Hivatkozás létrehozása `Get Status` a `Start-AzVM`ból.

    ![Forgatókönyv kódmodullal](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Jelölje ki a hivatkozást, és a Konfiguráció ablaktáblán módosítsa az **Alkalmazás feltételt** **Igen**-re. Vegye figyelembe, hogy a hivatkozás szaggatott vonallá válik, ami azt jelzi, hogy a céltevékenység csak akkor fut, ha a feltétel igaz lesz.  
1. A Feltételkifejezés `$ActivityOutput['Get Status'] -eq "Stopped"`mezőbe írja be a következőt: .For Condition expression **(Feltételkifejezés)** mezőbe írja `Start-AzVM`most csak akkor fut, ha a virtuális gép le van állítva.
1. A Könyvtár vezérlőben bontsa ki a **Parancsmagok** elemet, és válassza a **Microsoft.PowerShell.Utility** lehetőséget.
1. Add `Write-Output` hozzá a vásznon kétszer.
1. Az első `Write-Output` vezérlőhöz kattintson a **Paraméterek** elemre, és módosítsa a **Címke** értékét **A virtuális gép indítása értesítése**értékre.
1. Az **InputObject**esetében módosítsa **az adatforrást** **PowerShell-kifejezésre**, és írja be a kifejezést. `$VMName successfully started.`
1. A második `Write-Output` vezérlőn kattintson a **Paraméterek gombra,** és módosítsa a **Címke** értékét **A virtuális gép indítása sikertelen**értesítésértékre.
1. Az **InputObject**esetében módosítsa **az adatforrást** **PowerShell-kifejezésre**, és írja be a kifejezést. `$VMName could not start`
1. Hivatkozások létrehozása `Start-AzVM` `Notify VM Started` a `Notify VM Start Failed`és a csoportból.
1. Jelölje ki `Notify VM Started` a hivatkozást, és módosítsa **a Feltétel alkalmazása igaz értékre.**
1. A **Feltétel kifejezéshez**írja be a következőt: `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`. Ez `Write-Output` a vezérlő most már csak akkor fut, ha a virtuális gép sikeresen elindul.
1. Jelölje ki `Notify VM Start Failed` a hivatkozást, és módosítsa **a Feltétel alkalmazása igaz értékre.**
1. A **Feltételkifejezés** mezőbe `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`írja be a következőt: a . Ez `Write-Output` a vezérlő most már csak akkor fut, ha a virtuális gép nem sikerült elindítani. A runbook nak az alábbi hoz hasonlónak kell lennie.

    ![Forgatókönyv Write-Output parancsmaggal](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt.
1. Indítsa el a runbookot a virtuális gép leállításával, és a gépnek el kell indulnia.

## <a name="next-steps"></a>További lépések

* A grafikus szerzői jogról további információ: [Grafikus szerzői jog az Azure Automationben.](automation-graphical-authoring-intro.md)
* A PowerShell-forgatókönyvek használatának megismeréséhez tekintse meg a következőt: [Az első PowerShell-runbookom](automation-first-runbook-textual-powershell.md).
* A PowerShell-munkafolyamat-runbookok első [lépései: Az első PowerShell-munkafolyamat-runbook.](automation-first-runbook-textual.md)
* A PowerShell-parancsmag referencia, lásd: [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).