Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre.

A „contoso.com” tartomány például számos DNS-rekordot tartalmazhat, például „mail.contoso.com” (levelezési kiszolgálóhoz) és „www.contoso.com” (webhelyhez).

DNS-zóna Azure DNS-ben való létrehozásakor:

* A zóna nevének egyedinek kell lennie az erőforráscsoporton belül, és nem egyezhet egy meglévő névvel. Ellenkező esetben a művelet sikertelen lesz.
* Az egyes zónanevek újra felhasználhatók egy másik erőforráscsoportban vagy egy másik Azure-előfizetésben.
* Abban az esetben, ha több zóna rendelkezik ugyanazzal a névvel, minden példány különböző névkiszolgálócímet kap. Csak egy címkészlet konfigurálható a tartományregisztrálóhoz.

> [!NOTE]
> Nem kell ahhoz tartománynévvel rendelkeznie, hogy azzal a tartománynévvel létrehozzon egy DNS-zónát az Azure DNS-ben. Azonban ahhoz, hogy az Azure DNS névkiszolgálókat a tartománynév helyes névkiszolgálójaként konfigurálhassa a tartományregisztrálóhoz, birtokolnia kell a tartományt.
> 
> További információ: [Delegate a domain to Azure DNS](../articles/dns/dns-domain-delegation.md) (Tartomány delegálása az Azure DNS-be).