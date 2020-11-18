---
title: Nagyobb verziófrissítés – Azure Portal-Azure Database for MySQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan frissíthető a Azure Database for MySQL-Single Server főverziója Azure Portal használatával
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 78c35e42cefa8897d9f93c3a941b4c0e8b81e5f9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686955"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>A főverzió frissítése Azure Database for MySQL egyetlen kiszolgálón a Azure Portal használatával

> [!IMPORTANT]
> Az Azure Database for MySQL egyetlen kiszolgálójának főverziójának frissítése nyilvános előzetes verzióban érhető el.

Ez a cikk azt ismerteti, hogyan frissítheti a MySQL főverzióját helyben, Azure Database for MySQL egyetlen kiszolgálón.

Ez a funkció lehetővé teszi, hogy az ügyfelek a MySQL 5,6-kiszolgálókat a MySQL 5,7-re helyben, az adatmozgatás nélkül, vagy az alkalmazás-kapcsolódási sztringek megváltozásának szükségességére kattintva végezzék.

> [!Note]
> * A főverzió frissítése csak a MySQL 5,6-ből a MySQL 5,7-re való frissítés főverziójának frissítése esetén érhető el<br>
> * A főverzió frissítése még nem támogatott a replika-kiszolgálón.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for MySQL egyetlen kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Főverzió frissítésének végrehajtása a MySQL 5,6-ből a MySQL 5,7-be

Az alábbi lépéseket követve elvégezheti a MySQL 5,6-kiszolgáló Azure-adatbázisának verziófrissítését

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MySQL 5,6-kiszolgálót.

2. Az **Áttekintés** lapon kattintson a **frissítés** gombra az eszköztáron.

3. A **frissítés** szakaszban kattintson az **OK** gombra az Azure Database for MySQL 5,6-kiszolgáló frissítéséhez a 5,7 Serverre.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL – áttekintés – frissítés":::

4. Egy értesítés megerősíti, hogy a frissítés sikeres volt.

## <a name="next-steps"></a>Következő lépések

További információ a [Azure Database for MySQL verziószámozási szabályzatáról](concepts-version-policy.md).