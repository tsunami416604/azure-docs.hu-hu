---
title: Létrehozása és kezelése a tűzfalszabályok az Azure Database for postgresql-hez
description: Hozzon létre és kezelhető az Azure Database for PostgreSQL tűzfalszabályok az Azure portal használatával
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 7211b7f5db14f4bad114556e5c080cf5d535b00b
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545484"
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>Hozzon létre és kezelhető az Azure Database for PostgreSQL tűzfalszabályok az Azure portal használatával
Kiszolgálószintű tűzfalszabályok lehetővé teszik a rendszergazdák eléréséhez egy Azure Database for PostgreSQL-kiszolgáló megadott IP-cím vagy IP-címtartományt. 

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- A kiszolgáló [hozzon létre egy Azure Database for postgresql-hez](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon
1. A PostgreSQL kiszolgáló, a beállítások lapon szakaszban kattintson **kapcsolatbiztonság** megnyitásához a kapcsolatbiztonság lapon az Azure Database for PostgreSQL-hez.

  ![Az Azure portal – kapcsolatbiztonság kattintson](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Kattintson a **adjon hozzá saját IP-címet** az eszköztáron. Ez automatikusan hoz létre egy tűzfalszabályt a számítógép nyilvános IP-címét, az Azure rendszer által érzékelt.

  ![Az Azure portal – kattintson a Hozzáadás saját IP-cím](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Az IP-cím ellenőrzése a konfiguráció mentése előtt. Bizonyos esetekben az Azure portal által megfigyelt IP-cím eltér az internetes és az Azure-kiszolgálókkal való elérésekor használt IP-cím. Emiatt előfordulhat, hogy módosítani szeretné a kezdő IP- és a záró IP-cím, hogy a szabály az elvárt módon működnek.
Egy keresőmotor vagy egyéb online eszközt használja a saját IP-cím ellenőrzése. Például keresse meg "Mi az saját IP-cím."

  ![Mi az a saját IP-cím a Bing keresése](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. További címtartomány hozzáadásához. A tűzfalszabályok az Azure Database for PostgreSQL, az egyetlen IP-címet vagy címtartományt is megadhat. Ha szeretné korlátozni a szabályt, hogy egyetlen IP-címet, írja be ugyanazt a címet a mezőben a kezdő IP- és a záró IP-cím. A tűzfal megnyitása után lehetővé teszi a rendszergazdák, a felhasználók és alkalmazások számára, hogy jelentkezzen be bármely olyan adatbázisába a PostgreSQL-kiszolgáló, amelyhez érvényes hitelesítő adatokkal rendelkeznek.

  ![Az Azure portal - tűzfalszabályok ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Kattintson a **mentése** gombra az eszköztárban, a kiszolgálószintű tűzfalszabály mentéséhez. Várjon, amíg a visszaigazolás, hogy a tűzfalszabályok frissítése sikeres volt-e.

  ![Az Azure portal – kattintson a Mentés gombra](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Ahhoz, hogy az Azure-alkalmazások az Azure Database for PostgreSQL-kiszolgálóhoz való kapcsolódáshoz, engedélyezni kell az Azure-kapcsolatokat. Ha például üzemeltetése az Azure Web Apps-alkalmazáshoz, vagy egy Azure-beli virtuális gépen futó alkalmazást, vagy egy Azure Data Factory az adatkezelési átjáró csatlakozni. Az erőforrások nem kell ugyanazon a virtuális hálózaton (VNet) vagy a tűzfalszabály erőforráscsoportjának ahhoz, hogy ezeket a kapcsolatokat lehet. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. Van néhány módszerek ilyen típusú kapcsolatok engedélyezéséhez. A 0.0.0.0 kezdő- és zárócímet tartalmazó tűzfalbeállítás jelzi, hogy ezek a kapcsolatok engedélyezettek. Beállíthatja azt is megteheti, a **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítást **ON** a portálon a **kapcsolatbiztonság** ablaktáblán, majd nyomja le **mentése**. A csatlakozási kísérlet nem engedélyezett, ha a kérés nem éri el az Azure Database for PostgreSQL-kiszolgálóhoz.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Meglévő kiszolgálószintű tűzfalszabályok kezelése az Azure Portalon
Ismételje meg a tűzfalszabályok kezelésére.
* Az aktuális számítógép hozzáadásához kattintson a gombra kattintva + **adjon hozzá saját IP-címet**. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* További IP-címek hozzáadásához adja meg a Szabály neve, a Kezdő IP-cím és a Záró IP-cím értékét. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály módosításához kattintson a szabály valamelyik mezőjére, és adja meg a módosításokat. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály törléséhez kattintson a három pont [...], és kattintson a **törlése** eltávolítani a szabályt. Kattintson a **Mentés** gombra a módosítások mentéséhez.

## <a name="next-steps"></a>További lépések
- Hasonló módon is, a parancsfájl [hozzon létre és kezelhető az Azure Database for PostgreSQL-tűzfalszabályok Azure CLI-vel](howto-manage-firewall-using-cli.md).
- Segítségre van szüksége az Azure Database for PostgreSQL-kiszolgálóhoz csatlakozik, lásd: [adatkapcsolattárak az Azure Database for PostgreSQL](concepts-connection-libraries.md).
