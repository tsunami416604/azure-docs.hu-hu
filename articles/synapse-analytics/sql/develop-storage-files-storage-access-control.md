---
title: A tárfiók-hozzáférés szabályozása az SQL igény szerinti verziójához (előzetes verzió)
description: Ez a témakör azt ismerteti, hogy az SQL igény szerinti (előzetes verzióban) hogyan fér hozzá az Azure Storage-hoz, és hogyan szabályozhatja az SQL igény szerinti tároláshoz való hozzáférését az Azure Synapse Analytics szolgáltatásban.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424033"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Az Azure Synapse Analytics-ben az SQL igény szerinti (előzetes verzió) tárfiókhoz való hozzáférésének szabályozása

Az SQL igény szerinti (előzetes verziójú) lekérdezés közvetlenül az Azure Storage-ból olvassa be a fájlokat. Mivel a tárfiók egy olyan objektum, amely az SQL igény szerinti erőforráson kívül található, megfelelő hitelesítő adatokra van szükség. A felhasználónak szüksége van a szükséges hitelesítő adatok használatához megadott engedélyekre. Ez a cikk ismerteti a hitelesítő adatok használatát, és hogyan hitelesítő adatok lekérdezése az SQL és az Azure AD-felhasználók számára.

## <a name="supported-storage-authorization-types"></a>Támogatott tárolási engedélyezési típusok

Egy felhasználó, amely bejelentkezett egy SQL igény szerinti erőforrást engedélyezni kell a fájlok eléréséhez és lekérdezéséhez az Azure Storage-ban. Három engedélyezési típus támogatott:

- [Közös hozzáférésű jogosultságkód](#shared-access-signature)
- [Felügyelt identitás](#managed-identity)
- [Felhasználói identitás](#user-identity)

> [!NOTE]
> [Az Azure AD áthaladási](#force-azure-ad-pass-through) az alapértelmezett viselkedés, amikor létrehoz egy munkaterületet. Ha használja, nem kell hitelesítő adatokat létrehoznia minden ad bejelentkezések használatával elérhető tárfiókhoz. Ezt [a viselkedést letilthatja.](#disable-forcing-azure-ad-pass-through)

Az alábbi táblázatban megtalálja a különböző engedélyezési típusokat, amelyek vagy támogatottak, vagy hamarosan támogatottak lesznek.

| Engedélyezés típusa                    | *SQL-felhasználó*    | *Azure AD-felhasználó*     |
| ------------------------------------- | ------------- | -----------    |
| [Sas](#shared-access-signature)       | Támogatott     | Támogatott      |
| [Felügyelt identitás](#managed-identity) | Nem támogatott | Nem támogatott  |
| [Felhasználói identitás](#user-identity)       | Nem támogatott | Támogatott      |

### <a name="shared-access-signature"></a>Közös hozzáférésű jogosultságkód

**A megosztott hozzáférésű aláírás (SAS)** delegált hozzáférést biztosít a tárfiók ban lévő erőforrásokhoz. A SAS segítségével az ügyfél a fiókkulcsok megosztása nélkül biztosíthat hozzáférést az ügyfeleknek a tárfiók erőforrásaihoz. A SAS részletes enciklit ad a SAS-sel rendelkező ügyfeleknek biztosított hozzáférés típusa felett, beleértve az érvényességi intervallumot, a megadott engedélyeket, az elfogadható IP-címtartományt és az elfogadható protokollt (https/http).

SAS-jogkivonatot az Azure **Portalon –> storage-> megosztott hozzáférés-aláírás -> engedélyek konfigurálása -> SAS-generálása és kapcsolati karakterlánc** ra navigálva kaphat.

> [!IMPORTANT]
> SAS-jogkivonat létrehozásakor a jogkivonat elején egy kérdőjel ('?') található. A token igény szerinti SQL-ben való használatához el kell távolítania a kérdőjelet ('?') a hitelesítő adatok létrehozásakor. Például:
>
> SAS token: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st==2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSSMANd0ef9BrIPBNJ3VYEIq78%3D

### <a name="user-identity"></a>Felhasználói identitás

**Felhasználói identitás**, más néven "áthaladás", egy engedélyezési típus, ahol az Sql-be igény szerinti bejelentkezett Azure AD-felhasználó identitását használják az adatok elérésének engedélyezéséhez. Az adatok elérése előtt az Azure Storage-rendszergazdának engedélyeket kell adnia az Azure AD-felhasználónak. Amint azt a fenti táblázatban, az SQL felhasználói típus nem támogatott.

> [!NOTE]
> Ha [az Azure AD pass-through](#force-azure-ad-pass-through) nem kell létrehozni a hitelesítő adatokat minden tárfiók elérhető AD bejelentkezések használatával.

> [!IMPORTANT]
> Az adatok eléréséhez storage Blob-adattulajdonosi/-olvasói szerepkörrel kell rendelkeznie.
> Még akkor is, ha egy tárfiók tulajdonosa, akkor is hozzá kell adnia magát az egyik storage blob adatszerepkörök.
>
> Ha többet szeretne megtudni az Azure Data Lake Store Gen2 hozzáférés-vezérléséről, tekintse át a [hozzáférés-vezérlést az Azure Data Lake Storage Gen2 cikkében.](../../storage/blobs/data-lake-storage-access-control.md)
>

### <a name="managed-identity"></a>Felügyelt identitás

**A felügyelt identitást** MSI-nek is nevezik. Ez az Azure Active Directory (Azure AD) egyik szolgáltatása, amely igény szerint biztosít Azure-szolgáltatásokat az SQL számára. Emellett egy automatikusan felügyelt identitást telepít az Azure AD-ben. Ez az identitás az Azure Storage-beli adathozzáférési kérelem engedélyezéséhez használható.

Az adatok elérése előtt az Azure Storage rendszergazdájának engedélyeket kell adnia a felügyelt identitásnak az adatok eléréséhez. A felügyelt identitás engedélyek megadása ugyanúgy történik, mint bármely más Azure AD-felhasználó engedélyének megadása.

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

Az Azure Storage-ban található fájl lekérdezéséhez az SQL igény szerinti végpontja kiszolgálószintű hitelesítő adatokat tartalmazó kiszolgálószintű hitelesítő adatokat igényel. A hitelesítő adatok hozzáadása a CREATE CREDENTIAL ( [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)) futtatásával történik. Meg kell adnia egy HITELESÍTŐ NEVET argumentumot. Meg kell egyeznie az elérési út vagy a teljes elérési út az adatok tárolása (lásd alább).

> [!NOTE]
> A KRIPTOGRÁFIAi SZOLGÁLTATÓ argumentum nem támogatott.

Az összes támogatott engedélyezési típus esetében a hitelesítő adatok egy fiókra, egy tárolóra, bármely könyvtárra (nem legfelső szintű) vagy egyetlen fájlra mutathatnak.

A HITELESÍTŐ ADATOK NEVÉNEK a tároló, mappa vagy fájl teljes elérési útjának a következő formátumban kell egyeznie:`<prefix>://<storage_account_path>/<storage_path>`

| Külső adatforrás       | Előtag | Tárfiók elérési útja                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | storage_account>.blob.core.windows.net <             |
| 1. generációs Azure Data Lake Storage | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| 2. generációs Azure Data Lake Storage | https  | <storage_account storage_account>.dfs.core.windows.net              |

 A "<storage_path>" egy elérési út a tárolón belül, amely az olvasni kívánt mappára vagy fájlra mutat.

> [!NOTE]
> Van egy speciális `UserIdentity` hitelesítő adatok neve, amely [kényszeríti az Azure AD pass-through.](#force-azure-ad-pass-through) Kérjük, olvassa el, hogy milyen hatással van a [hitelesítő adatok megkeresésére](#credential-lookup) a lekérdezések végrehajtása során.

Ha szükséges, hogy a felhasználó létrehozhassa vagy eldobhassa a hitelesítő adatokat, a rendszergazda bármely hitelesítő adatjogosultságot megadhat/megtagadhat egy felhasználószámára:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Támogatott tárolók és engedélyezési típusok

Az engedélyezési és az Azure Storage-típusok következő kombinációit használhatja:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *Sas*               | Támogatott      | Nem támogatott   | Támogatott     |
| *Felügyelt identitás* | Nem támogatott  | Nem támogatott    | Nem támogatott |
| *Felhasználói identitás*    | Támogatott      | Támogatott        | Támogatott     |

### <a name="examples"></a>Példák

Az engedélyezési [típustól](#supported-storage-authorization-types)függően az alábbi T-SQL szintaxissal hozhat létre hitelesítő adatokat.

**Megosztott hozzáférési aláírás és blobtároló**

Az Exchange <*mystorageaccountname*> a tényleges tárfiók nevével, és <*a mystorageaccountcontainername*> a tényleges tárolónévvel:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Felhasználói identitás és Az Azure Data Lake storage gen1**

Az Exchange <*mystorageaccountname*> a tényleges tárfiók nevével, és <*a mystorageaccountcontainername*> a tényleges tárolónévvel:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**Felhasználói identitás és Az Azure Data Lake Storage Gen2**

Az Exchange <*mystorageaccountname*> a tényleges tárfiók nevével, és <*a mystorageaccountcontainername*> a tényleges tárolónévvel:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Az Azure AD áteresse

Az Azure AD-áthaladás kényszerítése egy speciális CREDENTIAL NAME `UserIdentity`által elért alapértelmezett viselkedés, amely automatikusan jön létre az Azure Synapse-munkaterület kiépítése során. Kényszeríti az Azure AD-áteresztő minden azure-beli bejelentkezés minden lekérdezésének használatát, amely más hitelesítő adatok megléte ellenére fog bekövetkezni.

> [!NOTE]
> Az Azure AD áthaladási egy alapértelmezett viselkedés. Nem kell hitelesítő adatokat létrehoznia az AD bejelentkezések által elért minden egyes tárfiókhoz.

Abban az esetben, ha [letiltotta az Azure AD-áthaladáskényszerítést minden lekérdezéshez,](#disable-forcing-azure-ad-pass-through)és újra engedélyezni szeretné, hajtsa végre:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Egy adott felhasználó számára egy Azure AD-átadó kényszerítésének engedélyezéséhez adhat meg HIVATKOZÁSi engedélyt az adott felhasználónak a hitelesítő adatokra. `UserIdentity` A következő példa lehetővé teszi egy Azure AD-áthaladás kényszerítése egy user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Ha többet szeretne tudni arról, hogy az SQL igény szerinti hitelesítő adatokat hogyan találja meg, olvassa el a hitelesítő adatok keresése című [témakört.](#credential-lookup)

## <a name="disable-forcing-azure-ad-pass-through"></a>Az Azure AD-áthaladás kényszerítése

Az [Azure AD-áthaladás kényszerítése letiltható minden egyes lekérdezéshez.](#force-azure-ad-pass-through) A letiltáshoz `Userdentity` dobja el a hitelesítő adatokat a következő használatával:

```sql
DROP CREDENTIAL [UserIdentity];
```

Ha azt szeretné, hogy újra engedélyezze újra, tekintse meg az [Azure AD áthaladási](#force-azure-ad-pass-through) szakasz.

Ha le szeretné tiltani az Azure AD áthaladási kényszerítésének `UserIdentity` kényszerítését egy adott felhasználó számára, megtagadhatja a REFERENCIA-engedélyt egy adott felhasználó hitelesítő adataihoz. A következő példa letiltja az Azure AD áthaladási kényszeríti a user_name:

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Ha többet szeretne tudni arról, hogy az SQL igény szerinti hitelesítő adatokat hogyan találja meg, olvassa el a hitelesítő adatok keresése című [témakört.](#credential-lookup)

## <a name="grant-permissions-to-use-credential"></a>Hitelesítő adatok használatára vonatkozó engedélyek megadása

A hitelesítő adatok használatához a felhasználónak referenciákkal kell rendelkeznie egy adott hitelesítő adathoz. Ha egy specific_user storage_credential hivatkozási engedélyt szeretne adni, hajtsa végre a következőket:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

A zökkenőmentes Azure AD áthaladási élmény biztosítása érdekében alapértelmezés szerint minden `UserIdentity` felhasználónak joga van a hitelesítő adatok használatára. Ez a következő utasítás automatikus végrehajtásával érhető el az Azure Synapse-munkaterület kiépítésekor:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>Hitelesítő adatok megkeresése

A lekérdezések engedélyezésekor a hitelesítő adatok lekérdezése a tárfiók eléréséhez használható, és a következő szabályokon alapul:

- A felhasználó Azure AD-bejelentkezésként van bejelentkezve

  - Ha létezik UserIdentity hitelesítő adat, és a felhasználó rendelkezik hivatkozási engedélyekkel, az Azure AD áthaladási lesz használva, ellenkező esetben [a következő hitelesítő adatok at](#lookup-credential-by-path)

- A felhasználó SQL bejelentkezésként van bejelentkezve

  - Keress [hitelesítő adatokat elérési út szerint](#lookup-credential-by-path)

### <a name="lookup-credential-by-path"></a>Hitelesítő adatok felkésése elérési út szerint

Ha az Azure AD áthaladási kényszeríti le van tiltva, a hitelesítő adatok megkeresése a tárolási útvonal (mélység) és az adott hitelesítő adatokra vonatkozó HIVATKOZÁSOK-engedély megléte alapján történik. Ha több hitelesítő adat is használható ugyanazon fájl eléréséhez, az SQL igény szerinti használata a legkonkrétabb.  

Az alábbi példa a következő fájlelérési úton lévő lekérdezésre mutat *be: account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX.ext*

A hitelesítő adatok megkeresése a következő sorrendben lesz elvégezhető:

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

Ha a felhasználó nem rendelkezik HIVATKOZÁSI engedéllyel az 5-ös hitelesítő adatokhoz, az SQL igény szerinti hitelesítési adatok ellenőrzik, hogy a felhasználó rendelkezik-e HIVATKOZÁS-engedéllyel egy szinttel magasabb hitelesítő adatokkal, amíg meg nem találja azokat a hitelesítő adatokat, amelyekhez a felhasználó hivatkozási engedéllyel rendelkezik. Ha nem talál ilyen engedélyt, hibaüzenet jelenik meg.

### <a name="credential-and-path-level"></a>Hitelesítő adatok és elérési út szintje

A kívánt görbealakzattól függően a következő követelmények vannak érvényben a lekérdezések futtatásához:

- Ha a lekérdezés több fájlt (mappákat, helyettesítő kártyákkal vagy anélkül) céloz meg, a felhasználónak legalább a gyökérkönyvtár szintjén (tárolószinten) hozzá kell férnie egy hitelesítő adathoz. Erre a hozzáférési szintre azért van szükség, mert a listafájlok a gyökérkönyvtárhoz viszonyítottak (Azure Storage-korlátozások)
- Ha a lekérdezés egyetlen fájlt céloz meg, a felhasználónak bármilyen szinten hozzá kell férnie egy hitelesítő adathoz, mivel az SQL igény szerinti hozzáférése közvetlenül, azaz mappák listázása nélkül történik.

|                  | *Fiók* | *Gyökérkönyvtár* | *Bármely más könyvtár* | *Fájl*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Egyetlen fájl*    | Támogatott | Támogatott        | Támogatott             | Támogatott     |
| *Több fájl* | Támogatott | Támogatott        | Nem támogatott         | Nem támogatott |

## <a name="next-steps"></a>További lépések

Az alábbi cikkek segítenek megtanulni, hogyan lehet lekérdezni a különböző mappatípusokat, fájltípusokat, valamint hogyan hozhat létre és használhat nézeteket:

- [Egyetlen CSV-fájl lekérdezése](query-single-csv-file.md)
- [Mappák és több CSV-fájl lekérdezése](query-folders-multiple-csv-files.md)
- [Adott fájlok lekérdezése](query-specific-files.md)
- [Parquet-fájlok lekérdezése](query-parquet-files.md)
- [Nézetek létrehozása és használata](create-use-views.md)
- [JSON-fájlok lekérdezése](query-json-files.md)
- [Parketta beágyazott típusai lekérdezése](query-parquet-nested-types.md)
