---
title: Az első Azure Automation PowerShell-forgatókönyvem | Microsoft Docs
description: Ez az oktatóanyag bemutatja egy egyszerű PowerShell-forgatókönyv létrehozását, tesztelését és közzétételét.
services: automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: ''
keywords: azure powershell, powershell-szkript oktatóanyag, powershell automation

ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/19/2016
ms.author: magoedte;sngun

---
# Az első PowerShell-forgatókönyvem
> [AZURE.SELECTOR] - [Grafikus](automation-first-runbook-graphical.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md) - [PowerShell munkafolyamat](automation-first-runbook-textual.md)  
> 
> 

Ez az oktatóanyag bemutatja, hogyan hozhat létre az Azure Automationben egy [PowerShell-forgatókönyvet](automation-runbook-types.md#powershell-runbooks). Egy egyszerű forgatókönyvvel fogunk kezdeni, amelyet tesztelünk és közzé is teszünk, és bemutatjuk a forgatókönyv állapotának nyomon követését is. Ezután módosítjuk a forgatókönyvet, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure virtuális gépet. Ezután még robusztusabbá tesszük a forgatókönyvet, és forgatókönyv-paramétereket adunk hozzá.

## Előfeltételek
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége.

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve <a href="/pricing/free-account/" target="_blank">[regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
* [Automation-fiók](automation-security-overview.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez.  Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Ezt a gépet leállítjuk és elindítjuk, tehát ne legyen éles használatban.

## 1. lépés – Új forgatókönyv létrehozása
Először egy egyszerű forgatókönyvet hozunk létre, amely a *Hello World* szöveget adja vissza.

1. Az Azure portálon nyissa meg az Automation-fiókját.  
   Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg rendelkezik már adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.
2. Kattintson a **Forgatókönyvek** csempére a forgatókönyvek listájának megnyitásához.  
   ![RunbooksControl](media/automation-first-runbook-textual-powershell/automation-runbooks-control.png)  
3. Hozzon létre egy új forgatókönyvet a **Forgatókönyv hozzáadása**, majd az **Új forgatókönyv létrehozása** elemre kattintva.
4. Adja a forgatókönyvnek a *MyFirstRunbook-PowerShell* nevet.
5. Ebben az esetben egy [PowerShell-forgatókönyvet](automation-runbook-types.md#powershell-runbooks) hozunk létre, ezért a **Forgatókönyv típusának** válassza a **PowerShell** lehetőséget.  
   ![Forgatókönyv típusa](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)  
6. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## 2. lépés – Kód hozzáadása a forgatókönyvhöz
Beírhat közvetlenül a forgatókönyvbe kódot, vagy választhat parancsmagokat, forgatókönyveket és adategységeket a Könyvtár vezérlőből, majd hozzáadhatja őket a forgatókönyvhöz a kapcsolódó paraméterekkel együtt. Ebben a bemutatóban közvetlenül a forgatókönyvbe írunk.

1. A forgatókönyvünk jelenleg üres, írja be a következőt: *Write-Output "Hello World."*.  
   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. A **Mentés** gombra kattintva mentse el a forgatókönyvet.  
   ![Mentés gomb](media/automation-first-runbook-textual-powershell/automation-save-button.png)  

## 3. lépés – A forgatókönyv tesztelése
Mielőtt közzétesszük a forgatókönyvet, hogy éles üzemben is elérhető legyen, tesztelnünk kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.  
   ![Teszt panel](media/automation-first-runbook-textual-powershell/automation-testpane.png)  
2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
3. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), és megjelenik annak állapota.  
   A feladat állapota kezdetben *Várólistán*. Ez azt jelöli, hogy egy felhőben lévő forgatókönyv-feldolgozó elérhetővé válására vár. Ezután *Indítás* állapotúra változik, ha egy feldolgozó elvállalja a feladatot, majd *Fut* állapotúra, amikor a forgatókönyv elkezd futni.  
4. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. A mi esetünkben ez a következő: *Hello World*  
   ![Teszt panel kimenete](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## 4. lépés: Közzététel és a forgatókönyv indítása
A létrehozott forgatókönyv még mindig Piszkozat módban van. Az üzemi környezetben való futtatás előtt közzé kell tennünk. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Az esetünkben még nincs Közzétett verzió, mivel még csak most hoztuk létre a forgatókönyvet.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.  
   ![Közzététel gomb](media/automation-first-runbook-textual-powershell/automation-publish-button.png)  
2. Ha most balra görgetve megtekinti a forgatókönyvet a **Forgatókönyvek** panelen, a **Közzétett** **Szerzői állapot** jelenik meg.
3. Görgessen vissza jobbra a **MyFirstRunbook-PowerShell** panel megtekintéséhez.  
   A felül látható lehetőségekkel elindíthatjuk és megtekinthetjük a forgatókönyvet, ütemezhetjük egy későbbi időpontban való indításra, vagy létrehozhatunk egy [webhookot](automation-webhooks.md), amely segítségével elindítható a forgatókönyv egy HTTP-hívással.
4. Most csak el szeretnénk indítani a forgatókönyvet, tehát kattintson az **Indítás**, majd, amikor a Forgatókönyv indítása panel megnyílik, kattintson az **OK** gombra.  
   ![Indítás gomb](media/automation-first-runbook-textual-powershell/automation-start-button.png)  
5. Megnyílik egy feladatpanel az imént létrehozott forgatókönyv-feladathoz. Ezt a panelt bezárhatjuk, de ebben az esetben nyitva hagyjuk, hogy lássuk a feladat előrehaladását.
6. A feladat állapota a **Feladat összegzése** területen látszik, és megfelel a korábban, a forgatókönyv tesztelésekor látott állapotoknak.  
   ![Feladat összegzése](media/automation-first-runbook-textual-powershell/automation-job-summary.png)  
7. Ha a forgatókönyv a *Befejezve* állapotot mutatja, kattintson a **Kimenet** lehetőségre. A Kimenet panel megnyílik, és megjelenik a *Hello World* feliratunk.  
   ![Feladat kimenete](media/automation-first-runbook-textual-powershell/automation-job-output.png)
8. Zárja be a Kimenet panelt.
9. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. A kimeneti streamben csak a *Hello World* eredményt látjuk, de ez megjeleníthet egyéb streameket is egy forgatókönyv feladatból, mint a Részletes vagy a Hiba, ha a forgatókönyv ezekbe ír.  
   ![Minden napló](media/automation-first-runbook-textual-powershell/automation-alllogs.png)  
10. A MyFirstRunbook-PowerShell panelre való visszatéréshez zárja be a Streamek és a Feladat panelt.
11. A forgatókönyv Feladatok paneljének megnyitásához kattintson a **Feladatok** lehetőségre. Ez felsorolja az összes, a forgatókönyv által létrehozott feladatot. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtattuk a feladatot.  
    ![Feladatlista](media/automation-first-runbook-textual-powershell/automation-job-list.png)  
12. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már láttunk a forgatókönyv indításakor. Ez lehetővé teszi, hogy az időben visszamenve megtekintse egy adott forgatókönyvhöz létrehozott összes feladat részleteit.

## 5. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez
Most már teszteltük és közzétettük a forgatókönyvet, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Erre nem lesz képes egészen addig, amíg nem végez hitelesítést az [előfeltételek](#prerequisites) között említett hitelesítő adatokkal. Ezt az **Add-AzureRMAccount** parancsmaggal hajtjuk végre.

1. Kattintson a MyFirstRunbook-PowerShell panel **Szerkesztés** gombjára szöveges szerkesztő megnyitásához.  
   ![Forgatókönyv szerkesztése](media/automation-first-runbook-textual-powershell/automation-edit-runbook.png)  
2. A **Write-Output** sorra már nincs szükségünk, így ezt kitörölheti.
3. Írja be, vagy másolja és illessze be a következő kódot, amely kezeli a hitelesítést az Automation futtató fiókjával.
   
   ```
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ``` 
   <br>
4. Kattintson a **Teszt panel** elemre, hogy tesztelhessük forgatókönyvet.
5. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, a kimenetnek a fiókja alapvető adatait kell megjelenítenie, a következőhöz hasonló módon. Ez ellenőrzi, hogy érvényes-e a hitelesítő adat. <br> ![Hitelesítés](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## 6. lépés: Kód hozzáadása a virtuális gép indításához
Most, hogy a forgatókönyv hitelesítést végez az Azure-előfizetésünkön, kezelhetjük az erőforrásokat. Hozzáadunk egy parancsot a virtuális gép elindítására. Kiválaszthatja az Azure-előfizetésében lévő bármelyik virtuális gépet, és most szoftveresen rögzítjük ezt a nevet a parancsmagba.

1. Az *Add-AzureRmAccount* után írja be a következőt: *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'*. Ezzel megadja az elindítani kívánt virtuális gép nevét, valamint az erőforráscsoportjának nevét.  
   
   ```
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint 
    Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
    ```
   <br>
2. Mentse a forgatókönyvet, majd kattintson a **Teszt panel** elemre, hogy tesztelhessük.
3. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, ellenőrizze, hogy a virtuális gép elindult-e.

## 7. lépés – Bemeneti paraméter hozzáadása a forgatókönyvhöz
A forgatókönyvünk jelenleg elindítja a forgatókönyvben szoftveresen rögzített virtuális gépet, de hasznosabb lenne, ha meg tudnánk adni a virtuális gépet a forgatókönyv elindulásakor. Most hozzáadunk bemeneti paramétereket a forgatókönyvhöz, amelyek biztosítják ezt a működést.

1. Adja hozzá a *VMName* és a *ResourceGroupName* paramétereket a forgatókönyvhöz, és használja ezeket a változókat a **Start-AzureRmVM** parancsmaggal, amint az az alábbi példában látható.  
   
   ```
   Param(
      [string]$VMName,
      [string]$ResourceGroupName
   )
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint 
    Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
    ```
   <br> 
2. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Figyelje meg, hogy most már megadhat értékeket a teszt során használt két bemeneti változóhoz.
3. Zárja be a Teszt panelt.
4. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.
5. Állítsa le az előző lépésben elindított virtuális gépet.
6. Kattintson az **Indítás** gombra a forgatókönyv elindításához. Írja be a **VMName** és a **ResourceGroupName** értéket az elindítani kívánt virtuális géphez.  
   ![Paraméter átadása](media/automation-first-runbook-textual-powershell/automation-pass-params.png)  
7. Ha a forgatókönyv kész, ellenőrizze, hogy a virtuális gép elindult-e.

## Eltérések a PowerShell-munkafolyamattól
A PowerShell-forgatókönyvek életciklusa, képességei és felügyelete megegyezik a PowerShell-alapú munkafolyamat-forgatókönyvekével, de van néhány eltérés és korlátozás.

1. A PowerShell-forgatókönyvek a munkafolyamat-forgatókönyvekhez képest gyorsabban futnak, mert nem szerepel bennük a fordítási lépés.
2. A PowerShell-alapú munkafolyamat-forgatókönyvek támogatják az ellenőrzőpontokat, és az ellenőrzőpontok segítségével a PowerShell-alapú munkafolyamat-forgatókönyvek a forgatókönyv bármely pontjától folytathatók, ezzel szemben egy PowerShell-forgatókönyv csak az elejéről indítható újra.
3. A PowerShell-alapú munkafolyamat-forgatókönyvek támogatják a párhuzamos és soros végrehajtást is, míg a PowerShell-forgatókönyvek csak sorosan képesek végrehajtani a parancsokat.
4. Egy PowerShell-alapú munkafolyamat-forgatókönyvben egy tevékenység-, parancs- vagy parancsfájl-blokk rendelkezhet saját futási térrel, míg a PowerShell-forgatókönyvekben minden parancsfájl egyetlen futási térben fut. Van néhány [szintaktikai eltérés](https://technet.microsoft.com/magazine/dn151046.aspx) is a natív PowerShell-forgatókönyvek és a PowerShell-alapú munkafolyamat-forgatókönyvek között.

## Következő lépések
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* További információk a PowerShell-parancsprogramok támogatásáról: [PowerShell-parancsprogramok natív támogatása az Azure Automationben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

<!--HONumber=Sep16_HO4-->


