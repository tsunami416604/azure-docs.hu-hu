---
title: Tűzfalszabályok kezelése - Azure portal – Azure Database for MySQL
description: Azure Database for MySQL tűzfalszabályok létrehozása és kezelése az Azure Portalhasználatával
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: edd6403ed3d7607eb96bc7c6a603c3fef8a4f99e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063548"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Azure Database for MySQL tűzfalszabályok létrehozása és kezelése az Azure Portal használatával
A kiszolgálószintű tűzfalszabályok segítségével kezelhetők a MySQL Server Azure-adatbázisához való hozzáférés egy megadott IP-címről vagy IP-címek tartományából. 

Virtuális hálózati (VNet) szabályok is használható a kiszolgálóhoz való hozzáférés biztonságossá tétele. További információ a [virtuális hálózati szolgáltatás végpontjainak és szabályainak létrehozásáról és kezeléséről az Azure Portal használatával.](howto-manage-vnet-using-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon

1. A MySQL-kiszolgáló lap Beállítások fejléce csoportban kattintson a **Kapcsolatbiztonság** elemre az Azure Database for MySQL Kapcsolatbiztonság lapjának megnyitásához.

   ![Azure Portal – kattintson a Kapcsolatbiztonság elemre](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Kattintson az eszköztár **Ip hozzáadása** gombjára. Ez automatikusan létrehoz egy tűzfalszabályt a számítógép nyilvános IP-címével, ahogy azt az Azure-rendszer érzékeli.

   ![Azure portal – kattintson a Saját IP hozzáadása gombra](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. A konfiguráció mentése előtt ellenőrizze az IP-címet. Bizonyos esetekben az Azure Portal által megfigyelt IP-cím eltér az internet és az Azure-kiszolgálók elérésekor használt IP-címtől. Ezért előfordulhat, hogy módosítania kell a Kezdő IP és a Záró IP-t, hogy a szabály a várt módon működjön.

   Használjon keresőmotort vagy más online eszközt saját IP-címének ellenőrzéséhez. Például keressen rá a "mi az IP-címem" kifejezésre.

4. Adjon hozzá további címtartományokat. A MySQL-alapú Azure Database tűzfalszabályaiban egyetlen IP-címet vagy címtartományt adhat meg. Ha a szabályt egyetlen IP-címre szeretné korlátozni, írja be ugyanazt a címet a Kezdő IP és a Záró IP mezőbe. A tűzfal megnyitása lehetővé teszi a rendszergazdák, a felhasználók és az alkalmazások számára, hogy hozzáférjenek a MySQL-kiszolgáló bármely olyan adatbázisához, amelyhez érvényes hitelesítő adatokkal rendelkeznek.

   ![Azure Portal – tűzfalszabályok](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. A kiszolgálószintű tűzfalszabály mentéséhez kattintson az eszköztár **Mentés** gombjára. Várja meg a megerősítést, hogy a tűzfalszabályok frissítése sikeres.

   ![Azure Portal – kattintson a Mentés gombra](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Ahhoz, hogy az Azure-ból származó alkalmazások csatlakozhassanak az Azure-adatbázishoz a MySQL-kiszolgálóhoz, engedélyezni kell az Azure-kapcsolatokat. Például egy Azure Web Apps-alkalmazás vagy egy Azure virtuális gépen futó alkalmazás üzemeltetéséhez, vagy egy Azure Data Factory adatkezelési átjáróból való csatlakozáshoz. Az erőforrásoknak nem kell ugyanabban a virtuális hálózatban (VNet) vagy erőforráscsoportban lenniük a tűzfalszabályhoz a kapcsolatok engedélyezéséhez. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. Az ilyen típusú kapcsolatok engedélyezésére többféle módszer is rendelkezésre áll. A 0.0.0.0 kezdő- és zárócímet tartalmazó tűzfalbeállítás jelzi, hogy ezek a kapcsolatok engedélyezettek. Másik lehetőségként beállíthatja az **Azure-szolgáltatások elérésének engedélyezése** a portálon a **portálon** **a** Kapcsolat biztonsági ablaktábláján, és nyomja meg a **Mentés**lehetőséget. Ha a csatlakozási kísérlet nem engedélyezett, a kérelem nem éri el az Azure Database for MySQL-kiszolgáló.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Meglévő kiszolgálószintű tűzfalszabályok kezelése az Azure Portal használatával
Ismételje meg a lépéseket a tűzfalszabályok kezeléséhez.
* Az aktuális számítógép hozzáadásához kattintson a **+ Saját IP hozzáadása gombra.** Kattintson a **Mentés** gombra a módosítások mentéséhez.
* További IP-címek hozzáadásához írja be a **SZABÁLY NEVE**, **START IP**és END **IP .** Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály módosításához kattintson a szabály bármelyik mezőjére, majd módosítsa. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály törléséhez kattintson a három pontra [...], majd a **Törlés gombra.** Kattintson a **Mentés** gombra a módosítások mentéséhez.


## <a name="next-steps"></a>További lépések
     Similarly, you can script to [Create and manage Azure Database for MySQL firewall rules using Azure CLI](howto-manage-firewall-using-cli.md).
     Further secure access to your server by [creating and managing Virtual Network service endpoints and rules using the Azure portal](howto-manage-vnet-using-portal.md).
        For help in connecting to an Azure     atabase for MySQL server, see [Connection libraries for Azure Database for MySQL](./concepts-connection-libraries.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        