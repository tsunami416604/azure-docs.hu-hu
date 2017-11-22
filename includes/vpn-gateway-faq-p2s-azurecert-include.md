[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Használhatom a saját PKI legfelső szintű hitelesítésszolgáltatómat a pont–hely kapcsolathoz?

Igen. Korábban csak önaláírt főtanúsítványt lehetett használni. Továbbra is 20 főtanúsítvány tölthető fel.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Milyen eszközök használhatók olyan tanúsítványokat hoznak létre?

A vállalati nyilvános kulcsú infrastruktúra megoldás (a belső használatú PKI), az Azure PowerShell, a MakeCert és a OpenSSL is használhatja.

### <a name="certsettings"></a>Vannak-e tanúsítvány beállításai és a paraméterek?

* **Belső PKI/vállalati nyilvános kulcsú infrastruktúra megoldás:** lépései [tanúsítványokat létrehozni](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Az Azure PowerShell:** tekintse meg a [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) cikk lépéseit.

* **A MakeCert:** tekintse meg a [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) cikk lépéseit.

* **OpenSSL:** 

    * Amikor a tanúsítványok exportálása, ügyeljen arra, hogy a legfelső szintű tanúsítvány átalakítása Base64.

    * Az ügyféltanúsítványt:

      * A titkos kulcs létrehozásakor adja meg a hosszának 4096.
      * Ha a tanúsítvány létrehozása a *-bővítmények* paraméter, adja meg *usr_cert*.