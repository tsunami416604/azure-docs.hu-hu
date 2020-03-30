---
title: Az importálási/exportálási szolgáltatás hosszú időt vesz igénybe
description: Az Azure SQL Database Import/Export szolgáltatás a donátláshoz és az exportáláshoz hosszú időt vesz igénybe
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
manager: dcscontentpm
ms.openlocfilehash: cf2d9b218fe63414af2446b8562d3ba187b2d395
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535765"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Az Azure SQL Database Import/Export szolgáltatás a donátláshoz és az exportáláshoz hosszú időt vesz igénybe

Az Azure SQL Database Import/Export szolgáltatás használata esetén a folyamat a vártnál tovább tarthat. Ez a cikk a késleltetés és az alternatív megoldási módszerek lehetséges okait ismerteti.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL-adatbázis importálási/exportálási szolgáltatása

Az Azure SQL Database Import/Export szolgáltatás egy REST-alapú webszolgáltatás, amely fut minden Azure adatközpontban. Ez a szolgáltatás akkor jön létre, ha az [Adatbázis importálása](sql-database-import.md#using-azure-portal) vagy [exportálása](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) lehetőséget használja az SQL-adatbázis áthelyezéséhez az Azure Portalon. A szolgáltatás ingyenes kérés-sorban állási és számítási szolgáltatásokat biztosít az Azure SQL-adatbázis és az Azure Blob-tároló közötti importálás és exportálás elvégzéséhez.

Az importálási és exportálási műveletek nem hagyományos fizikai adatbázis-biztonsági mentést jelentenek, hanem egy speciális BACPAC formátumot használó adatbázis logikai biztonsági másolatát. A BACPAC formátum lehetővé teszi, hogy ne kelljen olyan fizikai formátumot használnia, amely a Microsoft SQL Server és az Azure SQL Database verziói között változhat. Ezért segítségével biztonságosan visszaállíthatja az adatbázist egy SQL Server-adatbázisba és egy SQL-adatbázisba.

## <a name="what-causes-delays-in-the-process"></a>Mi okozza a késedelmet a folyamatban?

Az Azure SQL Database Import/Export szolgáltatás régiónként korlátozott számú számítási virtuális gépet (VM)-t biztosít az importálási és exportálási műveletek feldolgozásához. A számítási virtuális gépek régiónként vannak tárolva, hogy az importálás vagy az exportálás elkerüli a régiók közötti sávszélesség-késéseket és díjakat. Ha egyszerre túl sok kérés történik ugyanabban a régióban, jelentős késések fordulhatnak elő a műveletek feldolgozása során. A kérelmek teljesítéséhez szükséges idő néhány másodperctől több óráig változhat.

> [!NOTE]
> Ha egy kérés négy napon belül nem kerül feldolgozásra, a szolgáltatás automatikusan visszavonja a kérést.

## <a name="recommended-solutions"></a>Ajánlott megoldások

Ha az adatbázis-exportálás csak a véletlen adattörlésből való helyreállításhoz használatos, az Azure SQL Database összes kiadása önkiszolgáló visszaállítási képességet biztosít a rendszer által létrehozott biztonsági mentésekből. Ha azonban más okból is szüksége van ezekre az exportálásokra, és következetesen gyorsabb vagy kiszámíthatóbb importálási/exportálási teljesítményre van szüksége, vegye figyelembe a következő lehetőségeket:

* [Exportáljon BACPAC-fájlba az SQLPackage segédprogrammal.](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [Exportálás BACPAC-fájlba az SQL Server Management Studio (SSMS) segítségével.](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Futtassa a BACPAC importálását vagy exportálását közvetlenül a kódban a Microsoft SQL Server Data-Tier Application Framework (DacFx) API használatával. További információ:
  * [Adatszintű alkalmazás exportálása](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft.SqlServer.Dac névtér](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [DacFx letöltése](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Az Azure SQL-adatbázisok exportálása kor vagy importálásakor figyelembe vejeafigyelembe venni kívánt tudnivalókat

* Az ebben a cikkben tárgyalt összes módszer használja fel az adatbázis-tranzakciós egység (DTU) kvótát, ami az Azure SQL Database szolgáltatás szabályozását okozza. Az [adatbázis DTU-statisztikáit az Azure Portalon tekintheti meg.](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring) Ha az adatbázis elérte az erőforrás-korlátokat, [frissítse a szolgáltatási réteget](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) további erőforrások hozzáadásához.
* Ideális esetben az ügyfélalkalmazásokat (például az sqlpackage segédprogramot vagy az egyéni DAC-alkalmazást) egy virtuális gépről kell futtatnia ugyanabban a régióban, mint az SQL-adatbázist. Ellenkező esetben előfordulhat, hogy a hálózati késéssel kapcsolatos teljesítményproblémákléphetnek fel.
* Nagy táblák fürtözött indexek nélküli exportálása nagyon lassú lehet, vagy akár hibát is okozhat. Ez a jelenség azért fordul elő, mert a tábla nem osztható fel és nem exportálható párhuzamosan. Ehelyett egyetlen tranzakcióban kell exportálni, ami lassú teljesítményt és potenciális hibát okoz az exportálás során, különösen a nagy táblák esetében.


## <a name="related-documents"></a>Kapcsolódó dokumentumok

[Az Azure SQL-adatbázisok exportálásának szempontjai](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
