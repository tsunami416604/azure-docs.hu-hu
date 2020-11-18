---
title: Application Gateway nagy forgalmú kötetek támogatása
description: Ez a cikk útmutatást nyújt az Azure Application Gateway konfigurálásához a nagy hálózati forgalomra vonatkozó forgatókönyvek támogatásához.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 3854e7f3c19f1724a2df1508c9fa519809e07ba9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658672"
---
# <a name="application-gateway-high-traffic-support"></a>Nagy mennyiségű forgalom támogatása az Application Gatewayben

>[!NOTE]
> Ez a cikk néhány javasolt útmutatót tartalmaz, amelyek segítségével beállíthatja, hogy a Application Gateway a nagy forgalmú kötetek további forgalmának kezeléséhez. A riasztási küszöbértékek kizárólag javaslatok és általános jellegűek. A felhasználók a számítási feladatok és a kihasználtsági elvárások alapján határozhatják meg a riasztási küszöbértékeket.

A webalkalmazások forgalmát méretezhető és biztonságos módon kezelheti a webalkalmazási tűzfallal (WAF) Application Gateway használatával.

Fontos, hogy a Application Gateway az adatforgalomnak és egy kis puffernek megfelelően méretezze, hogy előkészítse a forgalom felskálázását vagy a tüskéket, és minimalizálja a QoS-ben esetlegesen felmerülő hatásokat. A következő javaslatok segítséget nyújtanak a WAF-vel való Application Gateway beállításában a további forgalom kezeléséhez.

Tekintse meg a [metrikák dokumentációját](./application-gateway-metrics.md) a Application Gateway által kínált metrikák teljes listájának megtekintéséhez. A metrikákkal kapcsolatos riasztások beállításával kapcsolatban lásd: [mérőszámok megjelenítése](./application-gateway-metrics.md#metrics-visualization) a Azure Portal és az [Azure monitor dokumentációjában](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric) .

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>Méretezés a Application Gateway v1 SKU-hoz (standard/WAF SKU)

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>A példányok számának beállítása a maximális CPU-használat alapján
Ha v1 SKU-átjárót használ, beállíthatja, hogy a Application Gateway legfeljebb 32 példányban állítható be a skálázás. Ellenőrizze a Application Gateway CPU-kihasználtságát az elmúlt egy hónapban bármely, 80%-nál nagyobb tüskék esetében, amely a figyeléshez mérőszámként érhető el. Azt javasoljuk, hogy a példányszámot a maximális kihasználtság és a 10 – 20%-os további puffer alapján állítsa be a forgalmi tüskék kiszámításához.

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Használja a v2 SKU-t a v1 protokollon keresztül az automatikus skálázási képességek és a teljesítmény előnyeihez
A v2 SKU automatikus skálázást biztosít annak biztosításához, hogy a Application Gateway képes legyen a forgalom növekedésére. Emellett további jelentős teljesítménybeli előnyöket is kínál, például az 5x-ös jobb TLS-kiszervezési teljesítményt, a gyorsabb üzembe helyezést és a frissítés időpontját, a zónák redundanciát és egyebeket a V1-hez képest. További információkért lásd a [v2 dokumentációt](./application-gateway-autoscaling-zone-redundant.md) , és tekintse meg a v1 – v2 [áttelepítési dokumentációt](./migrate-v1-v2.md) , amelyből megtudhatja, hogyan telepítheti át meglévő v1 SKU-átjáróit a v2 SKU-ra. 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>Automatikus skálázás Application Gateway v2 SKU-hoz (Standard_v2/WAF_v2 SKU)

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>A példányok maximális számának beállítása a lehető legnagyobb értékre (125)
 
Application Gateway v2 SKU esetében a maximális példányszámot a 125 maximális lehetséges értékre állítva a Application Gateway igény szerint felskálázást tesz lehetővé. Ez lehetővé teszi, hogy kezelje az alkalmazásaihoz való adatforgalom lehetséges növekedését. Csak a használt kapacitási egységekre (ke) számítunk fel díjat. 

Ügyeljen rá, hogy ellenőrizze az alhálózat méretét és az IP-címek darabszámát az alhálózatban, és a példányok maximális száma alapján állítsa be a példányszámot. Ha az alhálózat nem rendelkezik elegendő lemezterülettel, akkor újra létre kell hoznia az átjárót ugyanabban a vagy a különböző alhálózatban, amely elegendő kapacitással rendelkezik. 

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>A példányok minimális számának beállítása az átlagos számítási egység használata alapján

Application Gateway v2 SKU esetében az automatikus skálázás 6 – hét percet vesz igénybe a horizontális felskálázáshoz, és további példányok üzembe helyezésére készen áll a forgalom átvételére. Addig is, ha a forgalomban rövid tüskék vannak, előfordulhat, hogy a meglévő Gateway-példányok terhelés alatt állnak, és ez váratlan késést vagy adatvesztést okozhat. 

Javasoljuk, hogy a példányok minimális darabszámát optimális szintre állítsa. Ha például 50-példányra van szükség a forgalom a maximális terhelésnél való kezeléséhez, akkor a minimum 25 – 30 érték beállítása jó ötlet, nem pedig <10, így még akkor is, ha kevés a forgalom, a Application Gateway képes lenne kezelni, és elegendő időt biztosítani az automatikus skálázásra a válaszadáshoz és életbe léptetéséhez.

Az elmúlt egy hónapban keresse meg a számítási egység metrikáját. A számítási egység mérőszáma az átjáró CPU-kihasználtságának ábrázolása, és a maximális kihasználtsága 10-re bontva a szükséges példányok minimális számát állíthatja be. Vegye figyelembe, hogy 1 Application Gateway-példány legalább 10 számítási egységet képes kezelni

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Manuális skálázás Application Gateway v2 SKU-hoz (Standard_v2/WAF_v2)

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>A példányok számának beállítása a maximális számítási egység használata alapján 

Az automatikus skálázástól eltérően a manuális skálázás során manuálisan kell beállítani az Application Gateway példányainak számát a forgalmi követelmények alapján. Azt javasoljuk, hogy a példányszámot a maximális kihasználtság és a 10 – 20%-os további puffer alapján állítsa be a forgalmi tüskék kiszámításához. Ha például a forgalomhoz 50-es példányok szükségesek a csúcson, akkor a 55 – 60 példányok kiépítésével az esetlegesen előforduló váratlan forgalmi tüskéket kezelheti.

Az elmúlt egy hónapban keresse meg a számítási egység metrikáját. A számítási egység mérőszáma az átjáró CPU-kihasználtságának ábrázolása, és a maximális kihasználtsága 10-re bontva, beállíthatja a szükséges példányok számát, mivel 1 az Application Gateway-példány legalább 10 számítási egységet képes kezelni.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás

Ha értesítést szeretne kapni a forgalomról vagy a kihasználtsági rendellenességekről, beállíthat riasztásokat bizonyos mérőszámokra vonatkozóan. A Application Gateway által kínált mérőszámok teljes listáját a [metrikák dokumentációjában](./application-gateway-metrics.md) találja. A metrikákkal kapcsolatos riasztások beállításával kapcsolatban lásd: [mérőszámok megjelenítése](./application-gateway-metrics.md#metrics-visualization) a Azure Portal és az [Azure monitor dokumentációjában](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric) .

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>Riasztások Application Gateway v1 SKU-hoz (standard/WAF)

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>Riasztás, ha az átlagos CPU-kihasználtság 80%-kal több

Normál körülmények között a processzorhasználat nem haladhatja meg rendszeresen a 90%-ot, mivel ez késést okozhat az Application Gateway mögött futtatott webhelyeken, ami ronthatja az ügyfélélményt. A Application Gateway konfigurációjának módosításával közvetve vezérelheti vagy növelheti a CPU-kihasználtságot azáltal, hogy növeli a példányszámot, vagy ha nagyobb SKU-méretre vagy mindkettőre vált. Riasztás beállítása, ha a CPU-kihasználtsági metrika 80%-nál nagyobb átlagot mutat.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Riasztás, ha a nem kifogástalan állapotú gazdagépek száma átlépi a küszöbértéket

Ez a metrika azt jelzi, hogy hány háttér-kiszolgáló nem tudja sikeresen elvégezni az Application Gateway-t. Ez olyan problémákat fog észlelni, amelyekben az Application Gateway-példányok nem tudnak kapcsolódni a háttérhez. Riasztás, ha ez a szám a háttérbeli kapacitás 20%-át meghaladja. Például Ha jelenleg 30 háttér-kiszolgáló van a háttér-készletben, állítson be egy riasztást, ha a nem kifogástalan állapotú gazdagépek száma 6 fölé esik.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Riasztás, ha a válasz állapota (4xx, 5xx) átlépi a küszöbértéket 

Riasztás létrehozása, ha Application Gateway válasz állapota 4xx vagy 5xx. Átmeneti problémák miatt előfordulhat, hogy alkalmi 4xx vagy 5xx válaszol. Az átjárót éles környezetben kell megfigyelni a statikus küszöbérték megállapítása vagy a riasztás dinamikus küszöbértékének használata esetén.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Riasztás, ha a sikertelen kérelmek átlépik a küszöbértéket 

Riasztás létrehozása, ha a sikertelen kérelmek mérőszáma átlépi a küszöbértéket. Az átjárót éles környezetben kell megfigyelni a statikus küszöbérték megállapítása vagy a riasztás dinamikus küszöbértékének használata esetén.

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Riasztások Application Gateway v2 SKU-hoz (Standard_v2/WAF_v2)

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>Riasztás, ha a számítási egység kihasználtsága az átlagos használat 75%-át keresztezi 

A számítási egység a Application Gateway számítási kihasználtságának mértéke. Ellenőrizze az átlagos számítási egység használatát az elmúlt egy hónapban, és állítsa be a riasztást, ha az 75%-át teszi ki. Ha például az átlagos használat 10 számítási egység, állítson be egy riasztást 7,5 ke-ra. Ez riasztást küld, ha a használat növekszik, és időt ad a válaszadásra. Ha úgy gondolja, hogy a forgalom továbbra is fennáll, akkor a minimumot növelheti. A fenti skálázási javaslatok végrehajtásához szükség szerint végezze el a méretezést.

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Példa: riasztás beállítása az átlagos CU-használat 75%-ában

Ebből Azure Portal a példából megtudhatja, hogyan állíthatja be a riasztást, ha elérte a CU-használat 75%-át. 
1. Navigáljon a **Application Gateway**.
2. A bal oldali panelen a **figyelés** lapon válassza a **metrikák** lehetőséget. 
3. Metrika hozzáadása az **átlagos aktuális számítási egységekhez**. 
![WAF metrika beállítása](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Ha beállította a minimális példányszámot az átlagos CU-használathoz, folytassa a riasztást, ha a minimális példányok 75%-a használatban van. Ha például az átlagos használat 10 ke, állítson be egy riasztást 7,5 ke-ra. Ez riasztást küld, ha a használat növekszik, és időt ad a válaszadásra. Ha úgy gondolja, hogy a forgalom továbbra is fennáll, akkor a minimumot növelheti. 
![WAF riasztás beállítása](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> Beállíthatja, hogy a riasztás alacsonyabb vagy magasabb CU-kihasználtsági arányban történjen, attól függően, hogy mennyire érzékeny a potenciális forgalom.

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>Riasztás, ha a kapacitási egység kihasználtsága a maximális kihasználtság 75%-át keresztezi 

A kapacitási egységek az átjárók teljes kihasználtságát jelentik az átviteli sebesség, a számítás és a kapcsolatok száma tekintetében. Ellenőrizze a maximális kapacitás-egységek használatát az elmúlt egy hónapban, és állítsa be a riasztást, ha az 75%-át teszi ki. Ha például a maximális kihasználtság 100 kapacitású egység, akkor a rendszer riasztást állít be az 75 ke-ra. Ha szükséges, kövesse a fenti két javaslatot a vertikális felskálázáshoz.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Riasztás, ha a nem kifogástalan állapotú gazdagépek száma átlépi a küszöbértéket 

Ez a metrika azt jelzi, hogy hány háttér-kiszolgáló nem tudja sikeresen elvégezni az Application Gateway-t. Ez olyan problémákat fog észlelni, amelyekben az Application Gateway-példányok nem tudnak kapcsolódni a háttérhez. Riasztás, ha ez a szám a háttérbeli kapacitás 20%-át meghaladja. Például Ha jelenleg 30 háttér-kiszolgáló van a háttér-készletben, állítson be egy riasztást, ha a nem kifogástalan állapotú gazdagépek száma 6 fölé esik.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Riasztás, ha a válasz állapota (4xx, 5xx) átlépi a küszöbértéket 

Riasztás létrehozása, ha Application Gateway válasz állapota 4xx vagy 5xx. Átmeneti problémák miatt előfordulhat, hogy alkalmi 4xx vagy 5xx válaszol. Az átjárót éles környezetben kell megfigyelni a statikus küszöbérték megállapítása vagy a riasztás dinamikus küszöbértékének használata esetén.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Riasztás, ha a sikertelen kérelmek átlépik a küszöbértéket 

Riasztás létrehozása, ha a sikertelen kérelmek mérőszáma átlépi a küszöbértéket. Az átjárót éles környezetben kell megfigyelni a statikus küszöbérték megállapítása vagy a riasztás dinamikus küszöbértékének használata esetén.

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>Riasztás, ha a háttérbeli utolsó bájt válaszideje túllépi a küszöbértéket 

Ez a metrika a háttér-kiszolgálóval létesített kapcsolat létesítése és a válasz törzse utolsó bájtjának fogadása közötti időtartamot jelzi. Hozzon létre riasztást, ha a háttérbeli válasz késése több, mint a szokásostól bizonyos küszöbérték. Ha például a rendszer riasztást állít be, ha a háttérbeli válasz késése több mint 30%-kal nő a szokásos értéknél.

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>Riasztás, ha Application Gateway összesített idő küszöbértéke

Ez az az időtartam, amikor a Application Gateway megkapja a HTTP-kérelem első bájtját arra az időre, amikor az utolsó válasz bájtot elküldték az ügyfélnek. Riasztást kell létrehoznia, ha a háttérbeli válasz késése több, mint a szokásostól bizonyos küszöbérték. Ezt beállíthatja például úgy, hogy a rendszer riasztást küldjön, ha a teljes késés a szokásos értéknél több mint 30%-kal nő.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>A WAF beállítása a geofiltering és a bot Protection szolgáltatással a támadások leállításához
Ha további biztonsági réteget szeretne használni az alkalmazás előtt, használja a Application Gateway WAF_v2 SKU-t a WAF képességekhez. A v2 SKU-t úgy állíthatja be, hogy csak adott ország/régió vagy országok/régiók számára engedélyezze az alkalmazásokhoz való hozzáférést. A WAF egyéni szabály beállításával explicit módon engedélyezheti vagy letilthatja a forgalmat a földrajzi hely alapján. További információ: [geofiltering egyéni szabályok](../web-application-firewall/ag/geomatch-custom-rules.md) és [egyéni szabályok konfigurálása Application Gateway WAF_v2 SKU-n keresztül a PowerShell használatával](../web-application-firewall/ag/configure-waf-custom-rules.md).

Engedélyezze a bot Protectiont az ismert helytelen robotok blokkolásához. Ez csökkenti az alkalmazás felé irányuló forgalom mennyiségét. További információ: [a bot Protection beállítása a set up utasítással](../web-application-firewall/ag/configure-waf-custom-rules.md).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Diagnosztika bekapcsolása Application Gateway és WAF

A diagnosztikai naplók lehetővé teszik a tűzfalak, a Teljesítménynaplók és a hozzáférési naplók megtekintését. Az Azure-ban ezeket a naplókat használhatja az Application Gateway-alkalmazások kezeléséhez és hibakereséséhez. További információkért tekintse meg a [diagnosztika dokumentációját](./application-gateway-diagnostics.md#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>TLS-szabályzat beállítása további biztonsághoz
Győződjön meg arról, hogy a legújabb TLS-házirend verzióját használja ([AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s)). Ez kikényszeríti a TLS 1,2 és az erősebb titkosítási algoritmusokat. További információ: a [TLS-házirend verzióinak és a titkosítási csomagok konfigurálása a PowerShell](./application-gateway-configure-ssl-policy-powershell.md)használatával.