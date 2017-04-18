A helyszíni hálózaton a helyek közötti kapcsolatok létesítéséhez VPN-eszközre van szükség. Bár nem biztosítunk konfigurációs lépéseket minden VPN-eszközhöz, az alábbi hivatkozások hasznos információval szolgálhatnak:

- További információ a kompatibilis VPN-eszközökről: [VPN-eszközök](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). 
- Az eszközkonfigurációs beállításokra mutató hivatkozásokért lásd: [Ellenőrzött VPN-eszközök](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable). Az eszközkonfigurációs hivatkozásokat képességeinkhez mérten biztosítjuk. Minden esetben célszerű az eszköz gyártójához fordulni a legfrissebb konfigurációs információért.
- Az eszközkonfigurációs minták szerkesztésével kapcsolatos információért lásd: [Minták szerkesztése](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#editing).
- IPsec/IKE-paraméterek: [Paraméterek](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec).
- A VPN-eszköz konfigurálása előtt ellenőrizze, hogy a használni kívánt VPN-eszközre nem vonatkoznak-e [ismert eszközkompatibilitási problémák](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known).

A VPN-eszköz konfigurálásakor a következő elemekre lesz szüksége:

- A virtuális hálózati átjáró nyilvános IP-címe.

    -  A nyilvános IP-cím Azure Portal használatával történő megkereséséhez válassza a **Virtual network gateways** (Virtuális hálózati átjárók) elemet, majd kattintson az átjárója nevére. 
    - A virtuális hálózati átjáró IP-címének PowerShellel való megkereséséhez használja a következő példát (az értékeket cserélje le a sajátjaira).

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor ad meg. A példákban igen alapvető megosztott kulcsot használunk. Ennél összetettebb kulcsot kell létrehoznia.




