---
title: "Kezelheti a személyes adatok Microsoft Azure-ban |} Microsoft Docs"
description: "útmutatás kijavításához frissítése, törlése és az Azure Active Directory és az Azure SQL Database személyes adatok exportálása"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.openlocfilehash: 3b57c92bd744644ea81878712b4272ed3ece4e2e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-personal-data-in-microsoft-azure"></a>A Microsoft Azure-ban személyes adatok kezelése

Ez a cikk nyújt útmutatást, frissítése, törlése, és az Azure Active Directory és az Azure SQL Database személyes adatok exportálása.

## <a name="scenario"></a>Forgatókönyv

Egy Dublin-alapú vállalati biztosít egy helyen a csúcskategóriás cél esküvő Írországban és a világ különböző mindkét helyi és nemzetközi ügyfél alap. Található, az ügyfelek, az alkalmazottak és szállítók teljes szolgáltatás a helyszínek kínálnak a világ rendelkeznek.

Egyebek között számos a vállalat nyomon követi az étele allergiák és étkezési beállításokat tartalmazó RSVPs. Lakodalmát vendégek regisztrálhatja különböző tevékenységek, például horseback haladás, barangolás, lakókocsik fel stb., és még egymással a központi weblapon az eseményt megelőző hónap során. A vállalat alkalmazottai, a szállítók, az ügyfelek és a lakodalmát vendégek személyes adatokat gyűjt. Az üzleti nemzetközi jellege miatt a vállalat több szinten szabályozás meg kell felelnie.

## <a name="problem-statement"></a>Probléma leírása

- Adatok rendszergazdák helyes-e személyes információkat és frissítési hiányos vagy változó személyes pontatlanná képesnek kell lennie.

- Adatok rendszergazdák törli a személyes adatokat a kérésre a adatok tulajdonos képesnek kell lennie.

- Adatok rendszergazdák exportálhatja az adatokat, és adja meg a saját kérésre közös, strukturált formátumban érintett kell.

## <a name="company-goals"></a>Vállalati célok

- Ügyfél hibás vagy hiányos, lakodalmát Vendég, alkalmazott és szállítói személyes adatokat kell javítani vagy frissítése az Azure Active Directory és az Azure SQL Database.

- Személyes adatokat törölni kell az Azure Active Directory és az Azure SQL Database egy érintett kérésére.

- Személyes adatok egy érintett kérésére közös, strukturált formátumban kell exportálni.

## <a name="solutions"></a>Megoldások

### <a name="azure-active-directory-rectifycorrect-inaccurate-or-incomplete-personal-data-and-erasedelete-personal-datauser-profiles"></a>Az Azure Active Directory: kijavításához vagy javítsa ki hibás vagy hiányos személyes adatokat, és a törlés vagy a személyes adatok és felhasználói profilok törlése

[Az Azure Active Directory](https://azure.microsoft.com/services/active-directory/) a Microsoft felhőalapú, több-bérlős directory és az identity management szolgáltatás.
Javítsa ki, frissíteni vagy törölni a felhasználói profilok felhasználói és az alkalmazottak és a felhasználói munkahelyi adatokat, amelyek tartalmazzák a személyes adatok, például a felhasználó nevét, munkahelyi cím, cím vagy telefonszám, a [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD-) környezet használatával a [Azure-portálon](https://portal.azure.com/).

A könyvtár egy globális rendszergazdai fiókkal kell bejelentkeznie.

#### <a name="how-do-i-correct-or-update-user-profile-and-work-information-in-azure-active-directory"></a>Hogyan javítsa vagy frissítse a felhasználói profil és a munkahelyi Azure Active Directoryban?

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.

2. Válassza ki **további szolgáltatások**, adja meg **felhasználók és csoportok** a szövegmezőbe, majd válassza ki azt a **Enter**.

    ![Media/image1.png](media/manage-personal-data-azure/image001.png)

3. Az a **felhasználók és csoportok** panelen válassza **felhasználók**.

    ![Media/image2.png](media/manage-personal-data-azure/image003.png)

4. Az a **felhasználók és csoportok - felhasználók** panelen válasszon ki egy felhasználót a listából, és ezt követően a kiválasztott felhasználó paneljén válassza **profil** frissíteni vagy javítani kell felhasználói profillal kapcsolatos információk megtekintéséhez.

    ![Media/image3.png](media/manage-personal-data-azure/image005.png)

5. Javítsa ki vagy frissíteni az adatait, és ezt követően a parancssávon válassza **mentéséhez.**

6.  A kiválasztott felhasználó paneljén válassza **munkahelyi adatai** megtekinteni a felhasználó munkahelyi adatokat, frissíteni vagy javítani kell.

    ![Media/image4.png](media/manage-personal-data-azure/image007.png)

7. Javítsa ki vagy frissíteni a felhasználó munkahelyi adatokat, és ezt követően a parancssávon válassza **mentéséhez.**

#### <a name="how-do-i-delete-a-user-profile-in-azure-active-directory"></a>Az Azure Active Directory felhasználói profil törlése

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.

2. Válassza ki **további szolgáltatások**, adja meg **felhasználók és csoportok** a szövegmezőbe, majd válassza ki azt a **Enter**.

    ![](media/manage-personal-data-azure/image001.png)

3. Az a **felhasználók és csoportok** panelen válassza **felhasználók**.

    ![Media/image2.png](media/manage-personal-data-azure/image003.png)

4. A **Felhasználók és csoportok – Felhasználók** panelen válasszon ki egy felhasználót a listából.

    ![Media/image3.png](media/manage-personal-data-azure/image007.png)

5. A kiválasztott felhasználó paneljén válassza **áttekintése**, majd a parancssávon válassza **törlése**.

    ![](media/manage-personal-data-azure/image013.png)

### <a name="sql-database-rectifycorrect-inaccurate-or-incomplete-personal-data-erasedelete-personal-data-export-personal-data"></a>Az SQL Database: kijavításához/helyesbítse hibás vagy hiányos személyes adatokat; a törlés vagy törlése a személyes adatok; személyes adatok exportálása 

[Az Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) egy felhő-adatbázis, amely a fejlesztőket létrehozása és alkalmazások karbantartása.

A személyes adatok frissíthető [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) szabványos SQL-lekérdezések, és azt is törölheti. Emellett a személyes adatok exportálhatja az SQL-adatbázis segítségével többféle módszerrel, beleértve az Azure SQL Server importálása és exportálása varázsló, és többféle formátumúak, beleértve a BACPAC fájl.

#### <a name="how-do-i-correct-update-or-erase-personal-data-in-sql-database"></a>Hogyan javítsa ki, frissítés, vagy törölheti a személyes adatokat az SQL-adatbázis?

Megtudhatja, hogyan javítsa vagy frissítse az SQL-adatbázis személyes adatait, látogasson el a [frissítés (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql), [szöveg frissítése](https://docs.microsoft.com/sql/t-sql/queries/updatetext-transact-sql), [közös Táblakifejezés frissíteni](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql), vagy [frissítése szöveg írása](https://docs.microsoft.com/sql/t-sql/queries/writetext-transact-sql) dokumentációját.

Személyes adatokat az SQL-adatbázis törlése megismeréséhez látogasson el a [törlése (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) dokumentációját.

#### <a name="how-do-i-export-personal-data-to-a-bacpac-file-in-sql-database"></a>Személyes adatok exportálása az SQL-adatbázis BACPAC fájlba

BACPAC-fájl tartalmazza az SQL-adatbázis adatait és a metaadatok, és egy zip-fájl BACPAC kiterjesztéssel. Ehhez használja a [Azure-portálon](https://portal.azure.com/), a SQLPackage parancssori segédprogram, az SQL Server Management Studio (SSMS) vagy a PowerShell.

Adatok exportálása fájlba BACPAC megismeréséhez látogasson el a [Azure SQL-adatbázis BACPAC fájlba exportálása](https://docs.microsoft.com/azure/sql-database/sql-database-export) lapon, amely az egyes módszerek fent felsorolt részletes utasításokat tartalmaz.

Személyes adatok exportálása az SQL-adatbázis SQL Server importálása és exportálása varázsló

Ezzel a varázslóval forrásból származó adatokat másolhat egy cél. A varázsló bevezető, többek között legyen, engedélyek információkat, és kérjen segítséget az eszközt, látogasson el a [adatok importálása és exportálása a SQL Server importálása és exportálása varázsló](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard) weblap.

A varázsló lépéseinek áttekintése, látogasson el a [lépéseit az SQL Server Importálás és exportálása varázsló](https://docs.microsoft.com/sql/integration-services/import-export-data/steps-in-the-sql-server-import-and-export-wizard) weblap.

## <a name="next-steps"></a>További lépések:

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) 

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

