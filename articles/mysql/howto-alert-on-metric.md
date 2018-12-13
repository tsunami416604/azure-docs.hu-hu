---
title: Metrikák riasztások konfigurálása az Azure Database for MySQL-hez az Azure Portalon
description: Ez a cikk ismerteti konfigurálása és a hozzáférési metrikákhoz kapcsolódó riasztások az Azure Database for MySQL-hez az Azure Portalról.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6db451ab7de50039c38b59612dd2ab1c61c35e7b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271250"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Riasztásokat állíthat be mérőszámok az Azure Database for MySQL-hez az Azure portal használatával 

Ez a cikk bemutatja, hogyan állítható be Azure Database for MySQL-riasztások az Azure portal használatával. A metrikák az Azure-szolgáltatások monitorozása alapján riasztást kaphat.

A riasztás akkor aktiválódik, ha egy adott mérőszám értéke átlép egy küszöbértéket, rendeli. A riasztási eseményindítók is, ha az első feltétele teljesül, és majd ezt követően, hogy a feltétel ha van már nem teljesül. 

A következő műveleteket hajthatja végre, amikor elindítja riasztásokat lehet beállítani:
* e-mail értesítések küldéséhez a szolgáltatás-rendszergazda és társrendszergazdák
* További e-mailek, Ön által megadott e-mail küldése.
* Webhook meghívása

Konfigurálhatja és riasztási szabályok használatával kapcsolatos információk lekérése:
* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Parancssori felület (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Az Azure Monitor REST API-val](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Hozzon létre egy riasztási szabályt a metrika az Azure Portalról
1. Az a [az Azure portal](https://portal.azure.com/), válassza ki az Azure Database for MySQL-kiszolgálóhoz szeretné figyelni.

2. Alatt a **figyelés** oldalsávon válassza szakaszában **riasztási szabályok** látható módon:

   ![Válassza ki a riasztási szabályok](./media/howto-alert-on-metric/1-alert-rules.png)

3. Válassza ki **metrikariasztás hozzáadása** (+ ikon). 

4. A **szabály felvétele** lap, melyen alább látható módon.  Töltse ki a szükséges adatokat:

   ![Metrikaalapú riasztás űrlap hozzáadása](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Beállítás | Leírás  |
   |---------|---------|
   | Name (Név) | Adja meg a riasztási szabály nevét. Ez az érték a riasztási értesítések e-mailek küldése. |
   | Leírás | Adjon meg egy rövid leírást a riasztási szabályt. Ez az érték a riasztási értesítések e-mailek küldése. |
   | Riasztás: | Válasszon **metrikák** az ilyen típusú riasztás. |
   | Előfizetés | Ez a mező az előfizetéshez, amelyen az Azure Database MySQL-hez előre van töltve. |
   | Erőforráscsoport | Ebben a mezőben előre van töltve az erőforráscsoport az Azure database for MySQL-hez. |
   | Erőforrás | Ebben a mezőben előre van töltve olyan neve az Azure Database for MySQL-hez. |
   | Metrika | Válassza ki a riasztással a kívánt metrikát. Ha például **tárolási százalékos**. |
   | Állapot | Válassza ki azt a feltételt, a metrika az össze kell hasonlítani. Ha például **nagyobb, mint**. |
   | Küszöbérték | Küszöbérték a mérőszám, például 85 (százalék). |
   | Időszak | Az az időtartam, a metrikaszabály kell biztosítani a riasztási eseményindítók előtt. Ha például **az elmúlt 30 perc**. |

   A példa alapján, a riasztás keres tárolási százalékos 85 % felett egy 30 perces időszak alatt. A riasztást aktivál, ha a tárolási átlagos százalékos 30 percig 85 % felett volt. Az első eseményindító akkor fordul elő, ha elindítja újra amikor az átlagos tárolási százalékos értéke 85 % alatt több mint 30 perc.

5. Válassza ki az értesítési módszer a riasztási szabály használni szeretne. 

   Ellenőrizze **E-mail-tulajdonosoknak, közreműködőknek és olvasóknak** beállítást, ha azt szeretné, hogy az előfizetés rendszergazdái és társadminisztrátorai a riasztás akkor aktiválódik, ha e-mail üzenetben.

   Ha azt szeretné, további e-mail értesítést kapnak, ha a riasztás akkor aktiválódik, adja hozzá őket a **további rendszergazdai email(s)** mező. Pontosvesszővel több e-mailek -  *email@contoso.com;email2@contoso.com*

   Szükség esetén adja meg az érvényes URI-t a **Webhook** Ha azt szeretné, hogy ez a riasztás aktiválódásakor nevű mezőt.

6. Válassza ki **OK** a riasztás létrehozásához.

   Néhány percen belül a riasztás aktív, és elindítja a fent leírtaknak megfelelően.

## <a name="manage-your-alerts"></a>A riasztások kezelése
Miután létrehozta a riasztást, válassza ki, és hajtsa végre a következőket:

* Tekintse meg a metrika küszöbértéke és a tényleges értékek az előző nap a riasztáshoz tartozó megjelenítő grafikon.
* **Szerkesztés** vagy **törlése** a riasztási szabályt.
* **Tiltsa le** vagy **engedélyezése** a riasztás, ha azt szeretné, ideiglenesen leállíthatja, vagy folytathatja a értesítéseket kapni.


## <a name="next-steps"></a>További lépések
* Tudjon meg többet [webhookok konfigurálásáról a riasztások](../azure-monitor/platform/alerts-webhooks.md).
* Get- [metrikák gyűjtési áttekintése](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) , győződjön meg arról, hogy szolgáltatása elérhető és válaszkész.
