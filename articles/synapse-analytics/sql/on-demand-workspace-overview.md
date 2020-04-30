---
title: Igény szerinti SQL-verzió (előzetes verzió)
description: Ismerje meg az Azure szinapszis Analytics-ben igénybe veszi az SQL-t.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8d4de424d5d4d6da1ee80e04b35e63ae29df57c8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424908"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>SQL on-demand (előzetes verzió) az Azure szinapszis Analytics szolgáltatásban 

Az Azure szinapszis Analytics-munkaterülete (előzetes verzió) az SQL on-demand (előzetes verzió)-végpontokat tartalmazza, amelyekkel a Lake-ben lévő adatlekérdezéseket lehet használni.

Az SQL on-demand egy lekérdezési szolgáltatás a adatközpontban található adaton keresztül. Az alábbi funkciókkal érheti el az adatait:
 
- A jól ismert T-SQL-szintaxist, amellyel az adatlekérdezés nem szükséges, és nem kell egy speciális tárolóba másolni vagy betölteni az adatfájlokat. 
- Integrált kapcsolódás a T-SQL felületen keresztül, amely számos üzleti intelligenciát és alkalmi lekérdezési eszközt kínál, beleértve a legnépszerűbb illesztőprogramokat is. 

Az SQL on-demand egy elosztott adatfeldolgozó rendszer, amely nagy mennyiségű adattal és számítási feladatokhoz készült. Az SQL on-demand lehetővé teszi a nagy mennyiségű adatok elemzését másodpercek alatt, a munkaterhelés függvényében. A beépített lekérdezés-végrehajtás hibatűrésének köszönhetően a rendszer magas megbízhatóságot és sikerességi arányt biztosít, még a nagy adatkészleteket is tartalmazó hosszan futó lekérdezések esetében is.

Az SQL on-demand kiszolgáló nélküli, ezért nincs szükség a telepítéshez vagy a fürtök karbantartásához szükséges infrastruktúra kiépítésére. A szolgáltatás alapértelmezett végpontja minden Azure szinapszis-munkaterületen elérhető, így a munkaterület létrehozása után azonnal elindíthatja az adatlekérdezést. A fenntartott erőforrások díjmentesek, ezért csak a futtatott lekérdezések által beolvasott adatmennyiségért kell fizetnie, így ez a modell egy valódi, használaton kívüli modell.  

Ha az adatfolyamatban a Sparkot használja az adatok előkészítéséhez, tisztításához vagy bővítéséhez, akkor a folyamat során létrehozott [összes Spark-táblázatot lekérdezheti](develop-storage-files-spark-tables.md) közvetlenül az SQL igény szerint. Használja a [privát hivatkozást](../security/how-to-connect-to-workspace-with-private-links.md) , hogy az SQL igény szerinti végpontját a [felügyelt munkaterület VNet](../security/synapse-workspace-managed-vnet.md)hozza.  

## <a name="who-is-sql-on-demand-for"></a>Igény szerinti SQL-szolgáltatás

Ha meg kell vizsgálnia az adattárban lévő adatok elemzését, vagy a meglévő Adatátalakítási folyamat optimalizálását, igénybe veheti az SQL igény szerinti használatát. A következő esetekben alkalmas:

- Alapszintű felderítés és feltárás – gyorsan elsajátíthatja az adatok különböző formátumokban (a parketta, a CSV, a JSON) tárolt adatait, így megtervezheti az elemzések kinyerését.
- Logikai adattárház – az adatok áthelyezése és átalakítása nélkül, a nyers vagy a különböző adatokon alapuló összevont absztrakciót biztosít, amely lehetővé teszi az adatok naprakész áttekintését.
- Adatátalakítás – egyszerű, méretezhető és elvégezhető az adatok átalakítása a tóban a T-SQL használatával, így a BI-hoz és más eszközökhöz is betölthetők, vagy egy rokon adattárolóba (szinapszis SQL-adatbázisok, Azure SQL Database stb.).

A különböző szakmai szerepkörök az SQL igény szerint részesülhetnek:

- Az adatmérnökök megtekinthetik a tavat, átalakítják és előkészíthetik az adatfeldolgozást, és egyszerűbbé teszik az Adatátalakítási folyamatokat. További információért olvassa el ezt az [oktatóanyagot](tutorial-data-analyst.md).
- Az adatszakértők gyorsan megtudhatják, hogy a tó adatainak tartalma és szerkezete milyen funkciókkal, például a OPENROWSET és az automatikus séma-következtetésekkel kapcsolatos.
- Az adatelemzők megtekinthetik az adatszakértők vagy adatmérnökök által létrehozott, ismerős T-SQL nyelvvel vagy azok kedvenc eszközeivel létrehozott, az SQL igény szerinti elérésére képes, az adatelemzési [és a Spark-táblákat](develop-storage-files-spark-tables.md) .
- A BI-szakemberek gyorsan [hozhatnak létre Power bi jelentéseket a Lake és a](tutorial-connect-power-bi-desktop.md) Spark tábláiban.

## <a name="what-do-i-need-to-do-to-start-using-it"></a>Mit kell tennem a használat megkezdéséhez?

Az igény szerinti SQL-végpont minden Azure-beli szinapszis-munkaterületen elérhető. Létrehozhat egy munkaterületet, és azonnal megkezdheti az adatlekérdezést az Ön által jól ismert eszközök használatával.

## <a name="client-tools"></a>Ügyféleszközök

Az SQL on-demand lehetővé teszi a meglévő SQL ad-hoc lekérdezési és üzleti intelligenciát használó eszközök használatát a (z) adatközpontba. Ahogy a jól ismert T-SQL-szintaxist nyújt, a TDS-kapcsolati SQL-ajánlatokat kiépítő bármely eszköz [csatlakozhat és lekérdezheti a SZINAPSZIS SQL igény szerinti elérését](connect-overview.md) . A Azure Data Studiohoz csatlakozhat, és alkalmi lekérdezéseket futtathat, vagy csatlakozhat a Power BIhoz, és percek alatt megszerezheti a betekintést.

## <a name="is-full-t-sql-supported"></a>A teljes T-SQL támogatott?

Az SQL on-demand a T-SQL lekérdezési felületét kínálja, amely némileg kibővített/bővíthető bizonyos aspektusokban, hogy a részben strukturált és strukturálatlan adatmennyiségek lekérdezéséhez igazodjon. Emellett a T-SQL nyelv néhány aspektusa nem támogatott az SQL igény szerinti kialakítása miatt, például a DML funkció jelenleg nem támogatott.

- A számítási feladatok jól ismert fogalmakkal rendezhetők:
- Adatbázisok – az igény szerinti SQL-végpont több adatbázissal is rendelkezhet.
- Sémák – egy adatbázison belül egy vagy több, sémák nevű objektum tulajdonosi csoport is lehet.
- Nézetek
- Külső erőforrások – adatforrások, fájlformátumok és táblák

A biztonság a használatával kényszeríthető:

- Bejelentkezések és felhasználók
- A Storage-fiókokhoz való hozzáférés vezérlésére szolgáló hitelesítő adatok
- Engedélyek megadása, megtagadása és visszavonása az egyes objektumok szintjén
- Azure Active Directory-integráció

Támogatott T-SQL:

- A teljes [kijelölési](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) felület támogatott, beleértve az SQL-függvények többségét
- CETAS – KÜLSŐ TÁBLA LÉTREHOZÁSA KIJELÖLÉSKÉNT
- Csak nézetekkel és biztonsággal kapcsolatos DDL-utasítások

Az SQL on-demand nem rendelkezik helyi tárterülettel, csak a metaadat-objektumokat tárolja az adatbázisokban. Ezért a következő fogalmakhoz kapcsolódó T-SQL nem támogatott:

- Táblák
- Eseményindítók
- Lényeges nézetek
- A nézetekhez és a biztonsághoz kapcsolódó DDL-utasítások
- DML-utasítások

### <a name="extensions"></a>Bővítmények

Ahhoz, hogy zökkenőmentesen lehessen megtapasztalni a fájlokban tárolt adatlekérdezéseket a adattárban, az SQL igény szerint kiterjeszti a meglévő [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) függvényt a következő képességek hozzáadásával:

[Több fájl vagy mappa lekérdezése](develop-storage-files-overview.md#query-multiple-files-or-folders)

[PARKETTA fájlformátum](develop-storage-files-overview.md#parquet-file-format)

[A tagolt szöveg (mező lezáró, sor lezáró, escape-karakter) használatának további lehetőségei](develop-storage-files-overview.md#additional-options-for-working-with-delimited-text)

[Oszlopok kiválasztott részhalmazának olvasása](develop-storage-files-overview.md#read-a-chosen-subset-of-columns)

[Séma-következtetés](develop-storage-files-overview.md#schema-inference)

[filename függvény](develop-storage-files-overview.md#filename-function)

[filepath függvény](develop-storage-files-overview.md#filepath-function)

[Összetett típusok és beágyazott vagy ismétlődő adatstruktúrák használata](develop-storage-files-overview.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Biztonság

Az SQL on-demand az adataihoz való hozzáférés biztosítására szolgáló mechanizmusokat kínál.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

Az SQL on-demand segítségével központilag kezelheti az adatbázis-felhasználó és más Microsoft-szolgáltatások identitásait [Azure Active Directory integrációval](../../sql-database/sql-database-Azure AD-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. A Azure Active Directory (Azure AD) támogatja a [többtényezős hitelesítést](../../sql-database/sql-database-ssms-mfa-authentication-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA) az adatkezelés és az alkalmazások biztonságának növelésére az egyszeri bejelentkezési folyamat támogatása mellett.

#### <a name="authentication"></a>Hitelesítés

Az igény szerinti SQL-hitelesítés arra utal, hogy a felhasználók hogyan igazolják identitásukat a végponthoz való csatlakozáskor. Két hitelesítési típus támogatott:

- **SQL-hitelesítés**

  Ez a hitelesítési módszer egy felhasználónevet és egy jelszót használ.

- **Azure Active Directory hitelesítés**:

  Ez a hitelesítési módszer Azure Active Directory által felügyelt identitásokat használ. Az Azure AD-felhasználók esetében a többtényezős hitelesítés is engedélyezhető. [Amikor csak lehet](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), használja az Active Directory-hitelesítést (beépített biztonság).

#### <a name="authorization"></a>Engedélyezés

Az engedélyezés arra utal, hogy a felhasználók mit tehetnek egy SQL igény szerinti adatbázison belül, és a felhasználói fiók adatbázis-szerepkör-tagságai és objektum-szintű engedélyei vezérlik.

SQL-hitelesítés használata esetén az SQL-felhasználó csak az igény szerinti SQL-alkalmazásban létezik, és az engedélyek hatóköre az SQL igény szerinti objektumaira vonatkozik. A biztonságos objektumok elérését más szolgáltatásokban (például az Azure Storage-ban) nem lehet közvetlenül az SQL-felhasználó számára biztosítani, mert csak az SQL igény szerinti hatókörében van. Az SQL-felhasználónak a [támogatott engedélyezési típusok](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) egyikét kell használnia a fájlok eléréséhez.

Ha az Azure AD-hitelesítést használja, a felhasználók bejelentkezhetnek az SQL igény szerinti és egyéb szolgáltatásaiba, például az Azure Storage-ba, és engedélyeket adhatnak az Azure AD-felhasználónak.

### <a name="access-to-storage-accounts"></a>Hozzáférés a Storage-fiókokhoz

Az SQL igény szerinti szolgáltatásba bejelentkezett felhasználónak jogosultnak kell lennie az Azure Storage-ban tárolt fájlok elérésére és lekérdezésére. Az SQL on-demand a következő engedélyezési típusokat támogatja:

- A **közös hozzáférésű aláírás (SAS)** delegált hozzáférést biztosít a Storage-fiók erőforrásaihoz. A SAS használatával a fiók kulcsainak megosztása nélkül biztosíthat hozzáférést az ügyfeleknek a Storage-fiók erőforrásaihoz. Az SAS részletesen szabályozhatja az olyan ügyfelek számára biztosított hozzáférés típusát, akik SAS: érvényességi időtartam, megadott engedélyek, elfogadható IP-címtartomány, elfogadható protokoll (https/http).

- A **felhasználói identitás** (más néven "átmenő") olyan engedélyezési típus, ahol az SQL-on igénybe vett Azure ad-felhasználó identitása az adatokhoz való hozzáférés engedélyezésére szolgál. Az adatok elérése előtt az Azure Storage rendszergazdájának engedélyeket kell adnia az Azure AD-felhasználónak az adatokhoz való hozzáféréshez. Ez az engedélyezési típus az SQL on-demand szolgáltatásba bejelentkezett Azure AD-felhasználót használja, ezért az SQL-felhasználók típusai nem támogatottak.

## <a name="next-steps"></a>További lépések
A végponti kapcsolatok és a lekérdezési fájlok további információi a következő cikkekben találhatók: 
- [Kapcsolódás a végponthoz](connect-overview.md)
- [Fájlok lekérdezése](develop-storage-files-overview.md)
