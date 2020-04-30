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
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424033"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>A Storage-fiókhoz való hozzáférés szabályozása az SQL on-demand (előzetes verzió) számára az Azure szinapszis Analytics szolgáltatásban

Az SQL on-demand (előzetes verzió) lekérdezés közvetlenül az Azure Storage-ból olvassa be a fájlokat. Mivel a Storage-fiók olyan objektum, amely az SQL igény szerinti erőforráson kívül esik, a megfelelő hitelesítő adatok szükségesek. A felhasználónak a szükséges hitelesítő adatok használatára van szüksége a megfelelő engedélyekkel. Ez a cikk ismerteti a használható hitelesítő adatok típusait, valamint azt, hogy az SQL és az Azure AD-felhasználók hogyan használják a hitelesítő adatokat.

## <a name="supported-storage-authorization-types"></a>Támogatott tárolási engedélyezési típusok

Egy SQL igény szerinti erőforrásba bejelentkezett felhasználónak jogosultnak kell lennie az Azure Storage-ban tárolt fájlok elérésére és lekérdezésére. Három engedélyezési típus támogatott:

- [Közös hozzáférésű jogosultságkód](#shared-access-signature)
- [Felügyelt identitás](#managed-identity)
- [Felhasználói identitás](#user-identity)

> [!NOTE]
> A munkaterületek létrehozásakor az [Azure ad pass-through](#force-azure-ad-pass-through) az alapértelmezett viselkedés. Ha ezt használja, nem szükséges hitelesítő adatokat létrehoznia minden olyan Storage-fiókhoz, amely az AD-bejelentkezések használatával érhető el. [Ezt a viselkedést letilthatja](#disable-forcing-azure-ad-pass-through).

Az alábbi táblázatban megtalálja azokat a különböző engedélyezési típusokat, amelyek támogatottak vagy hamarosan támogatottak lesznek.

| Engedélyezés típusa                    | *SQL-felhasználó*    | *Azure AD-felhasználó*     |
| ------------------------------------- | ------------- | -----------    |
| [SAS](#shared-access-signature)       | Támogatott     | Támogatott      |
| [Felügyelt identitás](#managed-identity) | Nem támogatott | Nem támogatott  |
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

> [!NOTE]
> Ha az [Azure ad-](#force-azure-ad-pass-through) t használja, nem szükséges hitelesítő adatokat létrehoznia minden olyan Storage-fiókhoz, amely az ad-bejelentkezések használatával érhető el.

> [!IMPORTANT]
> Az adatok eléréséhez rendelkeznie kell egy Storage blob-adattulajdonosi/közreműködői/olvasói szerepkörrel, amely az Ön identitását használja.
> Még ha Ön is egy Storage-fiók tulajdonosa, akkor is hozzá kell adnia magát a Storage blob adatszerepköreinek egyikéhez.
>
> Ha többet szeretne megtudni a Azure Data Lake Store Gen2 való hozzáférés-vezérlésről, tekintse át a [Azure Data Lake Storage Gen2 cikkben található hozzáférés-vezérlést](../../storage/blobs/data-lake-storage-access-control.md) .
>

### <a name="managed-identity"></a>Felügyelt identitás

A **felügyelt identitást** MSI-ként is nevezzük. Azure Active Directory (Azure AD) szolgáltatása, amely Azure-szolgáltatásokat biztosít igény szerinti SQL-szolgáltatásokhoz. Emellett automatikusan felügyelt identitást helyez üzembe az Azure AD-ben. Ez az identitás használható az Azure Storage-beli adatelérési kérelem engedélyezésére.

Az adatok elérése előtt az Azure Storage rendszergazdájának engedélyeket kell adnia a felügyelt identitásnak az adatok eléréséhez. A felügyelt identitásra vonatkozó engedélyek megadásának módja ugyanúgy történik, mint bármely más Azure AD-felhasználó számára.

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

Az Azure Storage-ban található fájlok lekérdezéséhez az SQL igény szerinti végpontjának a hitelesítési adatokat tartalmazó kiszolgálói szintű HITELESÍTő ADATOKra van szüksége. A hitelesítő adatok a [create hitelesítő adatok létrehozása](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)paranccsal adhatók hozzá. Meg kell adnia a HITELESÍTŐADAT-név argumentumot. A fájlnak meg kell egyeznie az elérési út vagy a tárolóban lévő összes elérési út részével (lásd alább).

> [!NOTE]
> A FOR KRIPTOGRÁFIAi szolgáltató argumentuma nem támogatott.

Az összes támogatott engedélyezési típus esetében a hitelesítő adatok egy fiókra, egy tárolóra, bármely könyvtárra (nem root) vagy egyetlen fájlra mutatnak.

A HITELESÍTő adatok nevének meg kell egyeznie a tároló, mappa vagy fájl teljes elérési útjával a következő formátumban:`<prefix>://<storage_account_path>/<storage_path>`

| Külső adatforrás       | Előtag | Storage-fiók elérési útja                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>. blob.core.windows.net             |
| 1. generációs Azure Data Lake Storage | https  | <storage_account>. azuredatalakestore.net/webhdfs/v1 |
| 2. generációs Azure Data Lake Storage | https  | <storage_account>. dfs.core.windows.net              |

 A (z) "<storage_path>" a tárhelyen belüli elérési út, amely az olvasni kívánt mappára vagy fájlra mutat.

> [!NOTE]
> Az `UserIdentity` [Azure ad-t kényszerítő](#force-azure-ad-pass-through)hitelesítő adatok neve van. Kérjük, olvassa el, milyen hatással van a [hitelesítő adatok keresésére](#credential-lookup) a lekérdezések végrehajtása során.

Ha engedélyezni szeretné a felhasználó számára a hitelesítő adatok létrehozását vagy eldobását, a rendszergazda engedélyezheti vagy megtagadhatja a hitelesítő adatok MÓDOSÍTÁSát a felhasználó számára:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Támogatott tárolók és engedélyezési típusok

Az engedélyezési és az Azure Storage-típusok következő kombinációit használhatja:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | Támogatott      | Nem támogatott   | Támogatott     |
| *Felügyelt identitás* | Nem támogatott  | Nem támogatott    | Nem támogatott |
| *Felhasználói identitás*    | Támogatott      | Támogatott        | Támogatott     |

### <a name="examples"></a>Példák

Az [engedélyezési típustól](#supported-storage-authorization-types)függően létrehozhat hitelesítő adatokat az alábbi T-SQL szintaxis használatával.

**Közös hozzáférésű aláírás és Blob Storage**

Az Exchange <*mystorageaccountname*> a tényleges Storage-fiók nevével, és <*mystorageaccountcontainername*> a tároló tényleges nevével:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Felhasználói identitás és Azure Data Lake Storage Gen1**

Az Exchange <*mystorageaccountname*> a tényleges Storage-fiók nevével, és <*mystorageaccountcontainername*> a tároló tényleges nevével:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**Felhasználói identitás és Azure Data Lake Storage Gen2**

Az Exchange <*mystorageaccountname*> a tényleges Storage-fiók nevével, és <*mystorageaccountcontainername*> a tároló tényleges nevével:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Azure AD-továbbítás kényszerítése

Az Azure AD-ra való váltás egy speciális HITELESÍTő adat neve által elért alapértelmezett viselkedés, `UserIdentity`amely az Azure szinapszis-munkaterület kiépítés során automatikusan jön létre. Minden Azure AD-bejelentkezés minden egyes lekérdezése esetében kényszeríti az Azure AD-továbbítás használatát, amely más hitelesítő adatok megléte ellenére is megtörténik.

> [!NOTE]
> Az Azure AD pass-through alapértelmezett viselkedés. Nem szükséges hitelesítő adatokat létrehoznia minden olyan Storage-fiókhoz, amelyet az AD-bejelentkezések biztosítanak.

Ha [letiltotta az Azure ad átmenő kényszerítését az egyes lekérdezésekhez](#disable-forcing-azure-ad-pass-through), és újra engedélyezni szeretné a műveletet, hajtsa végre a következőt:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Egy adott felhasználóra vonatkozó Azure AD-továbbítás engedélyezéséhez az adott felhasználó hitelesítő adatainak `UserIdentity` megadására vonatkozó jogosultságot adhat meg. Az alábbi példa lehetővé teszi, hogy az Azure AD-ra irányuló továbbítást egy user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

További információ arról, hogyan találja meg a hitelesítő adatokat az SQL igény szerinti azonosításához: [hitelesítő adatok keresése](#credential-lookup).

## <a name="disable-forcing-azure-ad-pass-through"></a>Az Azure AD átmenő kényszerítésének letiltása

[Minden lekérdezés esetében letilthatja az Azure ad átmenő kényszerítését](#force-azure-ad-pass-through). A letiltásához dobja el `Userdentity` a hitelesítő adatokat a következő használatával:

```sql
DROP CREDENTIAL [UserIdentity];
```

Ha újra újra engedélyezni szeretné a műveletet, tekintse meg az [Azure ad átmenő kényszerítése](#force-azure-ad-pass-through) szakaszt.

Ha le szeretné tiltani az Azure AD átmenő átvitelét egy adott felhasználó számára, megtagadhatja az adott felhasználó `UserIdentity` hitelesítő adatait. Az alábbi példa letiltja az Azure AD átmenő user_nameának kényszerítését:

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

További információ arról, hogyan találja meg a hitelesítő adatokat az SQL on-demand a hitelesítő adatok [keresése](#credential-lookup)című témakörben.

## <a name="grant-permissions-to-use-credential"></a>Engedélyek megadása a hitelesítő adatok használatához

A hitelesítő adatok használatához a felhasználóknak HIVATKOZÁSokra van szükségük egy adott hitelesítő adathoz. Ha egy specific_user storage_credentialra vonatkozó HIVATKOZÁSokat szeretne megadni, hajtsa végre a következőt:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

A zökkenőmentes Azure AD átmenő élmény biztosítása érdekében a felhasználók alapértelmezés szerint jogosultak a `UserIdentity` hitelesítő adatok használatára. Ezt a következő utasítás automatikus végrehajtása hajtja végre az Azure szinapszis-munkaterület kiépítés után:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>Hitelesítő adatok keresése

A lekérdezések engedélyezésekor a hitelesítő adatok keresése a Storage-fiókok elérésére szolgál, és a következő szabályokon alapul:

- A felhasználó Azure AD-bejelentkezésként van bejelentkezve

  - Ha létezik egy UserIdentity hitelesítő adat, és a felhasználónak van rájuk vonatkozó hivatkozása, az Azure AD-t használja a rendszer, ellenkező esetben a [hitelesítő adatok elérési útja alapján](#lookup-credential-by-path)

- A felhasználó SQL-bejelentkezésként van bejelentkezve

  - [Keresési hitelesítő adatok használata elérési út alapján](#lookup-credential-by-path)

### <a name="lookup-credential-by-path"></a>Keresési hitelesítő adat az elérési út alapján

Ha az Azure AD-ra való továbbítás kényszerítése le van tiltva, a hitelesítő adatok keresése a tárolási útvonalon (első mélység) és az adott hitelesítő adatokra vonatkozó hivatkozások megléte alapján történik. Ha több hitelesítő adat is használható ugyanazon fájl eléréséhez, az SQL on-demand a legpontosabbat fogja használni.  

Alább látható egy példa a következő elérési úttal rendelkező lekérdezésre: *Account.DFS.Core.Windows.net/filesystem/folder1/.../folderN/fileX.ext*

A hitelesítő adatok keresése ebben a sorrendben történik:

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

Ha a felhasználó nem rendelkezik HIVATKOZÁSokkal az 5. számú hitelesítő adatokra vonatkozóan, az SQL on-demand azt fogja ellenőriznie, hogy a felhasználó rendelkezik-e hivatkozásokkal a hitelesítő adatokhoz, amely egy magasabb szint, amíg meg nem találja azokat a hitelesítő adatokat, amelyekre a felhasználó HIVATKOZik Ha nem található ilyen engedély, a rendszer hibaüzenetet küld.

### <a name="credential-and-path-level"></a>Hitelesítő adatok és elérési út szintje

A kívánt elérésiút-alakzattól függően az alábbi követelmények állnak rendelkezésre a lekérdezések futtatásához:

- Ha a lekérdezés több fájlt céloz meg (a mappákat vagy anélkül, hogy azok nem rendelkeznek), a felhasználónak legalább a gyökérkönyvtár szintjén (a tároló szintjén) kell hozzáférnie a hitelesítő adatokhoz. Erre a hozzáférési szintre azért van szükség, mert a fájlok listázása a gyökérkönyvtárhoz viszonyítva történik (az Azure Storage korlátozásai)
- Ha a lekérdezés egyetlen fájl megcélzását célozza meg, a felhasználónak bármely szinten meg kell férnie a hitelesítő adatokhoz, ha az SQL igény szerinti hozzáférése a fájlhoz közvetlenül, azaz a mappák listázása nélkül történik.

|                  | *Fiók* | *Gyökérkönyvtár* | *Bármely más címtár* | *Fájl*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Egyetlen fájl*    | Támogatott | Támogatott        | Támogatott             | Támogatott     |
| *Több fájl* | Támogatott | Támogatott        | Nem támogatott         | Nem támogatott |

## <a name="next-steps"></a>További lépések

Az alább felsorolt cikkek segítenek megismerni a különböző típusú mappák, fájltípusok és a nézetek létrehozásának és használatának a lekérdezését:

- [Egyetlen CSV-fájl lekérdezése](query-single-csv-file.md)
- [Mappák és több CSV-fájl lekérdezése](query-folders-multiple-csv-files.md)
- [Adott fájlok lekérdezése](query-specific-files.md)
- [Parquet-fájlok lekérdezése](query-parquet-files.md)
- [Nézetek létrehozása és használata](create-use-views.md)
- [JSON-fájlok lekérdezése](query-json-files.md)
- [A Parquet beágyazott típusainak lekérdezése](query-parquet-nested-types.md)
