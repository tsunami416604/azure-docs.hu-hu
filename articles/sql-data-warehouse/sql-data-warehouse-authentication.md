---
title: Hitelesítés az Azure SQL Data Warehouse |} A Microsoft Docs
description: Ismerje meg, hogyan hitelesítheti az Azure SQL Data Warehouse, Azure Active Directory (AAD) vagy az SQL Server-hitelesítés használatával.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: d082ba8bd2819450609a8a6e4ab41b4320158d4b
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307856"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse hitelesítése
Ismerje meg, hogyan hitelesítheti az Azure SQL Data Warehouse, Azure Active Directory (AAD) vagy az SQL Server-hitelesítés használatával.

Szeretne csatlakozni az SQL Data warehouse-ba, akkor meg kell adnia a biztonsági hitelesítő adatok hitelesítési célra. A kapcsolat létrehozása után egyes kapcsolati beállításai a lekérdezési munkamenetet létrehozó részeként.  

További információ a biztonsági és az adattárház kapcsolatok engedélyezése: [biztonságossá egy adatbázis az SQL Data Warehouse][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>SQL-hitelesítés
Csatlakozás az SQL Data Warehouse, a következő adatokat kell megadnia:

* Teljes kiszolgálónév
* Adja meg az SQL-hitelesítés
* Felhasználónév
* Jelszó
* Alapértelmezett adatbázis (nem kötelező)

Alapértelmezés szerint a kapcsolat csatlakozik a *fő* adatbázis és a nem a felhasználói adatbázishoz. Szeretne csatlakozni a felhasználói adatbázishoz, választhat, két dolog egyikét ehhez:

* Adja meg az alapértelmezett adatbázis, az SQL Server Object Explorer az SSDT-ben az ssms-ben, vagy az alkalmazás kapcsolati karakterláncában a kiszolgáló regisztrálásakor. Például az InitialCatalog paraméter egy ODBC-kapcsolat.
* Jelölje ki a felhasználói adatbázis az SSDT-ben a munkamenet létrehozása előtt.

> [!NOTE]
> A Transact-SQL utasítás **használata MyDatabase;** módosítása az adatbázis-kapcsolat esetén nem támogatott. Csatlakozás az SQL Data Warehouse és az SSDT együttes útmutatásért tekintse meg a [lekérdezés a Visual Studióval] [ Query with Visual Studio] cikk.
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Az Azure Active Directory (AAD) hitelesítés
[Az Azure Active Directory] [ What is Azure Active Directory] hitelesítés az identitások Azure Active Directoryban (Azure AD) használatával csatlakozik a Microsoft Azure SQL Data Warehouse egy mechanizmusa. Azure Active Directory-hitelesítéssel központilag kezelheti az identitásokat az adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen. Központi azonosítófelügyeleti biztosít az SQL Data Warehouse-felhasználók kezelése egy helyen, és egyszerűsíti az engedélyek kezelését. 

### <a name="benefits"></a>Előnyök
Az Azure Active Directory előnyöket nyújtják:

* Itt az SQL Server-hitelesítés helyett.
* Segít állítsa le a felhasználói identitások elterjedése adatbázis-kiszolgáló között.
* Lehetővé teszi, hogy a jelszóváltoztatás egyetlen helyen
* Külső (AAD-) csoportokat használ az adatbázis-engedélyek kezeléséhez.
* Integrált Windows-hitelesítés és egyéb Azure Active Directory által támogatott hitelesítési engedélyezésével kiküszöböli a jelszavak tárolását.
* Használja a tartalmazott adatbázis-felhasználók az adatbázis szintjén identitások hitelesítésére.
* Jogkivonat-alapú hitelesítés támogatja az alkalmazások csatlakoztatásához az SQL Data warehouse-bA.
* SQL Server Management Studio támogatja a többtényezős hitelesítést az Active Directory univerzális hitelesítéssel. A multi-factor Authentication szolgáltatás leírását lásd: [SSMS-támogatás az Azure AD MFA és az SQL Database és az SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Az Azure Active Directory még viszonylag újak és bizonyos korlátozások vonatkoznak. Győződjön meg arról, hogy az Azure Active Directory jó megoldás lehet a környezetben, lásd: [Azure AD-funkciók és korlátozások][Azure AD features and limitations], kifejezetten a további szempontok.
> 
> 

### <a name="configuration-steps"></a>Konfigurációs lépések
Kövesse az alábbi lépéseket az Azure Active Directory-hitelesítés konfigurálása.

1. Létre és tölthet fel egy Azure Active Directory
2. Választható lehetőség: Társítani, vagy módosítsa az active directory jelenleg társított Azure-előfizetésében
3. Hozzon létre egy Azure Active Directory-rendszergazda az Azure SQL Data warehouse-hoz.
4. Az ügyfélszámítógépek konfigurálása
5. Hozzon létre tartalmazottadatbázis-felhasználók az Azure AD-identitások leképezett adatbázis
6. Csatlakozás az adattárházhoz az Azure AD-identitások használatával

Jelenleg az Azure Active Directory-felhasználók nem jelennek meg az SSDT Object Explorerben. Áthidaló megoldásként a felhasználók megtekintéséhez [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Keresse meg a részleteket
* A lépéseket, konfigurálhatja és használhatja az Azure Active Directory-hitelesítés szinte azonosak az Azure SQL Database és az Azure SQL Data Warehouse. Kövesse a témakör részletes lépéseit [SQL Database vagy az SQL Data Warehouse által használatával az Azure Active Directory-hitelesítéssel csatlakozik](../sql-database/sql-database-aad-authentication.md).
* Hozzon létre egyéni adatbázis-szerepkörökhöz, és felhasználók hozzáadása a szerepkörökhöz. Ezután adja meg a szerepkörök részletes engedélyeket. További információkért lásd: [– első lépések a Database Engine engedélyekkel](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>További lépések
A Visual Studio és más alkalmazások az adatraktár lekérdezéséről, lásd: [lekérdezés a Visual Studióval][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
