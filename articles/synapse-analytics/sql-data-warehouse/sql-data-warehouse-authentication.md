---
title: Hitelesítés
description: Ismerje meg, hogyan hitelesítheti magát az Azure Synapse Analytics az Azure Active Directory (Azure AD) vagy az SQL Server-hitelesítés használatával.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: ccc5db828a03c37d3fc4f49b13883ac3eeda2368
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584231"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Hitelesítés az Azure Synapse Analytics szolgáltatásban

Ismerje meg, hogyan hitelesítheti magát a Synapse SQL poool az Azure Synapse az Azure Active Directory (Azure AD) vagy az SQL Server-hitelesítés használatával.

Az SQL-készlethez való csatlakozáshoz hitelesítési célból meg kell adnia a biztonsági hitelesítő adatokat. A kapcsolat létrehozásakor bizonyos kapcsolatbeállítások a lekérdezési munkamenet létrehozásának részeként vannak konfigurálva.  

A biztonságról és az adattárházhoz való csatlakozás engedélyezéséről az [adatbázis dokumentációjának védelme](sql-data-warehouse-overview-manage-security.md)című témakörben olvashat bővebben.

## <a name="sql-authentication"></a>SQL-hitelesítés

Az SQL-készlethez való csatlakozáshoz a következő adatokat kell megadnia:

* Teljesen minősített kiszolgálónév
* SQL-hitelesítés megadása
* Felhasználónév
* Jelszó
* Alapértelmezett adatbázis (nem kötelező)

Alapértelmezés szerint a kapcsolat a *fő* adatbázishoz csatlakozik, nem pedig a felhasználói adatbázishoz. A felhasználói adatbázishoz való csatlakozáshoz két lehetőség közül választhat:

* Adja meg az alapértelmezett adatbázist, amikor regisztrálja a kiszolgálót az SQL Server Object Explorer ssdt, SSMS vagy az alkalmazás kapcsolati karakterláncában. Adja meg például egy ODBC-kapcsolat InitialCatalog paraméterét.
* A munkamenet SSDT-ben való létrehozása előtt jelölje ki a felhasználói adatbázist.

> [!NOTE]
> A TRANSact-SQL utasítás **USE MyDatabase;** nem támogatott a kapcsolat adatbázisának módosítása. Az SSDT-vel rendelkező SQL-készlethez való csatlakozásról a [Query with Visual Studio](sql-data-warehouse-query-visual-studio.md) cikkben olvashat.
> 
> 

## <a name="azure-active-directory-azure-ad-authentication"></a>Azure Active Directory (Azure AD) hitelesítés

[Az Azure Active Directory-hitelesítés](../../active-directory/fundamentals/active-directory-whatis.md) az SQL-készlethez való csatlakozás mechanizmusa az Azure Active Directoryban (Azure AD) lévő identitások használatával. Az Azure Active Directory-hitelesítéssel központilag kezelheti az adatbázis-felhasználók és más Microsoft-szolgáltatások identitásait egy központi helyen. A központi azonosítókezelés egyetlen helyet biztosít az Azure Synapse-felhasználók kezeléséhez, és leegyszerűsíti az engedélykezelést. 

### <a name="benefits"></a>Előnyök

Az Azure Active Directory előnyei a következők:

* Az SQL Server-hitelesítés alternatívája.
* Segít megállítani a felhasználói identitások elterjedését az adatbázis-kiszolgálók között.
* Központi helyet biztosít a jelszóváltoztatáshoz
* Adatbázis-engedélyek kezelése külső (Azure AD) csoportok használatával.
* Megszünteti a jelszavak tárolását az integrált Windows-hitelesítés és az Azure Active Directory által támogatott egyéb hitelesítési formák engedélyezésével.
* A tartalmazott adatbázis-felhasználók segítségével hitelesíti az identitásokat az adatbázis szintjén.
* Támogatja a jogkivonat-alapú hitelesítést az SQL-készlethez csatlakozó alkalmazások számára.
* Támogatja a többtényezős hitelesítést az Active Directory univerzális hitelesítésen keresztül különböző eszközökhöz, például [az SQL Server Management Studio](../../sql-database/sql-database-ssms-mfa-authentication.md) és az SQL Server Data Tools [szolgáltatáshoz.](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json)

> [!NOTE]
> Az Azure Active Directory még viszonylag új, és bizonyos korlátozásokkal rendelkezik. Annak érdekében, hogy az Azure Active Directory megfelelő legyen a környezetéhez, tekintse meg az [Azure AD-funkciók és -korlátozások](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)című témakört, különösen a További szempontok című témakört.
> 
> 

### <a name="configuration-steps"></a>Konfigurációs lépések

Az Azure Active Directory-hitelesítés konfigurálásához kövesse az alábbi lépéseket.

1. Azure Active Directory létrehozása és feltöltése
2. Nem kötelező: Az Azure-előfizetéshez jelenleg társított active directory társítása vagy módosítása
3. Azure Active Directory-rendszergazda létrehozása az Azure Synapse számára
4. Az ügyfélszámítógépek konfigurálása
5. Az Azure AD-identitásokhoz leképezett adatbázis-felhasználók létrehozása az adatbázisban
6. Csatlakozás az SQL-készlethez az Azure AD-identitások használatával

Jelenleg az Azure Active Directory-felhasználók nem jelennek meg az SSDT Object Explorerben. Kerülő megoldásként tekintse meg a felhasználókat a [sys.database_principals.](https://msdn.microsoft.com/library/ms187328.aspx)

### <a name="find-the-details"></a>A részletek megkeresése

* Az Azure Active Directory-hitelesítés konfigurálásának és használatának lépései közel azonosak az Azure SQL Database és a Synapse SQL-készlet esetében az Azure Synapse-ban. Kövesse az [SQL-adatbázishoz vagy az SQL-készlethez való csatlakozás az Azure Active Directory-hitelesítés használatával](../../sql-database/sql-database-aad-authentication.md)című témakör részletes lépéseit.
* Hozzon létre egyéni adatbázis-szerepköröket, és adjon hozzá felhasználókat a szerepkörökhöz. Ezután adja meg a szerepkörök részletes engedélyeket. További információt az [Adatbázis-kezelő motor engedélyeinek első lépései című témakörben talál.](https://msdn.microsoft.com/library/mt667986.aspx)

## <a name="next-steps"></a>További lépések

A Lekérdezés a Visual Studio és más alkalmazások segítségével a [Lekérdezés a Visual Studióval](sql-data-warehouse-query-visual-studio.md)menüben található.
