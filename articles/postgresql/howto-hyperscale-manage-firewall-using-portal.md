---
title: Tűzfalszabályok kezelése - Nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Tűzfalszabályok létrehozása és kezelése az Azure Database for PostgreSQL - Hyperscale (Citus) számára az Azure Portal használatával
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 660c395e6cff81b0abcac07e66385f80a538695f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977539"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Tűzfalszabályok kezelése az Azure Database for PostgreSQL - Hyperscale (Citus) számára
A kiszolgálószintű tűzfalszabályok segítségével kezelhetők a megadott IP-címből vagy IP-címtartományból származó, nagy méretű (Citus) koordinátori csomóponthoz való hozzáférés.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- Kiszolgálócsoport [Létrehozása Azure Database for PostgreSQL – Hyperscale (Citus) kiszolgálócsoporthoz.](quickstart-create-hyperscale-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon

> [!NOTE]
> Ezek a beállítások a PostgreSQL – Hyperscale (Citus) kiszolgálócsoporthoz készült Azure Database létrehozása során is elérhetők. A **Hálózat** lapon kattintson a **Nyilvános végpont gombra.**
> ![Azure Portal – hálózatkezelés lap](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. A PostgreSQL kiszolgálócsoport lap Biztonság fejlécén kattintson a **Hálózat elemre** a tűzfalszabályok megnyitásához.

   ![Azure Portal – kattintson a Hálózatozás elemre](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. Kattintson az **Ügyfél IP hozzáadása**gombra az eszköztáron (az alábbi A lehetőség), vagy a hivatkozásban (B lehetőség). Mindkét esetben automatikusan létrehoz egy tűzfalszabályt a számítógép nyilvános IP-címével, az Azure-rendszer által érzékelt.

   ![Azure Portal – kattintson az Ügyfél IP-címének hozzáadása gombra](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

Másik lehetőségként a **+Add 0.0.0.0 - 255.255.255** (a B lehetőségtől jobbra) lehetőségre kattintva nem csak az IP-cím, hanem az egész internet is hozzáférhet a koordinátor csomópont 5432-es portjához. Ebben az esetben az ügyfeleknek továbbra is a megfelelő felhasználónévvel és jelszóval kell bejelentkezniük a fürt használatához. Mindazonáltal azt javasoljuk, hogy világszerte csak rövid ideig és csak nem éles adatbázisok számára engedélyezzük a hozzáférést világszerte.

3. A konfiguráció mentése előtt ellenőrizze az IP-címet. Bizonyos esetekben az Azure Portal által megfigyelt IP-cím eltér az internet és az Azure-kiszolgálók elérésekor használt IP-címtől. Ezért előfordulhat, hogy módosítania kell a Kezdő IP és a Záró IP-t, hogy a szabály a várt módon működjön.
   Használjon keresőmotort vagy más online eszközt saját IP-címének ellenőrzéséhez. Például keressen rá a "mi az én IP.".

   ![Bing keresés Mi az én IP](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Adjon hozzá további címtartományokat. A tűzfalszabályokban egyetlen IP-címet vagy címtartományt adhat meg. Ha a szabályt egyetlen IP-címre szeretné korlátozni, írja be ugyanazt a címet a Kezdő IP és a Záró IP mezőbe. A tűzfal megnyitása lehetővé teszi a rendszergazdák, felhasználók és alkalmazások számára, hogy hozzáférjenek a koordinátor csomópontjához az 5432-es porton.

5. A kiszolgálószintű tűzfalszabály mentéséhez kattintson az eszköztár **Mentés** gombjára. Várja meg a megerősítést, hogy a tűzfalszabályok frissítése sikeres volt.

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból

Az Azure-ban üzemeltetett alkalmazások (például egy Azure Web Apps-alkalmazás vagy az Azure virtuális gépen futó alkalmazások) egyszerű módon biztosíthatnak nagy kapacitású adatbázis-hozzáférést. Egyszerűen állítsa az **Azure-szolgáltatások és -erőforrások hozzáférésének engedélyezése a kiszolgálócsoporthoz** beállítást **Igen** értékre a **portálon** a Hálózat ablaktáblán, és nyomja le a **Mentés**gombot.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Meglévő kiszolgálószintű tűzfalszabályok kezelése az Azure Portalon
Ismételje meg a lépéseket a tűzfalszabályok kezeléséhez.
* Az aktuális számítógép hozzáadásához kattintson a gombra a + **Ügyfél IP hozzáadása gombra**. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* További IP-címek hozzáadásához adja meg a Szabály neve, a Kezdő IP-cím és a Záró IP-cím értékét. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály módosításához kattintson a szabály valamelyik mezőjére, és adja meg a módosításokat. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály törléséhez kattintson a három pontra [...] és a **Törlés** gombra a szabály eltávolításához. Kattintson a **Mentés** gombra a módosítások mentéséhez.

## <a name="next-steps"></a>További lépések
- További információ [a tűzfalszabályok ról](concepts-hyperscale-firewall-rules.md), többek között a csatlakozási problémák elhárításáról.
