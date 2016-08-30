<properties
    pageTitle="Az első Azure Automation grafikus forgatókönyvem | Microsoft Azure"
    description="Ez az oktatóanyag bemutatja egy egyszerű grafikus forgatókönyv létrehozását, tesztelését és közzétételét."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="runbook, runbook template, runbook automation, azure runbook"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/31/2016"
    ms.author="magoedte;bwren"/>

# Az első grafikus forgatókönyvem

> [AZURE.SELECTOR] - [Grafikus](automation-first-runbook-graphical.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md) - [PowerShell munkafolyamat](automation-first-runbook-textual.md)

Egy Azure Automation [grafikus forgatókönyv](automation-runbook-types.md#graphical-runbooks) létrehozását bemutató oktatóanyag.  Egy egyszerű forgatókönyvvel fogunk kezdeni, amelyet tesztelünk és közzé is teszünk, és bemutatjuk a forgatókönyv állapotának nyomon követését is.  Ezután módosítjuk a forgatókönyvet, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure virtuális gépet.  Ezután még robusztusabbá tesszük a forgatókönyvet: forgatókönyv-paramétereket és feltételes hivatkozásokat adunk hozzá.

## Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége.

-   Egy Azure-előfizetés.  Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve <a href="/pricing/free-account/" target="_blank">[regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
-   [Azure-beli futtató fiók](automation-sec-configure-azure-runas-account.md) a forgatókönyv tárolásához és az Azure-erőforrásokban való hitelesítéshez.  Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
-   Egy Azure virtuális gép.  Ezt a gépet leállítjuk és elindítjuk, tehát ne legyen éles használatban.


## 1. lépés – Új forgatókönyv létrehozása

Először egy egyszerű forgatókönyvet hozunk létre, amely a *Hello World* szöveget adja vissza.

1.  Az Azure portálon nyissa meg az Automation-fiókját.  
    Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról.  Valószínűleg rendelkezik már adategységekkel.  Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul.  Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.
2.  Kattintson a **Forgatókönyvek** csempére a forgatókönyvek listájának megnyitásához.<br> ![Forgatókönyvek szabályozása](media/automation-first-runbook-graphical/runbooks-control.png)
3.  Hozzon létre egy új forgatókönyvet a **Forgatókönyv hozzáadása**, majd az **Új forgatókönyv létrehozása** elemre kattintva.
4.  Adja a forgatókönyvnek a *MyFirstRunbook-Graphical* nevet.
5.  Ebben az esetben egy [grafikus forgatókönyvet](automation-graphical-authoring-intro.md) hozunk létre, ezért a **Forgatókönyv típusának** válassza a **Grafikus** lehetőséget.<br> ![Új forgatókönyv](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6.  A forgatókönyv létrehozásához és a grafikus szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## 2. lépés – Tevékenységek hozzáadása a forgatókönyvhöz

A szerkesztő bal oldalán levő Könyvtárvezérlés segítségével kiválaszthatja a forgatókönyvhöz hozzáadni kívánt tevékenységeket.  A forgatókönyvhöz hozzáadunk egy **Write-Output** írási-olvasási parancsmagot is, hogy szöveget adjon ki a forgatókönyv.

1.  A Könyvtárvezérlésben kattintson a keresési szövegmezőbe, és írja be a **Write-Output** kifejezést.  A keresési eredmények megjelennek alul. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
2.  Görgessen le a lista aljához.  Kattintson a **Write-Output** elemre, és válassza a **Hozzáadás a vászonhoz** lehetőséget, vagy kattintson a parancsmag melletti három pontra, és válassza a **Hozzáadás a vászonhoz** lehetőséget.
3.  A vásznon kattintson a **Write-Output** tevékenységre.  Ez megnyitja a Konfiguráció vezérlőpanelt, amely lehetővé teszi a tevékenység konfigurálását.
4.  A **Címke** értéke alapértelmezés szerint a parancsmag neve, de módosíthatjuk valami barátságosabbra. Módosítsa a következőre: *Hello World megjelenítése a kimenetben*.
5.  A **Paraméterek** elemre kattintva megadhatja a parancsmag paramétereinek értékét.  
    Bizonyos parancsmagok több paraméterkészlettel rendelkeznek, és ki kell választania, melyiket fogja használni. Ebben az esetben a **Write-Output** parancsmag csak egy paraméterkészlettel rendelkezik, ezért nem kell választania. <br> ![A Write-Output tulajdonságai](media/automation-first-runbook-graphical/write-output-properties-b.png)
6.  Jelölje ki az **InputObject** paramétert.  Ez az a paraméter, ahol megadjuk a kimeneti streambe küldendő szöveget.
7.  Az **Adatforrások** legördülő menüben válassza a **PowerShell kifejezés** lehetőséget.  Az **Adatforrás** legördülő menüben paraméterértékek kitöltésére használt különböző forrásokat talál.  
    Használhatja ilyen források (pl. egy másik tevékenység, egy Automation-adategység vagy egy PowerShell-kifejezés) kimenetét.  Ebben az esetben egyszerűen a *Hello World* szöveget akarjuk megjeleníteni. Megadhatunk egy karakterláncot egy PowerShell-kifejezéssel is.
8.  A **Kifejezés** mezőbe írja be a *„Hello World”* kifejezést, majd kattintson az **OK** gombra kétszer a vászonra való visszatéréshez.<br> ![PowerShell-kifejezés](media/automation-first-runbook-graphical/expression-hello-world.png)
9.  A **Mentés** gombra kattintva mentse el a forgatókönyvet.<br> ![Forgatókönyv mentése](media/automation-first-runbook-graphical/runbook-toolbar-save-revised20165.png)

## 3. lépés – A forgatókönyv tesztelése

Mielőtt közzétesszük a forgatókönyvet, hogy éles üzemben is elérhető legyen, tesztelnünk kell, hogy biztosan jól működik-e.  Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1.  Kattintson a **Teszt panelre** a Teszt panel megnyitásához.<br> ![Teszt panel](media/automation-first-runbook-graphical/runbook-toolbar-test-revised20165.png)
2.  Kattintson az **Indítás** gombra a teszt elindításához.  Elvileg ez az egyetlen engedélyezett lehetőség.
3.  Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), az állapota pedig megjelenik a panelen.  
    A feladat állapota kezdetben *Várólistán*, azt mutatva, hogy egy felhőben lévő forgatókönyv-feldolgozó elérhetővé válására vár.  Ezután *Indítás* állapotúra változik, ha egy feldolgozó elvállalja a feladatot, majd *Fut* állapotúra, amikor a forgatókönyv elkezd futni.  
4.  Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. A mi esetünkben ez a következő: *Hello World*.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
5.  A vászonra való visszatéréshez zárja be a Teszt panelt.

## 4. lépés: Közzététel és a forgatókönyv indítása

A létrehozott forgatókönyv még mindig Piszkozat módban van. Az üzemi környezetben való futtatás előtt közzé kell tennünk.  Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal.  Az esetünkben még nincs Közzétett verzió, mivel még csak most hoztuk létre a forgatókönyvet.

1.  A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.<br> ![Közzététel](media/automation-first-runbook-graphical/runbook-toolbar-publish-revised20166.png)
2.  Ha balra görgetve megtekinti a forgatókönyvet a **Forgatókönyvek** panelen, a **Közzétett** **Szerzői állapot** jelenik meg.
3.  Görgessen vissza jobbra a **MyFirstRunbook** panel megtekintéséhez.  
    A felül látható lehetőségekkel elindíthatjuk a forgatókönyvet, ütemezhetjük egy későbbi időpontban való indításra, vagy létrehozhatunk egy [webhookot](automation-webhooks.md), amely segítségével elindítható a forgatókönyv egy HTTP-hívással.
4.  Most csak elindítani szeretnénk a forgatókönyvet, tehát kattintson az **Indítás**, majd az **Igen** gombra, amikor a rendszer erre kéri.<br> ![Forgatókönyv indítása](media/automation-first-runbook-graphical/runbook-controls-start-revised20165.png)
5.  Megnyílik egy feladatpanel az imént létrehozott forgatókönyv-feladathoz.  Ezt a panelt bezárhatjuk, de ebben az esetben nyitva hagyjuk, hogy lássuk a feladat előrehaladását.
6.  A feladat állapota a **Feladat összegzése** területen látszik, és megfelel a korábban, a forgatókönyv tesztelésekor látott állapotoknak.<br> ![Feladat összegzése](media/automation-first-runbook-graphical/runbook-job-summary.png)
7.  Ha a forgatókönyv a *Befejezve* állapotot mutatja, kattintson a **Kimenet** lehetőségre. A **Kimenet** panel megnyílik, és megjelenik a *Hello World* a panelen.<br> ![Feladat összegzése](media/automation-first-runbook-graphical/runbook-job-output.png)  
8.  Zárja be a Kimenet panelt.
9.  A forgatókönyv-feladathoz tartozó Streams panel megnyitásához kattintson **Az összes napló** lehetőségre.  A kimeneti streamben csak a *Hello World* eredményt látjuk, de ez megjeleníthet egyéb streameket is egy forgatókönyv feladatból, mint a Részletes vagy a Hiba, ha a forgatókönyv ezekbe ír.<br> ![Feladat összegzése](media/automation-first-runbook-graphical/runbook-job-AllLogs.png)
10. Zárja be Az összes napló panelt és a Feladat panelt a MyFirstRunbook panelhez való visszatéréshez.
11. A forgatókönyv Feladatok paneljének megnyitásához kattintson a **Feladatok** lehetőségre.  Ez felsorolja az összes, a forgatókönyv által létrehozott feladatot. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtattuk a feladatot.<br> ![Feladatok](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már láttunk a forgatókönyv indításakor.  Ez lehetővé teszi, hogy az időben visszamenve megtekintse egy adott forgatókönyvhöz létrehozott összes feladat részleteit.

## 5. lépés: Változó adategységek létrehozása

Most már teszteltük és közzétettük a forgatókönyvet, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen.  Mielőtt konfigurálnánk a forgatókönyvet a hitelesítésre, létrehozunk egy változót, amely tárolja az előfizetés-azonosítót, és hivatkozunk rá, miután beállítottuk a tevékenységet hitelesítésre alább, a 6. lépésben.  Ha hivatkozást szerepeltet az előfizetési környezetben, könnyebben használhat több előfizetést.  A továbbhaladás előtt másolja az előfizetés-azonosítót a Navigáció panel előfizetés-beállításai közül.  

1. Az Automation-fiókok panelen kattintson az **Adategységek** csempére, és megnyílik az **Adategységek** panel.
2. Az Adategységek panelen kattintson a **Változók** csempére.
3. A Változók panelen kattintson a **Változó hozzáadása** lehetőségre.<br>![Automation változó](media/automation-first-runbook-graphical/create-new-subscriptionid-variable.png)
4. Az Új változó panelen a **Név** mezőben adja meg a következőt: **AzureSubscriptionId**. Az **Érték** mezőben adja meg az előfizetés-azonosítóját.  A **Típus** maradjon **Karakterlánc**, és tartsa meg a *Titkosítás* alapértelmezett értékét.  
5. A változó létrehozásához kattintson a **Létrehozás** gombra.  


## 6. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most, hogy van egy változónk az előfizetés-azonosító tárolására, konfigurálhatjuk a forgatókönyvünket, hogy megtörténjen a hitelesítésük a Futtató hitelesítő adatokkal, amelyek az [előfeltételek](#prerequisites) között szerepelnek.  Ehhez hozzáadjuk az **Asset** Azure-beli Futtató kapcsolatot és az **Add-AzureRMAccount** parancsmagot a vászonhoz.  

1.  Kattintson a MyFirstRunbook panel **Szerkesztés** gombjára a grafikus szerkesztő megnyitásához.<br> ![Forgatókönyv szerkesztése](media/automation-first-runbook-graphical/runbook-controls-edit-revised20165.png)
2.  A **Hello World megjelenítése a kimenetben** elemre nincs többet szükségünk, ezért kattintson rá a jobb gombbal, és válassza a **Törlés** parancsot.
3.  A Könyvtár vezérlőben bontsa ki a **Kapcsolatok** lehetőséget, és a **Hozzáadás a vászonhoz** lehetőséget választva adja hozzá a vászonhoz az **AzureRunAsConnection** elemet.
4.  A vásznon válassza ki az **AzureRunAsConnection** elemet, és a Konfiguráció vezérlőpanelen írja be a **Futtató kapcsolat létesítése** mondatot a **Címke** szövegmezőbe.  Ez a kapcsolat 
5.  A Könyvtár vezérlőben írja be a keresési szövegmezőbe a következőt: **Add-AzureRMAccount**.
6.  Adja hozzá a vászonhoz az **Add-AzureRMAccount** elemet.<br> ![Add-AzureRMAccount](media/automation-first-runbook-graphical/search-powershell-cmdlet-addazurermaccount.png)
7.  Vigye a kurzort a **Futtató kapcsolat létesítése** fölé, és várja meg, amíg megjelenik az alakzat alján egy kör. Kattintson a körre, és húzza a nyilat az **Add-AzureRmAccount** elemre.  A most létrehozott nyíl egy *hivatkozás*.  A forgatókönyv a **Futtató kapcsolat beszerzése** elemmel indul, majd futtatja az **Add-AzureRmAccount** elemet.<br> ![Hivatkozás létrehozása a tevékenységek között](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
8.  A vásznon válassza ki az **Add-AzureRmAccount** elemet, és a Konfiguráció vezérlőpanelen írja be a **Bejelentkezés az Azure-ba** mondatot a **Címke** szövegmezőbe.
9.  Kattintson a **Paraméterek** elemre, és megjelenik a Tevékenység paramétereinek konfigurálása panel. 
10.  Az **Add-AzureRmAccount** több paraméterkészlettel rendelkezik, ezért ki kell választanunk egyet, mielőtt megadhatnánk a paraméterértékeket.  Kattintson a **Paraméterkészlet** lehetőségre, és válassza a **ServicePrincipalCertificate** paraméterkészletet. 
11.  Ha kiválasztotta a paraméterkészletet, a paraméterek megjelennek a Tevékenység paramétereinek konfigurálása panelen.  Kattintson az **APPLICATIONID** elemre.<br> ![Azure RM-fiók paramétereinek hozzáadása](media/automation-first-runbook-graphical/add-azurermaccount-parameterset.png)
12.  A Paraméter értéke panelen válassza a **Tevékenység kimenete** elemet az **Adatforrás** területen, majd válassza a **Futtató kapcsolat létesítése** elemet a listáról. A **Mező elérési útja** szövegmezőbe írja be az **ApplicationId** kifejezést, majd kattintson az **OK** elemre.  A mező elérési útjához tartozó tulajdonság nevét azért adjuk meg, mert a tevékenység egy több tulajdonsággal rendelkező objektumot eredményez.
13.  Kattintson a **CERTIFICATETHUMBPRINT** elemre, és a Paraméter értéke panelen válassza a **Tevékenység kimenete** elemet az **Adatforrás** területen.  Válassza a **Futtató kapcsolat létesítése** elemet a listáról, és a **Mező elérési útja** szövegmezőbe írja be a **CertificateThumbprint** kifejezést, majd kattintson az **OK** elemre. 
14.  Kattintson a **SERVICEPRINCIPAL** elemre, és a Paraméter értéke panelen válassza a **ConstantValue** elemet az **Adatforrás** területen, kattintson a **True** lehetőségre, és kattintson az **OK** gombra.
15.  Kattintson a **TENANTID** elemre, és a Paraméter értéke panelen válassza a **Tevékenység kimenete** elemet az **Adatforrás** területen.  Válassza a **Futtató kapcsolat létesítése** elemet a listáról, és a **Mező elérési útja** szövegmezőbe írja be a **TenantId** kifejezést, majd kattintson kétszer az **OK** elemre.  
16.  A Könyvtár vezérlőben írja be a keresési szövegmezőbe a következőt: **Set-AzureRmContext**.
17.  Adja hozzá a vászonhoz a **Set-AzureRmContext** elemet.
18.  A vásznon válassza ki a **Set-AzureRmContext** elemet, és a Konfiguráció vezérlőpanelen írja be az **Előfizetés azonosítójának megadása** mondatot a **Címke** szövegmezőbe.
19.  Kattintson a **Paraméterek** elemre, és megjelenik a Tevékenység paramétereinek konfigurálása panel. 
20. A **Set-AzureRmContext** több paraméterkészlettel rendelkezik, ezért ki kell választanunk egyet, mielőtt megadhatnánk a paraméterértékeket.  Kattintson a **Paraméterkészlet** lehetőségre, és válassza a **SubscriptionId** paraméterkészletet.  
21.  Ha kiválasztotta a paraméterkészletet, a paraméterek megjelennek a Tevékenység paramétereinek konfigurálása panelen.  Kattintson a **SubscriptionID** elemre.
22.  A Paraméter értéke panelen válassza a **Változó adategység** elemet az **Adatforrás** területen, majd válassza az **AzureSubscriptionId** elemet a listáról, és kattintson kétszer az **OK** gombra.   
23.  Vigye a kurzort a **Bejelentkezés az Azure-ba** fölé, és várja meg, amíg megjelenik az alakzat alján egy kör. Kattintson a körre, és húzza a nyilat az **Előfizetés azonosítójának megadása** elemre.


A forgatókönyvnek ezen a ponton az alábbi kódhoz kell hasonlítania: <br>![Forgatókönyv-hitelesítés konfigurálása](media/automation-first-runbook-graphical/runbook-auth-config.png)

## 7. lépés – Virtuális gépet elindító tevékenység hozzáadása

Most hozzáadunk egy **Start-AzureRmVM** tevékenységet egy virtuális gép elindítására.  Kiválaszthatja az Azure-előfizetésében lévő bármelyik virtuális gépet, és most szoftveresen rögzítjük ezt a nevet a parancsmagba.

1. A Könyvtár vezérlőben írja be a keresési szövegmezőbe a következőt: **Start-AzureRm**.
2. Adja hozzá a **Start-AzureRmVM** elemet a vászonhoz, és húzza az **Előfizetés azonosítójának megadása** alá.
3. Vigye a kurzort a **Előfizetés azonosítójának megadása** fölé, és várja meg, amíg megjelenik az alakzat alján egy kör.  Kattintson a körre, és húzza a nyilat a **Start-AzureRmVM** elemre. 
4.  Jelölje ki a **Start-AzureRmVM** elemet.  A **Start-AzureRmVM** készleteinek megtekintéséhez kattintson a **Paraméterek**, majd a **Paraméterkészlet** lehetőségre.  Válassza ki a **ResourceGroupNameParameterSetName** paraméterkészletet. Figyelje meg, hogy a **ResourceGroupName** és a **Name** mellett felkiáltójel van.  Ez azt jelzi, hogy ezek kötelező paraméterek.  Azt is észreveheti, hogy mindkét helyen szöveges értéket kell megadni.
5.  Válassza ki a **Name** paramétert.  Válassza ki a **PowerShell-kifejezés** elemet az **Adatforrás** területen, majd írja be a forgatókönyvvel elindított virtuális gép nevét idézőjelek között.  Kattintson az **OK** gombra.<br>![Start-AzureRmVM nevének paraméteres értéke](media/automation-first-runbook-graphical/runbook-startvm-nameparameter.png)
6.  Válassza a **ResourceGroupName** elemet. Használja a **PowerShell-kifejezés** elemet az **Adatforrás** mezőben, majd írja be az erőforráscsoport nevét idézőjelek között.  Kattintson az **OK** gombra.<br> ![Start-AzureRmVM paraméterek](media/automation-first-runbook-graphical/startazurermvm-params.png)
8.  Kattintson a Teszt panelre, hogy tesztelhessük forgatókönyvet.
9.  Kattintson az **Indítás** gombra a teszt elindításához.  Ha kész, ellenőrizze, hogy a virtuális gép elindult-e.

A forgatókönyvnek ezen a ponton az alábbi kódhoz kell hasonlítania: <br>![Forgatókönyv-hitelesítés konfigurálása](media/automation-first-runbook-graphical/runbook-startvm.png)

## 8. lépés – További bemeneti paraméterek hozzáadása a forgatókönyvhöz

A forgatókönyvünk jelenleg elindítja a **Start-AzureRmVM** parancsmagban megadott virtuális gépet az erőforráscsoportban, de a forgatókönyvünk hasznosabb lenne, ha meg tudnánk adni mindkettőt a forgatókönyv elindulásakor.  Most hozzáadunk bemeneti paramétereket a forgatókönyvhöz, amelyek biztosítják ezt a működést.

1. Kattintson a **MyFirstRunbook** panel **Szerkesztés** gombjára a grafikus szerkesztő megnyitásához.
2. A Forgatókönyv bemeneti paramétere panel megnyitásához kattintson a **Bemenet és kimenet**, majd a **Bemenet hozzáadása** lehetőségre.<br> ![Forgatókönyv-bemenet és -kimenet](media/automation-first-runbook-graphical/runbook-toolbar-InputandOutput-revised20165.png)
3. A**Name** paraméterhez adja meg a *VMNAme* értéket.  A **Típus** maradjon *karakterlánc*, de a **Kötelező** értékét módosítsa arra, hogy *Igen*.  Kattintson az **OK** gombra.
4. Hozzon létre egy második kötelező bemeneti paramétert *ResourceGroupName* néven, majd kattintson az **OK** gombra a **Bemenet és kimenet** panelen.<br> ![Forgatókönyv bemeneti paraméterei](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
5. Válassza a **Start-AzureRmVM** tevékenységet, majd kattintson a **Paraméterek** elemre.
6. A **Name** paraméter **Adatforrás** elemét módosítsa a **Forgatókönyv-bemenet** beállításra, majd válassza a **VMName** lehetőséget.<br>
7. A **ResourceGroupName** paraméternél az **Adatforrás** beállítást módosítsa a **Forgatókönyv-bemenet** értékre, majd válassza a **ResourceGroupName** lehetőséget.<br> ![Start-AzureVM paraméterei](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
8. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt.  Figyelje meg, hogy most már megadhat értékeket a teszt során használt két bemeneti változóhoz.
9. Zárja be a Teszt panelt.
10. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.
11. Állítsa le az előző lépésben elindított virtuális gépet.
12. Kattintson az **Indítás** gombra a forgatókönyv elindításához.  Írja be a **VMName** és a **ResourceGroupName** értéket az elindítani kívánt virtuális géphez.<br> ![Forgatókönyv indítása](media/automation-first-runbook-graphical/runbook-start-inputparams.png)
13. Ha a forgatókönyv kész, ellenőrizze, hogy a virtuális gép elindult-e.

## 9. lépés – Feltételes hivatkozás létrehozása

Most úgy fogjuk módosítani a forgatókönyvet, hogy csak akkor próbálja meg elindítani a virtuális gépet, ha az még nem indult el.  Ennek érdekében hozzáadjuk a **Get-AzureRmVM** parancsmagot a forgatókönyvhöz. Ez lekéri a virtuális gép példányszintű állapotát. Ezután hozzáadunk egy **Állapot kérése** nevű PowerShell-munkafolyamati kódmodult egy PowerShell-kódrészlettel, amely meghatározza, hogy a virtuális gép fut-e vagy le van állítva.  Az **Állapot lekérése** modulból származó feltételes hivatkozás csak akkor futtatja a **Start-AzureRmVM** tevékenységet, ha a jelenlegi, futó állapot helyett a gép le van állítva.  Végül a PowerShell Write-Output parancsmag használatával megjelenő üzenet tájékoztatja, hogy a virtuális gép sikeresen elindult-e vagy sem.

1. Nyissa meg a **MyFirstRunbook** elemet a grafikus szerkesztőben.
2. Távolítsa el az **Előfizetés azonosítójának megadása** és a **Start-AzureRmVM** közötti hivatkozást. Ehhez kattintson rá, majd nyomja le a *Delete* billentyűt.
3. A Könyvtár vezérlőben írja be a keresési szövegmezőbe a következőt: **Get-AzureRm**.
4. Adja hozzá a vászonhoz a **Get-AzureRmVM** elemet.
5. A **Get-AzureRmVM** készleteinek megtekintéséhez válassza a **Get-AzureRmVM**, majd a **Paraméterkészlet** lehetőséget.  Válassza ki a **GetVirtualMachineInResourceGroupNameParamSet** paraméterkészletet.  Figyelje meg, hogy a **ResourceGroupName** és a **Name** mellett felkiáltójel van.  Ez azt jelzi, hogy ezek kötelező paraméterek.  Azt is észreveheti, hogy mindkét helyen szöveges értéket kell megadni.
6. A **Name** paraméterhez tartozó **Adatforrás** elemét módosítsa a **Forgatókönyv bemenet** beállításra, majd válassza a **VMName** lehetőséget.  Kattintson az **OK** gombra.
7. A **ResourceGroupName** paraméterhez tartozó **Adatforrás** elemét módosítsa a **Forgatókönyv bemenet** beállításra, majd válassza a **ResourceGroupName** lehetőséget.  Kattintson az **OK** gombra.
8. A **Status** paraméterhez tartozó **Adatforrás** elemét módosítsa a **Forgatókönyv bemenet** beállításra, majd kattintson az **Igaz** gombra.  Kattintson az **OK** gombra.  
9. Hozzon létre egy hivatkozást az **Előfizetés azonosítójának megadása** és a **Get-AzureRmVM** között.
10. A Könyvtár vezérlőben bontsa ki a **Forgatókönyv vezérlése** lehetőséget, és adja hozzá a vászonhoz az **Code** elemet.  
11. Hozzon létre egy hivatkozást a **Get-AzureRmVM** és a **Code** között.  
12. Kattintson a **Code** elemre, majd a Konfiguráció panelen módosítsa a címkét a következőre: **Állapot kérése**.
13. Válassza a **Code** paramétert, majd megjelenik a **Kódszerkesztő** panel.  
14. A kódszerkesztőben illessze be az alábbi kódrészletet.

     ```
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText 
     $Statuses = ConvertFrom-Json $StatusesJson 
     $StatusOut ="" 
     foreach ($Status in $Statuses){ 
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"} 
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"} 
     } 
     $StatusOut 
     ```

15. Hozzon létre egy hivatkozást az **Állapot kérése** és a **Start-AzureRmVM** között.<br> ![Forgatókönyv kódmodullal](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
16. Jelölje ki a hivatkozást, majd a Konfiguráció panelen módosítsa a **Feltétel alkalmazása** értékét ara, hogy **Igen**.   Figyelje meg, hogy a hivatkozás egy szaggatott vonallá változik, amely azt jelzi, hogy a céltevékenység csak akkor fut, ha a feltétel igazzá válik.  
17. A **Feltételkifejezés** területen írja be a következőt: *$ActivityOutput['Get Status'] -eq "Stopped"*.  A **Start-AzureRmVM** most csak akkor fog futni, ha a virtuális gép le van állítva.
18. A Könyvtár vezérlőben bontsa ki a **Parancsmagok** elemet, és válassza a **Microsoft.PowerShell.Utility** lehetőséget.
19. Adja hozzá a vászonhoz kétszer a következőt: **Write-Output**.<br> ![Forgatókönyv Write-Output parancsmaggal](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
20. Az első **Write-Output** vezérlőn módosítsa a **Címke** értéket a következőre:*Értesítés a virtuális gép indulásáról*
21. Az **InputObject** elemnél módosítsa az **Adatforrás** beállítását a **PowerShell-kifejezés** értékre, és írja be a következő kifejezést:*„$VMName sikeresen elindult.”*.
22. A második **Write-Output** vezérlőn módosítsa a **Címke** értéket a következőre:*Értesítés a virtuális gép indulásának meghiúsulásáról*
23. Az **InputObject** elemnél módosítsa az **Adatforrás** beállítását a **PowerShell-kifejezés** értékre, és írja be a következő kifejezést:*„VMName nem tudott elindulni.”*.
24. Hozzon létre egy hivatkozást a **Start-AzureRmVM** és az **Értesítés a virtuális gép indulásáról**, valamint az **Értesítés a virtuális gép indulásának meghiúsulásáról** között.
25. Válassza ki az **Értesítés a virtuális gép indulásáról** felé mutató hivatkozást, és módosítsa a **Feltétel alkalmazása** beállítást arra, hogy **Igaz**.
26. A **Feltételkifejezéshez** írja be a következőt: *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*.  A Write-Output vezérlés most csak akkor fog futni, ha a virtuális gép sikeresen elindult.
27. Válassza ki az **Értesítés a virtuális gép indulásának meghiúsulásáról** felé mutató hivatkozást, és módosítsa a **Feltétel alkalmazása** beállítást arra, hogy **Igaz**.
28. A **Feltételkifejezéshez** írja be a következőt: *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*.  A Write-Output vezérlés most csak akkor fog futni, ha a virtuális gép nem indult el sikeresen. 
29. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt.
30. Indítsa el a forgatókönyvet úgy, hogy a virtuális gép nem fut, és elvileg el kell indulnia.

## Következő lépések

-   További információk a Grafikus létrehozásról: [Grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md).
-   A PowerShell-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-forgatókönyvem](automation-first-runbook-textual-powershell.md).
-   A PowerShell munkafolyamat  forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md).



<!--HONumber=jun16_HO2-->


