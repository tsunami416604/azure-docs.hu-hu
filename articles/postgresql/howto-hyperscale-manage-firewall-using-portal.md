---
title: Tűzfalszabályok kezelése – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Tűzfalszabályok létrehozása és kezelése a Azure Database for PostgreSQL-nagy kapacitású (Citus) számára a Azure Portal használatával
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/12/2019
ms.openlocfilehash: c84616e8a9b9ff9722f5a104175c80c37dbcbcc3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116913"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-nagy kapacitású (Citus) tűzfalszabályok kezelése
A kiszolgálói szintű tűzfalszabályok segítségével kezelheti a nagy kapacitású (Citus) koordinátor-csomópontokhoz való hozzáférést egy adott IP-cím vagy IP-címtartomány használatával.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- A kiszolgálócsoport [létrehoz egy Azure Database for PostgreSQL – nagy kapacitású (Citus) kiszolgálói csoportot](quickstart-create-hyperscale-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon

> [!NOTE]
> Ezek a beállítások egy Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgálócsoport létrehozása során is elérhetők. A **hálózatkezelés** lapon kattintson a **nyilvános végpont**elemre.
> ![Azure Portal – hálózatkezelés lap](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. A PostgreSQL-kiszolgáló csoport lapon, a biztonság fejléc alatt kattintson a **hálózatkezelés** elemre a tűzfalszabályok megnyitásához.

   ![Azure Portal kattintson a hálózatkezelés elemre](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. Kattintson az **ügyfél IP-** címének hozzáadása elemre az eszköztáron (az alábbi kapcsolón) vagy a hivatkozásban (B. lehetőség). A automatikusan létrehoz egy tűzfalszabályot a számítógép nyilvános IP-címével, ahogyan azt az Azure-rendszer észleli.

   ![Azure Portal – kattintson az ügyfél IP-címének hozzáadása elemre.](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

Másik lehetőségként kattintson a **+ 0.0.0.0-255.255.255.255** (a B beállítástól jobbra) gombra, és nem csak az Ön IP-címét, hanem a teljes internetet a koordinátori csomópont 5432-as portjának eléréséhez. Ebben az esetben az ügyfeleknek továbbra is be kell jelentkezniük a megfelelő felhasználónévvel és jelszóval a fürt használatához. Azonban javasoljuk, hogy csak rövid ideig és csak a nem éles adatbázisok esetében engedélyezze a globális hozzáférést.

3. A konfiguráció mentése előtt ellenőrizze az IP-címet. Bizonyos helyzetekben a Azure Portal által megfigyelt IP-cím eltér az Internet és az Azure-kiszolgálók elérésekor használt IP-címről. Ezért előfordulhat, hogy módosítania kell a kezdő IP-címet és a záró IP-címet, hogy a szabály a várt módon működjön.
   A saját IP-címének vizsgálatához használjon keresőmotort vagy más online eszközt. Keressen például a "mi az én IP-címe" kifejezésre.

   ![Keresési Bing – mi az az IP-cím](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. További címtartományok hozzáadása. A tűzfalszabályok esetében egyetlen IP-címet vagy címtartományt is megadhat. Ha egyetlen IP-címhez szeretné korlátozni a szabályt, a kezdő IP-cím és a záró IP-cím mezőben adja meg ugyanazt a címet. A tűzfal megnyitása lehetővé teszi a rendszergazdák, a felhasználók és az alkalmazások számára, hogy hozzáférjenek a koordinátori csomóponthoz a 5432-es porton.

5. A kiszolgálói szintű tűzfalszabály mentéséhez kattintson az eszköztár **Mentés** gombjára. Várjon, amíg a rendszer megerősíti a tűzfalszabályok frissítésének sikerességét.

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból

Az Azure-ban üzemeltetett alkalmazásokhoz (például Azure Web Apps alkalmazásokhoz vagy Azure-beli virtuális gépekhez) egyszerűen biztosítható nagy kapacitású adatbázis-hozzáférés. Egyszerűen állítsa be az **Azure-szolgáltatások és-erőforrások elérésének engedélyezése a kiszolgálócsoport** számára beállítást **Igen** értékre a portálon a **hálózat** ablaktáblán, és kattintson a **Mentés**gombra.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Meglévő kiszolgálószintű tűzfalszabályok kezelése az Azure Portalon
Ismételje meg a lépéseket a tűzfalszabályok kezeléséhez.
* Az aktuális számítógép hozzáadásához kattintson a gombra az **ügyfél IP-címének hozzáadásához**. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* További IP-címek hozzáadásához adja meg a Szabály neve, a Kezdő IP-cím és a Záró IP-cím értékét. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály módosításához kattintson a szabály valamelyik mezőjére, és adja meg a módosításokat. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály törléséhez kattintson a három pont [...] elemre, majd a szabály eltávolításához kattintson a **Törlés** gombra. Kattintson a **Mentés** gombra a módosítások mentéséhez.

## <a name="next-steps"></a>Következő lépések
- További információ a [Tűzfalszabályok fogalmáról](concepts-hyperscale-firewall-rules.md), többek között a kapcsolódási problémák elhárításáról.
