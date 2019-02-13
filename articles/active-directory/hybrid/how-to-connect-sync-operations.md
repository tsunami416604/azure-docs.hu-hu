---
title: 'Az Azure AD Connect szinkronizálása: Üzemeltetési feladatok és szempontok |} A Microsoft Docs'
description: Ez a témakör ismerteti az Azure AD Connect szinkronizálása, és ez az összetevő működő előkészítése operatív feladatokról.
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
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d115afbb9769101c5082a48b15309408b070b6f1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56197827"
---
# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Az Azure AD Connect szinkronizálása: Üzemeltetési feladatok és szempontok
Ez a témakör célja, az Azure AD Connect szinkronizálási szolgáltatás üzemeltetési feladatok leírására.

## <a name="staging-mode"></a>Átmeneti mód
Átmeneti környezetű üzemmód használható számos forgatókönyv, többek között:

* Magas rendelkezésre állás.
* Tesztelhet, és helyezhet üzembe új konfigurációs módosításokat.
* Új kiszolgáló bevezetése és a régi leszerelése.

Átmeneti környezetű üzemmód egy kiszolgálóval módosítja a konfigurációt, és a módosítások előnézetének megtekintése, mielőtt aktívvá tétele a-kiszolgáló. Azt is lehetővé teszi teljes importálást és teljes szinkronizálást, győződjön meg arról, hogy minden módosítás várhatóan előtt ezeket a módosításokat éles környezetben való futtatásához.

A telepítés során jelölje be a kiszolgálóra, hogy a **átmeneti módban**. Ez a művelet lehetővé teszi a kiszolgáló aktív az importálás és a szinkronizálás, de azt nem futtat semmilyen exportálást. Átmeneti módban lévő kiszolgálók nem fut jelszó-szinkronizálás és jelszóvisszaíró, még akkor is, ha ezeket a funkciókat a telepítés során kiválasztott. Átmeneti környezetű üzemmód letiltása esetén a kiszolgáló exportálása elindul, lehetővé teszi, hogy a jelszó-szinkronizálás, és lehetővé teszi, hogy a jelszóvisszaíró.

> [!NOTE]
> Tegyük fel, hogy szükség van egy Azure AD Connect a Jelszókivonat-szinkronizálás szolgáltatás engedélyezve van. Átmeneti környezetű üzemmód, a kiszolgáló leáll, a jelszó szinkronizálása változik engedélyezésekor a helyszíni AD. Átmeneti környezetű üzemmód letiltása esetén a kiszolgáló folytatja a szinkronizálás jelszó-módosítások, ahol utolsó abbahagyta. Ha a kiszolgáló hosszabb idő marad az átmeneti környezetű üzemmód, a kiszolgáló által szinkronizálja az összes jelszó módosítására, amely során az adott időszakban történt volna egy ideig is eltarthat.
>
>

A synchronization service Managert használatával továbbra is kényszerítheti az exportálást.

Átmeneti módban lévő kiszolgálók továbbra is fogadni a változásokat az Active Directory és az Azure ad-ben. A felelősséget, a másik kiszolgáló keresztül egy másolatát a legutóbbi változtatásokat és is nagyon gyorsan elvégezhető mindig rendelkezik. Ha az elsődleges kiszolgáló konfigurációs módosításokat hajt végre, feladata az, hogy a módosításokat, a kiszolgáló átmeneti módra.

Azoknak a régebbi szinkronizálási technológiák ismerete, az átmeneti környezetű üzemmód eltér, mivel a kiszolgáló rendelkezik-e a saját SQL-adatbázis. Ez az architektúra lehetővé teszi, hogy az átmeneti módot kiszolgálót egy másik adatközpontban található.

### <a name="verify-the-configuration-of-a-server"></a>A kiszolgáló konfigurációjának ellenőrzése
A alkalmazni ezt a módszert, kövesse az alábbi lépéseket:

1. [Előkészítése](#prepare)
2. [Konfigurálás](#configuration)
3. [Importálja és szinkronizálja](#import-and-synchronize)
4. [Ellenőrizze](#verify)
5. [Kapcsoló aktív kiszolgáló](#switch-active-server)

#### <a name="prepare"></a>Előkészítés
1. Válassza ki az Azure AD Connect telepítése **átmeneti módban**, és törölje a jelet **szinkronizálás elindításához** a telepítővarázsló utolsó lapján. Ebben a módban lehetővé teszi, hogy manuálisan futtassa a szinkronizálási motor.
   ![ReadyToConfigure](./media/how-to-connect-sync-operations/readytoconfigure.png)
2. Jelentkezzen ki/bejelentkezés a és a start menüből válassza **szinkronizálási szolgáltatás**.

#### <a name="configuration"></a>Konfiguráció
Ha egyéni módosításokat végzett az elsődleges kiszolgáló és az átmeneti kiszolgálóval konfigurációt szeretne, használja a [az Azure AD Connect konfigurációs dokumentáló](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importálja és szinkronizálja
1. Válassza ki **összekötők**, és válassza ki az első összekötőt típusú **Active Directory Domain Services**. Kattintson a **futtatása**válassza **teljes importálást**, és **OK**. Az ilyen típusú összekötők elvégezheti ezeket a lépéseket.
2. Válassza ki az összekötő típusú **Azure Active Directory (Microsoft)**. Kattintson a **futtatása**válassza **teljes importálást**, és **OK**.
3. Győződjön meg arról, hogy az összekötők lapon továbbra is ki van jelölve. Az egyes összekötők típusú **Active Directory Domain Services**, kattintson a **futtatása**válassza **különbözeti szinkronizálás**, és **OK**.
4. Válassza ki az összekötő típusú **Azure Active Directory (Microsoft)**. Kattintson a **futtatása**válassza **különbözeti szinkronizálás**, és **OK**.

Van már előkészített exportálási módosítja az Azure AD és a helyszíni AD (Ha Exchange hibrid telepítést használ). A következő lépések megvizsgálhatja, mi az az Exportálás a könyvtárak ténylegesen megkezdése előtt megváltozása teszi lehetővé.

#### <a name="verify"></a>Ellenőrzés
1. Indítsa el a parancssort, és nyissa meg `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Futtassa: `csexport "Name of Connector" %temp%\export.xml /f:x` Az összekötő neve szinkronizálási szolgáltatás található. A "contoso.com – AAD" hasonló névvel rendelkezik az Azure ad-hez.
3. Futtassa: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` A % temp % export.csv, amely a Microsoft Excelben megvizsgálhatók nevű fájllal rendelkezik. Ez a fájl tartalmazza, amelyek az exportálni kívánt összes módosítást.
4. Hajtsa végre a módosításokat az adatok vagy konfiguráció, és futtassa ezeket a lépéseket újra (importálás és szinkronizálás, és győződjön meg arról) mindaddig, amíg a változtatásokat, hogy exportálni kívánt várható.

**A export.csv fájl ismertetése** a fájlt a legtöbb értetődő. Néhány rövidítések a megértése érdekében:
* OMODT – objektumtípus-módosítás. Azt jelzi, hogy a művelet az objektumok szintjén egy hozzáadása, frissítése vagy törlése.
* AMODT – attribútum módosítás típusa. Azt jelzi, hogy a művelet egy attribútum szinten egy hozzáadása, frissítése vagy törlése.

**Közös azonosítók lekéréséhez** a export.csv fájl tartalmaz, amelyek az exportálni kívánt összes módosítást. Egy objektumot az összekötőtérben változását, minden sor megfelel, és az objektum a megkülönböztető név attribútuma által azonosított. A megkülönböztető név attribútuma rendelve egy objektumot az összekötőtérben egyedi azonosítója. Ha a export.csv elemezheti a sok sort vagy a változtatásokat, döntse el, milyen objektumokat, a változások a következők alapján csak a megkülönböztető név attribútuma meg nehéz lehet. A módosítások elemzése egyszerűbbé, használja a csanalyzer.ps1 PowerShell-parancsfájlt. A szkript lekéri az objektumok közös azonosítói (például displayName, userPrincipalName). A parancsprogram használata:
1. A szakasz a PowerShell-parancsfájl másolása [CSAnalyzer](#appendix-csanalyzer) nevű `csanalyzer.ps1`.
2. Nyisson meg egy PowerShell-ablakot, és keresse meg a mappát, ahol létrehozta a PowerShell-parancsfájlt.
3. Futtatás: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Most már rendelkezik egy fájlt **processedusers1.csv** , amely a Microsoft Excelben kell vizsgálni. Vegye figyelembe, hogy a fájl tartalmazza a DN attribútumból való közös azonosítók (például a displayName és a userPrincipalName). Jelenleg nem tartalmazza a tényleges attribútum változtatásokat, hogy exportálásra váró adatokat.

#### <a name="switch-active-server"></a>Kapcsoló aktív kiszolgáló
1. A jelenleg aktív kiszolgálón vagy kapcsolja ki a kiszolgálót (a DirSync vagy FIM/az Azure AD Sync), nem exportálnia kell azt az Azure AD, vagy állítsa be azt az átmeneti módban (Azure AD Connect).
2. A telepítővarázsló futtatása a kiszolgálón a **átmeneti módban** , és tiltsa le **átmeneti módban**.
   ![ReadyToConfigure](./media/how-to-connect-sync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Vészhelyreállítás
A megvalósítás kialakításának, hogy mi a teendő abban az esetben, katasztrófa amennyiben elveszíti a szinkronizálási kiszolgáló megtervezése. Számos különböző képességekkel szeretné használni, és számos tényezőtől függ, melyiket szeretné használni:

* Mi az a tűrés nem objektumok képesnek módosíthatja a leállás ideje alatt az Azure AD-ben?
* Ha használja a jelszó-szinkronizálás, tegye a felhasználók elfogadják, hogy rendelkeznek-e a régi jelszó használata az Azure ad-ben, abban az esetben a helyszíni módosítását?
* Rendelkezik egy függőséget a valós idejű műveleteket, például a jelszóvisszaíró?

Attól függően, a válaszok ezekre a kérdésekre, és a szervezeti házirend a következő stratégiák egyikét is kell végrehajtani:

* Építse újra, amikor szükséges.
* Rendelkezik egy tartalék készenléti kiszolgáló, más néven **átmeneti módban**.
* Használhat virtuális gépeket.

Ha nem használja az SQL Express beépített adatbázis, akkor tekintse meg a [SQL magas rendelkezésre állású](#sql-high-availability) szakaszban.

### <a name="rebuild-when-needed"></a>Szükség esetén újraépítése
Egy működőképes stratégia, hogy szükség esetén egy kiszolgáló újraépítése tervezése. A szinkronizálási motor és a kezdeti importálás és szinkronizálás elvégezhető, néhány órán belül do általában telepítése. Ha egy tartalék kiszolgáló nem érhető el, ideiglenesen használható egy tartományvezérlőt a szinkronizálási motor üzemeltetésére.

A szinkronizálási motor kiszolgálóhoz nem tárolja az objektumok minden olyan állapotban, az adatbázis újraépíthető, az Active Directory és az Azure AD az adatokból. A **sourceAnchor** attribútum segítségével csatlakozzon a helyszíni és felhőbeli objektumokat. Ha a kiszolgáló meglévő objektumokat a helyszíni és a felhőben, a szinkronizálási motor megegyezik azokat az objektumokat össze újra a újratelepítése, majd építse újra. A dokumentum, és menteni kell a következők a kiszolgálóra, például a szűrés és szinkronizálási szabályok konfigurációs módosításait. Egyéni konfigurációkról kell állítható vissza, szinkronizálásának megkezdése előtt.

### <a name="have-a-spare-standby-server---staging-mode"></a>A tartalék készenléti kiszolgáló – átmeneti módban van
Ha egy ennél összetettebb környezetben, majd egy vagy több készenléti kiszolgáló kellene használata javasolt. A telepítés során engedélyezheti egy kiszolgálóra, hogy a **átmeneti módban**.

További információkért lásd: [átmeneti módban](#staging-mode).

### <a name="use-virtual-machines"></a>Virtuális gépek használata
A gyakori és támogatott, hogy a szinkronizálási motor futtatása virtuális gépen. Abban az esetben a gazdagép rendelkezik egy problémát, a kép és a szinkronizálási motor kiszolgáló áttelepíthetők egy másik kiszolgálóra.

### <a name="sql-high-availability"></a>Az SQL magas rendelkezésre állás
Ha nem használ az SQL Server Express, az Azure AD Connect előre, majd az SQL Server magas rendelkezésre állást is kell tekinteni. A támogatott magas rendelkezésre állású megoldások közé tartozik, az SQL-fürtözés és AOA (Always On rendelkezésre állási csoportok). Nem támogatott megoldások közé tartozik a tükrözés.

SQL AOA támogatása az Azure AD Connect 1.1.524.0 verzióban lett hozzáadva. Az Azure AD Connect telepítése előtt engedélyeznie kell az SQL AOA. A telepítés során az Azure AD Connect észleli a megadott SQL-példány engedélyezve van-e az SQL AOA vagy sem. Ha SQL AOA engedélyezve van, az Azure AD Connect további kitalálja, hogy ha az SQL AOA szinkron replikáció vagy aszinkron replikáció használatára van konfigurálva. Állítson be a rendelkezésre állási csoport figyelőjét, javasoljuk, hogy a RegisterAllProvidersIP tulajdonságot 0 értékre állítja. Ennek az oka, hogy az Azure AD Connect jelenleg SQL natív ügyfél használ kapcsolódni az SQL és az SQL natív ügyfél nem támogatja a MultiSubNetFailover tulajdonság használatát.

## <a name="appendix-csanalyzer"></a>A függelék CSAnalyzer
Című témakör [ellenőrzése](#verify) Ez a szkript használatával.

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
**Áttekintő témakör**  

* [Az Azure AD Connect szinkronizálása: Megismerheti, és testre szabhatja a szinkronizálás](how-to-connect-sync-whatis.md)  
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)  
