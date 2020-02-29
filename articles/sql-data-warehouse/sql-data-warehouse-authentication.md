---
title: Hitelesítés
description: Megtudhatja, hogyan végezhet hitelesítést az Azure szinapszis Analytics szolgáltatásban Azure Active Directory (HRE) vagy SQL Server hitelesítés használatával.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 235d13cfd6f33830d832a6a79e3bc1c78bbfe53e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195974"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Hitelesítés az Azure szinapszis Analytics szolgáltatásban
Ismerje meg, hogyan hitelesítheti az Azure Szinapszisban az SQL Analytics szolgáltatást Azure Active Directory (HRE) vagy SQL Server hitelesítés használatával.

SQL-készlethez való kapcsolódáshoz hitelesítő adatokat kell megadnia hitelesítési célokra. A kapcsolat létrehozásakor a rendszer bizonyos kapcsolatbeállításokat a lekérdezési munkamenet létrehozásának részeként konfigurál.  

A biztonsággal és az adattárház kapcsolatainak engedélyezésével kapcsolatos további információkért lásd az [adatbázis-dokumentáció biztonságossá](sql-data-warehouse-overview-manage-security.md)tételét ismertető témakört.

## <a name="sql-authentication"></a>SQL-hitelesítés
Az SQL-készlethez való kapcsolódáshoz a következő információkat kell megadnia:

* Teljes kiszolgálónév
* SQL-hitelesítés meghatározása
* Felhasználónév
* Jelszó
* Alapértelmezett adatbázis (nem kötelező)

Alapértelmezés szerint a kapcsolat a *Master* adatbázishoz csatlakozik, nem pedig a felhasználói adatbázishoz. A felhasználói adatbázishoz való kapcsolódáshoz két dolog közül választhat:

* Adja meg az alapértelmezett adatbázist, amikor regisztrálja a kiszolgálót a SSDT, SSMS vagy az alkalmazás-kapcsolódási karakterláncban szereplő SQL Server Object Explorer. Adja meg például a InitialCatalog paramétert egy ODBC-kapcsolatok esetében.
* A SSDT-munkamenet létrehozása előtt jelölje ki a felhasználói adatbázist.

> [!NOTE]
> A Transact-SQL-utasítás a **MyDatabase; használata** nem támogatott a kapcsolatok adatbázisának módosításához. Ahhoz, hogy útmutatást biztosítson egy SQL-készlethez a SSDT-mel, tekintse meg a [lekérdezés a Visual Studio](sql-data-warehouse-query-visual-studio.md) használatával című cikket.
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (HRE) hitelesítés
[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) hitelesítés a Azure Active Directory (Azure ad) identitások használatával történő csatlakozásának mechanizmusa az SQL-készlethez. A Azure Active Directory hitelesítéssel központilag kezelheti az adatbázis-felhasználók és más Microsoft-szolgáltatások identitásait egy központi helyen. A központi AZONOSÍTÓk kezelése egyetlen helyet biztosít az Azure szinapszis-felhasználók felügyeletéhez, és egyszerűbbé teszi az engedélyek kezelését. 

### <a name="benefits"></a>Előnyök
A Azure Active Directory előnyei a következők:

* A SQL Server hitelesítés alternatíváját adja meg.
* Segít leállítani a felhasználói identitások elterjedését az adatbázis-kiszolgálókon.
* Központi helyet biztosít a jelszóváltoztatáshoz
* Adatbázis-engedélyek kezelése külső (HRE) csoportok használatával.
* Az integrált Windows-hitelesítés és a Azure Active Directory által támogatott hitelesítés más formáinak engedélyezésével kiküszöböli a jelszavak tárolását.
* A a tárolt adatbázis-felhasználók használatával hitelesíti az identitásokat az adatbázis szintjén.
* Támogatja a jogkivonat-alapú hitelesítést az SQL-készlethez kapcsolódó alkalmazások esetében.
* A többtényezős hitelesítést Active Directory univerzális hitelesítéssel támogatja különböző eszközökhöz, például [SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md) és [SQL Server Data Toolsekhez](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json).

> [!NOTE]
> Azure Active Directory még mindig viszonylag új, és bizonyos korlátozásokkal rendelkezik. Annak biztosítása érdekében, hogy Azure Active Directory a környezetnek megfelelő legyen, tekintse meg az [Azure ad-szolgáltatások és-korlátozások](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)című témakört, pontosabban a további szempontokat.
> 
> 

### <a name="configuration-steps"></a>Konfigurációs lépések
Azure Active Directory hitelesítés konfigurálásához kövesse az alábbi lépéseket.

1. Azure Active Directory létrehozása és feltöltése
2. Nem kötelező: az Azure-előfizetéshez jelenleg társított Active Directory társítása vagy módosítása
3. Azure Active Directory-rendszergazda létrehozása az Azure Szinapszishoz
4. Ügyfélszámítógépek konfigurálása
5. Tárolt adatbázis-felhasználók létrehozása az adatbázisban az Azure AD-identitásokhoz rendelve
6. Kapcsolódás az SQL-készlethez az Azure AD-identitások használatával

Jelenleg Azure Active Directory felhasználók nem jelennek meg a SSDT-Object Explorerokban. Megkerülő megoldásként tekintse meg a [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx)felhasználóit.

### <a name="find-the-details"></a>A részletek megkeresése
* A Azure Active Directory hitelesítés konfigurálásának és használatának lépései közel azonosak az Azure szinapszis-beli Azure SQL Database és SQL-elemzések esetében. A Azure Active Directory-hitelesítés használatával hajtsa végre a következő témakör részletes lépéseit: [csatlakozás SQL Database vagy SQL-készlethez](../sql-database/sql-database-aad-authentication.md).
* Hozzon létre egyéni adatbázis-szerepköröket, és adja hozzá a felhasználókat a szerepkörökhöz. Ezután adjon meg részletes engedélyeket a szerepkörökhöz. További információ: [első lépések az adatbázismotor engedélyeivel](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>További lépések
A Visual Studióval és más alkalmazásokkal való lekérdezés megkezdéséhez lásd: [lekérdezés a Visual Studióval](sql-data-warehouse-query-visual-studio.md).
