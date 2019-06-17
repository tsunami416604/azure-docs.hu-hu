---
title: Konfigurálja és hozzáférési naplók az Azure Database for MariaDB az Azure Portalon
description: Ez a cikk bemutatja, hogyan konfigurálásához és a vizsgálati naplók, Azure Database-ben eléréséhez a MariaDB-hez az Azure Portalról.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: c9ee106972cc78a08709d2ce55d2dfddc96edbf7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079235"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Konfigurálja és hozzáférési naplók az Azure Portalon

Konfigurálhatja a [, Azure Database for MariaDB auditnaplók](concepts-audit-logs.md) és a diagnosztikai beállításokat az Azure Portalon.

> [!IMPORTANT]
> Auditálási napló funkció jelenleg előzetes verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató lépéseinek, az alábbiak szükségesek:

- [Azure Database for MariaDB-kiszolgáló](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

Engedélyezze és konfigurálja a naplózásra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki az Azure Database for MariaDB-kiszolgáló.

1. Alatt a **beállítások** szakasz az oldalsávon válassza **kiszolgáló paramétereinek**.
    ![Kiszolgálói paraméterek](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Frissítés a **audit_log_enabled** paraméter ON értékre állítása.
    ![Engedélyezze a vizsgálati naplók](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Válassza ki az események frissítésével be kell jelentkeznie a **audit_log_events** paraméter.
    ![Alkalmazásnapló-események naplózása](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Bármely frissítésével naplózási kizárandók MariaDB-felhasználók hozzáadása a **audit_log_exclude_users** paraméter. Adja meg a felhasználók a MariaDB felhasználói név megadásával.
    ![Naplózási kizárási felhasználói](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Miután módosította a paramétereket, kattinthat **mentése**. Vagy beállíthatja a **elveti** a módosításokat.
    ![Mentés](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

1. Alatt a **figyelés** szakasz az oldalsávon válassza **diagnosztikai beállítások**.

1. Kattintson a "+ diagnosztikai beállítás hozzáadása" ![diagnosztikai beállítás hozzáadása](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Adja meg a diagnosztikai beállítás nevét.

1. Adja meg, mely adatokat küldeni a vizsgálati naplók (storage-fiók, az eseményközpont, és/vagy Log Analytics-munkaterület) fogadók.

1. Válassza ki a napló típusa "MySqlAuditLogs".
![Diagnosztikai beállítás konfigurálása](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Miután do kanálu a naplókat az adatfogadókba konfigurálta, rákattinthat **mentése**.
![Diagnosztikai beállítás mentése](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Az auditnaplók eléréséhez az adatfogadókba konfigurálta a áttekintésével. Előfordulhat, hogy megjelenjenek a naplók akár 10 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [auditnaplók](concepts-audit-logs.md) MariaDB-hez készült Azure Database-ben.