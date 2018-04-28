---
title: Az első grafikus forgatókönyvnek az Azure Automationben
description: Ez az oktatóanyag bemutatja egy egyszerű grafikus forgatókönyv létrehozását, tesztelését és közzétételét.
keywords: runbook, runbook-sablon, runbook automation, azure runbook
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/13/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6893d3c79a5f827f214b12ce1dc5f5af7bbc2891
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="my-first-graphical-runbook"></a>Az első grafikus forgatókönyvem

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Egy Azure Automation [grafikus forgatókönyv](automation-runbook-types.md#graphical-runbooks) létrehozását bemutató oktatóanyag. Az első lépés egy egyszerű runbook tesztelése és közzététele, miközben elsajátítja a runbookfeladat állapotának nyomon követését is. Ezután módosítja a runbookot, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure-beli virtuális gépet. Végül az oktatóanyag befejezéseként még robusztusabbá teszi a runbookot: runbookparamétereket és feltételes hivatkozásokat ad hozzá.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Ezt a gépet leállítja és elindítja, tehát ne olyan virtuális gépet használjon, amely élesben működik.

## <a name="create-runbook"></a>Runbook létrehozása

Először egy egyszerű runbookot hozunk létre, amelynek a kimenete a *Hello World* szöveg.

1. Az Azure Portalon nyissa meg az Automation-fiókját.

   Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg rendelkezik már adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.

2. Kattintson a **Runbookok** elemre a **FOLYAMATKEZELÉS** területen a runbookok listájának megnyitásához.
3. Hozzon létre egy új runbookot kiválasztásával **+ Hozzáadás runbook**, majd kattintson a **hozzon létre egy új runbookot**.
4. Adja a forgatókönyvnek a *MyFirstRunbook-Graphical* nevet.
5. Ebben az esetben egy [grafikus runbookot](automation-graphical-authoring-intro.md) hoz létre, ezért a **Forgatókönyv típusának** válassza a **Grafikus** lehetőséget.<br> ![Új runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. A forgatókönyv létrehozásához és a grafikus szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="add-activities"></a>Tevékenységek hozzáadása

A szerkesztő bal oldalán levő Könyvtárvezérlés segítségével kiválaszthatja a forgatókönyvhöz hozzáadni kívánt tevékenységeket. A forgatókönyvhöz hozzáad egy **Write-Output** írási-olvasási parancsmagot is, hogy a runbook szöveget adjon ki.

1. A Könyvtárvezérlésben kattintson a keresési szövegmezőbe, és írja be a **Write-Output** kifejezést. A keresés eredményei az alábbi ábrán láthatók: <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Görgessen le a lista aljához. Kattintson a **Write-Output** elemre, és válassza a **Hozzáadás a vászonhoz** lehetőséget, vagy kattintson a parancsmag melletti három pontra, és válassza a **Hozzáadás a vászonhoz** lehetőséget.
1. A vásznon kattintson a **Write-Output** tevékenységre. Ez a művelet megnyitja a konfigurációs vezérlő lapon, amely lehetővé teszi a tevékenység konfigurálása.
1. A **Címke** értéke alapértelmezés szerint a parancsmag neve, de módosíthatja rövidebbre is. Módosítsa a következőre: *Hello World megjelenítése a kimenetben*.
1. A **Paraméterek** elemre kattintva megadhatja a parancsmag paramétereinek értékét.

   Bizonyos parancsmagok több paraméterkészlettel rendelkeznek, Önnek pedig ki kell választania, melyiket fogja használni. Ebben az esetben a **Write-Output** parancsmag csak egy paraméterkészlettel rendelkezik, ezért nem kell választania.

1. Jelölje ki az **InputObject** paramétert. Ez az a paraméter, ahol megadhatja a kimeneti streambe küldendő szöveget.
1. Az **Adatforrások** legördülő menüben válassza a **PowerShell kifejezés** lehetőséget. Az **Adatforrás** legördülő menüben paraméterértékek kitöltésére használt különböző forrásokat talál.

   Használhatja ilyen források (pl. egy másik tevékenység, egy Automation-adategység vagy egy PowerShell-kifejezés) kimenetét. Ebben az esetben a kimenet csak a *Hello World* szöveg lesz. Megadhat egy karakterláncot egy PowerShell-kifejezéssel is.<br>

1. A **Kifejezés** mezőbe írja be a *„Hello World”* kifejezést, majd kattintson az **OK** gombra kétszer a vászonra való visszatéréshez.
1. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="test-the-runbook"></a>A runbook tesztelése

Mielőtt közzéteszi a runbookot, hogy éles üzemben is elérhető legyen, tesztelnie kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Válassza ki **teszt ablaktábla** teszt lapjának megnyitásához.
1. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
1. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), az állapota pedig megjelenik a panelen.

   A feladat állapota kezdetben *Várólistán*. Ez azt jelöli, hogy egy felhőben lévő runbook-feldolgozó elérhetővé válására vár. Ezután *Indítás* állapotúra változik, ha egy feldolgozó elvállalja a feladatot, majd *Fut* állapotúra, amikor a forgatókönyv elkezd futni.

1. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Ebben az esetben a *Hello World* szöveg jelenik meg.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Zárja be a tesztoldalt térjen vissza a vászonra.

## <a name="publish-and-start-the-runbook"></a>Közzététele, és elindítja a runbookot

A létrehozott runbook még mindig Piszkozat módban van. Az üzemi környezetben való futtatás előtt közzé kell tennünk. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nincs Közzétett verzió, mivel még csak most hozta létre a runbookot.

1. Válassza ki **közzététel** közzétenni a runbookot, majd **Igen** megjelenésekor.
1. Ha balra a runbook megtekintése a **Runbookok** lapon, akkor megjelenik egy **szerzői állapot** a **közzétett**.
1. Vissza a lap megtekintéséhez görgessen **MyFirstRunbook grafikus**.

   A felül látható lehetőségekkel elindíthatjuk a forgatókönyvet, ütemezhetjük egy későbbi időpontban való indításra, vagy létrehozhatunk egy [webhookot](automation-webhooks.md), amely segítségével elindítható a forgatókönyv egy HTTP-hívással.

1. Válassza ki **Start** , majd **Igen** amikor a rendszer kéri a runbook elindításához.
1. A runbook-feladat létrehozott egy feladat lap van megnyitva. Győződjön meg arról, hogy a **Feladat állapota** értéke **Befejezve**.
1. Ha a forgatókönyv a *Befejezve* állapotot mutatja, kattintson a **Kimenet** lehetőségre. A **kimeneti** lap van megnyitva, és megtekintheti a *Hello World* a panelen.
1. Zárja be a kimeneti lapot.
1. Kattintson a **összes napló** a runbook-feladat az adatfolyamok lapjának megnyitásához. A kimeneti streamben csak a *Hello World* eredményt látja, de itt megjelenhetnek egyéb streamek is egy runbook-feladatból, mint például a Részletes vagy a Hiba, ha a forgatókönyv ezekbe ír.
1. Zárja be az összes Naplók lapján, a feladat lapon a MyFirstRunbook grafikus lapra való visszatéréshez.
1. Összes a runbookhoz tartozó feladatok zárja be a **feladat** lapon, és válassza **feladatok** alatt **erőforrások**. Ez felsorolja az összes, a runbook által létrehozott feladatot. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtatta a feladatot.
1. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már látott a runbook elindításakor. Ez lehetővé teszi, hogy az időben visszamenve megtekintse egy adott forgatókönyvhöz létrehozott összes feladat részleteit.

## <a name="create-variable-assets"></a>Változó eszközök létrehozása

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Mielőtt konfigurálná a runbookot a hitelesítésre, létre fog hozni egy változót, amely tárolja az előfizetés-azonosítót, és hivatkozni fog rá, miután beállította a tevékenységet hitelesítésre az alábbi 6. lépésben. Ha hivatkozást szerepeltet az előfizetési környezetben, könnyebben használhat több előfizetést. A továbbhaladás előtt másolja ki az előfizetés-azonosítót a navigációs ablaktábla előfizetés-beállításai közül.

1. Automation-fiókok lapján válassza **változók** alatt **megosztott erőforrások**.
1. Válassza ki **változó hozzáadása**.
1. Az új változó lapon a a **neve** adja meg a **AzureSubscriptionId** és a a **érték** mezőben adja meg az előfizetés-azonosító. A **Típus** maradjon **Karakterlánc**, és tartsa meg a *Titkosítás* alapértelmezett értékét.
1. A változó létrehozásához kattintson a **Létrehozás** gombra.

## <a name="add-authentication"></a>Hitelesítés hozzáadása

Most, hogy van egy változója az előfizetés-azonosító tárolására, úgy konfigurálhatja a runbookot, hogy megtörténjen a hitelesítés a Futtató hitelesítő adatokkal, amelyek az [előfeltételek](#prerequisites) között szerepelnek. Az Azure-beli futtató kapcsolat hozzáadása ehhez **eszköz** és **Connect-AzureRmAccount** parancsmag a vászonra.

1. Lépjen vissza a runbookot, és válassza ki **szerkesztése** MyFirstRunbook grafikus lapon.
1. Nincs szükség a **írási Hello World kimeneti** többé, így kattintson folytatást jelző pontokra (...), és válassza **törlése**.
1. A Könyvtár vezérlőben bontsa ki az **ADATEGYSÉGEK**, **Kapcsolatok** lehetőséget, és a **Hozzáadás a vászonhoz** lehetőség kiválasztásával adja hozzá a vászonhoz az **AzureRunAsConnection** elemet.
1. Írja be a könyvtár vezérlő **Connect-AzureRmAccount** a keresési szövegmezőben.
1. Adja hozzá **Connect-AzureRmAccount** a vászonra.
1. Vigye a kurzort a **Futtató kapcsolat létesítése** fölé, és várja meg, amíg megjelenik az alakzat alján egy kör. A kör kattintással és húzással vigye a nyílra kattintva **Connect-AzureRmAccount**. A létrehozott nyíl egy *hivatkozás*. A runbook kezdődik-e **beolvasása Futtatás mint kapcsolat** , majd futtassa **Connect-AzureRmAccount**.<br> ![Hivatkozás létrehozása a tevékenységek között](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. A vásznon, válassza ki a **Connect-AzureRmAccount** és a konfigurációs vezérlőtípus ablaktáblán **Azure bejelentkezési** a a **címke** szövegmező.
1. Kattintson a **paraméterek** és a tevékenység-paraméter konfiguráció lap jelenik meg.
1. **Connect-AzureRmAccount** paraméterkészletek, rendelkezik, ezért ki kell választania egy előtt megadhatja a paraméterértékek. Kattintson a **Paraméterkészlet** lehetőségre, és válassza a **ServicePrincipalCertificate** paraméterkészletet.
1. A paraméterhalmaz kiválasztása után a paramétereket a tevékenység-paraméter konfigurálása lapon jelennek meg. Kattintson az **APPLICATIONID** elemre.<br> ![Azure RM-fiók paramétereinek hozzáadása](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Paraméterérték lapján válassza **tevékenység kimeneti** a a **adatforrás** válassza ki **beolvasása Futtatás mint kapcsolat** a listáról, az a **mező elérési útja** szövegmező típus **ApplicationId**, és kattintson a **OK**. A mező elérési útjához tartozó tulajdonság nevét azért adja meg, mert a tevékenység több tulajdonsággal rendelkező objektumot eredményez.
1. Kattintson a **CERTIFICATETHUMBPRINT**, és a paraméter értéke lapon válassza **tevékenység kimeneti** a a **adatforrás**. Válassza a **Futtató kapcsolat létesítése** elemet a listáról, és a **Mező elérési útja** szövegmezőbe írja be a **CertificateThumbprint** kifejezést, majd kattintson az **OK** gombra.
1. Kattintson a **szolgáltatásnév**, és a paraméter értéke lapon válassza **ConstantValue** a a **adatforrás**, kattintson a lehetőségre **igaz**, majd **OK**.
1. Kattintson a **TENANTID**, és a paraméter értéke lapon válassza **tevékenység kimeneti** a a **adatforrás**. Válassza a **Futtató kapcsolat létesítése** elemet a listáról, és a **Mező elérési útja** szövegmezőbe írja be a **TenantId** kifejezést, majd kattintson kétszer az **OK** elemre.
1. A Könyvtár vezérlőben írja be a keresési szövegmezőbe a következőt: **Set-AzureRmContext**.
1. Adja hozzá a vászonhoz a **Set-AzureRmContext** elemet.
1. A vásznon válassza ki a **Set-AzureRmContext** elemet, és a Konfiguráció vezérlőpanelen írja be az **Előfizetés azonosítójának megadása** mondatot a **Címke** szövegmezőbe.
1. Kattintson a **paraméterek** és a tevékenység-paraméter konfiguráció lap jelenik meg.
1. Az **Set-AzureRmContext** több paraméterkészlettel rendelkezik, ezért ki kell választania egyet, mielőtt megadhatná a paraméterértékeket. Kattintson a **Paraméterkészlet** lehetőségre, és válassza a **SubscriptionId** paraméterkészletet.
1. A paraméterhalmaz kiválasztása után a paramétereket a tevékenység-paraméter konfigurálása lapon jelennek meg. Kattintson a **SubscriptionID** elemre.
1. A paraméter értéke lapon válassza **Változóeszköz** a a **adatforrás** válassza ki **AzureSubscriptionId** a listában, és kattintson a **OK** kétszer.
1. Vigye a kurzort a **Bejelentkezés az Azure-ba** fölé, és várja meg, amíg megjelenik az alakzat alján egy kör. Kattintson a körre, és húzza a nyilat az **Előfizetés azonosítójának megadása** elemre.

> [!IMPORTANT]
> **Connect-AzureRmAccount** alias már **Connect-AzureRMAccount**. Amikor keresést a könyvtár elemek, ha nem látja **Connect-AzureRMAccount**, használhat **Connect-AzureRmAccount**, vagy frissítheti a modulok az Automation-fiókban.

A forgatókönyvnek ezen a ponton az alábbi kódhoz kell hasonlítania: <br>![Forgatókönyv-hitelesítés konfigurálása](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="add-activity-to-start-a-vm"></a>A virtuális gép elindításához tevékenység hozzáadása

Most hozzáad egy **Start-AzureRmVM** tevékenységet, amellyel egy virtuális gépet indít el. Válassza ki az Azure-előfizetésében lévő bármelyik virtuális gépet, amelynek a nevét ideiglenesen szoftveresen rögzíti a parancsmagba.

1. A Könyvtár vezérlőben írja be a keresési szövegmezőbe a következőt: **Start-AzureRm**.
2. Adja hozzá a **Start-AzureRmVM** elemet a vászonhoz, és húzza az **Előfizetés azonosítójának megadása** alá.
1. Vigye a kurzort a **Előfizetés azonosítójának megadása** fölé, és várja meg, amíg megjelenik az alakzat alján egy kör. Kattintson a körre, és húzza a nyilat a **Start-AzureRmVM** elemre.
1. Jelölje ki a **Start-AzureRmVM** elemet. A **Start-AzureRmVM** készleteinek megtekintéséhez kattintson a **Paraméterek**, majd a **Paraméterkészlet** lehetőségre. Válassza ki a **ResourceGroupNameParameterSetName** paraméterkészletet. A **ResourceGroupName** és a **Name** mellett felkiáltójel van. Ez azt jelzi, hogy ezek kötelező paraméterek. Azt is észreveheti, hogy mindkét helyen szöveges értéket kell megadni.
1. Válassza ki a **Name** paramétert. Válassza ki a **PowerShell-kifejezés** elemet az **Adatforrás** területen, majd írja be idézőjelek között a runbookkal elindított virtuális gép nevét. Kattintson az **OK** gombra.
1. Válassza a **ResourceGroupName** elemet. Használja a **PowerShell-kifejezés** elemet az **Adatforrás** mezőben, majd írja be az erőforráscsoport nevét idézőjelek között. Kattintson az **OK** gombra.
1. Kattintson a Teszt panelre, hogy tesztelhesse a runbookot.
1. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, ellenőrizze, hogy a virtuális gép elindult-e.

A forgatókönyvnek ezen a ponton az alábbi kódhoz kell hasonlítania: <br>![Forgatókönyv-hitelesítés konfigurálása](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="add-additional-input-parameters"></a>Adja meg a további bemeneti paramétereit

A runbook jelenleg a **Start-AzureRmVM** parancsmagban meghatározott erőforráscsoport virtuális gépét indítja el. A runbook hasznosabb lenne, ha megadhatnánk, hogy mikor induljon. Most olyan bemeneti paramétereket fog hozzáadni a runbookhoz, amelyek biztosítják ezt a funkciót.

1. Nyissa meg a grafikus szerkesztő kattintva **szerkesztése** a a **MyFirstRunbook grafikus** ablaktáblán.
1. Válassza ki **bemeneti és kimeneti** , majd **bemenet hozzáadása** Runbook bemeneti paraméter ablaktábla megnyitása.
1. A**Name** paraméterhez adja meg a *VMNAme* értéket. A **Típus** maradjon *karakterlánc*, de a **Kötelező** értékét módosítsa arra, hogy *Igen*. Kattintson az **OK** gombra.
1. Hozzon létre egy második kötelező bemeneti paramétert *ResourceGroupName* néven, majd kattintson az **OK** gombra a **Bemenet és kimenet** panelen.<br> ![Runbook bemeneti paraméterei](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Válassza a **Start-AzureRmVM** tevékenységet, majd kattintson a **Paraméterek** elemre.
1. A **Name** paraméter **Adatforrás** elemét módosítsa a **Forgatókönyv-bemenet** beállításra, majd válassza a **VMName** lehetőséget.
1. A **ResourceGroupName** paraméternél az **Adatforrás** beállítást módosítsa a **Forgatókönyv-bemenet** értékre, majd válassza a **ResourceGroupName** lehetőséget.<br> ![Start-AzureVM paraméterei](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a teszt során használt két bemeneti változóhoz.
1. Zárja be a Teszt panelt.
1. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.
1. Állítsa le az előző lépésben elindított virtuális gépet.
1. Kattintson az **Indítás** gombra a forgatókönyv elindításához. Írja be a **VMName** és a **ResourceGroupName** értéket az elindítani kívánt virtuális géphez.
1. Ha a forgatókönyv kész, ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="create-a-conditional-link"></a>Hozzon létre egy feltételes hivatkozás

Most úgy módosítja a runbookot, hogy csak akkor próbálja meg elindítani a virtuális gépet, ha még nem indult el. Ennek érdekében hozzáadjuk a **Get-AzureRmVM** parancsmagot a runbookhoz. Ez lekéri a virtuális gép példányszintű állapotát. Ezután adjon hozzá egy **Állapot kérése** nevű PowerShell-munkafolyamati kódmodult egy PowerShell-kódrészlettel, amely meghatározza, hogy a virtuális gép fut-e vagy le van állítva. Az **Állapot lekérése** modulból származó feltételes hivatkozás csak akkor futtatja a **Start-AzureRmVM** tevékenységet, ha a jelenlegi, futó állapot helyett a gép le van állítva. Végül a PowerShell Write-Output parancsmag használatával megjelenő üzenet tájékoztatja, hogy a virtuális gép sikeresen elindult-e.

1. Nyissa meg **MyFirstRunbook grafikus** grafikus-szerkesztőben.
1. Távolítsa el az **Előfizetés azonosítójának megadása** és a **Start-AzureRmVM** közötti hivatkozást. Ehhez kattintson rá, majd nyomja le a *Delete* billentyűt.
1. A Könyvtár vezérlőben írja be a keresési szövegmezőbe a következőt: **Get-AzureRm**.
1. Adja hozzá a vászonhoz a **Get-AzureRmVM** elemet.
1. A **Get-AzureRmVM** készleteinek megtekintéséhez válassza a **Get-AzureRmVM**, majd a **Paraméterkészlet** lehetőséget. Válassza ki a **GetVirtualMachineInResourceGroupNameParamSet** paraméterkészletet. A **ResourceGroupName** és a **Name** mellett felkiáltójel van. Ez azt jelzi, hogy ezek kötelező paraméterek. Azt is észreveheti, hogy mindkét helyen szöveges értéket kell megadni.
1. A **Name** paraméterhez tartozó **Adatforrás** elemét módosítsa a **Forgatókönyv bemenet** beállításra, majd válassza a **VMName** lehetőséget. Kattintson az **OK** gombra.
1. A **ResourceGroupName** paraméterhez tartozó **Adatforrás** elemét módosítsa a **Forgatókönyv bemenet** beállításra, majd válassza a **ResourceGroupName** lehetőséget. Kattintson az **OK** gombra.
1. A **Status** paraméterhez tartozó **Adatforrás** elemét módosítsa a **Konstans érték** beállításra, majd kattintson az **Igaz** gombra. Kattintson az **OK** gombra.
1. Hozzon létre egy hivatkozást az **Előfizetés azonosítójának megadása** és a **Get-AzureRmVM** között.
1. A Könyvtár vezérlőben bontsa ki a **Forgatókönyv vezérlése** lehetőséget, és adja hozzá a vászonhoz az **Code** elemet.  
1. Hozzon létre egy hivatkozást a **Get-AzureRmVM** és a **Code** között.  
1. Kattintson a **Code** elemre, majd a Konfiguráció panelen módosítsa a címkét a következőre: **Állapot kérése**.
1. Válassza ki **kód** paramétert, és a **kód szerkesztése** lap jelenik meg.  
1. A kódszerkesztőben illessze be az alábbi kódrészletet.

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Hozzon létre egy hivatkozást az **Állapot kérése** és a **Start-AzureRmVM** között.<br> ![Runbook kódmodullal](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Jelölje ki a hivatkozást, majd a Konfiguráció panelen módosítsa a **Feltétel alkalmazása** értékét ara, hogy **Igen**. Figyelje meg, hogy a hivatkozás egy szaggatott vonallá változik, amely azt jelzi, hogy a céltevékenység csak akkor fut, ha a feltétel igazzá válik.  
1. A **Feltételkifejezés** területen írja be a következőt: *$ActivityOutput['Get Status'] -eq "Stopped"*. A **Start-AzureRmVM** most csak akkor fog futni, ha a virtuális gép le van állítva.
1. A Könyvtár vezérlőben bontsa ki a **Parancsmagok** elemet, és válassza a **Microsoft.PowerShell.Utility** lehetőséget.
1. Adja hozzá a vászonhoz kétszer a következőt: **Write-Output**.
1. Az első **Write-Output** vezérlőn kattintson a **Paraméterek** elemre, és módosítsa a **Címke** értéket a következőre: *Értesítés a virtuális gép indulásáról*.
1. Az **InputObject** elemnél módosítsa az **Adatforrás** beállítását a **PowerShell-kifejezés** értékre, és írja be a következő kifejezést:*„$VMName sikeresen elindult.”*.
1. A második **Write-Output** vezérlőn kattintson a **Paraméterek** elemre, és módosítsa a **Címke** értéket a következőre: *Értesítés a virtuális gép indulásának meghiúsulásáról*
1. Az **InputObject** elemnél módosítsa az **Adatforrás** beállítását a **PowerShell-kifejezés** értékre, és írja be a következő kifejezést:*„VMName nem tudott elindulni.”*.
1. Hozzon létre egy hivatkozást a **Start-AzureRmVM** és az **Értesítés a virtuális gép indulásáról**, valamint az **Értesítés a virtuális gép indulásának meghiúsulásáról** között.
1. Válassza ki az **Értesítés a virtuális gép indulásáról** felé mutató hivatkozást, és módosítsa a **Feltétel alkalmazása** beállítást arra, hogy **Igaz**.
1. A **Feltételkifejezéshez** írja be a következőt: *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*. A Write-Output vezérlés most csak akkor fog futni, ha a virtuális gép sikeresen elindult.
1. Válassza ki az **Értesítés a virtuális gép indulásának meghiúsulásáról** felé mutató hivatkozást, és módosítsa a **Feltétel alkalmazása** beállítást arra, hogy **Igaz**.
1. A **Feltételkifejezéshez** írja be a következőt: *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*. A Write-Output vezérlés most csak akkor fog futni, ha a virtuális gép sikeresen elindult. A runbook a következő kép hasonlóan kell kinéznie: <br> ![Runbook Write-Output parancsmaggal](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt.
1. Indítsa el a forgatókönyvet úgy, hogy a virtuális gép nem fut, és elvileg el kell indulnia.

## <a name="next-steps"></a>További lépések

* További információk a Grafikus létrehozásról: [Grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md).
* A PowerShell-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-forgatókönyvem](automation-first-runbook-textual-powershell.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)

