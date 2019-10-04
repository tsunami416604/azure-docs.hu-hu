---
title: Naplózási naplók konfigurálása és elérése – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni és elérni a naplókat a Azure Portal Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 2a2d2a697f0e41fb296c61c01909a814678f8277
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350410"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>A Azure Portal Azure Database for MySQL naplózási naplóinak konfigurálása és elérése

A Azure Portal [Azure Database for MySQL naplózási naplókat](concepts-audit-logs.md) és diagnosztikai beállításokat is konfigurálhat.

> [!IMPORTANT]
> A naplózási funkció jelenleg előzetes verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- [Azure Database for MySQL kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

A naplózás engedélyezése és konfigurálása.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki az Azure Database for MySQL-kiszolgálóhoz.

1. Az oldalsáv **Beállítások** szakaszában válassza ki a **kiszolgáló paramétereit**.
    ![Kiszolgálóparaméterek](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Frissítse a **audit_log_enabled** PARAMÉTERt a következőre:.
    @no__t – 0Enable napló @ no__t-1

1. Válassza ki a naplózni kívánt [események típusát](concepts-audit-logs.md#configure-audit-logging) a **audit_log_events** paraméter frissítésével.
    @no__t 0Audit-naplózási események @ no__t-1

1. A **audit_log_exclude_users** paraméter frissítésével adja hozzá a kizárni kívánt MySQL-felhasználókat a naplózásból. Adja meg a felhasználókat a MySQL-Felhasználónév megadásával.
    @no__t – 0Audit-napló kizárják a felhasználókat @ no__t-1

1. A paraméterek módosítása után kattintson a **Mentés**gombra. Vagy elvetheti a módosításokat.
    ![Save @ no__t-1

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

1. Az oldalsáv **figyelés** területén válassza a **diagnosztikai beállítások**elemet.

1. Kattintson a "+ diagnosztikai beállítás hozzáadása" ![Add diagnosztikai beállítás @ no__t-1 elemre.

1. Adja meg a diagnosztikai beállítások nevét.

1. Itt adhatja meg, hogy mely adatnyelők küldje el a naplókat (Storage-fiók, Event hub és/vagy Log Analytics munkaterület).

1. Válassza a "MySqlAuditLogs" lehetőséget a napló típusaként.
@no__t – 0Configure diagnosztikai beállítás @ no__t-1

1. Miután konfigurálta az adattárolást a naplókba, kattintson a **Save (Mentés**) gombra.
@no__t – 0Save diagnosztikai beállítás @ no__t-1

1. A naplókat úgy érheti el, hogy a konfigurált adattárolókban vizsgálja őket. A naplók megjelenése akár 10 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések

- További információ a Azure Database for MySQL [naplózási naplóiról](concepts-audit-logs.md) .