---
title: Azure SQL Database importálási/exportálási szolgáltatás egy adatbázis importálásához vagy exportálásához hosszú időt vesz igénybe | Microsoft Docs
description: Azure SQL Database importálási/exportálási szolgáltatás hosszú időt vesz igénybe az adatbázisok importálásához vagy exportálásához
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: ed5f3d1cd505270eb91c9cfbd6fb5c38b908f33d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974460"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL Database importálási/exportálási szolgáltatás hosszú időt vesz igénybe az adatbázisok importálásához vagy exportálásához

Azure SQL Database importálási/exportálási szolgáltatás használatakor észreveheti, hogy néha a folyamat végrehajtása hosszabb ideig is tarthat. Ez a cikk további információkat tartalmaz a késések lehetséges okairól, valamint a problémák megoldásához használható alternatív módszerekről.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database importálási/exportálási szolgáltatás

A Azure SQL Database import/export szolgáltatás egy REST-alapú webszolgáltatás, amely minden Microsoft Azure adatközpontban fut. Ez az a szolgáltatás, amely akkor lesz meghívva, amikor az [adatbázis importálása](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal) vagy az [Exportálás](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) lehetőség használatával helyezi át az SQL-adatbázist a Microsoft Azure Portal. A szolgáltatás ingyenes kérések üzenetsor-kezelő szolgáltatást és ingyenes számítási szolgáltatást biztosít egy Microsoft Azure SQL-adatbázisból Microsoft Azure bináris nagy objektum (BLOB) tárolóba történő Importálás és exportálás végrehajtásához.

Az importálási és exportálási műveletek nem a hagyományos fizikai adatbázis biztonsági mentése, hanem a speciális BACPAC formátumot használó adatbázis logikai biztonsági mentése. Ez a logikai BACPAC formátum lehetővé teszi, hogy ne kelljen olyan fizikai formátumot használnia, amely a SQL Server és SQL Database verziói között változhat. Ezért a segítségével biztonságosan állíthatja vissza az adatbázist egy SQL-adatbázisba és egy SQL Server adatbázisba.

## <a name="what-causes-the-process-to-take-a-long-time"></a>Mi okozza, hogy a folyamat hosszú ideig tart

A Azure SQL Database importálási/exportálási szolgáltatás régiónként korlátozott számú számítási virtuális gépet (VM) biztosít az importálási és exportálási műveletek feldolgozásához. A számítási virtuális gép régiónként üzemel, így biztos lehet abban, hogy az Importálás vagy az Exportálás elkerüli a régiók közötti sávszélesség késését és díját. Ha azonban túl sok kérést adott meg ugyanabban a régióban, akkor a műveletek feldolgozásakor jelentős késések történnek. A kérések végrehajtásához szükséges idő néhány másodperctől akár több óráig is változhat.

> [!NOTE]
> Ha a kérést négy napon belül nem dolgozzák fel, a szolgáltatás automatikusan megszakítja a kérést.

## <a name="recommended-solutions"></a>Ajánlott megoldások

Ha az adatbázis-exportálást csak a véletlen adattörlésből történő helyreállításra használja, az összes Azure SQL Server adatbázis-kiadás biztosítja az önkiszolgáló visszaállítási képességet a rendszer által létrehozott biztonsági mentésből. Ha azonban más okból is szükség van ezekre az exportálásokra, és ha következetesen gyorsabb vagy kiszámítható importálási vagy exportálási teljesítményre van szüksége, vegye figyelembe a következő lehetőségeket:

* [Exportálás BACPAC-fájlba az SQLPackage segédprogram használatával](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [Exportálás BACPAC-fájlba SQL Server Management Studio (SSMS) használatával](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Futtassa a BACPAC Importálás vagy exportálás közvetlenül a kódban a Microsoft® SQL Server® adatrétegbeli alkalmazás-keretrendszer (DacFx) API használatával. További információk áttekintése
  * [Adatrétegbeli alkalmazás exportálása](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft. SqlServer. DAC névtér](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [DACFx letöltése](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="considerations-when-exporting-or-importing-an-azure-sql-database"></a>Az Azure SQL Database-adatbázisok exportálásával vagy importálásával kapcsolatos megfontolások

* Az ebben a cikkben ismertetett módszerek a DTU kvótát használják, ami az Azure SQLDB szolgáltatás általi szabályozást eredményez. Az [adatbázis DTU-statisztikáit a Azure Portal tekintheti meg](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Ha az adatbázis eléri az erőforrások korlátait, [frissítse a szolgáltatási szintet](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) további erőforrások hozzáadásához.
* Az ügyfélalkalmazások (például a sqlpackage segédprogram vagy az egyéni DAC-alkalmazás) ideális esetben egy virtuális gépről (VM) futnak az SQL Database-adatbázissal azonos régióban, vagy ha a hálózat késése miatt teljesítménybeli problémákba kerülhetnek.
* A nagyméretű táblák fürtözött indexek nélküli exportálása nagyon lassú lehet, vagy meghibásodást eredményezhet. Ennek az az oka, hogy a táblázatot nem lehet párhuzamosan felosztani és exportálni, és egyetlen tranzakcióban kell exportálni, amely a lassú és a lehetséges hibákat okozza az exportálás során, különösen nagy táblák esetén. 


## <a name="related-documents"></a>Kapcsolódó dokumentumok

[Az Azure SQL Database-adatbázisok exportálásának szempontjai](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
