---
title: Konfigurálja és hozzáférési naplók az Azure Database for MySQL-hez az Azure Portalon
description: Ez a cikk azt ismerteti, konfigurálása és elérése a vizsgálati naplók az Azure Database MySQL-hez az Azure Portalról.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: a2f44b52c6d34adb1bebf666ff8453b17f7778a5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448490"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Konfigurálja és hozzáférési naplók az Azure Portalon

Konfigurálhatja a [, Azure Database for MySQL-auditnaplók](concepts-audit-logs.md) és a diagnosztikai beállításokat az Azure Portalon.

> [!IMPORTANT]
> Auditálási napló funkció jelenleg előzetes verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató lépéseinek, az alábbiak szükségesek:

- [Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

Engedélyezze és konfigurálja a naplózásra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki az Azure Database for MySQL-kiszolgálóhoz.

1. Alatt a **beállítások** szakasz az oldalsávon válassza **kiszolgáló paramétereinek**.
    ![Kiszolgálói paraméterek](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Frissítés a **audit_log_enabled** paraméter ON értékre állítása.
    ![Engedélyezze a vizsgálati naplók](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Válassza ki a [eseménytípusok](concepts-audit-logs.md#configure-audit-logging) frissítésével be kell jelentkeznie a **audit_log_events** paraméter.
    ![Alkalmazásnapló-események naplózása](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Bármely frissítésével naplózási kizárandók MySQL-felhasználók hozzáadása a **audit_log_exclude_users** paraméter. Adja meg a felhasználók azáltal, hogy a MySQL-felhasználó nevét.
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

- Tudjon meg többet [auditnaplók](concepts-audit-logs.md) az Azure Database for MySQL-hez.