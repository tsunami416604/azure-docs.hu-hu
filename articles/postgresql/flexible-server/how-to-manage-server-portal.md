---
title: Kiszolgáló – Azure Portal-Azure Database for PostgreSQL – rugalmas kiszolgáló kezelése
description: Megtudhatja, hogyan kezelheti Azure Database for PostgreSQL-rugalmas kiszolgálót a Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 13d78110f50d6ce72b8525914ed0e91dfcadd2cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935675"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Azure Database for PostgreSQL rugalmas kiszolgáló kezelése a Azure Portal használatával

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Ez a cikk bemutatja, hogyan kezelheti a Azure Database for PostgreSQL-rugalmas kiszolgálót. A felügyeleti feladatok közé tartozik a számítási és tárolási skálázás, a rendszergazdai jelszó alaphelyzetbe állítása és a kiszolgáló adatainak megtekintése.

## <a name="sign-in"></a>Bejelentkezés

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg a rugalmas kiszolgálói erőforrást a Azure Portal.

## <a name="scale-compute-and-storage"></a>Számítási és tárolási méretezés

A kiszolgáló létrehozása után a különböző [díjszabási szintek](https://azure.microsoft.com/pricing/details/postgresql/) között méretezheti a változó igényeket. A számítási és a memóriát a virtuális mag növelésével vagy csökkentésével is csökkentheti.

> [!NOTE]
> A tárterület nem méretezhető le alacsonyabb értékre.

1. Válassza ki a kiszolgálót a Azure Portal. Válassza a **számítás és tárolás**elemet, amely a **Beállítások** szakaszban található.
2. Megváltoztathatja a **számítási szintet** , a **virtuális mag**, a **tárterületet** a kiszolgáló vertikális felskálázásához magasabb számítási szinten, vagy akár ugyanazon a szinten, azáltal, hogy növeli a tárhelyet vagy a virtuális mag a kívánt értékre.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="a Storage rugalmas kiszolgáló méretezése":::

> [!Important]
> - A tárterület nem méretezhető le.
> - A skálázás virtuális mag a kiszolgáló újraindítását okozza.

3. A módosítások mentéséhez kattintson **az OK gombra** .

## <a name="reset-admin-password"></a>Rendszergazdai jelszó visszaállítása

A rendszergazdai szerepkör jelszava a Azure Portal használatával módosítható.

1. Válassza ki a kiszolgálót a Azure Portal. Az **Áttekintés** ablakban válassza a **jelszó alaphelyzetbe állítása**lehetőséget.
2. Adjon meg egy új jelszót, és erősítse meg a jelszót. A szövegmező a jelszó bonyolultságára vonatkozó követelményeket fogja kérni.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="a Storage rugalmas kiszolgáló méretezése":::

3. Az új jelszó mentéséhez kattintson a **Mentés** gombra.

## <a name="delete-a-server"></a>Kiszolgáló törlése

Ha már nincs szüksége rá, törölheti a kiszolgálót.

1. Válassza ki a kiszolgálót a Azure Portal. Az **Áttekintés** ablakban válassza a **Törlés**lehetőséget.
2. Írja be a kiszolgáló nevét a beviteli mezőbe annak megerősítéséhez, hogy törölni kívánja a kiszolgálót.

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="a Storage rugalmas kiszolgáló méretezése":::

   > [!IMPORTANT]
   > A kiszolgáló törlése visszafordíthatatlan.

  > [!div class="mx-imgBorder"]
  > ![a rugalmas kiszolgáló törlése](./media/howto-manage-server-portal/delete-server.png)  

3. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések

- [A biztonsági mentési és visszaállítási fogalmak ismertetése](concepts-backup-restore.md)
- [A kiszolgáló hangolása és figyelése](concepts-monitoring.md)
