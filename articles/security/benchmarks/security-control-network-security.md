---
title: Azure Security Control – hálózati biztonság
description: Biztonság-vezérlési hálózati biztonság
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251872"
---
# <a name="security-control-network-security"></a>Biztonsági ellenőrzés: hálózati biztonság

A hálózati biztonsági javaslatok azt határozzák meg, hogy mely hálózati protokollok, TCP/UDP-portok és hálózati csatlakoztatott szolgáltatások engedélyezettek vagy tagadják meg az Azure-szolgáltatások elérését.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.1 | 9,2, 9,4, 14.1-14.3 | Ügyfél |

Győződjön meg arról, hogy az összes Virtual Network alhálózati üzemelő példány rendelkezik egy hálózati biztonsági csoporttal, amely az alkalmazás megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel rendelkezik. Az Azure-szolgáltatások használata engedélyezett privát kapcsolattal, üzembe helyezheti a szolgáltatást a vnet belül, vagy privát végpontok használatával kapcsolódhat. A szolgáltatásra vonatkozó követelményekért tekintse meg az adott szolgáltatásra vonatkozó biztonsági javaslatot.

Ha konkrét használati esettel rendelkezik, a követelmények teljesítése Azure Firewall megvalósításával lehetséges.

Általános információk a privát hivatkozásról:

https://docs.microsoft.com/azure/private-link/private-link-overview

Virtual Network létrehozása:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Biztonsági konfigurációval rendelkező NSG létrehozása:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure Firewall üzembe helyezése és konfigurálása:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.2 | 9,3, 12,2 | Ügyfél |

Az Azure-ban a hálózati erőforrások biztonságossá tételéhez használja a Azure Security Centert, és kövesse a hálózati védelmi javaslatok című témakört. Engedélyezze a NSG folyamat naplóit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében.

A NSG folyamat naplófájljainak engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Azure Security Center által biztosított hálózati biztonság ismertetése:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.3 | 9.5 | Ügyfél |

Telepítse az Azure webalkalmazási tűzfalat (WAF) a kritikus webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. Diagnosztikai beállítás engedélyezése a WAF és a naplók betöltéséhez egy Storage-fiókba, az Event hub-ba vagy a Log Analytics-munkaterületre.

Az Azure WAF üzembe helyezése:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.4 | 12,3 | Ügyfél |

Az Azure-beli virtuális hálózatok DDoS standard szintű védelmének engedélyezése a DDoS-támadások elleni védelem érdekében. Az ismert kártékony IP-címekkel folytatott kommunikáció megtagadásához használja Azure Security Center integrált veszélyforrások felderítését.

Azure Firewall üzembe helyezése a szervezet hálózati határain, és a fenyegetések felderítése engedélyezve van, és úgy van konfigurálva, hogy &quot;riasztást, és megtagadja a kártékony hálózati forgalom&quot;ét.

A NSG konfigurálásához használja a Azure Security Center igény szerinti hálózati hozzáférését, hogy korlátozza a végpontok a jóváhagyott IP-címekre való kitettségét korlátozott időtartamra.

Azure Security Center adaptív hálózati korlátozással olyan NSG-konfigurációkat javasolhat, amelyek a portok és a forrás IP-címeket korlátozzák a tényleges forgalom és a veszélyforrások felderítése alapján.

A DDoS Protection konfigurálása:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Firewall üzembe helyezése:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Az Azure Security Center integrált veszélyforrások felderítésének ismertetése:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Azure Security Center adaptív hálózat megerősítésének ismertetése:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure Security Center az időponthoz tartozó hálózati Access Control ismertetése:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.5 | 12,5, 15,8 | Ügyfél |

Rögzítse a NSG folyamat naplóit a flow-rekordok létrehozásához. Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze Network Watcher csomagok rögzítését.

A NSG folyamat naplófájljainak engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Network Watcher engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.6 | 12,6, 12,7 | Ügyfél |

Azure Firewall üzembe helyezése a szervezet hálózati határain, és a fenyegetések felderítése engedélyezve van, és úgy van konfigurálva, hogy &quot;riasztást, és megtagadja a kártékony hálózati forgalom&quot;ét.

Azure Firewall üzembe helyezése: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Riasztások konfigurálása Azure Firewall használatával: https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.7 | 12,9, 12,10 | Ügyfél |

Azure-Application Gateway üzembe helyezése a megbízható tanúsítványokhoz engedélyezett HTTPS/SSL-alapú webalkalmazásokhoz.

Application Gateway üzembe helyezése:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

A Application Gateway konfigurálása a HTTPS használatára:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

A 7. rétegbeli terheléselosztás az Azure Web Application Gateways használatával:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.8 | 1.5 | Ügyfél |

A hálózati biztonsági csoportokon vagy Azure Firewall a hálózati hozzáférés-vezérlések definiálásához használja Virtual Network szolgáltatás címkéit. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

A szolgáltatási címkék megismerése és használata:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.9 | 11,1 | Ügyfél |

A hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy.

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését, így például Azure Resource Manager sablonokat, RBAC-vezérlőket és házirendeket egyetlen terv definíciójában. A tervrajzot új előfizetésekre alkalmazhatja, és a verziószámozással finomhangolást és felügyeletet is alkalmazhat.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy minták a hálózatkezeléshez:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

Azure Blueprint létrehozása:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1.1 | 11,2 | Ügyfél |

Címkék használata a NSG és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz. Az egyes NSG-szabályok esetében a &quot;Description&quot; mezőben adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Virtual Network létrehozása:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

NSG létrehozása biztonsági konfigurációval:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 1,11 | 11,3 | Ügyfél |

A hálózati erőforrások konfigurációjának érvényesítéséhez (és/vagy szervizeléséhez) Azure Policy használható.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy minták a hálózatkezeléshez:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a következő biztonsági vezérlőt: [naplózás és figyelés](security-control-logging-monitoring.md)
