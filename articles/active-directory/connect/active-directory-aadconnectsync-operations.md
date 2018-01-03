---
title: "Azure AD Connect szinkronizálása: működtetési feladatok és szempontok |} Microsoft Docs"
description: "Ez a témakör ismerteti az operatív feladatok az Azure AD Connect szinkronizálási szolgáltatás és előkészítése operációs ezt az összetevőt."
services: active-directory
documentationcenter: 
author: AndKjell
manager: mtillman
editor: 
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 4252a5ff3dd985185745060073cbe5432dba726a
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Azure AD Connect szinkronizálása: működtetési feladatok és szempont
Ez a témakör célja az Azure AD Connect szinkronizálási szolgáltatás működési feladatokat írják le.

## <a name="staging-mode"></a>Átmeneti mód
Az átmeneti környezetű üzemmód használható több forgatókönyvek, köztük:

* Magas rendelkezésre állású.
* Tesztelése és telepítése az új konfigurációs módosításokat.
* Új kiszolgáló esetében, és a régi leszereléséhez.

Átmeneti módban a kiszolgálóval a konfigurációs módosításokat, és tekintse meg a módosítások elvégzése előtt a kiszolgáló aktív. Lehetővé teszi teljes importálást és teljes szinkronizálás győződjön meg arról, hogy minden módosítás előtt ezeket a módosításokat az éles környezetében várható futtatásához.

A telepítés során válassza ki a kiszolgálót a **átmeneti módban**. Ez a művelet lehetővé teszi a kiszolgáló importálása és szinkronizálás aktív, de nem futtatható bármely exportálja. Átmeneti módban nem fut a jelszó-szinkronizálás és jelszóvisszaíró, még akkor is, ha ezek a szolgáltatások telepítésekor kiválasztott. Az átmeneti környezetű üzemmód letiltása esetén a kiszolgáló kezdődik, exportálása, lehetővé teszi, hogy a jelszó-szinkronizálást, és lehetővé teszi, hogy a jelszóvisszaíró.

> [!NOTE]
> Tegyük fel, az Azure AD Connect a Jelszókivonat-szinkronizálást szolgáltatás engedélyezve van. Ha az átmeneti környezetű üzemmód, a szinkronizálási jelszó is módosul a fájlkiszolgáló nem engedélyezi a helyszíni AD. Az átmeneti környezetű üzemmód letiltása esetén a kiszolgáló folytatja a jelszó-módosítások, ahol utolsó abbahagyta szinkronizálása. Ha a kiszolgáló egy hosszú időn átmeneti módban, szinkronizálása a összes, amely során az adott időszakban történt volna a kiszolgáló egy ideig is igénybe vehet.
>
>

Az export továbbra is kényszerítheti a synchronization service manager használatával.

Átmeneti módban továbbra is kaphat a módosítást az Active Directoryból és az Azure AD. Azt mindig van egy példányát a legutóbbi módosítások és a részleg nagyon gyorsan elvégezhető a kötelezettségeit, egy másik kiszolgálóra. Ha konfigurációs módosítások az elsődleges kiszolgálóval, feladata a ugyanazt a módosításokat a kiszolgáló átmeneti módban.

Azok a régebbi szinkronizálási technológiák ismerete is az átmeneti környezetű üzemmód nem azonos mivel a kiszolgáló csak a saját SQL-adatbázis. Ez az architektúra lehetővé teszi, hogy az átmeneti mód kiszolgáló ugyanabban az adatközpontban található.

### <a name="verify-the-configuration-of-a-server"></a>A kiszolgáló konfigurációjának ellenőrzése
Ha szeretné alkalmazni ezt a módszert, kövesse az alábbi lépéseket:

1. [Előkészítése](#prepare)
2. [Konfigurálás](#configuration)
3. [Importálja és szinkronizálja](#import-and-synchronize)
4. [Ellenőrzése](#verify)
5. [Az active server kapcsoló](#switch-active-server)

#### <a name="prepare"></a>Előkészítés
1. Azure AD Connectet telepíti, válassza ki **átmeneti módban**, és kikapcsolni **a szinkronizálás megkezdéséhez** a varázsló utolsó lapján. Ebben a módban a szinkronizálási motor manuális futtatása teszi lehetővé.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Jelentkezzen ki/sign és a start menüből válassza a **szinkronizálási szolgáltatás**.

#### <a name="configuration"></a>Konfiguráció
Ha egyéni módosításokat végzett az elsődleges kiszolgáló, és a átmeneti kiszolgálóval konfigurációt szeretne, majd használja [az Azure AD Connect konfigurációs dokumentáló](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importálja és szinkronizálja
1. Válassza ki **összekötők**, és válassza ki az első összekötőt típusú **Active Directory tartományi szolgáltatások**. Kattintson a **futtatása**, jelölje be **teljes importálás**, és **OK**. Hajtsa végre ezeket a lépéseket az összes ilyen típusú-összekötőhöz.
2. Válassza ki az összekötő típusú **Azure Active Directoryban (Microsoft)**. Kattintson a **futtatása**, jelölje be **teljes importálás**, és **OK**.
3. Győződjön meg arról, hogy a lap összekötők aktív marad. Minden típusú összekötő **Active Directory tartományi szolgáltatások**, kattintson a **futtatása**, jelölje be **különbözeti szinkronizálás**, és **OK**.
4. Válassza ki az összekötő típusú **Azure Active Directoryban (Microsoft)**. Kattintson a **futtatása**, jelölje be **különbözeti szinkronizálás**, és **OK**.

Most előkészítette rendelkezik exportálási módosításai az Azure AD és a helyszíni AD (ha az Exchange hibrid telepítést használ). A következő lépéseket lehetővé teszi a vizsgálandó ténylegesen a könyvtárakat az exportálás megkezdése előtt megváltoztatta újdonságai.

#### <a name="verify"></a>Ellenőrzés
1. Indítsa el egy parancssort, és navigáljon a`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Futtatás: `csexport "Name of Connector" %temp%\export.xml /f:x` a nevét, az összekötő szinkronizálási szolgáltatás található. A "contoso.com – AAD" hasonló névvel rendelkezik az Azure AD.
3. Futtatás: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` % temp %, amely megfelel a Microsoft Excel export.csv nevű fájlt. Ez a fájl minden exportálni, módosításokat tartalmaz.
4. Hajtsa végre a módosításokat az adatok vagy konfiguráció, és futtassa ezeket a lépéseket újra (importálás és szinkronizálás és ellenőrzése) addig, amíg az exportálni kívánt módosításokkal várható.

**A export.csv fájl ismertetése** a fájlt a legtöbb értetődő. Néhány rövidítések tudni, hogy a tartalom:
* OMODT – objektumtípus-módosítás. Azt jelzi, hogy a művelet az objektum szinten egy hozzáadása, frissítése vagy törlése.
* AMODT – attribútumtípus módosítását. Azt jelzi, hogy a művelet egy attribútum szinten egy hozzáadása, frissítése vagy törlése.

**Közös azonosítók beolvasása** a export.csv fájl exportálni, minden módosításokat tartalmaz. A kapcsolódási térbe szereplő objektum állásidejének változás minden sor megfelel-e, és az objektum megkülönböztető név attribútum által azonosított. A megkülönböztető név attribútum található a kapcsolódási térbe objektumhoz hozzárendelt egyedi azonosítója. Ha sok sorok/módosítások elemzéséhez export.csv rendelkezik, ahhoz, hogy mérje fel, hogy milyen objektumokat, a változtatások vannak, csak a megkülönböztető név attribútum-alapú nehéz lehet. Egyszerűbbé elemzése a módosításokat, használja a csanalyzer.ps1 PowerShell-parancsfájlt. A parancsfájl beolvassa az objektumok közös azonosítói (például displayName, userPrincipalName). A parancsprogram használata:
1. A szakasz a PowerShell parancsfájl másolása [CSAnalyzer](#appendix-csanalyzer) nevű fájlba `csanalyzer.ps1`.
2. Nyisson meg egy PowerShell-ablakot, és keresse meg a mappát, amelyben létrehozta a PowerShell-parancsfájlt.
3. Futtatás: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Most már rendelkezik egy fájlt **processedusers1.csv** , amely a Microsoft Excel kell vizsgálni. Vegye figyelembe, hogy a fájl tartalmazza-e a leképezés a megkülönböztető név attribútum csak akkor közös azonosítók (például displayName és userPrincipalName). Jelenleg nem tartalmazza a tényleges Attribútummódosítások, amelyek exportálásra váró adatokat.

#### <a name="switch-active-server"></a>Az active server kapcsoló
1. A jelenleg aktív kiszolgálón vagy kapcsolja ki a kiszolgálót (a DirSync vagy FIM vagy az Azure AD Sync), akkor nem exportálása az Azure ad Szolgáltatásba, vagy állítsa az átmeneti módban (az Azure AD Connect).
2. A telepítővarázsló futtatása a kiszolgálón a **átmeneti módban** és tiltsa le a **átmeneti módban**.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Vészhelyreállítás
A megvalósítási terv részét képezi, mi a teendő, ha van egy olyan vészhelyzet esetén Ha elveszti a sync-kiszolgáló tervezését. Számos különböző képességekkel és melyiket használja többek között számos tényezőtől függ:

* Mi az a tűrés, nem tudja ellenőrizze a objektumok változásait az Azure AD a leállások során?
* Ha használja a jelszó-szinkronizálás, tegye a felhasználók fogadja el, hogy kell-e a régi jelszót használja az Azure AD abban az esetben, ha azok megváltoztatnia a helyszíni?
* Rendelkezik a valós idejű műveletek, például a jelszóvisszaírás függőség?

Attól függően, hogy ezek a kérdések és a szervezet házirendje a választ a következő stratégiák egyikét is kell végrehajtani:

* Építse újra, amikor szükséges.
* Tartalék készenléti kiszolgáló, úgynevezett **átmeneti módban**.
* Használja a virtuális gépek.

Ha nem használja a beépített SQL Express adatbázist, majd tekintse meg a [SQL magas rendelkezésre állású](#sql-high-availability) szakasz.

### <a name="rebuild-when-needed"></a>Szükség esetén újraépítése
Életképes stratégiát, hogy szükség esetén egy kiszolgáló újjáépítése tervezése. A szinkronizálási motor és a kezdeti importálás és szinkronizálás elvégezhető néhány órán belül nem általában telepítése. Ha tartalék kiszolgáló nem érhető el, akkor lehet a ideiglenesen tartományvezérlő is tárolni a szinkronizálási motor.

A Szinkronizáló vezérlő kiszolgálója nem tárolja az objektumok olyan állapotokat, az adatbázis is építhető újra, az Active Directory és az Azure AD az adatokból. A **sourceAnchor** attribútum segítségével csatlakoztassa az objektumokat a helyszíni és felhőben. A meglévő objektumokat a helyszíni és a felhő kiszolgáló újraépítése, majd a szinkronizálási motor megegyezik azokat az objektumokat együtt újra az újratelepítést. A dokumentum, és menteni kell minden olyan a kiszolgálót, például a szűrés és szinkronizálási szabályok konfigurációs módosításait. Ezen egyéni konfigurációk kell kell újra alkalmazza a szinkronizálás megkezdése előtt.

### <a name="have-a-spare-standby-server---staging-mode"></a>Tartalék készenléti kiszolgáló - átmeneti módban
Ha összetettebb környezetben, akkor ajánlott, ha egy vagy több készenléti kiszolgálót. A telepítés során engedélyezheti a server a **átmeneti módban**.

További információkért lásd: [átmeneti módban](#staging-mode).

### <a name="use-virtual-machines"></a>Virtuális gépek használata
A közös és támogatott módja, hogy a szinkronizálási motor egy virtuális gép futtatásához. Abban az esetben, ha a gazdagép egy hibát tartalmaz, a kép és a Szinkronizáló vezérlő kiszolgálója áttelepíthetők egy másik kiszolgálóra.

### <a name="sql-high-availability"></a>Az SQL magas rendelkezésre állás
Ha nem használ az SQL Server Express, az Azure AD Connect előre, majd az SQL Server magas rendelkezésre állás is meg kell fontolni. A támogatott magas rendelkezésre állás megoldásai a következők: SQL-fürtözés és AOA (az Always On rendelkezésre állási csoportok). Nem támogatott megoldásai a következők tükrözés.

SQL AOA támogatása az Azure AD Connect 1.1.524.0 verziójában lett hozzáadva. Az Azure AD Connect telepítése előtt engedélyeznie kell az SQL AOA. A telepítés során az Azure AD Connect észleli, hogy a megadott SQL-példány engedélyezve van az SQL AOA vagy nem. Ha SQL AOA engedélyezve van, az Azure AD Connect további adatok Ha SQL AOA replikáció szinkron vagy aszinkron replikáció használatára van konfigurálva. A rendelkezésre állási csoport figyelőjének beállításakor javasoljuk, hogy a RegisterAllProvidersIP tulajdonságot 0 értékre állítja. Ennek az az oka az Azure AD Connect jelenleg használ SQL Native Client kapcsolódni az SQL és az SQL natív ügyfél nem támogatja a MultiSubNetFailover tulajdonság használatával.

## <a name="appendix-csanalyzer"></a>A függelék CSAnalyzer
Című témakör [ellenőrizze](#verify) a parancsfájl használatával.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as as CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>További lépések
**Áttekintő témakör**  

* [Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása](active-directory-aadconnectsync-whatis.md)  
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)  
