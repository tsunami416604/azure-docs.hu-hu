---
title: Application Gateway nagy forgalmú kötetek támogatása
description: Ez a cikk útmutatást nyújt az Azure Application Gateway konfigurálásához a nagy hálózati forgalomra vonatkozó forgatókönyvek támogatásához.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: b96720ead2c7b7bc942efca32a8510f57c2dbcad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85250248"
---
# <a name="application-gateway-high-traffic-support"></a>Nagy mennyiségű forgalom támogatása az Application Gatewayben

>[!NOTE]
> Ez a cikk néhány javasolt útmutatót tartalmaz, amelyek segítségével beállíthatja, hogy a Application Gateway a COVID-19 válság miatt esetlegesen előforduló nagy forgalmú forgalom miatt további forgalmat kezeljen.

A webalkalmazások forgalmát méretezhető és biztonságos módon kezelheti a webalkalmazási tűzfallal (WAF) Application Gateway használatával.

A következő javaslatok segítséget nyújtanak a WAF-vel való Application Gateway beállításában a további forgalom kezeléséhez.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Használja a v2 SKU-t a v1 protokollon keresztül az automatikus skálázási képességek és a teljesítmény előnyeihez
A v2 SKU automatikus skálázást biztosít annak biztosításához, hogy a Application Gateway képes legyen a forgalom növekedésére. Emellett további jelentős teljesítménybeli előnyöket is kínál, például az 5x-ös jobb TLS-kiszervezési teljesítményt, a gyorsabb üzembe helyezést és a frissítés időpontját, a zónák redundanciát és egyebeket a V1-hez képest. További információkért lásd a [v2 dokumentációját](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>A példányok maximális számának beállítása a lehető legnagyobb értékre (125)
 
Feltételezve, hogy rendelkezik egy Application Gateway v2 SKU-val, a maximális példányszámot a maximális 125 értékre állítja, így a Application Gateway igény szerint felskálázást tesz lehetővé. Ez lehetővé teszi, hogy kezelje az alkalmazásaihoz való adatforgalom lehetséges növekedését. Csak a használt kapacitási egységekre (ke) számítunk fel díjat.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>A példányok minimális számának beállítása az átlagos CU-használat alapján

Feltételezve, hogy rendelkezik egy Application Gateway v2 SKU-val, az automatikus skálázás 6 – hét percet vesz igénybe a horizontális felskálázás érdekében. A minimális példányszámnál a Application Gateway a terhelés növelésekor jobban kezelheti a forgalmat, mivel a forgalomban nem szükséges egy automatikus skálázási művelet.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Riasztás, ha egy adott metrika meghaladja a CU átlagos kihasználtságának 75%-át 
A metrikák és egyéb útmutatók részletes ismertetését lásd a [Application Gateway metrikák dokumentációjában](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) . 

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

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>A WAF beállítása a geofiltering és a bot Protection szolgáltatással a támadások leállításához
Ha további biztonsági réteget szeretne használni az alkalmazás előtt, használja a Application Gateway WAF_v2 SKU-t a WAF képességekhez. A v2 SKU-t úgy állíthatja be, hogy csak adott ország/régió vagy országok/régiók számára engedélyezze az alkalmazásokhoz való hozzáférést. A WAF egyéni szabály beállításával explicit módon engedélyezheti vagy letilthatja a forgalmat a földrajzi hely alapján. További információ: [geofiltering egyéni szabályok](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) és [egyéni szabályok konfigurálása Application Gateway WAF_v2 SKU-n keresztül a PowerShell használatával](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

Engedélyezze a bot Protectiont az ismert helytelen robotok blokkolásához. Ez csökkenti az alkalmazás felé irányuló forgalom mennyiségét. További információ: [a bot Protection beállítása a set up utasítással](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Diagnosztika bekapcsolása Application Gateway és WAF

A diagnosztikai naplók lehetővé teszik a tűzfalak, a Teljesítménynaplók és a hozzáférési naplók megtekintését. Az Azure-ban ezeket a naplókat használhatja az Application Gateway-alkalmazások kezeléséhez és hibakereséséhez. További információkért tekintse meg a [diagnosztika dokumentációját](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>TLS-szabályzat beállítása további biztonsághoz
Győződjön meg arról, hogy a legújabb TLS-házirend verzióját használja ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)). Ez kikényszeríti a TLS 1,2 és az erősebb titkosítási algoritmusokat. További információ: a [TLS-házirend verzióinak és a titkosítási csomagok konfigurálása a PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell)használatával.
