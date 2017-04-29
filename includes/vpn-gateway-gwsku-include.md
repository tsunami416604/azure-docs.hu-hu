Egy virtuális hálózati átjáró létrehozásakor meg kell adni a használni kívánt termékváltozatot. Ha egy átjáró magasabb szintű termékváltozatát választja, a rendszer több processzort és hálózati sávszélességet foglal le a számára, ezért az átjáró nagyobb hálózati átviteli sebesség támogatására képes a virtuális hálózaton.

A VPN Gateway a következő termékváltozatok használatára képes:

* Basic
* Standard
* HighPerformance

A VPN Gateway nem használja az UltraPerformance átjáró-termékváltozatot. Az UltraPerformance termékváltozattal kapcsolatos információkért lásd az [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) dokumentációját.

A termékváltozat kiválasztásakor vegye figyelembe az alábbiakat:

* PolicyBased-típusú VPN-ekhez az alapszintű átjáró-termékváltozatot kell használni. A PolicyBased VPN-eket (régebbi nevén Statikus útválasztás) semmilyen más termékváltozat nem támogatja.
* A BGP az alapszintű termékváltozathoz nem támogatott.
* Az alapszintű termékváltozat nem támogatja az ExpressRoute-VPN-átjárók egyszerre konfigurált beállításait.
* Aktív/aktív módú S2S VPN Gateway-kapcsolatokat csak a nagy teljesítményű termékváltozaton lehet konfigurálni.

