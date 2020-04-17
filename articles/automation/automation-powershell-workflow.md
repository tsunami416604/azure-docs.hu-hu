---
title: PowerShell-munkafolyamat tanulása az Azure Automationhez
description: Ez a cikk egy gyors lecke a PowerShellt jól ismerő szerzők számára a PowerShell és a PowerShell-munkafolyamat közötti konkrét különbségek, valamint az Automation runbookok ra vonatkozó fogalmak megismeréséhez.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 1b275239c19584bc11472711a32972aa3ebea1ab
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457535"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>A Windows PowerShell-munkafolyamat legfontosabb fogalmai az automatizálási runbookokhoz

Az Azure Automation runbookjai Windows PowerShell-munkafolyamatként vannak megvalósítva.  A Windows PowerShell-munkafolyamat hasonló a Windows PowerShell-parancsfájlhoz, de van néhány jelentős különbség, amely zavaró lehet egy új felhasználó számára.  Bár ez a cikk a PowerShell-munkafolyamat használatával a runbookok írásának elősegítésére szolgál, javasoljuk, hogy runbookokat írjon a PowerShell használatával, kivéve, ha ellenőrzőpontokra van szüksége.  A PowerShell-munkafolyamat runbookjainak készítésekor számos szintaxiskülönbség van, és ezek a különbségek egy kicsit több munkát igényelnek a hatékony munkafolyamatok írásához.

A munkafolyamat olyan programozott, összekapcsolódó lépések sora, amik hosszan futó feladatokat végeznek el vagy több eszközön vagy felügyelt csomóponton keresztül végrehajtandó, több lépés koordinációját igénylik. A munkafolyamatok előnye az egyszerű parancsprogramokhoz képest részben az, hogy képesek egyidejűleg elvégezni egy műveletet több eszközön, továbbá az, hogy képesek automatikusan helyreállni hibák után. A Windows PowerShell-munkafolyamat egy Windows PowerShell-parancsfájl, amely a Windows Workflow Foundation rendszert használja. Bár a munkafolyamatot a Windows PowerShell szintaxisával kell megírni, és a Windows PowerShell használatával kell elindítani, a munkafolyamat feldolgozását a Windows Workflow Foundation végzi.

A cikkben található témakörökről az [Első lépések a Windows PowerShell-munkafolyamattal című](https://technet.microsoft.com/library/jj134242.aspx)témakörben olvashat részletesen.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="basic-structure-of-a-workflow"></a>A munkafolyamat alapvető szerkezete

A PowerShell-parancsfájlPowerShell-munkafolyamattá történő konvertálásának első `Workflow` lépése a kulcsszót.  A munkafolyamat a `Workflow` kulcsszóval kezdődik, amelyet a kapcsos zárójelbe zárt parancsfájl törzse követ. A munkafolyamat neve a `Workflow` kulcsszót követi az alábbi szintaxisban látható módon:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

A munkafolyamat nevének meg kell egyeznie az Automation runbook nevével. Ha a runbook importálása folyamatban van, akkor a fájlnévnek meg kell egyeznie a munkafolyamat nevével, és .ps1 végződésre kell *végződnie.*

Ha paramétereket szeretne hozzáadni a `Param` munkafolyamathoz, ugyanúgy használja a kulcsszót, mint egy parancsfájlban.

## <a name="code-changes"></a>Kódmódosítások

A PowerShell munkafolyamat-kódja majdnem megegyezik a PowerShell parancsfájlkódjával, kivéve néhány jelentős módosítást.  A következő szakaszok ismertetik a módosításokat, amelyeket el kell végrehajtania egy PowerShell-parancsfájlon ahhoz, hogy egy munkafolyamatban fusson.

### <a name="activities"></a>Tevékenységek

Egy tevékenység egy adott feladat egy munkafolyamatot belül. Ugyanúgy, ahogy a parancsfájl egy vagy több parancsból áll, a munkafolyamat egy vagy több, sorrendben végrehajtott tevékenységből áll. A Windows PowerShell Workflow futtatórendszer automatikusan tevékenységgé alakít számos Windows PowerShell-parancsmagot egy munkafolyamat futtatásakor. Ha megadja ezeket a parancsmagokat a runbookban, a megfelelő tevékenységet a Windows Workflow Foundation futtatja. A megfelelő tevékenység nélküli parancsmagok esetében a Windows PowerShell-munkafolyamat automatikusan futtatja a parancsmatot egy [InlineScript-tevékenységen](#inlinescript) belül. Van egy parancsmagkészlet, amely ki van zárva, és nem használható a munkafolyamatban, kivéve, ha explicit módon szerepel nekik egy InlineScript blokkban. Ezekről a fogalmakról további információt a [Tevékenységek használata parancsfájl-munkafolyamatokban című témakörben talál.](https://technet.microsoft.com/library/jj574194.aspx)

A munkafolyamatok tevékenységeinek működése közös paraméterek egy halmazával konfigurálható. A munkafolyamat közös paramétereiről a [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx)című témakörben talál részleteket.

### <a name="positional-parameters"></a>Pozicionálási paraméterek

A pozíciós paraméterek nem használhatók tevékenységekkel és parancsmagokkal a munkafolyamatban.  Ez azt jelenti, hogy paraméterneveket kell használnia.

Vegyük például a következő kódot, amely megkapja az összes futó szolgáltatást.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Ha ugyanezt a kódot próbálja futtatni egy munkafolyamatban, egy olyan üzenet jelenik meg, mint például: "A paraméterkészlet nem oldható fel a megadott elnevezett paraméterekkel."  A hiba kijavításához adja meg a paraméter nevét az alábbiak szerint.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Deszerializált objektumok

A munkafolyamatokban lévő objektumok deszerializálódnak.  Ez azt jelenti, hogy tulajdonságaik továbbra is elérhetők, de a metódusaik nem.  Fontolja meg például a következő PowerShell-kódot, amely leállít egy szolgáltatást a Service-objektum Stop metódusával.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Ha ezt munkafolyamatban próbálja futtatni, a következő hibaüzenet jelenik meg: "A metódusmeghívás nem támogatott a Windows PowerShell-munkafolyamatban."

Az egyik lehetőség az, hogy ezt a két sorkódot egy [InlineScript](#inlinescript) blokkban csomagolja, amely esetben $Service szolgáltatásobjektum lenne a blokkon belül.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Egy másik lehetőség egy másik parancsmag használata, amely ugyanazt a funkciót hajtja végre, mint a metódus, ha van ilyen.  A mintában a Stop-Service parancsmag ugyanazokat a funkciókat biztosítja, mint a Stop metódus, és használhatja a következő munkafolyamathoz.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

A`InlineScript` tevékenység akkor hasznos, ha egy vagy több parancsot kell futtatnia hagyományos PowerShell-parancsfájlként a PowerShell-munkafolyamat helyett.  Míg a munkafolyamaton belüli parancsokat a szolgáltatás a Windows Workflow Foundation rendszernek küldi el feldolgozásra, az InlineScript blokkon belüli parancsok feldolgozását a Windows PowerShell végzi.

Az InlineScript az alábbi szintaxist használja.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Az InlineScript kimenetét úgy adja vissza, hogy a kimenetet egy változóhoz rendeli. A következő példa leállít egy szolgáltatást, majd kiadja a szolgáltatás nevét.

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

Az értékeket átadhatja egy InlineScript blokkba, de **$Using** hatókör-módosítót kell használnia.  A következő példa megegyezik az előző példával, azzal a különbséggel, hogy a szolgáltatás nevét egy változó adja meg.

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

Bár az InlineScript-tevékenységek bizonyos munkafolyamatokban kritikus fontosságúak lehetnek, nem támogatják a munkafolyamat-konstrukciókat, és csak akkor használhatók, ha szükséges a következő okok miatt:

* Az InlineScript-blokkon belüli [ellenőrzőpontok](#checkpoints) nem használhatók. Ha hiba történik a blokkon belül, azt a blokk elejétől kell folytatni.
* Az InlineScriptBlock blokkon belül nem használható [párhuzamos végrehajtás.](#parallel-processing)
* Az InlineScript hatással van a munkafolyamat méretezhetőségére, mivel az InlineScript-blokk teljes hosszában megtartja a Windows PowerShell-munkamenetet.

Az InlineScript használatával kapcsolatos további tudnivalókért olvassa el [a Windows PowerShell-parancsok futtatása munkafolyamatban](https://technet.microsoft.com/library/jj574197.aspx) és [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx)című témakört.

## <a name="parallel-processing"></a>Párhuzamos feldolgozás

A Windows PowerShell-munkafolyamatok egyik előnye, hogy az általános parancsprogramoktól eltérően párhuzamosan is végre tudnak hajtani parancsokat, nem csak egymást követően.

A `Parallel` kulcsszó segítségével parancsfájlblokkot hozhat létre több, egyidejűleg futó paranccsal. Ez az alábbi szintaxist használja. Ebben az esetben az Activity1 és az Activity2 egyidejűleg kezdődik. Az Activity3 csak az Activity1 és az Activity2 befejezése után indul el.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Vegyük például a következő PowerShell-parancsokat, amelyek több fájlt másolnak egy hálózati célhelyre.  Ezek a parancsok egymás után futnak, így az egyik fájlnak be kell fejeznie a másolást a következő indításelőtt.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

A következő munkafolyamat ezeket a parancsokat párhuzamosan futtatja, így mindegyik egyszerre kezdi el a másolást.  Csak az összes másolásután jelenik meg a befejezési üzenet.

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

A `ForEach -Parallel` konstruktus segítségével egyidejűleg feldolgozhat parancsokat a gyűjtemény egyes eleméhez. A gyűjtemény elemeinek feldolgozása párhuzamosan történik, míg a parancsblokkban levő parancsok egymást követően futnak le. Ez az alábbi szintaxist használja. Ebben az esetben az Activity1 egy időben kezdődik a gyűjtemény összes eleméhez. A Activity2 tevékenységminden egyes elem esetében a Activity1 befejezése után kezdődik. A Tevékenység3 csak akkor indul el, ha az Activity1 és az Activity2 is befejeződött az összes cikkhez. A paramétert használjuk a `ThrottleLimit` párhuzamosság korlátozására. Túl magas `ThrottleLimit` a problémákat okozhat. A paraméter ideális `ThrottleLimit` értéke számos tényezőtől függ a környezetben. Meg kell próbálnia kezdeni egy alacsony értékű, és próbálja meg a különböző növekvő értékeket, amíg meg nem találja az egyik, hogy működik az adott körülmények között.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

A következő példa hasonló az előző példa fájlok másolása párhuzamosan.  Ebben az esetben a másolás után minden fájlhoz megjelenik egy üzenet.  Csak miután teljesen átmásolta őket, megjelenik a végső befejezési üzenet.

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
> Nem javasoljuk a gyermek runbookok párhuzamos futtatását, mivel ez nem megbízható eredményeket eredményez. A gyermek runbook kimenete néha nem jelenik meg, és az egyik gyermek runbook beállításai hatással lehetnek a másik párhuzamos gyermek runbookok. Az olyan változók, mint a $VerbosePreference, $WarningPreference és mások, nem terjeszthetők a gyermek runbookok. És ha a gyermek runbook módosítja ezeket az értékeket, előfordulhat, hogy nem megfelelően visszahívás után vissza.

## <a name="checkpoints"></a>Ellenőrzőpontok

Az *ellenőrzőpont* a munkafolyamat aktuális állapotának pillanatképe, amely tartalmazza a változók aktuális értékét és az adott ponthoz létrehozott kimeneteket. Ha egy munkafolyamat hibásan végződik vagy fel van függesztve, akkor a következő futtatáskor a munkafolyamat kezdete helyett az utolsó ellenőrzőpontról indul el.  A `Checkpoint-Workflow` tevékenységgel rendelkező munkafolyamatban beállíthat egy ellenőrzőpontot. Az Azure Automation rendelkezik egy [méltányos megosztás](automation-runbook-execution.md#fair-share)nevű funkcióval, ahol a 3 órán keresztül futó runbookok eltávolítása lehetővé teszi más runbookok futtatását. Végül a terhelés nélkül töltött runbook újratöltődik, és ha ez megtörténik, akkor folytatja a runbook utolsó ellenőrzőpontjáról történő végrehajtást. Annak érdekében, hogy a runbook végül befejeződjön, 3 óránál rövidebb ideig futó időközönként ellenőrzőpontokat kell hozzáadnia. Ha minden egyes futtatás során egy új ellenőrzőpont kerül hozzáadásra, és ha a runbook 3 óra elteltével egy hiba miatt kilakoltatva lesz, akkor a runbook határozatlan ideig folytatódik.

A következő mintakódban kivétel történik az Activity2 után, ami a munkafolyamat befejezéséhez okoz. Amikor a munkafolyamat újra fut, az Activity2 futtatásával kezdődik, mivel ez közvetlenül az utolsó ellenőrzőpont-készlet után történt.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Ellenőrzőpontokat kell beállítania a munkafolyamatban olyan tevékenységek után, amelyek kivételre hajlamosak lehetnek, és nem szabad megismételni, ha a munkafolyamat folytatódik. Például a munkafolyamat létrehozhat egy virtuális gépet. Célszerű beállítani egy ellenőrzőpontot a virtuális gép létrehozására szolgáló parancsok előtt és után is. Ha a létrehozás sikertelen, akkor a parancsok megismétlődnek, ha a munkafolyamat újra indul. Ha a munkafolyamat a létrehozás sikeresse után sikertelen, akkor a virtuális gép nem jön létre újra a munkafolyamat folytatásakor.

A következő példa több fájlt másol egy hálózati helyre, és minden fájl után beállít egy ellenőrzőpontot.  Ha a hálózati hely elvész, a munkafolyamat hiba miatt ér véget.  Amikor újra indul, az utolsó ellenőrzőponton folytatódik, ami azt jelenti, hogy csak a már másolt fájlokat hagyja ki a program.

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

Mivel a felhasználónév hitelesítő adatai nem maradnak meg a [Felfüggesztési munkafolyamat](https://technet.microsoft.com/library/jj733586.aspx) tevékenység vagy az utolsó ellenőrzőpont után, a hitelesítő `Suspend-Workflow` adatokat null értékre kell állítania, majd újra le kell kérnie őket az eszköztárolóból, vagy az ellenőrzőpont ot hívják meg.  Ellenkező esetben a következő hibaüzenet jelenhet meg:`The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

A következő ugyanazt a kódot bemutatja, hogyan kell kezelni ezt a PowerShell-munkafolyamat runbookok.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> Nem grafikus PowerShell-runbookok esetén, `Add-AzAccount` és `Add-AzureRMAccount` a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)aliasai. Használhatja ezeket a parancsmagokat, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban a legújabb verziókra. Előfordulhat, hogy frissítenie kell a modulokat, még akkor is, ha nemrég létrehozott egy új Automation-fiókot.


Ez nem szükséges, ha egyszerű szolgáltatással konfigurált Futtatás mint fiók használatával hitelesíti a hitelesítést.

Az ellenőrzőpontokról további információt az [Ellenőrzőpontok hozzáadása parancsfájl-munkafolyamathoz című témakörben talál.](https://technet.microsoft.com/library/jj574114.aspx)

## <a name="next-steps"></a>További lépések

* A PowerShell-munkafolyamat-runbookok első lépései: [Az első PowerShell-munkafolyamat-runbook](automation-first-runbook-textual.md)

