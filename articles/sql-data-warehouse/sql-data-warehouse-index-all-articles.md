---
title: Az SQL Data Warehouse szolgáltatást minden témakörök |} Microsoft Docs
description: Az Azure-szolgáltatás az összes témakörök tábla nevű SQL Data Warehouse a http://azure.microsoft.com/documentation/articles/, címét és leírását.
services: sql-data-warehouse
documentationcenter: ''
author: barbkess
manager: jhubbard
editor: ''
ms.assetid: a26a6dec-9c08-4415-8f58-4ee1dd41f718
ms.service: sql-data-warehouse
ms.workload: sql-data-warehouse
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: reference
ms.date: 03/30/2017
ms.author: barbkess
ms.openlocfilehash: 9fe41f12960dc099700e01573b4f03ebf63f8749
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678426"
---
# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Az Azure SQL Data Warehouse szolgáltatást minden kapcsolatos témakörök
Ez a témakör felsorolja minden témakörnek a közvetlenül a **SQL Data Warehouse** Azure szolgáltatást. A weblap kulcsszavak használatával kereshet **Ctrl + F**, az aktuális érdeklő témakörök kereséséhez.

## <a name="new"></a>Új
| &nbsp; | Beosztás | Leírás |
| ---:|:--- |:--- |
| 1 |[Az SQL Data Warehouse biztonsági mentések](sql-data-warehouse-backups.md) |Ismerje meg az SQL Data Warehouse beépített adatbázis biztonsági mentését, amelyek segítségével állíthatja vissza az Azure SQL Data Warehouse visszaállítási pont vagy egy másik földrajzi régióban. |

## <a name="updated-articles-sql-data-warehouse"></a>Frissített cikkeket, az SQL Data Warehouse
Ez a rész felsorolja cikkei, amelyek frissültek, ahol a frissítési nagy vagy jelentős volt-e. Minden egyes frissített cikk egy durva szövegrészletet a hozzáadott markdown jelenik meg. A cikkek frissültek dátum közötti tartományba **2016-08-22** való **2016-10-05**.

| &nbsp; | Cikk | Frissített szöveg, a részlet | Ha frissítése |
| ---:|:--- |:--- |:--- |
| 2 |[Adatok betöltése az Azure blob storage az SQL Data warehouse-ba (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |/- To track bytes and files SELECT  r.command,  s.request_id,  r.status,  count(distinct input_name) as nbr_files,  sum(s.bytes_processed)/1024/1024 as gb_processed FROM  sys.dm_pdw_exec_requests r  inner join sys.dm_pdw_dms_external_work s  on r.request_id = s.request_id WHERE  r. címke = "CTAS: terhelés cso. DimProduct "OR r. címke = "CTAS: terhelés cso. FactOnlineSales' GROUP BY r.command, s.request_id, r.status ORDER BY nbr_files desc, gb_processed desc; |2016-09-07 |
| 3 |[Az SQL Data Warehouse visszaállítása](sql-data-warehouse-restore-database-overview.md) |** Állíthatók vissza egy felfüggesztett data warehouse? ** szüneteltetett adatraktár visszaállításához először újra elérhetővé kell. Az adatraktár újra online állapotba kerül, követően választható visszaállítási pontok hét nap. ** Állítsa vissza a georedundáns régió ** a georedundáns tárolás használata visszaállíthatja az adatraktár az párosított adatközpont más földrajzi régióban. Az adatraktár az utolsó napi biztonsági másolatból állítottak vissza. ** Visszaállítása ütemterv ** visszaállíthatja egy adatbázist a helyreállítási pont az utóbbi hét napban. A pillanatképek négy és nyolc óránként start és hét napja. Ha pillanatkép régebbi, mint hét nap, jár le, és a helyreállítási pont már nem érhető el. ** Visszaállítása költségek ** a tárolási költség a visszaállított adatraktár a prémium szintű Azure Storage díj terheli. A visszaállított adatok adatraktár szünetelteti, ha van szó, a prémium szintű Azure Storage díj tárolására. Felfüggesztés előnye nem kell fizetni |2016-09-29 |

## <a name="get-started"></a>Bevezetés
| &nbsp; | Beosztás | Leírás |
| ---:|:--- |:--- |
| 4 |[Hitelesítés az Azure SQL Warehouse-szal](sql-data-warehouse-authentication.md) |Az Azure Active Directory (AAD) és az SQL Server hitelesítés az Azure SQL Data Warehouse. |
| 5 |[Ajánlott eljárások az Azure SQL Data Warehouse-hoz](sql-data-warehouse-best-practices.md) |Javaslatok és ajánlott eljárások, amelyeket érdemes tudni az Azure SQL Data Warehouse-megoldások fejlesztésekor. Ezek segítségével a megoldások jobban sikerülhetnek. |
| 6 |[Az Azure SQL Data Warehouse illesztőprogramok](sql-data-warehouse-connection-strings.md) |A kapcsolati karakterláncokat és az SQL Data Warehouse illesztőprogramok |
| 7 |[Csatlakozás az Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md) |Az Azure SQL Data Warehouse kiszolgálónevének és kapcsolati karakterláncának lekérdezése |
| 8 |[Azure Machine Learning adatok elemzése](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) |Az Azure Machine Learning segítségével létrehozhat egy prediktív gépi tanulási modellt, amely az Azure SQL Data Warehouse-ban tárolt adatokon alapul. |
| 9 |[Lekérdezés Azure SQL Data Warehouse (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) |Az Azure SQL Data Warehouse lekérdezése az sqlcmd parancssori segédprogram használatával. |
| 10 |[Az SQL Data Warehouse-adatbázis létrehozása a Transact-SQL (TSQL) használatával](sql-data-warehouse-get-started-create-database-tsql.md) |Tudnivalók Azure SQL Data Warehouse a TSQL használatával történő létrehozásáról |
| 11 |[Az SQL Data Warehouse egy támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md) |Támogatási jegy létrehozása az SQL Data Warehouse-hoz. |
| 12 |[Adatok betöltése az Azure Data Factoryvel](sql-data-warehouse-get-started-load-with-azure-data-factory.md) |Sajátítsa el az adatok betöltését az Azure Data Factoryvel |
| 13 |[Adatok betöltése a PolyBase az SQL Data Warehouse](sql-data-warehouse-get-started-load-with-polybase.md) |Megismerheti a PolyBase-t és az adatraktározási forgatókönyvekben való használatát. |
| 14 |[Egy Azure SQL Data Warehouse létrehozása](sql-data-warehouse-get-started-provision.md) |Tudnivalók Azure SQL Data Warehouse létrehozásáról az Azure Portalon |
| 15 |[PowerShell-lel SQL Data Warehouse létrehozása](sql-data-warehouse-get-started-provision-powershell.md) |SQL Data Warehouse létrehozása PowerShell használatával |
| 16 |[Adatok megjelenítése Power BI használatával](sql-data-warehouse-get-started-visualize-with-power-bi.md) |SQL-adatraktár adatainak megjelenítése Power BI használatával |
| 17 |[Az Azure SQL Data Warehouse lekérdezése (Visual Studio)](sql-data-warehouse-query-visual-studio.md) |Az SQL Data Warehouse lekérdezése a Visual Studióval. |

## <a name="develop"></a>Fejlesztés
| &nbsp; | Beosztás | Leírás |
| ---:|:--- |:--- |
| 18 |[Az SQL Data Warehouse tranzakciók optimalizálása](sql-data-warehouse-develop-best-practices-transactions.md) |Az Azure SQL Data Warehouse hatékony tranzakció frissítések írásáról bevált gyakorlatokat tartalmazó útmutatóval |
| 19 |[Párhuzamossági és munkaterhelés-kezelés az SQL Data Warehouse](sql-data-warehouse-develop-concurrency.md) |Ismerje meg a feldolgozási és munkaterhelés-kezelés az Azure SQL Data Warehouse adattárházzal történő, megoldások. |
| 20 |[Select (CTAS) tábla az SQL Data Warehouse létrehozása](sql-data-warehouse-develop-ctas.md) |Tippek a létrehozás táblával kódolás szerint select (CTAS) utasítás az Azure SQL Data Warehouse adattárházzal történő, megoldások. |
| 21 |[Az SQL Data Warehouse dinamikus SQL](sql-data-warehouse-develop-dynamic-sql.md) |Tippek az Azure SQL Data Warehouse adattárházzal történő, megoldások dinamikus SQL használatát. |
| 22 |[Az SQL Data Warehouse beállítások szerint kell csoportosítani](sql-data-warehouse-develop-group-by-options.md) |Ötletek a csoport beállításai az Azure SQL Data Warehouse adattárházzal történő, megoldások. |
| 23 |[Az eszköz-lekérdezésekre címkék használata az SQL Data Warehouse](sql-data-warehouse-develop-label.md) |Tippek az eszköz lekérdezések címkék használata az Azure SQL Data Warehouse adattárházzal történő, megoldások. |
| 24 |[Az SQL Data Warehouse hurkok](sql-data-warehouse-develop-loops.md) |Tippek a Transact-SQL hurkok és az Azure SQL Data Warehouse adattárházzal történő, megoldások tagjára kurzorok. |
| 25 |[Az SQL Data Warehouse tárolt eljárások](sql-data-warehouse-develop-stored-procedures.md) |Tippek a tárolt eljárások végrehajtása az Azure SQL Data Warehouse adattárházzal történő, megoldások. |
| 26 |[Az SQL Data Warehouse-tranzakciók](sql-data-warehouse-develop-transactions.md) |Ötletek a tranzakciók az Azure SQL Data Warehouse adattárházzal történő, megoldások. |
| 27 |[Az SQL Data Warehouse felhasználói sémák](sql-data-warehouse-develop-user-defined-schemas.md) |Tippek a Transact-SQL-sémák használata az Azure SQL Data Warehouse adattárházzal történő, megoldások. |
| 28 |[Rendelje hozzá az SQL Data Warehouse változók](sql-data-warehouse-develop-variable-assignment.md) |Tippek a Transact-SQL változókat az Azure SQL Data Warehouse adattárházzal történő, megoldások hozzárendelése. |
| 29 |[Az SQL Data Warehouse-nézetek](sql-data-warehouse-develop-views.md) |Tippek a Transact-SQL-nézetek használata az Azure SQL Data Warehouse adattárházzal történő, megoldások. |
| 30 |[Tervezési döntések és az SQL Data Warehouse kódolási eljárások](sql-data-warehouse-overview-develop.md) |Fejlesztői fogalmak, tervezési döntéseit, javaslatok és az SQL Data Warehouse kódolási eljárások. |

## <a name="manage"></a>Kezelés
| &nbsp; | Beosztás | Leírás |
| ---:|:--- |:--- |
| 31 |[Kezelheti a számítási teljesítményt az Azure SQL Data Warehouse (áttekintés)](sql-data-warehouse-manage-compute-overview.md) |Az Azure SQL Data Warehouse képességek kibővítési teljesítményét. Horizontális felskálázás dwu-k módosításával vagy és sablonok felfüggesztése és folytatása a számítási erőforrásokat költségek csökkentése érdekében. |
| 32 |[Kezelheti a számítási teljesítményt az Azure SQL Data Warehouse (Azure-portál)](sql-data-warehouse-manage-compute-portal.md) |Az Azure portál feladatok kezelésére számítási teljesítményt. Skála dwu-k beállításával számítási erőforrásokat. Vagy, és sablonok felfüggesztése és folytatása a számítási erőforrásokat költségek csökkentése érdekében. |
| 33 |[Az Azure SQL Data Warehouse (PowerShell) a számítási teljesítmény kezelése](sql-data-warehouse-manage-compute-powershell.md) |PowerShell-feladatok kezelésére számítási teljesítményt. Skála dwu-k beállításával számítási erőforrásokat. Vagy, és sablonok felfüggesztése és folytatása a számítási erőforrásokat költségek csökkentése érdekében. |
| 34 |[Számítási teljesítményt az Azure SQL Data Warehouse (REST) kezelése](sql-data-warehouse-manage-compute-rest-api.md) |PowerShell-feladatok kezelésére számítási teljesítményt. Skála dwu-k beállításával számítási erőforrásokat. Vagy, és sablonok felfüggesztése és folytatása a számítási erőforrásokat költségek csökkentése érdekében. |
| 35 |[Számítási teljesítményt az Azure SQL Data Warehouse (T-SQL) kezelése](sql-data-warehouse-manage-compute-tsql.md) |Transact-SQL (T-SQL) feladatok kibővített teljesítmény dwu-k beállításával. Költségeket takaríthat vissza csúcsidőszakon kívüli időszakokban. |
| 36 |[számítási feladatok DMV-vel végzett megfigyelésével](sql-data-warehouse-manage-monitor.md) |Útmutató: a dinamikus felügyeleti nézetek használatával számítási feladat figyeléséhez. |
| 37 |[Az Azure SQL Data Warehouse adatbázisok kezelése](sql-data-warehouse-overview-manage.md) |SQL Data Warehouse-adatbázisokban kezelésének áttekintése. Felügyeleti eszközök, a dwu-k és kibővített teljesítmény, lekérdezési teljesítményt, a helyes biztonsági házirendek létrehozása, és egy adatbázis visszaállításához adatsérülés akár regionális kimaradás hibaelhárítási tartalmazza. |
| 38 |[Az Azure SQL Data Warehouse felhasználói lekérdezések figyelése](sql-data-warehouse-overview-manage-user-queries.md) |A szempontok, ajánlott eljárások és a feladatokat az Azure SQL Data Warehouse felhasználói lekérdezések Figyelés áttekintése |
| 39 |[Az SQL Data Warehouse visszaállítása](sql-data-warehouse-restore-database-overview.md) |Az Azure SQL Data Warehouse adatbázis helyreállítása adatbázis visszaállítási lehetőségek áttekintése. |
| 40 |[Állítsa vissza az Azure SQL Data Warehouse (portál)](sql-data-warehouse-restore-database-portal.md) |Az Azure portál feladatok Azure SQL Data Warehouse visszaállítására. |
| 41 |[Állítsa vissza az Azure SQL Data Warehouse (PowerShell)](sql-data-warehouse-restore-database-powershell.md) |PowerShell feladatok Azure SQL Data Warehouse visszaállítására. |
| 42 |[Állítsa vissza az Azure SQL Data Warehouse (REST API-t)](sql-data-warehouse-restore-database-rest-api.md) |Azure SQL Data Warehouse visszaállítására feladatok REST API-t. |

## <a name="tables-and-indexes"></a>Táblák és indexek
| &nbsp; | Beosztás | Leírás |
| ---:|:--- |:--- |
| 43 |[Az SQL Data Warehouse táblák adattípusok](sql-data-warehouse-tables-data-types.md) |Ismerkedés az Azure SQL Data Warehouse tábláiba adattípusokkal. |
| 44 |[Az SQL Data Warehouse táblák terjesztése](sql-data-warehouse-tables-distribute.md) |Első lépések az Azure SQL Data Warehouse táblák terjesztése. |
| 45 |[Az SQL Data Warehouse táblák indexelő](sql-data-warehouse-tables-index.md) |Ismerkedés az Azure SQL Data Warehouse indexelő táblával. |
| 46 |[Az SQL Data Warehouse táblák áttekintése](sql-data-warehouse-tables-overview.md) |Ismerkedés az Azure SQL Data Warehouse tábláiba. |
| 47 |[Az SQL Data Warehouse Táblák particionálása](sql-data-warehouse-tables-partition.md) |Első lépések az Azure SQL Data Warehouse táblaparticionálást. |
| 48 |[Az SQL Data Warehouse táblák statisztikák kezelése](sql-data-warehouse-tables-statistics.md) |Első lépések az Azure SQL Data Warehouse táblákon statisztika. |
| 49 |[Az SQL Data Warehouse az ideiglenes táblák](sql-data-warehouse-tables-temporary.md) |Ismerkedés az Azure SQL Data Warehouse ideiglenes táblákat. |

## <a name="integrate"></a>Integrálás
| &nbsp; | Beosztás | Leírás |
| ---:|:--- |:--- |
| 50 |[Az Azure Data Factory használja az SQL Data Warehouse szolgáltatással](sql-data-warehouse-integrate-azure-data-factory.md) |Tippek az Azure Data Factory (ADF) használata az Azure SQL Data Warehouse adattárházzal történő, megoldások. |
| 51 |[Az Azure gépi tanulás használja az SQL Data Warehouse szolgáltatással](sql-data-warehouse-integrate-azure-machine-learning.md) |Oktatóanyag az Azure Machine Learning Azure SQL Data Warehouse adattárházzal történő, megoldások fejlesztésére irányuló használatához. |
| 52 |[Az Azure Stream Analytics használja az SQL Data Warehouse szolgáltatással](sql-data-warehouse-integrate-azure-stream-analytics.md) |Tippek az Azure SQL Data Warehouse adattárházzal történő, megoldások Azure Stream Analytics használ. |
| 53 |[A Power BI használja az SQL Data Warehouse szolgáltatással](sql-data-warehouse-integrate-power-bi.md) |Tippek a Power BI használatával az Azure SQL Data Warehouse adattárházzal történő, megoldások. |
| 54 |[Használja ki az egyéb szolgáltatásokat az SQL Data Warehouse szolgáltatással](sql-data-warehouse-overview-integrate.md) |Eszközök és a partnerek, amelyekbe beépül az SQL Data Warehouse-megoldás. |

## <a name="load"></a>Betöltés
| &nbsp; | Beosztás | Leírás |
| ---:|:--- |:--- |
| 55 |[Adatok betöltése az Azure blob storage az Azure SQL Data Warehouse (Azure Data Factory)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) |Sajátítsa el az adatok betöltését az Azure Data Factoryvel |
| 56 |[Adatok betöltése az Azure blob storage az SQL Data warehouse-ba (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |Útmutató az Azure blob storage adatok betöltése az SQL Data Warehouse PolyBase segítségével. A nyilvános adatok a Contoso kereskedelmi adatraktár sémába néhány táblák betöltése. |
| 57 |[Adatok betöltése az SQL Serverről az Azure SQL Data Warehouse-(ba AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) |A bcp segítségével exportál adatokat az SQL Serverről egybesimított fájlokba, az AzCopy segítségével importál adatokat az Azure Blob Storage-ban, és a PolyBase használatával viszi be az adatokat az SQL Data Warehouse-ba. |
| 58 |[Adatok betöltése az SQL Serverről az Azure SQL Data Warehouse (egybesimított fájlok)](sql-data-warehouse-load-from-sql-server-with-bcp.md) |Kisebb adatméret esetében a bcp segítségével exportál adatokat az SQL Serverről egybesimított fájlokba, majd közvetlenül az SQL Data Warehouse-ba importálja őket. |
| 59 |[Adatok betöltése az SQL Server be Azure SQL Data Warehouse (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) |Bemutatja, hogyan tárolt adatok mozgatása az adatforrások széles SQL-adatraktár SQL Server Integration Services (SSIS) csomag létrehozásához. |
| 60 |[Adatok betöltése a PolyBase az SQL Data Warehouse](sql-data-warehouse-load-from-sql-server-with-polybase.md) |A bcp segítségével exportál adatokat az SQL Serverről egybesimított fájlokba, az AzCopy segítségével importál adatokat az Azure Blob Storage-ban, és a PolyBase használatával viszi be az adatokat az SQL Data Warehouse-ba. |
| 61 |[Útmutató az SQL Data Warehouse PolyBase használatával](sql-data-warehouse-load-polybase-guide.md) |Irányelvek és javaslatok a PolyBase az SQL Data Warehouse forgatókönyvekben. |
| 62 |[Mintaadatok betöltése az SQL Data Warehouse-ba](sql-data-warehouse-load-sample-databases.md) |Mintaadatok betöltése az SQL Data Warehouse adatbázisba |
| 63 |[Adatok betöltése a bcp használatával](sql-data-warehouse-load-with-bcp.md) |Megismerheti a bcp-t és az adatraktározási forgatókönyvekben való használatát. |
| 64 |[Adatok betöltése az Azure SQL Data Warehouse-ba](sql-data-warehouse-overview-load.md) |Ismerje meg, hogy az adatok betöltése az SQL Data Warehouse gyakori forgatókönyvei. Ezek közé tartozik a PolyBase, az Azure blob Storage tárolóban, egybesimított fájlokba és lemez szállítási használatával. Külső eszközöket használhatja. |

## <a name="migrate"></a>Migrate (Áttelepítés)
| &nbsp; | Beosztás | Leírás |
| ---:|:--- |:--- |
| 65 |[Az SQL Data Warehouse az SQL-kódot áttelepítése](sql-data-warehouse-migrate-code.md) |Tippek az SQL-kódot az Azure SQL Data Warehouse adattárházzal történő, megoldások történő áttelepítéséhez. |
| 66 |[Adatok áttelepítése](sql-data-warehouse-migrate-data.md) |Tippek az adatok áttelepítése az Azure SQL Data Warehouse adattárházzal történő, megoldások. |
| 67 |[Data Warehouse Fájláttelepítő segédprogram (előzetes verzió)](sql-data-warehouse-migrate-migration-utility.md) |Az SQL Data Warehouse telepíthet át. |
| 68 |[A séma áttelepítése az SQL Data Warehouse](sql-data-warehouse-migrate-schema.md) |Tippek a séma áttelepítése az Azure SQL Data Warehouse adattárházzal történő, megoldások. |
| 69 |[Megoldás áttelepítése az SQL Data Warehouse-ba](sql-data-warehouse-overview-migrate.md) |Áttelepítési útmutató az Azure SQL Data Warehouse platform, hogy a megoldás. |

## <a name="partners"></a>Partnerek
| &nbsp; | Beosztás | Leírás |
| ---:|:--- |:--- |
| 70 |[Az SQL Data Warehouse üzleti intelligencia partnerek](sql-data-warehouse-partner-business-intelligence.md) |A külső üzleti intelligencia partnerek, amely támogatja az SQL Data Warehouse-megoldás listáit. |
| 71 |[Az SQL Data Warehouse-adatok integrációs partnerek](sql-data-warehouse-partner-data-integration.md) |Külső partnerek adatok integrációs megoldásokkal, amelyek támogatják az Azure SQL Data Warehouse listája. |
| 72 |[Az SQL Data Warehouse-adatok felügyeleti partnerek](sql-data-warehouse-partner-data-management.md) |Külső adatok felügyeleti partnerek, amely támogatja az SQL Data Warehouse-megoldás listája. |

## <a name="reference"></a>Leírások
| &nbsp; | Beosztás | Leírás |
| ---:|:--- |:--- |
| 73 |[Az SQL Data Warehouse referencia-témakörei](sql-data-warehouse-overview-reference.md) |Az SQL Data Warehouse referencia tartalom hivatkozások. |
| 74 |[PowerShell-parancsmagok és a REST API-k, az SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md) |A felső PowerShell-parancsmagok az Azure SQL Data Warehouse figyeléséről, valamint szüneteltetéséről és folytatásáról adatbázis található. |
| 75 |[Nyelvi elemei](sql-data-warehouse-reference-tsql-language-elements.md) |Az SQL Data Warehouse használt Transact-SQL nyelvi elem esetén referenciatartalmat listája. |
| 76 |[Transact-SQL topics](sql-data-warehouse-reference-tsql-statements.md) |A Transact-SQL témaköröket, használja az SQL Data Warehouse referenciatartalmat mutató hivatkozásokat. |
| 77 |[Rendszernézetek](sql-data-warehouse-reference-tsql-system-views.md) |Rendszer mutató hivatkozásokat tartalmaz az SQL Data Warehouse megtekinti a tartalmat. |

## <a name="security"></a>Biztonság
| &nbsp; | Beosztás | Leírás |
| ---:|:--- |:--- |
| 78 |[Az SQL Data Warehouse - a régebbi típusú ügyfeleknek naplózási és dinamikus Adatmaszkolási támogatása](sql-data-warehouse-auditing-downlevel-clients.md) |További tudnivalók az SQL Data Warehouse a régebbi típusú ügyfelek támogatása adatok naplózása |
| 79 |[Az Azure SQL Data Warehouse naplózás](sql-data-warehouse-auditing-overview.md) |Ismerkedés az Azure SQL Data Warehouse naplózás |
| 80 |[Az átlátszó adatok titkosítás (TDE) az SQL Data Warehouse első lépései](sql-data-warehouse-encryption-tde.md) |Az SQL Data Warehouse átlátható adattitkosítás (TDE) |
| 81 |[Ismerkedés a transzparens adatok titkosítás (TDE)](sql-data-warehouse-encryption-tde-tsql.md) |Az SQL Data Warehouse (T-SQL) átlátható adattitkosítás (TDE) |
| 82 |[Az SQL Data Warehouse adatbázis védelme](sql-data-warehouse-overview-manage-security.md) |Tippek az Azure SQL Data Warehouse adatbázis védelme adattárházzal történő, megoldások. |

## <a name="miscellaneous"></a>Egyéb rendelkezések
| &nbsp; | Beosztás | Leírás |
| ---:|:--- |:--- |
| 83 |[A Visual Studio és az SSDT telepítése SQL Data Warehouse](sql-data-warehouse-install-visual-studio.md) |A Visual Studio és az SQL Server Development Tools (SSDT) telepítése az Azure SQL Data Warehouse-hoz |
| 84 |[Prémium szintű Storage adatainak áttelepítése](sql-data-warehouse-migrate-to-premium-storage.md) |Prémium szintű storage történő áttelepítéséhez egy meglévő SQL-adatraktár |
| 85 |[A fenyegetésészlelés az első lépései](sql-data-warehouse-security-threat-detection.md) |A Fenyegetésészlelés az első lépések |
| 86 |[Az SQL Data Warehouse kapacitáskorlátait](sql-data-warehouse-service-capacity-limits.md) |A kapcsolatok, adatbázisok, táblák és az SQL Data Warehouse lekérdezések maximális értékeket. |
| 87 |[Azure SQL Data Warehouse hibaelhárítása](sql-data-warehouse-troubleshoot.md) |Hibaelhárítás az Azure SQL Data Warehouse. |

