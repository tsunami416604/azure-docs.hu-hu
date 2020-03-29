---
title: Tűzfalszabályok kezelése - Azure portal - Azure Database for PostgreSQL - Single Server
description: Tűzfalszabályok létrehozása és kezelése az Azure Database for PostgreSQL-hez – Egyetlen kiszolgáló az Azure portal használatával
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: aeef22bf96221061a444f40e16e33343fafe511c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770305"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Tűzfalszabályok létrehozása és kezelése az Azure Database for PostgreSQL-hez – Egyetlen kiszolgáló az Azure portal használatával
A kiszolgálószintű tűzfalszabályok segítségével kezelheti a PostgreSQL Server Azure-adatbázisához való hozzáférést egy megadott IP-címről vagy IP-címtartományból.

Virtuális hálózati (VNet) szabályok is használható a kiszolgálóhoz való hozzáférés biztonságossá tétele. További információ a [virtuális hálózati szolgáltatás végpontjainak és szabályainak létrehozásáról és kezeléséről az Azure Portal használatával.](howto-manage-vnet-using-portal.md)

## <a name="prerequisites"></a>Előfeltételek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- Kiszolgáló [Létrehozása Azure-adatbázis a PostgreSQL számára](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon
1. A PostgreSQL kiszolgálólapján a Beállítások fejlécben kattintson a **Kapcsolat biztonsága** elemre az Azure Database for PostgreSQL kapcsolatbiztonsági lapjának megnyitásához.

   ![Azure Portal – kattintson a Kapcsolatbiztonság elemre](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Kattintson az eszköztár **Ip hozzáadása** gombjára. Ez automatikusan létrehoz egy tűzfalszabályt a számítógép nyilvános IP-címével, ahogy azt az Azure-rendszer érzékeli.

   ![Azure portal – kattintson a Saját IP hozzáadása gombra](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. A konfiguráció mentése előtt ellenőrizze az IP-címet. Bizonyos esetekben az Azure Portal által megfigyelt IP-cím eltér az internet és az Azure-kiszolgálók elérésekor használt IP-címtől. Ezért előfordulhat, hogy módosítania kell a Kezdő IP és a Záró IP-t, hogy a szabály a várt módon működjön.
   Használjon keresőmotort vagy más online eszközt saját IP-címének ellenőrzéséhez. Például keressen rá a "mi az én IP.".

   ![Bing keresés Mi az én IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Adjon hozzá további címtartományokat. A PostgreSQL Azure-adatbázis tűzfalszabályaiban megadhat egyetlen IP-címet vagy egy címtartományt. Ha a szabályt egyetlen IP-címre szeretné korlátozni, írja be ugyanazt a címet a Kezdő IP és a Záró IP mezőbe. A tűzfal megnyitása lehetővé teszi a rendszergazdák, felhasználók és alkalmazások számára, hogy hozzáférjenek a PostgreSQL kiszolgáló bármely olyan adatbázisához, amelyhez érvényes hitelesítő adatokkal rendelkeznek.

   ![Azure Portal – tűzfalszabályok](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. A kiszolgálószintű tűzfalszabály mentéséhez kattintson az eszköztár **Mentés** gombjára. Várja meg a megerősítést, hogy a tűzfalszabályok frissítése sikeres volt.

   ![Azure Portal – kattintson a Mentés gombra](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Ahhoz, hogy az Azure-ból származó alkalmazások csatlakozhassanak az Azure-adatbázishoz a PostgreSQL-kiszolgálóhoz, engedélyezni kell az Azure-kapcsolatokat. Például egy Azure Web Apps-alkalmazás vagy egy Azure virtuális gépen futó alkalmazás üzemeltetéséhez, vagy egy Azure Data Factory adatkezelési átjáróból való csatlakozáshoz. Az erőforrásoknak nem kell ugyanabban a virtuális hálózatban (VNet) vagy erőforráscsoportban lenniük a tűzfalszabályhoz a kapcsolatok engedélyezéséhez. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. Az ilyen típusú kapcsolatok engedélyezésére többféle módszer is rendelkezésre áll. A 0.0.0.0 kezdő- és zárócímet tartalmazó tűzfalbeállítás jelzi, hogy ezek a kapcsolatok engedélyezettek. Másik lehetőségként beállíthatja az **Azure-szolgáltatások elérésének engedélyezése** a portálon a **portálon** **a** Kapcsolat biztonsági ablaktábláján, és nyomja meg a **Mentés**lehetőséget. Ha a csatlakozási kísérlet nem engedélyezett, a kérelem nem éri el az Azure Database for PostgreSQL-kiszolgáló.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Meglévő kiszolgálószintű tűzfalszabályok kezelése az Azure Portalon
Ismételje meg a lépéseket a tűzfalszabályok kezeléséhez.
* Az aktuális számítógép hozzáadásához kattintson a gombra a + **Add My IP**. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* További IP-címek hozzáadásához adja meg a Szabály neve, a Kezdő IP-cím és a Záró IP-cím értékét. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály módosításához kattintson a szabály valamelyik mezőjére, és adja meg a módosításokat. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály törléséhez kattintson a három pontra [...] és a **Törlés** gombra a szabály eltávolításához. Kattintson a **Mentés** gombra a módosítások mentéséhez.

## <a name="next-steps"></a>További lépések
- Hasonlóképpen parancsfájlt is parancsfájlt készíthet [a PostgreSQL tűzfalszabályaihoz készült Azure Database for PostgreSQL tűzfalszabályok létrehozásához és kezeléséhez az Azure CLI használatával.](howto-manage-firewall-using-cli.md)
- További biztonságos hozzáférés a kiszolgálóhoz [virtuális hálózati szolgáltatás végpontjainak és szabályainak létrehozásával és kezelésével az Azure Portal használatával.](howto-manage-vnet-using-portal.md)
- Ha segítségre van szüksége a PostgreSQL-adatbázishoz való csatlakozáshoz, olvassa el [a Kapcsolattárak az Azure Database for PostgreSQL kapcsolattárak](concepts-connection-libraries.md)című témakört.
