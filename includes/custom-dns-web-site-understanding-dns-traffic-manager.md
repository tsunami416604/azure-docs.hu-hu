A tartománynévrendszer (DNS) segítségével keresse meg a művelet az interneten. Például adjon meg egy címet a böngészőben, vagy egy hivatkozásra egy weblap, azt használja DNS lefordítani a tartományhoz tartozó IP-címet. Az IP-cím rendezésére a hasonlít egy utca, házszám, de nem nagyon emberi leíró. Például sokkal egyszerűbb, egy DNS-nevet, például a megjegyzése **contoso.com** meghaladja a megjegyezhető például 192.168.1.88 vagy 2001:0:4137:1f67:24a2:3888:9cce:fea3 IP-címet.

A DNS-rendszerében alapuló *rekordok*. Rekordok társítson egy adott *neve*, például a **contoso.com**, IP-címet vagy egy másik DNS-nevét. Amikor egy alkalmazás, például egy webes böngésző keres a DNS-nevét, a rekord keresése, és bármilyen mutat címként használja. Ha az érték mutat egy IP-címet, a böngésző ezt az értéket fogja használni. Mutat egy másik DNS-nevét, majd az alkalmazás rendelkezik feloldási újra elvégzéséhez. Végső soron minden névfeloldás véget ér az IP-címet.

Amikor egy Azure-webhelyet hoz létre, a DNS-név automatikusan a helyhez. Ez a név formájában történik  **&lt;yoursitename&gt;. azurewebsites.net**. Ha a webhely egy Azure Traffic Manager-végpontot, a webhely majd érhető el a  **&lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** tartomány.

> [!NOTE]
> Ha a webhely egy Traffic Manager-végpont van konfigurálva, használhatja a **. trafficmanager.net** címet a DNS-rekordok létrehozásához.
> 
> Csak használhatja a CNAME-rekordok a Traffic Managerrel
> 
> 

Van még több típusú bejegyzés – mindegyiket a saját funkciók és -korlátozások, de a Traffic Manager-végpontként konfigurált webhelyek esetén csak ügyelünk egy; *CNAME* rögzíti.

### <a name="cname-or-alias-record"></a>CNAME- vagy aliasnév rekord
A CNAME rekord rendeli hozzá a *adott* DNS-nevével, például a **mail.contoso.com** vagy **www.contoso.com**, egy másik (kanonikus) tartománynévvel. A kanonikus tartománynév megadása esetén a Traffic Manager használata az Azure Websites, a  **&lt;myapp >. trafficmanager.net** a Traffic Manager-profil tartománynevére. Létrehozása után a CNAME REKORDOT hoz létre egy aliast a  **&lt;myapp >. trafficmanager.net** tartomány nevét. A CNAME-bejegyzés feloldja az IP-címére a  **&lt;myapp >. trafficmanager.net** tartománynév automatikusan, így ha megváltozik az IP-cím, a webhely, nincs teendője.

Miután forgalom a Traffic Manager megérkeznek, majd irányítja a forgalmat webhelyéhez, használja a terheléselosztási mód van konfigurálva. Ez az teljesen átlátható a webhely látogatóinak. Csak akkor jelenik meg az egyéni tartománynév a böngészőbe.

> [!NOTE]
> Néhány tartomány regisztráló szervezetek engedélyezése csak csatlakoztatható altartományt, például egy olyan CNAME rekordot használatakor **www.contoso.com**, és nem neveket, többek között a **contoso.com**. További információ a CNAME-rekordot, a regisztráló által dokumentációjában talál <a href="http://en.wikipedia.org/wiki/CNAME_record">CNAME rekordot a Wikipedia bevitelének</a>, vagy a <a href="http://tools.ietf.org/html/rfc1035">IETF tartománynév - megvalósítása és meghatározása</a> dokumentum.
> 
> 

