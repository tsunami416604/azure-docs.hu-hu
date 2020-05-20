---
title: Az Azure SQL Edge támogatott funkciói (előzetes verzió)
description: Ismerje meg az Azure SQL Edge által támogatott szolgáltatások részleteit (előzetes verzió)
keywords: az SQL Edge bemutatása, mi az SQL Edge, az SQL Edge áttekintése
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f0994ac1d28118869f0d5c2844a034623d101ee8
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684368"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Az Azure SQL Edge támogatott funkciói (előzetes verzió) 

Ez a cikk az Azure SQL Edge által támogatott funkciók részleteit tartalmazza. Az Azure SQL Edge a Linuxon futó Microsoft SQL Server adatbázismotor legújabb verziójára épül, és támogatja a Linux 2019 SQL Server-es verziójában támogatott funkciók egy részét, valamint a Linuxon vagy a Windowson SQL Server 2019-es verzióban jelenleg nem támogatott szolgáltatásokat. A SQL Server on Linux által támogatott funkciók teljes listáját a [Linuxon futó SQL Server 2019 kiadásait és támogatott funkcióit](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019)ismertető témakörben tekintheti meg. A Windows SQL Server kiadásait és támogatott funkcióit a [SQL Server 2019 (15. x) kiadásait és támogatott funkcióit](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15)ismertető témakörben tekintheti meg.

> [!NOTE]
> Az Azure SQL Edge jelenleg (előzetes verzió), ezért nem használható éles környezetben. A Microsoft javasolhatja, hogy az Azure SQL Edge éles környezetekben fusson az üzembe helyezés és a használati eset forgatókönyvek ellenőrzése után.

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge-kiadások

Az Azure SQL Edge két különböző kiadással vagy szoftver csomaggal érhető el. Ezek a kiadások azonos szolgáltatáskészlet-készlettel rendelkeznek, és csak a használati jogosultságok és a gazdagéprendszer számára elérhető CPU/memória mennyisége alapján térnek el egymástól.

   |**Terv**  |**Leírás**  |
   |---------|---------|
   |Azure SQL Edge-fejlesztő  |  Csak fejlesztési SKU, minden Azure SQL Edge fejlesztői tároló legfeljebb 4 maggal és 32 GB memóriára van korlátozva  |
   |Azure SQL Edge    |  Üzemi SKU, az Azure SQL Edge-tárolók legfeljebb 8 maggal és 64 GB memóriával vannak korlátozva  |

## <a name="operating-system"></a>Operációs rendszer

Az Azure SQL Edge-tárolók jelenleg Ubuntu 16,04-alapúak, és csak olyan Docker-gazdagépeken futnak, amelyek Ubuntu 16,04 (ajánlott) vagy Ubuntu 18,04 rendszert futtatnak. Az Azure SQL Edge más operációs rendszerű gazdagépeken is futtatható, például a Linux vagy a Windows egyéb disztribúciói (a Docker CE vagy a Docker EE használatával), azonban ezeket a konfigurációkat a Microsoft nem teszteli alaposan.

Az Azure SQL Edge jelenleg csak a Azure IoT Edgeon keresztül történő üzembe helyezéshez támogatott. A Azure IoT Edge támogatott rendszereiről a [Azure IoT Edge támogatott rendszerek](https://docs.microsoft.com/azure/iot-edge/support)című témakörben olvashat bővebben.

Az Azure SQL Edge Windows rendszeren való futtatásához ajánlott konfiguráció egy Ubuntu virtuális gép konfigurálása a Windows-gazdagépen, majd az SQL Edge futtatása a Linux rendszerű virtuális gépen.

## <a name="hardware-support"></a>Hardveres támogatás

Az Azure SQL Edge egy 64 bites processzort igényel, amely az Inteltől, az AMD-től vagy az ARM-tól, és legalább egy processzorral és egy GB RAM-mal rendelkezik a gazdagépen. Míg az Azure SQL Edge indítási memória-lábnyoma 500 MB-tal közelíthető meg, a peremhálózati eszközön futó többi IoT Edge-modulhoz további memória szükséges.

## <a name="azure-sql-edge-components"></a>Azure SQL Edge-összetevők

Az Azure SQL Edge csak az adatbázismotor használatát támogatja, és nem támogatja a Windows SQL Server 2019-es vagy Linux-os SQL Server 2019-es verziója által elérhető egyéb összetevőket. Az Azure SQL Edge nem támogatja az olyan SQL Server összetevőket, mint a Analysis Services, a Reporting Services, az Integration Services, a Master Data Services, a Machine Learning Services (in-Database) és a Machine Learning Server (önálló).

## <a name="supported-features"></a>Támogatott szolgáltatások

A SQL Server on Linux szolgáltatások egy részhalmazának támogatása mellett az Azure SQL Edge a következő új funkciók támogatását is tartalmazza. 

- Az SQL streaming, amely ugyanazon a motoron alapul, mint a Azure Stream Analytics, valós idejű adatfolyam-funkciókat biztosít az Azure SQL Edge-ben. 
- Új T-SQL-függvény hívása Date_Bucket az idősorozat adatelemzéséhez.
- Machine Learning képességek az SQL-motorhoz mellékelt ONNX-futtatókörnyezettel.

## <a name="sql-server-on-linux-features-not-supported-in-azure-sql-edge"></a>Az Azure SQL Edge-ben nem támogatott SQL Server on Linux szolgáltatások

Az alábbi lista az Azure SQL Edge által jelenleg nem támogatott Linux-funkciókra vonatkozó SQL Server 2019-es listát tartalmazza.

| Terület | Nem támogatott szolgáltatás vagy szolgáltatás |
|-----|-----|
| **Adatbázis-kialakítás** | Memóriában tárolt OLTP és kapcsolódó DDL-parancsok és Transact-SQL függvények, katalógus-nézetek és dinamikus felügyeleti nézetek |
| &nbsp; | HierarchyID adattípus és kapcsolódó DDL-parancsok és Transact-SQL függvények, katalógus-nézetek és dinamikus felügyeleti nézetek |
| &nbsp; | Térbeli adatok típusa és kapcsolódó DDL-parancsok és Transact-SQL függvények, katalógus-nézetek és dinamikus felügyeleti nézetek |
| &nbsp; | A stretch DB és a kapcsolódó DDL-parancsok és a Transact-SQL függvények, a katalógus nézetei és a dinamikus felügyeleti nézetek |
| &nbsp; | Teljes szöveges indexek és keresési és kapcsolódó DDL-parancsok és Transact-SQL függvények, katalógus-nézetek és dinamikus felügyeleti nézetek|
| &nbsp; | Lehasználható, FILESTREAM és kapcsolódó DDL-parancsok és Transact-SQL függvények, katalógus-nézetek és dinamikus felügyeleti nézetek|
| **Adatbázismotor** | Replikáció. Az Azure SQL Edge azonban konfigurálható egy replikációs topológia leküldéses előfizetői számára. |
| &nbsp; | Polybase. Az Azure SQL Edge azonban megadható célként a következő külső táblákhoz: |
| &nbsp; | Nyelvi bővíthetőség a Java és a Spark használatával |
| &nbsp; | Active Directory-integráció |
| &nbsp; | Adatbázis-Pillanatképek |
| &nbsp; | Tartós memória támogatása |
| &nbsp; | Microsoft Elosztott tranzakciók koordinátora |
| &nbsp; | Resource Governor és IO erőforrás-szabályozás |
| &nbsp; | Puffer-készlet kiterjesztése |
| &nbsp; | Elosztott lekérdezés külső felek kapcsolataival |
| &nbsp; | Társított kiszolgálók |
| &nbsp; | Rendszer kiterjesztett tárolt eljárásai (XP_CMDSHELL stb.) |
| &nbsp; | CLR-szerelvények és kapcsolódó DDL-parancsok és Transact-SQL függvények, katalógus-nézetek és dinamikus felügyeleti nézetek |
| &nbsp; | CLR-függő T-SQL-függvények, például ASSEMBLYPROPERTY, FORMAT, PARSe, TRY_PARSE |
| &nbsp; | CLR-függő dátum-és időkatalógus-nézetek, függvények és lekérdezési záradékok |
| &nbsp; | Puffer-készlet kiterjesztése |
| &nbsp; | Database Mail |
| **SQL Server Agent** |  Alrendszerek: CmdExec, PowerShell, üzenetsor-olvasó, SSIS, SSAS, SSRS |
| &nbsp; | Riasztások |
| &nbsp; | Felügyelt biztonsági mentés |
| **Magas rendelkezésre állás** | Always On rendelkezésre állási csoportok  |
| &nbsp; | Alapszintű rendelkezésre állási csoportok |
| &nbsp; | Always On feladatátvevő fürt példánya |
| &nbsp; | Adatbázis-tükrözés |
| &nbsp; | Gyors memória-és CPU-Hozzáadás |
| **Biztonság** | Bővíthető kulcskezelő |
| &nbsp; | Active Directory-integráció|
| &nbsp; | A biztonságos enklávék támogatása|
| **Szolgáltatások** | SQL Server Browser |
| &nbsp; | Machine Learning az R és a Python használatával |
| &nbsp; | StreamInsight |
| &nbsp; | Analysis Services |
| &nbsp; | Reporting Services |
| &nbsp; | Adatminőségi szolgáltatások |
| &nbsp; | Master Data Services |
| &nbsp; | Distributed Replay |
| **Kezelhetőség** | SQL Server segédprogram-vezérlési pont |

## <a name="next-steps"></a>Következő lépések

- [Az Azure SQL Edge üzembe helyezése](deploy-portal.md)
- [Az Azure SQL Edge konfigurálása](configure.md)
- [Kapcsolódás Azure SQL Edge-példányhoz SQL Server ügyféleszközök használatával](connect.md)
- [Adatbázisok biztonsági mentése és visszaállítása az Azure SQL Edge-ben](backup-restore.md)
