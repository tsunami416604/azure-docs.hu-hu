---
title: Az importálási/exportálási szolgáltatás hosszú időt vesz igénybe
description: Azure SQL Database importálási/exportálási szolgáltatás hosszú időt vesz igénybe az adatbázisok importálásához vagy exportálásához
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: e1638c9779ca50507a1ce30dd3bbc9c18248964a
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807068"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL Database importálási/exportálási szolgáltatás hosszú időt vesz igénybe az adatbázisok importálásához vagy exportálásához

Ha a Azure SQL Database importálási/exportálási szolgáltatást használja, a folyamat a vártnál hosszabb időt is igénybe vehet. Ez a cikk a késés és az alternatív megoldási módszerek lehetséges okait ismerteti.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database importálási/exportálási szolgáltatás

A Azure SQL Database import/export szolgáltatás egy REST-alapú webszolgáltatás, amely minden Azure-adatközpontban fut. A szolgáltatás akkor lesz meghívva, ha az [adatbázis importálása](sql-database-import.md#using-azure-portal) vagy [exportálása](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) lehetőséget használja az SQL-adatbázis áthelyezéséhez a Azure Portal. A szolgáltatás ingyenes kérelmek üzenetsor-kezelő és számítási szolgáltatásokat biztosít az Azure SQL Database és az Azure Blob Storage közötti importálás és exportálás végrehajtásához.

Az importálási és exportálási műveletek nem a hagyományos fizikai adatbázis biztonsági mentését jelentik, hanem a speciális BACPAC formátumot használó adatbázis logikai biztonsági mentését. A BACPAC formátuma lehetővé teszi, hogy ne kelljen olyan fizikai formátumot használnia, amely a Microsoft SQL Server és Azure SQL Database verziói között változhat. Ezért a segítségével biztonságosan visszaállíthatja az adatbázist egy SQL Server adatbázisba és egy SQL-adatbázisba.

## <a name="what-causes-delays-in-the-process"></a>Mi okozza a folyamat késéseit?

A Azure SQL Database importálási/exportálási szolgáltatás korlátozott számú számítási virtuális gépet (VM) kínál régiónként az importálási és exportálási műveletek feldolgozásához. A számítási virtuális gépek régiónként vannak tárolva, így biztos lehet benne, hogy az Importálás vagy az Exportálás elkerüli a régiók közötti sávszélesség késését és díját. Ha túl sok kérést hajtanak végre ugyanabban a régióban, a műveletek feldolgozásakor jelentős késések merülhetnek fel. A kérelmek teljesítéséhez szükséges idő néhány másodperctől akár több óráig is változhat.

> [!NOTE]
> Ha a kérést négy napon belül nem dolgozzák fel, a szolgáltatás automatikusan megszakítja a kérést.

## <a name="recommended-solutions"></a>Ajánlott megoldások

Ha az adatbázis-exportálást csak a véletlen adattörlésből történő helyreállításra használja, az összes Azure SQL Database kiadás a rendszer által létrehozott biztonsági másolatok önkiszolgáló visszaállítási funkcióját biztosítja. Ha azonban szükség van ezekre az exportálásokra más okokból, és ha következetesen gyorsabb vagy kiszámítható importálási/exportálási teljesítményre van szüksége, vegye figyelembe a következő lehetőségeket:

* [EXPORTÁLÁS BACPAC-fájlba az SQLPackage segédprogram használatával](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [EXPORTÁLÁS BACPAC-fájlba SQL Server Management Studio (SSMS) használatával](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Futtassa a BACPAC Importálás vagy exportálás közvetlenül a kódban a Microsoft SQL Server Data-Tier Application Framework (DacFx) API használatával. További információt a következő témakörben talál:
  * [Adatrétegbeli alkalmazás exportálása](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft. SqlServer. DAC névtér](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [DACFx letöltése](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Az Azure SQL Database-adatbázisok exportálásakor és importálásakor megfontolandó szempontok

* A cikkben tárgyalt összes módszer az adatbázis-tranzakciós egység (DTU) kvótáját használja, amely a Azure SQL Database szolgáltatás általi szabályozást okoz. Az [adatbázis DTU-statisztikáit a Azure Portal tekintheti meg](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Ha az adatbázis elérte az erőforrás-korlátozásokat, [frissítse a szolgáltatási szintet](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) további erőforrások hozzáadásához.
* Ideális esetben az ügyfélalkalmazások (például a sqlpackage segédprogram vagy az egyéni DAC-alkalmazás) egy, az SQL-adatbázissal megegyező régióban lévő virtuális gépről kell futniuk. Ellenkező esetben a hálózati késéssel kapcsolatos teljesítményproblémák merülhetnek fel.
* A nagyméretű táblák fürtözött indexek nélküli exportálása nagyon lassú lehet, vagy akár hibát okozhat. Ez a viselkedés azért fordul elő, mert a tábla nem bontható fel és nem exportálható párhuzamosan. Ehelyett egyetlen tranzakcióban kell exportálni, ami lassú teljesítményt és lehetséges meghibásodást okoz az exportálás során, különösen nagy táblák esetén.


## <a name="related-documents"></a>Kapcsolódó dokumentumok

[Az Azure SQL Database-adatbázisok exportálásának szempontjai](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
