---
title: What's New in Azure Data Catalog |} Microsoft Docs
description: "Ez a cikk egy áttekintést nyújt az Azure Data Catalog hozzáadott új funkciók."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/13/2017
ms.author: maroche
ms.openlocfilehash: 2839c265596d98834fb4071dea1684d5a6669907
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="whats-new-in-azure-data-catalog"></a>What's new in Azure Data Catalog
Szoftverfrissítések **Azure Data Catalog** rendszeresen kiadásakor. Nem minden kiadás szolgáltatásai új felhasználók számára is elérhető, néhány kiadásokban a háttér-szolgáltatás jellemzőinek lévőként. Ezen a lapon az új felhasználók számára is elérhető képességbeli az Azure Data Catalog szolgáltatás mutatja be.

## <a name="whats-new-for-november-2017"></a>Új 2017. November 
2017. November a következő képességekkel rendelkezik lett hozzáadva Azure Data Catalog:

* Közvetlenül az adott üzleti szószedet feltételeit a Data Catalog-portál összekapcsolása támogatása. A felhasználók az üzleti szószedet hivatkozások másolja és a beágyazása dokumentumok, e-mailek, jelentéseket vagy egyéb helyek közvetlenül kapcsolódik a szószedet a kifejezés meghatározása.
* Támogatás az Azure Active Directory szolgáltatás rendszerbiztonsági tagok. Katalógus-rendszergazdák adhatják meg a katalógus eléréséhez használt szolgáltatásnevekről ügyfélalkalmazások, és ezek az alkalmazások adott engedélyek megadásához, mint azok engedélyeket adhat a felhasználók és biztonsági csoportok. További információ: [alkalmazás és szolgáltatás egyszerű objektumok az Azure Active Directoryban](../active-directory/develop/active-directory-application-objects.md).
* Támogatás az Azure Active Directory-hitelesítéshez az Azure SQL Database és az Azure SQL Data Warehouse adatforrások a Data Catalog adatforrás-regisztráló eszköz használatával történő csatlakozás során. További információ: [Azure Active Directory hitelesítés használata a hitelesítés és az SQL Database vagy az SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).


## <a name="whats-new-for-september-2017"></a>Új 2017. szeptember 
2017. szeptember a következő képességekkel rendelkeznek lett hozzáadva Azure Data Catalog:

* Kibontása támogatása csatlakoztassa a kapcsolat metaadatok DB2 adatforrásokból az adatforrás-regisztráló eszköz használatával, a kapcsolódó táblák regisztrálásakor.
* Támogatás a MongoDB verzió 3.4 adatforrások regisztrálása az adatforrás-regisztráló eszköz használatával.
* Egyetlen művelettel tárolt objektumok összes metaadatai törlése, ha egy adatbázist, vagy más tároló eltávolítása a Data Catalog támogatása.
* Támogatási meg, legfeljebb 1000 címkéket, az üzleti szószedet feltételek vagy az egyéb keresési facets, ha a Keresés a Data Catalog-portál szűkebbé tenni.


## <a name="whats-new-for-august-2017"></a>Új 2017. augusztus 
Augusztus 2017 a következő képességekkel rendelkeznek lett hozzáadva Azure Data Catalog:

*   Egy új fejlesztői minta létrehozásával és kezelésével kapcsolatban metaadatok a Data Catalog REST API használatával érhető el. A *kapcsolatadatokat importálnia kell a Data Catalog* minta érhető el a [Data Catalog minták kódlap](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0). 
* Kibontása támogatása csatlakoztassa a kapcsolat metaadatai Teradata-adatforrásokat a kapcsolódó táblák használata az adatforrás-regisztráló eszköz regisztrálásakor.
* Támogatás az SQL Server táblázat értékű függvényt (TVF) objektumokhoz, ha regisztrálja az SQL Server-adatforrásait az adatforrás-regisztráló eszköz használatával.
* Több frissítések és a teljesítményük és használhatóságuk a Data Catalog-portál növeléséhez finomításokra.

## <a name="whats-new-for-july-2017"></a>Új 2017. július 
2017. júliusi a következő képességekkel rendelkeznek lett hozzáadva Azure Data Catalog:
*   Részletesebben vezérelhető, beleértve lehetővé teszik metaadat-művelet keresztül támogatása:
    - Katalógus-rendszergazdák is a felhasználók korlátozása abban címkék és a katalógushoz kapcsolódó metaadatok közre a katalógus csak olvasható hozzáférést tesz lehetővé.
    - Katalógus-rendszergazdák is a felhasználók korlátozása abban az új adatforrások regisztrálása a katalógusban.
    - Katalógus-rendszergazdák korlátozhatja a felhasználók általi saját tulajdonba vétele adatok eszköz metaadatait a katalógusban.
    - Hozzárendelhető Azure Active Directory biztonsági csoportok és felhasználók számára a engedélyek kezelésének megkönnyítése érdekében.
* Regisztrált adategységeket és a Data Catalog-portál felderítő kapcsolódó adatok eszközök közötti kapcsolatok támogatása többek között:
    - Kapcsolat metaadatok kivonása (beleértve az Azure SQL Database) SQL Server, az Oracle és MySQL az adatforrás a Data Catalog adatforrás-regisztráló eszköz használatakor.
    - A Data Catalog-portált az eszköz metaadatok megtekintésekor kapcsolódó adatok eszközök felderítése.
    - Műveletek megadásához, valamint kezelésében a Data Catalog REST API használatával adatok eszközök közötti kapcsolatokat.

További engedélyek a Data Catalog kezeléséről további információkért lásd: [biztonságossá tétele a hozzáférést a data catalog és adategységeket](data-catalog-how-to-secure-catalog.md).
A Data Catalog kapcsolatoknál további részletekért lásd: [kapcsolódó adatok eszközök megtekintése az Azure Data Catalog](data-catalog-how-to-view-related-data-assets.md).

## <a name="whats-new-for-june-2017"></a>2017. júniusi Újdonságok 
2017. június a következő képességekkel rendelkeznek lett hozzáadva Azure Data Catalog:
*   Támogatja a Sybase, Apache Cassandra és MongoDB adatforrásokat. A felhasználók most regisztrálni és Cassandra és MongoDB adatbázisok és táblák és Sybase-adatbázisok, táblák és nézetek felderítése.

> [!NOTE]
> Például rögzíti és tömböket összetett adattípusú oszlopokat tartalmazó táblák MongoDB és Cassandra regisztrálásakor ezekben az oszlopokban nem szerepelni fog a Data Catalog hozzá metaadatokat.

## <a name="whats-new-for-may-2017"></a>Előfordulhat, hogy 2017 újdonságai 
Előfordulhat, hogy 2017 a következő képességekkel rendelkeznek lett hozzáadva Azure Data Catalog:
*   Egy új fejlesztői minta az üzleti szószedet feltételek tömeges importálásához érhető el. A tömeges importálással szószedet minta érhető el a [Data Catalog fejlesztői minták lap](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples). 
*   Támogatási ODBC kapcsolati információit az Azure Data Catalog-portál szerkesztésre. Adatok eszköz tulajdonosai és a Data Catalog-rendszergazdák mostantól szerkesztheti a regisztrált ODBC-adatforrás-kapcsolódási információt anélkül, hogy regisztrálja újra az adatforrásokat.
*   Az üzleti szószedet és kattintható URL-címek támogatása. Amikor URL-címek üzleti szószedet a feltételek a leírást és a definíciós tulajdonságai szerepelnek, az URL-címeket a Data Catalog-portál hivatkozásként jelenik meg.
*   Támogatás a szakértői e-mail címek mellett szakértői nevének megjelenítése. A Data Catalog-portál szakértők egy adategységet tulajdonságainak megtekintésekor a szakértő teljes nevét az Azure Active Directoryból az elemleírás jelenik meg.

## <a name="whats-new-for-april-2017"></a>Új 2017. április 
2017. április a következő képességekkel rendelkeznek lett hozzáadva Azure Data Catalog:
*   ODBC adatforrások támogatása. A felhasználók most regisztrálni és ODBC adatbázisok, táblák és nézetek használatával a Data Catalog adatforrás-regisztráló eszköz felderítése.

## <a name="whats-new-for-march-2017"></a>Új március 2017 
2017. március a következő képességekkel rendelkezik lett hozzáadva Azure Data Catalog:
*   AAD-biztonságicsoportok használata a Data Catalog rendszergazdák támogatása.
*   Az Azure Data Catalog mostantól egy új Azure-régiót. Az ügyfelek építhető ki az Azure Data Catalog régióban nyugati középső Régiójában, USA keleti régiója, USA nyugati régiója, Nyugat-Európában, és Kelet-Ausztrália, Észak-Európában és Délkelet-Ázsia mellett. További információkért lásd: [Azure-régiókat](https://azure.microsoft.com/regions/).

## <a name="whats-new-for-february-2017"></a>Új 2017. február 
2017. február a következő képességekkel rendelkeznek lett hozzáadva Azure Data Catalog:
*   A Data Catalog adatforrás-regisztráló eszköz a Speciális beállítások támogatása. Felhasználók parancs időkorlátja értékeket adhat meg, amikor nagy adatforrások regisztrálása.
*   FTP-HELYET és PostgreSQL adatforrások támogatása. A felhasználók most már regisztrálása és az FTP-fájlok és könyvtárak, és PostgreSQL adatbázisok, táblák és nézetek felderítése.

## <a name="whats-new-for-january-2017"></a>Új 2017. január 
2017. január a következő képességekkel rendelkeznek lett hozzáadva Azure Data Catalog:
*   Az Azure Data Catalog mostantól [CSA csillag](https://www.microsoft.com/trustcenter/compliance/csa-star-certification) kompatibilis.
*   Integráció a [beszerzése és az Excel 2016 és a Power Query az Excel alakítás](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605). Excel felhasználók lekérdezések megosztása és Fedezze fel az Azure Data Catalog használatával lekérdezéseket az Excel belül. Ez a funkció a Power BI Pro-licenccel rendelkező felhasználók számára érhető el.

## <a name="whats-new-for-december-2016"></a>Újdonságok 2016. December
Azure Data Catalog 2016. December, a következő képességekkel bővült:
*   Az Azure Data Catalog mostantól [HIPAA](https://www.microsoft.com/trustcenter/Compliance/HIPAA) és [EU Modellcikkelyeknek](https://www.microsoft.com/TrustCenter/Compliance/EU-Model-Clauses) kompatibilis.
*   Támogatási szerkesztését az adatforrás kapcsolódási adatait. Adatok eszköz tulajdonosai és a Data Catalog-rendszergazdák mostantól szerkesztheti a regisztrált adatforrás-kapcsolódási információt anélkül, hogy regisztrálja újra az adatforrásokat.
*   Támogatás a Salesforce.com-adatforrásokhoz. A felhasználók most regisztrálni és Salesforce objektumok felderítése.


## <a name="whats-new-for-november-2016"></a>Újdonságok 2016. November
Azure Data Catalog November 2016, az alábbi képességeket bővült:
*   Az Azure Data Catalog mostantól [ISO/IEC 27001](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001) és [ISO/IEC 27018](https://www.microsoft.com/TrustCenter/Compliance/iso-iec-27018) kompatibilis.
*   A Data Catalog-portált és a REST API használatával ODBC adatforrások manuális regisztrációja támogatása.

## <a name="whats-new-for-september-2016"></a>2016 szeptemberétől újdonságai
2016 szeptemberétől az alábbi képességeket Azure Data Catalog bővült:

* IBM DB2 adatforrások támogatása. A felhasználók most regisztrálni és DB2-adatbázisok, a táblák és nézetek felderítése.
* Támogatás az Azure Cosmos DB-adatforrásokhoz. A felhasználók most regisztrálni és Cosmos DB adatbázisok és gyűjteményeket.
* A katalógusnév a Data Catalog-portál testreszabása támogatása. Katalógus-rendszergazdák mostantól biztosíthat a szöveg, például a szervezet nevét a portál elnevezéseként jelenik meg.

## <a name="whats-new-for-august-2016"></a>2016 augusztusától újdonságai
2016 augusztusától az alábbi képességeket Azure Data Catalog bővült:

* Az SQL Server Master Data Services (MDS) adatforrások regisztrálási fejlesztései. Felhasználók is között már elérhető az előzetes verziójú funkciók és az adatok profilok MDS entitások használatával a Data Catalog adatforrás-regisztráló eszköz regisztrálásakor.
* Rendszergazda által meghatározott szervezeti mentett keresések támogatása. Ha menteni a Keresés a Data Catalog-portált, a Data Catalog rendszergazdák választhat a személyes használatra, vagy az összes katalógus felhasználónak keresések mentése. Szervezeti mentett keresések senki katalógus vannak megosztva, és a szabványos kezdőpontja biztosíthat az adatforrás-felderítés.
* A Data Catalog-portál a tulajdonságok nézet frissítése. Minden eszköz adattulajdonságainak most jelenik meg, és egyetlen, méretezhető panelen adjon meg egy egységesebb, valamint felderíthető élmény felügyelni.

## <a name="whats-new-for-july-2016"></a>Újdonságok 2016. július
2016. július az alábbi képességeket Azure Data Catalog bővült:

* SQL Server Master Data Services (MDS) adatforrások támogatása. A felhasználók most regisztrálni és Fedezze fel az MDS-modellek és entitások.
* Támogatás az SQL Server tárolt eljárást. A felhasználók most már regisztrálása és az SQL Server-adatforrásait tárolt eljárás objektumok felderítése.
* Az Azure Data Catalog-portál és az adatforrás-regisztráló eszköz, 18 támogatott nyelvek összesen további nyelvek támogatásával. Az Azure Data Catalog felhasználói élmény honosítva van, a nyelvi beállítások a Windows vagy a webböngészőben.
* Frissítések és a Data Catalog portál kezdőlapján, beleértve a teljesítménnyel kapcsolatos fejlesztések és zökkenőmentes felhasználói élmény finomítása.

## <a name="whats-new-for-june-2016"></a>2016. júniusi Újdonságok
2016. június az alábbi képességeket Azure Data Catalog bővült:

* A lista nézet oszlopainak átméretezéséhez, amikor adategységek kereséséhez a Data Catalog-portál a támogatási szolgálathoz. Mostantól a felhasználók átméretezhetik egyedi oszlop könnyebben olvasható hosszadalmas eszköz metaadatokat, például címkék és leírásokat.
* A Power Query az Excel programhoz fel lett véve a "Megnyitás" a Data Catalog-portál menüjében. Felhasználók megnyithatja a támogatott adatforrások Excel 2016 vagy az Excel 2010 és az Excel 2013-as verziójának a [Power Query az Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) bővítményt.
* Támogatás az Azure Table Storage adatforrások. A felhasználók most regisztrálása és az Azure Storage adatforrások tábla objektumok felderítése.

## <a name="whats-new-for-may-2016"></a>Újdonságok 2016. május
Előfordulhat, hogy 2016 az alábbi képességeket Azure Data Catalog bővült:

* Egy üzleti szószedetet, amely lehetővé teszi, hogy a katalógus-rendszergazdák üzleti feltételeket és a hierarchiák egy közös üzleti szószedet létrehozásához. Felhasználók címkézheti használatával könnyebben megtalálhatóvá és értelmezhetővé a katalógus tartalma szószedet kifejezéseivel jelölhetik regisztrált adategységeket. További információk: [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Az irányított címkézéshez használt üzleti szószedet összeállítása)  
* A Data Catalog üzleti szószedet, amely lehetővé teszi a felhasználók egy művelettel több Szójegyzék frissítése fejlesztései. Felhasználók választhatják ki több feltételeket, hogy módosítsa a következő mezőket:
  * Szülő kifejezés: A felhasználó kiválaszthatja a kívánt új szülő kifejezést, és az összes kiválasztott feltételek frissítve lett, hogy a kijelölt szülő kifejezés gyermeke lehet. Ha a kijelölt feltételek összes azonos a szülővel rendelkezik, majd, hogy a szülő is megjelennek a TextBox elemhez, ellenkező esetben a szülő kifejezés mező értéke üres.   
  * Címkék és az érdekelt felekkel: felhasználók hozzáadása és távolítsa el a címkéket és az érdekelt felek több szószedet a feltételek a felhasználói élmény használatával egyszerre több adategységet címkézést.

> [!NOTE]
> Az üzleti szószedet csak a Standard Edition Azure Data Catalog érhető el. Az ingyenes kiadás nem biztosít az irányadó címkézéssel, illetve egy üzleti szószedetet képességeit.

## <a name="whats-new-for-march-2016"></a>Újdonságok 2016. március
2016. március, az alábbi képességekkel bővült az Azure Data Catalog: g:

* Egy összevont REST API-végpont a keresési képességek és a katalógus eszköz felügyeleti képességek az Azure Data Catalog szolgáltatás programozott módon eléréséhez. A keresési API-végpont és a katalógus API-végpont lett elavult, és nem támogatja a 2016. március 21-én. Az API-t szemantikáját nem módosulnak. Csak a végpont URI azonosító módosította. További információkért lásd: a [Azure Data Catalog REST API-referencia](https://msdn.microsoft.com/library/azure/mt267595.aspx). API-mintákat, lásd: [Azure Data Catalog fejlesztői minták](data-catalog-samples.md).

## <a name="whats-new-for-february-2016"></a>Újdonságok 2016. február
2016. február az alábbi képességekkel bővült Azure Data CATALOG:

* Egy újonnan áttervezett forrás kiválasztása az Azure Data Catalog adatforrás-regisztráló eszköz a felhasználói élmény. Az adatforrás-regisztráló eszköz használatával könnyebben keresse meg és jelölje ki az adatforrástól érkező értesítésektől Azure Data Catalog által támogatott frissítve lett.
* Az Azure Data Catalog-portál és az adatforrás-regisztráló eszköz 10 további nyelvek támogatásával. Az angoltól az Azure Data Catalog élmény már elérhető a német, spanyol, francia, olasz, japán, koreai, brazíliai portugál, orosz, egyszerűsített és hagyományos kínai. Az Azure Data Catalog felhasználói élmény honosítva van, a nyelvi beállítások a Windows vagy a felhasználó böngészőben.
* Az Azure Data Catalog adatok az üzleti folytonossági és vészhelyreállítási helyreállítási georeplikáció támogatása. Az összes Azure Data Catalog tartalmát, adatok forrás metaadatok és a közösségi jegyzeteket, beleértve az ügyfél számára további költségek nélkül két Azure-régiók közötti most replikálódnak. Az Azure-régiók előre van párosítva, legalább 500 miles egymástól, és kövesse a leképezés a [üzleti folytonossági és vészhelyreállítási helyreállítási (BCDR): Azure-régiókat párosítva](../best-practices-availability-paired-regions.md).
* Támogatás az Azure Data Catalog által használt Azure-előfizetés módosítása. Az Azure Data Catalog rendszergazdák jelölje be egy másik Azure-előfizetés más célra használhatja az Azure Data Catalog-portál beállításait tartalmazó oldalt.

## <a name="whats-new-for-january-2016"></a>Újdonságok 2016. január
2016. január az alábbi képességeket Azure Data Catalog bővült:

* Manuálisan regisztrálja a további adatforrások támogatása. Most "Manuális bejegyzés létrehozása" az Azure Data Catalog-portált használja, vagy az Azure Data Catalog REST API használatával a következő adatforrások regisztrálása:
  * Az OData - függvény, Entitáskészletet és Entitástároló
  * HTTP - fájl, a végpont, a jelentés és a hely
  * Fájlrendszer - fájl
  * SharePoint - lista
  * FTP - fájlokra és könyvtárakra
  * A Salesforce.com - objektum
  * DB2 - tábla, nézet és adatbázis
  * PostgreSQL - tábla, nézet és adatbázis
* "Nyissa meg az SQL Server Data Tools" támogatása (beleértve az Azure SQL Database és Azure SQL Data Warehouse) SQL Server-adatforrásait.  
* Regisztrálja, és felderíti az SAP HANA-nézetek és csomagok támogatását. Az Azure Data Catalog adatok adatforrás-regisztráló eszközzel és megjegyzésekkel és az Azure Data Catalog-portál használatával regisztrált SAP HANA adatforrások felfedezése használatával SAP HANA-adatforrások regisztrálása
* PIN-kód és az Azure Data Catalog portálon adategységeket rögzítésének lehetősége. Ha szeretné, PIN-kód adategységeket a könnyebb újbóli felderítése, és újra felhasználhatja.
* Egy újonnan újratervezett kezdőlap az Azure Data Catalog-portálon. Az új kezdőlapja a jelenlegi felhasználók tevékenység - nemrégiben publikált eszközök, a rögzített eszközök, és a mentett keresések - és a tevékenység betekintést beleértve a katalógus egész betekintést tartalmazza.
* Az Azure Data Catalog portálon állandó felhasználói beállítások támogatása. Felhasználói élményt befolyásoló beállításait – beleértve a rács vagy Mozaik elrendezés nézetben, a számú eredményt, és nyomja le a be- és kikapcsolása kiemelés oldalankénti - megmaradnak a felhasználói munkamenetek között.
* Az Azure Data Catalog már elérhető két új Azure-régió. Az ügyfelek az Azure Data Catalog régiókban Észak-Európában és Délkelet-Ázsiában, mellett USA keleti régiója, USA nyugati régiója, Nyugat-Európában, Kelet-Ausztrália hozhat létre. További információkért lásd: [Azure-régiókat](https://azure.microsoft.com/regions/).

> [!NOTE]
> "Nyissa meg az SQL Server Data Tools" van szükség a Visual Studio 2013 Update 4 és az SQL Server tooling eszköz telepíthető. SQL Server Data Tools legújabb verziójának telepítéséhez látogasson el a [letöltése SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).


## <a name="whats-new-for-december-2015"></a>2015. decemberi újdonságai
Azure Data Catalog 2015 decemberében a következő lehetőségekkel bővült:

* Azure SQL Data Warehouse-adatforrásokhoz tartozó adatok profilok támogatása. Amikor regisztrál az Azure SQL Data Warehouse-táblák és nézetek, a felhasználók eldönthetik, szerepeltetni kívánt adatok profil metrikákat a metaadatokat az adatforrás-e.
* Regisztrálja, és felderíti az MySQL objektumok és adatbázisok támogatása. A felhasználók regisztrálhatják MySQL adatforrásokból az Azure Data Catalog adatok adatforrás-regisztráló eszközzel és megjegyzésekkel és az Azure Data Catalog-portál használatával regisztrált MySQL adatforrások felderítésére.
* A Teradata-adatforrásokhoz SPNEGO és Windows hitelesítés támogatása. Teradata-táblák és nézetek regisztrálásakor felhasználók maguk dönthetik teradata rendszerhez való kapcsolódáshoz SPNEGO és a Windows, és az LDAP és a TD2 hitelesítést használ.
* Azure Data Lake Store adatforrások támogatása. A felhasználók most regisztrálni és Azure Data Catalog használatával az Azure Data Lake Store-adatforrások felderítését.
* Az Azure Data Catalog adatforrás-regisztráló eszköz manuális megadása hálózati proxybeállításai támogatása. Felhasználók "Módosítás proxybeállítások" az eszköz üdvözlőoldal választja ki, és adhatja meg a proxykiszolgáló címét és az eszköz által használni kívánt portot.

## <a name="whats-new-for-november-2015"></a>Újdonságok a 2015. November
2015. November a következő képességekkel rendelkeznek lett hozzáadva Azure Data Catalog:

* Megtekintése és másolása az Azure Data Catalog portálon találja meg a kapcsolati karakterláncok (beleértve az Azure SQL Database) SQL Server és Oracle-adatforrások lehetősége. Felhasználók rákattinthatnak a "Nézet kapcsolati karakterláncok" hivatkozásra az SQL Server- vagy Oracle tábla, nézet vagy adatbázist, tekintse meg a kapcsolati karakterláncokkal az adatforráshoz való kapcsolódáshoz használt kapcsolódási információt. SQL Server-adatforrásait ADO.NET, ODBC, OLEDB és JDBC kapcsolati karakterláncok célokat szolgálnak. ODBC és OLEDB kapcsolati karakterláncok Oracle-adatforrások célokat szolgálnak.
* Támogatja az adatok profilok például Teradata-táblák és nézetek regisztrálásakor.
* Támogatása "Nyissa meg a Power BI Desktop" SQL Server (beleértve az Azure SQL Database és Azure SQL Data Warehouse), az SQL Server Analysis Services, Azure Storage és HDFS források.  
* A Teradata-adatforrásokhoz LDAP-hitelesítés támogatása. Teradata-táblák és nézetek regisztrálásakor felhasználók eldönthetik, teradata rendszerhez való kapcsolódáshoz használja az LDAP, és TD2 hitelesítést.
* "Megnyitás az Excel" támogatása a Teradata-adatforrásokhoz.
* Az Azure Data Catalog-portál legutóbbi kifejezést támogatása. A portál keresésekor felhasználók annak érdekében, a felderítés élmény legutóbb használt keresőkifejezéseket is kijelölhet.
* Előzetes Teradata adatforrások támogatása. Teradata-táblák és nézetek regisztrálásakor a felhasználók eldönthetik, szerepeltetni kívánt pillanatkép rögzíti a metaadatokat az adatforrás-e.
* Az Azure SQL Data Warehouse adatforrásokat "Megnyitás az Excel" támogatása.
* Támogatja a meghatározása, és manuálisan regisztrált adategységeket oszlopszintű sémái szerkesztése. Után manuálisan hoz létre egy adategységet, az Azure Data Catalog-portál használatával, a felhasználók hozzáadhatnak oszlopdefiníciók az adatok eszköz tulajdonságai.
* Támogatás az Azure Data Catalog, bizonyos metaadatait megkapnák regisztrált adategységeket feltárásának engedélyezéséhez keresésekor "tartalmaz" lekérdezések. Most már tartalmazza az Azure Data Catalog-lekérdezés szintaxisa:

| Lekérdezés szintaxisa | Cél |
| --- | --- |
| `has:previews` |Megkeresi a adategységeiről, amelyeket az előképet |
| `has:documentation` |Megkeresi az adategységek, amelynek dokumentáció adtak meg |
| `has:tableDataProfiles` |Megkeresi a tábla szintű adatok profiladatok az adategységeket |
| `has:columnsDataProfiles` |Megkeresi az adategységeket oszlopszintű adatok profiladatok |


> [!NOTE]
> "Megnyitás Power BI Desktop" telepíteni kell a Power BI Desktop alkalmazás legújabb verziója szükséges. Hogy a felmerülő problémákat vagy hibákat ezzel a szolgáltatással, gondoskodjon arról, hogy rendelkezik-e a legújabb verzióját a Power BI Desktop [powerbi.com webhelyen](https://powerbi.com).


## <a name="whats-new-for-october-2015"></a>Újdonságok a 2015 október
2015 október a következő képességekkel rendelkeznek lett hozzáadva Azure Data Catalog:

* Az előzetes verziójú funkciók és az adatok profilokat regisztrált adatforrások támogatják az adatok titkosítását. Az Azure Data Catalog transzparens módon titkosítja az előzetes rekordok, és adatokat az adatforrások regisztrálva a szolgáltatásban, nem kell a katalógus-rendszergazdák által kulcskezeléshez profilok.
* Támogatás a Teradata-adatforrásokhoz. A felhasználók most regisztrálni és Teradata-táblák és nézetek felderítése.
* A helyszíni Hive adatforrások támogatása. A felhasználók most regisztrálni és Hive táblák felderíteni az Apache Hadoop a helyszíni adatforrások Hive.
* Az Azure Data Catalog-portál a mentett keresések támogatása. A felhasználók mentése a keresési feltételek és szűrésére beállításokat egyszerűen ismételje meg az előző keresések, és a katalógus tartalma hasznos nézeteinek definiálásához. Felhasználó, az alapértelmezett keresési is jelölheti meg a mentett kereséseket. Ha a felhasználó az Azure Data Catalog portál kezdőlapján, illetve a "első lépések" lap "Nagyító" keresési ikonra kattint, a felhasználó végrehajtott közvetlenül a mentett keresés megjelölve alapértelmezettként.
* Rich text formátumú dokumentáció regisztrált adategységeket és az Azure Data Catalog-portál a tárolók támogatása. Felhasználók mostantól biztosíthat dokumentáció adatok erőforrásokat, például a táblákat, nézeteket és jelentéseket, valamint a tárolókhoz, például adatbázisok és -modellek, forgatókönyvek, amelyben címkék és leírásait is nem megfelelő.
* Manuálisan regisztrálja az ismert adatforrásokat támogatása. Felhasználó manuálisan adhat meg az adatforrásra vonatkozó információ az Azure Data Catalog-portált használja az Azure Data Catalog által támogatott összes adatforrásokat.
* Támogatás, amelyek engedélyezik az Azure Active Directory biztonsági csoportokat. Katalógus-rendszergazdák engedélyezheti a katalógus biztonsági csoportokat, felhasználói fiókokat, így könnyebben Azure Data Catalog való hozzáférés kezelése a hozzáférést.
* Hive adatforrások megnyitni az Excel programban az Azure Data Catalog portálról támogatása.

> [!NOTE]
> A jelenlegi kiadásban csak a Teradata TD2 hitelesítést is támogatja. Feloldja a további hitelesítési mechanizmusok támogatottak a jövőben.

> [!NOTE]
> A "Megnyitás az Excel" szolgáltatás használatához a Hive-adatforrásokhoz, felhasználók telepítve kell lennie az ODBC-illesztő a Hive.

## <a name="whats-new-for-september-2015"></a>Újdonságok a 2015. szeptember
2015. szeptember a következő képességekkel rendelkeznek lett hozzáadva Azure Data Catalog:

* Hive-adatforrás regisztrálásakor, többek között adatok profilok támogatása.
* Programozott módon felderítéséhez a katalógus alkalmazásprogramozási felület, megkönnyíti az alkalmazások integrálása az Azure Data Catalog támogatása.
* Egy új "első lépések" adatforrás-felderítés az Azure Data Catalog-portál felhasználói élménye. Amikor a felhasználók a keresett kifejezés beírása nélkül adja meg a "felderítése" lapjáról, az Azure Data Catalog-portált, jelenjenek meg ezek a katalógus tartalma, többek között a leggyakrabban használt címkék, szakértők, adatforrásokat és objektumtípusok áttekintést.
* Regisztrálja, és felderíti az Azure SQL Data Warehouse-objektumok és adatbázisok támogatása. További információ az Azure SQL Data Warehouse: [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
* Regisztrálja, és felderíti az SQL Server Analysis Services-modellek és tárolóként SQL Server Reporting Services-kiszolgálók támogatása. Az SSAS és az SSRS objektumok regisztrálásakor Azure Data Catalog bejegyzést hoz létre az SSAS modell és az SSRS-kiszolgáló, és a jelentések és egyéb objektumok. A tárolók könnyen megtalálhatók legyenek, és jegyzetelve, az Azure Data Catalog-portál használatával. Felhasználók keresése és egy modell vagy a kiszolgálót a Keresés és szűrés a katalógus tartalma felül tartalmát is.
* Regisztráció és a HTTP/HTTPS kapcsolaton az SQL Server Analysis Services felderítéséhez támogatása. Felhasználók SSAS kiszolgálók ahelyett, hogy a kiszolgáló nevét (például https://servername/olap/msmdpump.dll) URL-cím használatával is csatlakozhat, és az egyszerű hitelesítés és a Windows-hitelesítés mellett anonim felhasználói kapcsolatok. A HTTP/HTTPS-kapcsolatok az SSAS további információkért lásd: [HTTP elérésének konfigurálása az Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
* A HDInsight Hive adatforrások támogatása. A felhasználók most már regisztrálása és az Apache Hive az adatforrásokban HDInsight Hadoop Hive táblák felderíteni. A HDInsight Hive további információkért tekintse meg a [HDInsight dokumentációs központban](../hdinsight/hadoop/hdinsight-use-hive.md).
* Regisztrálja, és felderíti az Oracle-adatbázis és a HDFS-fürt tárolóként támogatása. Oracle-táblák és nézetek vagy HDFS regisztrálásakor Azure Data Catalog bejegyzést hoz létre az adatbázist, táblák és nézetek. Az adatbázis könnyen megtalálhatók legyenek, és jegyzetelve, az Azure Data Catalog-portál használatával. Felhasználók keresése és egy adatbázis vagy a fürt mellett a Keresés és szűrés a katalógus tartalma tartalmát is.
* Manuálisan regisztrálja a ismeretlen adatforrásokat támogatása. Felhasználó manuálisan adhat meg az adatforrásra vonatkozó információ az Azure Data Catalog-portált használja, úgy, hogy nem kifejezetten az adatforrás-regisztráló eszköz által támogatott adatforrások lehet feliratozva és felderített.
* Regisztrálja és SQL Server-adatbázisok mint tárolók felderítésével támogatása. SQL Server-táblák és nézetek regisztrálásakor Azure Data Catalog bejegyzést hoz létre az adatbázist, táblák és nézetek. Az adatbázis könnyen megtalálhatók legyenek, és jegyzetelve, az Azure Data Catalog-portál használatával. A felhasználók is keresni és a Keresés és szűrés a katalógus tartalma felül adatbázis tartalmát.

> [!NOTE]
> Az SSAS és az SSRS objektumok regisztrált a 18. szeptemberi kiadás előtti újra regisztrálni kell az adatforrás-regisztráló eszköz használata előtt a modell vagy a kiszolgáló bejegyzés kerül a katalógusba. Újra az adatforrás regisztrálása az Azure Data Catalog portálon a felhasználók által hozzáadott bármely jegyzetek nincs hatással.

> [!NOTE]
> Oracle-táblák és nézetek és HDFS fájlok és könyvtárak, amelyek a 11. szeptemberi kiadás előtti regisztrált lettek újra regisztrálni kell az adatforrás-regisztráló eszköz használatával, a katalógust az adatbázis vagy a fürt bejegyzés hozzáadása előtt. Újra az adatforrás regisztrálása az Azure Data Catalog portálon a felhasználók által hozzáadott bármely jegyzetek nincs hatással.

> [!NOTE]
> SQL Server-táblák és nézetek, a 4. szeptemberi kiadás előtti regisztrált is újra regisztrálni kell az adatforrás-regisztráló eszköz használatával, a katalógust az adatbázis-bejegyzés hozzáadása előtt. Újra az adatforrás regisztrálása az Azure Data Catalog portálon a felhasználók által hozzáadott bármely jegyzetek nincs hatással.

## <a name="whats-new-for-august-2015"></a>Újdonságok a 2015. augusztus
2015. augusztus a következő képességekkel rendelkeznek lett hozzáadva Azure Data Catalog:

* Az SQL Server és Oracle-adatforrások profilkészítési adatok támogatása. SQL Server és Oracle-táblák és nézetek regisztrálásakor felhasználók eldönthetik, adatok profil információval regisztrált objektumokra vonatkozóan. Az adatok profil objektumszintű és oszlopszintű statisztikai adatokat tartalmaz.
* Hadoop HDFS adatforrások támogatása. A felhasználók most regisztrálni és HDFS fájlok és könyvtárak felderítése.
* Hozzáférési kérelem részleteit regisztrált adatforrások biztosító támogatása. Bármely regisztrált eszköz a felhasználók mostantól elérhető utasításokat hozzáférés kérelmezésének, beleértve az e-mailek hivatkozásokat vagy URL-címek, könnyen integrálható a meglévő eszközök és folyamatok.
* Eszköztipp címkék és szakértők könnyebb felderítéséhez, a felhasználók milyen milyen metaadatok megadott regisztrált adategységeket.
* A felső navigációs sáv vettünk egy új "User" és a menüből. Ebben a menüben lehetővé teszi, hogy a felhasználó, tekintse meg az Azure Data Catalog bejelentkezéshez használandó fiókot, és jelentkezzen ki, ha a szükséges. Ebben a menüben jelennek meg azok a katalógus nevét, amely az Azure Data Catalog REST API használatával a fejlesztők számára.
* Csak Standard Edition: Tulajdonosok adategységhez hozzáadásakor Azure Data Catalog mostantól támogatja felhasználói fiókok és a biztonsági csoportok tulajdonos. Biztonsági csoport hozzáadása az adategységek tulajdonos, megadhatja a csoporthoz tartozó megjelenített név vagy a csoport UPN e-mail címet, ha van ilyen.
* Támogatás az Azure Blob Storage-adatforrásokhoz. A felhasználók most regisztrálni és Fedezze fel az Azure Storage blobs és könyvtárakat.

