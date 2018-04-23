---
title: Hozzon létre és tűzfalszabályok MySQL az Azure-adatbázisban kezelheti a MySQL
description: Hozzon létre és kezelheti az Azure-adatbázis MySQL tűzfalszabályokat az Azure portál használatával
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6475c5c3ecb43352a8ef8db8fe1c023a16cd8a3d
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Létrehozása és kezelése az Azure-adatbázis a MySQL tűzfalszabályok az Azure portál használatával
Kiszolgálószintű tűzfal-szabályok lehetővé teszik a rendszergazdák az Azure-adatbázisának eléréséhez MySQL-kiszolgáló a megadott IP-cím vagy egy adott IP-címeket. 

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon

1. A MySQL-kiszolgáló a beállítások lapon elemcsoportban kattintson **kapcsolatbiztonsági** nyissa meg a kapcsolat biztonsági beállításait tartalmazó lapot a MySQL az Azure-adatbázis számára.

   ![Azure portál – kattintson a kapcsolat biztonságát](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Kattintson a **hozzáadása a saját IP** az eszköztáron. Ez automatikusan létrehozza a tűzfalszabályok a számítógép a nyilvános IP-címmel, az Azure rendszer által érzékelt.

   ![Azure portál – kattintson a saját IP-cím hozzáadása](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Az IP-cím ellenőrzése a konfiguráció mentéséhez. Bizonyos esetekben az IP-cím, az Azure portál által megfigyelt eltér az internetes és az Azure-kiszolgálók eléréséhez használt IP-címét. Emiatt előfordulhat, hogy módosítani szeretné a kezdő IP- és a záró IP-, a várt módon szabály függvény végrehajtásához.

   Egy keresőmotor vagy más online eszközt használja a saját IP-cím ellenőrzése. Keresse meg például, "Mi az az IP-cím". 

   ![Mi az a saját IP Bing](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Adjon hozzá további címtartományok. Az adatbázisra vonatkozó tűzfalszabályok az Azure a MySQL megadhatja a egyetlen IP-cím vagy címtartományokat. Ha szeretné korlátozni a szabály, amely egyetlen IP-címnek, írja be ugyanazt a címet a kezdő IP- és a záró IP-Címnél. A tűzfal megnyitása lehetővé teszi a rendszergazdák, a felhasználók és az alkalmazás minden rendelkeznek érvényes hitelesítő adatokat a MySQL kiszolgálón-adatbázisának eléréséhez.

   ![Azure portál – tűzfalszabályok ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Kattintson a **mentése** az eszköztáron menteni a kiszolgálószintű tűzfalszabály. Várjon, amíg a megerősítést, hogy a frissítés, hogy a tűzfalszabályok sikeres.

   ![Azure portál – válassza a Mentés](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Lehetővé teszik az alkalmazások az Azure-ból a MySQL-kiszolgáló Azure-adatbázishoz való kapcsolódáshoz, Azure-kapcsolatok engedélyezni kell. Például egy Azure Web Apps alkalmazást vagy olyan alkalmazás, amely egy Azure virtuális gép üzemeltetésére, vagy csatlakoztassa egy Azure Data Factory az adatkezelési átjáró. Az erőforrások nem kell ugyanazt a virtuális hálózatot (VNet) vagy a tűzfalszabály tartozó erőforráscsoport ahhoz, hogy ezeket a kapcsolatokat. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. Többféle módszer ahhoz, hogy ilyen típusú kapcsolatokat. A 0.0.0.0 kezdő- és zárócímet tartalmazó tűzfalbeállítás jelzi, hogy ezek a kapcsolatok engedélyezettek. Beállíthatja azt is megteheti, a **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** lehetőséggel **ON** a portálon a **kapcsolatbiztonsági** ablaktáblán, és nyomja le **mentése**. A kapcsolódási kísérlet nem engedélyezett, ha a kérelem nem éri el a MySQL-kiszolgálóhoz tartozó Azure-adatbázis.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Meglévő kiszolgálószintű tűzfal-szabályok kezelése az Azure-portál használatával
Ismételje meg a tűzfal-szabályok kezelése.
* Az aktuális számítógép hozzáadásához kattintson **+ saját IP-cím hozzáadása**. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* További IP-cím hozzáadásához írja be a **SZABÁLYNÉV**, **KEZDŐ IP-**, és **záró IP-Címnél**. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály módosításához kattintson a szabály a mezőket, és módosítsa. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály törléséhez kattintson a három pont [...], majd **törlése**. Kattintson a **Mentés** gombra a módosítások mentéséhez.


## <a name="next-steps"></a>További lépések
- Ehhez hasonlóan is, a parancsfájl [hozzon létre és kezelheti az Azure-adatbázis Azure parancssori felület használatával MySQL tűzfalszabályok](howto-manage-firewall-using-cli.md).
- A MySQL-kiszolgáló egy Azure-adatbázishoz szeretne csatlakozni, lásd: [adatkapcsolattárak MySQL az Azure-adatbázis](./concepts-connection-libraries.md)
