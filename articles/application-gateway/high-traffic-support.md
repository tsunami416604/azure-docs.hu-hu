---
title: Application Gateway nagy forgalmú mennyiség támogatása
description: Ez a cikk útmutatást nyújt az Azure Application Gateway konfigurálásához a nagy hálózati forgalomban szereplő forgatókönyvek támogatása érdekében.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 65f404b52a5fc06d8fa5bb5aad291e57fde8caba
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617202"
---
# <a name="application-gateway-high-traffic-support"></a>Nagy mennyiségű forgalom támogatása az Application Gatewayben

>[!NOTE]
> Ez a cikk néhány javasolt útmutatást ismertet, amelyek segítenek az Application Gateway beállításában a COVID-19 válság miatt fellépő nagy forgalmú forgalom kezelése érdekében.

Az Application Gateway webalkalmazás-tűzfallal (WAF) skálázható és biztonságos módon kezelheti a webalkalmazások forgalmát.

Az alábbi javaslatok segítenek az Application Gateway WAF-fel történő beállításában a további forgalom kezelésére.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Használja a v2 Termékváltozat ot a v1-en keresztül az automatikus skálázási képességekhez és a teljesítményhez
A v2 Termékváltozat automatikus skálázást kínál annak érdekében, hogy az Application Gateway a forgalom növekedésével növekedjen. Emellett más jelentős teljesítménybeli előnyöket is kínál, például az 5x jobb TLS-kiszervezési teljesítményt, a gyorsabb üzembe helyezési és frissítési időket, a zónaredundanciát és még többet a v1-hez képest. További információt a [v2 dokumentációjában](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)talál. 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>A példányok maximális számának beállítása a lehető legnagyobb értékre (125)
 
Feltéve, hogy egy Application Gateway v2 Termékváltozat, a maximális példányszám beállítása a maximális lehetséges érték 125 lehetővé teszi, hogy az Application Gateway szükség szerint horizontális felskálázás. Ez lehetővé teszi, hogy kezelje az alkalmazások forgalmának lehetséges növekedését. A használt kapacitásegységekért csak díjat számítunk fel.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Állítsa be a minimális példányszámot az átlagos CU-használat alapján

Feltéve, hogy egy Application Gateway v2 termékváltozat, automatikus skálázás hat-hét percet vesz igénybe horizontális felskálázás. A nagyobb minimális példányszám, az Application Gateway jobban tudja kezelni a forgalmat, ha a terhelés növekszik, mert a forgalom kiugrása nem igényel automatikus skálázási műveletet.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Riasztás, ha egy bizonyos metrika meghaladja az átlagos CU-kihasználtság 75%-át 
Tekintse meg az [Application Gateway metrikák dokumentációját](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) a metrikák és egyéb forgatókönyvek részletes magyarázatáért. 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Példa: Riasztás beállítása az átlagos CU-használat 75%-ára

Ebben a példában bemutatja, hogyan használhatja az Azure Portalon egy riasztás beállítása, ha az átlagos CU-használat 75%-a elérte. 
1. Nyissa meg az **Alkalmazásátjárót.**
2. A bal oldali panelen válassza **a Metrikák** lehetőséget a **Figyelés** lapon. 
3. Adjon hozzá egy metrikát az **átlagos aktuális számítási egységekhez.** 
![Waf-mérőszám beállítása](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Ha a minimális példányszámot az átlagos CU-használatra állította be, folytassa a riasztást, ha a minimális példányok 75%-a használatban van. Ha például az átlagos használat 10 cus, állítson be riasztást 7.5 CU.For example, if your average usage is 10 CUs, set a alert on 7.5 CUs. Ez figyelmezteti, ha a használat növekszik, és időt ad a válaszadásra. Növelheti a minimumot, ha úgy gondolja, hogy ez a forgalom fenntartható lesz, hogy figyelmeztesse Önt, hogy a forgalom növekedhet. 
![WAF-riasztás beállítása](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> Beállíthatja, hogy a riasztás alacsonyabb vagy magasabb CU kihasználtsági százalékban történjen attól függően, hogy mennyire szeretné bizalmasan beállítani a potenciális forgalmi csúcsokat.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>A WAF beállítása geoszűréssel és botvédelemmel a támadások leállításához
Ha egy további biztonsági réteget szeretne az alkalmazás előtt, használja az Application Gateway WAF_v2 Termékváltozat waf-képességek. Beállíthatja, hogy a v2 Termékváltozat csak egy adott országból vagy országokból származó alkalmazásokhoz való hozzáférést engedélyezze. Waf-egyéni szabályt állít be, amely kifejezetten engedélyezi vagy blokkolja a földrajzi helyen alapuló forgalmat. További információt az [egyéni szabályok geoszűrése](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) című témakörben talál, valamint [arról, hogy miként konfigurálhatja az egyéni szabályokat az Application Gateway WAF_v2 termékváltozaton keresztül a PowerShellen keresztül.](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)

Engedélyezze a robotvédelem et az ismert rossz robotok blokkolására. Ez csökkenti az alkalmazáshoz való forgalom mennyiségét. További információt a [robotvédelem beállítási utasításokkal című](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)témakörben talál.

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>A diagnosztika bekapcsolása az Application Gateway és a WAF szolgáltatásban

A diagnosztikai naplók lehetővé teszik a tűzfalnaplók, a teljesítménynaplók és a hozzáférési naplók megtekintését. Ezeket a naplókat az Azure-ban az Alkalmazásátjárók kezeléséhez és hibaelhárításához használhatja. További információt a [diagnosztikai dokumentációban](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging)talál. 

## <a name="set-up-an-tls-policy-for-extra-security"></a>TLS-házirend beállítása a fokozott biztonság érdekében
Győződjön meg arról, hogy a legújabb TLS-házirendverziót[(AppGwSslPolicy20170401S)](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)használja. Ez kikényszeríti a TLS 1.2 és erősebb titkosításokat. További információt a [TLS-házirendverziók és titkosítási csomagok PowerShell en keresztüli konfigurálása című](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell)témakörben talál.
