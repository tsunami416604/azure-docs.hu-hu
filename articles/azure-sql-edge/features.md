---
title: Az Azure SQL Edge támogatott funkciói (előzetes verzió)
description: Ismerje meg az Azure SQL Edge (előzetes verzió) által támogatott funkciók részleteit.
keywords: az SQL Edge bemutatása, mi az SQL Edge, az SQL Edge áttekintése
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 87ccb28f527082dccee338396b460124652c0e76
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462700"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Az Azure SQL Edge támogatott funkciói (előzetes verzió) 

Az Azure SQL Edge a Microsoft SQL Server adatbázismotor legújabb verziójára épül Linuxon. Támogatja a Linux SQL Server 2019-es verziójában támogatott szolgáltatások egy részhalmazát, valamint néhány olyan funkciót, amely jelenleg nem támogatott, vagy SQL Server 2019-ben érhető el Linux rendszeren (vagy SQL Server Windows rendszeren).

A SQL Server on Linux által támogatott funkciók teljes listáját a [Linuxon futó SQL Server 2019 kiadásait és támogatott funkcióit](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019)ismertető témakörben tekintheti meg. A Windows SQL Server kiadásait és támogatott funkcióit lásd: [SQL Server 2019 (15. x) kiadásai és támogatott szolgáltatásai](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15).

> [!NOTE]
> Az Azure SQL Edge jelenleg előzetes verzióban érhető el, ezért nem használható éles környezetben. Előfordulhat, hogy a Microsoft az üzemelő példány ellenőrzésének és a használati eseteknek megfelelően javasolja az Azure SQL Edge használatát éles környezetekben.

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge-kiadások

Az Azure SQL Edge két különböző kiadással vagy szoftver csomaggal érhető el. Ezek a kiadások azonos szolgáltatáskészlettel rendelkeznek, és csak a használati jogosultságok, valamint a gazdagéprendszer számára elérhető memória és magok mennyisége tekintetében térnek el egymástól.

   |**Terv**  |**Leírás**  |
   |---------|---------|
   |Azure SQL Edge-fejlesztő  |  Csak fejlesztési célokra. Minden Azure SQL Edge fejlesztői tároló legfeljebb 4 maggal és 32 GB memóriával rendelkezik.  |
   |Azure SQL Edge    |  Éles környezetben. Az egyes Azure SQL Edge-tárolók legfeljebb 8 maggal és 64 GB memóriával rendelkeznek.  |

## <a name="operating-system"></a>Operációs rendszer

Az Azure SQL Edge-tárolók jelenleg Ubuntu 16,04-alapúak, és csak az Ubuntu 16,04 LTS (ajánlott) vagy az Ubuntu 18,04 LTS rendszerű Docker-gazdagépeken futnak. Az Azure SQL Edge-tárolók futtathatók más operációs rendszerű gazdagépeken, például a Linux vagy a Windows rendszerű (Docker CE vagy Docker EE használatával) más disztribúciókban is futtathatók, azonban a Microsoft nem javasolja ezt, mert ez a konfiguráció nem lesz alaposan tesztelve.

Az Azure SQL Edge jelenleg csak a Azure IoT Edgeon keresztül történő üzembe helyezéshez támogatott. További információ: [Azure IoT Edge támogatott rendszerek](https://docs.microsoft.com/azure/iot-edge/support).

Az Azure SQL Edge Windows rendszeren való futtatásához ajánlott konfiguráció egy Ubuntu virtuális gép konfigurálása a Windows-gazdagépen, majd az Azure SQL Edge futtatása a Linux rendszerű virtuális gépen.

Az Azure SQL Edge ajánlott és támogatott fájlrendszere EXT4 és XFS. Ha állandó köteteket használ az Azure SQL Edge adatbázis-tárolójának visszaállítására, akkor a mögöttes gazdagép fájlrendszerének EXT4 és XFS kell lennie.

## <a name="hardware-support"></a>Hardveres támogatás

Az Azure SQL Edge használatához 64 bites processzor szükséges (x64 vagy ARM64), amely legalább egy processzorral és egy GB RAM-mal rendelkezik a gazdagépen. Míg az Azure SQL Edge indítási memória-lábnyoma 500 MB-tal közelíthető meg, a peremhálózati eszközön futó többi IoT Edge-modulhoz további memória szükséges. Az Azure SQL Edge tényleges memória-és CPU-követelményei a számítási feladatok és a feldolgozott adatmennyiség összetettsége alapján változhatnak. Ha hardvert választ a megoldásához, a Microsoft azt javasolja, hogy széleskörű teljesítménytesztek futtatásával ellenőrizze, hogy teljesülnek-e a megoldáshoz szükséges teljesítménybeli jellemzők.  

## <a name="azure-sql-edge-components"></a>Azure SQL Edge-összetevők

Az Azure SQL Edge csak az adatbázismotor használatát támogatja. Nem tartalmaz támogatást a Windows SQL Server 2019-es vagy Linux-alapú SQL Server 2019-es egyéb összetevőihez. Az Azure SQL Edge nem támogatja az olyan SQL Server összetevőket, mint a Analysis Services, a Reporting Services, az Integration Services, a Master Data Services, a Machine Learning Services (in-Database) és a Machine Learning Server (önálló).

## <a name="supported-features"></a>Támogatott funkciók

A SQL Server on Linux funkcióinak támogatása mellett az Azure SQL Edge a következő új funkciók támogatását is magában foglalja: 

- Az SQL streaming, amely ugyanazon a motoron alapul, mint a Azure Stream Analytics, valós idejű adatfolyam-funkciókat biztosít az Azure SQL Edge-ben. 
- A T-SQL függvény a `Date_Bucket` Time-Series adatelemzést hívja meg.
- A gépi tanulási képességek a ONNX futtatókörnyezettel, amely az SQL-motorhoz tartozik.

## <a name="unsupported-features"></a>Nem támogatott funkciók

Az alábbi lista az Azure SQL Edge által jelenleg nem támogatott Linux-funkciókra vonatkozó SQL Server 2019-es listát tartalmazza.

| Terület | Nem támogatott szolgáltatás vagy szolgáltatás |
|-----|-----|
| **Adatbázis-kialakítás** | Memóriában tárolt OLTP és kapcsolódó DDL-parancsok és Transact-SQL függvények, katalógus-nézetek és dinamikus felügyeleti nézetek. |
| &nbsp; | `HierarchyID` adattípus és kapcsolódó DDL-parancsok és Transact-SQL függvények, katalógus-nézetek és dinamikus felügyeleti nézetek. |
| &nbsp; | `Spatial` adattípus és kapcsolódó DDL-parancsok és Transact-SQL függvények, katalógus-nézetek és dinamikus felügyeleti nézetek. |
| &nbsp; | A stretch DB és a kapcsolódó DDL-parancsok, valamint a Transact-SQL függvények, katalógus-nézetek és dinamikus felügyeleti nézetek. |
| &nbsp; | Teljes szöveges indexek és keresések, valamint kapcsolódó DDL-parancsok és Transact-SQL függvények, katalógus-nézetek és dinamikus felügyeleti nézetek.|
| &nbsp; | `FileTable`, `FILESTREAM` és kapcsolódó DDL-parancsok és Transact-SQL függvények, katalógus-nézetek és dinamikus felügyeleti nézetek.|
| **Adatbázismotor** | Replikációs. Vegye figyelembe, hogy az Azure SQL Edge-t egy replikációs topológia leküldéses előfizetője is konfigurálhatja. |
| &nbsp; | Polybase. Vegye figyelembe, hogy az Azure SQL Edge a külső táblákhoz tartozó célként is konfigurálható. |
| &nbsp; | Nyelvi bővíthetőség a Java és a Spark használatával. |
| &nbsp; | Active Directory integráció. |
| &nbsp; | Adatbázis-Pillanatképek. |
| &nbsp; | Az állandó memória támogatása. |
| &nbsp; | Microsoft Elosztott tranzakciók koordinátora. |
| &nbsp; | Erőforrás-kormányzó és IO erőforrás-szabályozás. |
| &nbsp; | Puffer-készlet kiterjesztése. |
| &nbsp; | Elosztott lekérdezés külső felek kapcsolataival. |
| &nbsp; | Csatolt kiszolgálók. |
| &nbsp; | Rendszer kiterjesztett tárolt eljárásai (például `XP_CMDSHELL` ). |
| &nbsp; | CLR-szerelvények és kapcsolódó DDL-parancsok és Transact-SQL függvények, katalógus-nézetek és dinamikus felügyeleti nézetek. |
| &nbsp; | CLR-függő T-SQL függvények, például, `ASSEMBLYPROPERTY` , `FORMAT` `PARSE` és `TRY_PARSE` . |
| &nbsp; | CLR-függő dátum és idő katalógus-nézetek, függvények és lekérdezési záradékok. |
| &nbsp; | Puffer-készlet kiterjesztése. |
| &nbsp; | Adatbázis e-mail-címe. |
| &nbsp; | Szolgáltatásközvetítő |
| &nbsp; | Házirend-alapú felügyelet |
| &nbsp; | Felügyeleti adattárház |
| &nbsp; | Foglalt adatbázisok |
| **SQL Server Agent** |  Alrendszerek: CmdExec, PowerShell, üzenetsor-olvasó, SSIS, SSAS és SSRS. |
| &nbsp; | Riasztások. |
| &nbsp; | Felügyelt biztonsági mentés. |
| **Magas rendelkezésre állás** | Always On rendelkezésre állási csoportok.  |
| &nbsp; | Alapszintű rendelkezésre állási csoportok. |
| &nbsp; | Always On feladatátvevő fürt példánya. |
| &nbsp; | Adatbázis-tükrözés. |
| &nbsp; | Gyakori Hozzáadás a memóriához és a PROCESSZORhoz. |
| **Biztonság** | Bővíthető kulcs kezelése. |
| &nbsp; | Active Directory integráció.|
| &nbsp; | Biztonságos enklávék támogatása.|
| **Szolgáltatások** | SQL Server Browser. |
| &nbsp; | Machine Learning az R és a Python használatával. |
| &nbsp; | StreamInsight. |
| &nbsp; | Analysis Services. |
| &nbsp; | Reporting Services. |
| &nbsp; | Az adatminőségi szolgáltatások. |
| &nbsp; | Master Data Services. |
| &nbsp; | Distributed Replay. |
| **Kezelhetőség** | SQL Server segédprogram-vezérlő pont. |

## <a name="next-steps"></a>Következő lépések

- [Az Azure SQL Edge üzembe helyezése](deploy-portal.md)
- [Az Azure SQL Edge konfigurálása](configure.md)
- [Kapcsolódás Azure SQL Edge-példányhoz SQL Server ügyféleszközök használatával](connect.md)
- [Adatbázisok biztonsági mentése és visszaállítása az Azure SQL Edge-ben](backup-restore.md)
