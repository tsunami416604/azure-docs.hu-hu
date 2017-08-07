Egy virtuális hálózati átjáró létrehozásakor meg kell adni a használni kívánt termékváltozatot. Válassza ki a számítási feladatok, a teljesítmény, a funkciók és a szolgáltatói szerződés igényeinek megfelelő termékváltozatokat.

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

###  <a name="workloads"></a>Termelés *vs.* Dev-Test számítási feladatok

A szolgáltatói szerződések és a szolgáltatáskészletek eltérései miatt az alábbi termékváltozatokat javasoljuk termelés *vs.* dev-test környezetekhez:

| **Számítási feladat**                       | **Termékváltozatok**               |
| ---                                | ---                    |
| **Termelés, kritikus fontosságú számítási feladatok** | VpnGw1, VpnGw2, VpnGw3 |
| **Dev-test vagy a koncepció igazolása**   | Basic                  |
|                                    |                        |

Ha a régi termékváltozatokat használja, a termelési termékváltozatnak a Standard és a Nagy teljesítményű termékváltozatot javasoljuk. További információ a régi termékváltozatokról: [Átjáró termékváltozatai (örökölt termékváltozatok)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).

###  <a name="feature"></a>Átjáró termékváltozatainak szolgáltatáskészletei

Az átjárók új termékváltozatai egyszerűbbé teszik az átjárókon elérhető szolgáltatáskészleteket alkalmazását:

| **Termékváltozat**| **Szolgáltatások**|
| ---    | ---         |
|Alapszintű   | Útvonalalapú: 10 alagút P2S-sel<br>Házirend-alapú (IKEv1): 1 alagút, P2S nélkül|
| VpnGw1, VpnGw2, VpnGw3 |Útvonalalapú VPN legfeljebb 30 alagúttal (*) <br>P2S, BGP, aktív-aktív, egyéni IPsec/IKE-házirend, ExpressRoute/ VPN együttes jelenléte |
|        |             |

(*) A „PolicyBasedTrafficSelectors” paraméter konfigurálásával egy útvonalalapú VPN-átjárót (VpnGw1, VpnGw2, VpnGw3) több helyszíni, házirendalapú tűzfaleszközhöz is csatlakoztathat. További részletekért tekintse meg a [VPN-átjárók több helyszíni házirendalapú VPN-eszközhöz való csatlakoztatása a PowerShellel](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) című cikket.

###  <a name="resize"></a>Az átjárók termékváltozatainak átméretezése

1. Az átméretezés során a VpnGw1, a VpnGw2 és a VpnGw3 termékváltozatok közül választhat.
2. Ha a régi átjárók termékváltozataival dolgozik, az átméretezéskor az Alapszintű, a Standard és a Nagy teljesítményű termékváltozatok közül választhat.
2. Az Alapszintű/Standard/Nagy teljesítményű termékváltozatokról **nem** méretezhet a VpnGw1/VpnGw2/VpnGw3 termékváltozatokra. Ehelyett [migrálnia](#migrate) kell az új termékváltozatokra.

###  <a name="migrate"></a>Migrálás a régi termékváltozatokról az új termékváltozatokra

[!INCLUDE [Migrate SKU](./vpn-gateway-migrate-legacy-sku-include.md)]
