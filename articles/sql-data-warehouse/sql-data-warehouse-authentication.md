---
title: Authentication
description: Megtudhatja, hogyan végezhet hitelesítést a Azure SQL Data Warehouse Azure Active Directory (HRE) vagy a SQL Server hitelesítés használatával.
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
ms.openlocfilehash: fda29e432fbd952261893f3c32a4df7b9990ae66
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692934"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Hitelesítés Azure SQL Data Warehouse
Megtudhatja, hogyan végezhet hitelesítést a Azure SQL Data Warehouse Azure Active Directory (HRE) vagy a SQL Server hitelesítés használatával.

A SQL Data Warehousehoz való kapcsolódáshoz hitelesítő adatokat kell átadnia hitelesítési célokra. A kapcsolat létrehozásakor a rendszer bizonyos kapcsolatbeállításokat a lekérdezési munkamenet létrehozásának részeként konfigurál.  

A biztonsággal és az adattárház kapcsolatainak engedélyezésével kapcsolatos további információkért lásd: [adatbázis biztonságossá tétele SQL Data Warehouseban][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>SQL-hitelesítés
A SQL Data Warehousehoz való kapcsolódáshoz a következő információkat kell megadnia:

* Teljes kiszolgálónév
* SQL-hitelesítés meghatározása
* Felhasználónév
* Jelszó
* Alapértelmezett adatbázis (nem kötelező)

Alapértelmezés szerint a kapcsolat a *Master* adatbázishoz csatlakozik, nem pedig a felhasználói adatbázishoz. A felhasználói adatbázishoz való kapcsolódáshoz két dolog közül választhat:

* Adja meg az alapértelmezett adatbázist, amikor regisztrálja a kiszolgálót a SSDT, SSMS vagy az alkalmazás-kapcsolódási karakterláncban szereplő SQL Server Object Explorer. Adja meg például a InitialCatalog paramétert egy ODBC-kapcsolatok esetében.
* A SSDT-munkamenet létrehozása előtt jelölje ki a felhasználói adatbázist.

> [!NOTE]
> A Transact-SQL-utasítás a **MyDatabase; használata** nem támogatott a kapcsolatok adatbázisának módosításához. A SQL Data Warehouse és a SSDT közötti csatlakozással kapcsolatos útmutatásért tekintse meg a [lekérdezés a Visual Studióval][Query with Visual Studio] című cikket.
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (HRE) hitelesítés
[Azure Active Directory][What is Azure Active Directory] hitelesítés a Microsoft Azure SQL Data Warehousehoz való kapcsolódási mechanizmus a Azure Active Directory (Azure ad) identitások használatával. A Azure Active Directory hitelesítéssel központilag kezelheti az adatbázis-felhasználók és más Microsoft-szolgáltatások identitásait egy központi helyen. A központi AZONOSÍTÓk kezelése egyetlen helyet biztosít a SQL Data Warehouse felhasználók felügyeletéhez, és egyszerűsíti az engedélyek kezelését. 

### <a name="benefits"></a>Előnyök
A Azure Active Directory előnyei a következők:

* A SQL Server hitelesítés alternatíváját adja meg.
* Segít leállítani a felhasználói identitások elterjedését az adatbázis-kiszolgálókon.
* Központi helyet biztosít a jelszóváltoztatáshoz
* Adatbázis-engedélyek kezelése külső (HRE) csoportok használatával.
* Az integrált Windows-hitelesítés és a Azure Active Directory által támogatott hitelesítés más formáinak engedélyezésével kiküszöböli a jelszavak tárolását.
* A a tárolt adatbázis-felhasználók használatával hitelesíti az identitásokat az adatbázis szintjén.
* Támogatja a jogkivonat-alapú hitelesítést a SQL Data Warehousehoz csatlakozó alkalmazások esetében.
* A többtényezős hitelesítést Active Directory univerzális hitelesítéssel támogatja különböző eszközökhöz, például [SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md) és [SQL Server Data Toolsekhez](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json).

> [!NOTE]
> Azure Active Directory még mindig viszonylag új, és bizonyos korlátozásokkal rendelkezik. Annak biztosítása érdekében, hogy Azure Active Directory a környezetnek megfelelő legyen, tekintse meg az [Azure ad-szolgáltatások és-korlátozások][Azure AD features and limitations]című témakört, pontosabban a további szempontokat.
> 
> 

### <a name="configuration-steps"></a>Konfigurációs lépések
Azure Active Directory hitelesítés konfigurálásához kövesse az alábbi lépéseket.

1. Azure Active Directory létrehozása és feltöltése
2. Nem kötelező: az Azure-előfizetéshez jelenleg társított Active Directory társítása vagy módosítása
3. Hozzon létre egy Azure Active Directory rendszergazdát Azure SQL Data Warehousehoz.
4. Ügyfélszámítógépek konfigurálása
5. Tárolt adatbázis-felhasználók létrehozása az adatbázisban az Azure AD-identitásokhoz rendelve
6. Kapcsolódás az adattárházhoz az Azure AD-identitások használatával

Jelenleg Azure Active Directory felhasználók nem jelennek meg a SSDT-Object Explorerokban. Megkerülő megoldásként tekintse meg a [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx)felhasználóit.

### <a name="find-the-details"></a>A részletek megkeresése
* A Azure Active Directory hitelesítés konfigurálásának és használatának lépései közel azonosak a Azure SQL Database és a Azure SQL Data Warehouse esetében. Azure Active Directory hitelesítés használatával kövesse a következő témakör részletes lépéseit: [csatlakozás SQL Database vagy SQL Data Warehousehoz](../sql-database/sql-database-aad-authentication.md).
* Hozzon létre egyéni adatbázis-szerepköröket, és adja hozzá a felhasználókat a szerepkörökhöz. Ezután adjon meg részletes engedélyeket a szerepkörökhöz. További információ: [első lépések az adatbázismotor engedélyeivel](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>További lépések
További információ az adatraktár lekérdezéséről a Visual Studio vagy egyéb alkalmazások használatával: [Query with Visual Studio][Query with Visual Studio] (Lekérdezés a Visual Studióval).

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
