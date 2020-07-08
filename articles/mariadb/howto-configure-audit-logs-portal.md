---
title: Hozzáférés-naplózási naplók – Azure Portal – Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni és elérni a naplókat a Azure Portal Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/24/2020
ms.openlocfilehash: f05f26c54d9f3ea62180c598aada59d405c3c805
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85362679"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Naplózási naplók konfigurálása és elérése a Azure Portal

A Azure Portal [Azure Database for MariaDB naplózási naplókat](concepts-audit-logs.md) és diagnosztikai beállításokat is konfigurálhat.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- [Azure Database for MariaDB kiszolgáló](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

>[!IMPORTANT]
> Azt javasoljuk, hogy csak azokat az eseményeket és felhasználókat naplózza, amelyek szükségesek a naplózáshoz, hogy a kiszolgáló teljesítménye ne legyen nagy hatással.

A naplózás engedélyezése és konfigurálása.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki a Azure Database for MariaDB-kiszolgálót.

1. Az oldalsáv **Beállítások** szakaszában válassza ki a **kiszolgáló paramétereit**.
    ![Kiszolgálóparaméterek](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Frissítse a **audit_log_enabled** PARAMÉTERt a következőre:.
    ![Naplók engedélyezése](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Válassza ki a naplózni kívánt [események típusát](concepts-audit-logs.md#configure-audit-logging) a **audit_log_events** paraméter frissítésével.
    ![Naplózási események](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Adja hozzá a naplózásból kizárandó MariaDB felhasználókat a **audit_log_exclude_users** paraméter frissítésével. Adja meg a felhasználókat a MariaDB felhasználónevének megadásával.
    ![Naplók kizárása a felhasználók számára](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. A paraméterek módosítása után kattintson a **Mentés**gombra. Vagy **elvetheti** a módosításokat.
    ![Mentés](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

1. Az oldalsáv **figyelés** területén válassza a **diagnosztikai beállítások**elemet.

1. Kattintson a "+ diagnosztikai beállítás hozzáadása" ![ diagnosztikai beállítás hozzáadása elemre.](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Adja meg a diagnosztikai beállítások nevét.

1. Itt adhatja meg, hogy mely adatnyelők küldje el a naplókat (Storage-fiók, Event hub és/vagy Log Analytics munkaterület).

1. Válassza a "MySqlAuditLogs" lehetőséget a napló típusaként.
![Diagnosztikai beállítás konfigurálása](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Miután konfigurálta az adattárolást a naplókba, kattintson a **Save (Mentés**) gombra.
![Diagnosztikai beállítás mentése](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. A naplókat úgy érheti el, hogy a konfigurált adattárolókban vizsgálja őket. A naplók megjelenése akár 10 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések

- További információ a Azure Database for MariaDB [naplózási naplóiról](concepts-audit-logs.md)
- Ismerje meg, hogyan konfigurálhatja a naplókat az [Azure CLI](howto-configure-audit-logs-cli.md) -ben