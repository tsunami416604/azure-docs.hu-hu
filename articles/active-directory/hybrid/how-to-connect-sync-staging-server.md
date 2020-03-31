---
title: 'Azure AD Connect szinkronizálása: Működési feladatok és szempontok | Microsoft dokumentumok'
description: Ez a témakör az Azure AD Connect szinkronizálásának operatív feladatait ismerteti, és az összetevő üzemeltetésére való felkészülést.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc88640cdff4f716902a80bb149913b961d40ae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261020"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: Átmeneti kiszolgáló és vészhelyreállítás
Ha egy kiszolgáló átmeneti üzemmódban van, módosíthatja a konfigurációt, és megtekintheti a módosítások előnézetét, mielőtt a kiszolgálót aktívvá tenné. Azt is lehetővé teszi, hogy futtassa a teljes importálás és a teljes szinkronizálás, hogy ellenőrizze, hogy minden változás várható, mielőtt ezeket a módosításokat az éles környezetben.

## <a name="staging-mode"></a>Átmeneti mód
Az átmeneti mód több forgatókönyvhöz is használható, többek között a következőkre:

* Magas rendelkezésre állás.
* Tesztelje és telepítse az új konfigurációs módosításokat.
* Vezessen be egy új szervert, és szerelje le a régit.

A telepítés során kiválaszthatja azt a kiszolgálót, amely **átmeneti üzemmódban van.** Ez a művelet aktívvá teszi a kiszolgálót importálásra és szinkronizálásra, de nem futtat exportálást. Az átmeneti üzemmódban lévő kiszolgáló nem futtat jelszó-szinkronizálást vagy jelszó-visszaírást, még akkor sem, ha ezeket a szolgáltatásokat a telepítés során választotta ki. Az átmeneti mód letiltásakor a kiszolgáló elkezdi az exportálást, engedélyezi a jelszószinkronizálást, és engedélyezi a jelszóvisszaírást.

> [!NOTE]
> Tegyük fel, hogy rendelkezik egy Azure AD Connect jelszókivonat-szinkronizálási funkcióval. Ha engedélyezi az átmeneti módot, a kiszolgáló leállítja a jelszómódosítások szinkronizálását a helyszíni AD-ről. Ha letiltja az átmeneti módot, a kiszolgáló folytatja a jelszómódosítások szinkronizálását onnan, ahol az utolsó abbahagyta. Ha a kiszolgáló hosszabb ideig átmeneti üzemmódban marad, eltarthat egy ideig, amíg a kiszolgáló szinkronizálja az adott időszakban bekövetkezett összes jelszómódosítást.
>
>

Az exportálást továbbra is kényszerítheti a szinkronizálási szolgáltatáskezelő használatával.

Az átmeneti üzemmódban lévő kiszolgáló továbbra is fogadja a módosításokat az Active Directoryból és az Azure AD-ből, és hiba esetén gyorsan átveheti egy másik kiszolgáló feladatait. Ha az elsődleges kiszolgálón módosítja a konfigurációt, az Ön felelőssége, hogy átmeneti módban is módosítsa a kiszolgálót.

Azok számára, akik ismerik a régebbi szinkronizálási technológiákat, az átmeneti mód más, mivel a kiszolgáló saját SQL-adatbázissal rendelkezik. Ez az architektúra lehetővé teszi, hogy az átmeneti módú kiszolgáló egy másik adatközpontban legyen.

### <a name="verify-the-configuration-of-a-server"></a>Kiszolgáló konfigurációjának ellenőrzése
A módszer alkalmazásához hajtsa végre az alábbi lépéseket:

1. [Előkészítés](#prepare)
2. [Konfiguráció](#configuration)
3. [Importálás és szinkronizálás](#import-and-synchronize)
4. [Ellenőrzés](#verify)
5. [Váltás az aktív kiszolgálóközött](#switch-active-server)

#### <a name="prepare"></a>Előkészítés
1. Telepítse az Azure AD Connectet, válassza **az átmeneti módot**, és törölje a jelet a szinkronizálás **elindításának megszüntetéséből** a telepítővarázsló utolsó lapján. Ez a mód lehetővé teszi a szinkronizálási motor manuális futtatását.
   ![ReadyToConfigure (Készbeállításra)](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Jelentkezzen ki/jelentkezzen be, és a start menüben válassza a **Szinkronizálási szolgáltatás lehetőséget.**

#### <a name="configuration"></a>Konfiguráció
Ha egyéni módosításokat hajtott végre az elsődleges kiszolgálón, és össze szeretné hasonlítani a konfigurációt az átmeneti kiszolgálóval, használja az [Azure AD Connect konfigurációs dokumentálóját.](https://github.com/Microsoft/AADConnectConfigDocumenter)

#### <a name="import-and-synchronize"></a>Importálás és szinkronizálás
1. Válassza **az Összekötők**lehetőséget, és válassza ki az első Active Directory tartományi szolgáltatások típusú **összekötőt.** Kattintson **a Futtatás**menü **Teljes importálás parancsára,** és az OK **gombra.** Végezze el ezeket a lépéseket az összes ilyen típusú összekötők.
2. Válassza ki az **Azure Active Directory (Microsoft)** típusú összekötőt. Kattintson **a Futtatás**menü **Teljes importálás parancsára,** és az OK **gombra.**
3. Győződjön meg arról, hogy az Összekötők lap továbbra is ki van jelölve. Minden **Active Directory tartományi szolgáltatások**típusú összekötőesetén kattintson a **Futtatás**gombra, válassza a **Különbözeti szinkronizálás**lehetőséget, és az **OK gombot.**
4. Válassza ki az **Azure Active Directory (Microsoft)** típusú összekötőt. Kattintson **a Futtatás**menü **Delta-szinkronizálás parancsára,** és **az OK gombra.**

Most már előkészített exportálási módosításokat az Azure AD és a helyszíni AD (ha az Exchange hibrid telepítés). A következő lépések lehetővé teszik, hogy ellenőrizze, mi fog változni, mielőtt ténylegesen megkezdi az exportálást a könyvtárakba.

#### <a name="verify"></a>Ellenőrzés
1. Indítsa el a cmd-s kérdést, és lépjen a`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Futtatás: `csexport "Name of Connector" %temp%\export.xml /f:x` Az összekötő neve megtalálható a Szinkronizálási szolgáltatásban. Az Azure AD"-hoz hasonló a "contoso.com – AAD" név.
3. Futtatás: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` Van egy export.csv nevű %temp% nevű fájlja, amely et meg lehet vizsgálni a Microsoft Excelben. Ez a fájl tartalmazza az összes exportálni készülő módosítást.
4. Hajtsa végre az adatok vagy a konfiguráció szükséges módosításait, és futtassa újra ezeket a lépéseket (Importálás és szinkronizálás és ellenőrzés), amíg az exportálandó módosítások várhatók.

**Az export.csv fájl ismertetése** A legtöbb fájl magától értetődő. Néhány rövidítés, hogy megértsék a tartalmat:
* OMODT – Objektummódosítás típusa. Azt jelzi, hogy az objektum szintű művelet hozzáadás, frissítés vagy törlés.
* AMODT – Attribútummódosítás típusa. Azt jelzi, hogy az attribútumszintjén végzett művelet hozzáadás, frissítés vagy törlés.

**Közös azonosítók beolvasása** Az export.csv fájl tartalmazza az összes exportálni készülő módosítást. Minden sor az összekötő térben lévő objektum változásának felel meg, és az objektumot a DN attribútum azonosítja. A DN attribútum egy egyedi azonosító, amely az összekötő térben lévő objektumhoz van rendelve. Ha az export.csv-ben sok sort/módosítást kell elemeznie, nehéz lehet kitalálni, hogy a módosítások mely objektumokra szolgálnak csak a DN attribútum alapján. A módosítások elemzésének egyszerűsítése érdekében használja a csanalyzer.ps1 PowerShell parancsfájlt. A parancsfájl beolvassa az objektumok közös azonosítóit (például displayName, userPrincipalName). A parancsfájl használata:
1. Másolja a PowerShell-parancsfájlt a [CSAnalyzer](#appendix-csanalyzer) szakaszból egy fájlba, amelynek neve `csanalyzer.ps1`.
2. Nyisson meg egy PowerShell-ablakot, és keresse meg azt a mappát, amelyben létrehozta a PowerShell-parancsfájlt.
3. Adja ki a következő parancsot: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Most már van egy **processedusers1.csv** nevű fájlja, amelyet meg lehet vizsgálni a Microsoft Excelben. Ne feledje, hogy a fájl a DN attribútumból a közös azonosítókhoz (például displayName és userPrincipalName) való leképezést biztosít. Jelenleg nem tartalmazza az exportálandó tényleges attribútummódosításokat.

#### <a name="switch-active-server"></a>Váltás az aktív kiszolgálóközött
1. Az aktuálisan aktív kiszolgálón kapcsolja ki a kiszolgálót (DirSync/FIM/Azure AD Sync), hogy ne exportálja az Azure AD-be, vagy állítsa be átmeneti módban (Azure AD Connect).
2. Futtassa a telepítővarázslót a kiszolgálón **átmeneti üzemmódban,** és tiltsa le az **átmeneti üzemmódot**.
   ![ReadyToConfigure (Készbeállításra)](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Vészhelyreállítás
A megvalósítás tervének része, hogy megtervezze, mit kell tenni, ha katasztrófa történik, ahol elveszíti a szinkronizálási kiszolgálót. Vannak különböző modellek használata, és melyik et kell használni több tényezőtől függ, beleértve:

* Mi a tűréshatára, hogy nem tudja módosítani az objektumokat az Azure AD-ben az állásidő alatt?
* Ha jelszó-szinkronizálást használ, a felhasználók elfogadják, hogy a régi jelszót kell használniuk az Azure AD-ben, ha a helyszínen módosítják?
* Függőséget jelent a valós idejű műveletektől, például a jelszó-visszaírástól?

Az ezekre a kérdésekre adott válaszoktól és a szervezet irányelveitől függően az alábbi stratégiák egyike valósítható meg:

* Szükség esetén építse újra.
* Van egy tartalék készenléti kiszolgáló, az úgynevezett **átmeneti mód**.
* Használjon virtuális gépeket.

Ha nem használja a beépített SQL Express-adatbázist, akkor tekintse át az [SQL magas rendelkezésre állású szakaszt](#sql-high-availability) is.

### <a name="rebuild-when-needed"></a>Szükség esetén újraépítés
Egy életképes stratégia a kiszolgáló újraépítésének megtervezése, ha szükséges. Általában a szinkronizálási motor telepítése, és nem a kezdeti importálás és szinkronizálás lehet befejezni néhány órán belül. Ha nincs elérhető tartalék kiszolgáló, ideiglenesen tartományvezérlőt is használhat a szinkronizálási motor üzemeltetéséhez.

A szinkronizálási motor kiszolgáló nem tárolja az objektumok állapotát, így az adatbázis az Active Directory és az Azure AD adataiból újraépíthető. A **sourceAnchor** attribútum a helyszíni és a felhőbeli objektumok csatlakoztatására szolgál. Ha újraépíti a kiszolgálót a meglévő helyszíni és a felhőbeli objektumokkal, akkor a szinkronizálási motor újraésen újra egyezteti ezeket az objektumokat. A dokumentálandó és mentett dokumentumok a kiszolgálón végrehajtott konfigurációs módosítások, például a szűrési és szinkronizálási szabályok. Ezeket az egyéni konfigurációkat újra alkalmazni kell a szinkronizálás megkezdése előtt.

### <a name="have-a-spare-standby-server---staging-mode"></a>Van egy tartalék készenléti szerver - átmeneti mód
Ha összetettebb környezettel rendelkezik, akkor egy vagy több készenléti kiszolgáló használata ajánlott. A telepítés során engedélyezheti, hogy a kiszolgáló **átmeneti üzemmódban**legyen.

További információt az [Átmeneti mód című témakörben talál.](#staging-mode)

### <a name="use-virtual-machines"></a>Virtuális gépek használata
Gyakori és támogatott módszer a szinkronizálási motor futtatása egy virtuális gépen. Abban az esetben, ha az állomás nak problémája van, a szinkronizálási motorkiszolgálóval rendelkező lemezkép áttelepíthető egy másik kiszolgálóra.

### <a name="sql-high-availability"></a>SQL magas rendelkezésre állás
Ha nem az Azure AD Connect hez kapcsolódó SQL Server Express szolgáltatást használja, akkor az SQL Server magas rendelkezésre állását is figyelembe kell venni. A támogatott magas rendelkezésre állású megoldások közé tartozik az SQL-fürtözés és az AOA (Always On Availability Groups). A nem támogatott megoldások közé tartozik a tükrözés.

Az SQL AOA támogatása hozzá lett adva az Azure AD Connect 1.1.524.0-s verziójú verziójához. Az Azure AD Connect telepítése előtt engedélyeznie kell az SQL AOA-t. A telepítés során az Azure AD Connect észleli, hogy a megadott SQL-példány engedélyezve van-e az SQL AOA-hoz. Ha az SQL AOA engedélyezve van, az Azure AD Connect tovább számol, hogy az SQL AOA szinkron replikáció vagy aszinkron replikáció használatára van-e konfigurálva. Az Elérhetőségi csoport figyelőjének beállításakor ajánlott a RegisterAllProvidersIP tulajdonságot 0-ra állítani. Ennek az az oka, hogy az Azure AD Connect jelenleg az SQL Natív ügyfél programot használja az SQL-hez való csatlakozáshoz, és az SQL Natív ügyfél nem támogatja a MultiSubNetFailover tulajdonság használatát.

## <a name="appendix-csanalyzer"></a>Függelék CSAnalyzer
Tekintse meg a parancsfájl használatát [ellenőrző](#verify) szakaszt.

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
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
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

        #export the collection of users as a CSV
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
**Áttekintő témakörök**  

* [Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása](how-to-connect-sync-whatis.md)  
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)  
