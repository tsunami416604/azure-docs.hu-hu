---
title: Kiszolgálók Azure veremben üzemeltető MySQL |} Microsoft Docs
description: A MySQL Adapter erőforrás-szolgáltató használatával történő üzembe helyezéséhez MySQL példányok hozzáadása
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 5522eb1b8b0398aeb6f1b0dd8578b906880b4e89
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938411"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>A MySQL erőforrás-szolgáltató az üzemeltetési kiszolgáló hozzáadása

A MySQL példánya egy virtuális gép (VM) tárolhatja a [Azure verem](azure-stack-poc.md), vagy a virtuális gép Azure verem környezetét, amennyiben a MySQL erőforrás-szolgáltató csatlakozhat a példány kívül.

## <a name="connect-to-a-mysql-hosting-server"></a>Egy MySQL üzemeltetési kiszolgálóhoz kapcsolódni

Győződjön meg arról, hogy egy rendszergazdai jogosultságokkal rendelkező fiók hitelesítő adatait. Egy Állomáskiszolgálót hozzáadásához kövesse az alábbi lépéseket:

1. A szolgáltatás-rendszergazdaként jelentkezzen be a Azure verem operátor portálra
2. Válassza ki **további szolgáltatások**.
3. Válassza ki **felügyeleti erőforrások** > **üzemeltető kiszolgálók MySQL** > **+ Hozzáadás**. Ekkor megnyílik a **MySQL üzemeltető kiszolgáló hozzáadása** párbeszédpanel, az alábbi képernyőfelvételen látható.

   ![Üzemeltetési kiszolgáló konfigurálása](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Adja meg a MySQL-kiszolgálópéldány kapcsolat adatait.

   * A **MySQL futtató kiszolgáló neve**, adja meg a teljesen minősített tartománynevét (FQDN) vagy egy érvényes IPv4-címet. Ne használja a virtuális gép rövid nevét.
   * Egy alapértelmezett MySQL-példány nincs megadva, ezért meg kell adnia a **méretét az üzemeltetési kiszolgáló GB-ban**. Adja meg az adatbázis-kiszolgáló közel méretét.
   * Alapértelmezett beállítása **előfizetés**.
   * A **erőforráscsoport**, hozzon létre egy újat, vagy egy meglévő csoportot.

   > [!NOTE]
   > Ha a MySQL-példány hozzáférhet a bérlői és az Azure Resource Manager rendszergazda, használatba veheti az erőforrás-szolgáltató ellenőrzése alatt. De a MySQL-példány **kell** kizárólag az erőforrás-szolgáltató kiosztani.

5. Válassza ki **termékváltozatok** megnyitásához a **létrehozása SKU** párbeszédpanel.

   ![Hozzon létre egy MySQL Termékváltozat](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   A Termékváltozat **neve** a Termékváltozat tulajdonságainak tükröznie kell, így a felhasználók a megfelelő SKU tudjanak telepíteni az adatbázisok.

   >[!IMPORTANT]
   >Különleges karaktereket, szóközöket és időszakok, beleértve a nem támogatott **neve** vagy **réteg** létrehozásakor egy SKU a MySQL erőforrás-szolgáltató.

6. Válassza ki **OK** a Termékváltozat létrehozásához.
7. A **MySQL üzemeltető kiszolgáló hozzáadása**, jelölje be **létrehozása**.

Kiszolgálók hozzáadása, rendelje hozzá őket egy új vagy meglévő SKU szolgáltatásajánlatok megkülönböztetéséhez. Lehet például egy MySQL vállalati példányt, amely nagyobb adatbázis és az automatikus biztonsági mentésekhez. A szervezet különböző részlegeinek a nagy teljesítményű kiszolgáló foglalhat.

## <a name="increase-backend-database-capacity"></a>Háttérbeli adatbázis kapacitásbővítés

A háttérrendszer adatbázis kapacitása növelheti a verem Azure portálon több MySQL-kiszolgálók üzembe helyezésével. Ezek a kiszolgálók hozzáadása egy új vagy meglévő Termékváltozat. Ha a kiszolgáló hozzáadása egy meglévő SKU, győződjön meg arról, hogy a kiszolgáló jellemzőkkel ugyanazok, mint a többi kiszolgáló a Termékváltozat.

## <a name="make-mysql-database-servers-available-to-your-users"></a>MySQL-adatbázis-kiszolgálók elérhetővé a felhasználók számára

Csomagok és a MySQL adatbázis-kiszolgálók a felhasználók számára elérhetővé ajánlatok létrehozása. A Microsoft.MySqlAdapter szolgáltatás hozzáadása a terv, majd adja hozzá az alapértelmezett kvótát, vagy létrehozhat egy új kvótát.

![Csomagok és az adatbázisok ajánlatok létrehozása](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="next-steps"></a>További lépések

[MySQL-adatbázis létrehozása](azure-stack-mysql-resource-provider-databases.md)