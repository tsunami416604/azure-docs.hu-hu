|  | **Pont-pont** | **Pont-pont** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Azure támogatott szolgáltatások** |Cloud Services és Virtual Machines |Cloud Services és Virtual Machines |[Szolgáltatáslista](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Jellemző sávszélességek** |Jellemzően < 100 mbps (összesen) |Jellemzően < 1 GB/s összesítés |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps |
| **Támogatott protokollok** |Secure Sockets Tunneling Protocol (SSTP) |IPsec |Közvetlen kapcsolat VLAN-okon, NSP-k VPN technológiáin keresztül (MPLS, VPLS,...) |
| **Útválasztás** |RouteBased (dinamikus) |Támogatott PolicyBased (statikus útválasztás) és RouteBased (dinamikus útválasztási VPN) |BGP |
| **Kapcsolat rugalmassága** |aktív-passzív |aktív-passzív vagy aktív-aktív |aktív-aktív |
| **Tipikus használati eset** |Prototípus-készítés, fejlesztői / tesztelői / laboratóriumi forgatókönyvek felhőszolgáltatásokhoz és virtuális gépekhez |Fejlesztői / tesztelői / laboratóriumi forgatókönyvek és kis léptékű termelési számítási feladatok felhőszolgáltatásokhoz és virtuális gépekhez |Hozzáférés az összes Azure-szolgáltatáshoz (ellenőrzött lista), nagyvállalati szintű és kritikus fontosságú számítási feladatokhoz, biztonsági mentésekhez, big data adatokhoz és az Azure-hoz DR-webhelyként |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Díjszabás** |[Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Díjszabás](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Technikai dokumentáció** |[VPN-átjáró dokumentációjában talál.](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[VPN-átjáró dokumentációjában talál.](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Az ExpressRoute dokumentációja](https://azure.microsoft.com/documentation/services/expressroute/) |
| **Gyakori kérdések** |[VPN Gateway – gyakori kérdések](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[VPN Gateway – gyakori kérdések](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[ExpressRoute – Gyakori kérdések](../articles/expressroute/expressroute-faqs.md) |

