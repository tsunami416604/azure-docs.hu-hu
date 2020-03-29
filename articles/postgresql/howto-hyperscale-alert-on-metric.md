---
title: Riasztások konfigurálása - Nagy kapacitású (Citus) - Azure Database for PostgreSQL
description: Ez a cikk bemutatja, hogyan konfigurálható és érhet el metrikariasztásokat az Azure Database for PostgreSQL - Hyperscale (Citus) számára.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: 80c061f72ce827df8f8354a5881c032c6f874fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063142"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Az Azure Portal használatával riasztásokat állíthat be a PostgreSQL-alapú Azure Database – Hyperscale (Citus) metrikákhoz.

Ez a cikk bemutatja, hogyan állíthatja be az Azure Database for PostgreSQL riasztások az Azure Portalhasználatával. Az Azure-szolgáltatások [figyelési metrikák](concepts-hyperscale-monitoring.md) alapján kaphat riasztást.

Beállítunk egy riasztást, amely akkor aktiválódik, ha egy megadott metrika értéke átlép egy küszöbértéket. A riasztás akkor aktiválódik, amikor a feltétel először teljesül, és utána is folytatódik.

A riasztást úgy konfigurálhatja, hogy az aktiválások során a következő műveleteket végezze el:
* E-mailértesítések küldése a szolgáltatás rendszergazdájának és társrendszergazdának.
* E-mail küldése a megadott további e-mailekbe.
* Hívjon egy webhookot.

A riasztási szabályok konfigurálása és az okkal kapcsolatos információk az:
* [Azure-portál](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Riasztási szabály létrehozása az Azure Portalon lévő metrikán
1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a figyelni kívánt Azure Database for PostgreSQL-kiszolgálót.

2. Az oldalsáv **Figyelés** szakaszában válassza a **Riasztások** lehetőséget az alábbi módon:

   ![Figyelmeztetési szabályok kiválasztása](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Válassza **az Új riasztási szabály** (+ ikon) lehetőséget.

4. A **Szabály létrehozása** lap az alábbiak szerint nyílik meg. Töltse ki a szükséges adatokat:

   ![Metrikariasztási űrlap hozzáadása](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. A **Feltétel csoportban** válassza a **Hozzáadás**lehetőséget.

6. Válasszon ki egy metrikát a figyelmeztetendő jelek listájából. Ebben a példában válassza a "Tárolási százalék" lehetőséget.
   
   ![Metrika kiválasztása](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. A riasztási logika konfigurálása:

    * **Kezelő** (pl. "Nagyobb, mint")
    * **Küszöbérték** (pl. 85 százalék)
    * **Összesítés részletességi** idő a metrikaszabály nak meg kell felelnie, mielőtt a riasztási eseményindítók (pl. "Az elmúlt 30 percben")
    * és **az értékelés gyakorisága** (pl. "1 perc")
   
   Válassza a **Kész lehetőséget,** ha befejeződött.

   ![Metrika kiválasztása](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. A **Műveletcsoportok** csoportban válassza az **Új létrehozása lehetőséget,** ha új csoportot szeretne létrehozni a riasztással kapcsolatos értesítések fogadásához.

9. Töltse ki a "Műveletcsoport hozzáadása" űrlapot névvel, rövid névvel, előfizetéssel és erőforráscsoporttal.

    ![Műveletcsoport](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. **E-mail/SMS/Push/Voice** művelettípus konfigurálása.
    
    Válassza az "E-mail Azure Resource Manager szerepkör" lehetőséget, ha értesítéseket szeretne küldeni az előfizetés-tulajdonosoknak, a közreműködőknek és az olvasóknak.
   
    Ha befejeződött, válassza **az OK** gombot.

    ![Műveletcsoport](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Adja meg a riasztási szabály nevét, leírását és súlyosságát.

    ![Műveletcsoport](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. A riasztás létrehozásához válassza a **Figyelmeztetési szabály létrehozása** lehetőséget.

    Néhány percen belül a riasztás aktív, és a korábban leírt eseményindítók.

### <a name="managing-alerts"></a>Riasztások kezelése

Miután létrehozott egy riasztást, kiválaszthatja azt, és a következő műveleteket teheti:

* Tekintse meg a mérőszám küszöbértéket és az előző napi aktuális értékeket bemutató grafikont, amely a riasztásra vonatkozik.
* A figyelmeztetési szabály **szerkesztése** vagy **törlése.**
* **Tiltsa le** vagy **engedélyezze** a riasztást, ha ideiglenesen le szeretné állítani vagy folytatni szeretné az értesítések fogadását.

## <a name="suggested-alerts"></a>Javasolt riasztások

### <a name="disk-space"></a>Lemezterület

A figyelés és riasztás minden éles nagy kapacitású (Citus) kiszolgálócsoport számára fontos. Az alapul szolgáló PostgreSQL-adatbázis megfelelő működéséhez szabad lemezterületre van szükség. Ha a lemez megtelik, az adatbázis-kiszolgáló csomópontja offline állapotba kerül, és nem indul el, amíg nincs szabad hely. Ezen a ponton a helyzet megoldásához microsofttámogatási kérelemre van szükség.

Javasoljuk, hogy a lemezterület-riasztásokat minden kiszolgálócsoport minden csomópontján, még a nem éles használat esetén is bekell- íteni. A lemezterület-használati riasztások biztosítják a beavatkozáshoz és a csomópontok állapotának megőrzéséhez szükséges előzetes figyelmeztetést. A legjobb eredmény érdekében próbálkozzon egy sor riasztással 75%-os, 85%-os és 95%-os használattal. A választható százalékok az adatbetöltés sebességétől függenek, mivel a gyors adatbetöltés gyorsabban tölti ki a lemezt.

Ahogy a lemez megközelíti a területkorlátot, próbálja ki ezeket a technikákat, hogy több szabad helyet kapjon:

* Tekintse át az adatmegőrzési házirendet. Ha lehetséges, helyezze át a régebbi adatokat a hűtőbe.
* Fontolja meg [csomópontok hozzáadása](howto-hyperscale-scaling.md#add-worker-nodes) a kiszolgálócsoporthoz, és a szegmensek kiegyensúlyozása. Az újraelosztás több számítógép között osztja el az adatokat.
* Fontolja meg a munkavégző csomópontok [kapacitásának növekedését.](howto-hyperscale-scaling.md#increase-or-decrease-vcores-on-nodes) Minden dolgozó legfeljebb 2 TiB tárhellyel rendelkezhet. A csomópontok újraméretezése előtt azonban meg kell kísérelnie a csomópontok hozzáadását, mivel a csomópontok hozzáadása gyorsabban befejeződik.

### <a name="cpu-usage"></a>Processzorhasználat

A CPU-használat figyelése hasznos a teljesítmény alapkonfigurációjának létrehozásához. Például észreveheti, hogy a CPU-használat általában 40-60% körül van. Ha a CPU-használat hirtelen 95% körül kezd lebegni, felismerhet egy anomáliát. A CPU-használat tükrözheti az organikus növekedés, de azt is felfedheti a kóbor lekérdezést. Cpu-riasztás létrehozásakor állítson be egy hosszú összesítési granularitást a hosszan tartó növekedés ek elfogásához és a pillanatnyi csúcsok figyelmen kívül hagyásához.

## <a name="next-steps"></a>További lépések
* További információ [a webhookok riasztásokban való konfigurálásáról.](../azure-monitor/platform/alerts-webhooks.md)
* Áttekintést [kaphat a metrikák gyűjteményéről,](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) hogy a szolgáltatás elérhető és válaszkész legyen.
