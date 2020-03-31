---
title: Hozzáférés naplóihoz – Azure portal – Azure Database for MySQL
description: Ez a cikk ismerteti, hogyan konfigurálhatja és érheti el a naplózási naplók at Azure Database for MySQL az Azure Portalon.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 188ef3a1b9777c37f8557a69e19887638a973611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062531"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Az Azure Database for MySQL naplózási naplóinak konfigurálása és elérése az Azure Portalon

Konfigurálhatja az [Azure Database a MySQL naplózási naplók](concepts-audit-logs.md) és diagnosztikai beállítások az Azure Portalon.

> [!IMPORTANT]
> A napló funkciója jelenleg előzetes verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató útmutatón való átlépéshez a következőkre van szükség:

- [Azure Database for MySQL szerver](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

A naplózás engedélyezése és konfigurálása.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Válassza ki az Azure-adatbázis t a MySQL-kiszolgálóhoz.

1. Az oldalsáv **Beállítások** szakaszában válassza a **Kiszolgáló paramétereit**.
    ![Kiszolgálóparaméterek](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Frissítse a **audit_log_enabled** paramétert BE-re.
    ![Naplónaplók engedélyezése](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Válassza ki a naplózni kívánt [eseménytípusokat](concepts-audit-logs.md#configure-audit-logging) a **audit_log_events** paraméter frissítésével.
    ![Naplóesemények naplózása](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Adja hozzá azokat a MySQL-felhasználókat, akiket ki szeretne zárni a naplózásból a **audit_log_exclude_users** paraméter frissítésével. Adja meg a felhasználók at a MySQL felhasználónév megadásával.
    ![A napló felhasználókat nem zár ki](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Miután módosította a paramétereket, kattintson a **Mentés gombra.** Vagy **elvetheti** a módosításokat.
    ![Mentés](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

1. Az oldalsáv **Figyelés** szakaszában válassza a **Diagnosztikai beállítások**lehetőséget.

1. Kattintson a "+ ![Diagnosztikai beállítás hozzáadása" Diagnosztikai beállítás hozzáadása](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Adja meg a diagnosztikai beállítás nevét.

1. Adja meg, hogy mely adatok fogadók küldeni a naplónaplók (tárfiók, eseményközpont és/vagy Log Analytics munkaterület).

1. Válassza ki a "MySqlAuditLogs" a napló típusát.
![Diagnosztikai beállítás konfigurálása](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Miután beállította az adatgyűjtőket, hogy a naplónaplókat átadhassa, kattintson a **Mentés gombra.**
![Diagnosztikai beállítás mentése](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. A naplónaplók hoz a konfigurált adatgyűjtőkben való feltárásuk. A naplók megjelenése akár 10 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések

- További információ a [naplózási naplókról](concepts-audit-logs.md) az Azure Database for MySQL-ben.