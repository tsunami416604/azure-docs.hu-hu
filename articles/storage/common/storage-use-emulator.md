---
title: Az Azure storage emulator használata a fejlesztéshez és teszteléshez |} Microsoft Docs
description: Az Azure storage emulator egy szabad helyi fejlesztési környezetet biztosít az fejlesztés és tesztelés az Azure Storage-alkalmazások. Ismerje meg, hogyan kérések hitelesítése az emulátor csatlakoztatása az alkalmazásról és a parancssori eszköz használatával.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: tamram
ms.openlocfilehash: c16bf1e750ea059e663e05c91835884eb0bc54a5
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Az Azure storage emulator használata a fejlesztéshez és teszteléshez

A Microsoft Azure storage emulator emulálja a fejlesztéshez Azure Blob, Queue és Table szolgáltatások, helyi környezetet biztosít. A storage emulator használatával, akkor nélkül lehet tesztelni az alkalmazást helyileg, a tárolási szolgáltatások az Azure-előfizetés létrehozása vagy az ezzel járó költségeket. Ha elégedett az alkalmazást az emulátorban alakulását, megváltoztathatja az Azure storage-fiók használata a felhőben.

## <a name="get-the-storage-emulator"></a>A storage emulator beolvasása
A storage emulator áll rendelkezésre, mert része a [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). A storage emulator használatával is telepíthet a [önálló telepítő](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (közvetlen letöltése). A storage emulator telepítéséhez rendszergazdai jogosultsággal kell rendelkeznie a számítógépen.

A storage emulator jelenleg kizárólag Windows rendszeren fut. Annak eldöntéséhez, hogy a storage emulator Linux azok egy beállítás akkor a közösségi fenn, nyílt forráskódú storage emulator [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> A storage emulator egy verziójában létrehozott adatokat elérhetőnek kell lennie egy másik verzió használata esetén nem garantált. Ha szeretné megőrizni az adatokat a hosszú távú, ajánlott adatok tárolásához Azure storage-fiók helyett a storage emulator.
> <p/>
> A storage emulator attól függ, hogy az OData-tárak adott verzióját. A storage emulator más verziókkal által használt OData dll cseréje nem támogatott, és nem várt működést eredményezhet. Azonban a tároló szolgáltatás által támogatott OData bármely verziója használható kérelmeket küldeni az emulátorban.
>

## <a name="how-the-storage-emulator-works"></a>A storage emulator működése
A storage Emulator egy Microsoft SQL Server helyi példányát és a helyi fájlrendszer emulálni Azure storage szolgáltatások. Alapértelmezés szerint a storage Emulator egy adatbázis a Microsoft SQL Server 2012 Express LocalDB. Kiválaszthatja, konfigurálhatja a storage emulator eléréséhez a LocalDB példányához helyett az SQL Server helyi példányát. További információkért lásd: a [kezdő- és a storage emulator inicializálása](#start-and-initialize-the-storage-emulator) szakasz a cikk későbbi részében.

A storage emulator az SQL Server vagy Windows-hitelesítést használó LocalDB csatlakozik.

Néhány funkcióbeli különbségek létezik a storage emulator és az Azure storage szolgáltatások között. Ezek a különbségek kapcsolatos további információkért tekintse meg a [a storage emulator és az Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) szakasz a cikk későbbi részében.

## <a name="start-and-initialize-the-storage-emulator"></a>Indítsa el, és a storage emulator inicializálása

Az Azure storage emulator indítása:
1. Válassza ki a **Start** gombra vagy nyomja le az **Windows** kulcs.
2. Írja be a szöveget `Azure Storage Emulator`.
3. Válassza ki az emulátor megjelenített alkalmazások listája.

A storage emulator indításakor jelenik meg egy parancssori ablakot. A konzolablakban segítségével indítsa el és állítsa le a storage emulator, törölje az adatokat, állapotának beolvasása, és az emulátor inicializálni. További információkért lásd: a [Storage emulator parancssori eszköz hivatkozás](#storage-emulator-command-line-tool-reference) szakasz a cikk későbbi részében.

Amikor az emulátor fut, a Windows tálca értesítési területén egy ikon látható.

Amikor bezárja a storage emulator parancssori ablakban, a storage emulator továbbra is futtatható. A Storage Emulator konzolablakban újra elindítani, kövesse a fenti lépéseket, mintha a storage emulator indítása.

A storage emulator az első futtatásakor meg a helyi tároló környezet inicializálását. Az inicializálási folyamatot adatbázist hoz létre LocalDB és fenntartja a HTTP-portok minden egyes helyi tároló szolgáltatás.

A storage emulator alapértelmezés szerint telepítve van `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`.

> [!TIP]
> Használhatja a [Microsoft Azure Tártallózó](http://storageexplorer.com) a helyi storage emulator erőforrásokat. Keresse meg "(fejlesztés)" a "Tárfiókok" a Tártallózó erőforrások fában telepítve és a storage emulator lépések után.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>A storage emulator egy másik SQL-adatbázist használja inicializálása

A storage emulator parancssori eszköz segítségével a storage emulator egy SQL adatbázis-példányt az alapértelmezett LocalDB példányához eltérő mutasson inicializálása:

1. Nyissa meg a Storage Emulator konzolablakban leírtak a [kezdő- és a storage emulator inicializálása](#start-and-initialize-the-storage-emulator) szakasz.
1. A konzolablakban, írja be a következő parancsot, ahol `<SQLServerInstance>` az SQL Server-példány neve. LocalDB használatához adja meg `(localdb)\MSSQLLocalDb` az SQL Server-példányt.

  `AzureStorageEmulator.exe init /server <SQLServerInstance>`

  A következő parancsot, amely arra utasítja az emulátor, SQL Server alapértelmezett példányának használatára is használhatja:

  `AzureStorageEmulator.exe init /server .\\`

  Vagy a következő parancsot, amely az adatbázis az alapértelmezett LocalDB példányához újrainicializálja használhatja:

  `AzureStorageEmulator.exe init /forceCreate`

Ezek a parancsok kapcsolatos további információkért lásd: [Storage emulator parancssori eszköz hivatkozás](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Használhatja a [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) kezelheti az SQL Server-példányokat, beleértve a LocalDB telepítését. A SMSS a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg `(localdb)\MSSQLLocalDb` a a **kiszolgálónév:** mező a LocalDB példányához való kapcsolódáshoz.

## <a name="authenticating-requests-against-the-storage-emulator"></a>A storage emulatorban kérések hitelesítése
Miután telepített, és a storage emulator elindult, tesztelheti rajta a kódot. Az Azure Storage a felhőben, minden kérelemnél elvégezte a storage emulatorban hitelesíteni kell, kivéve, ha egy névtelen kérelem. A storage emulatorban megosztott kulcsos hitelesítést használ, vagy a közös hozzáférésű jogosultságkód (SAS) kérelmek hitelesítheti.

### <a name="authenticate-with-shared-key-credentials"></a>Hitelesítés megosztott kulcsos hitelesítő adatokkal
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

A kapcsolati karakterláncok további információkért lásd: [konfigurálása Azure Storage kapcsolati karakterláncok](../storage-configure-connection-string.md).

### <a name="authenticate-with-a-shared-access-signature"></a>A közös hozzáférésű jogosultságkód hitelesítéshez
Egy Azure storage ügyfélkódtáraival, például a Xamarin-függvénytárat, csak egy közös hozzáférésű jogosultságkód (SAS) jogkivonattal hitelesítés támogatására. A SAS-jogkivonat hasonló eszköz használatával hozhat létre a [Tártallózó](http://storageexplorer.com/) vagy egy másik alkalmazás, amely támogatja a megosztott kulcsos hitelesítést.

Azure PowerShell használatával is létrehozhat egy SAS-jogkivonatot. Az alábbi példa létrehoz egy SAS jogkivonatot teljes körű jogosultsága legyen a blob-tároló:

1. Telepítse az Azure PowerShell Ha még (a legújabb verzióját használja az Azure PowerShell parancsmagok ajánlott). A telepítési utasításokért lásd: [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/install-azurerm-ps).
2. Nyissa meg az Azure PowerShell és a következő parancsokat, cseréje `ACCOUNT_NAME` és `ACCOUNT_KEY==` a saját hitelesítő adataival, és `CONTAINER_NAME` a néven:

```powershell
$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="

New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Az új tároló az eredményül kapott közös hozzáférésű jogosultságkódot URI a következőképpen kell kinéznie:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

A közös hozzáférésű jogosultságkódot létrehozni ebben a példában egy napig érvénytelen. Az aláírás a tárolóban található blobok teljes hozzáférést (olvasási, írási, törlés, lista) biztosít.

További információ a közös hozzáférésű jogosultságkódokról: [használata közös hozzáférésű jogosultságkód (SAS) az Azure Storage](../storage-dotnet-shared-access-signature-part-1.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>A storage emulator címzési erőforrások
A storage Emulator szolgáltatásvégpontokra különböznek az Azure storage-fiók. A különbség az, mert a helyi számítógép nem végez a tartománynév feloldásával, hogy a storage emulator végpontok kell lennie a helyi címek esetén.

Cím egy Azure storage-fiókot az erőforráshoz, használja a következő séma. A fióknév URI állomásnév részét képezi, és az erőforrás megközelítése az URI elérési út egy része:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Például a következő URI-azonosítója az Azure-tárfiók a blob egy érvényes címet:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

A storage emulatort, mivel a helyi számítógép nem végez a tartománynév feloldásával, a fiók neve van az állomásnév helyett az URI elérési út része. A storage emulator egy erőforrás használja a következő URI-formátum:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Például a következő cím felhasználható a storage emulator a blob eléréséhez:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

A storage Emulator szolgáltatásvégpontokra a következők:

* BLOB szolgáltatás: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Queue szolgáltatás: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* TABLE szolgáltatás: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>A fiók az RA-GRS másodlagos címzés
3.1-es verziójával kezdve a storage emulator írásvédett georedundáns replikáció (RA-GRS) támogatja. A tárolási erőforrások a felhőben, és a helyi emulátorban, végezheti el a másodlagos hely az fűznek - másodlagos fióknevet. Például a következő cím felhasználható a storage emulator használatával a csak olvasható másodlagos blob eléréséhez:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> A másodlagos kiszolgálón a storage emulator programozott hozzáférést használja a Storage ügyféloldali kódtára a .NET-hez 3.2-es vagy újabb verziója. Tekintse meg a [Microsoft Azure Storage ügyféloldali kódtára a .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) részleteiről.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Storage emulator parancssori eszköz referencia
3.0-s verziójával kezdődően a konzolablakban jelenik meg a Storage Emulator indításakor. A parancsot használja a konzolablakban indítása és leállítása az emulátor, valamint a lekérdezés az állapotot, és egyéb műveleteket végezhet.

> [!NOTE]
> Ha a Microsoft Azure compute emulator telepítve van, a rendszer tálcaikon akkor jelenik meg, amikor a Storage Emulator indíthatja el. Kattintson a jobb gombbal egy menü indítása és leállítása a Storage Emulator grafikus módszert biztosít a megjelenítéséhez ikonra.
>
>

### <a name="command-line-syntax"></a>Parancssori szintaxis
`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Beállítások
Beállítások listájának megtekintéséhez írja be a `/help` parancsot a parancssorba.

| Beállítás | Leírás | Parancs | Argumentumok |
| --- | --- | --- | --- |
| **Kezdés** |A storage emulator elindul. |`AzureStorageEmulator.exe start [-inprocess]` |*-inprocess*: Indítsa el az emulátort helyett egy új folyamat létrehozása a jelenlegi folyamatban. |
| **állj** |A storage emulator leáll. |`AzureStorageEmulator.exe stop` | |
| **Állapot** |A storage emulator állapotának nyomtatása. |`AzureStorageEmulator.exe status` | |
| **Törölje a jelet** |Törli az összes olyan szolgáltatás, a parancssorban megadott. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]                                                    ` |*a BLOB*: blob-adatok törlése. <br/>*várólista*: várólista adatokat törli. <br/>*tábla*: törli a tábla adatai. <br/>*minden*: az összes szolgáltatás az összes adat törlése. |
| **Init** |Állítsa be az emulátor egyszeri inicializálás hajt végre. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-kiszolgáló Kiszolgáló_neve\példány_neve*: Adja meg az SQL-példányt futtató kiszolgálón. <br/>*-sqlinstance példánynév*: az alapértelmezett kiszolgálópéldánynál használható SQL-példány nevét adja meg. <br/>*-forcecreate*: az SQL-adatbázis létrehozása kényszeríti, még akkor is, ha már létezik. <br/>*-skipcreate*: kihagyja az SQL-adatbázis létrehozása. Ez elsőbbséget élvez - forcecreate.<br/>*-reserveports*: a HTTP-portok megadott szolgáltatásokkal társított kísérletek.<br/>*-unreserveports*: távolítsa el a HTTP-port lefoglalását megkísérli a szolgáltatásokkal kapcsolatos. Ez elsőbbséget élvez - reserveports.<br/>*-inprocess*: inicializálási végez helyett egy új folyamat terjesztése a jelenlegi folyamatban. Az aktuális folyamat emelt jogosultsági szintű indítható el, ha port foglalások módosítása. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>A storage emulator és az Azure Storage közötti különbségek
Mivel a storage emulator egy emulált környezet egy helyi SQL-példány fut, számos funkcióit és közötti különbségek az emulátor egy Azure storage-fiók a felhőben.

* A storage emulator csak egyetlen rögzített fiók és egy jól ismert hitelesítési kulcs támogatja.
* A storage emulator nem méretezhető tárhelyre, és nem támogatja a nagyszámú egyidejű ügyfelek.
* A [a storage emulator erőforrások címzési](#addressing-resources-in-the-storage-emulator), erőforrások másképp tárgyalja a storage emulator egy Azure storage-fiókot és. A különbség az, mert a tartománynév feloldásával érhető el a felhőben, de nem a helyi számítógépen.
* 3.1-es verziójával kezdve az emulátor tárfiók írásvédett georedundáns replikáció (RA-GRS) támogatja. Az emulátorban minden fiókok RA-GRS engedélyezve van, és soha nincs bármely lag az elsődleges és másodlagos replikák között. A lekérése Blob szolgáltatás statisztikák, a várólista-szolgáltatás statisztikák beolvasása és a Table szolgáltatás statisztikák beolvasása műveletek támogatottak a másodlagos fiók, és mindig ad vissza az értéket a `LastSyncTime` válasz eleme, amely az aktuális idő alapján az alapul szolgáló SQL-adatbázis.
* A szolgáltatás és az SMB protokoll Szolgáltatásvégpontok jelenleg nem támogatottak a storage emulator a.
* Ha a tárolási szolgáltatások az emulátor által még nem támogatott verziót használ, a storage emulator egy VersionNotSupportedByEmulator hiba (HTTP-állapotkód: 400 - Hibás kérés) adja vissza.

### <a name="differences-for-blob-storage"></a>Blob Storage különbségek
Az alábbiakban különbözik a Blob storage az emulátorban vonatkoznak:

* A storage emulator csak támogatja a blob méretének legfeljebb 2 GB.
* Növekményes másolatot lehetővé teszi, hogy pillanatképeinek felülírt blobot másolni, amely egy sikertelen adja vissza. a szolgáltatás.
* Get tartományokat különbözeti nem működik a másolt növekményes másolatot Blob használatával pillanatképek között.
* Egy Put Blob művelet már szerepel a storage emulator egy aktív bérleti jog vonatkozik, a blob elleni telepítése sikerülhet, még akkor is, ha a címbérlet azonosítója nincs megadva a kérelemben.
* A hozzáfűző Blob műveletek nem támogatottak az emulátor által. A hozzáfűző blob egy műveletet próbált adja vissza egy FeatureNotSupportedByEmulator hiba (HTTP-állapotkód: 400 - Hibás kérés).

### <a name="differences-for-table-storage"></a>A Table storage eltéréseket
A Table storage az emulátorban alkalmazása a következő eltérésekkel:

* A Table szolgáltatás a storage emulator dátum tulajdonságok támogatja a csak az SQL Server 2005 (akkor szükséges lehet későbbi, mint 1753. január 1.) által támogatott tartományon. Minden dátum előtt 1753. január 1. erre az értékre módosult. A pontosság dátumok korlátozva az SQL Server 2005, ami azt jelenti, hogy dátum pontos 1 értékei a pontosság/másodperc 300th.
* A storage emulator partíciós kulcs és a sor kulcstulajdonság-értéket kisebb, mint 512 bájtos támogatja. Ezenkívül a fiók neve, a tábla neve és a kulcstulajdonság neve együtt teljes mérete legfeljebb 900 bájtot.
* A storage emulator egy olyan táblázatában sor teljes mérete legfeljebb 1 MB-nál kevesebb.
* Az adatok tulajdonságok írja be a storage emulator `Edm.Guid` vagy `Edm.Binary` csak a `Equal (eq)` és `NotEqual (ne)` összehasonlító operátorok, a lekérdezési karakterláncok szűréséhez.

### <a name="differences-for-queue-storage"></a>A Queue storage eltéréseket
Nincsenek a Queue storage az emulátorban jellemző különbségek.

## <a name="storage-emulator-release-notes"></a>Storage emulator kibocsátási megjegyzései
### <a name="version-52"></a>5.2-es verzió
* A storage emulator a tárolási szolgáltatások 2017-04-17 verziója mostantól támogatja a Blob, Queue és Table szolgáltatási végpont.
* Rögzített programhiba, ahol tábla tulajdonságértékek volt folyamatban kódolása nem megfelelő.

### <a name="version-51"></a>5.1-es verzió
* Rögzített programhiba, ahol a storage emulator vissza lett a `DataServiceVersion` fejléc a következő néhány válaszokat, ahol a szolgáltatás nem volt.

### <a name="version-50"></a>5.0-s verziója
* A storage emulator telepítő már nem létező MSSQL keres, és telepíti a .NET-keretrendszer.
* A storage emulator telepítő már nem létrehozza az adatbázist, a telepítés részeként. Adatbázis továbbra is létrejön, indítási részeként szükség esetén.
* Az adatbázis létrehozása már nem szükséges jogosultságszint-emelés.
* Lefoglalások port már nincs szükség az indításhoz.
* Az alábbi beállítások hozzáadása `init`: `-reserveports` (jogosultságszint-emelés szükséges), `-unreserveports` (jogosultságszint-emelés szükséges), `-skipcreate`.
* A Storage Emulator felhasználói felületén lehetőséget, a rendszer tálcaikon most elindítja a parancssori felületen. A régi grafikus felhasználói felület már nem érhető el.
* Egyes DLL-ek eltávolították vagy átnevezték.

### <a name="version-46"></a>4.6-os verzió
* A storage emulator a tárolási szolgáltatások 2016-05-31 verziója mostantól támogatja a Blob, Queue és Table szolgáltatási végpont.

### <a name="version-45"></a>4.5-ös verziója
* Egy hiba, inicializálása és telepítését a storage emulator sikertelen lehet, ha a biztonsági adatbázis át lett nevezve, hogy rögzíteni.

### <a name="version-44"></a>4.4 verziója
* A storage emulator mostantól támogatja a tárolási szolgáltatások 2015-12-11-es verzió Blob, Queue és Table szolgáltatás végpontokon.
* A storage emulator szemétgyűjtés a blob típusú adatok funkciója még hatékonyabb a nagyszámú BLOB meghatározásakor.
* Egy hiba, a tároló hozzáférés-vezérlési lista XML némileg eltérően a módját a társzolgáltatás minderre ellenőrizendő, hogy rögzíteni.
* Egy hiba, néha okozott max és min dátum/idő értéket kell megadni a helytelen időzónában rögzített.

### <a name="version-43"></a>4.3 verziója
* A storage emulator a tárolási szolgáltatások 2015-07-08 verziója mostantól támogatja a Blob, Queue és Table szolgáltatási végpont.

### <a name="version-42"></a>4.2-es verzió
* A storage emulator a tárolási szolgáltatások 2015-04-05-ös verziója mostantól támogatja a Blob, Queue és Table szolgáltatási végpont.

### <a name="version-41"></a>4.1-es verziója
* A storage emulator most verzióját támogatja 2015-02-21 a tárolási szolgáltatások Blob, Queue és Table szolgáltatási végponton, kivéve az új hozzáfűző Blob-szolgáltatásokat.
* Ha a tárolási szolgáltatások az emulátor által még nem támogatott verziót használja, az emulátor jelentéssel bíró hibaüzenetet ad vissza. Azt javasoljuk, hogy az emulátor legújabb verzióját használja. Ha egy VersionNotSupportedByEmulator hiba (HTTP-állapotkód: 400 - Hibás kérés), töltse le a storage emulator legújabb verzióját.
* Rögzített programhiba amelynek egy versenyhelyzet okoz tábla entitás helytelennek egyidejű merge műveletek során.

### <a name="version-40"></a>4.0-s verziója
* A storage emulator végrehajtható fájl át lett nevezve a *AzureStorageEmulator.exe*.

### <a name="version-32"></a>3.2-es verziója
* A storage emulator a tárolási szolgáltatások 2014-02-14-es verziója mostantól támogatja a Blob, Queue és Table szolgáltatási végpont. Fájl Szolgáltatásvégpontok jelenleg nem támogatottak a storage emulator a. Lásd: [az Azure Storage Services Versioning](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) a 2014-02-14-es verzióval kapcsolatos adatokat.

### <a name="version-31"></a>3.1-es verziója
* Írásvédett georedundáns tárolás (RA-GRS) mostantól a storage emulator használata támogatott. A Blob szolgáltatás statisztikák beolvasása, várólista-szolgáltatás statisztikák beolvasása és első tábla szolgáltatás statisztikák API-másodlagos fiók támogatottak, ezért az aktuális idő alapján az alapul szolgáló SQL-adatbázis szerint a LastSyncTime válasz eleme értékének mindig ad vissza. A másodlagos kiszolgálón a storage emulator programozott hozzáférést használja a Storage ügyféloldali kódtára a .NET-hez 3.2-es vagy újabb verziója. A Microsoft Azure Storage ügyféloldali kódtára a .NET-referencia talál információt.

### <a name="version-30"></a>3.0-s verzió
* Az Azure storage emulator nem képezte a compute emulator azonos csomagban található.
* A storage emulator grafikus felhasználói felület helyett egy parancsfájlok futtatására alkalmas parancssori felület elavult. A parancssori felület a részletekért lásd: a Storage Emulator parancssori eszköz útmutatóban. A grafikus felületen továbbra is a 3.0-s verziója található, de csak elérhető legyen a Compute Emulator csomagot jobb gombbal a tálcaikonjára, majd válassza a Storage Emulator UI megjelenítése telepítésekor.
* Az Azure storage szolgáltatásainak verzió 2013-08-15 most már teljes mértékben támogatott. (Korábban Storage Emulator 2.2.1-en verziója által támogatott az ebben a verzióban csak előzetes.)

## <a name="next-steps"></a>További lépések

* Értékelje ki a platformfüggetlen, közösségi karbantartása nyílt forráskódú storage emulator [Azurite](https://github.com/arafato/azurite). 
* [Azure Storage-minták .NET használatával](../storage-samples-dotnet.md) is használhatja, ha az alkalmazás fejlesztése több mintakódok mutató hivatkozásokat tartalmaz.
* Használhatja a [Microsoft Azure Tártallózó](http://storageexplorer.com) a Storage-fiók a felhőben, és a storage emulator az erőforrásokat.
