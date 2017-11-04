---
title: "Metrikák vonatkozó riasztások konfigurálása az Azure Database MySQL az Azure portálon |} Microsoft Docs"
description: "A cikk konfigurálása és hozzáférési metrika riasztások az Azure Database MySQL az Azure-portálról."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: 5b4263e76f89ae27b9c9f317db3038def01e3e6b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Riasztásokat állíthat be metrikák Azure-adatbázis a MySQL az Azure-portál használatával 

Ez a cikk bemutatja, hogyan Azure-adatbázis létrehozásakor az Azure portál használatával MySQL riasztásokhoz. Figyelése az Azure-szolgáltatások metrikáját alapuló riasztást kaphat.

A riasztási eseményindítók megadott metrika értékét ebbe a hozzárendelt küszöbértéket. A riasztási eseményindítók is, ha az első feltétele teljesül, majd ezt követően, hogy a feltétel mikor van már nem teljesül. 

A következő műveleteket hajthatja végre, amikor elindítja a riasztásokat lehet beállítani:
* e-mail értesítések küldéséhez a szolgáltatás-rendszergazda és a társadminisztrátorok
* e-mail küldéséhez megadott további e-maileket.
* A webhook hívása

Konfigurálhatja, és a riasztási szabályok használatával adatainak beolvasása:
* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [Parancssori felület (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [Az Azure figyelő REST API-n](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Riasztási szabályt létrehozni a metrika az Azure-portálon
1. Az a [Azure-portálon](https://portal.azure.com/), válassza ki a figyelni kívánt MySQL-kiszolgálóhoz tartozó Azure-adatbázis.

2. A a **figyelés** az oldalsávon, jelölje be szakasza **riasztási szabályok** látható módon:

   ![Válassza ki a riasztási szabályok](./media/howto-alert-on-metric/1-alert-rules.png)

3. Válassza ki **metrika riasztás hozzáadása** (+ ikonra). 

4. A **Hozzáadás szabály** oldalon lent látható módon megnyílik.  Adja meg a szükséges adatokat:

   ![Metrika riasztási űrlap hozzáadása](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Beállítás | Leírás  |
   |---------|---------|
   | Név | Adja meg a riasztási szabály nevét. Ez az érték a riasztás-értesítési e-mailek küldése. |
   | Leírás | A riasztási szabály rövid leírását adhatja meg. Ez az érték a riasztás-értesítési e-mailek küldése. |
   | Riasztás | Válasszon **metrikák** az ilyen típusú riasztás. |
   | Előfizetés | Ebben a mezőben a rendszer az előfizetéshez, amely az Azure-adatbázis MySQL előre feltöltve. |
   | Erőforráscsoport | Ez a mező a erőforráscsoporttal, az Azure-adatbázis a MySQL előre feltöltve a rendszer. |
   | Erőforrás | Ebben a mezőben az Azure-adatbázis neve a MySQL előre feltöltve a rendszer. |
   | Metrika | Válassza ki a cél, hogy szeretne kiadni egy riasztást. Például **tárolási százalékos**. |
   | Az állapot | Válassza ki a metrika az összehasonlítandó feltételét. Például **nagyobb, mint**. |
   | Küszöbérték | Küszöbérték a metrika, például 85 (százalék). |
   | Időszak | Az időtartam alatt a metrika szabály a riasztási eseményindítók előtt teljesülniük kell. Például **az elmúlt 30 perc**. |

   A példa alapján, a riasztás keresi tárolási százalékos 85 % feletti 30 perces időszakon át. Riasztás váltja ki, ha a tárhely átlagos százalékos 30 percig 85 % felett volt. Akkor következik be, az első eseményindító, ha elindítja a újra átlagos tárolási százalékos esetén 85 % alatt több mint 30 perc.

5. Adja meg az értesítés módját, a riasztási szabály használni szeretne. 

   Ellenőrizze **E-mail-tulajdonosok, közreműködőknek és olvasóknak** lehetőséget, ha azt szeretné, hogy az előfizetés rendszergazdák és a társadminisztrátorok e-mailben a riasztás aktiválódásakor.

   Ha azt szeretné, hogy további az e-maileket kap értesítést, a riasztás aktiválódásakor, adja hozzá a a **további rendszergazda email(s)** mező. Több e-mailek külön és pontosvesszővel kell elválasztani -  *email@contoso.com;email2@contoso.com*

   Igény szerint adja meg egy érvényes URI-azonosító található a **Webhook** mezőben, ha azt szeretné, hogy a riasztás aktiválódásakor meghívta.

6. Válassza ki **OK** a riasztás létrehozása.

   Néhány percen belül a riasztás aktív, és elindítja a leírt módon.

## <a name="manage-your-alerts"></a>A riasztások kezelése
Miután létrehozta a riasztást, válassza ki azt, és a következő műveleteket hajthatja végre:

* Tekintse meg a metrika küszöbérték és a tényleges értékek az előző nap az ehhez a riasztáshoz kapcsolódó megjelenítő grafikon.
* **Szerkesztés** vagy **törlése** a riasztási szabályt.
* **Tiltsa le a** vagy **engedélyezése** a riasztás, ha azt szeretné, ideiglenesen leállítani, vagy folytathatja a fogadott értesítések.


## <a name="next-steps"></a>Következő lépések
* További információ [konfigurálása webhookokkal a riasztások](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Első egy [metrikák gyűjtemény áttekintése](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) ellenőrizze, hogy a szolgáltatás elérhető, és a gyors.
