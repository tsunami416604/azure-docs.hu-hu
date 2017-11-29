---
title: "Hozzon létre és kezelheti az Azure adatbázis tűzfalszabályok PostgreSQL |} Microsoft Docs"
description: "Hozzon létre és kezelheti az Azure-adatbázis PostgreSQL-tűzfalszabályok az Azure portál használatával"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: 248bd491bf688ff9b3ef4252c295989dc340b79c
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>Hozzon létre és kezelheti az Azure-adatbázis PostgreSQL-tűzfalszabályok az Azure portál használatával
Kiszolgálószintű tűzfal-szabályok lehetővé teszik a rendszergazdák az Azure-adatbázisának eléréséhez PostgreSQL-kiszolgáló a megadott IP-cím vagy az IP-címek. 

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- A kiszolgáló [PostgreSQL az Azure-adatbázis létrehozása](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon
1. PostgreSQL-kiszolgáló beállításai lapon elemcsoportban kattintson **kapcsolatbiztonsági** PostgreSQL megnyitandó a kapcsolat biztonsági beállításait tartalmazó lapot az Azure-adatbázishoz.

  ![Azure portál – kattintson a kapcsolat biztonságát](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Válassza ki **hozzáadása a saját IP** az eszköztáron. Ez a művelet automatikusan létrehozza a tűzfalszabályok a számítógép a nyilvános IP-címmel, az Azure rendszer által érzékelt.

  ![Azure portál – kattintson a saját IP-cím hozzáadása](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Az IP-cím ellenőrzése a konfiguráció mentéséhez. Bizonyos esetekben az IP-cím, az Azure portál által megfigyelt eltér az internetes és az Azure-kiszolgálók eléréséhez használt IP-címét. Emiatt előfordulhat, hogy módosítani szeretné a kezdő IP- és a záró IP-, a várt módon szabály függvény végrehajtásához.
Egy keresőmotor vagy más online eszköz használatával ellenőrizze a saját IP-cím (például a Bing keresési "Mi az a saját IP").

  ![Mi az a saját IP Bing keresése](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Adjon hozzá további címtartományok. Az adatbázisra vonatkozó tűzfalszabályok az Azure a PostgreSQL megadhatja a egyetlen IP-címet, vagy címtartományokat. Ha szeretné korlátozni a szabály, amely egyetlen IP-címnek, írja be ugyanazt a címet a mezőben a kezdő IP- és a záró IP. Megnyitásáról a tűzfal lehetővé teszi a rendszergazdák, a felhasználók és az alkalmazások a bejelentkezéshez a PostgreSQL-kiszolgálón, amelyhez bármely adatbázisra érvényes hitelesítő adatokkal rendelkezik.

  ![Azure portál – tűzfalszabályok ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Kattintson a **mentése** az eszköztáron menteni a kiszolgálószintű tűzfalszabály. Várja meg, hogy a tűzfalszabályok frissítése sikeres volt-e a jóváhagyást.

  ![Azure portál – válassza a Mentés](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)


## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Meglévő kiszolgálószintű tűzfalszabályok kezelése az Azure Portalon
Ismételje meg a tűzfal-szabályok kezelése.
* Az aktuális számítógép hozzáadásához kattintson a gombra kattintva + **hozzáadása a saját IP**. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* További IP-címek hozzáadásához adja meg a Szabály neve, a Kezdő IP-cím és a Záró IP-cím értékét. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály módosításához kattintson a szabály valamelyik mezőjére, és adja meg a módosításokat. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály törléséhez kattintson a három pont [...], és kattintson a **törlése** a szabály eltávolításához. Kattintson a **Mentés** gombra a módosítások mentéséhez.

## <a name="next-steps"></a>Következő lépések
- Ehhez hasonlóan is, a parancsfájl [hozzon létre és kezelheti az Azure-adatbázis Azure parancssori felület használatával PostgreSQL tűzfalszabályok](howto-manage-firewall-using-cli.md).
- Egy PostgreSQL-kiszolgáló Azure-adatbázishoz szeretne csatlakozni a témakörben talál segítséget [PostgreSQL az Azure-adatbázis adatkapcsolattárak](concepts-connection-libraries.md).
