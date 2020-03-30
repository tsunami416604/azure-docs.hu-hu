---
title: Azure Security Control – Hálózati biztonság
description: Biztonsági vezérlő hálózati biztonság
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251872"
---
# <a name="security-control-network-security"></a>Biztonsági ellenőrzés: Hálózati biztonság

A hálózati biztonsági javaslatok arra összpontosítanak, hogy meghatározzák, mely hálózati protokollok, TCP/UDP-portok és hálózati csatlakoztatott szolgáltatások engedélyezettek vagy megtagadva az Azure-szolgáltatásokhoz való hozzáférést.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Erőforrások védelme hálózati biztonsági csoportokkal vagy az Azure tűzfallal a virtuális hálózaton

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1-14.3 | Ügyfél |

Győződjön meg arról, hogy a virtuális hálózat minden alhálózati központi telepítésen van egy hálózati biztonsági csoport, amely az alkalmazás megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel rendelkezik. Használja az Azure Services tanonckapcsolattal engedélyezve van, telepítse a szolgáltatást a virtuális hálózaton belül, vagy csatlakozzon privát végpontok használatával. A szolgáltatásspecifikus követelményekről az adott szolgáltatásra vonatkozó biztonsági javaslatban tájékot.

Másik lehetőségként, ha egy adott használati eset, követelmények is teljesülnek az Azure Firewall megvalósításával.

Általános információk a Privát Linkről:

https://docs.microsoft.com/azure/private-link/private-link-overview

Hogyan hozzunk létre egy virtuális hálózat:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

NSG létrehozása biztonsági konfigurációval:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Az Azure tűzfal telepítése és konfigurálása:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: A virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.2 | 9.3, 12.2 | Ügyfél |

Használja az Azure Security Centert, és kövesse a hálózatvédelmi javaslatokat a hálózati erőforrások azure-beli védelméhez. Engedélyezze az NSG-folyamatnaplókat, és küldjön naplókat egy tárfiókba a forgalomnaplózáshoz.

Az NSG-folyamatnaplók engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Az Azure Security Center által biztosított hálózati biztonság megismerése:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3: Védje a kritikus webes alkalmazásokat

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.3 | 9.5 | Ügyfél |

Telepítse az Azure Web Application Firewall (WAF) a kritikus webalkalmazások előtt a bejövő forgalom további vizsgálata. Engedélyezze a diagnosztikai beállítást a WAF-hez, és betöltése naplók egy tárfiókba, Event Hub vagy Log Analytics-munkaterület.

Az Azure WAF telepítése:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert rosszindulatú IP-címekkel folytatott kommunikáció megtagadása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.4 | 12.3 | Ügyfél |

Engedélyezze a DDoS Standard védelmet az Azure virtuális hálózatokon a DDoS-támadások elleni védelem érdekében. Az Azure Security Center integrált fenyegetésfelderítési szolgáltatásával megtagadhatja az ismert rosszindulatú IP-címekkel folytatott kommunikációt.

Telepítse az Azure Firewall-t a szervezet minden hálózati határain, &quot;és&quot; a fenyegetésfelderítés engedélyezve van, és úgy van beállítva, hogy riasztást küld, és megtagadja a rosszindulatú hálózati forgalmat.

Az Azure Security Center Just In Time hálózati hozzáféréssel konfigurálhatja az NSG-ket, hogy korlátozott ideig korlátozza a végpontok kitettségét a jóváhagyott IP-címekre.

Az Azure Security Center Adaptive Network Hardening használatával olyan NSG-konfigurációkat javasolhat, amelyek a tényleges forgalom és a fenyegetésfelderítés alapján korlátozzák a portokat és a forrás IP-ket.

A DDoS-védelem konfigurálása:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Az Azure Tűzfal telepítése:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Ismerje meg az Azure Security Center integrált fenyegetésekkel kapcsolatos intelligenciáját:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Ismerje meg az Azure Security Center Adaptive Network edzését:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Ismerje meg az Azure Security Center just in time hálózati hozzáférés-vezérlését:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5: Hálózati csomagok és folyamatnaplók rögzítése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.5 | 12.5, 15.8 | Ügyfél |

NSG-folyamatnaplók rögzítése egy tárfiókba folyamatrekordok létrehozásához. Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze a Network Watcher csomagrögzítését.

Az NSG-folyamatnaplók engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Network Watcher engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózati behatolásészlelő/behatolás-megelőző rendszerek (IDS/IPS) telepítése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.6 | 12.6, 12.7 | Ügyfél |

Telepítse az Azure Firewall-t a szervezet minden hálózati határain, &quot;és&quot; a fenyegetésfelderítés engedélyezve van, és úgy van beállítva, hogy riasztást küld, és megtagadja a rosszindulatú hálózati forgalmat.

Az Azure Tűzfal telepítése:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

A riasztások konfigurálása az Azure Tűzfallal:https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7: A webes alkalmazások forgalmának kezelése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.7 | 12.9, 12.10 | Ügyfél |

Telepítse az Azure Application Gateway-t olyan webes alkalmazásokhoz, amelyeken https/SSL engedélyezve van a megbízható tanúsítványokhoz.

Az Application Gateway telepítése:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Az Application Gateway https használatára történő beállítása:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

A 7.

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.8 | 1.5 | Ügyfél |

A virtuális hálózati szolgáltatás címkék segítségével hálózati hozzáférés-vezérlést definiáljon a hálózati biztonsági csoportokon vagy az Azure tűzfalon. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének (pl. ApiManagement) megadásával a szabály megfelelő forrás- vagy célmezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásakor.

A szolgáltatáscímkék ismertetése és használata:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációinak karbantartása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.9 | 11.1 | Ügyfél |

Az Azure Policy segítségével szabványos biztonsági konfigurációkat definiálhat és valósíthat meg a hálózati erőforrásokhoz.

Azure Blueprints is használhatja a nagy méretű Azure-központi telepítések a csomagolás kulcsfontosságú környezeti összetevők, például az Azure Resource Manager-sablonok, RBAC-vezérlők és szabályzatok, egyetlen tervezet definíciójában. A tervezetet alkalmazhatja az új előfizetésekre, és a verziószámozással finomhangolhatja a vezérlést és a felügyeletet.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy-minták hálózatépítéshez:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

Azure Blueprint létrehozása:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.1 | 11.2 | Ügyfél |

Használja címkék NSG-k és egyéb kapcsolódó források a hálózati biztonság és a forgalom áramlását. Az egyes NSG-szabályok &quot;&quot; esetében a Leírás mezőben adhatja meg az üzleti igényeket és/vagy az időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatba/hálózatról érkező forgalmat.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Hogyan hozzunk létre egy virtuális hálózat:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Hogyan hozzunk létre egy NSG egy biztonsági config:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrás-konfigurációk figyelésére és a változások észlelésére

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.11 | 11.3 | Ügyfél |

Az Azure-szabályzat használatával érvényesítheti (és/vagy kiigazítja) a hálózati erőforrások konfigurációját.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy-minták hálózatépítéshez:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>További lépések

- Tekintse meg a következő biztonsági vezérlőt: [Naplózás és figyelés](security-control-logging-monitoring.md)
