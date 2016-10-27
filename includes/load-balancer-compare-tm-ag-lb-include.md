## <a name="load-balancer-differences"></a>Terheléselosztási különbségek

A Microsoft Azure-ral többféleképpen lehet elosztani a hálózati forgalmat. Ezek a lehetőségek különböző működési elveken alapulnak, különböző funkciókészlettel rendelkeznek és különböző forgatókönyveket támogatnak. Elkülönítve vagy egymással kombinálva egyaránt használhatók.

- Az **Azure Load Balancer** a szállítási rétegben működik (4. réteg a hálózatireferencia-veremben). Hálózati szintű forgalomelosztást biztosít az adott alkalmazás ugyanabban az Azure-adatközpontban futó példányai között.

- Az **Application Gateway** az alkalmazási rétegben működik (7. réteg az OSI hálózatireferencia-veremben). Fordított proxyszolgáltatásként működik, az ügyfélkapcsolatok leállítását és a kérelmek háttérvégpontokhoz való továbbítását végzi.

- A **Traffic Manager** a DNS szintjén működik.  DNS-válaszokat használva irányítja a végfelhasználói forgalmat a globálisan elosztott végpontok felé. Ezután az ügyfelek közvetlenül a végpontokhoz csatlakoznak.

Az alábbi táblázat az egyes szolgáltatások funkcióit összegzi:

| Szolgáltatás | Azure Load Balancer | Application Gateway | Traffic Manager |
|---|---|---|---|
|Technológia| Forgalmi szint (4. szint) | Alkalmazásszint (7. szint) | DNS-szint |
| Támogatott alkalmazásprotokollok | Bármelyik | HTTP és HTTPS |  Bármelyik (a végpontmonitoringhoz szükség van egy HTTP-végpontra) |
| Végpontok | Azure-os virtuális gépek és Cloud Services-szerepkörpéldányok | Bármely belső Azure IP-cím vagy nyilvános IP-cím | Azure-os virtuális gépek, Cloud Services-példányok, Azure Web Apps és külső végpontok |
| Vnet-támogatás | Internetes és belső (virtuális hálózati) alkalmazásokhoz egyaránt felhasználható | Internetes és belső (virtuális hálózati) alkalmazásokhoz egyaránt felhasználható |    Csak az internetes alkalmazásokat támogatja |
Végpontmonitoring | Mintavételen keresztül támogatott | Mintavételen keresztül támogatott | HTTP/HTTPS GET-en keresztül támogatott | 

Az Azure Load Balancer és az Application Gateway a végpontok felé irányítja a hálózati forgalmat, de különböző használati forgatókönyvvel rendelkeznek a kezelt forgalom szempontjából. Az alábbi táblázat segít a két terheléselosztó közti különbség megértésében:

| Típus | Azure Load Balancer | Application Gateway |
|---|---|---|
| Protokollok | UDP/TCP | HTTP/HTTPS |
| IP-címfenntartás | Támogatott | Nem támogatott | 
| Terheléselosztási mód | 5 rekordos (forrás IP-címe, forrásport, cél IP-címe, célport, protokolltípus) | Ciklikus időszeletelés<br>Útválasztás URL-cím alapján | 
| Terheléselosztási mód (forrás IP-cím/fix kiszolgálású munkamenetek) |  2 rekordos (forrás IP-cím és cél IP-cím), 3 rekordos (forrás IP-cím, cél IP-cím és port). Méretezés a virtuális gépek számának megfelelően | Cookie-alapú affinitás<br>Útválasztás URL-cím alapján |
| Állapotminták | Alapértelmezett: 15 másodperces mintavételi időköz. A rotációból kivéve: 2 folyamatos hiba után. Támogatja a felhasználó által definiált mintavételeket | Üresjárati mintavételi időköz – 30 másodperc. 5 egymásutáni élő forgalmi hiba vagy egyetlen üresjárati módú mintavételi hiba után kivéve. Támogatja a felhasználó által definiált mintavételeket | 
| SSL-kiürítés | Nem támogatott | Támogatott | 
  

<!--HONumber=Oct16_HO3-->


