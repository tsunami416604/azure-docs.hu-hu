---
title: SQL igény szerinti (előzetes verzió)
description: Ismerje meg a Synapse SQL igény szerinti, igény szerinti szolgáltatását az Azure Synapse Analytics szolgáltatásban.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8d4de424d5d4d6da1ee80e04b35e63ae29df57c8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424908"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>SQL igény szerinti (előzetes verzió) az Azure Synapse Analytics szolgáltatásban 

Minden Azure Synapse Analytics munkaterület (előzetes verzió) jön az SQL igény szerinti (előzetes verzió) végpontok, amelyek segítségével adatok lekérdezése a tóban.

SQL on-demand egy lekérdezési szolgáltatás az adatok a data lake. Lehetővé teszi az adatok elérését a következő funkciókkal:
 
- Ismerős T-SQL szintaxis az adatok lekérdezéséhez anélkül, hogy adatokat kellene másolni vagy betölteni egy speciális tárolóba. 
- Integrált kapcsolat a T-SQL felületen keresztül, amely az üzleti intelligencia széles skáláját kínálja, és ad-hoc lekérdezési eszközöket kínál, beleértve a legnépszerűbb illesztőprogramokat is. 

Sql on-demand egy elosztott adatfeldolgozó rendszer, amely et nagy mennyiségű adat és a számítási. Sql on-demand lehetővé teszi, hogy elemezze a Big Data másodpercek alatt perc, a ttól függően, hogy a számítási feladatok. A beépített lekérdezés-végrehajtási hibatűrésnek köszönhetően a rendszer nagy megbízhatósági és sikerességi arányokat biztosít még a nagy adatkészleteket tartalmazó, hosszú ideig futó lekérdezések esetén is.

Sql on-demand kiszolgáló nélküli, ezért nincs infrastruktúra beállítása vagy fürtök fenntartása. A szolgáltatás alapértelmezett végpontja minden Azure Synapse-munkaterületen belül biztosított, így a munkaterület létrehozása után már elkezdheti az adatok lekérdezését. A fenntartott erőforrásokért nem számítunk fel díjat, csak a futtatott lekérdezések által beolvasott adatokért kell fizetnie, ezért ez a modell egy valódi használatalapú fizetésmodell.  

Ha a Sparkot használja az adatfolyamatokban, az adatok előkészítéséhez, tisztításához vagy bővítéséhez, lekérdezheti a folyamat során létrehozott [Spark-táblákat,](develop-storage-files-spark-tables.md) közvetlenül az SQL igény szerinti rendszeréből. A [Private Link](../security/how-to-connect-to-workspace-with-private-links.md) használatával igény szerinti SQL-végpontot vihet a felügyelt [munkaterületi virtuális hálózatba.](../security/synapse-workspace-managed-vnet.md)  

## <a name="who-is-sql-on-demand-for"></a>Ki az SQL on-demand

Ha az adattóban adatokat szeretne feltárni, betekintést nyerhet belőle, vagy optimalizálni a meglévő adatátalakítási folyamatot, igény szerint használhatja az SQL előnyeit. Ez alkalmas a következő forgatókönyvek:

- Alapvető felfedezés és feltárás – Gyorsan okaz adatok különböző formátumokban (Parquet, CSV, JSON) az adattóban, így megtervezheti, hogyan nyerhet ki belőle elemzéseket.
- Logikai adattárház – Relációs absztrakció biztosítása a nyers vagy eltérő adatok tetején az adatok áthelyezése és átalakítása nélkül, lehetővé téve az adatok mindig naprakész nézetét.
- Adatátalakítás – Egyszerű, skálázható és hatékony módja annak, hogy a T-SQL használatával átalakítsa az adatokat a tóban, így betáplálható a BI és más eszközök betöltése, vagy egy relációs adattárba (Synapse SQL-adatbázisok, Azure SQL Database stb.).

A különböző szakmai szerepkörök igény szerint élvezhetik az SQL előnyeit:

- Adatmérnökök felfedezheti a tó, átalakítása és előkészítése adatok ezzel a szolgáltatással, és egyszerűsíti az adatátalakítási folyamatok. További információért tekintse meg ezt az [oktatóanyagot.](tutorial-data-analyst.md)
- Adattudósok gyorsan oka a tartalmát és szerkezetét az adatok a tóban, köszönhetően funkciók, mint például OPENROWSET és automatikus séma következtetés.
- Az adatelemzők a data scientists vagy adatmérnökök által létrehozott [adatokat és Spark-táblákat a](develop-storage-files-spark-tables.md) jól ismert T-SQL nyelv vagy kedvenc eszközeik használatával fedezhetik fel, amelyek igény szerint csatlakozhatnak az SQL-hez.
- Az üzletiintelligencia-szakemberek gyorsan [létrehozhatnak Power BI-jelentéseket a tó- és a Spark-táblák ban lévő adatok alapján.](tutorial-connect-power-bi-desktop.md)

## <a name="what-do-i-need-to-do-to-start-using-it"></a>Mit kell tennem a használat megkezdéséhez?

SQL igény szerinti végpont minden Azure Synapse-munkaterületen belül biztosított. A jól ismert eszközökkel munkaterületet hozhat létre, és azonnal megkezdheti az adatok lekérdezését.

## <a name="client-tools"></a>Ügyféleszközök

Sql on-demand lehetővé teszi a meglévő SQL ad-hoc lekérdezési és üzleti intelligencia eszközök kihasználni a data lake. Mivel ismerős T-SQL szintaxist biztosít, minden olyan eszköz, amely képes létrehozni a TDS-kapcsolat SQL-ajánlatait, [képes csatlakozni a Synapse SQL-hez](connect-overview.md) igény szerint. Kapcsolatba léphet az Azure Data Stuzóval, és alkalmi lekérdezéseket futtathat, vagy kapcsolatba léphet a Power BI-val, hogy percek alatt betekintést nyerjen.

## <a name="is-full-t-sql-supported"></a>A teljes T-SQL támogatott?

SQL on-demand kínál T-SQL lekérdezési felület, amely kissé továbbfejlesztett/kiterjesztett bizonyos szempontból, hogy alkalmazkodjon a tapasztalatok körül lekérdezése félig strukturált és strukturálatlan adatok. Továbbá a T-SQL nyelv bizonyos aspektusai nem támogatottak az SQL igény szerinti tervezése miatt, például a DML funkció jelenleg nem támogatott.

- A munkaterhelés ismerős fogalmak alapján rendszerezhető:
- Adatbázisok – SQL on-demand végpont több adatbázist is rendelkezhet.
- Sémák – egy adatbázison belül lehet egy vagy több objektum tulajdonosi csoportok úgynevezett sémák.
- Nézetek
- Külső erőforrások – adatforrások, fájlformátumok és táblázatok

A biztonság a következő használatával érvényesíthető:

- Bejelentkezések és felhasználók
- Hitelesítő adatok a tárfiókokhoz való hozzáférés szabályozásához
- Engedélyek megadása, megtagadása és visszavonása objektumszintenként
- Azure Active Directory-integráció

Támogatott T-SQL:

- A teljes [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) felület támogatott, beleértve az SQL függvények többségét
- CETAS - KÜLSŐ TÁBLA LÉTREHOZÁSA KIJELÖLÉSKÉNT
- DDL-nyilatkozatok csak nézetekre és biztonságra vonatkozóan

Az igény szerinti SQL nem rendelkezik helyi tárolóval, csak metaadat-objektumokat tárol az adatbázisok. Ezért a következő fogalmakhoz kapcsolódó T-SQL nem támogatott:

- Táblák
- Eseményindítók
- Materializált nézetek
- A nézetekhez és a biztonsághoz kapcsolódó ddl-nyilatkozatoktól eltérő
- DML-utasítások

### <a name="extensions"></a>Bővítmények

Annak érdekében, hogy zökkenőmentes felhasználói élményt nyújthasson a Data Lake fájljaiban található adatok lekérdezéséhez, az SQL on-demand a következő képességek hozzáadásával bővíti a meglévő [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) függvényt:

[Több fájl vagy mappa lekérdezése](develop-storage-files-overview.md#query-multiple-files-or-folders)

[PARQUET fájlformátum](develop-storage-files-overview.md#parquet-file-format)

[A tagolt szöveggel való munka további lehetőségei (mezőterminátor, sorlesek, escape karakter)](develop-storage-files-overview.md#additional-options-for-working-with-delimited-text)

[Oszlopok kiválasztott részhalmazának olvasása](develop-storage-files-overview.md#read-a-chosen-subset-of-columns)

[Séma-következtetés](develop-storage-files-overview.md#schema-inference)

[fájlnév függvény](develop-storage-files-overview.md#filename-function)

[filepath függvény](develop-storage-files-overview.md#filepath-function)

[Összetett típusokkal és beágyazott vagy ismétlődő adatstruktúrákkal végzett munka](develop-storage-files-overview.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Biztonság

Az SQL igény szerinti mechanizmusokat kínál az adatokhoz való hozzáférés biztosítására.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

Az SQL igény szerinti lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások identitásának központi kezelését az [Azure Active Directory-integrációval.](../../sql-database/sql-database-Azure AD-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. Az Azure Active Directory (Azure AD) támogatja a [többtényezős hitelesítést](../../sql-database/sql-database-ssms-mfa-authentication-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA) az adatok és az alkalmazások biztonságának növelése érdekében, miközben támogatja az egyszeri bejelentkezési folyamatot.

#### <a name="authentication"></a>Hitelesítés

SQL igény szerinti hitelesítés azt, hogy a felhasználók hogyan bizonyítják identitásukat, amikor csatlakozik a végponthoz. Kétféle hitelesítés támogatott:

- **SQL-hitelesítés**

  Ez a hitelesítési módszer felhasználónevet és jelszót használ.

- **Azure Active Directory-hitelesítés:**

  Ez a hitelesítési módszer az Azure Active Directory által kezelt identitásokat használja. Az Azure AD-felhasználók számára a többtényezős hitelesítés engedélyezhető. [Amikor csak lehet](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), használja az Active Directory-hitelesítést (beépített biztonság).

#### <a name="authorization"></a>Engedélyezés

Az engedélyezés arra utal, hogy a felhasználó mit tehet az SQL igény szerinti adatbázisában, és a felhasználói fiók adatbázis-szerepkör-tagságai és objektumszintű engedélyei szabályozzák.

Sql-hitelesítés használata esetén az SQL-felhasználó csak az SQL igény szerinti rendszerben létezik, és az engedélyek hatóköre az SQL-ben igény szerinti objektumokra terjed ki. Hozzáférés más szolgáltatásokban (például az Azure Storage-ban) biztonságos objektumokhoz közvetlenül nem adhatók meg az SQL-felhasználónak, mivel csak az SQL igény szerinti hatókörében léteznek. Az SQL-felhasználónak a [támogatott engedélyezési típusok](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) egyikét kell használnia a fájlok eléréséhez.

Ha Azure AD-hitelesítést használ, a felhasználó bejelentkezhet az SQL igény szerinti és más szolgáltatásokba, például az Azure Storage-ba, és engedélyeket adhat az Azure AD-felhasználónak.

### <a name="access-to-storage-accounts"></a>Hozzáférés a tárfiókokhoz

Az SQL igény szerinti szolgáltatásba bejelentkezett felhasználónak jogosultnak kell lennie a fájlok eléréséhez és lekérdezéséhez az Azure Storage-ban. Az SQL igény szerinti szolgáltatása a következő engedélyezési típusokat támogatja:

- **A megosztott hozzáférésű aláírás (SAS)** delegált hozzáférést biztosít a tárfiókban lévő erőforrásokhoz. A SAS segítségével hozzáférést biztosíthat az ügyfeleknek a tárfiókban lévő erőforrásokhoz a fiókkulcsok megosztása nélkül. A SAS segítségével részletesen szabályozhatja a SAS-sel rendelkező ügyfeleknek biztosított hozzáférés típusát: érvényességi időköz, megadott engedélyek, elfogadható IP-címtartomány, elfogadható protokoll (https/http).

- **Felhasználói identitás** (más néven "áthaladás") egy engedélyezési típus, ahol az Azure AD-felhasználó, amely bejelentkezett az SQL on-demand az adatokhoz való hozzáférés engedélyezéséhez használt identitását. Az adatok elérése előtt az Azure Storage-rendszergazdának engedélyeket kell adnia az Azure AD-felhasználónak az adatok eléréséhez. Ez az engedélyezési típus az Azure AD-felhasználó, amely bejelentkezett az SQL igény szerint, ezért az SQL-felhasználói típusok nem támogatott.

## <a name="next-steps"></a>További lépések
A végpontok kapcsolatával és lekérdezésével kapcsolatos további információk a következő cikkekben találhatók: 
- [Csatlakozás a végponthoz](connect-overview.md)
- [Fájlok lekérdezése](develop-storage-files-overview.md)
