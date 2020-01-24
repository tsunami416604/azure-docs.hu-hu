---
title: A Application Gateway metrikáinak Azure Monitor
description: Tudnivalók a metrikák használatáról az Application Gateway teljesítményének figyeléséhez
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: 12759deb3e1775b5170d40cc609fe8c6226bf0d6
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704578"
---
# <a name="metrics-for-application-gateway"></a>Application Gateway metrikái

A Application Gateway a metrikák nevű adatpontokat teszi közzé a Application Gateway és a háttérbeli példányok teljesítményének [Azure Monitoréhez](https://docs.microsoft.com/azure/azure-monitor/overview) . Ezek a mérőszámok az idősorozat-adatok rendezett készletében lévő numerikus értékek, amelyek egy adott időpontban írják le az Application Gateway bizonyos aspektusait. Ha a Application Gatewayon átáramló kérések, a rendszer a 60 másodperces intervallumban méri és továbbítja a metrikákat. Ha nincsenek átáramló kérelmek a Application Gatewayon, vagy egy metrika nem rendelkezik adatokkal, a rendszer nem jelenti a metrikát. További információ: [Azure monitor mérőszámok](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Application Gateway v2 SKU által támogatott metrikák

### <a name="timing-metrics"></a>Időzítési mérőszámok

A kérelem és válasz időzítésével kapcsolatos következő mérőszámok érhetők el. Az adott figyelőhöz tartozó mérőszámok elemzésével meghatározhatja, hogy a WAN, a Application Gateway, a Application Gateway és a háttérrendszer közötti hálózat, illetve a háttérrendszer-alkalmazás teljesítménye miatti lassulás az alkalmazásban.

> [!NOTE]
>
> Ha a Application Gateway több figyelő is van, akkor a rendszer mindig a *figyelő* dimenzió alapján szűri a különböző késési metrikákat, hogy az értelmes következtetést kapjon.

- **Háttérbeli kapcsolat ideje**

  A háttér-alkalmazással létesített kapcsolatok létrehozásának ideje. Ez magában foglalja a hálózati késést, valamint a háttér-kiszolgáló TCP-veremének az új kapcsolatok létrehozásához szükséges időt. SSL esetén a rendszer a kézfogás során eltöltött időt is tartalmazza. 

- **Háttérbeli első bájt válaszideje**

  A háttér-kiszolgálóval létesített kapcsolat létrehozásának és a válasz fejlécének első bájtjának fogadása közötti időtartam. Ez megközelíti a háttérbeli *kapcsolat idejét* és a háttérbeli alkalmazás válaszideje (a kiszolgáló által a tartalom előállítása, az adatbázis-lekérdezések beolvasása, és a válasz átadásának megkezdése Application Gatewayre).

- **Háttérbeli utolsó bájt válaszideje**

  A háttér-kiszolgálóval létesített kapcsolat létesítése és a válasz törzse utolsó bájtjának fogadása között eltelt idő. Ez megközelíti a *háttérbeli első bájt* és az adatátviteli idő összegét (ez a szám nagy mértékben változhat a kért objektumok méretétől és a kiszolgáló hálózatának késésével függően)

- **Application Gateway – teljes idő**

  A kérelem feldolgozásának és a válasz elküldésekor elvégezhető átlagos idő. Ez az időszak átlaga, amikor a Application Gateway megkapja egy HTTP-kérelem első bájtját, amikor a válasz küldése művelet befejeződik, és megközelíti a Application Gateway feldolgozási idejének összegét és a *háttérbeli utolsó bájtra adott időpontot* .

- **Ügyfél-RTT**

  Az ügyfelek és a Application Gateway közötti átlagos menetidő. Ez a metrika azt jelzi, hogy mennyi ideig tart a kapcsolatok létrehozása és a visszaigazolások visszaküldése. 

Ezek a metrikák segítségével megállapítható, hogy a megfigyelt lassulás a Application Gateway, a hálózat és a háttérrendszer-kiszolgáló TCP-veremének telítettsége, a háttérbeli alkalmazások teljesítménye vagy nagyméretű fájlméret miatt.
Ha például van egy tüske a háttérbeli első bájtban, de a háttérbeli kapcsolódási idő állandó, akkor arra utalhat, hogy az Application Gateway és a háttérrendszer késése, valamint a kapcsolat létesítéséhez szükséges idő stabil, és a nyárs oka a következő: n nő a háttérbeli alkalmazás válaszideje. Hasonlóképpen, ha a háttérbeli első bájt válaszideje a háttérbeli csatlakozási idő egy megfelelő szegével van társítva, akkor a hálózat vagy a kiszolgáló TCP-vereme telített. Ha egy csúcsot észlel a háttérbeli utolsó bájtban, de a háttér első bájtjának válaszideje állandó, akkor a legnagyobb valószínűséggel a Nyárs a kért nagyobb fájl miatt. Hasonlóképpen, ha az Application Gateway teljes ideje sokkal több, mint a háttérbeli utolsó bájtok válaszideje, akkor a teljesítmény szűk keresztmetszetének jele lehet a Application Gateway.



### <a name="application-gateway-metrics"></a>Application Gateway metrikák

Application Gateway esetén a következő metrikák érhetők el:

- **Fogadott bájtok száma**

   Az ügyfelektől érkező Application Gateway által fogadott bájtok száma

- **Eljuttatott bájtok**

   A Application Gateway által az ügyfeleknek eljuttatott bájtok száma

- **Ügyfél TLS protokoll**

   Azoknak a TLS-és nem TLS-kérelmeknek a száma, amelyek az ügyfél által kezdeményezett Application Gatewaykal létesített kapcsolatban állnak. A TLS protokoll terjesztésének megtekintéséhez szűrje a Dimension TLS protokollt.

- **Aktuális kapacitási egységek**

   A felhasznált kapacitási egységek száma. A kapacitási egységek a rögzített költség mellett felszámított felhasználáson alapuló költségeket mérik fel. A kapacitás mértékegysége – számítási egység, állandó kapcsolatok és átviteli sebesség – három tényező. Minden kapacitási egység legfeljebb a következőkből áll: 1 számítási egység, vagy 2500 állandó kapcsolat vagy 2,22 – Mbps átviteli sebesség.

- **Aktuális számítási egységek**

   A felhasznált processzor kapacitásának száma. A számítási egységet befolyásoló tényezők a TLS-kapcsolatok/mp, az URL-Újraírási számítások és a WAF-szabályok feldolgozása. 

- **Aktuális kapcsolatok**

   Application Gatewaysal létesített aktuális kapcsolatok száma

- **Sikertelen kérelmek**

   A Application Gateway által kiszolgált sikertelen kérelmek száma. A kérelmek száma tovább szűrhető úgy, hogy megjelenjenek az egyes/specifikus backend-készletek száma – a http-beállítások kombinációja.


- **Válasz állapota**

   Application Gateway által visszaadott HTTP-válasz állapota. A válasz állapotkód-eloszlása tovább kategorizálható a válaszok megjelenítéséhez a 2xx, a 3xx, a 4xx és a 5xx kategóriákban.

- **Átviteli sebesség**

   A Application Gateway által kiszolgált bájtok másodpercenkénti száma

- **Kérelmek összesen**

   A Application Gateway által kiszolgált sikeres kérelmek száma. A kérelmek száma tovább szűrhető úgy, hogy megjelenjenek az egyes/specifikus backend-készletek száma – a http-beállítások kombinációja.

- **Webalkalmazási tűzfal egyeztetett szabályai**

- **Webalkalmazási tűzfal által aktivált szabályok**

### <a name="backend-metrics"></a>Háttérbeli metrikák

Application Gateway esetén a következő metrikák érhetők el:

- **Háttérbeli válasz állapota**

  A háttérrendszer által visszaadott HTTP-válasz állapot-kódok száma. Ez nem tartalmazza a Application Gateway által létrehozott válasz-kódokat. A válasz állapotkód-eloszlása tovább kategorizálható a válaszok megjelenítéséhez a 2xx, a 3xx, a 4xx és a 5xx kategóriákban.

- **Kifogástalan állapotú gazdagépek száma**

  Az állapot mintavétele által kifogástalanul meghatározott háttérrendszer száma. Egy adott háttérbeli készlet kifogástalan/nem megfelelő állapotú gazdagépének megjelenítéséhez szűrést végezhet a háttérbeli készlet alapján.

- **Nem kifogástalan állapotú gazdagépek száma**

  Az állapot-mintavétel által nem kifogástalan állapotú háttérrendszer száma. Egy háttérbeli készlet alapján szűrheti a nem kifogástalan állapotú gazdagépeket egy adott háttérbeli készletben.

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Application Gateway v1 SKU által támogatott metrikák

### <a name="application-gateway-metrics"></a>Application Gateway metrikák

Application Gateway esetén a következő metrikák érhetők el:

- **CPU-kihasználtság**

  Megjeleníti a Application Gateway lefoglalt processzorok kihasználtságát.  Normál körülmények között a CPU-használat nem haladhatja meg a 90%-ot, mivel ez késést okozhat a Application Gateway mögött futtatott webhelyeken, és megszakíthatja az ügyfél élményét. A Application Gateway konfigurációjának módosításával közvetve vezérelheti vagy növelheti a CPU-kihasználtságot azáltal, hogy növeli a példányszámot vagy nagyobb SKU-méretre vált, vagy mindkettőt.

- **Aktuális kapcsolatok**

  Application Gatewaysal létesített aktuális kapcsolatok száma

- **Sikertelen kérelmek**

  A Application Gateway által kiszolgált sikertelen kérelmek száma. A kérelmek száma tovább szűrhető úgy, hogy megjelenjenek az egyes/specifikus backend-készletek száma – a http-beállítások kombinációja.

- **Válasz állapota**

  Application Gateway által visszaadott HTTP-válasz állapota. A válasz állapotkód-eloszlása tovább kategorizálható a válaszok megjelenítéséhez a 2xx, a 3xx, a 4xx és a 5xx kategóriákban.

- **Átviteli sebesség**

  A Application Gateway által kiszolgált bájtok másodpercenkénti száma

- **Kérelmek összesen**

  A Application Gateway által kiszolgált sikeres kérelmek száma. A kérelmek száma tovább szűrhető úgy, hogy megjelenjenek az egyes/specifikus backend-készletek száma – a http-beállítások kombinációja.

- **Webalkalmazási tűzfal egyeztetett szabályai**

- **Webalkalmazási tűzfal által aktivált szabályok**

### <a name="backend-metrics"></a>Háttérbeli metrikák

Application Gateway esetén a következő metrikák érhetők el:

- **Kifogástalan állapotú gazdagépek száma**

  Az állapot mintavétele által kifogástalanul meghatározott háttérrendszer száma. Egy adott háttérbeli készlet kifogástalan/nem megfelelő állapotú gazdagépének megjelenítéséhez szűrést végezhet a háttérbeli készlet alapján.

- **Nem kifogástalan állapotú gazdagépek száma**

  Az állapot-mintavétel által nem kifogástalan állapotú háttérrendszer száma. Egy háttérbeli készlet alapján szűrheti a nem kifogástalan állapotú gazdagépeket egy adott háttérbeli készletben.

## <a name="metrics-visualization"></a>Metrikák vizualizációja

Tallózással keresse meg az Application Gatewayt a **figyelés** kiválasztása **mérőszámok**területen. Az elérhető értékeket a **METRIKÁK** legördülő listában találja.

Az alábbi képen egy példa látható három mérőszámmal az elmúlt 30 percben:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

A metrikák aktuális listájának megjelenítéséhez tekintse meg a [támogatott metrikák a Azure Monitorkal](../azure-monitor/platform/metrics-supported.md)című témakört.

### <a name="alert-rules-on-metrics"></a>Riasztási szabályok a metrikák esetében

A riasztási szabályokat az erőforrás metrikái alapján indíthatja el. A riasztások például meghívhatnak egy webhookot vagy e-mailt a rendszergazdának, ha az Application Gateway átviteli sebessége meghaladja a következőt, vagy a küszöbértéket egy adott időszakra vonatkozóan.

Az alábbi példa végigvezeti egy olyan riasztási szabály létrehozásán, amely e-mailt küld egy rendszergazdának az átviteli sebesség megsértése után:

1. a **szabály hozzáadása** lap megnyitásához válassza a **metrika hozzáadása riasztás** elemet. Ezt a lapot a metrikák lapról is elérheti.

   !["Metrikai riasztás hozzáadása" gomb][6]

2. A **szabály hozzáadása** lapon töltse ki a nevet, a feltételt és az értesítési szakaszt, majd kattintson **az OK gombra**.

   * A **feltétel** -választóban válasszon ki egyet a következő négy érték közül: **nagyobb**, mint, **nagyobb vagy egyenlő**, **kisebb**, mint, vagy **kisebb vagy egyenlő**.

   * Az **időszak** -választóban válasszon ki egy pontot öt perc és hat óra között.

   * Ha az **e-mail-tulajdonosok, a közreműködők és az olvasók**lehetőséget választja, az e-mailek az adott erőforráshoz hozzáférő felhasználók alapján dinamikusak lehetnek. Ellenkező esetben a **további rendszergazdai e-mailek (ek)** mezőben megadhatja a felhasználók vesszővel elválasztott listáját.

   ![Szabály hozzáadása lap][7]

Ha a küszöbértéket megszegik, az alábbi képen láthatóhoz hasonló e-mail érkezik:

![Sérült küszöbértékre vonatkozó e-mail][8]

A riasztások listája a metrikai riasztás létrehozása után jelenik meg. Áttekintést nyújt az összes riasztási szabályról.

![Riasztások és szabályok listája][9]

A riasztási értesítésekről további információt a [Riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)című témakörben talál.

Ha többet szeretne megtudni a webhookokról, és arról, hogyan használhatja őket riasztásokkal, látogasson el [a webhook konfigurálása Azure metrikai riasztásra](../azure-monitor/platform/alerts-webhooks.md)című témakörben.

## <a name="next-steps"></a>Következő lépések

* Számlálók és Eseménynaplók megjelenítése [Azure monitor naplók](../azure-monitor/insights/azure-networking-analytics.md)használatával.
* [Jelenítse meg az Azure-beli tevékenység naplóját Power bi](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogbejegyzésben.
* [Megtekintheti és elemezheti az Azure-beli tevékenységek naplóit Power bi és további](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogbejegyzésekben.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
