Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartomány üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát. Az egyes tartományokhoz létrehozott DNS-rekordok a tartomány DNS-zónájában helyezkednek majd el.

A „contoso.com” tartomány például számos DNS-rekordot tartalmazhat, például „mail.contoso.com” (levelezési kiszolgálóhoz) és „www.contoso.com” (webhelyhez).

## <a name="a-namenamesaabout-dns-zone-names"></a><a name="names"></a>A DNS-zónák neve
* A zóna nevének egyedinek kell lennie az erőforráscsoporton belül, és nem egyezhet egy meglévő névvel. Ellenkező esetben a művelet sikertelen lesz.
* Az egyes zónanevek újra felhasználhatók egy másik erőforráscsoportban vagy egy másik Azure-előfizetésben.
* Abban az esetben, ha több zóna rendelkezik ugyanazzal a névvel, minden példány különböző névkiszolgáló-címet kap, és csak egyetlen példány delegálható a szülőtartományból. További információ: [Delegate a domain to Azure DNS](../articles/dns/dns-domain-delegation.md) (Tartomány delegálása az Azure DNS-be).


<!--HONumber=Nov16_HO2-->


