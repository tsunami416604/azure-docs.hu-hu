---
title: A Storage-fiók hozzáférésének vezérlése az SQL igény szerinti használatához (előzetes verzió)
description: Leírja, hogyan fér hozzá az SQL on-demand (előzetes verzió) az Azure Storage szolgáltatáshoz, és hogyan szabályozhatja az SQL igény szerinti tárolási hozzáférését az Azure szinapszis Analytics szolgáltatásban.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 6ebf23720d1d323b66671c6770ab2121c9091920
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197694"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview"></a>A Storage-fiók hozzáférésének vezérlése az SQL igény szerinti használatához (előzetes verzió)

Az igény szerinti SQL-lekérdezés közvetlenül az Azure Storage-ból olvassa be a fájlokat. Az Azure Storage-beli fájlok eléréséhez szükséges engedélyek két szinten vannak szabályozva:
- **Tárolási szint** – a felhasználónak engedéllyel kell rendelkeznie a mögöttes tárolási fájlok eléréséhez. A tároló rendszergazdájának engedélyeznie kell az Azure AD rendszerbiztonsági tag számára a fájlok olvasását/írását, vagy a tároló eléréséhez használni kívánt SAS-kulcs létrehozását.
- **SQL-szolgáltatási szint** – a felhasználónak `ADMINISTER BULK ADMIN` engedéllyel kell rendelkeznie a végrehajtáshoz `OPENROWSET` , és engedélyt kell adnia a tároló elérésére használt hitelesítő adatok használatára is.

Ez a cikk ismerteti a használható hitelesítő adatok típusait, valamint azt, hogy az SQL és az Azure AD-felhasználók hogyan használják a hitelesítő adatokat.

## <a name="supported-storage-authorization-types"></a>Támogatott tárolási engedélyezési típusok

Egy SQL igény szerinti erőforrásba bejelentkezett felhasználónak jogosultnak kell lennie az Azure Storage-ban tárolt fájlok elérésére és lekérdezésére. Három engedélyezési típus támogatott:

- [Közös hozzáférésű jogosultságkód](#shared-access-signature)
- [Felügyelt identitás](#managed-identity)
- [Felhasználói identitás](#user-identity)

> [!NOTE]
> A munkaterületek létrehozásakor az [Azure ad pass-through](#force-azure-ad-pass-through) az alapértelmezett viselkedés. Ha ezt használja, nem szükséges hitelesítő adatokat létrehoznia minden olyan Storage-fiókhoz, amely az Azure AD-bejelentkezések használatával érhető el. [Ezt a viselkedést letilthatja](#disable-forcing-azure-ad-pass-through).

Az alábbi táblázatban az elérhető engedélyezési típusok találhatók:

| Engedélyezés típusa                    | *SQL-felhasználó*    | *Azure AD-felhasználó*     |
| ------------------------------------- | ------------- | -----------    |
| [SAS](#shared-access-signature)       | Támogatott     | Támogatott      |
| [Felügyelt identitás](#managed-identity) | Nem támogatott | Támogatott      |
| [Felhasználói identitás](#user-identity)       | Nem támogatott | Támogatott      |

### <a name="shared-access-signature"></a>Közös hozzáférésű jogosultságkód

A **közös hozzáférésű aláírás (SAS)** delegált hozzáférést biztosít a Storage-fiók erőforrásaihoz. Az SAS használatával az ügyfelek a fiók kulcsainak megosztása nélkül biztosíthatnak hozzáférést a Storage-fiók erőforrásaihoz. Az SAS részletesen szabályozza a hozzáférést az olyan ügyfelek számára, akik SAS-val rendelkeznek, beleértve az érvényességi időt, a megadott engedélyeket, az elfogadható IP-címtartományt és az elfogadható protokollt (https/http).

SAS-token beszerzéséhez lépjen a **Azure Portal-> Storage-fiókhoz – > közös hozzáférésű aláírás – > konfigurálja az engedélyeket – > sas létrehozása és kapcsolati karakterlánc.**

> [!IMPORTANT]
> SAS-token létrehozásakor a jogkivonat elején szerepel egy kérdőjel ("?"). Ha az SQL-ben igény szerint szeretné használni a tokent, el kell távolítania a kérdőjelet ("?") a hitelesítő adatok létrehozásakor. Például:
>
> SAS-jogkivonat:? SV = 2018-03-28&SS = bfqt&SRT = SCO&SP = rwdlacup&se = 2019-04-18T20:42:12Z&St = 2019-04-18T12:42:12Z&spr = HTTPS&SIG = lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78% 3D

### <a name="user-identity"></a>Felhasználói identitás

A **felhasználói identitás**, más néven "áteresztő", olyan engedélyezési típus, amelyben az SQL on-demand szolgáltatásba bejelentkezett Azure ad-felhasználó identitása az adathozzáférés engedélyezésére szolgál. Az adatok elérése előtt az Azure Storage rendszergazdájának engedélyeket kell adnia az Azure AD-felhasználónak. Ahogy azt a fenti táblázatban is említettük, az SQL-felhasználó típusa nem támogatott.

> [!IMPORTANT]
> Az adatok eléréséhez rendelkeznie kell egy Storage blob-adattulajdonosi/közreműködői/olvasói szerepkörrel, amely az Ön identitását használja.
> Még ha Ön is egy Storage-fiók tulajdonosa, akkor is hozzá kell adnia magát a Storage blob adatszerepköreinek egyikéhez.
>
> Ha többet szeretne megtudni a Azure Data Lake Store Gen2 való hozzáférés-vezérlésről, tekintse át a [Azure Data Lake Storage Gen2 cikkben található hozzáférés-vezérlést](../../storage/blobs/data-lake-storage-access-control.md) .
>

Explicit módon engedélyeznie kell az Azure AD átmenő hitelesítést ahhoz, hogy az Azure AD-felhasználók hozzáférhessenek a tárolóhoz az identitásuk alapján.

#### <a name="force-azure-ad-pass-through"></a>Azure AD-továbbítás kényszerítése

Az Azure AD-ra való váltás egy speciális HITELESÍTő adat neve által elért alapértelmezett viselkedés, `UserIdentity` amely az Azure szinapszis-munkaterület kiépítés során automatikusan jön létre. Minden Azure AD-bejelentkezés minden egyes lekérdezése esetében kényszeríti az Azure AD-továbbítás használatát, amely más hitelesítő adatok megléte ellenére is megtörténik.

> [!NOTE]
> Az Azure AD pass-through alapértelmezett viselkedés. Nem szükséges hitelesítő adatokat létrehoznia minden olyan Storage-fiókhoz, amelyet az AD-bejelentkezések biztosítanak.

Ha [letiltotta az Azure ad átmenő kényszerítését az egyes lekérdezésekhez](#disable-forcing-azure-ad-pass-through), és újra engedélyezni szeretné a műveletet, hajtsa végre a következőt:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Egy adott felhasználóra vonatkozó Azure AD-továbbítás engedélyezéséhez az adott felhasználó hitelesítő adatainak megadására vonatkozó jogosultságot adhat meg `UserIdentity` . Az alábbi példa lehetővé teszi, hogy az Azure AD-ra irányuló továbbítást egy user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

#### <a name="disable-forcing-azure-ad-pass-through"></a>Az Azure AD átmenő kényszerítésének letiltása

[Minden lekérdezés esetében letilthatja az Azure ad átmenő kényszerítését](#force-azure-ad-pass-through). A letiltásához dobja el a `Userdentity` hitelesítő adatokat a következő használatával:

```sql
DROP CREDENTIAL [UserIdentity];
```

Ha újra újra engedélyezni szeretné a műveletet, tekintse meg az [Azure ad átmenő kényszerítése](#force-azure-ad-pass-through) szakaszt.

### <a name="managed-identity"></a>Felügyelt identitás

A **felügyelt identitást** MSI-ként is nevezzük. Azure Active Directory (Azure AD) szolgáltatása, amely Azure-szolgáltatásokat biztosít igény szerinti SQL-szolgáltatásokhoz. Emellett automatikusan felügyelt identitást helyez üzembe az Azure AD-ben. Ez az identitás használható az Azure Storage-beli adatelérési kérelem engedélyezésére.

Az adatok elérése előtt az Azure Storage rendszergazdájának engedélyeket kell adnia a felügyelt identitásnak az adatok eléréséhez. A felügyelt identitásra vonatkozó engedélyek megadásának módja ugyanúgy történik, mint bármely más Azure AD-felhasználó számára.

## <a name="credentials"></a>Hitelesítő adatok

Az Azure Storage-ban található fájlok lekérdezéséhez az SQL igény szerinti végpontjának a hitelesítési adatokat tartalmazó hitelesítő adatokra van szüksége. A hitelesítő adatok két típusát használják:
- A kiszolgálói szintű HITELESÍTő adatok a függvény használatával végrehajtott ad hoc lekérdezésekhez használatosak `OPENROWSET` . A hitelesítő adatok nevének egyeznie kell a tároló URL-címével.
- Az adatbázis-HATÓKÖRrel rendelkező hitelesítő adatok külső táblákhoz használatosak. A külső tábla `DATA SOURCE` arra a hitelesítő adatokra hivatkozik, amelyet a tároló elérésére kell használni.

A hitelesítő adatok a [create hitelesítő adatok létrehozása](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)paranccsal adhatók hozzá. Meg kell adnia a HITELESÍTŐADAT-név argumentumot. A fájlnak meg kell egyeznie az elérési út vagy a tárolóban lévő összes elérési út részével (lásd alább).

> [!NOTE]
> A FOR KRIPTOGRÁFIAi szolgáltató argumentuma nem támogatott.

Az összes támogatott engedélyezési típus esetében a hitelesítő adatok egy fiókra vagy egy tárolóra mutatnak.

A kiszolgáló szintű HITELESÍTő adatok nevének meg kell egyeznie a Storage-fiók (és opcionálisan tároló) teljes elérési útjával a következő formátumban:`<prefix>://<storage_account_path>/<storage_path>`

| Külső adatforrás       | Előtag | Storage-fiók elérési útja                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>. blob.core.windows.net             |
| 1. generációs Azure Data Lake Storage | https  | <storage_account>. azuredatalakestore.net/webhdfs/v1 |
| 2. generációs Azure Data Lake Storage | https  | <storage_account>. dfs.core.windows.net              |


> [!NOTE]
> Speciális kiszolgálói szintű HITELESÍTő ADATOKkal rendelkezik `UserIdentity` , amelyek [kényszerítik az Azure ad átmenő továbbítását](#force-azure-ad-pass-through).

Ha engedélyezni szeretné a felhasználó számára a hitelesítő adatok létrehozását vagy eldobását, a rendszergazda engedélyezheti vagy megtagadhatja a hitelesítő adatok MÓDOSÍTÁSát a felhasználó számára:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Támogatott tárolók és engedélyezési típusok

Az engedélyezési és az Azure Storage-típusok következő kombinációit használhatja:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | Támogatott      | Nem támogatott   | Támogatott     |
| *Felügyelt identitás* | Támogatott      | Támogatott        | Támogatott     |
| *Felhasználói identitás*    | Támogatott      | Támogatott        | Támogatott     |


### <a name="grant-permissions-to-use-credential"></a>Engedélyek megadása a hitelesítő adatok használatához

A hitelesítő adatok használatához a felhasználóknak HIVATKOZÁSokra van szükségük egy adott hitelesítő adathoz. Ha egy specific_user storage_credentialra vonatkozó HIVATKOZÁSokat szeretne megadni, hajtsa végre a következőt:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

A zökkenőmentes Azure AD átmenő élmény biztosítása érdekében a felhasználók alapértelmezés szerint jogosultak a `UserIdentity` hitelesítő adatok használatára. Ezt a következő utasítás automatikus végrehajtása hajtja végre az Azure szinapszis-munkaterület kiépítés után:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

### <a name="examples"></a>Példák

Az [engedélyezési típustól](#supported-storage-authorization-types)függően létrehozhat hitelesítő adatokat az alábbi T-SQL szintaxis használatával.
- A kiszolgáló hatókörű hitelesítő adatait akkor használja a rendszer, ha az SQL-bejelentkezési hívások `OPENROWSET` funkciója nem `DATA_SOURCE` a fájlok egyes Storage-fiókokban való olvasásával történik. A kiszolgáló-hatókörű hitelesítő adatok nevének **meg kell** egyeznie az Azure Storage URL-címével.
- Adatbázis-hatókörű hitelesítő adatokat akkor kell használni, ha bármelyik résztvevő meghívja `OPENROWSET` a `DATA_SOURCE` nyilvános fájlokhoz nem hozzáférő külső táblából származó adatokat, vagy kiválasztja azokat. Az adatbázis-hatókörrel rendelkező hitelesítő adatnak nem kell megegyeznie a Storage-fiók nevével, mert explicit módon fogja használni az adatforrásban, amely meghatározza a tárterület helyét.

**Kiszolgáló – hatókörrel rendelkező hitelesítő adatok közös hozzáférési aláírással Blob Storage**

A következő szkript létrehoz egy kiszolgálói szintű hitelesítő adatot, amely az Azure Storage-ban az SAS-token használatával fér hozzá bármely fájlhoz. Ennek a hitelesítő adatnak a létrehozásával engedélyezheti az olyan SQL-rendszerbiztonsági tag számára, amely végrehajtja `OPENROWSET` a függvényt az SAS-kulccsal védett fájlok olvasásához az Azure Storage-ban a hitelesítő adatok

Az Exchange <*mystorageaccountname*> a tényleges Storage-fiók nevével, és <*mystorageaccountcontainername*> a tároló tényleges nevével:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Kiszolgáló – hatókörön belüli hitelesítő adatok, amelyek lehetővé teszik a nyilvános tárolóhoz való hozzáférést**

A következő szkript létrehoz egy kiszolgálói szintű hitelesítő adatot, amely a nyilvánosan elérhető Azure Storage-ban található bármely fájl elérésére használható. Ennek a hitelesítő adatnak a létrehozásával engedélyezheti, hogy az SQL-rendszerbiztonsági tag végrehajtsa a `OPENROWSET` függvényt az Azure Storage-ban található, a hitelesítő adatok neve nevű nyilvános

Az Exchange <*mystorageaccountname*> kell rendelkeznie a tényleges Storage-fiók nevével, és <*mystorageaccountcontainername*> a tároló tényleges nevével:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = '';
GO
```

**Adatbázis – hatókörrel rendelkező hitelesítő adatok SAS-jogkivonattal**

A következő szkript létrehoz egy hitelesítő adatot, amely a hitelesítő adatokban megadott SAS-jogkivonat használatával fér hozzá a fájlokhoz a tárolóban.

```sql
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Adatbázis – hatókörrel rendelkező hitelesítő adatok az Azure AD-identitással**

A következő szkript létrehoz egy hitelesítő adatot, amelyet a külső táblák és függvények használnak a `OPENROWSET` saját Azure ad-identitásával való hozzáféréshez szükséges hitelesítő adatok használatával.

```sql
CREATE DATABASE SCOPED CREDENTIAL [AzureAD]
WITH IDENTITY = 'User Identity';
GO
```

**Adatbázis – hatókörrel rendelkező hitelesítő adat felügyelt identitással**

A következő szkript létrehoz egy hitelesítő adatokat, amelyekkel megszemélyesítheti a jelenlegi Azure AD-felhasználót a szolgáltatás felügyelt identitásával. 

```sql
CREATE DATABASE SCOPED CREDENTIAL [SynapseIdentity]
WITH IDENTITY = 'Managed Identity';
GO
```

Az adatbázis-hatókörrel rendelkező hitelesítő adatnak nem kell megegyeznie a Storage-fiók nevével, mert explicit módon fogja használni az adatforrásban, amely meghatározza a tárterület helyét.

## <a name="next-steps"></a>Következő lépések

Az alább felsorolt cikkek segítenek megismerni a különböző típusú mappák, fájltípusok és a nézetek létrehozásának és használatának a lekérdezését:

- [Egyetlen CSV-fájl lekérdezése](query-single-csv-file.md)
- [Mappák és több CSV-fájl lekérdezése](query-folders-multiple-csv-files.md)
- [Adott fájlok lekérdezése](query-specific-files.md)
- [Parquet-fájlok lekérdezése](query-parquet-files.md)
- [Nézetek létrehozása és használata](create-use-views.md)
- [JSON-fájlok lekérdezése](query-json-files.md)
- [A Parquet beágyazott típusainak lekérdezése](query-parquet-nested-types.md)
