---
title: Az Azure storage emulator használata a fejlesztési és tesztelési |} A Microsoft Docs
description: Az Azure storage emulator ingyenes helyi fejlesztési környezetet biztosít az Azure Storage-alkalmazások fejlesztésén és tesztelésén. Ismerje meg, hogyan történik a kérelmek hitelesítése, hogyan csatlakozhat az emulátorban az alkalmazásból, és a parancssori eszköz használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/10/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 529612aeecfcea1d775c2f4359c5135ca3c6885e
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052543"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Az Azure storage emulator használata a fejlesztési és tesztelési célra

A Microsoft Azure storage emulator egy helyi környezet emulálja az Azure Blob, Queue és Table szolgáltatások, fejlesztési célra szolgáló biztosít. A storage emulatort használja, akkor az alkalmazást helyileg, a tárolási szolgáltatások és nélkül lehet tesztelni létrehoz egy Azure-szolgáltatásért. Amikor már elégedett az alkalmazás hogyan működik az emulátorban, átválthat az Azure storage-fiók használatára a felhőben.

## <a name="get-the-storage-emulator"></a>A storage emulator beolvasása
A storage emulator érhető el, része a [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). A storage emulator használatával is telepítheti a [önálló telepítő](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (közvetlen letöltésére). A storage emulator telepítéséhez rendszergazdai jogosultságokkal kell rendelkeznie a számítógépen.

A storage emulator jelenleg kizárólag Windows futtatja. Azok számára, a storage emulatort, Linux esetén a mérlegeli, az egyik lehetőség a Közösség tartható fenn, nyílt forráskódú storage emulator [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> A storage emulator egy verziójában létrehozott adatok elérhetők legyenek egy másik verzió használata esetén nem garantált. Ha szeretné megőrizni az adatokat a hosszú távú, javasoljuk, tárolja az adatokat egy Azure storage-fiókban, nem pedig a storage emulator a.
> 
> A storage emulator attól függ, hogy az OData-kódtárak verzióját. Az OData dll-EK, a storage emulator az egyéb verziói által használt cseréje nem támogatott, és nem várt viselkedést okozhat. Azonban bármelyik verziója, a storage szolgáltatás által támogatott OData-kérelmeket küldjön az emulátorban való használhatók.

## <a name="how-the-storage-emulator-works"></a>A storage emulator működése
A storage emulator emulálása érdekében az Azure storage-szolgáltatások a helyi fájlrendszerben és a egy Microsoft SQL Server helyi példányát használja. Alapértelmezés szerint a storage emulator a Microsoft SQL Server 2012 Express LocalDB adatbázist használ. Ha szeretné, konfigurálja a storage emulator eléréséhez helyett a LocalDB-példányt az SQL Server helyi példányát. További információkért lásd: a [kezdő és a storage emulator inicializálása](#start-and-initialize-the-storage-emulator) Ez a cikk későbbi szakaszában talál.

A storage emulator az SQL Server vagy Windows-hitelesítés használatával LocalDB csatlakozik.

Néhány funkcióbeli különbségek a storage emulator és az Azure storage-szolgáltatások között található. Ezek a különbségek kapcsolatos további információkért lásd: a [a storage emulator és az Azure Storage közötti különbségek](#differences-between-the-storage-emulator-and-azure-storage) Ez a cikk későbbi szakaszában talál.

## <a name="start-and-initialize-the-storage-emulator"></a>Indítsa el, és a storage emulator inicializálása

Az Azure storage emulator indítása:
1. Válassza ki a **Start** gombra vagy nyomja le az **Windows** kulcsot.
2. Kezdje el beírni `Azure Storage Emulator`.
3. Válassza ki az emulátorban a megjelenített alkalmazások listáját.

A storage emulator indításakor jelenik meg egy parancssori ablakot. A konzolablakban segítségével indítsa el, és állítsa le a storage emulator, adatok törlése, állapotának lekérése és inicializálni az emulátorban. További információkért lásd: a [Storage emulator parancssori eszköz leírása](#storage-emulator-command-line-tool-reference) Ez a cikk későbbi szakaszában talál.

Amikor fut az emulátor, egy ikont lát a Windows tálca értesítési területén.

Ha bezárja a storage emulator parancssori ablakban, a storage emulator továbbra is futni fog. Ahhoz, hogy a Storage Emulator console ablakban mentést újból, kövesse a fenti lépéseket, mintha a storage emulator indítása.

A storage emulator első futtatásakor a helyi tároló környezet inicializálása az Ön számára. Az inicializálási folyamatot LocalDB-adatbázist hoz létre, és fenntartja az egyes helyi storage szolgáltatás HTTP-port.

A storage emulator telepítve van alapértelmezés szerint a `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`.

> [!TIP]
> Használhatja a [Microsoft Azure Storage Explorer](http://storageexplorer.com) a helyi storage emulator erőforrásokat. Keresse meg "(fejlesztés)" a "Storage-fiókok" a Storage Explorer erőforrások fában telepítve és a storage emulator elindítása után.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>A storage emulator egy másik SQL database használata inicializálása

A storage emulator parancssori eszköz segítségével inicializálása a storage emulator egy SQL database-példány nem az alapértelmezett LocalDB példányt mutat:

1. Nyissa meg a Storage Emulator konzolablakban leírtak szerint a [kezdő és a storage emulator inicializálása](#start-and-initialize-the-storage-emulator) szakaszban.
1. A konzol ablakában írja be a következő parancsot, ahol `<SQLServerInstance>` az SQL Server-példány neve. LocalDB használatához adja meg `(localdb)\MSSQLLocalDb` , az SQL Server-példányt.

  `AzureStorageEmulator.exe init /server <SQLServerInstance>`

  Az alábbi parancsot, amely arra utasítja az emulátor használata az alapértelmezett SQL Server-példány is használható:

  `AzureStorageEmulator.exe init /server .`

  Vagy használhatja az alábbi parancsot, amely újrainicializálja az adatbázist az alapértelmezett LocalDB példányt:

  `AzureStorageEmulator.exe init /forceCreate`

Ezekkel a parancsokkal kapcsolatos további információkért lásd: [Storage emulator parancssori eszköz leírása](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Használhatja a [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) kezeléséhez az SQL Server-példányokat, beleértve a LocalDB telepítését. Az SMSS a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg `(localdb)\MSSQLLocalDb` a a **kiszolgálónév:** mező a LocalDB-példányhoz való csatlakozáshoz.

## <a name="authenticating-requests-against-the-storage-emulator"></a>A storage emulator ellen irányuló kérelmek hitelesítéséhez
Miután telepítette és a storage emulator elindult, tesztelheti a kódját azt. Azure Storage a felhőben, az minden kérelmet, győződjön meg a storage emulatorban, engedélyezni kell, kivéve, ha egy névtelen kérelem. Engedélyezheti a storage emulator használatával a megosztott kulcsos hitelesítés vagy a közös hozzáférésű jogosultságkód (SAS-) kéréseket.

### <a name="authorize-with-shared-key-credentials"></a>Engedélyezze a megosztott kulcsos hitelesítő adatokkal
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

További információ a kapcsolati karakterláncok: [konfigurálása az Azure Storage kapcsolati karakterláncok](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Engedélyezze a közös hozzáférésű jogosultságkód
Bizonyos Azure storage ügyfélkódtáraival, például a Xamarin-klienskódtár csak egy közös hozzáférésű jogosultságkód (SAS-) hitelesítést támogatják. A SAS-jogkivonat hasonló eszköz használatával hozhat létre a [Tártallózó](http://storageexplorer.com/) vagy egy másik alkalmazás, amely támogatja a megosztott kulcsos hitelesítés.

Azure PowerShell használatával is létrehozhat egy SAS-jogkivonatot. Az alábbi példa létrehozza az SAS-jogkivonatát blob-tárolóba teljes körű engedélyekkel:

1. Telepítse az Azure PowerShell, ha még nem tette (a legújabb verzióját használja az Azure PowerShell parancsmagok ajánlott). A telepítési utasításokért lásd: [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/install-azurerm-ps).
2. Nyissa meg az Azure Powershellt, és futtassa a következő parancsokat, és cserélje le `CONTAINER_NAME` egy névvel, Ön:

```powershell
$context = New-AzureStorageContext -Local

New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Az eredményül kapott közös hozzáférésű jogosultságkód URI-t az új tárolóhoz hasonló lesz:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

A közös hozzáférésű jogosultságkódot létrehozni ebben a példában a egy napig érvényes. Az aláírás a tárolóban található blobok teljes hozzáférést (olvasási, írási, törlési, lista) biztosít.

További információ a közös hozzáférésű jogosultságkódok: [a közös hozzáférésű jogosultságkódok (SAS) az Azure Storage-ban](../storage-dotnet-shared-access-signature-part-1.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>A storage emulator a erőforrásainak címzéséhez
A Szolgáltatásvégpontok a storage Emulator nem azonosak az Azure storage-fiók. A különbség, mivel a helyi számítógép nem végez tartománynevek feloldását, a storage emulator végpontokat kell a helyi címek megkövetelése.

Ha meg az Azure storage-fiók erőforrás-cím, a következő séma használja. A fiók nevét az URI-állomás neve része, és az erőforrás javítása folyamatban az URI elérési út része:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Például a következő URI-ja egy Azure storage-fiókban található blob esetében érvényes cím:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Azonban a storage emulatort, mivel a helyi számítógép nem végez tartománynevek feloldását, a fiók nevét, az állomásnév helyett az URI elérési út része. A storage emulator egy erőforrás formátuma a következő URI-t használja:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

A következő cím például előfordulhat, hogy használható a storage emulator a blobok elérése:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

A Szolgáltatásvégpontok a storage Emulator a következők:

* BLOB szolgáltatás: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Queue szolgáltatás: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* TABLE Storage-szolgáltatás: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>A fiók a másodlagos RA-grs-címzés
A storage emulator támogatja verziója 3.1-es verziótól kezdve a replikációs-írásvédett georedundáns tárolást (RA-GRS). A tárolási erőforrások, mind a felhőben, mind a helyi emulátor, elérheti a másodlagos hely szerint hozzáfűzése – másodlagos fióknevet. A következő cím például előfordulhat, hogy használható a storage emulator használatával a csak olvasható másodlagos blobok elérése:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> A storage emulator másodlagost programozás alapú hozzáférést használja a Storage ügyféloldali kódtára a .NET-hez 3.2-es vagy újabb verziója. Tekintse meg a [a Microsoft Azure Storage ügyféloldali kódtára a .NET-hez](https://msdn.microsoft.com/library/azure/dn261237.aspx) részleteiről.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>A Storage emulator parancssori eszköz – referencia
3.0-s verziójával kezdődően a konzolablakban jelenik meg a Storage Emulator indításakor. A konzolablakban a parancssor használatával indítsa el, és állítsa le az emulatort, valamint a lekérdezés állapot és egyéb műveleteket.

> [!NOTE]
> Ha a Microsoft Azure compute emulator telepítve van, a rendszer tálcai ikonja jelenik meg, ha elindítja a Storage Emulator. Kattintson a jobb gombbal az ikonra, elindíthatja és leállíthatja a Storage Emulator grafikus módszert biztosít a menü megjelenítéséhez.
>
>

### <a name="command-line-syntax"></a>Parancssori szintaxis
`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Beállítások
A beállítások listájának megtekintéséhez írja be a `/help` parancsot a parancssorba.

| Beállítás | Leírás | Parancs | Argumentumok |
| --- | --- | --- | --- |
| **Kezdés** |A storage emulator indul. |`AzureStorageEmulator.exe start [-inprocess]` |*-inprocess*: Indítsa el az emulátort helyett egy új folyamat létrehozása a jelenlegi folyamatban. |
| **állj** |A storage emulator leáll. |`AzureStorageEmulator.exe stop` | |
| **Állapot** |A storage emulator állapotát megjeleníti. |`AzureStorageEmulator.exe status` | |
| **Világos** |Törli a parancssorban megadott összes szolgáltatás adatait. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]                                                    ` |*BLOB*: Blobadatok törli. <br/>*várólista*: sorban lévő adatok törlése. <br/>*tábla*: törli a tábla adatait. <br/>*az összes*: az összes szolgáltatás az összes adat törlése. |
| **Init** |Az emulátor beállításához, egyszeri inicializálási hajt végre. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-kiszolgáló Kiszolgálónév\példánynév*: Itt adhatja meg az SQL-példányt futtató kiszolgálót. <br/>*instanceName – sqlinstance*: az alapértelmezett kiszolgálópéldánynál használható SQL-példány nevét adja meg. <br/>*-forcecreate*: az SQL-adatbázis létrehozása kényszeríti, még akkor is, ha már létezik. <br/>*-skipcreate*: kihagyja az SQL-adatbázis létrehozása. Ez elsőbbséget élvez - forcecreate.<br/>*-reserveports*: a HTTP-portok megadott szolgáltatásokkal társított próbál.<br/>*-unreserveports*: távolítsa el a HTTP-port lefoglalását tett kísérletek társított szolgáltatásokat. Ez elsőbbséget élvez - reserveports.<br/>*-inprocess*: hajt végre az inicializálási helyett egy új folyamat terjesztése a jelenlegi folyamatban. Az aktuális folyamat emelt jogosultsági szintű indítható el, ha port foglalások módosítása. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>A storage emulator és az Azure Storage közötti különbségek
Mivel a storage emulator egy helyi SQL-példányban fut egy emulált környezetet, között különbségek vannak a funkciók az emulátorban, és a egy Azure storage-fiókot a felhőben:

* A storage emulator csak egyetlen rögzített fiók és a egy jól ismert hitelesítési kulcs támogatja.
* A storage emulator nem méretezhető tárolási szolgáltatás, és nem támogatja a nagy mennyiségű egyidejű ügyfelet.
* Leírtak szerint [a storage emulator a erőforrásainak címzéséhez](#addressing-resources-in-the-storage-emulator), erőforrások eltérően foglalkozik a storage emulator és a egy Azure storage-fiókot. A különbség, mivel a tartománynevek feloldását érhető el a felhőben, de nem a helyi számítógépen.
* 3.1-es verziójával kezdve a tárfiók emulátor támogatja a replikációs-írásvédett georedundáns tárolást (RA-GRS). Az emulátorban minden fiókok jogosultak az RA-GRS engedélyezve van, és soha nincs semmilyen lag az elsődleges és másodlagos replikák közötti. A Get Blob szolgáltatás statisztikáiról, a Queue szolgáltatás statisztikáiról beolvasása és a Table Service Stats beolvasása műveletek támogatottak a másodlagos fiókot, és mindig értékét adja vissza a `LastSyncTime` válasz eleme, amely az aktuális idő alapján az alapul szolgáló SQL-adatbázisban.
* A File Storage-szolgáltatás és az SMB protokoll Szolgáltatásvégpontok jelenleg nem támogatottak a storage-emulátorban.
* Ha egy verzióját a storage-szolgáltatás, amely még nem támogatja az emulatort használja, a storage emulator egy VersionNotSupportedByEmulator hiba (HTTP-állapotkód: 400 – Hibás kérés) adja vissza.

### <a name="differences-for-blob-storage"></a>Különbségek a Blob Storage
Blob storage-ban az emulátorban alkalmazása a következő eltérésekkel:

* A storage emulator csak támogatja a blob mérete legfeljebb 2 GB.
* A a storage emulator egy blob név legfeljebb 256 karakter közben az Azure Storage-blob név hossza legfeljebb 1024 karakter.
* A növekményes másolási lehetővé teszi, hogy másolandó, felülírt blobok pillanatképeinek, amely a szolgáltatás hibát ad vissza.
* Másolja a növekményes másolási Blob használatával-pillanatképek közötti laptartomány-beolvasási Diff nem működik.
* Egy Put Blob művelet sikerülhet ellen, hogy a rendszer aktív bérletet, a storage emulator megtalálható a blob, akkor is, ha a kérés nem lett megadva a bérlet Azonosítóját.
* Hozzáfűző Blob műveletek nem támogatottak az emulátor által. Kísérlet egy műveletet a hozzáfűző blob egy FeatureNotSupportedByEmulator hiba (HTTP-állapotkód: 400 – Hibás kérés) adja vissza.

### <a name="differences-for-table-storage"></a>Különbségek a Table storage számára
A Table storage az emulátorban alkalmazása a következő eltérésekkel:

* A Table service, a storage emulator az a dátum tulajdonságok támogatja a csak az SQL Server 2005 (azok szükségesek későbbi 1753. január 1.) által támogatott tartományon. 1753. január 1. előtt az összes dátumot a következő értékre módosulnak. A pontosság dátumok korlátozódik, a pontosság az SQL Server 2005, ami azt jelenti, hogy a dátumok a pontos 1/másodperc 300th.
* A storage emulator partíciós kulcs és a sor kulcstulajdonság értékeket kisebb, mint 512 bájtos támogatja. Ezenkívül a fiók nevét, a tábla neve és a kulcstulajdonság nevei együtt a teljes mérete nem haladhatja meg 900 bájt.
* A storage emulator táblában egy sor teljes mérete legfeljebb 1 MB-nál kisebb.
* Az adatok tulajdonságai írja be a storage emulator `Edm.Guid` vagy `Edm.Binary` támogatása csak a `Equal (eq)` és `NotEqual (ne)` összehasonlító operátorok, a lekérdezés szűrése karakterláncok.

### <a name="differences-for-queue-storage"></a>Különbségek a Queue storage szolgáltatáshoz
Nincsenek meghatározott, a Queue storage az emulátorban különbségek.

## <a name="storage-emulator-release-notes"></a>A Storage emulator kibocsátási megjegyzései

### <a name="version-57"></a>5.7-es verzióra
Kijavítva a hiba, amely a naplózás engedélyezve lett egy összeomlási okozhatnak.

### <a name="version-56"></a>5.6-os verziója
* A storage emulator mostantól támogatja a tárolási szolgáltatások 2018-03-28-as verziójának Blob, Queue és Table service végpontokon.

### <a name="version-55"></a>5.5-ös verzió
* A storage emulator mostantól támogatja a tárolási szolgáltatások 2017-11-09 verziója Blob, Queue és Table service végpontokon.
* A BLOB már támogatja **létrehozva** tulajdonság, amely a blob létrehozása időt adja vissza.

### <a name="version-54"></a>5.4 verzióra
Telepítési stabilitását javítása érdekében az emulátorban nem próbál tovább fenntartott portok telepítésekor. Port foglalások tetszés szerint használhatja a *- reserveports* lehetőséget a **init** parancs használatával adhatja meg őket.

### <a name="version-53"></a>5.3-as verzió
A storage emulator mostantól támogatja a tárolási szolgáltatások 2017-07-29 verziója Blob, Queue és Table service végpontokon.

### <a name="version-52"></a>5.2-es verzió
* A storage emulator a tárolási szolgáltatások 2017-04-17-es verziója mostantól támogatja a Blob, Queue és Table service végpontokon.
* Kijavítva a hiba, ahol tábla tulajdonságértékek volt folyamatban kódolása nem megfelelő.

### <a name="version-51"></a>5.1-es verzió
Kijavítva a hiba, ahol a storage emulator visszaadó volt a `DataServiceVersion` fejléc a következő néhány válaszokat, ahol a szolgáltatás nem volt.

### <a name="version-50"></a>5.0-s verzió
* A storage emulator telepítő már nem létező MSSQL keres, és telepíti a .NET-keretrendszer.
* A storage emulator telepítő már nem készít az adatbázis-telepítés része. Adatbázis is létrejön, indítási részeként szükség esetén.
* Adatbázis létrehozása már nem szükséges a jogosultságszint-emelési.
* Port foglalások indítási már nincs szükség.
* Hozzáadja az alábbi lehetőségek `init`: `-reserveports` (igényel a jogosultságszint-emelés), `-unreserveports` (igényel a jogosultságszint-emelés), `-skipcreate`.
* A Storage Emulator UI lehetőséget, a rendszer tálcai ikonja most elindítja a parancssori felület. A régi grafikus felhasználói felület már nem érhető el.
* DLL eltávolították vagy átnevezték.

### <a name="version-46"></a>4.6-os verzió
* A storage emulator a storage services 2016-05-31 verziója mostantól támogatja a Blob, Queue és Table service-végpont.

### <a name="version-45"></a>4.5-ös verziója
* Kijavítva a hiba, inicializálása és telepítése sikertelen lehet, ha a biztonsági adatbázis át lett nevezve: a storage emulator okozó.

### <a name="version-44"></a>4.4-es verziója
* A storage emulator mostantól támogatja a tárolási szolgáltatások 2015-12-11-es verzió Blob, Queue és Table service végpontokon.
* A storage emulator szemétgyűjtés blobadatot már hatékonyabb nagyszámú blobok esetén.
* Kijavítva a hiba, ACL XML kissé különbözően ellenőrizhető, hogy a storage szolgáltatás elvégzi a container okozó.
* Kijavítva a hiba, néha kiváltó minimális és maximális jelentendő dátum/idő értékek a megfelelő időzónában.

### <a name="version-43"></a>4.3-as verzió
* A storage emulator mostantól támogatja a tárolási szolgáltatások 2015-07-08 verziója Blob, Queue és Table service végpontokon.

### <a name="version-42"></a>4.2-es verzió
* A storage emulator mostantól támogatja a tárolási szolgáltatások 2015-04-05-verziójának Blob, Queue és Table service végpontokon.

### <a name="version-41"></a>4.1 verzió
* A storage emulator mostantól támogatja az verziója 2015-02-21 a storage szolgáltatásokat, a Blob, Queue és Table service-végpont, kivéve az új hozzáfűző Blob szolgáltatásokat.
* Egy verzióját a storage-szolgáltatás, amely még nem támogatja az emulatort használja, ha az emulátor egy jelentéssel bíró hibaüzenetet adja vissza. Az emulator legújabb verziója használatát javasoljuk. Ha egy VersionNotSupportedByEmulator hiba (HTTP-állapotkód: 400 – Hibás kérés), töltse le a storage emulator legújabb verziója.
* Kijavítva a hiba, amelynek a versenyhelyzet okozott feltétel tábla Entitásadatok a nem megfelelő az egyidejű egyesítési műveletek során.

### <a name="version-40"></a>4.0-s verziója
* A storage emulator végrehajtható fájl át lett nevezve a *AzureStorageEmulator.exe*.

### <a name="version-32"></a>3.2-es verziója
* A storage emulator mostantól támogatja a tárolási szolgáltatások 2014. 02. 14 verziója Blob, Queue és Table service végpontokon. A Szolgáltatásvégpontok fájl jelenleg nem támogatottak a storage emulator a. Lásd: [támogatása az Azure Storage szolgáltatásainak](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 2014. 02. 14-verzióval kapcsolatban.

### <a name="version-31"></a>3.1-es verzióját
* A storage emulator mostantól támogatott az írásvédett georedundáns tárolás (RA-GRS). A Get Blob szolgáltatás statisztikáiról, első Queue szolgáltatás statisztikáiról és első Table Service Stats API-k támogatottak a másodlagos fiók, és mindig ad vissza a LastSyncTime válasz elem értékét, az aktuális idő alapján az alapul szolgáló SQL-adatbázis. A storage emulator másodlagost programozás alapú hozzáférést használja a Storage ügyféloldali kódtára a .NET-hez 3.2-es vagy újabb verziója. A Microsoft Azure Storage ügyféloldali kódtára a .NET-referencia részleteket talál.

### <a name="version-30"></a>3.0-s verzió
* Az Azure storage emulator már nem tartalmazza a szükséges a compute emulator ugyanazon csomagban található.
* A storage emulator grafikus felhasználói felület elavult értéke egy parancsfájlok futtatására alkalmas parancssori felületet. A parancssori felület részletes ismertetéséért tekintse meg a Storage Emulator parancssori eszköz leírása. A grafikus felületen továbbra is megtalálható a 3.0-s verzió, de csak legyen elérhető rendszer tálca ikonjára a jobb gombbal, és megjelenítése a Storage Emulator felhasználói felületén válassza a Compute Emulator telepítésekor.
* Verzió 2013-08-15 az Azure storage-szolgáltatás mostantól teljes körűen támogatott. (Korábban Storage Emulator 2.2.1-es verzió által támogatott az ebben a verzióban csak előzetes verzió.)

## <a name="next-steps"></a>További lépések

* A platformfüggetlen, Közösség által fenntartott nyílt forráskódú storage emulator kiértékelése [Azurite](https://github.com/arafato/azurite). 
* [.NET-tel az Azure Storage-mintákat](../storage-samples-dotnet.md) számos mintakódot is használhatja, ha az alkalmazás fejlesztése mutató hivatkozásokat tartalmaz.
* Használhatja a [Microsoft Azure Storage Explorer](http://storageexplorer.com) a Storage-fiók a felhőben, és a storage emulator erőforrásokat.
