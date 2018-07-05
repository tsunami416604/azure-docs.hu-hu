A szolgáltatás és az előfizetés típusa határozza meg a másodpercenként futtatható lekérdezések számát (QPS). Érdemes gondoskodnia arról, hogy az alkalmazása tartalmazza a kvótán belül maradáshoz szükséges logikát. Ha túllépi a QPS értékét, a kérés 429-es HTTP-állapotkóddal meghiúsul. A válasz tartalmazza a Retry-After fejlécet is, amely másodpercekben megadja az újabb kérés elküldése előtti várakozási időt.  
  
### <a name="denial-of-service-dos-versus-throttling"></a>Szolgáltatásmegtagadás (DOS) és szabályozás

A szolgáltatás különbséget tesz egy szolgáltatásmegtagadásos támadás és egy QPS-eltérés között. Ha a szolgáltatás szolgáltatásmegtagadásos támadást feltételez, a kérés sikeres lesz (200 OK HTTP-állapotkóddal), azonban a választörzs ilyenkor üres.