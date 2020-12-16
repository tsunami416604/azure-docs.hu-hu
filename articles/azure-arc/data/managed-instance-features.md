---
title: Az Azure arc használatára képes SQL felügyelt példány funkciói és képességei
description: Az Azure arc használatára képes SQL felügyelt példány funkciói és képességei
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c42acb69e13cc1eb0fbba3fcafaec1451bc4d77
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589219"
---
# <a name="features-and-capabilities-of-azure-arc-enabled-sql-managed-instance"></a>Az Azure arc használatára képes SQL felügyelt példány funkciói és képességei

Az Azure arc használatára képes SQL felügyelt példány közös programkódot oszt meg a SQL Server legújabb stabil verziójával. A szabványos SQL-nyelv, a lekérdezés-feldolgozás és az adatbázis-kezelési funkciók többsége azonos. A SQL Server és SQL Database, illetve az SQL felügyelt példányai között gyakori funkciók a következők:

- Nyelvi funkciók – [a flow nyelvi kulcsszavainak, a](/sql/t-sql/language-elements/control-of-flow) [kurzorok](/sql/t-sql/language-elements/cursors-transact-sql), [az adattípusok](/sql/t-sql/data-types/data-types-transact-sql), a [DML-utasítások](/sql/t-sql/queries/queries), [predikátumok](/sql/t-sql/queries/predicates), [sorszámok](/sql/relational-databases/sequence-numbers/sequence-numbers), [tárolt eljárások](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)és [változók](/sql/t-sql/language-elements/variables-transact-sql)vezérlése.
- Adatbázis-funkciók [– automatikus hangolás (tervezési kényszer)](/sql/relational-databases/automatic-tuning/automatic-tuning), [változások követése](/sql/relational-databases/track-changes/about-change-tracking-sql-server), [adatbázis-rendezés](/sql/relational-databases/collations/set-or-change-the-database-collation), [tárolt adatbázisok](/sql/relational-databases/databases/contained-databases), [tárolt felhasználók](/sql/relational-databases/security/contained-database-users-making-your-database-portable), [adattömörítés](/sql/relational-databases/data-compression/data-compression), [adatbázis-konfigurációs beállítások](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), [online indexelési műveletek](/sql/relational-databases/indexes/perform-index-operations-online), [particionálás](/sql/relational-databases/partitions/partitioned-tables-and-indexes)és [időbeli táblázatok](/sql/relational-databases/tables/temporal-tables) ([lásd az első lépéseket ismertető útmutatót](/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables)).
- Biztonsági funkciók – [alkalmazás-szerepkörök](/sql/relational-databases/security/authentication-access/application-roles), [dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking) ([első lépések SQL Database dinamikus adatmaszkolás a Azure Portal](../../azure-sql/database/dynamic-data-masking-configure-portal.md)), a [sorok szintjének biztonsága](/sql/relational-databases/security/row-level-security)
- Többmodelles funkciók – [gráf feldolgozása](/sql/relational-databases/graphs/sql-graph-overview), [JSON-adattartalom](/sql/relational-databases/json/json-data-sql-server), [OpenXML](/sql/t-sql/functions/openxml-transact-sql), [térbeli](/sql/relational-databases/spatial/spatial-data-sql-server), [openjson utasítással](/sql/t-sql/functions/openjson-transact-sql)és [XML-indexek](/sql/t-sql/statements/create-xml-index-transact-sql).



[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="features-of-azure-arc-enabled-sql-managed-instance"></a>Az Azure arc használatára képes SQL felügyelt példány funkciói

###  <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> RDBMS magas rendelkezésre állása  
  
|Funkció|Azure Arc-kompatibilis, felügyelt SQL-példány|
|-------------|----------------|
|Naplóküldés|Igen| 
|Biztonsági másolatok tömörítése|Igen|
|Adatbázis-pillanatkép|Igen|
|Always On feladatátvevő fürt<sup>1</sup> . példánya| Nem alkalmazható. Hasonló funkciók érhetők el |
|Always On rendelkezésre állási csoportok<sup>2</sup>|HA a szolgáltatások tervezése megtörténik.|
|Alapszintű rendelkezésre állási csoportok <sup>2</sup>|HA a szolgáltatások tervezése megtörténik.|
|A replika minimális végrehajtási rendelkezésre állási csoportja <sup>2</sup>|HA a szolgáltatások tervezése megtörténik.|
|Fürt nélküli rendelkezésre állási csoport|Igen|
|Online oldal és fájl visszaállítása|Igen|
|Online indexelés|Igen|
|Visszafolytatható online index-Újraépítés|Igen|
|Online séma módosítása|Igen|
|Gyors helyreállítás|Igen|
|Tükrözött biztonsági másolatok|Igen|
|Gyors memória-és CPU-Hozzáadás|Igen|
|Titkosított biztonsági mentés|Igen|
|Hibrid biztonsági mentés az Azure-ba (biztonsági mentés az URL-címre)|Igen|

<sup>1</sup> abban a forgatókönyvben, ahol a pod meghibásodása van, egy új SQL felügyelt példány indul el, és újra csatolva lesz az adatait tartalmazó állandó kötethez. [További információ az állandó kötetek Kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes).

<sup>2</sup> a jövőbeli kiadások az AG képességeit fogják biztosítani 

###  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> RDBMS méretezhetőség és teljesítmény  

| Funkció | Azure Arc-kompatibilis, felügyelt SQL-példány |
|--|--|
| Oszlopcentrikus | Igen |
| Nagyméretű objektumok bináris fájljai fürtözött oszlopcentrikus indexekben | Igen |
| Online nem fürtözött oszlopcentrikus index újraépítése | Igen |
| Memóriában tárolt OLTP | Igen |
| Állandó fő memória | Igen |
| Tábla-és indexelési particionálás | Igen |
| Adattömörítés | Igen |
| Resource Governor | Igen |
| Particionált tábla párhuzamossága | Igen |
| NUMA-Aware és nagyméretű oldal memóriája és puffer-tömb kiosztása | Igen |
| IO-erőforrás szabályozása | Igen |
| Késleltetett tartósság | Igen |
| Automatikus finomhangolás | Igen |
| Kötegelt módú adaptív illesztések | Igen |
| Kötegelt üzemmód memória-engedélyezési visszajelzés | Igen |
| Többutasításos táblázat értékű függvények egymással megszakított végrehajtása | Igen |
| Tömeges Beszúrás – tökéletesítések | Igen |

###  <a name="rdbms-security"></a><a name="RDBMSS"></a> RDBMS biztonság

| Funkció | Azure Arc-kompatibilis, felügyelt SQL-példány |
|--|--|
| Sorszintű biztonság | Igen |
| Always Encrypted | Igen |
| Biztonságos enklávékkal ellátott Always Encrypted | Nem |
| Dinamikus adatmaszkolás | Igen |
| Alapszintű naplózás | Igen |
| Részletes naplózás | Igen |
| Transzparens adatbázis-titkosítás | Igen |
| Felhasználó által definiált szerepkörök | Igen |
| Tartalmazott adatbázisok | Igen |
| Biztonsági másolatok titkosítása | Igen |

###  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> RDBMS kezelhetősége  

| Funkció | Azure Arc-kompatibilis, felügyelt SQL-példány |
|--|--|
| Dedikált rendszergazdai kapcsolatok | Igen |
| PowerShell-parancsfájlok támogatása | Igen |
| Az adatrétegbeli alkalmazás-összetevő műveleteinek támogatása – kinyerés, üzembe helyezés, frissítés, törlés | Igen |
| Házirend-automatizálás (ellenőrzési ütemterv és módosítás) | Igen |
| Teljesítményadatokat gyűjtő | Igen |
| Normál teljesítményű jelentések | Igen |
| Útmutatók tervezése és a tervek befagyasztásának megtervezése | Igen |
| Indexelt nézetek közvetlen lekérdezése (a deexpand mutató használatával) | Igen |
| Automatikus indexelt nézetek karbantartása | Igen |
| Elosztott particionált nézetek | Igen |
| Párhuzamos indexelt műveletek | Igen |
| Indexelt nézet automatikus használata lekérdezés-optimalizáló használatával | Igen |
| Párhuzamos konzisztencia-ellenőrzés | Igen |

### <a name="programmability"></a><a name="Programmability"></a> Programozhatóság  

| Funkció | Azure Arc-kompatibilis, felügyelt SQL-példány |
|--|--|
| JSON | Igen |
| Lekérdezéstár | Igen |  |
| Historikus | Igen |  |
| Natív XML-támogatás | Igen |  |
| XML-indexelés | Igen |  |
| & UPSERT-képességek EGYESÍTÉSe | Igen |  |
| Dátum és idő adattípus | Igen |  |
| Honosítási támogatás | Igen |  |
| Teljes szöveges és szemantikai keresés | Nem |
| A nyelv meghatározása a lekérdezésben | Igen |  |
| Service Broker (üzenetküldés) | Igen |  |
| Transact-SQL-végpontok | Igen |  |
| Graph | Igen |  |
| Machine Learning Services | Nem |  |
| PolyBase | Nem |


### <a name="tools"></a>Eszközök

Az Azure arc használatára képes SQL felügyelt példány különböző adateszközöket támogat, amelyek segítségével kezelheti adatait.

| **Eszköz** | Azure Arc-kompatibilis, felügyelt SQL-példány|
| --- | --- | --- |
| Azure Portal <sup>1</sup> | Nem |
| Azure CLI | Nem |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | Igen |
| Azure PowerShell | Igen |
| [BACPAC-fájl (exportálás)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Igen |
| [BACPAC-fájl (importálás)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Igen |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | Igen |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | Igen |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | Igen |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | Igen |

<sup>1</sup> a Azure Portal csak olvasható módban tekintheti meg az Azure arc használatára képes SQL felügyelt példányokat az előzetes verzió használatakor.


### <a name="unsupported-features--services"></a><a name="Unsupported"></a> Nem támogatott szolgáltatások & szolgáltatások

A következő szolgáltatások és szolgáltatások nem érhetők el az Azure arc használatára képes SQL felügyelt példányhoz. Ezeknek a szolgáltatásoknak a támogatása az idő múlásával egyre nagyobb mértékben engedélyezve lesz.

| Terület | Nem támogatott szolgáltatás vagy szolgáltatás |
|-----|-----|
| **Database engine** | Replikálás egyesítése |
| &nbsp; | A stretch DB |
| &nbsp; | Elosztott lekérdezés külső felek kapcsolataival |
| &nbsp; | Csatolt kiszolgálók a SQL Server és az Azure SQL-termékektől eltérő adatforrásokhoz |
| &nbsp; | Rendszer kiterjesztett tárolt eljárásai (XP_CMDSHELL stb.) |
| &nbsp; | Lefilé, FILESTREAM |
| &nbsp; | CLR-szerelvények EXTERNAL_ACCESS vagy nem biztonságos engedélyekkel |
| &nbsp; | Puffer-készlet kiterjesztése |
| **SQL Server Agent** |  Alrendszerek: CmdExec, PowerShell, üzenetsor-olvasó, SSIS, SSAS, SSRS |
| &nbsp; | Riasztások |
| &nbsp; | Felügyelt biztonsági mentés |
| **Magas rendelkezésre állás** | Adatbázis-tükrözés  |
| **Biztonság** | Bővíthető kulcskezelő |
| &nbsp; | AD-hitelesítés csatolt kiszolgálók esetén | 
| &nbsp; | AD-hitelesítés a rendelkezésre állási csoportokhoz (AGs) | 