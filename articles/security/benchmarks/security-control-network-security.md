---
title: Azure Security Control – hálózati biztonság
description: Azure Security Control hálózati biztonság
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: dad01212be3589af7167082ff22c624fa776772a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82193122"
---
# <a name="security-control-network-security"></a>Biztonsági ellenőrzés: hálózati biztonság

A hálózati biztonsági javaslatok azt határozzák meg, hogy mely hálózati protokollok, TCP/UDP-portok és hálózati csatlakoztatott szolgáltatások engedélyezettek vagy tagadják meg az Azure-szolgáltatások elérését.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.1 | 9,2, 9,4, 14,1, 14,2, 14,3 | Ügyfél |

Győződjön meg arról, hogy az összes Virtual Network alhálózati üzemelő példány rendelkezik egy hálózati biztonsági csoporttal, amely az alkalmazás megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel rendelkezik. Ha elérhető, privát végpontok használatával gondoskodhat az Azure-szolgáltatások erőforrásainak virtuális hálózatra való védelméről, ha a VNet-identitást kiterjesztjük a szolgáltatásra. Ha a privát végpontok és a privát hivatkozások nem érhetők el, használja a szolgáltatási végpontokat. A szolgáltatásra vonatkozó követelményekért tekintse meg az adott szolgáltatásra vonatkozó biztonsági javaslatot. 

Ha konkrét használati esettel rendelkezik, a követelmények teljesítése Azure Firewall megvalósításával lehetséges.

- [Virtual Network szolgáltatási végpontok ismertetése](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [Az Azure privát hivatkozásának megismerése](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [Virtual Network létrehozása](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Biztonsági konfigurációval rendelkező NSG létrehozása](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [Azure Firewall üzembe helyezése és konfigurálása](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.2 | 9,3, 12,2, 12,8 | Ügyfél |

Az Azure-ban a hálózati erőforrások biztonságossá tételéhez használja a Azure Security Centert, és kövesse a hálózati védelmi javaslatok című témakört. Engedélyezze a NSG folyamat naplóit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [A Traffic Analytics engedélyezése és használata](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.3 | 9,5 | Ügyfél |

Telepítse az Azure webalkalmazási tűzfalat (WAF) a kritikus webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. Diagnosztikai beállítás engedélyezése a WAF és a naplók betöltéséhez egy Storage-fiókba, az Event hub-ba vagy a Log Analytics-munkaterületre.

- [Az Azure WAF üzembe helyezése](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.4 | 12,3 | Ügyfél |

Az Azure-beli virtuális hálózatok DDoS standard szintű védelmének engedélyezése a DDoS-támadások elleni védelem érdekében. Az ismert kártékony IP-címekkel folytatott kommunikáció megtagadásához használja Azure Security Center integrált veszélyforrások felderítését.

Azure Firewall üzembe helyezése minden szervezet hálózati határain, a fenyegetések felderítése engedélyezve van, és a "riasztás és megtagadás" értékre van állítva a kártékony hálózati forgalom esetében.

A NSG konfigurálásához használja a Azure Security Center igény szerinti hálózati hozzáférését, hogy korlátozza a végpontok a jóváhagyott IP-címekre való kitettségét korlátozott időtartamra.

Azure Security Center adaptív hálózati korlátozással olyan NSG-konfigurációkat javasolhat, amelyek a portok és a forrás IP-címeket korlátozzák a tényleges forgalom és a veszélyforrások felderítése alapján.

- [A DDoS Protection konfigurálása](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Azure Firewall üzembe helyezése](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Azure Security Center adaptív hálózat megerősítésének ismertetése](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Azure Security Center az időponthoz tartozó hálózati Access Control ismertetése](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.5 | 12,5 | Ügyfél |

Network Watcher csomagok rögzítésének engedélyezése a rendellenes tevékenységek kivizsgálásához.

- [A Network Watcher engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.6 | 12,6, 12,7 | Ügyfél |

Válasszon ki egy olyan ajánlatot az Azure piactéren, amely támogatja az AZONOSÍTÓk/IP-címek funkciót a hasznos adatok ellenőrzésére szolgáló funkciókkal.  Ha a betörési észlelés és/vagy a tartalom-ellenőrzésen alapuló megelőzés nem követelmény, akkor az Azure Firewall a veszélyforrások felderítésére használható. Azure Firewall fenyegetés intelligencián alapuló szűréssel riasztást kaphat, és megtagadhatja az ismert kártékony IP-címek és tartományok felé irányuló forgalmat. Az IP-címek és tartományok forrása a Microsoft Threat Intelligence-hírcsatorna.

Az Ön által választott tűzfal-megoldás üzembe helyezésével azonosíthatja és/vagy megtagadhatja a kártékony forgalmat.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall üzembe helyezése](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Riasztások konfigurálása Azure Firewall](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.7 | 12,9, 12,10 | Ügyfél |

Az Azure Application Gateway üzembe helyezése a megbízható tanúsítványok számára engedélyezett HTTPS/TLS-alapú webalkalmazásokhoz.

- [Application Gateway üzembe helyezése](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [A Application Gateway konfigurálása a HTTPS használatára](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [A 7. rétegbeli terheléselosztás és az Azure-webalkalmazás-átjárók ismertetése](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.8 | 1.5 | Ügyfél |

A hálózati biztonsági csoportokon vagy Azure Firewall a hálózati hozzáférés-vezérlések definiálásához használja Virtual Network szolgáltatás címkéit. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Emellett az alkalmazás biztonsági csoportjaival is egyszerűsítheti az összetett biztonsági konfigurációt. Az alkalmazásbiztonsági csoportokkal az alkalmazás struktúrájának természetes bővítményeként konfigurálhatja a hálózati biztonságot, így csoportosíthatja a virtuális gépeket, és ezen csoportok alapján meghatározhatja a hálózati biztonsági szabályokat.

- [A szolgáltatási címkék megismerése és használata](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Az alkalmazás biztonsági csoportjai megismerése és használata](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.9 | 11,1 | Ügyfél |

A hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy.

Az Azure-tervrajzok segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését, például az Azure Resources Manager-sablonokat, a RBAC-vezérlőket és a házirendeket egyetlen terv definíciójában. A tervrajzot új előfizetésekre alkalmazhatja, és az irányítás és felügyelet finomhangolását a verziószámozás segítségével végezheti el.

- [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Policy minták a hálózatkezeléshez](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [Azure Blueprint létrehozása](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.10 | 11,2 | Ügyfél |

Címkék használata a NSG és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

- [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Virtual Network létrehozása](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [NSG létrehozása biztonsági konfigurációval](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1,11 | 11,3 | Ügyfél |

Az Azure-tevékenység naplójának használatával figyelheti az erőforrás-konfigurációkat, és felderítheti az Azure-erőforrások módosításait. Hozzon létre riasztásokat a Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus erőforrások változásai megváltoznak.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Riasztások létrehozása a Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>További lépések

- Tekintse meg a következő biztonsági vezérlőt: [naplózás és figyelés](security-control-logging-monitoring.md)
