---
title: Az Azure storage emulátorának használata fejlesztéshez és teszteléshez | Microsoft dokumentumok
description: Az Azure storage-emulátor ingyenes helyi fejlesztői környezetet biztosít az Azure Storage-alkalmazások fejlesztéséhez és teszteléséhez.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 997aa9d96f2f52331865fd15d97443d74bb8bc1f
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398019"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Az Azure storage-emulátor használata fejlesztéshez és teszteléshez

A Microsoft Azure storage-emulátor egy olyan eszköz, amely helyi fejlesztési célokra emulálja az Azure Blob, Queue és Table szolgáltatásokat. Az alkalmazást helyileg tesztelheti a tárolási szolgáltatásokkal szemben anélkül, hogy létrehozna egy Azure-előfizetést, vagy bármilyen költségmerülne fel. Ha elégedett azzal, ahogyan az alkalmazás működik az emulátorban, váltson egy Azure-tárfiók használatára a felhőben.

## <a name="get-the-storage-emulator"></a>A tároló emulátor ának betöltése

A storage-emulátor a [Microsoft Azure SDK](https://azure.microsoft.com/downloads/)részeként érhető el. A tároló emulátort az [önálló telepítő](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (közvetlen letöltés) segítségével is telepítheti. A tárolóemulátor telepítéséhez rendszergazdai jogosultságokkal kell rendelkeznie a számítógépen.

A tárolóemulátor jelenleg csak Windows rendszeren fut. Ha szüksége van egy tárolási emulátor Linux, az egyik lehetőség a közösség által fenntartott, nyílt forráskódú tároló emulátor [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> A tárolóemulátor egyik verziójában létrehozott adatok nem garantáltan elérhetők egy másik verzió használata esetén. Ha hosszú távon meg kell persistnie az adatokat, azt javasoljuk, hogy az adatokat egy Azure-tárfiókban tárolja, ne pedig a storage-emulátorban.
> 
> A tárolóemulátor az OData-kódtárak adott verzióitól függ. A tárolóemulátor által használt OData DL-ek más verziókkal való lecserélése nem támogatott, és nem várt viselkedést okozhat. A tárolószolgáltatás által támogatott OData-verziók azonban felhasználhatók az emulátornak küldött kérelmek küldésére.

## <a name="how-the-storage-emulator-works"></a>A tárolóemulátor működése

A tároló-emulátor egy helyi Microsoft SQL Server 2012 Express LocalDB példányt használ az Azure storage-szolgáltatások emulálásához. A storage emulátorkonfigurálása a LocalDB-példány helyett az SQL Server helyi példányának eléréséhez használható. További információért tekintse meg a cikk későbbi részében a [tárolási emulátor indítása és inicializálása](#start-and-initialize-the-storage-emulator) című szakaszt.

A tárolóemulátor Windows-hitelesítéssel csatlakozik az SQL Server vagy a LocalDB kiszolgálóhoz.

A storage-emulátor és az Azure storage-szolgáltatások között bizonyos működési különbségek vannak. Ezekről a különbségekről a jelen cikk későbbi részében [a tárolóemulátor és az Azure Storage közötti különbségek](#differences-between-the-storage-emulator-and-azure-storage) című szakaszban olvashat bővebben.

## <a name="start-and-initialize-the-storage-emulator"></a>A tárolóemulátor indítása és inicializálása

Az Azure storage-emulátor indítása:

1. Válassza a **Start** gombot, vagy nyomja le a **Windows** billentyűt.
2. Kezdjen `Azure Storage Emulator`el gépelni .
3. Válassza ki az emulátort a megjelenített alkalmazások listájából.

Amikor a tároló emulátor elindul, megjelenik egy parancssorablak. Ezzel a konzolablakkal elindíthatja és leállíthatja a tárolóemulátort. Törölheti az adatokat, állapotot is kaphat, és inicializálhatja az emulátort a parancssorból. További információt a [cikk későbbi, a Storage emulátor parancssori eszközreferencia](#storage-emulator-command-line-tool-reference) szakaszában talál.

> [!NOTE]
> Előfordulhat, hogy az Azure storage-emulátor nem indul el megfelelően, ha egy másik tároló-emulátor, például az Azurite fut a rendszeren.

Amikor fut az emulátor, egy ikont lát a Windows tálca értesítési területén.

Amikor bezárja a tároló emulátor parancssori ablakát, a tároló emulátor a továbbra is futni fog. A Storage Emulator konzolablak ismételt beállításához kövesse az előző lépéseket úgy, mintha a tároló emulátort indítaná el.

A tároló emulátor első futtatásakor a helyi tárolási környezet inicializálásra kerül. Az inicializálási folyamat létrehoz egy adatbázist a LocalDB-ben, és http-portokat foglal le minden egyes helyi tárolási szolgáltatáshoz.

A tároló emulátor a `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`telepítése alapértelmezés szerint a.

> [!TIP]
> A Microsoft [Azure Storage Explorer](https://storageexplorer.com) segítségével helyi táremlényerőforrásokkal dolgozhat. Keresse meg a "(Emulátor - Alapértelmezett portok) (kulcs)" kifejezést a Storage Explorer erőforrásfájának "Helyi & csatlakoztatva" csoportjában, miután telepítette és elindította a tárolóemulátort.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>A tárolóemulátor inicializálása egy másik SQL-adatbázis használatához

A tárolóemulátor parancssori eszközzel inicializálhatja a tárolóemulátort, hogy az alapértelmezett LocalDB-példánytól eltérő SQL-adatbázispéldányra mutasson:

1. Nyissa meg a Storage Emulator konzol ablakát a Start című szakaszban leírtak szerint, [és inicializálja a tárolóemulátorszakaszt.](#start-and-initialize-the-storage-emulator)
1. A konzolablakba írja be a `<SQLServerInstance>` következő parancsot, ahol az SQL Server-példány neve. A LocalDB használatához `(localdb)\MSSQLLocalDb` adja meg SQL Server-példányként.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   A következő paranccsal is használhatja az emulátort az alapértelmezett SQL Server-példány használatára:

   `AzureStorageEmulator.exe init /server .`

   Vagy használhatja a következő parancsot, amely újrainicializálja az adatbázist az alapértelmezett LocalDB példányra:

   `AzureStorageEmulator.exe init /forceCreate`

Ezekről a parancsokról további információt a [Storage emulátor parancssori eszközútmutatócímű témakörben talál.](#storage-emulator-command-line-tool-reference)

> [!TIP]
> A Microsoft [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) segítségével kezelheti az SQL Server-példányokat, beleértve a LocalDB telepítését is. Az SMSS **Csatlakozás kiszolgálóhoz** `(localdb)\MSSQLLocalDb` párbeszédpanelen adja meg a **Kiszolgáló neve:** mezőt a LocalDB példányhoz való csatlakozáshoz.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Kérelmek hitelesítése a tárolóemulátor ellen

Miután telepítette és elindította a tároló emulátort, tesztelheti a kódot ellene. Minden kérelmet, amit a tároló emulátor ellen tesz, engedélyezni kell, kivéve, ha névtelen kérésről van szó. A tárolóemulátorra vonatkozó kéréseket megosztott kulcsú hitelesítéssel vagy megosztott hozzáférésű aláírással (SAS) engedélyezheti.

### <a name="authorize-with-shared-key-credentials"></a>Engedélyezés megosztott kulcshitelesítő adatokkal

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

A kapcsolati karakterláncokról az [Azure Storage-kapcsolati karakterláncok konfigurálása](../storage-configure-connection-string.md)című témakörben talál további információt.

### <a name="authorize-with-a-shared-access-signature"></a>Engedélyezés megosztott hozzáférésű aláírással

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Egyes Azure storage-ügyfélkódtárak, például a Xamarin-könyvtár, csak a hitelesítést támogatja a megosztott hozzáférésű aláírás (SAS) jogkivonattal. A SAS-jogkivonatot a [Storage Explorer](https://storageexplorer.com/) vagy más olyan alkalmazás segítségével hozhatja létre, amely támogatja a megosztott kulcs hitelesítését.

SAS-jogkivonatot is létrehozhat az Azure PowerShell használatával. A következő példa létrehoz egy SAS-jogkivonatot, amely teljes engedélyekkel rendelkezik egy blobtárolóhoz:

1. Telepítse az Azure PowerShellt, ha még nem tette meg (az Azure PowerShell-parancsmagok legújabb verziójának használata ajánlott). A telepítési útmutatót az [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-Az-ps)című témakörben találja.
2. Nyissa meg az Azure PowerShellt, és futtassa a következő parancsokat, és cserélje le `CONTAINER_NAME` az Ön által választott nevet:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Az új tároló hoz létrejövő megosztott hozzáférésű hozzáférési kód URI-jának a következőhöz hasonlónak kell lennie:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Az ezzel a példával létrehozott megosztott hozzáférési aláírás egy napig érvényes. Az aláírás teljes hozzáférést biztosít (olvasás, írás, törlés, lista) a tárolón belüli blobokhoz.

A megosztott hozzáférésű aláírásokról további információt az [Azure Storage-erőforrásokhoz való korlátozott hozzáférés megadása megosztott hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md)című témakörben talál.

## <a name="addressing-resources-in-the-storage-emulator"></a>Erőforrások kezelése a tárolóemulátorban

A storage-emulátor szolgáltatásvégpontjai eltérnek az Azure storage-fiók végpontjaitól. A helyi számítógép nem végez tartománynév-feloldást, ezért a tárolóemulátor végpontjainak helyi címeknek kell lenniük.

Ha egy Azure-tárfiókban egy erőforrást címez, a következő sémát használja. A fióknév az URI-állomásnév része, a címzett erőforrás pedig az URI elérési út része:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

A következő URI például egy Azure-tárfiókblob érvényes címe:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Mivel a helyi számítógép nem végez tartománynév-feloldást, a fióknév az URI elérési út része az állomásnév helyett. Használja a következő URI-formátumot egy erőforráshoz a tárolóemulátorban:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Például a következő cím használható a tárolóemulátorban lévő blob eléréséhez:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

A tárolóemulátor szolgáltatásvégpontjai a következők:

* Blob szolgáltatás:`http://127.0.0.1:10000/<account-name>/<resource-path>`
* Várólista-szolgáltatás:`http://127.0.0.1:10001/<account-name>/<resource-path>`
* Táblaszolgáltatás:`http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>A fiók másodlagos kezelése az RA-GRS-szel

A 3.1-es verziótól kezdve a tárolóemulátor támogatja az olvasási hozzáférésű georedundáns replikációt (RA-GRS). A másodlagos helyet a fiók nevéhez -másodlagos hozzáfűzésével érheti el. Például a következő cím használható egy blob eléréséhez a tárolóemulátor írásvédett másodlagos használatával:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> A másodlagos tároló-emulátor programozott eléréséhez használja a Storage Client Library for .NET 3.2 vagy újabb verzióját. A részleteket a [Microsoft Azure Storage ügyfélkönyvtárában](https://msdn.microsoft.com/library/azure/dn261237.aspx) találja.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Tárolóemulátor parancssori eszközjának hivatkozása

A 3.0-s verziótól kezdve egy konzolablak jelenik meg a Storage Emulator indításakor. Az emulátor indításához és leállításához használja a konzolablak parancssorát. Az állapotlekérdezést a parancssorból is lekérdezheti, és egyéb műveleteket is elvégezhet.

> [!NOTE]
> Ha telepítve van a Microsoft Azure számítási emulátora, a storage emulátor indításakor megjelenik egy tálcaikon. Kattintson a jobb gombbal az ikonra, hogy megjeleníthetegy olyan menüt, amely grafikus módon indítja el és állítja le a Storage Emulátort.
>
>

### <a name="command-line-syntax"></a>Parancssori szintaxis

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Beállítások

A beállítások listájának megtekintéséhez írja be a `/help` parancsot a parancssorba.

| Beállítás | Leírás | Parancs | Argumentumok |
| --- | --- | --- | --- |
| **Kezdés** |Elindítja a tároló emulátort. |`AzureStorageEmulator.exe start [-inprocess]` |*-Reprocess*: Indítsa el az emulátort az aktuális folyamatban ahelyett, hogy új folyamatot hozna létre. |
| **Leállítás** |Leállítja a tároló emulátort. |`AzureStorageEmulator.exe stop` | |
| **Állapot** |A tárolóemulátor állapotának kinyomtatása. |`AzureStorageEmulator.exe status` | |
| **Törlés** |Törli az adatokat a parancssorban megadott összes szolgáltatásban. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob*: Törli a blobadatokat. <br/>*várólista*: Törli a várólista-adatokat. <br/>*tábla*: Törli a táblaadatokat. <br/>*az összes*: Törli az összes adatot az összes szolgáltatásban. |
| **Init** |Egyszeri inicializálás az emulátor beállításához. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*: Megadja az SQL-példányt tároló kiszolgálót. <br/>*-sqlinstance instancename*: Az alapértelmezett kiszolgálópéldányban használandó SQL-példány nevét adja meg. <br/>*-forcecreate*: Kényszeríti az SQL-adatbázis létrehozását, még akkor is, ha már létezik. <br/>*-skipcreate*: Kihagyja az SQL-adatbázis létrehozását. Ez elsőbbséget élvez a -forcecreate-val szemben.<br/>*-reserveports*: A szolgáltatásokhoz társított HTTP-portok lefoglalásának megkísérlése.<br/>*-unreserveports*: A szolgáltatásokhoz társított HTTP-portok foglalásainak eltávolítására tett kísérletek. Ez elsőbbséget élvez a -reserveports-tal szemben.<br/>*-inprocess*: Az inicializálást az aktuális folyamatban hajtja végre, nem pedig új folyamatot hoz létre. A portfoglalások módosításakor az aktuális folyamatot emelt szintű engedélyekkel kell elindítani. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>A tároló-emulátor és az Azure Storage közötti különbségek

Mivel a storage-emulátor egy helyi emulált környezet, különbségek vannak az emulátor és az Azure storage-fiók felhőben való használata között:

* A tároló-emulátor csak egyetlen rögzített fiókot és egy jól ismert hitelesítési kulcsot támogat.
* A tároló-emulátor nem méretezhető tárolási szolgáltatás, és nem támogatja a nagyszámú egyidejű ügyfelek.
* A [storage-emulátor ban található erőforrások kezelése](#addressing-resources-in-the-storage-emulator)című részben leírtak szerint az erőforrásokat a storage-emulátor és az Azure storage-fiók eltérően kezeli. A különbség az, hogy a tartománynév-feloldás elérhető a felhőben, de a helyi számítógépen nem.
* A 3.1-es verziótól kezdve a tárolóemulátor fiók támogatja az olvasási hozzáférésű georedundáns replikációt (RA-GRS). Az emulátorban minden fiók RA-GRS engedélyezve van, és soha nincs késés az elsődleges és másodlagos replikák között. A Blob szolgáltatásstatisztikáinak beküldése, a Várólista-szolgáltatás statisztikái és a Table Service `LastSyncTime` Stats szolgáltatásstatisztika beszerezműveletek támogatottak a másodlagos fiókban, és az alapul szolgáló SQL-adatbázis nak megfelelően mindig a válaszelem értékét adja vissza az aktuális időként.
* A fájlszolgáltatás és az SMB protokoll szolgáltatás végpontjai jelenleg nem támogatottak a tárolóemulátorban.
* Ha a tárolási szolgáltatások olyan verzióját használja, amelyet az emulátor nem támogat, az emulátor VersionNotSupportedByEmulator hibát (HTTP 400- Hibás kérelem) ad vissza.

### <a name="differences-for-blob-storage"></a>Különbségek a Blob storage-hoz

A következő különbségek vonatkoznak az emulátorblob-tárolóra:

* A tároló emulátor csak támogatja a blob mérete legfeljebb 2 GB.The storage emulator only supports blob sizes up to 2 GB.
* A tároló-emulátorban egy blobnév maximális hossza 256 karakter, míg az Azure Storage-ban a blobnév maximális hossza 1024 karakter.
* Növekményes másolat lehetővé teszi a felülírt blobok pillanatképeinek másolását, ami hibát ad vissza a szolgáltatáson.
* A Laptartományok diff beszereznie nem működik a növekményes másolási blob használatával másolt pillanatképek között.
* A Put Blob művelet sikeres lehet egy blob, amely létezik a storage-emulátor egy aktív bérlet akkor is, ha a címbérlet-azonosító nincs megadva a kérelemben.
* A Blob-műveletek hozzáfűzése nem támogatja az emulátor. Egy hozzáfűző blobon végzett művelet megkísérlése szolgáltatásNotTámogatottByEmulátor hibát (400 HTTP-állapotkód - hibás kérelem) ad vissza.

### <a name="differences-for-table-storage"></a>A táblatárolás különbségei

A következő különbségek vonatkoznak az emulátorban lévő táblázattárolására:

* A storage-emulátor táblaszolgáltatásának dátumtulajdonságai csak az SQL Server 2005 által támogatott tartományt támogatják (1753. január 1-je után kell lenniük). Az 1753. A dátumok pontossága az SQL Server 2005 pontosságára korlátozódik, ami azt jelenti, hogy a dátumok pontosak a másodperc 1/300-ad része.
* A tárolóemulátor támogatja a partíciókulcs és a sorkulcs tulajdonságértékeit, amelyek egyenként kevesebb mint 512 bájt. A fióknév, a táblanév és a kulcstulajdonságnevek együttes teljes mérete nem haladhatja meg a 900 bájtot.
* A tárolóemulátorban lévő táblázat egy sorának teljes mérete legfeljebb 1 MB lehet.
* A tárolóemulátorban az `Edm.Guid` adattípus `Edm.Binary` tulajdonságai `Equal (eq)` vagy `NotEqual (ne)` csak a lekérdezésszűrőkarakterláncok és összehasonlító operátorok támogatják.

### <a name="differences-for-queue-storage"></a>A várólista-tárolás különbségei

Nincsenek különbségek az emulátorban lévő várólista-tárolótekintetében.

## <a name="storage-emulator-release-notes"></a>Tárolóemulátor kiadási megjegyzések

### <a name="version-510"></a>5.10-es verzió

* A storage-emulátor nem utasítja el a Blob, a Queue és a Table szolgáltatás végpontjaion lévő tárolási szolgáltatások 2019-07-07-es verzióját.

### <a name="version-59"></a>5.9-es verzió

* A storage-emulátor nem utasítja el a Blob, a Queue és a Table szolgáltatás végpontjaion lévő tárolási szolgáltatások 2019-02-02-es verzióját.

### <a name="version-58"></a>5.8-as verzió

* A storage-emulátor nem utasítja el a Blob, a Queue és a Table szolgáltatás végpontjaion lévő tárolási szolgáltatások 2018-11-09-es verzióját.

### <a name="version-57"></a>5.7-es verzió

* Javítva egy hiba, amely összeomlást okozott, ha a naplózás engedélyezve van.

### <a name="version-56"></a>5.6-os verzió

* A storage-emulátor most már támogatja a Blob, A várólista és a Table szolgáltatás végpontok 2018-03-28-as verzióját.

### <a name="version-55"></a>5.5-ös verzió

* A storage-emulátor most már támogatja a Blob, A várólista és a Table szolgáltatás végpontok 2017-11-09-es verzióját.
* A blob **created** tulajdonság, amely a blob létrehozási idejét adja hozzá.

### <a name="version-54"></a>5.4-es verzió

* A telepítés stabilitásának javítása érdekében az emulátor telepítéskor már nem kísérli meg a portok lefoglalását. Ha portfoglalásokat szeretne, az **init** parancs *-reserveports* beállításával adja meg őket.

### <a name="version-53"></a>5.3-as verzió

* A storage-emulátor most már támogatja a Blob, a Queue és a Table szolgáltatás végpontjaion lévő tárolási szolgáltatások 2017-07-29-es verzióját.

### <a name="version-52"></a>5.2-es verzió

* A storage-emulátor most már támogatja a Blob, a Queue és a Table szolgáltatás végpontjaion lévő tárolási szolgáltatások 2017-04-17-es verzióját.
* Javítottunk egy hibát, amely miatt a tábla tulajdonságértékei nem voltak megfelelően kódolva.

### <a name="version-51"></a>5.1-es verzió

* Javítva egy hiba, amely miatt a `DataServiceVersion` tárolóemulátor visszaadta a fejlécet néhány olyan válaszban, ahol a szolgáltatás nem volt.

### <a name="version-50"></a>5.0-s verzió

* A tárolóemulátor telepítője már nem keresi a meglévő MSSQL és .NET Framework telepítéseket.
* A tárolóemulátor telepítője már nem hozza létre az adatbázist a telepítés részeként. Az adatbázis továbbra is létrejön, ha szükséges az indítás részeként.
* Az adatbázis létrehozása már nem igényel jogosultságszint-emelést.
* A portfoglalásokra már nincs szükség az indításhoz.
* A következő `init`beállításokat `-reserveports` adja hozzá: (magasság szükséges), `-unreserveports` (magasságot igényel), `-skipcreate`.
* A storage emulátor felhasználói felülete a rendszertálca ikonján most elindítja a parancssori felületet. A régi grafikus felhasználói felület már nem érhető el.
* Néhány DL-t eltávolítottak vagy átneveztek.

### <a name="version-46"></a>4.6-os verzió

* A storage-emulátor mostmár támogatja a Blob, a Queue és a Table szolgáltatás végpontjaion lévő tárolási szolgáltatások 2016-05-31-es verzióját.

### <a name="version-45"></a>4.5-ös verzió

* Javítva egy hiba, amely miatt a telepítés és az inicializálás sikertelen volt, amikor a háttéradatbázist átnevezték.

### <a name="version-44"></a>4.4-es verzió

* A storage-emulátor mostmár támogatja a Blob, a Queue és a Table szolgáltatás végpontjaion lévő tárolási szolgáltatások 2015-12-11-es verzióját.
* A tárolási emulátor szemétgyűjtésblob-adatok most hatékonyabb, ha nagy számú blobok kezelése.
* Javítva egy hiba, amely miatt a tároló ACL XML-t a tárolási szolgáltatás tól kissé eltérően érvényesítették.
* Javítva egy hiba, amely néha a helytelen időzónában jelentett maximális és min DateTime értékeket okozta.

### <a name="version-43"></a>4.3-as verzió

* A storage-emulátor most már támogatja a Blob, A várólista és a Table szolgáltatás végpontok 2015-07-08-as verzióját.

### <a name="version-42"></a>4.2-es verzió

* A storage-emulátor mostmár támogatja a Blob, a Queue és a Table szolgáltatás végpontjainak 2015-04-05-ös verzióját.

### <a name="version-41"></a>4.1-es verzió

* A storage-emulátor mostmár támogatja a Blob, a Queue és a Table szolgáltatás végpontjaion lévő tárolási szolgáltatások 2015-02-21-es verzióját. Nem támogatja az új Hozzáfűző Blob funkciókat.
* Az emulátor most egy értelmes hibaüzenetet ad vissza a tárolási szolgáltatások nem támogatott verzióihoz. Javasoljuk, hogy az emulátor legújabb verzióját használja. Ha VersionNotSupportedByEmulator hibát (400-as HTTP-állapotkód - Hibás kérés) kap, töltse le az emulátor legújabb verzióját.
* Javítottunk egy hibát, amelyben egy versenyállapot miatt a táblaentitás adatai helytelenek voltak az egyidejű egyesítési műveletek során.

### <a name="version-40"></a>4.0-s verzió

* A storage-emulátor végrehajtható fájlja átnevezve *AzureStorageEmulator.exe névre.*

### <a name="version-32"></a>3.2-es verzió

* A storage-emulátor mostmár támogatja a Blob, a Queue és a Table szolgáltatás végpontjaion lévő tárolási szolgáltatások 2014-02-14-es verzióját. A fájlszolgáltatás végpontjai jelenleg nem támogatottak a tárolóemulátorban. A 2014-02-14-es verziószámozás című témakörben [az Azure Storage-szolgáltatások verziószámozása](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) című témakörben talál.

### <a name="version-31"></a>3.1-es verzió

* Az olvasási hozzáférésű georedundáns tárolás (RA-GRS) mostantól támogatott a tárolóemulátorban. A `Get Blob Service Stats` `Get Queue Service Stats`, `Get Table Service Stats` és API-k támogatottak a fiók másodlagos, és mindig vissza adja az értékét lastsynctime válaszelem, mint az aktuális idő szerint az alapul szolgáló SQL-adatbázis. A másodlagos tároló-emulátor programozott eléréséhez használja a Storage Client Library for .NET 3.2 vagy újabb verzióját. A részleteket a Microsoft Azure Storage ügyféltárban találja.

### <a name="version-30"></a>3.0-s verzió

* Az Azure storage-emulátor már nem szállítják ugyanabban a csomagban, mint a számítási emulátor.
* A tárolóemulátor grafikus felhasználói felülete elavult. Ez váltotta fel egy scriptable parancssori felület. A parancssori felületről a Storage Emulator parancssori eszközének útmutatója című témakörben talál. A grafikus felület továbbra is jelen lesz a 3.0-s verzióban, de csak akkor érhető el, ha a számítási emulátor telepítve van, ha a jobb gombbal a tálcán ikonra kattint, és a Storage Emulator felhasználói felületének megjelenítése parancsot választja.
* Az Azure storage-szolgáltatások 2013-08-15-ös verziója már teljes mértékben támogatott. (Korábban ezt a verziót csak a Storage Emulator 2.2.1-es verziójú előzetes verziótámogatta.)

## <a name="next-steps"></a>További lépések

* Értékelje ki a cross-platform, közösség által karbantartott nyílt forráskódú tároló emulátor [Azurite](https://github.com/azure/azurite). 
* [A .NET használatával az Azure Storage-minták](../storage-samples-dotnet.md) több kódmintára mutató hivatkozásokat tartalmaznak, amelyeket az alkalmazás fejlesztése során használhat.
* A Microsoft [Azure Storage Explorer](https://storageexplorer.com) segítségével a felhőbeli storage-fiókjában és a storage-emulátorban lévő erőforrásokkal dolgozhat.
