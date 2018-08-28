---
title: Az Azure Data Catalog újdonságai
description: Ez a cikk az Azure Data Cataloghoz hozzáadott új funkcióinak áttekintése.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 61f837fbe7f53cdf9f313e5498c6f99ea14203a7
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047005"
---
# <a name="whats-new-in-azure-data-catalog"></a>What's new in Azure Data Catalog
Frissítések **Azure Data Catalog** rendszeresen jelennek meg. Nem minden kiadás új szolgáltatásokkal rendelkező felhasználó, néhány kiadásai háttérszolgáltatás képességek összpontosítanak. Ez az oldal az Azure Data Catalog szolgáltatásban hozzáadott új felhasználói képességek emeli ki.

## <a name="whats-new-for-november-2017"></a>2017. novemberi újdonságai 
2017. November az Azure Data Cataloghoz az alábbi képességekkel bővült:

* Támogatás a közvetlenül összekapcsolása a Data Catalog-portál az adott üzleti szószedet kifejezéseit. A felhasználók hivatkozások átmásolhatja az üzleti szószedet és a beágyazása, e-mailek, jelentések, vagy más helyekre mutató közvetlen hivatkozás szerepel a szószedet kifejezés definícióját a.
* Azure Active Directory egyszerű szolgáltatások támogatása. A Data Catalog rendszergazdái is engedélyezik a szolgáltatásnevek használatával is hozzáférhet a katalógushoz ügyfélalkalmazások számára, és az engedélyeket az alkalmazások adott ugyanúgy, mint a azokat az engedélyeket felhasználók és biztonsági csoportok. További információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md).
* Az Azure Active Directory-hitelesítés a Data Catalog adatforrás-regisztráló eszköz használatával Azure SQL Database és az Azure SQL Data Warehouse-adatok forrásokhoz való kapcsolódáskor támogatása. További információkért lásd: [használata az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse-hitelesítéshez](../sql-database/sql-database-aad-authentication.md).


## <a name="whats-new-for-september-2017"></a>2017. szeptemberi újdonságai 
2017. szeptember az Azure Data Cataloghoz az alábbi képességekkel bővült:

* Kibontása támogatása csatlakozzon entitáskapcsolati metaadatok DB2 adatforrásokból származó, kapcsolódó táblák az adatforrás-regisztráló eszköz használatával történő regisztráláskor.
* Támogatja a MongoDB verzió 3.4 adatforrások regisztrálása az adatforrás-regisztráló eszköz használatával.
* Támogatja az összes befoglalt objektum egyetlen művelettel metaadatait törlése, ha egy adatbázist vagy más tárolóban eltávolítása a Data Catalog.
* Támogatási meg, legfeljebb 1000 címkéket, üzleti szószedet kifejezéseit vagy további keresési metszettel, ha a Data Catalog-portálon egy keresési finomítása.


## <a name="whats-new-for-august-2017"></a>2017. augusztusi újdonságai 
2017. augusztus az Azure Data Cataloghoz az alábbi képességekkel bővült:

*   Új fejlesztői mintát hozhat létre és kezelhet entitáskapcsolati metaadatok a Data Catalog REST API használatával érhető el. A *kapcsolat adatainak importálása a Data Catalog* minta érhető el a [a Data Catalog kódminta-oldala](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0). 
* Kibontása támogatása csatlakozzon entitáskapcsolati metaadatok Teradata adatforrásokból származó, kapcsolódó táblák az adatforrás-regisztráló eszköz használatával történő regisztráláskor.
* Támogatja az SQL Server táblázat értékű függvény (TVF) objektumok SQL Server-adatforrásait az adatforrás-regisztráló eszköz használatával történő regisztráláskor.
* Több frissítés és a teljesítmény és a Data Catalog-portál használhatóságának növeléséhez a finomítások.

## <a name="whats-new-for-july-2017"></a>2017 júliusi újdonságai 
2017 júliusától az Azure Data Cataloghoz az alábbi képességekkel bővült:
*   Részletesebb vezérléshez keresztül többek között lehetővé metaadat-műveletek támogatása:
    - Katalógus-rendszergazdák működhet közre a címkék és a kapcsolódó metaadatokat a katalógust, hogy a felhasználók korlátozhatja a csak olvasási hozzáférés a katalógus engedélyezése.
    - Katalógus-rendszergazdák az új adatforrások regisztrálása a katalógusban, hogy a felhasználók is korlátozhatja.
    - Katalógus-rendszergazdák korlátozhatja az adatok adategység metaadatainak szerezhetnek a katalógusban, hogy a felhasználók.
    - Engedélyek az Azure Active Directory biztonsági csoportokat és felhasználói engedélyek kezelése a könnyű adható meg.
* Regisztrált adategységeket, és a Data Catalog-portál felderítő kapcsolódó adategységek közötti kapcsolatok támogatása többek között:
    - Entitáskapcsolati metaadatok kivonása (beleértve az Azure SQL Database) az SQL Server, Oracle- és MySQL az adatforrásokhoz a Data Catalog adatforrás-regisztráló eszköz használatakor.
    - Ha az adategység metaadatainak megtekintése a Data Catalog-portál a kapcsolódó adategységek felderítése.
    - Műveletek definiálásához, Fedezze fel, és adategységeket a Data Catalog REST API használatával kapcsolatai kezelheti.

A Data Catalog az engedélyek kezelésének további részletekért lásd: [hogyan biztonságos hozzáférés az adatkatalógushoz és adategységeket](data-catalog-how-to-secure-catalog.md).
A Data Catalog kapcsolatok további részletekért lásd: [kapcsolódó adategységek megtekintése az Azure Data Catalog](data-catalog-how-to-view-related-data-assets.md).

## <a name="whats-new-for-june-2017"></a>2017 júniusi Újdonságok 
2017 júniusától az Azure Data Cataloghoz az alábbi képességekkel bővült:
*   Apache Cassandra, mongodb-hez és a Sybase-adatforrások támogatása. A felhasználók mostantól regisztrálása és Fedezze fel a Cassandra- és MongoDB adatbázisok és táblák és a Sybase-adatbázis, táblák és nézetek.

> [!NOTE]
> Mongodb-hez és a Cassandra-táblák, beleértve az összetett adattípusok, például a rekordok és -tömböket tartalmazó oszlopok regisztrálásakor a ezeknek az oszlopoknak nem szerepelni fog a Data-katalógushoz hozzáadott metaadatait.

## <a name="whats-new-for-may-2017"></a>What's new for 2017. május 
2017. május az Azure Data Cataloghoz az alábbi képességekkel bővült:
*   Új fejlesztői mintát érhető el az üzleti szószedet kifejezéseit tömeges importálásához. A szószedet tömeges importálás mintából érhető el a [Data Catalog fejlesztői minták lapján](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples). 
*   Szerkesztési, ODBC kapcsolati adatai az Azure Data Catalog-portál támogatása. Adatok adatforrások tulajdonosai és a Data Catalog-rendszergazdák most már szerkesztheti regisztrált ODBC-adatforrás kapcsolati adatait anélkül, hogy regisztrálja újra az adatforrásokat.
*   Az üzleti szószedet definíciók és -leírások kattintható URL-címek támogatása. URL-címek az üzleti szószedet kifejezéseit a leírást és a definíció tulajdonságai szerepelnek, ha az URL-címeket, a hivatkozások a Data Catalog-portálon jelennek meg.
*   Szakértői e-mail-címek mellett szakértői nevek megjelenítésének támogatása. A Data Catalog portál szakértők adategység tulajdonságainak megtekintésekor a szakértő teljes nevét az Azure Active Directoryból az elemleírás jelenik meg.

## <a name="whats-new-for-april-2017"></a>2017. áprilisi újdonságai 
2017. április az Azure Data Cataloghoz az alábbi képességekkel bővült:
*   ODBC-adatforrások támogatása. A felhasználók mostantól regisztrálása és ODBC adatbázisok, táblák és nézetek használata a Data Catalog adatforrás-regisztráló eszköz felderítése.

## <a name="whats-new-for-march-2017"></a>2017. márciusi újdonságai 
2017. március az Azure Data Cataloghoz az alábbi képességekkel bővült:
*   AAD-biztonságicsoportok használata a Data Catalog-rendszergazdák támogatása.
*   Az Azure Data Catalog szolgáltatás már elérhető egy új Azure-régióban. Ügyfelek üzembe helyezhetik az Azure Data Catalog régióban az USA nyugati középső Régiója, USA keleti RÉGIÓJA, USA nyugati RÉGIÓJA, Nyugat-Európa és Kelet-Ausztrália, Észak-Európa és Délkelet-Ázsia mellett. További információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).

## <a name="whats-new-for-february-2017"></a>2017. februári újdonságai 
2017 február az Azure Data Cataloghoz az alábbi képességekkel bővült:
*   A Data Catalog adatforrás-regisztráló eszköz a Speciális beállítások támogatása. Felhasználók parancs időtúllépési értékeket adhat meg, ha nagy méretű adatforrások regisztrálása.
*   Az FTP és a PostgreSQL-adatforrások támogatása. A felhasználók mostantól regisztrálása és az FTP-fájlok és könyvtárak, és a PostgreSQL adatbázisokat, táblák és nézetek felderítése.

## <a name="whats-new-for-january-2017"></a>What's new for 2017. január 
2017. január az Azure Data Cataloghoz az alábbi képességekkel bővült:
*   Az Azure Data Catalog már [CSA STAR](https://www.microsoft.com/en-us/trustcenter/compliance/csa-star-certification) megfelelő.
*   Integráció a [beolvasás és átalakítás az Excel 2016-ban és a Power Queryvel az Excelt](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605). Az Excel-felhasználók is lekérdezések Fedezzen fel és az Azure Data Catalog használatával lekérdezéseket az Excel belül. Ez a funkció a Power BI Pro licenccel rendelkező felhasználók számára érhető el.

## <a name="whats-new-for-december-2016"></a>Újdonságok 2016. December
2016 Decemberétől az Azure Data Cataloghoz az alábbi képességekkel bővült:
*   Az Azure Data Catalog már [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) és [uniós modellként szolgáló szerződéses klauzulák](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses) megfelelő.
*   Támogatja az adatforrás kapcsolati adatait szerkesztését. Adatok adatforrások tulajdonosai és a Data Catalog-rendszergazdák most már szerkesztheti a regisztrált adatforrások kapcsolati adatok anélkül, hogy regisztrálja újra az adatforrásokat.
*   A Salesforce.com-adatforrások támogatása. A felhasználók most már regisztrálása és a Salesforce-objektumok felderítése.


## <a name="whats-new-for-november-2016"></a>Újdonságok 2016. November
2016 November az Azure Data Cataloghoz az alábbi képességekkel bővült:
*   Az Azure Data Catalog már [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) és [ISO/IEC 27018](https://www.microsoft.com/en-us/TrustCenter/Compliance/iso-iec-27018) megfelelő.
*   A Data Catalog-portál és a REST API-t ODBC adatforrások manuális regisztrációja támogatása.

## <a name="whats-new-for-september-2016"></a>Újdonságok 2016. szeptember
2016 szeptemberétől Azure Data Catalog lettek hozzáadva a következő képességekkel:

* IBM DB2-adatforrások támogatása. A felhasználók mostantól regisztrálása és DB2-adatbázisok, táblák és nézetek felderítése.
* Azure Cosmos DB-adatforrások támogatása. A felhasználók mostantól regisztrálása és Fedezze fel a Cosmos DB-adatbázisok és gyűjtemények.
* A katalógus neve, a Data Catalog portál testreszabására támogatása. Katalógus-rendszergazdák most már megadhat például a szervezet nevét a portál címében megjelenített szöveget.

## <a name="whats-new-for-august-2016"></a>Újdonságok 2016. augusztus
Az Azure Data Catalog 2016 augusztusában az alábbi képességekkel bővült:

* Az SQL Server Master Data Services (MDS) adatforrások regisztrációs fejlesztései. Felhasználók is között már elérhető előzetes verziók és az adatok profilok MDS entitásokat a Data Catalog adatforrás-regisztráló eszköz használatával történő regisztráláskor.
* Rendszergazda által meghatározott szervezeti mentett keresések támogatása. A Keresés mentése a Data Catalog-portálon, ha a Data Catalog-rendszergazdák választhatják ki a személyes használatra vagy a katalógus minden felhasználó számára a keresések mentése. Mentett szervezeti keresés összes katalógus felhasználói vannak megosztva, és tud biztosítani a szabványos kezdőpontja az adatforrások felderítését.
* A Tulajdonságok megtekintése a Data Catalog portál frissítése. Minden adategység tulajdonságainak most már megjelenik, és a egy egyszerű, méretezhető ablaktáblán biztosít olyan egységes, felderíthető élmény felügyelt.

## <a name="whats-new-for-july-2016"></a>2016. júliusi újdonságai
2016. július az Azure Data Cataloghoz az alábbi képességekkel bővült:

* Az SQL Server Master Data Services (MDS) adatforrások támogatása. A felhasználók mostantól regisztrálása és Fedezze fel az MDS-modellt és entitást.
* SQL Server tárolt eljárásai támogatása. A felhasználók mostantól regisztrálása és az SQL Server-adatforrásait tárolt eljárás objektumok felderítése.
* Az Azure Data Catalog-portál és az adatforrás-regisztráló eszköz, a támogatott nyelvek 18 összesen további nyelvek támogatásával. Az Azure Data Catalog felhasználói honosított a Windows vagy a böngésző nyelvi beállítások alapján.
* Frissítések és a Data Catalog portál kezdőlapján, beleértve a teljesítménnyel kapcsolatos fejlesztések és a egy zökkenőmentes felhasználói élmény a finomítható.

## <a name="whats-new-for-june-2016"></a>2016. júniusi Újdonságok
2016. június az Azure Data Cataloghoz az alábbi képességekkel bővült:

* A lista nézet oszlopainak átméretezéséhez, ha a Data Catalog-portál a data calatog támogatása. Felhasználók most már méretezheti át könnyebben olvasható hosszadalmas adategység metaadatainak, például a címkék és leírások az egyes oszlopok.
* A Power Queryvel az Excelt a Data Catalog-portálon a "Megnyitás" menü bővült. Felhasználók most megnyithatja a támogatott adatforrások az Excel 2016 vagy Excel 2010, illetve az Excel 2013-at a [Power Queryvel az Excelt](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) bővítményt.
* Azure Table Storage-adatforrások támogatása. A felhasználók most már regisztrálása és az Azure Storage-adatforrások tábla objektumok felderítése.

## <a name="whats-new-for-may-2016"></a>Újdonságok 2016. május
2016. május az Azure Data Cataloghoz az alábbi képességekkel bővült:

* Egy üzleti szószedetet, amely lehetővé teszi, hogy a katalógus-rendszergazdák üzleti kifejezéseket és hierarchiákat hozhat létre egy közös üzleti szószedet meghatározásához. Felhasználók címkékkel láthatják el, hogy könnyebben megtalálhatóvá és értelmezhetővé a katalógus tartalmának a szószedet kifejezéseivel jelölhetik regisztrált adategységeket. További információk: [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Az irányított címkézéshez használt üzleti szószedet összeállítása)  
* A Data Catalog üzleti szószedet, amely lehetővé teszi a felhasználók frissíthetik a egyetlen művelettel több szószedet kifejezései fejlesztései. Felhasználók által választható több feltételek szerkesztése a következő mezőket:
  * Szülőkifejezés: A felhasználó kiválaszthat egy új szülőkifejezés, és az összes kiválasztott feltételek frissítve lett, hogy a kiválasztott szülőkifejezés gyermeke lehet. A kijelölt feltételek összes azonos a szülővel rendelkezik, akkor a szülő látható a szövegmezőbe, ellenkező esetben a szülőkifejezés mező értéke üres.   
  * A címkék és a közreműködők: felhasználók hozzáadhat és eltávolíthat a címkék és a közreműködők több szószedet feltételek használatával egyszerre több adategységet címkézés ugyanazt a felhasználói élményt.

> [!NOTE]
> Az üzleti szószedet csak a az Azure Data Catalog Standard kiadásában érhető el. Az ingyenes kiadás nem biztosít szabályozottabbá címkézés és a egy üzleti szószedetet képességeket.

## <a name="whats-new-for-march-2016"></a>2016. márciusi újdonságai
2016. március az alábbi képességekkel bővült az Azure Data Catalog: g:

* Egy összevont REST API-végpont programozott módon való hozzáféréshez szükséges a keresési lehetőségeket és a katalógus Eszközkezelési képességeket, az Azure Data Catalog szolgáltatást. A search API-végpont és katalógus API-végpont nem elavult, és 2016. március 21-én megszűnt. Nem változtak, az API-t szemantikáját. Csak a végpont URI azonosítója megváltozott. További információkért lásd: a [Azure Data Catalog REST API-referencia](https://msdn.microsoft.com/library/azure/mt267595.aspx). API-mintákat, lásd: [fejlesztői minták az Azure Data Catalog](data-catalog-samples.md).

## <a name="whats-new-for-february-2016"></a>Újdonságok 2016. február
2016 Februárja az Azure Data Cataloghoz az alábbi képességekkel bővült:

* Egy újonnan áttervezett forrás kiválasztása az Azure Data Catalog adatforrás-regisztráló eszköz felületét. Az adatforrás-regisztráló eszköz frissült, hogy egyszerűbb legyen, hogy keresse meg és válassza ki az adatforrásokból az Azure Data Catalog által támogatott.
* Az Azure Data Catalog-portál és az adatforrás-regisztráló eszköz 10 további nyelvek támogatásával. In Addition to English az Azure Data Catalog élmény már elérhető a német, spanyol, francia, olasz, japán, koreai, brazíliai portugál, orosz, egyszerűsített kínai és kínai (hagyományos). Az Azure Data Catalog felhasználói honosított megadott nyelvi beállítások a Windows vagy a felhasználó böngészőben.
* Georeplikáció az Azure Data Catalog adatok az üzleti folytonossági és vészhelyreállítási helyreállítási támogatása. Azure Data Catalog teljes tartalmát, többek között az adatok forrása metaadatok és a Közösségtől származó jegyzetek, most replikálódnak az ügyfelek számára további költségek nélkül két Azure-régiók között. Az Azure-régiók előre van párosítva, legalább 500 mérföld távolságra, és kövesse a leképezés leírtak szerint [üzleti folytonossági és vészhelyreállítási helyreállítási (BCDR): Azure párosított régiói](../best-practices-availability-paired-regions.md).
* Támogatás az Azure Data Catalog által használt Azure-előfizetés módosítása. Azure Data Catalog-rendszergazdák használatával a beállítások lapon az Azure Data Catalog-portálon válassza a számlázás szempontjából egy másik Azure-előfizetést.

## <a name="whats-new-for-january-2016"></a>Újdonságok 2016. január
2016. január az Azure Data Cataloghoz az alábbi képességekkel bővült:

* Manuális regisztrálása a további adatforrások támogatását. Most már használhatja a "Manuális bejegyzés létrehozása" az Azure Data Catalog-portálon, vagy az Azure Data Catalog REST API használatával a következő adatforrások regisztrálása:
  * OData - függvény, Entitáskészlet és Entitástároló
  * HTTP - fájl, a végpont, a jelentés és a hely
  * Fájlrendszer - fájl
  * SharePoint - lista
  * FTP - fájl és könyvtár
  * A Salesforce.com - objektum
  * DB2 - tábla, nézet és adatbázis
  * PostgreSQL - tábla, nézet és adatbázis
* "Nyissa meg az SQL Server Data Tools" támogatása (például Azure SQL Database és az Azure SQL Data Warehouse) az SQL Server-adatforrásait.  
* Regisztráció és az SAP HANA-nézetből és a csomagok felderítése támogatása. Az Azure Data cataloggal adatok adatforrás-regisztráló eszközzel és jegyzetekkel láthatja el és az Azure Data Catalog portál segítségével regisztrált SAP HANA adatforrások felfedezése az SAP HANA-adatforrások regisztrálására.
* PIN-kód és a rögzítés feloldása adategységeket az Azure Data Catalog portál lehetővé teszi. Kiválaszthatja, újbóli felderítése, és újra felhasználhatja könnyebb adatobjektumok kitűzése a.
* Az Azure Data Catalog-portálon ajánlatokig kezdőlap. Az új kezdőlap a jelenlegi felhasználók tevékenység – többek között a legutóbb közzétett eszközök, a rögzített adategységek és a mentett keresések - és a tevékenység betekintést a teljes katalógusban betekintést tartalmazza.
* Az Azure Data Catalog portál állandó felhasználói beállítások támogatása. A felhasználói élmény beállításainak – beleértve a rács vagy a csempe nézetben, a lap, és a találatok kiemelése a be- vagy kikapcsolása eredmények számát – megmaradnak a felhasználói munkamenetek között.
* Az Azure Data Catalog már elérhető két új Azure-régióban. Ügyfelek üzembe helyezhetik az Azure Data Catalog régiókban Észak-Európa és Délkelet-Ázsia, USA keleti RÉGIÓJA, USA nyugati RÉGIÓJA, Nyugat-Európa és Kelet-Ausztrália mellett. További információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).

> [!NOTE]
> "Megnyitás az SQL Server Data Tools" van szükség a Visual Studio 2013 Update 4-és SQL Server-eszközök kell telepíteni. SQL Server Data Tools legújabb verziójának telepítéséhez látogasson el [letöltése SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).


## <a name="whats-new-for-december-2015"></a>2015. decemberi újdonságai
Az Azure Data Catalog 2015 decemberében az alábbi képességekkel bővült:

* Azure SQL Data Warehouse-adatforrásokhoz tartozó adatok profilok támogatása. Azure SQL Data Warehouse-táblák és nézetek regisztrálásakor a felhasználók eldönthetik, adatok profil metrikákkal egészítheti ki a az adatforrásból kinyert metaadatok.
* Regisztráció és a MySQL-objektumok és az adatbázisok felderítéséhez támogatása. Felhasználók regisztrálhatnak az Azure Data cataloggal adatok adatforrás-regisztráló eszközzel és jegyzetekkel láthatja el és az Azure Data Catalog portál segítségével regisztrált MySQL adatforrások felfedezése MySQL adatforrások.
* A Teradata adatforrásokat SPNEGO és a Windows hitelesítés támogatása. Teradata-táblák és nézetek regisztrálásakor a felhasználók is csatlakozhat a Teradata SPNEGO és a Windows, és az LDAP és a TD2 hitelesítéssel.
* Az Azure Data Lake Store-adatforrások támogatása. Felhasználók regisztrálása és az Azure Data Catalog használatával az Azure Data Lake Store-adatforrások felfedezése most.
* Hálózati proxy-beállítások manuális megadásáról az Azure Data Catalog adatforrás-regisztráló eszköz támogatása. Felhasználók "Proxybeállítások módosítása" az eszköz kezdőlapjának választhatók ki, és adhatja meg a proxykiszolgáló címét és az eszköz által használt port.

## <a name="whats-new-for-november-2015"></a>2015. novemberi újdonságai
2015. November az Azure Data Cataloghoz az alábbi képességekkel bővült:

* Megtekintése és másolása az Azure Data Catalog-portálon a kapcsolati karakterláncok az SQL Server (beleértve az Azure SQL Database) és Oracle-adatforrások lehetővé teszi. Felhasználók rákattinthat egy SQL Server vagy az Oracle-tábla, nézet vagy adatbázis, tekintse meg a kapcsolati karakterláncokkal az adatforráshoz való kapcsolódáshoz használt kapcsolati adatait "Kapcsolati karakterláncok megtekintése" hivatkozásra. Az ADO.NET, ODBC, OLEDB és JDBC-kapcsolati karakterláncok biztosítják az SQL Server-adatforrásait. ODBC és OLEDB kapcsolati karakterláncokat biztosított Oracle-adatforrások esetében.
* Teradata-táblák és nézetek regisztrálásakor, beleértve az adatok profilok támogatása.
* Támogatja a "Megnyitás a Power BI Desktop" SQL Server (például Azure SQL Database és az Azure SQL Data Warehouse), SQL Server Analysis Services, Azure Storage és a HDFS-források.  
* A Teradata adatforrásokat LDAP-hitelesítés támogatása. Teradata-táblák és nézetek regisztrálásakor a felhasználók eldönthetik, Teradata csatlakozni az LDAP, és TD2 hitelesítést.
* Támogatás a "Megnyitás Excelben" a Teradata adatforrásokat.
* Legutóbbi kifejezést az Azure Data Catalog-portál támogatása. Keresés a portálon, ha felhasználók választhatják a legutóbb használt keresési kifejezéseket felgyorsítása a felfedezési.
* A Teradata adatforrásokat előzetes verziójának támogatása. Teradata-táblák és nézetek regisztrálásakor a felhasználók eldönthetik, pillanatkép bejegyzések mellékelése az adatforrásból kinyert metaadatok.
* Támogatás a "Megnyitás Excelben" Azure SQL Data Warehouse-adatforrásokhoz.
* Támogatás a definiálása és -szerkesztő oszlopszintű sémák manuálisan regisztrált adategységeket. Után manuálisan hoz létre egy adategységet, az Azure Data Catalog-portál használatával, a felhasználók hozzáadhatnak az adategység tulajdonságainak a magyarázat az oszlopokhoz.
* Azure Data Cataloghoz, a regisztrált adategységekből, amely rendelkezik az adott metaadatokat a felderítés engedélyezésére szolgáló keresésekor a delegáltszűrőt "tartalmaz" lekérdezések támogatása. Most már tartalmazza az Azure Data Catalog lekérdezési szintaxis:

| Lekérdezési szintaxis | Cél |
| --- | --- |
| `has:previews` |Megkeresi az adategységek, amelyek tartalmazzák az előzetes |
| `has:documentation` |Megkeresi az adategységek, amelynek dokumentáció lett megadva. |
| `has:tableDataProfiles` |Megkeresi a tábla-szintű adatok profiladatok adategységek |
| `has:columnsDataProfiles` |Az oszlopszintű adatok profiladatok adategységek keresése |


> [!NOTE]
> "A Power BI Desktopban nyissa meg" a Power BI Desktop alkalmazás jelenlegi verziója szükséges. Ha problémák vagy ezzel a funkcióval hibákat tapasztal, gondoskodjon arról, hogy van-e a Power BI Desktop legújabb verzióját [PowerBI.com](https://powerbi.com).


## <a name="whats-new-for-october-2015"></a>2015. október újdonságai
2015. október az Azure Data Cataloghoz az alábbi képességekkel bővült:

* Támogatja az adatok inaktív adatok titkosításához a regisztrált adatforrások előzetes verziók és az adatok profilokat. Az Azure Data Catalog transzparens módon titkosítja az előzetes verzió rekordokat, és adatokat profilok adatforrások regisztrálva a szolgáltatásban, nem kell a kulcskezelést, hiszen a katalógus-rendszergazdák.
* Támogatás a Teradata adatforrásokat. A felhasználók mostantól regisztrálása és Teradata-táblák és nézetek felderítése.
* A helyszíni Hive-adatforrások támogatása. A felhasználók mostantól regisztrálása és Fedezze fel a Hive-táblákat az Apache Hive, a Hadoop helyszíni adatforrásokhoz.
* Az Azure Data Catalog-portál a mentett keresések támogatása. A felhasználók menteni a keresési kifejezéseket és szűrőbeállítások egyszerűen ismételje meg a korábbi kereséseket, és a katalógus tartalmának hasznos nézeteinek definiálásához. Felhasználói is jelölheti meg a mentett keresést, az alapértelmezett keresés. Ha egy felhasználó az Azure Data Catalog portál kezdőlapján, illetve az "első lépések" lap "Nagyító" keresési ikonra kattint, a felhasználó venni közvetlenül a megjelölt alapértelmezett mentett keresés.
* Rich text formátumú szöveg dokumentációját regisztrált adategységeket és tárolók az Azure Data Catalog-portál támogatása. Felhasználók most már megadhat dokumentáció adategységeket, mint például a táblákat, nézeteket és jelentéseket, valamint a tárolókban, mint például az adatbázisok és a modellek forgatókönyvekhez, ahol a címkék és leírások lemezerőforrásai nem elégségesek.
* Manuálisan regisztrálja az ismert adatforrástípusok támogatása. Felhasználó manuálisan adhat meg az Azure Data Catalog portál segítségével az Azure Data Catalog által támogatott összes adatforrástípusok adatforrás adatait.
* Támogatás az Azure Active Directory biztonsági csoportok engedélyezése. Katalógus-rendszergazdák engedélyezheti a biztonsági csoportokat, felhasználói fiókok, ami megkönnyíti az Azure Data Cataloghoz való hozzáférés kezelése a katalógus a hozzáférést.
* Hive-adatforrások megnyitása az Excelben az Azure Data Catalog-portál támogatása.

> [!NOTE]
> A jelenlegi kiadás csak a Teradata TD2 hitelesítést is támogatja. További hitelesítési mechanizmusok támogatottak a jövőbeli kiadások.

> [!NOTE]
> A "Megnyitás Excelben" funkció használatához a Hive-adatforrások, felhasználók telepíteni kell az ODBC-illesztő a Hive.

## <a name="whats-new-for-september-2015"></a>2015. szeptemberi újdonságai
2015. szeptember az Azure Data Cataloghoz az alábbi képességekkel bővült:

* Támogatja az adatok profilokat is, ha a Hive-adatforrások regisztrálása.
* Programozott módon felderítése a katalógus API, ami megkönnyíti az alkalmazások integrálása az Azure Data Catalog támogatása.
* Egy új "első lépések" adatforrás-felderítés tapasztalattal az Azure Data Catalog-portálon. Amikor a felhasználók az Azure Data Catalog portál "felderítése" lapján adja meg a keresett kifejezés beírása nélkül jelenjenek meg ezek a katalógus tartalmának, beleértve a leggyakrabban alkalmazott címkék, szakértők, adatforrástípusok és objektumtípusok áttekintést.
* Regisztráció és az Azure SQL Data Warehouse-objektumok és az adatbázisok felderítéséhez támogatása. További információ az Azure SQL Data Warehouse: [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
* Regisztráció és az SQL Server Analysis Services-modellekhez és tárolókként SQL Server Reporting Services-kiszolgálók felderítéséhez támogatása. Az SSAS és az SSRS-objektumok regisztrálásakor a Azure Data Catalog bejegyzést hoz létre az SSAS-modellhez és az SSRS-kiszolgálóra, és a jelentések és egyéb objektumok. A tárolók könnyen megtalálhatók legyenek, és az Azure Data Catalog-portál használatával. Felhasználók is megkeresheti, és egy modellt vagy a kiszolgáló mellett a Keresés és szűrés a katalógus tartalmának tartalmát.
* Regisztráció és a HTTP/HTTPS-kapcsolaton keresztül az SQL Server Analysis Services-objektumok felderítésével támogatása. Felhasználók most már csatlakoztatható az SSAS-kiszolgálók URL-cím használata (például https://servername/olap/msmdpump.dll) ahelyett, hogy a kiszolgáló neve, és az egyszerű hitelesítés és a Windows-hitelesítésen felül névtelen kapcsolatok. A HTTP/HTTPS-kapcsolatok az SSAS további információkért lásd: [HTTP elérésének konfigurálása az Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
* A HDInsight Hive-adatforrások támogatása. A felhasználók mostantól regisztrálása és Fedezze fel a Hive-táblákat az Apache Hive, a Hadoop on HDInsight az adatforrásokat. A Hive a HDInsight további információkért lásd: a [HDInsight dokumentációs központjába](../hdinsight/hadoop/hdinsight-use-hive.md).
* Regisztrálás és Oracle-adatbázisok és a HDFS-fürtök tárolókként felderítése támogatása. Oracle-táblák és nézetek vagy HDFS regisztrálásakor a Azure Data Catalog bejegyzést hoz létre az adatbázist, táblák és nézetek. Az adatbázis könnyen megtalálhatók legyenek, és az Azure Data Catalog-portál használatával. Felhasználók is megkeresheti, és egy adatbázist vagy a fürt mellett a Keresés és szűrés a katalógus tartalmának tartalmát.
* Ismeretlen adatforrástípusok manuális regisztrálása támogatása. Felhasználók manuális adhat meg az Azure Data Catalog portál használatával, hogy nem kifejezetten az adatforrás-regisztráló eszköz által támogatott adatforrások is feliratozva és felderített adatforrás adatait.
* Regisztráció és tárolókként SQL Server-adatbázisok felderítéséhez támogatása. Ha regisztrálja az SQL Server-táblák és nézetek, az Azure Data Catalog bejegyzést hoz létre az adatbázist, táblák és nézetek. Az adatbázis könnyen megtalálhatók legyenek, és az Azure Data Catalog-portál használatával. A felhasználók is keresés és szűrés mellett a Keresés és szűrés a tartalmát a katalógus-adatbázis tartalmát.

> [!NOTE]
> Az SSAS és az SSRS objektumok regisztrált szeptember 18 kiadása előtt újra regisztrálni kell az adatforrás-regisztráló eszköz használatával a katalógusban a modell vagy a kiszolgáló-bejegyzés hozzáadása előtt. Egy adatforrás regisztrálása nem befolyásolja az Azure Data Catalog-portál a felhasználók által hozzáadott bármely jegyzetek.

> [!NOTE]
> Oracle-táblák és nézetek és a HDFS-fájlok és könyvtárak, amelyeket a. szeptember 11-i kiadás előtti regisztrált újra regisztrálni kell az adatforrás-regisztráló eszköz használata előtt a katalógust az adatbázis vagy a fürt bejegyzéssel bővül. Egy adatforrás regisztrálása nem befolyásolja az Azure Data Catalog-portál a felhasználók által hozzáadott bármely jegyzetek.

> [!NOTE]
> Az SQL Server-táblák és nézetek, amely már regisztrált a szeptember 4 kiadás előtt újra regisztrálni kell az adatforrás-regisztráló eszköz használatával, az adatbázis-bejegyzést a katalógushoz való hozzáadása előtt. Egy adatforrás regisztrálása nem befolyásolja az Azure Data Catalog-portál a felhasználók által hozzáadott bármely jegyzetek.

## <a name="whats-new-for-august-2015"></a>2015. augusztus újdonságai
2015 augusztusi állapot az Azure Data Cataloghoz az alábbi képességekkel bővült:

* Az SQL Server és Oracle-adatforrások profilkészítési adatok támogatása. Az SQL Server és az Oracle-táblák és nézetek regisztrálásakor a felhasználók eldönthetik, a regisztrálandó objektumok adatok profil információval. A data objektumszintű és oszlopszintű statisztikai foglal.
* Hadoop a HDFS-adatforrások támogatása. A felhasználók mostantól regisztrálása és Fedezze fel a HDFS-fájlok és könyvtárak.
* Hozzáférési kérelem részleteit a regisztrált adatforrások biztosítása támogatása. A regisztrált adatvagyont felhasználók most már megadhat egyszerűen integrálható a meglévő eszközökkel és folyamatokkal való hozzáférés kérelmezésének, beleértve az e-mailek hivatkozásokat vagy URL-címek, utasításokat.
* A címkék és a szakértők, hogy egyszerűbb legyen a felhasználók milyen metaadatok megadott regisztrált adategységek felderítése elemleírásokat.
* A felső navigációs sávban hozzáadtunk egy új "User" gombra, és a menüből. Ebben a menüben lehetővé teszi, hogy a felhasználó, tekintse meg az Azure Data Cataloghoz való bejelentkezéshez használt fiók, és jelentkezzen ki, ha szükség. Ebből a menüből is a katalógus neve, amely az Azure Data Catalog REST API használatával a fejlesztők számára a legértékesebb jeleníti meg.
* Csak a Standard Edition: Tulajdonosok adategységekhez hozzáadásakor az Azure Data Catalog mostantól egyaránt támogatja felhasználói fiókok és biztonsági csoportok tulajdonosként. A kijelölt adategységek tulajdonosként hozzáadni egy biztonsági csoportot, megadhatja a csoport megjelenítendő nevét vagy a csoport egyszerű Felhasználónevet e-mail-címét, ha rendelkezik ilyennel.
* Azure Blob Storage-adatforrások támogatása. A felhasználók mostantól regisztrálása és Fedezze fel az Azure Storage-blobokat és könyvtárak.

