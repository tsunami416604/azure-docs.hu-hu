---
title: Az Azure Automation integrációs modul létrehozása
description: Ez az oktatóprogram végigvezeti az integrációs modulok létrehozásán, tesztelésén és példahasználatán az Azure Automationben.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9122cf5cc908d578d8b781c6fdc49d7b04b0ab58
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990342"
---
# <a name="azure-automation-integration-modules"></a>Azure Automation integrációs modulok

Az Azure Automation mögötti alapvető technológia a PowerShell. Minthogy az Azure Automation a PowerShellre épül, a PowerShell-modulok kulcsfontosságúak az Azure Automation bővíthetősége szempontjából. Ebben a cikkben megismerheti az Azure Automation PowerShell-modulok nevezik integrációs modulok használata tulajdonságairól. Azt is megtudhatja a saját, győződjön meg arról, hogy azok biztosan működjenek integrációs modulként az Azure Automation PowerShell-modulok létrehozására vonatkozó ajánlott eljárásokat. 

## <a name="what-is-a-powershell-module"></a>Mi az a PowerShell-modul?

Egy PowerShell-modul olyan PowerShell-parancsmagokat például **Get-Date** vagy **Copy-Item**, amely a használható:

* A PowerShell-konzol
* szkriptek
* A munkafolyamatok
* Runbookok
* DSC-erőforrások

Összes funkciója parancsmagok és DSC-erőforrások használatával van közzétéve. Minden parancsmag és a DSC-erőforrás mögött egy PowerShell-modul ezek jelentős része magához a Powershellhez tartozik. Például a **Get-Date** parancsmag része a `Microsoft.PowerShell.Utility` PowerShell-modult, és **Copy-Item** parancsmag része a `Microsoft.PowerShell.Management` PowerShell-modul a csomag DSC erőforrás pedig része a PSDesiredStateConfiguration PowerShell-modul. A PowerShellben mindkét modul megtalálható. Sok PowerShell-modul nem PowerShell részét képezi. Ezeket a modulokat az első vagy harmadik fél termékek vagy, többek között a PowerShell-galériában vannak osztva. A modulok hasznosak, mert a beágyazott funkciók révén egyszerűbbé teszik a bonyolult feladatokat.  További információt talál a [PowerShell-modulokról az MSDN webhelyén](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>Mi az az Azure Automation integrációs modul?

Az integrációs modul nem különbözik egy PowerShell-modul. Egyszerűen egy olyan PowerShell-modul, amely opcionálisan tartalmaz egy további fájlt, egy Azure Automation kapcsolattípust megadó metaadatfájlt, amely a modul parancsmagjaival használható a forgatókönyvekben. PowerShell-modul importálható az Azure Automationbe, hogy a runbookok és a DSC-erőforrásaik parancsmagjaik használhatók DSC-konfigurációk parancsmagjaik használhatók. A színfalak mögött az Azure Automation tárolja ezeket a modulokat, és a runbook-feladat és a DSC fordítási feladat végrehajtási ideje betölti őket az Azure Automation próbakörnyezetbe, ahol a runbookok végrehajtása és a DSC-konfigurációk fordítása. Olyan modulok DSC-erőforrások is automatikusan kikerülnek az Automation DSC lekéréses kiszolgálón. Lekérhetik őket gépek DSC-konfigurációk alkalmazható.  

Az Azure Automationben beépített közzéteszünk egy Azure PowerShell-modulok száma. De importálhat PowerShell-modulokat bármilyen rendszer, szolgáltatás vagy eszköz, amelyet integrálni szeretne.

> [!NOTE]
> Egyes modulok mellékelt **globális modulok** az Automation szolgáltatásban. Ezek a globális modulok elérhetők, ha létrehoz egy automation-fiókot, és azokat időnként frissítjük, ami automatikusan leküldi az automation-fiókhoz. Ha nem szeretné, automatikus frissítését, bármikor importálhatja ugyanazon modulokat magát, és, amely élvez a globális modulverziókkal ezt a modult, amely a szolgáltatásban közzéteszünk.

Az importálni kívánt integrációsmodul-csomag formátuma a modullal azonos nevet viselő, .zip kiterjesztésű tömörített fájl. A mappa tartalmazza a Windows PowerShell-modult és minden kiegészítő fájlt, beleértve a jegyzékfájlt (.psd1) is, ha a modul rendelkezik ilyennel.

Ha a modul esetleg tartalmaz egy Azure Automation kapcsolattípust, akkor tartalmaznia kell egy `<ModuleName>-Automation.json` nevű fájlt is, amely meghatározza a kapcsolattípus tulajdonságait. Ez az egy json-fájlt, amely a tömörített .zip fájl modulmappájában elhelyezett van. Ez a fájl tartalmazza a mezőket, egy **kapcsolat** rendszerhez vagy a modul által képviselt szolgáltatáshoz való kapcsolódáshoz szükséges. Ez a konfiguráció említi kapcsolattípus létrehozása az Azure Automationben. Ezzel a fájllal megadhatja a modul kapcsolattípusához a mezőneveket, a típusait, valamint azt, hogy a mezőket titkosítani kell-e és/vagy hogy kötelezők-e. Az alábbi példában egy json fájlformátumú sablon:

```json
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
   "TypeName":  "System.String"
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Ha telepítette a Service Management Automation, és létrehozott Integrációsmodul-csomagokat az automatizálási forgatókönyvekhez, ez a sablon Bizonyára ismerős lesz Önnek.

## <a name="authoring-best-practices"></a>Szerzői gyakorlati tanácsok

Annak ellenére, hogy az integrációs modulok olyan PowerShell-modulok, van még egy dolog, fontolja meg, amikor szerzőként összeállít egy PowerShell-modul használata az Azure Automationben száma. Néhány javaslat, hogy a moduljai jól működjenek a PowerShell-munkafolyamat hasznosak lehetnek.

1. A modul összes parancsmagjáról mellékeljen egy szinopszist, egy leírást és egy súgó URI-t. A PowerShellben meghatározhat bizonyos súgóinformációkat a parancsmagokhoz, hogy a felhasználó segítséget kapjon a **Get-Help** parancsmaggal való használatukkor. Ha például van itt, hogyan meghatározhatja a szinopszist és súgó URI-t egy .psm1 fájlban megírt PowerShell-modulhoz. 
   
    ```powershell
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='https://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```

   Ezt az információt megadja a súgó használatát mutatja be a **Get-Help** parancsmag a PowerShell-konzolon. Ezt az információt is elérhetővé teszi az Azure Automation.  Például amikor tevékenységet szúr be a forgatókönyvek létrehozása alatt. "Részletes súgó megtekintése" kattint nyílik meg a Súgó URI-t, Azure Automation eléréséhez használt webböngésző új lapján.

   ![Integrációs modul súgó](media/automation-integration-modules/automation-integration-module-activitydesc.png)

2. Ha a modul egy távoli rendszeren fut:

   1. Tartalmaznia kell az integrációs modulhoz tartozó metaadatfájlt, amely meghatározza az adott távoli rendszerhez való csatlakozáshoz szükséges információkat, azaz a kapcsolat típusát.
   2. A modul minden egyes parancsmagjának képesnek kell lennie egy kapcsolat objektum (az adott kapcsolattípus egy példánya) befogadására paraméterként.  

    A modulban lévő parancsmagokat könnyebb használni az Azure Automationben, ha lehetővé teszi, hogy egy objektumot a kapcsolattípus mezőivel paraméterként továbbítson a parancsmagnak. Ez lehetővé teszi a felhasználók leképezniük a kapcsolat adategység a parancsmag megfelelő paramétereihez minden alkalommal, amikor azok meghívnak egy parancsmagot.

    A fenti forgatókönyv-példa alapján egy CorpTwilio nevű Twilio-kapcsolati adategység segítségével éri el a Twiliót, és visszaadja az összes telefonszámot a fiókból.  Figyelje meg, hogyan képezi le a kapcsolat mezőit a parancsmag paramétereinek?

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    Az egyszerűbb és jobb megoldás ezt a viselkedést, ha közvetlenül továbbítja a kapcsolat objektumot a parancsmagnak:

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```

    Az előző példában viselkedést engedélyezhet a parancsmagjaihoz, a azáltal, hogy elfogadjon egy kapcsolat objektumot közvetlenül paraméterként ahelyett csak kapcsolat. Általában érdemes minden, egy paramétert, hogy a felhasználó nem használja az Azure Automation is meghívhatja a parancsmagjait anélkül hozhat létre, egy kapcsolat objektumként paraméterkészletre. A paraméterkészlet **SpecifyConnectionFields**, be a kapcsolat egyenként tulajdonságai. A **UseConnectionObject** segítségével továbbíthatja egyenesen a kapcsolatot. Ahogy látja, a [Twilio PowerShell-modul](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) Send-TwilioSMS parancsmagja lehetővé teszi bármelyik továbbítást.

    ```powershell
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='https://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```

3. Adja meg a modulban lévő összes parancsmag kimenettípusát. Egy parancsmag kimenettípusának megadása lehetővé teszi a tervezés közben az IntelliSense használatát, amely segít meghatározni egy parancsmag kimenetének tulajdonságait a megírásuk támogatására. Ez különösen hasznos az Automation-forgatókönyvek grafikus létrehozásakor, ahol a tervezés az könnyedfelhasználói élmény biztosításához a modulban kulcs alatt.

   ![Grafikus forgatókönyv kimenettípusa](media/automation-integration-modules/runbook-graphical-module-output-type.png)

   Ez a viselkedés hasonlít a "type ahead" funkciójára, a parancsmag kimenete a PowerShell ISE-ben, anélkül, hogy nem kell futtatni.

   ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)

4. A modul parancsmagjai nem fogadnak komplex objektumtípusokat paraméterek. A PowerShell-munkafolyamat eltér a PowerShelltől abban, hogy komplex típusokat tárol deszerializált formában. Az egyszerű típusok egyszerűek maradnak, de a komplex típusok átalakítása deszerializált változatokká konvertálja, amelyek lényegében tulajdonságcsomagok. Ha például a **Get-Process** parancsmagot használta egy forgatókönyvben (vagy egy PowerShell-munkafolyamatban), az egy [Deserialized.System.Diagnostic.Process] típusú, nem pedig a várt [System.Diagnostic.Process] típusú objektumot ad vissza. Ez a típus ugyanazokkal a tulajdonságokkal rendelkezik, mint a nem deszerializált típus, de a metódusok nélkül. És ha megpróbálja továbbítja ezt az értéket paraméterként egy parancsmagnak, ahol a parancsmag a paraméter [System.Diagnostic.Process] értéket vár, a következő hibaüzenetet kapja: *A paraméter "folyamat" argumentum-átalakítás nem dolgozható fel. Hiba: "A"Konvertálása a következő típusra"be"System.Diagnostics.Process"típusú"System.Diagnostics.Process (CcmExec)"érték nem konvertálható.*   Ez azért, mert a típusa nem egyezik a várt [System.Diagnostic.Process] típus és a megadott [Deserialized.System.Diagnostic.Process] típus között. A probléma megoldásához módja annak érdekében, hogy a modul parancsmagjai ne fogadjanak be komplex típusú paraméterekhez. Itt látható ennek a téves megközelítése.

    ```powershell
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ```

    A megfelelő módon, hogy igénybe vehet egy primitívet, amely belsőleg használt a parancsmag befogadhatja és használhatja azt a komplex objektumot. Minthogy a parancsmagokat a rendszer a PowerShell környezetében hajtja végre, nem a PowerShell-munkafolyamatéban, a parancsmagon belül $process lesz a helyes [System.Diagnostic.Process] típus.  

    ```powershell
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```

   Kapcsolati adategységek a forgatókönyvekben kivonattáblák, amelyek komplex típusúak, és még ezek a kivonattáblák úgy tűnik, hogy tudni kell megkérnie-parancsmagok a "kapcsolati paraméter tökéletesen, az egyetlen kivételt. Technikailag bizonyos PowerShell-típusok képesek megfelelően átalakulni a szerializált formájukból a deszerializált formájukba, és így átadhatók a nem deszerializált típust fogadó paraméterként a parancsmagoknak. A kivonattábla egy ezek közül. Lehetséges, hogy egy modul szerzőjének definiált típusait oly módon, hogy megfelelően deszerializálásuk is szerint kell végrehajtani, de néhány feláldozását érdemes figyelembe venni. A típusnak rendelkeznie kell alapértelmezett konstruktorral, minden tulajdonságának nyilvánosnak kell kennie, és rendelkeznie kell egy PSTypeConverter elemmel. Azonban a a modul nem birtokolt már definiált típusoknál nincs mód "hibajavítás", ezért azt javasoljuk, hogy kerülje a paramétereknél az összetett típusokat. Forgatókönyv-készítési tipp: Ha a parancsmagjainak komplex típusú paraméterre kell, a PowerShell-munkafolyamatokban a megoldás az burkolhatja a komplex típust létrehozó parancsmagot és az ugyanabba az InlineScript-tevékenységbe a komplex típust használó parancsmagot. Minthogy az InlineScript a tartalmait inkább a PowerShellhez, mint a PowerShell-munkafolyamathoz hasonlóan hajtja végre, a komplex típust létrehozó parancsmag a helyes típust hozza létre, nem a deszerializált komplex típust.

5. A modul egyik parancsmagjának se legyen állapota. A PowerShell-munkafolyamat a munkafolyamatban meghívott parancsmagok mindegyikét külön munkamenetben hívja meg. Ez azt jelenti, hogy azok a parancsmagok, amelyek ugyanazon modul más parancsmagjai által létrehozott / módosított munkamenet-állapot függenek a PowerShell-munkafolyamati runbookok nem fog működni.  Az alábbi példa bemutatja, mit ne tegyen.
   
    ```powershell
    $globalNum = 0
    function Set-GlobalNum {
       param(
           [int] $num
       )
   
       $globalNum = $num
    }
    function Get-GlobalNumTimesTwo {
       $output = $globalNum * 2
   
       $output
    }
    ```

6. A modulnak teljes egészében egy Xcopy-kompatibilis csomagban kell lennie. Azure Automation-modulokat az Automation próbakörnyezetekbe distributd akkor, ha a runbookok végre kell hajtani. A modulok kell működniük a gazdagéptől futnak. Meg kell tudni Zip beállítása és a egy csomag és normális működtetésére, amikor egy másik gazdagép PowerShell környezetébe importálja azt. Ahhoz, hogy ez megtörténjen a modul a modul mappákon kívül semmilyen fájl nem függenek. Ez a mappa az a mappa, a modul importálása az Azure Automationbe modulmappán. A modul is nem függhet egy gazdagép bármilyen egyedi beállításjegyzék-beállításokat, mint például állítsa be ezeket a beállításokat, a termék telepítésekor. Ha ez az ajánlott eljárás nem követik, a modul nem használható az Azure Automationben.  

7. Ha a hivatkozó [Az Azure Powershell-modulok](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) a modult, ellenőrizze, nem is hivatkozó `AzureRM`. A `Az` modul nem használható együtt a `AzureRM` modulok. `Az` a runbookok támogatott, de alapértelmezés szerint nincsenek importálva. További információ a `Az` modulok és szempontokat vegye figyelembe, hogy tekintse meg a [házirendmodul-támogatás Az Azure Automation](az-modules.md).

## <a name="next-steps"></a>További lépések

* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* További információk PowerShell-modulok létrehozásáról: [Windows PowerShell-modul írása](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx).
