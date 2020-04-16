---
title: Azure Security Control – Hálózati biztonság
description: Az Azure Security Control hálózati biztonság
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f508de35470043cc71bfc8607367f28c04440b57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408300"
---
# <a name="security-control-network-security"></a>Biztonsági ellenőrzés: Hálózati biztonság

A hálózati biztonsági javaslatok arra összpontosítanak, hogy meghatározzák, mely hálózati protokollok, TCP/UDP-portok és hálózati csatlakoztatott szolgáltatások engedélyezettek vagy megtagadva az Azure-szolgáltatásokhoz való hozzáférést.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Az Azure-erőforrások védelme a virtuális hálózatokon belül

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1, 14.2, 14.3 | Ügyfél |

Győződjön meg arról, hogy a virtuális hálózat minden alhálózati központi telepítésen van egy hálózati biztonsági csoport, amely az alkalmazás megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel rendelkezik. Ha elérhető, használja a privát végpontok a privát kapcsolat tal az Azure-szolgáltatás erőforrásait a virtuális hálózatra a virtuális hálózat identitásának a szolgáltatásra való kiterjesztésével. Ha a privát végpontok és a privát kapcsolat nem érhető el, használja a szolgáltatásvégpontok. A szolgáltatásspecifikus követelményekről az adott szolgáltatásra vonatkozó biztonsági javaslatban tájékot. 

Másik lehetőségként, ha egy adott használati eset, követelmény lehet teljesíteni az Azure Firewall megvalósításával.

- [A virtuális hálózati szolgáltatás végpontjainak ismertetése](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [Az Azure privát hivatkozásának megismerése](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [Hogyan hozzunk létre egy virtuális hálózat](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [NSG létrehozása biztonsági konfigurációval](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [Az Azure tűzfal telepítése és konfigurálása](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: A virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.2 | 9.3, 12.2, 12.8 | Ügyfél |

Használja az Azure Security Centert, és kövesse a hálózatvédelmi javaslatokat a hálózati erőforrások azure-beli védelméhez. Engedélyezze az NSG-folyamatnaplókat, és küldjön naplókat egy tárfiókba a forgalomnaplózáshoz. NSG-folyamatnaplókat is küldhet a Log Analytics-munkaterületre, és a Traffic Analytics segítségével betekintést nyújthat az Azure-felhőben a forgalom áramlásába. A Traffic Analytics néhány előnye a hálózati tevékenység vizualizálása és a forró pontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalomáramlási minták megértése és a hálózati helytelen konfigurációk azonosítása.

- [Az NSG-folyamatnaplók engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [A Traffic Analytics engedélyezése és használata](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Az Azure Security Center által biztosított hálózati biztonság megismerése](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1.3: Védje a kritikus webes alkalmazásokat

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.3 | 9.5 | Ügyfél |

Telepítse az Azure Web Application Firewall (WAF) a kritikus webalkalmazások előtt a bejövő forgalom további vizsgálata. Engedélyezze a diagnosztikai beállítást a WAF-hez, és betöltése naplók egy tárfiókba, Event Hub vagy Log Analytics-munkaterület.

- [Az Azure WAF telepítése](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert rosszindulatú IP-címekkel folytatott kommunikáció megtagadása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.4 | 12.3 | Ügyfél |

Engedélyezze a DDoS Standard védelmet az Azure virtuális hálózatokon a DDoS-támadások elleni védelem érdekében. Az Azure Security Center integrált fenyegetésfelderítési szolgáltatásával megtagadhatja az ismert rosszindulatú IP-címekkel folytatott kommunikációt.

Telepítse az Azure Firewall-t a szervezet minden hálózati határain, és a fenyegetésfelderítés engedélyezve van, és úgy van beállítva, hogy "Riasztás és megtagadás" a rosszindulatú hálózati forgalom esetén.

Az Azure Security Center Just In Time hálózati hozzáféréssel konfigurálhatja az NSG-ket, hogy korlátozott ideig korlátozza a végpontok kitettségét a jóváhagyott IP-címekre.

Az Azure Security Center Adaptive Network Hardening használatával olyan NSG-konfigurációkat javasolhat, amelyek a tényleges forgalom és a fenyegetésfelderítés alapján korlátozzák a portokat és a forrás IP-ket.

- [A DDoS-védelem konfigurálása](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Az Azure tűzfal telepítése](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Az Azure Security Center integrált fenyegetésekkel kapcsolatos intelligenciájának megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Az Azure Security Center Adaptive Network hardening (Az Azure Security Center Adaptive Network Hardening) megismerésének ismertetése](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Az Azure Security Center just in time hálózati hozzáférés-vezérlése](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rögzítése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.5 | 12.5 | Ügyfél |

Engedélyezze a Network Watcher csomagrögzítését a rendellenes tevékenységek kivizsgálásához.

- [A Network Watcher engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózati behatolásészlelő/behatolás-megelőző rendszerek (IDS/IPS) telepítése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.6 | 12.6, 12.7 | Ügyfél |

Válasszon ki egy ajánlatot az Azure Piactérről, amely támogatja az IDS/IPS funkciókat a hasznos adatvizsgálati képességekkel.  Ha a behatolásészlelés és/vagy a hasznos adatvizsgálaton alapuló megelőzés nem követelmény, az Azure Firewall fenyegetési intelligenciával használható. Az Azure Firewall Threat intelligenciaalapú szűrés riasztást adhat és megtagadhatja az ismert rosszindulatú IP-címekre és tartományokra irányuló forgalmat. Az IP-címek és tartományok a Microsoft Threat Intelligence hírcsatornából származnak.

Telepítse az Ön által választott tűzfalmegoldást a szervezet minden hálózati határain a rosszindulatú forgalom észleléséhez és/vagy megtagadásához.

- [Azure Piactér](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Az Azure tűzfal telepítése](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Riasztások konfigurálása az Azure Tűzfallal](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1.7: A webes alkalmazások forgalmának kezelése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.7 | 12.9, 12.10 | Ügyfél |

Telepítse az Azure Application Gateway-t olyan webes alkalmazásokhoz, amelyeken https/SSL engedélyezve van a megbízható tanúsítványokhoz.

- [Az Alkalmazásátjáró telepítése](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [Az Application Gateway beállítása a HTTPS használatára](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [A 7.](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.8 | 1.5 | Ügyfél |

A virtuális hálózati szolgáltatás címkék segítségével hálózati hozzáférés-vezérlést definiáljon a hálózati biztonsági csoportokon vagy az Azure tűzfalon. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének (pl. ApiManagement) megadásával a szabály megfelelő forrás- vagy célmezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásakor.

Az alkalmazásbiztonsági csoportok segítségével egyszerűsítheti az összetett biztonsági beállításokat is. Az alkalmazásbiztonsági csoportokkal az alkalmazás struktúrájának természetes bővítményeként konfigurálhatja a hálózati biztonságot, így csoportosíthatja a virtuális gépeket, és ezen csoportok alapján meghatározhatja a hálózati biztonsági szabályokat.

- [A szolgáltatáscímkék ismertetése és használata](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Az alkalmazásbiztonsági csoportok ismertetése és használata](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációinak karbantartása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.9 | 11.1 | Ügyfél |

Az Azure Policy segítségével szabványos biztonsági konfigurációkat definiálhat és valósíthat meg a hálózati erőforrásokhoz.

Azure Blueprints is használhatja a nagy méretű Azure-központi telepítések a csomagolás kulcsfontosságú környezeti összetevők, például az Azure Resources Manager-sablonok, RBAC-vezérlők és szabályzatok, egyetlen tervezet definíciójában. A tervezetet alkalmazhatja az új előfizetésekre, és a verziószámozással finomhangolhatja a vezérlést és a felügyeletet.

- [Az Azure-szabályzat konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure-szabályzatminták hálózatépítéshez](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [Azure Blueprint létrehozása](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.10 | 11.2 | Ügyfél |

Használja címkék NSG-k és egyéb kapcsolódó források a hálózati biztonság és a forgalom áramlását. Az egyes NSG-szabályok esetében a "Leírás" mezőben adhatja meg az üzleti igényt és/vagy az időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló/onnan érkező forgalmat.

Használja a címkézéssel kapcsolatos beépített Azure-szabályzat-definíciók bármelyikét, például a "Címke és annak értéke" használatával győződjön meg arról, hogy minden erőforrás címkékkel jön létre, és értesítse a meglévő címkézetlen erőforrásokról.

Használhatja az Azure PowerShell vagy az Azure CLI a look-up, vagy műveleteket hajt végre az erőforrások alapján a címkéket.

- [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Hogyan hozzunk létre egy virtuális hálózat](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Hogyan hozzunk létre egy NSG egy biztonsági config](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrás-konfigurációk figyelésére és a változások észlelésére

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 1.11 | 11.3 | Ügyfél |

Az Azure Activity Log használatával figyelheti az erőforrás-konfigurációkat, és észlelheti az Azure-erőforrások változásait. Hozzon létre riasztásokat az Azure Monitoron belül, amelyek a kritikus erőforrások módosításakor aktiválódnak.

- [Az Azure-tevékenységnapló eseményeinek megtekintése és beolvasása](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Riasztások létrehozása az Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>További lépések

- Tekintse meg a következő biztonsági vezérlőt: [Naplózás és figyelés](security-control-logging-monitoring.md)
