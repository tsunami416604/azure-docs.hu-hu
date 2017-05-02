A helyszíni hálózaton a helyek közötti kapcsolatok létesítéséhez VPN-eszközre van szükség. Nem mutatjuk be a konfigurációs lépéseket minden VPN-eszköz esetében, azonban az alábbi hivatkozásokra kattintva hasznos információkat találhat erre vonatkozóan:

- A kompatibilis VPN-eszközökkel kapcsolatos információért tekintse meg a [VPN-eszközökkel foglalkozó szakaszt](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). 
- Az eszközkonfigurációs beállításokra mutató hivatkozásokért lásd: [Ellenőrzött VPN-eszközök](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable). Az eszközkonfigurációs hivatkozásokat képességeinkhez mérten biztosítjuk. Minden esetben célszerű az eszköz gyártójához fordulni a legfrissebb konfigurációs információért.
- Az eszközök konfigurációs mintáinak szerkesztésével kapcsolatos információkért tekintse meg a [minták szerkesztésével](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#editing) kapcsolatos részt.
- Az IPsec/IKE-paraméterekkel kapcsolatos információkért tekintse meg a [paraméterekkel](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) kapcsolatos részt.
- A VPN-eszköz konfigurálása előtt ellenőrizze, hogy a használni kívánt VPN-eszközre nem vonatkoznak-e [ismert eszközkompatibilitási problémák](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known).

A VPN-eszköz konfigurálásakor a következőkre van szüksége:

- Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor ad meg. A példákban alapvető megosztott kulcsot használunk. Javasoljuk egy ennél összetettebb kulcs létrehozását.

- A virtuális hálózati átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti.