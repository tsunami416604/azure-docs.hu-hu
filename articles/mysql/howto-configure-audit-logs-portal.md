---
title: Hozzáférés-naplózási naplók – Azure Portal – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni és elérni a naplókat a Azure Portal Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: c406fa6b49e800912edb5738b4d60596d828fc94
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570493"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>A Azure Portal Azure Database for MySQL naplózási naplóinak konfigurálása és elérése

A Azure Portal [Azure Database for MySQL naplózási naplókat](concepts-audit-logs.md) és diagnosztikai beállításokat is konfigurálhat.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- [Azure Database for MySQL kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

>[!IMPORTANT]
> Azt javasoljuk, hogy csak azokat az eseményeket és felhasználókat naplózza, amelyek szükségesek a naplózáshoz, hogy a kiszolgáló teljesítménye ne legyen nagy hatással.

A naplózás engedélyezése és konfigurálása.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki a Azure Database for MySQL-kiszolgálót.

1. Az oldalsáv **Beállítások** szakaszában válassza ki a **kiszolgáló paramétereit**.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="Kiszolgálóparaméterek":::

1. Frissítse a **audit_log_enabled** PARAMÉTERt a következőre:.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Kiszolgálóparaméterek":::

1. Válassza ki a naplózni kívánt [események típusát](concepts-audit-logs.md#configure-audit-logging) a **audit_log_events** paraméter frissítésével.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="Kiszolgálóparaméterek":::

1. Vegyen fel minden olyan MySQL-felhasználót, amelyet be szeretne vonni, vagy ki szeretne zárni a naplózásból a **audit_log_exclude_users** és **audit_log_include_users** paraméterek frissítésével. Adja meg a felhasználókat a MySQL-Felhasználónév megadásával.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Kiszolgálóparaméterek":::

1. A paraméterek módosítása után kattintson a **Mentés**gombra. Vagy **elvetheti** a módosításokat.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="Kiszolgálóparaméterek":::

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

1. Az oldalsáv **figyelés** területén válassza a **diagnosztikai beállítások**elemet.

1. Kattintson a "+ diagnosztikai beállítás hozzáadása" :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Kiszolgálóparaméterek" lehetőséget a napló típusaként.
:::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Kiszolgálóparaméterek":::

1. Miután konfigurálta az adattárolást a naplókba, kattintson a **Save (Mentés**) gombra.
:::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Kiszolgálóparaméterek":::

1. A naplókat úgy érheti el, hogy a konfigurált adattárolókban vizsgálja őket. A naplók megjelenése akár 10 percet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések

- További információ a Azure Database for MySQL [naplózási naplóiról](concepts-audit-logs.md)
- Ismerje meg, hogyan konfigurálhatja a naplókat az [Azure CLI](howto-configure-audit-logs-cli.md) -ben