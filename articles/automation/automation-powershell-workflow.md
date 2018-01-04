---
title: "Azure Automation PowerShell-munkafolyamati tanulási |} Microsoft Docs"
description: "Ez a cikk szándék szerint egy gyors lecke használta a Powershellt szerzők adott PowerShell és a PowerShell munkafolyamatok és a vonatkozó Automation-runbook fogalmak közötti különbségek megismeréséhez."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 84bf133e-5343-4e0e-8d6c-bb14304a70db
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 90a8229b3d4974b8385039c7d85f916a168947d8
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Tanulási automatizálási runbookok Windows PowerShell munkafolyamat alapfogalmai 
Azure Automation Runbookjai Windows PowerShell-munkafolyamatként vannak megvalósítva.  A Windows PowerShell munkafolyamat hasonló Windows PowerShell-parancsfájlba, de néhány jelentős különbség az, hogy egy új felhasználóhoz zavaró lehet.  Ez a cikk segítséget nyújt a runbookok használatával. a PowerShell-munkafolyamat írása készült, de javasolt PowerShell használatával, ha nem kell az ellenőrzőpontok runbookok írása.  Több szintaktikai különbségek vannak PowerShell munkafolyamat runbookok létrehozásakor, és ezek a különbségek egy kicsit nagyobb munkahelyi hatékony munkafolyamatok írása szükséges.  

Egy munkafolyamat olyan programozott, összekapcsolódó lépések, amelyek hosszan futó feladatokat végeznek el vagy több lépés összehangolása szükséges több eszközön vagy felügyelt csomóponton keresztül sorozatát. Az egyszerű parancsprogramokhoz képest munkafolyamat előnyei többek között a egyidejűleg elvégezni egy műveletet több eszközön lehetőséget, és képes automatikusan helyreállni hibák után. A Windows PowerShell munkafolyamat által használt Windows folyamatkövető alaprendszer Windows PowerShell-parancsfájl. A munkafolyamat készült Windows PowerShell-szintaxis, és a Windows PowerShell által elindított, feldolgozását a Windows Workflow Foundation.

Az ebben a cikkben szereplő témakörök a részleteket lásd: [Ismerkedés a Windows PowerShell munkafolyamat](http://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>A munkafolyamatok alapvető szerkezete
Első lépése a PowerShell-parancsfájl átalakítása egy PowerShell-munkafolyamat van befoglaló azt a **munkafolyamat** kulcsszó.  Egy munkafolyamat kezdődik-e a **munkafolyamat** kulcsszót a parancsprogram kapcsos zárójelek között elhelyezett törzse követ. A munkafolyamat neve követi a **munkafolyamat** kulcsszót, ahogy az a következő szintaxist:

    Workflow Test-Workflow
    {
       <Commands>
    }

A munkafolyamat nevének meg kell egyeznie az Automation-forgatókönyv neve. Ha a runbook importálja, majd a fájlnevet meg kell egyeznie a munkafolyamat nevével, és kell végződnie *.ps1*.

Ha paramétereket szeretne felvenni a munkafolyamathoz, használja a **Param** , ahogy a parancsfájl kulcsszó.

## <a name="code-changes"></a>Kódmódosítások
PowerShell-munkafolyamat kódjának jelek majdnem azonos PowerShell parancsfájl kódot néhány jelentős változások kivételével.  A következő szakaszok ismertetik, amelyek egy PowerShell-parancsfájlba ki azt a munkafolyamat futtatásához szükséges módosításokat.

### <a name="activities"></a>Tevékenységek
Egy tevékenység készen egy adott feladat egy munkafolyamatot belül. Ugyanúgy, mint a parancsfájl egy vagy több parancsból áll, egy munkafolyamat áll egy vagy több, sorrendben végrehajtott tevékenységből áll. Windows PowerShell-munkafolyamat automatikusan alakít számos Windows PowerShell-parancsmagok tevékenységben egy munkafolyamat futtatásakor. Ha megadja e parancsmagok egyikét a runbookban, a megfelelő tevékenységet futtatja a Windows Workflow Foundation. Azon parancsmagok esetében nem tartozik megfelelő tevékenység, a Windows PowerShell-munkafolyamat automatikusan futtatja a parancsmagot egy [InlineScript](#inlinescript) tevékenység. Nincs olyan parancsmagokat, amelyek ki vannak zárva és nem használható egy munkafolyamatban, ha nem explicit módon adja meg azokat az InlineScript blokkon belül. Ezekről a fogalmakról további információkért lásd: [tevékenységek használata parancsfájl-munkafolyamatok](http://technet.microsoft.com/library/jj574194.aspx).

Munkafolyamat-tevékenységek jellemzőkkel az általános paraméterek konfigurálása a műveletet. További általános munkafolyamat-paraméterekkel kapcsolatos további információkért lásd: [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Pozícióparaméterek
A tevékenységek és a munkafolyamat-parancsmagok pozícióparaméterek nem használható.  Ez azt jelenti az, hogy a paraméter nevét kell használnia.

Vegye figyelembe például az alábbi kódot, amely lekérdezi az összes futó szolgáltatásokat.

     Get-Service | Where-Object {$_.Status -eq "Running"}

Ha ugyanazt a kódot egy munkafolyamatban való futtatásához, kap egy üzenetet, például "paraméter beállítása nem lehet feloldani a megadott nevesített paraméterek használatával."  Ennek elkerülése érdekében adja meg a paraméter neve, ahogy a következő.

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>Deszerializált objektum
A munkafolyamatokban objektumok deszerializálni vannak.  Ez azt jelenti, hogy a tulajdonságai továbbra is elérhetők, de nem a módszerek.  Vegyük példaként a következő PowerShell-kódot, amely leállítja a szolgáltatást a Stop metódussal a szolgáltatás objektum.

    $Service = Get-Service -Name MyService
    $Service.Stop()

Ha ez a munkafolyamat futtatásához, hibaüzenet arról, hogy "a metódushívás nem támogatott a Windows PowerShell-munkafolyamat."  

Egy elem a kód két sort csomagolásához egy [InlineScript](#inlinescript) letiltása $Service esetben egy service objektum a blokkon belül lenne.

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    }

Egy másik lehetőség egy használja egy másik parancsmagot, a módszer ugyanazt a funkciót hajtja végre, ha elérhető ilyen.  A mintában a Stop-Service parancsmag a Stop metódus azonos funkciókat biztosítják, és a következő munkafolyamat használhat.

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>Az InlineScript
A **InlineScript** tevékenység akkor hasznos, ha egy vagy több parancsból PowerShell munkafolyamat helyett a hagyományos PowerShell parancsfájl futtatásához szükséges.  Egy munkafolyamat-parancsok Windows Workflow Foundation feldolgozásra kerülnek, amíg InlineScript blokkon belül lévő parancsok Windows PowerShell dolgoznak fel.

Az InlineScript szintaxisa a következő alább látható.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

A kimeneti rendel egy változó egy InlineScript kimeneti adhatja vissza. A következő példa a szolgáltatás leáll, és majd exportálja a szolgáltatás nevét.

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Az InlineScript blokkon belül lehet értéket átadni, de kell használnia **$Using** hatókör-módosítót.  A következő példa megegyezik az előző példához, azzal a különbséggel, hogy a szolgáltatás neve változó által biztosított.

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"

        $Output = InlineScript {
            $Service = Get-Service -Name $Using:ServiceName
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Amikor az InlineScript tevékenység lehet, hogy bizonyos munkafolyamatok alapvető fontosságú, azok nem támogatják a munkafolyamat szerkezetek, és csak kell használni, amikor erre szükség van a következő okok miatt:

* Nem használhat [ellenőrzőpontokat](#checkpoints) egy InlineScript blokkon belül. Ha hiba történik a blokkon belül, a blokk kezdetétől lehet folytatni.
* Nem használhat [párhuzamos végrehajtása](#parallel-processing) egy InlineScriptBlock belül.
* InlineScript hatással van a munkafolyamat méretezhetőségét, mert magánál tartja a Windows PowerShell-munkamenetben az InlineScript blokk teljes hosszán.

További információ az InlineScript használatával, lásd: [Windows PowerShell-parancsok futtatása munkafolyamatban](http://technet.microsoft.com/library/jj574197.aspx) és [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Párhuzamos feldolgozás
A Windows PowerShell-munkafolyamatok egyik előnye hajthatnak végre parancsokat párhuzamos ahelyett, hogy egymás után, mint a szokásos parancsfájlt.

Használhatja a **párhuzamos** kulcsszót a parancsprogram-blokkot tartalmazzon egyidejűleg futó parancsokat létrehozásához. Ez szintaxisa a következő alább látható. Ebben az esetben Activity1 és az Activity2 elindul egy időben. Activity3 csak az Activity1 és az Activity2 befejezése után elindul.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Vegyük példaként a következő PowerShell-parancsokat, amelyek több fájl másolása egy hálózati célra.  Ezek a parancsok egymás után futnak, úgy, hogy egy fájl Befejezés másolása a következő megkezdése előtt.     

    Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

Az alábbi munkafolyamat fut. ugyanezek a parancsok párhuzamosan, hogy az összes egyszerre másolásának megkezdése  Csak azok az összes után másolja a létrehozása után üzenet jelenik meg.

    Workflow Copy-Files
    {
        Parallel
        {
            Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }

        Write-Output "Files copied."
    }


Használhatja a **ForEach-Parallel** szerkezet használatával egyszerre egy gyűjtemény minden elemére feldolgozhat parancsokat. A gyűjtemény elemeinek feldolgozása párhuzamosan történik, amíg a parancsfájlblokkban lévő parancsok egymás után futnak. Ez szintaxisa a következő alább látható. Ebben az esetben Activity1 a gyűjtemény összes elemének azonos időpontban kezdődik. Minden elemhez a runbook az Activity2 Activity1 befejeződése után elindul. Activity3 csak az Activity1 és az Activity2 az összes befejezése után elindul.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

A következő példa az előző példában a fájlok másolása a párhuzamos hasonlít.  Ebben az esetben egy üzenet jelenik meg a fájl másolását követően.  Csak azután minden teljesen másolta az utolsó üzenet jelenik meg.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files)
        {
            Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }

        Write-Output "All files copied."
    }

> [!NOTE]
> A gyermekrunbookok a párhuzamosan futó, mivel ez kimutatták, hogy megbízhatatlan eredményekhez nem ajánlott.  Egyes esetekben a gyermekrunbook kimenetét nem jelenik meg, és egy gyermek runbook beállítás hatással lehet a más párhuzamos gyermekrunbookokra
>

## <a name="checkpoints"></a>Ellenőrzőpontok
A *ellenőrzőpont* az aktuális állapot, amely tartalmazza a változók aktuális értékét és bármi addig létrejött adott pontra a munkafolyamat egy pillanatkép. Ha egy munkafolyamatot fejeződik be a hiba, vagy fel van függesztve, majd a következő futtatáskor elindul, a munkafolyamat kezdetét helyett a legutóbbi ellenőrzőponttól.  Egy munkafolyamatban állíthat be ellenőrzőpontot a **Checkpoint-Workflow** tevékenység.

Az alábbi példakód a kivétel activity2 tevékenység után következik be, amely a munkafolyamat befejezéséhez. Ha a munkafolyamat fut újra, runbook az Activity2 futtatásával, hisz itt volt beállítva az utolsó ellenőrzőpont után kezdődik.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Akkor célszerű ellenőrzőpontokat beállítani egy munkafolyamat tevékenységek, amelyek nagyobb eséllyel okozhatnak kivételt, és nem kell ismételni, ha a munkafolyamat folytatása után. A munkafolyamat lehet, hogy hozzon létre például egy virtuális gépet. Beállíthat egy ellenőrzőpontot előtt és után a virtuális gép létrehozására szolgáló parancsok. Ha a létrehozás sikertelen, majd a parancsok volna ismételni, ha a munkafolyamat újra elindult. Ha a munkafolyamat meghiúsul, miután a létrehozás sikeresen befejeződik, majd a virtuális gép nem létrehozza újra a munkafolyamat folytatásakor.

Az alábbi példában több fájlokat másolja fel egy hálózati helyre, és beállítja az ellenőrzőpont után minden egyes fájl.  Ha a hálózati hely nem vesztek el, majd a munkafolyamat fejeződik be a hiba.  Ha ismét elindul, az utolsó ellenőrzőpont, ami azt jelenti, hogy csak a már másolt fájlok kimarad, fog folytatódni.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach ($File in $Files)
        {
            Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }

        Write-Output "All files copied."
    }

Mivel a username hitelesítő hívása után nem maradnak a [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) tevékenység vagy az utolsó ellenőrzőpont után be kell állítani a hitelesítő adatokat. null, és ezután kérheti le azokat újra után az eszköz áruházból **Suspend-Workflow** vagy ellenőrzőpont nevezik.  Ellenkező esetben a következő hibaüzenet jelenhet: *a munkafolyamat-feladatot nem lehet folytatni, vagy mert adatmegőrzési adatok nem teljesen mentve, vagy nem mentett adatok megőrzését sérült. A munkafolyamat újra kell indítani.*

A következő ugyanazt a kódot kezelni ezt a PowerShell-munkafolyamati forgatókönyvek mutatja be.

    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)

          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     }


Ez pedig nem szükséges vannak hitelesítéséhez a használatával egy egyszerű szolgáltatással konfigurált futtató fiókot.  

Az ellenőrzőpontok kapcsolatos további információkért lásd: [ellenőrzőpontok felvétele Parancsprogramos munkafolyamatba](http://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>További lépések
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
