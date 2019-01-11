---
title: Az Azure SQL Data Warehouse kibocsátási megjegyzések a 2018 október |} A Microsoft Docs
description: Kibocsátási megjegyzések az Azure SQL Data warehouse-hoz.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/04/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 9160a5f4e3a452682787ff500199e43e7fad0c77
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213689"
---
# <a name="whats-new-in-azure-sql-data-warehouse-october-2018"></a>Mi az új Azure SQL Data warehouse? 2018. október
Az Azure SQL Data Warehouse fejlesztései folyamatosan kap. Ez a cikk ismerteti az új szolgáltatásokat és változásokat tartalmazza, amelyek 2018. október.

## <a name="devops-for-data-warehousing"></a>DevOps-Adattárházak
A magas kért funkció az SQL Data warehouse-hoz (az SQL DW) már az SQL Server Data eszköz (SSDT) a Visual Studióban támogatása előzetes verzióban érhető el! Fejlesztői csapatok egy egyetlen, a verzió által szabályozott kódbázis keresztül most megosszanak és a gyors üzembe helyezése a módosításokat a világ tetszőleges példányra. Csatlakozás szeretne? Ez a funkció érhető el előzetes verzióra még ma! Funkcionáló regisztrálhatja a [SQL adatok adatraktár Visual Studio SQL Server Data Tools (SSDT) - előzetes regisztrációs űrlap](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u). Adja meg a nagy kereslet, hogy kezelt ügyfeleink számára a legjobb élmény biztosítása az előzetes verzió elfogadását. A regisztrációt követően az a célunk az az állapot megerősítéséhez hét munkanapon belül.

## <a name="row-level-security-generally-available"></a>Sorszintű biztonság általánosan elérhető
Az Azure SQL Data Warehouse (az SQL DW) mostantól támogatja a sorszintű biztonság (RLS) hozzáadása egy hatékony képesség, a bizalmas adatok biztonságossá tételéhez. Rls-t érintő biztonsági házirendek, ki férhet hozzá sorokat a mint a sort a táblában való hozzáférésének valósíthat meg. Az RLS lehetővé teszi, hogy a hozzáférés részletes szabályozása az adattárház átírása nélkül. Rls-t, a hozzáférés korlátozási logika megtalálható egyszerűbbé teszi az általános biztonsági modellt az adatbázisszinten magát, nem pedig egy másik alkalmazás adatait erről. Rls-t is szükségtelenné teszi a hozzáférés-vezérlés kezelését a sorok kiszűréséhez nézetek bevezetni. Nincs minden ügyfél számára a nagyvállalati szintű biztonsági funkció további költség nélkül.

## <a name="advanced-advisors"></a>Speciális tanácsadók
Speciális hangolási Azure SQL Data warehouse-hoz (az SQL DW) csak van további data warehouse javaslatok és a metrikák egyszerűbb. Nincsenek további speciális teljesítménnyel kapcsolatos javaslatok Azure advisorral a rendelkezésére, beleértve:
1.  Az adaptív gyorsítótár – felhasználóitevékenység-Ha a méretezési csoport gyorsítótár-kihasználtság optimalizálása érdekében.
2.  Táblaelosztással – hogy mikor replikálja a táblák adatáthelyezés csökkentheti és növelheti a számítási feladatok teljesítményére. 
3.  A TempDB – megértése, ha csökkenteni a versengést a tempdb osztályokat és erőforrás konfigurálása.

Van egy ugyanakkor szorosabb integrációt, az adatraktárak mérőszámait [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) többek között egy továbbfejlesztett testre szabható figyelés diagramra a közel valós idejű metrikák áttekintés paneljén. Ezentúl nem kell elhagynia az adattárház áttekintő paneljét ahhoz, hogy lássa az Azure Monitor metrikáit a használat monitorozásakor, illetve az adattárházra vonatkozó ajánlások ellenőrzésekor és alkalmazásakor. Emellett nincsenek új metrikák elérhető, például a TempDB-adatbázisának és adaptív gyorsítótár-kihasználtság kiegészíteni, a teljesítménnyel kapcsolatos javaslatok.

## <a name="advanced-tuning-with-integrated-advisors"></a>Speciális integrált tanácsadók finomhangolás
Speciális hangolási Azure SQL Data warehouse-hoz (az SQL DW) csak van további data warehouse javaslatok és metrikákkal és a portál áttekintése panel, amely az integrált megoldást nyújt az Azure Advisor és az Azure Monitor újratervezése egyszerűbb.

## <a name="accelerated-database-recovery-adr"></a>Gyorsított adatbázis-helyreállítás (ADR)
Az Azure SQL Data Warehouse a gyorsított adatbázis helyreállítási (ADR) már nyilvános előzetes verzióban érhető el. Automatikus központi telepítési szabály egy új SQL Server-motor, amely jelentősen csökkenti az adatbázis-elérhetőségi, különösen a hosszú ideig futó tranzakció, a jelenléte szerint teljesen újratervezése másolatot a jelenlegi helyreállítási folyamatot az alapoktól. Automatikus központi telepítési szabály elsődleges előnyei a következők: a gyors és következetes adatbázis-helyreállítás és azonnali tranzakció visszaállítása.

## <a name="azure-monitor-diagnostics-logs"></a>Az Azure Monitor-diagnosztikai naplók
Az SQL Data warehouse-ba (az SQL DW) most már lehetővé teszi a bővített elemzéseket kaphat elemzési számítási feladatok közvetlenül az Azure Monitor-diagnosztikai naplók integrálásával. Ez a lehetőség lehetővé teszi a fejlesztők számára, hogy a lekérdezés-optimalizálási vagy a kapacitás felügyeleti elemzi a számítási feladatok viselkedését egy hosszú időn keresztül és megalapozottabb döntéseket hozhat. Most már bevezettük a egy külső naplózási folyamatot [diagnosztikai naplók az Azure Monitor](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs) , amely az adatraktár-számítási feladat további betekintést nyújtson. Egyetlen kattintással gomb, most már áll konfigurálhatja a diagnosztikai naplók hibaelhárítási lehetőségek használatával előzmény lekérdezési teljesítményre [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries). Az Azure Monitor-diagnosztikai naplók testre szabható megőrzési időtartamú támogatási menti a naplókat a tárfiókhoz tartozó naplózási célokra, teszi, hogy a naplók streamelése az event hubs segítségével közel valós idejű telemetriai insights és a Log Analytics használatával naplók elemzéséhez napló lekérdezésekkel. A diagnosztikai naplók az adattárház olyan telemetriai nézeteiből állnak, amelyek egyenértékűek az SQL Data Warehouse teljesítményproblémáinak elhárításához leggyakrabban használt DMV lekérdezésekkel. A kezdeti kiadás engedélyeztük a következő rendszer dinamikus felügyeleti nézetek nézeteket:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)

## <a name="columnstore-memory-management"></a>Oszloptár memóriájának kezelése
Tömörített oszlop store sorcsoportok száma növekszik, a memóriára van szükség ezen naplóbájtot belső oszlop szegmens metaadatainak kezelése növekszik.  Ennek eredményeképpen a lekérdezési teljesítmény és az egyes Oszlopcentrikus dinamikus felügyeleti nézetekkel (DMV-kkel) ellen végrehajtott lekérdezések csökkentheti.  Fejlesztések ezekben az esetekben jobb kiszolgálást és teljesítményt az ilyen lekérdezések vezető belső metaadatait méretének optimalizálása érdekében ebben a kiadásban hajtott végre. 

## <a name="azure-data-lake-storage-gen2-integration-ga"></a>Az Azure Data Lake Storage Gen2-integráció (elérhetővé tétel GA)
Az Azure SQL Data Warehouse (az SQL DW) natív integráció az Azure Data Lake Storage Gen2 most már rendelkezik. Ügyfeleink most már betöltheti az adatokat az SQL DW ABFS külső táblák használatával. Ez a funkció lehetővé teszi, hogy az ügyfelek számára, hogy a data Lake tárolók a Data Lake Storage Gen2 integrálása. 

## <a name="bug-fixes"></a>Hibajavítások

| Beosztás | Leírás |
|:---|:---|
| **CETAS DW2000 és a Data warehouse-adattárházak kis erőforrásosztályok a parquet vagy egyéb hibák** | A javítás megfelelően azonosítja a Parquet kódelérési út a létrehozása külső táblát, a NULL értékű hivatkozás. |
|**Azonosító oszlop értékét előfordulhat, hogy elvesznek a CTAS egy másik művelet** | Az identitás oszlop értéke lehet, hogy nem megőrzi a CTASed egy másik táblába. Jelentett blog: [ https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/ ](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/). |
| **Bizonyos esetekben, amikor a munkamenet meg lett szakítva egy lekérdezés futása közben belső hiba** | A javítás eseményindítók egy InvalidOperationException, ha a munkamenet meg lett szakítva, amikor a lekérdezés futtatása. |
| **(Telepített 2018. November) Ügyfelek is tapasztalják optimálisnál teljesítmény, több kis méretű fájlt (Gen1) ADLS-ből a Polybase betöltése közben.** | A rendszer teljesítménye volt bottlenecked AAD biztonsági jogkivonat érvényesítése során. Teljesítménybeli problémák voltak hivatottak engedélyezése a biztonsági jogkivonatok gyorsítótárazását. |


## <a name="next-steps"></a>További lépések
Most, hogy jobban megismerte az SQL Data warehouse, megtudhatja, hogyan lehet gyorsan [hozzon létre egy SQL Data Warehouse][create a SQL Data Warehouse]. Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét][Azure glossary], amikor az új fogalmakkal ismerkedik. Vagy tekintsen meg néhányat a többi SQL Data Warehouse-erőforrás közül.  

* [Ügyfelek sikertörténetei]
* [Blogok]
* [Funkciókérések]
* [Videók]
* [Az ügyféltanácsadói csapat blogjai]
* [Stack Overflow-fórum]
* [Twitter]


[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Az ügyféltanácsadói csapat blogjai]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Ügyfelek sikertörténetei]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-fórum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
