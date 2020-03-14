---
title: Azure Automation PowerShell-munkafolyamatának megismerése
description: Ez a cikk a PowerShellt és a PowerShellt, valamint az Automation runbookok kapcsolatos fogalmakat és a PowerShell-munkafolyamatok közötti konkrét különbségeket ismertető gyors lecke.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 6e4c8057322b6208ea3b447b264e2bde1344540c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278687"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>A Windows PowerShell munkafolyamat-fogalmai az Automation runbookok

A Azure Automationban lévő runbookok Windows PowerShell-munkafolyamatként vannak implementálva.  A Windows PowerShell-munkafolyamatok hasonlóak egy Windows PowerShell-parancsfájlhoz, de vannak olyan jelentős eltérések, amelyek zavaróak lehetnek az új felhasználók számára.  Habár ez a cikk segítséget nyújt a runbookok PowerShell-munkafolyamattal való írásához, javasoljuk, hogy az runbookok-et a PowerShell használatával írja elő, ha ellenőrzőpontokra van szüksége.  A PowerShell-munkafolyamat runbookok készítése során több szintaktikai különbség is van, és ezek a különbségek egy kicsit több munkát igényelnek a hatékony munkafolyamatok írásához.

Egy munkafolyamat olyan programozott, összekapcsolódó lépések, amelyek hosszú ideig futó feladatok elvégzéséhez, vagy több eszközön vagy felügyelt csomóponton keresztül több lépést igénylik. Egy munkafolyamat képest előnyei a következők: a hogy képesek egyidejűleg elvégezni egy műveletet több eszközön, és lehetővé teszi, hogy automatikusan helyreálljon a hibák. A Windows PowerShell-munkafolyamat egy Windows Workflow Foundationt használó Windows PowerShell-parancsfájl. A munkafolyamat Windows PowerShell-szintaxis-készült, és a Windows PowerShell használatával kell elindítani, amíg a munkafolyamat feldolgozását a Windows Workflow Foundation.

A cikkben szereplő témakörökről a [első lépések a Windows PowerShell-munkafolyamattal](https://technet.microsoft.com/library/jj134242.aspx)című témakörben olvashat bővebben.

## <a name="basic-structure-of-a-workflow"></a>Munkafolyamatok alapszintű szerkezete

A PowerShell-parancsfájlok PowerShell-munkafolyamatra való átalakításának első lépése a **munkafolyamat** kulcsszava.  A munkafolyamatok a **munkafolyamat** -kulcsszóval kezdődnek, majd a szkript törzse kapcsos zárójelek közé. A munkafolyamat neve követi a **munkafolyamat** -kulcsszót, ahogyan az az alábbi szintaxisban látható:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

A munkafolyamat nevét meg kell egyeznie az Automation-runbook nevére. Ha a runbook importálása folyamatban van, a fájlnévnek meg kell egyeznie a munkafolyamat nevével, és a *. ps1*végződéssel kell végződnie.

Ha paramétereket szeretne hozzáadni a munkafolyamathoz, használja a **param** kulcsszót ugyanúgy, mint egy parancsfájlt.

## <a name="code-changes"></a>Kódmódosítások

A PowerShell-munkafolyamat kódja majdnem azonos a PowerShell-szkript kódjával, kivéve néhány jelentős változást.  A következő szakaszok azokat a módosításokat ismertetik, amelyeket a PowerShell-szkriptek futtatásához kell végeznie egy munkafolyamatban.

### <a name="activities"></a>Tevékenységek

Egy tevékenység egy adott feladat egy munkafolyamatot belül. A parancsfájl áll egy vagy több, mint a munkafolyamat egy vagy több, sorrendben végrehajtott tevékenységek tevődik össze. Windows PowerShell-munkafolyamat automatikusan átalakítja a Windows PowerShell-parancsmagok számos tevékenységek egy munkafolyamat futtatásakor. Ha megadja a parancsmagok egyikét a runbook, a Windows Workflow Foundation a megfelelő tevékenységet futtatja. Ezeknek a parancsmagoknak a megfelelő tevékenység nélkül a Windows PowerShell-munkafolyamat automatikusan futtatja a parancsmagot egy [InlineScript](#inlinescript) -tevékenységen belül. A parancsmagok egy halmaza ki van zárva, és nem használható munkafolyamatban, csak akkor, ha explicit módon egy InlineScript blokkban tünteti fel a szóban forgó parancsmagokat. További információ ezekről a fogalmakról: [tevékenységek használata a parancsfájl-munkafolyamatokban](https://technet.microsoft.com/library/jj574194.aspx).

Munkafolyamat-tevékenységek jellemzőkkel általános paraméterek konfigurálása a működésüket. Az általános munkafolyamat-paraméterekkel kapcsolatos részletekért lásd: [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Pozíciós paraméterek

A munkafolyamatokban nem használhat pozíciós paramétereket tevékenységekkel és parancsmagokkal.  Mindez azt jelenti, hogy paraméterek nevét kell használnia.

Vegyük például a következő kódot, amely az összes futó szolgáltatást lekéri.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Ha ugyanezt a kódot próbálja meg futtatni egy munkafolyamatban, egy üzenet jelenik meg, például a "Parameters set nem oldható fel a megadott nevesített paraméterek használatával".  Ennek kijavítania a paraméter nevét a következő módon adja meg.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Deszerializált objektumok

A munkafolyamatokban lévő objektumok deszerializálva vannak.  Ez azt jelenti, hogy a tulajdonságaik továbbra is elérhetők, de nem a módszereiket.  Vegyük például a következő PowerShell-kódot, amely leállítja a szolgáltatást a szolgáltatás objektumának leállítás metódusával.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Ha egy munkafolyamatban próbál futtatni, a "metódus meghívása nem támogatott Windows PowerShell-munkafolyamatokban" hibaüzenet jelenik meg.

Az egyik lehetőség, hogy becsomagolja ezt a két sornyi kódot egy [InlineScript](#inlinescript) -blokkba, amelyben $Service a blokkon belüli szolgáltatási objektum lenne.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Egy másik lehetőség egy másik parancsmag használata, amely a metódussal megegyező funkciót hajt végre, ha van ilyen.  A példában a stop-Service parancsmag ugyanazokat a funkciókat biztosítja, mint a stop metódus, és a következőt használhatja egy munkafolyamathoz.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

A **InlineScript** tevékenység akkor hasznos, ha egy vagy több parancsot hagyományos PowerShell-parancsfájlként kell futtatnia a PowerShell-munkafolyamat helyett.  Egy munkafolyamatban lévő parancsok feldolgozási Windows Workflow Foundation érkeznek, míg az InlineScript blokkon belüli parancsok dolgozza fel a Windows PowerShell.

A InlineScript az alábbi, alább látható szintaxist használja.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

A kimenetet egy InlineScript adhatja vissza, ha a kimenetet egy változóhoz rendeli. A következő példa leállítja a szolgáltatást, majd kiírja a szolgáltatás nevét.

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

Értékeket adhat át egy InlineScript blokkba, de **$using** hatókör-módosítót kell használnia.  A következő példa megegyezik az előző példával, azzal a különbséggel, hogy a szolgáltatás nevét egy változó adja meg.

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

Noha a InlineScript tevékenységek bizonyos munkafolyamatokban kritikus fontosságúak lehetnek, nem támogatják a munkafolyamat-szerkezeteket, és csak akkor használhatók, ha az a következő okok miatt szükséges:

* Az [ellenőrzőpontok](#checkpoints) nem használhatók InlineScript blokkon belül. Ha hiba történik a blokkon belül, azt a blokk elejétől kell folytatni.
* InlineScriptBlock belül nem használható [párhuzamos végrehajtás](#parallel-processing) .
* A InlineScript hatással van a munkafolyamat skálázhatóságára, mivel a Windows PowerShell-munkamenetet a InlineScript-blokk teljes hosszára vonatkozóan tárolja.

További információ a InlineScript használatáról: [Windows PowerShell-parancsok futtatása munkafolyamatban](https://technet.microsoft.com/library/jj574197.aspx) és [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Párhuzamos feldolgozás

Windows PowerShell-munkafolyamatok egyik előnye a hajthatnak végre parancsokat helyett párhuzamosan egymás után csakúgy, mint a szokásos parancsfájlt.

A **Parallel** kulcsszóval létrehozhat egy parancsfájl-blokkot több, párhuzamosan futó paranccsal. Ez a következő szintaxist használja. Ebben az esetben a Activity1 és a Activity2 egy időben indul el. A Activity3 csak akkor indul el, ha a Activity1 és a Activity2 is befejeződött.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Vegyük például a következő PowerShell-parancsokat, amelyek több fájlt másolnak egy hálózati célhelyre.  Ezek a parancsok szekvenciálisan futnak, így az egyik fájlnak a következő elindítása előtt be kell fejeznie a másolást.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

A következő munkafolyamat párhuzamosan futtatja ugyanezeket a parancsokat, így azok mindegyike egyszerre kezdi a másolást.  A befejezési üzenet csak azt követően jelenik meg, hogy az összes másolása megtörtént.

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

A **foreach-Parallel** szerkezet használatával a gyűjtemények minden egyes eleménél egyszerre dolgozhat fel parancsokat. A gyűjtemény elemeinek feldolgozása párhuzamosan, míg a parancsfájlblokkban lévő parancsok egymás után futnak. Ez a következő szintaxist használja. Ebben az esetben a Activity1 egy időben indul el a gyűjtemény összes eleméhez. Minden elem esetében a Activity2 a Activity1 befejezése után indul el. A Activity3 csak akkor indul el, ha a Activity1 és a Activity2 is befejeződött az összes elemnél. A párhuzamosság korlátozásához a `ThrottleLimit` paramétert használjuk. Túl magas a `ThrottleLimit` problémákhoz vezethet. A `ThrottleLimit` paraméter ideális értéke a környezetében számos tényezőtől függ. Próbálkozzon alacsony értékkel, és próbálkozzon különböző növekvő értékekkel, amíg meg nem találja az adott körülménynek megfelelőt.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Az alábbi példa hasonló az előző példában található fájlok párhuzamos másolásához.  Ebben az esetben egy üzenet jelenik meg az egyes fájlokhoz a másolás után.  Csak azt követően, hogy az összes teljes másolása megtörtént, a végső befejezési üzenet jelenik meg.

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
> Nem javasoljuk, hogy párhuzamosan futtassa a gyermek runbookok, mivel ez a művelet megbízhatatlan eredményt ad. A gyermek runbook kimenete időnként nem jelenik meg, és egy gyermek runbook beállításai befolyásolhatják a másik párhuzamos gyermek runbookok. Előfordulhat, hogy a $VerbosePreference, $WarningPreference és mások változói nem lesznek propagálva a gyermek runbookok. Ha a gyermek runbook megváltoztatja ezeket az értékeket, előfordulhat, hogy a hívás után nem lehet megfelelően visszaállítani őket.

## <a name="checkpoints"></a>Az ellenőrzőpontok

Az *ellenőrzőpont* a munkafolyamat aktuális állapotának pillanatképe, amely tartalmazza a változók aktuális értékét, valamint az adott ponthoz generált összes kimenetet. Ha egy munkafolyamat hibát jelez vagy felfüggesztve van, akkor a következő futtatásakor a rendszer a munkafolyamat kezdete helyett az utolsó ellenőrzőponttól indul el.  Az ellenőrzőpont **-munkafolyamat** tevékenységgel beállíthatja az ellenőrzőpontot egy munkafolyamatban. Azure Automation rendelkezik egy [Fair Share](automation-runbook-execution.md#fair-share)nevű funkcióval, ahol a 3 órán futó összes runbook el lett távolítva a más runbookok futtatásának engedélyezéséhez. Végül a memóriában lévő eltávolított runbook újra lesz töltve, és ha igen, akkor a rendszer a runbook utolsó ellenőrzőpontján folytatja a végrehajtást. Annak biztosítása érdekében, hogy a runbook végül is befejeződjön, a 3 óránál rövidebb ideig futó ellenőrzőpontokat kell hozzáadnia. Ha az egyes futtatások során új ellenőrzőpontot ad hozzá, és ha a runbook egy hiba miatt 3 óra elteltével kizárja a rendszer, akkor a runbook határozatlan időre folytatódik.

A következő mintakód kivételt jelent a munkafolyamat befejezését okozó Activity2 után. Ha a munkafolyamat újra fut, a Activity2 futtatásával indul el, mivel ez az utolsó ellenőrzőpont-beállítás után volt.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Az ellenőrzőpontokat a munkafolyamatokban olyan tevékenységek után kell beállítani, amelyek a kivételt okozhatják, és nem ismételhetők meg, ha a munkafolyamat folytatódik. Előfordulhat például, hogy a munkafolyamat létrehoz egy virtuális gépet. Ön előtt és után a virtuális gép létrehozása a parancsok beállítani egy ellenőrzőpontot. Ha a létrehozás sikertelen, akkor a rendszer megismétli a parancsokat, ha a munkafolyamatot újra elindítják. Ha a munkafolyamat a létrehozás sikeressége után meghiúsul, akkor a rendszer nem hozza létre újra a virtuális gépet a munkafolyamat folytatásakor.

Az alábbi példa egy hálózati helyre másol több fájlt, és minden fájl után beállítja az ellenőrzőpontot.  Ha a hálózati hely elvész, a munkafolyamat hibát jelez.  Ha újra elindítják, az utolsó ellenőrzőponton folytatódik, ami azt jelenti, hogy csak a már másolt fájlok lesznek kihagyva.

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

Mivel a Felhasználónév hitelesítő adatai nem maradnak meg a [felfüggesztési munkafolyamat](https://technet.microsoft.com/library/jj733586.aspx) tevékenységének meghívása vagy az utolsó ellenőrzőpont után, a hitelesítő adatokat NULL értékre kell állítania, majd újra le kell kérni őket az objektum-tárolóból a **felfüggesztés – munkafolyamat** vagy ellenőrzőpont hívása után.  Ellenkező esetben a következő hibaüzenet jelenhet meg: *a munkafolyamat-feladatot nem lehet folytatni, mert az adatmegőrzési adatok nem menthetők teljesen, vagy a mentett adatmegőrzési adatok sérültek. Újra kell indítania a munkafolyamatot.*

A következő kód bemutatja, hogyan kezelheti ezt a PowerShell-munkafolyamatok runbookok.

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
> A **Add-AzureRmAccount** mostantól egy alias a **kapcsolat-AzureRmAccount**. Ha nem látja a **AzureRMAccount**, használhatja a **AzureRMAccount**, vagy frissítheti a modulokat az Automation-fiókban.

Erre akkor van szükség, ha a hitelesítést egy egyszerű szolgáltatásnév használatával konfigurált futtató fiókkal végzi.

Az ellenőrzőpontokkal kapcsolatos további információkért lásd: [ellenőrzőpontok hozzáadása egy parancsfájl-munkafolyamathoz](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Következő lépések

* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)

