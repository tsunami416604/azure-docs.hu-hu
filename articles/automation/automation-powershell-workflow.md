---
title: Az Azure Automation PowerShell-munkafolyamat tanulási
description: Ebből a cikkből, egy gyors leckében a Powershellt szerzői adott PowerShell és a PowerShell-munkafolyamat és fogalmak érvényes Automation-runbookok közötti különbségeket.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/14/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c5764c36a646b9639c0eb6463c39b9f014c4272d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168085"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Automatizálási runbookok Windows PowerShell-munkafolyamat alapfogalmak tanulási

Az Azure Automation Runbookjai Windows PowerShell-munkafolyamatként vannak megvalósítva.  Egy Windows PowerShell-munkafolyamat hasonlít a Windows PowerShell-szkriptet, de van néhány jelentős különbség, hogy egy új felhasználóhoz zavaró lehet.  Bár a célja, hogy ez a cikk segítséget nyújt a PowerShell-munkafolyamati runbookok írása, javasolt a PowerShell használatával, ha nincs szükség az ellenőrzőpontok runbookok írása.  Több szintaktikai különbségek vannak PowerShell-munkafolyamati runbookok létrehozásakor, és ezek a különbségek hatékony munkafolyamatokat ír egy kicsit több munkát igényel.

Egy munkafolyamat olyan programozott, összekapcsolódó lépések, amelyek hosszú ideig futó feladatok elvégzéséhez, vagy több eszközön vagy felügyelt csomóponton keresztül több lépést igénylik. Egy munkafolyamat képest előnyei a következők: a hogy képesek egyidejűleg elvégezni egy műveletet több eszközön, és lehetővé teszi, hogy automatikusan helyreálljon a hibák. Egy Windows PowerShell-munkafolyamat egy Windows PowerShell-parancsprogram, amely használja a Windows Workflow Foundation. A munkafolyamat Windows PowerShell-szintaxis-készült, és a Windows PowerShell használatával kell elindítani, amíg a munkafolyamat feldolgozását a Windows Workflow Foundation.

Ebben a cikkben a témakörök a részleteket lásd: [– első lépések a Windows PowerShell-munkafolyamat](https://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>A munkafolyamat alapvető szerkezete

Az első lépése egy PowerShell-munkafolyamat egy PowerShell-parancsprogram konvertálása van mellékelve azt a **munkafolyamat** kulcsszót.  Egy munkafolyamat kezdődik a **munkafolyamat** kulcsszót a parancsprogram kapcsos zárójelek között elhelyezett törzse követ. A munkafolyamat neve követi a **munkafolyamat** kulcsszó, ahogyan az a következő szintaxist:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

A munkafolyamat nevét meg kell egyeznie az Automation-runbook nevére. Ha a runbook importálása folyamatban van, majd a fájlnevet meg kell egyeznie a munkafolyamat nevével, és végződése *.ps1*.

Ha paramétereket szeretne felvenni a munkafolyamathoz, használja a **Param** ugyanúgy szkript kulcsszót.

## <a name="code-changes"></a>Kódmódosítások

PowerShell-munkafolyamati kód megjelenését majdnem teljesen megegyezik a PowerShell-szkriptkódot, néhány lényeges módosítás kivételével.  A következő szakaszok ismertetik, hogy egy PowerShell-parancsfájlba, a munkafolyamat futtatásához szükséges módosításokat.

### <a name="activities"></a>Tevékenységek

Egy tevékenység egy adott feladat egy munkafolyamatot belül. A parancsfájl áll egy vagy több, mint a munkafolyamat egy vagy több, sorrendben végrehajtott tevékenységek tevődik össze. Windows PowerShell-munkafolyamat automatikusan átalakítja a Windows PowerShell-parancsmagok számos tevékenységek egy munkafolyamat futtatásakor. Amikor megad e parancsmagok egyikét a runbookban, a megfelelő tevékenység Windows Workflow Foundation futtatja. Azok a parancsmagok nem tartozik megfelelő tevékenység, a Windows PowerShell-munkafolyamat automatikusan futtatja a parancsmagot belül egy [InlineScript](#inlinescript) tevékenység. Nincs parancsmagok, amelyek ki vannak zárva, és nem használható munkafolyamatban, kivéve, ha tünteti fel az InlineScript blokkon belüli egy készletét. Ezekről a fogalmakról további részletek: [tevékenységek használata parancsfájl-munkafolyamatok](https://technet.microsoft.com/library/jj574194.aspx).

Munkafolyamat-tevékenységek jellemzőkkel általános paraméterek konfigurálása a működésüket. Általános munkafolyamat-paraméterekkel kapcsolatos részletekért lásd: [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Pozícióparaméterek

Pozícióparaméterek tevékenységek és a egy munkafolyamat-parancsmagok nem használható.  Ez azt jelenti a paraméterneveket kell használnia.

Vegyük példaként a következő kódot, amely lekéri az összes futó szolgáltatásokat.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Ha megpróbálja ugyanazt a kódot futtatása egy munkafolyamatban, megjelenik-e az üzenet például a "paraméterkészlet nem oldható fel a megadott nevesített paraméterek használatával."  A probléma megoldására adja meg a paraméter neve, ahogy a következő.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Deszerializált objektum

A munkafolyamatokban objektumok deszerializálni vannak.  Ez azt jelenti, hogy a tulajdonságai továbbra is elérhetők, de nem a módszereket.  Vegyük példaként a következő PowerShell-kódot, amely leállítja a szolgáltatást a Stop metódus a Service objektum használatával.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Ha ez a munkafolyamat futtatása próbál kap hibaüzenetet "metódus meghívásának nem támogatott a Windows PowerShell-munkafolyamatban."

Egyik lehetőség, hogy ezen két kódsort a burkolása egy [InlineScript](#inlinescript) $Service ebben az esetben lenne egy service objektum a blokkon belül letiltása.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Egy másik lehetőség, hogy egy másik parancsmag, amely végrehajtja az módszer esetén azonos funkciókat használja, ha ilyen.  A mintánkban a Stop-Service-parancsmag a Stop metódus azonos funkciókat biztosít, és használhatja a következő egy munkafolyamathoz.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

A **InlineScript** tevékenység akkor hasznos, ha a kell futtatnia egy vagy több, mint a hagyományos PowerShell-parancsfájlt a PowerShell-munkafolyamat helyett.  Egy munkafolyamatban lévő parancsok feldolgozási Windows Workflow Foundation érkeznek, míg az InlineScript blokkon belüli parancsok dolgozza fel a Windows PowerShell.

InlineScript szintaxisa a következő alább látható.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Egy InlineScript a kimeneti változóra való hozzárendelésével adhat vissza kimenetet. A következő példa leállítja a szolgáltatást, és majd megjeleníti a szolgáltatás nevét.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Az InlineScript blokkon belüli is átadhat értékeket, de kell használnia **$Using** hatókör-módosítót.  A következő példa megegyezik az előző példával, azzal a különbséggel, hogy a szolgáltatásnév egy változó által biztosított.

```powershell
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
```

Bár a InlineScript tevékenységet bizonyos munkafolyamatok kritikus fontosságú, nem támogatják a munkafolyamat szerkezeteket, és csak a következő okokból szükség esetén használható:

* Nem használhat [ellenőrzőpontok](#checkpoints) egy InlineScript blokkon belül. Ha hiba történik a blokkon belül, a blokk kezdetétől fogva lehet folytatni.
* Nem használhat [párhuzamos végrehajtása](#parallel-processing) egy InlineScriptBlock belül.
* InlineScript méretezhetőségi, a munkafolyamat hatással van, mert magánál tartja a Windows PowerShell-munkamenet az InlineScript blokk teljes hosszán.

InlineScript használatával kapcsolatos további információkért lásd: [Windows PowerShell-parancsok futtatása munkafolyamatban](https://technet.microsoft.com/library/jj574197.aspx) és [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Párhuzamos feldolgozás

Windows PowerShell-munkafolyamatok egyik előnye a hajthatnak végre parancsokat helyett párhuzamosan egymás után csakúgy, mint a szokásos parancsfájlt.

Használhatja a **párhuzamos** kulcsszó használatával hozzon létre egy parancsprogram-blokkot, amelyek egyidejűleg futtathatók több parancsokkal. Ez az alább látható a következő szintaxist használ. Ebben az esetben hogy az Activity1 és az Activity2 elindítja egy időben. Az Activity3 indítja el, csak az Activity1 és az Activity2 befejeződése után.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Vegyük példaként a következő PowerShell-parancsokat, amelyek több fájlok másolása egy hálózati célhelyre.  Ezek a parancsok egymás után futnak, úgy, hogy egy fájl Befejezés másolása a következő indítása előtt.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Az alábbi munkafolyamat ugyanazokat a parancsokat párhuzamosan fut, hogy az összes elindítja másolás egy időben.  Csak azután legyenek az összes másolt a befejezési üzenetet jelenik meg.

```powershell
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
```

Használhatja a **ForEach-Parallel** szerkezet használatával egyidejűleg egy gyűjtemény minden elemére feldolgozhat parancsokat. A gyűjtemény elemeinek feldolgozása párhuzamosan, míg a parancsfájlblokkban lévő parancsok egymás után futnak. Ez az alább látható a következő szintaxist használ. Ebben az esetben Activity1 elindítja a gyűjtemény összes eleme egy időben. Minden egyes elem esetében a runbook az Activity2 Activity1 befejeződése után elindul. Az Activity3 indítja el, csak azután az Activity1 és az Activity2 is befejeződött az összes elemet. Használjuk a `ThrottleLimit` korlátozza a párhuzamosságot a paramétert. Túl magas a `ThrottleLimit` problémákat okozhat. Az ideális értékét a `ThrottleLimit` paraméter a környezetben számos tényezőtől függ. Próbálja ki az alacsony érték kezdő kell, és próbálja meg növekvő értékei eltérőek, amíg meg nem találja, amely együttműködik a konkrét körülmény a.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Az alábbi példa az előző példában a fájlok másolása a párhuzamos hasonlít.  Ebben az esetben egy üzenet jelenik meg az egyes fájlok másolását követően.  Csak azután legyenek minden fejeződött be a másolása a végső befejezési üzenetet jelenik meg.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Gyermek runbookok a párhuzamosan futó, mivel ez kimutatták, hogy megbízhatatlan eredményekhez nem ajánlott. Néha a gyermekrunbook kimenetét nem jelenik meg, és a egy gyermek runbook beállítás hatással lehet a többi párhuzamos gyermekrunbookokra. Változók, például a $VerbosePreference, $WarningPreference, a gyermek runbook számára nem lesznek továbbítva. És ezeket az értékeket a gyermekrunbookok módosításakor, előfordulhat, hogy nem megfelelően után visszaállították meghívása.

## <a name="checkpoints"></a>Ellenőrzőpontok

A *ellenőrzőpont* egy pillanatkép, amely tartalmazza a változók aktuális értékét és bármi addig létrejött adott pontra a munkafolyamat aktuális állapotáról. Ha egy munkafolyamatot ér véget a hibás, vagy fel van függesztve, majd a következő futtatáskor elindul, az utolsó ellenőrzőponttól helyett a munkafolyamat elején.  Ön állíthat be ellenőrzőpontot egy munkafolyamatban a **Checkpoint-Workflow** tevékenység. Az Azure Automation szolgáltatás rendelkezik [igazságos elosztás](automation-runbook-execution.md#fair-share), ahol bármelyik runbookhoz, amely 3 óráig fut. van eltávolítva a memóriából, hogy a többi runbookok futtatását. Végül a memóriából runbook lesz töltve, és, ha azt a legutóbbi ellenőrzőponttól venni a runbook végrehajtása folytatódik. Így garantálható, hogy a runbook idővel fog befejeződni, hozzá kell adnia az ellenőrzőpontokat, amely kisebb, mint 3 óra időközönként. Ha minden egyes futtatás során egy új ellenőrzőpont hozzáadásakor, ha a runbook egy hiba miatt 3 óra után lekérdezi zárni, majd a runbook folytatódik határozatlan időre.

A következő mintakód a kivétel létrejötte után a runbook az Activity2 okoz a munkafolyamat befejezéséhez. Esetén a munkafolyamat fut újra, mivel itt volt beállítva az utolsó ellenőrzőpont runbook az Activity2 futtatásával kezdődik.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

A munkafolyamat célszerű ellenőrzőpontokat beállítani, tevékenységeket, kivétel eséllyel okozhatnak, és nem kell ismételni, ha a munkafolyamat folytatása után. A munkafolyamat például egy virtuális gépet is létrehozhat. Ön előtt és után a virtuális gép létrehozása a parancsok beállítani egy ellenőrzőpontot. Ha a létrehozás sikertelen, majd a parancsok lenne ismételni, ha újra elindult a munkafolyamat. Ha a munkafolyamat meghiúsul, miután a létrehozás sikeresen befejeződik, majd a virtuális gép nem lesz létrehozva újra a munkafolyamat folytatásakor.

Az alábbi példa több fájlt másol egy hálózati helyre, és beállítja egy ellenőrzőpont után minden egyes fájl.  A hálózati hely nem vesztek el, ha a munkafolyamat hiba befejeződik.  Amikor ismét elindul, folytatódik, ami azt jelenti, hogy csak azokat a fájlokat, amelyek már át lettek másolva a rendszer kihagyja az utolsó ellenőrzőpont.

```powershell
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
```

Mivel a felhasználónév hitelesítő adatok nem rögzíti meghívása után a [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) tevékenység vagy az utolsó ellenőrzőpont után be kell állítani, null, és majd az eszköz áruházból után újra lekérheti azokat a hitelesítő adatok  **Munkafolyamat-felfüggesztési** vagy ellenőrzőpont nevezzük.  Ellenkező esetben a következő hibaüzenet jelenhet meg: *A munkafolyamat-feladatot nem lehet folytatni, vagy mert adatmegőrzés adatok nem teljesen mentve, vagy nem mentett adatok megőrzése sérült. A munkafolyamat újra kell indítani.*

A következő ugyanazt a kódot ennek kezelése a PowerShell-munkafolyamati runbookok mutatja be.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzureAutomationCredential -Name "MyCredential"
    $null = Connect-AzureRmAccount -Credential $Cred

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
        $null = Connect-AzureRmAccount -Credential $Cred
        }
}
```

> [!IMPORTANT]
> **Add-AzureRmAccount** alias már **Connect-AzureRMAccount**. Ha a Keresés a szalagtár elemmel, ha nem látja, akkor **Connect-AzureRMAccount**, használhat **Add-AzureRmAccount**, vagy frissítheti az a modulokat az Automation-fiókban.

Ez azonban nem kötelező, ha egy egyszerű szolgáltatással konfigurált futtató fiók használatával hitelesít.

Az ellenőrzőpontok kapcsolatos további információkért lásd: [ellenőrzőpontok felvétele Parancsprogramos munkafolyamatba](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>További lépések

* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)

