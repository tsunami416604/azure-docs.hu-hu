---
title: Az Azure Storage Emulator használata fejlesztési és tesztelési célokra | Microsoft Docs
description: Az Azure Storage Emulator ingyenes helyi fejlesztési környezetet biztosít az Azure Storage-alkalmazások fejlesztéséhez és teszteléséhez. Megtudhatja, hogyan hitelesítheti a kéréseket, hogyan csatlakozhat az emulátorhoz az alkalmazásból, és hogyan használhatja a parancssori eszközt.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: a50b397ffe1cfc44d4234dcfbee1618e9fb2506c
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900346"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Az Azure Storage Emulator használata fejlesztéshez és teszteléshez

A Microsoft Azure Storage Emulator egy helyi környezetet biztosít, amely az Azure Blob, a üzenetsor és a Table Services fejlesztését emulálja. A Storage Emulator használatával helyileg, Azure-előfizetés létrehozása vagy költségek nélkül tesztelheti alkalmazását a Storage Services szolgáltatásban. Ha elégedett az alkalmazás működésével az emulátorban, átválthat egy Azure Storage-fiók használatára a felhőben.

## <a name="get-the-storage-emulator"></a>A Storage Emulator beszerzése

A Storage Emulator a [Microsoft Azure SDK](https://azure.microsoft.com/downloads/)részeként érhető el. A Storage emulatort a [különálló telepítő](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (közvetlen letöltés) használatával is telepítheti. A Storage Emulator telepítéséhez rendszergazdai jogosultságokkal kell rendelkeznie a számítógépen.

A Storage Emulator jelenleg csak Windows rendszeren fut. A Linux rendszerhez készült Storage emulatort figyelembe véve az egyik lehetőség a Közösség által karbantartott, nyílt forráskódú Storage Emulator [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> A Storage Emulator egyik verziójában létrehozott adatmennyiség nem garantált, hogy más verzió használata esetén nem érhető el. Ha hosszú távon is meg kell őriznie az adatait, javasoljuk, hogy az adatait egy Azure Storage-fiókban tárolja, nem pedig a Storage emulatorban.
> 
> A Storage Emulator a OData-kódtárak adott verzióitól függ. A Storage Emulator által más verziókkal használt OData DLL-ek nem támogatottak, és nem várt viselkedést okozhatnak. A tárolási szolgáltatás által támogatott bármely OData azonban a kérelmeknek az emulátorba való küldésére is használható.

## <a name="how-the-storage-emulator-works"></a>A Storage Emulator működése

A Storage Emulator egy helyi Microsoft SQL Server példányt és a helyi fájlrendszert használ az Azure Storage-szolgáltatások emulálása érdekében. Alapértelmezés szerint a Storage Emulator Microsoft SQL Server 2012 Express LocalDB található adatbázist használ. Beállíthatja, hogy a Storage Emulator a LocalDB-példány helyett SQL Server helyi példányához férhessen hozzá. További információt a cikk későbbi, a [Storage Emulator elindítása és inicializálása](#start-and-initialize-the-storage-emulator) című szakaszában talál.

A Storage Emulator a Windows-hitelesítés használatával csatlakozik SQL Serverhoz vagy LocalDB.

A Storage Emulator és az Azure Storage szolgáltatás között számos funkció létezik. További információ ezekről a különbségekről: a [Storage Emulator és az Azure Storage közötti különbségek](#differences-between-the-storage-emulator-and-azure-storage) a jelen cikk későbbi részében.

## <a name="start-and-initialize-the-storage-emulator"></a>A Storage Emulator elindítása és inicializálása

Az Azure Storage Emulator elindítása:

1. Kattintson a **Start** gombra, vagy nyomja le a **Windows** billentyűt.
2. Kezdjen `Azure Storage Emulator`el gépelni.
3. Válassza ki az emulátort a megjelenített alkalmazások listájából.

A Storage Emulator indításakor megjelenik egy parancssori ablak. A konzol ablakával elindíthatja és leállíthatja a tárolási emulátort, törölheti az adatvesztést, lekérheti az állapotot, és inicializálhatja az emulátort. További információt a cikk későbbi, a [Storage Emulator parancssori eszközének ismertetése](#storage-emulator-command-line-tool-reference) című szakaszában talál.

Amikor fut az emulátor, egy ikont lát a Windows tálca értesítési területén.

A Storage Emulator parancssori ablakának bezárását követően a Storage Emulator továbbra is futni fog. A Storage Emulator Console ablak újbóli létrehozásához kövesse az előző lépéseket a Storage Emulator elindítása után.

Amikor első alkalommal futtatja a Storage emulatort, a helyi tárolási környezet inicializálva lesz. Az inicializálási folyamat létrehoz egy adatbázist a LocalDB-ben, és minden helyi tárolási szolgáltatáshoz fenntart HTTP-portokat.

A Storage Emulator alapértelmezés `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`szerint telepítve van.

> [!TIP]
> A [Microsoft Azure Storage Explorer](https://storageexplorer.com) használatával dolgozhat a helyi tároló-emulátor erőforrásaival. A Storage-emulátor telepítése és elindítása után keresse meg a "(fejlesztés)" lehetőséget a "Storage-fiókok" alatt a Storage Explorer erőforrások fájában.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>A Storage Emulator inicializálása másik SQL-adatbázis használatára

A Storage Emulator parancssori eszköz használatával inicializálhatja a Storage-emulátort úgy, hogy az az alapértelmezett LocalDB-példánytól eltérő SQL Database-példányra mutasson:

1. Nyissa meg a Storage Emulator Console ablakot a [Storage Emulator elindítása és inicializálása](#start-and-initialize-the-storage-emulator) című szakaszban leírtak szerint.
1. A konzol ablakban írja be a következő parancsot, ahol `<SQLServerInstance>` a a SQL Server példány neve. A LocalDB használatához a SQL Server `(localdb)\MSSQLLocalDb` példányként kell megadnia.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   A következő parancsot is használhatja, amely az emulátort az alapértelmezett SQL Server példány használatára irányítja:

   `AzureStorageEmulator.exe init /server .`

   Használhatja a következő parancsot is, amely újrainicializálja az adatbázist az alapértelmezett LocalDB-példányra:

   `AzureStorageEmulator.exe init /forceCreate`

További információt ezekről a parancsokról a [Storage Emulator parancssori eszköz dokumentációjában](#storage-emulator-command-line-tool-reference)talál.

> [!TIP]
> A [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) használatával kezelheti SQL Server példányait, beleértve a LocalDB telepítését is. Az SMS- **kapcsolat a kiszolgálóhoz** párbeszédpanelen adja meg `(localdb)\MSSQLLocalDb` a **kiszolgáló neve:** mezőben, hogy csatlakozhasson a LocalDB-példányhoz.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Kérelmek hitelesítése a Storage Emulator használatával

Miután telepítette és elindította a Storage emulatort, tesztelheti a kódját. Ahogy az Azure Storage-ban a felhőben, a Storage-emulátoron végzett összes kérést engedélyezni kell, kivéve, ha névtelen kérés. Engedélyezheti a kérelmeket a Storage-emulátoron megosztott kulcsos hitelesítéssel vagy közös hozzáférésű aláírással (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Hitelesítés megosztott kulcsú hitelesítő adatokkal

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

További információ a kapcsolatok sztringekről: az [Azure Storage-beli kapcsolatok karakterláncának konfigurálása](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Hitelesítés közös hozzáférésű aláírással

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Néhány Azure Storage-ügyfél kódtára, például a Xamarin-könyvtár, csak a közös hozzáférésű aláírás (SAS) jogkivonatával történő hitelesítést támogatja. Az SAS-tokent egy olyan eszköz használatával hozhatja létre, mint a [Storage Explorer](https://storageexplorer.com/) vagy egy olyan alkalmazás, amely támogatja a megosztott kulcsos hitelesítést.

A Azure PowerShell használatával is létrehozhat SAS-tokent. Az alábbi példa egy blob-tárolóra vonatkozó teljes körű engedélyekkel rendelkező SAS-tokent hoz létre:

1. Azure PowerShell telepítése, ha még nem tette meg (az Azure PowerShell-parancsmagok legújabb verziójának használata ajánlott). A telepítési utasításokért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-Az-ps).
2. Nyissa meg Azure PowerShell és futtassa a következő parancsokat `CONTAINER_NAME` , és cserélje le a nevet a választott névre:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Az új tárolóhoz tartozó, közös hozzáférésű aláírás URI-azonosítójának a következőhöz hasonlónak kell lennie:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Az ezzel a példával létrehozott megosztott elérési aláírás egy napig érvényes. Az aláírás teljes hozzáférést biztosít (olvasási, írási, törlési, listázási) a tárolóban lévő blobokhoz.

A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Erőforrások kezelése a Storage emulatorban

A Storage Emulator szolgáltatási végpontok eltérnek az Azure Storage-fióktól. A különbség az, hogy a helyi számítógép nem hajtja végre a tartománynevek feloldását, ami megköveteli, hogy a Storage Emulator-végpontok helyi címek legyenek.

Amikor egy Azure Storage-fiókban kezel egy erőforrást, a következő sémát kell használnia. A fiók neve az URI-állomásnév része, és a címzett erőforrás az URI elérési útja része:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

A következő URI például egy Azure Storage-fiókban található blob érvényes címe:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Azonban a Storage Emulator, mivel a helyi számítógép nem hajtja végre a tartománynevek feloldását, a fiók neve az állomásnév helyett az URI elérési útja része. Használja a következő URI-formátumot egy erőforráshoz a Storage emulatorban:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Például a következő címről lehet hozzáférni egy blobhoz a Storage emulatorban:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

A Storage Emulator szolgáltatás-végpontok a következők:

* Blob service:`http://127.0.0.1:10000/<account-name>/<resource-path>`
* Queue szolgáltatás:`http://127.0.0.1:10001/<account-name>/<resource-path>`
* Table service:`http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Másodlagos fiók kezelése RA-GRS

A 3,1-es verziótól kezdődően a Storage Emulator támogatja az olvasási hozzáférésű geo-redundáns replikálást (RA-GRS). A tárolási erőforrások mind a felhőben, mind a helyi emulátorban a másodlagos helyet a fióknév hozzáfűzésével érhetik el. Például a következő címről lehet hozzáférni egy blobhoz a Storage Emulator írásvédett másodlagos funkciójával:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> A másodlagos és a Storage-emulátor programozott eléréséhez használja a Storage ügyféloldali kódtárat a .NET 3,2-es vagy újabb verziójához. További részletekért tekintse meg a [.net-hez készült ügyféloldali kódtár Microsoft Azure Storage](https://msdn.microsoft.com/library/azure/dn261237.aspx) .
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>A Storage Emulator parancssori eszközének dokumentációja

Az 3,0-es verziótól kezdődően a rendszer a Storage Emulator indításakor megjeleníti a konzol ablakát. A konzol ablakban a parancssorban az emulátor elindításához és leállításához, valamint az állapot lekérdezéséhez és egyéb műveletek végrehajtásához használhatja.

> [!NOTE]
> Ha telepítve van a Microsoft Azure Compute Emulator, a Storage Emulator indításakor megjelenik egy rendszertálca ikon. Kattintson a jobb gombbal az ikonra egy olyan menü megjelenítéséhez, amely grafikus módot biztosít a tároló-emulátor elindításához és leállításához.
>
>

### <a name="command-line-syntax"></a>Parancssori szintaxis

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Beállítások

A beállítások listájának megtekintéséhez írja be a `/help` parancsot a parancssorba.

| Beállítás | Leírás | Parancs | Argumentumok |
| --- | --- | --- | --- |
| **Start** |Elindítja a Storage emulatort. |`AzureStorageEmulator.exe start [-inprocess]` |*– InProcess*: Új folyamat létrehozása helyett a jelenlegi folyamaton belül indítsa el az emulátort. |
| **állj** |Leállítja a Storage-emulátort. |`AzureStorageEmulator.exe stop` | |
| **Állapot** |A Storage Emulator állapotának kinyomtatása. |`AzureStorageEmulator.exe status` | |
| **Egyértelmű** |Törli a parancssorban megadott összes szolgáltatásban lévő összes értéket. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob*: Törli a blob-adatfájlokat. <br/>*üzenetsor*: Törli a várólista-adatsorokat. <br/>*tábla*: Törli a táblákat. <br/>*összes*: Törli az összes szolgáltatás összes adatértékét. |
| **Init** |Végrehajt egy egyszeri inicializálást az emulátor beállításához. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-kiszolgáló serverName\instanceName*: Az SQL-példányt futtató kiszolgálót adja meg. <br/>*-Sqlinstance példánynév*: Az alapértelmezett kiszolgálópéldány által használandó SQL-példány nevét adja meg. <br/>*-forcecreate*: Akkor is kényszeríti az SQL-adatbázis létrehozását, ha már létezik. <br/>*-skipcreate*: Kihagyja az SQL-adatbázis létrehozását. Ez elsőbbséget élvez a forcecreate szemben.<br/>*-reserveports*: Megkísérli a szolgáltatásokhoz társított HTTP-portok foglalását.<br/>*-unreserveports*: Megkísérli eltávolítani a szolgáltatásokhoz társított HTTP-portok foglalásait. Ez elsőbbséget élvez a reserveports szemben.<br/>*– InProcess*: Az új folyamat létrehozása helyett az aktuális folyamat inicializálását végzi. A jelenlegi folyamatot emelt szintű engedélyekkel kell elindítani, ha módosítja a portok foglalását. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>A Storage Emulator és az Azure Storage közötti különbségek

Mivel a Storage Emulator egy helyi SQL-példányon futó emulált környezet, az emulátor és a felhőben található Azure Storage-fiók között eltérések tapasztalhatók:

* A Storage Emulator csak egyetlen rögzített fiókot és egy jól ismert hitelesítési kulcsot támogat.
* A Storage Emulator nem méretezhető tárolási szolgáltatás, és nem támogatja nagy számú egyidejű ügyfél használatát.
* A [Storage Emulator erőforrásainak kezelése](#addressing-resources-in-the-storage-emulator)című témakörben leírtak szerint az erőforrásokat a Storage-emulátorban és egy Azure Storage-fiókban eltérően kezeli a rendszer. Ez a különbség azért van, mert a tartománynév-feloldás elérhető a felhőben, de nem a helyi számítógépen.
* A 3,1-es verziótól kezdődően a Storage Emulator-fiók támogatja az olvasási hozzáférésű geo-redundáns replikálást (RA-GRS). Az emulátorban minden fiókhoz engedélyezve van az RA-GRS, és az elsődleges és a másodlagos replikák között soha nem marad késés. A blob szolgáltatás statisztikáinak beolvasása, a várólista-szolgáltatás statisztikáinak beolvasása és a Table szolgáltatás statisztikáinak beolvasása támogatott a másodlagos fiókban, és a `LastSyncTime` válasz elem értékét mindig a mögöttes SQL-adatbázisnak megfelelő aktuális időpontra fogja visszaadni.
* A file Service és az SMB protokoll szolgáltatási végpontok jelenleg nem támogatottak a Storage emulatorban.
* Ha a Storage szolgáltatás olyan verzióját használja, amelyet az emulátor még nem támogat, a Storage Emulator VersionNotSupportedByEmulator-hibát ad vissza (HTTP-állapotkód: 400 – hibás kérés).

### <a name="differences-for-blob-storage"></a>BLOB Storage-beli különbségek

Az emulátorban a blob Storage-ra a következő különbségek érvényesek:

* A Storage Emulator legfeljebb 2 GB méretű blob-méreteket támogat.
* A Blobok nevének maximális hossza a Storage-emulátorban 256 karakter, míg az Azure Storage-beli blob-nevek maximális hossza 1024 karakter.
* A növekményes másolás lehetővé teszi a felülírt Blobok másolását, így a szolgáltatás hibáját adja vissza.
* Az oldal-tartományok különbözetének beolvasása nem működik a növekményes másolási blob használatával másolt Pillanatképek között.
* Egy Put blob művelet egy olyan blobnál lehet sikeres, amely egy aktív bérlettel rendelkezik a Storage-emulátorban, még akkor is, ha a kérelemben nincs megadva a címbérlet azonosítója.
* Az emulátor nem támogatja a Blobok hozzáfűzését. Egy hozzáfűző blobon végrehajtott művelet egy FeatureNotSupportedByEmulator-hibát ad vissza (HTTP 400 – hibás kérés).

### <a name="differences-for-table-storage"></a>Táblázatos tárolással kapcsolatos különbségek

A következő eltérések vonatkoznak az emulátorban található Table Storage-ra:

* A Storage Emulator Table servicejában a dátum tulajdonságai csak a SQL Server 2005 által támogatott tartományokat támogatják (a 1753. január 1-től). A 1753. január 1. előtti összes dátum erre az értékre módosult. A dátumok pontossága a SQL Server 2005 pontosságára korlátozódik, ami azt jelenti, hogy a dátumok pontosak egy másodperc 1/300th.
* A Storage Emulator a partíciós kulcs és a sor kulcsa tulajdonságának értékét kevesebb mint 512 bájtra támogatja. Emellett a fiók neve, a tábla neve és a kulcs tulajdonságainak nevei együttesen nem haladhatják meg a 900 bájtot.
* Egy tábla sorainak teljes mérete a Storage-emulátorban 1 MB-nál kevesebb értékre van korlátozva.
* A `Edm.Guid` Storage-emulátorban az adattípus tulajdonságai vagy `Edm.Binary` csak a lekérdezési szűrő `NotEqual (ne)` sztringek és az `Equal (eq)` összehasonlító operátorok támogatása.

### <a name="differences-for-queue-storage"></a>A várólista-tárolással kapcsolatos különbségek

Nincsenek eltérések az emulátorban található üzenetsor-tároláshoz.

## <a name="storage-emulator-release-notes"></a>A Storage Emulator kibocsátási megjegyzései

### <a name="version-510"></a>5,10-es verzió

* A Storage Emulator nem utasítja el a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2019-07-07-es verzióját.

### <a name="version-59"></a>5,9-es verzió

* A Storage Emulator nem utasítja el a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2019-02-02-es verzióját.

### <a name="version-58"></a>5,8-es verzió

* A Storage Emulator nem utasítja el a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2018-11-09-es verzióját.

### <a name="version-57"></a>5,7-es verzió

* Kijavítva egy olyan hibát, amely összeomlást okoz, ha a naplózás engedélyezve volt.

### <a name="version-56"></a>5,6-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2018-03-28-os verzióját.

### <a name="version-55"></a>5,5-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2017-11-09-os verzióját.
* Támogatás lett hozzáadva a blob **által létrehozott** tulajdonsághoz, amely a blob létrehozási idejét adja vissza.

### <a name="version-54"></a>5,4-es verzió

* A telepítési stabilitás javítása érdekében az emulátor már nem próbálkozik a portok lefoglalására a telepítési időpontban. Ha a portok fenntartására van szükség, használja az **init** parancs *-reserveports* kapcsolóját a megadásához.

### <a name="version-53"></a>5,3-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2017-07-29-os verzióját.

### <a name="version-52"></a>5,2-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2017-04-17-os verzióját.
* Kijavítva egy hiba, ahol a Table tulajdonság értékei nem lettek megfelelően kódolva.

### <a name="version-51"></a>5,1-es verzió

* Kijavítva egy olyan hibát, amelyben a Storage Emulator `DataServiceVersion` a fejlécet olyan válaszokban adta vissza, amelyekben a szolgáltatás nem volt elérhető.

### <a name="version-50"></a>5,0-es verzió

* A Storage Emulator telepítője már nem ellenőrzi a meglévő MSSQL-és .NET-keretrendszer telepítéseit.
* A Storage Emulator telepítője már nem hozza létre az adatbázist a telepítés részeként. A rendszer a rendszerindítás részeként továbbra is létrehozza az adatbázist.
* Az adatbázis létrehozása már nem igényel jogosultságszint-emelést.
* A portok foglalása már nem szükséges az indításhoz.
* `init`A következő lehetőségeket adja hozzá a `-reserveports` következőkhöz: (jogosultságszint emelése szükséges), `-unreserveports` (jogosultságszint `-skipcreate`-emelést igényel),.
* A tárolási emulátor felhasználói felületének beállítása a rendszertálca ikonján most elindítja a parancssori felületet. A régi GUI már nem érhető el.
* Egyes DLL-fájlok el lettek távolítva vagy átnevezve lettek.

### <a name="version-46"></a>4,6-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2016-05-31-os verzióját.

### <a name="version-45"></a>4,5-es verzió

* Kijavítva egy olyan hibát, amely a Storage-emulátor inicializálását és telepítését eredményezte a háttér-adatbázis átnevezése során.

### <a name="version-44"></a>4,4-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2015-12-11-os verzióját.
* A Storage Emulator blob-adatokhoz tartozó Garbage-gyűjteménye hatékonyabban működik, ha nagy mennyiségű blobot kezel.
* Kijavított egy hibát, amely miatt a tároló ACL XML-fájlja némileg másképpen ellenőrizhető, hogy a tárolási szolgáltatás hogyan működik.
* Kijavítva egy olyan hibát, amely néha a maximális és a minimális dátum/idő értéket okozta, hogy a rendszer a helytelen időzónában jelentsen.

### <a name="version-43"></a>4,3-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2015-07-08-os verzióját.

### <a name="version-42"></a>4,2-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2015-04-05-os verzióját.

### <a name="version-41"></a>4,1-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2015-02-21-os verzióját, kivéve az új hozzáfűző blob-funkciókat.
* Ha a tárolási szolgáltatások olyan verzióját használja, amelyet az emulátor még nem támogat, az emulátor egy értelmes hibaüzenetet ad vissza. Javasoljuk, hogy az emulátor legújabb verzióját használja. Ha VersionNotSupportedByEmulator hibába ütközik (HTTP-állapotkód: 400 – hibás kérés), töltse le a Storage Emulator legújabb verzióját.
* Kijavítva egy olyan hibát, amelyben a versenyhelyzet feltétele miatt helytelenek voltak a tábla entitások az egyidejű egyesítési műveletek során.

### <a name="version-40"></a>4,0-es verzió

* A Storage Emulator végrehajtható fájlját átnevezték a *AzureStorageEmulator. exe*fájlra.

### <a name="version-32"></a>3,2-es verzió

* A Storage Emulator mostantól támogatja a blob-, üzenetsor-és Table service-végpontok tárolási szolgáltatásainak 2014-02-14-os verzióját. A file Service-végpontok jelenleg nem támogatottak a Storage emulatorban. Az 2014-02-14-es verzióval kapcsolatos részletekért tekintse meg [Az Azure Storage szolgáltatás verziószámozását](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) .

### <a name="version-31"></a>3,1-es verzió

* Az olvasási hozzáférésű geo-redundáns tárolás (RA-GRS) mostantól támogatott a Storage emulatorban. A blob szolgáltatás statisztikáinak beolvasása, a várólista-szolgáltatás statisztikáinak beolvasása és a Table Service stats API-k beolvasása támogatott a másodlagos fiók esetében, és mindig a LastSyncTime válasz elemének értékét fogja visszaadni a mögöttes SQL-adatbázisnak megfelelő aktuális időpontnak megfelelően. A másodlagos és a Storage-emulátor programozott eléréséhez használja a Storage ügyféloldali kódtárat a .NET 3,2-es vagy újabb verziójához. A részletekért tekintse meg a .NET-hez készült Microsoft Azure Storage ügyféloldali kódtárat ismertető témakört.

### <a name="version-30"></a>3,0-es verzió

* Az Azure Storage-emulátor már nem ugyanarra a csomagba kerül, mint a Compute Emulator.
* A Storage Emulator grafikus felhasználói felülete elavult parancssori felület mellett elavulttá vált. A parancssori felület részletes ismertetését lásd a Storage Emulator parancssori eszköz dokumentációjában. A grafikus felület továbbra is megtalálható a 3,0-es verzióban, de csak akkor érhető el, ha a számítási emulátor telepítve van, ha a jobb gombbal a rendszertálca ikonra kattint, és kiválasztja a Storage Emulator felhasználói felületének megjelenítése lehetőséget.
* Az Azure Storage-szolgáltatások 2013-08-15-es verziója már teljes mértékben támogatott. (Ezt a verziót korábban csak a Storage Emulator 2.2.1 előzetes verziója támogatja.)

## <a name="next-steps"></a>További lépések

* Értékelje ki a többplatformos, Közösség által karbantartott nyílt forráskódú Storage Emulator- [Azurite](https://github.com/arafato/azurite). 
* A .NET-et [használó Azure Storage-minták](../storage-samples-dotnet.md) több, az alkalmazás fejlesztésekor felhasználható kódrészletre mutató hivatkozásokat is tartalmaznak.
* A [Microsoft Azure Storage Explorer](https://storageexplorer.com) használatával dolgozhat a felhőalapú Storage-fiókban található erőforrásokkal, és a Storage emulatorban is.
