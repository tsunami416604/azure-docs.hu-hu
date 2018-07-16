> [!NOTE]
> Az Azure DNS segítségével konfigurálhat egy egyéni DNS-nevet az Azure-webalkalmazások számára. További információt az [egyéni tartománybeállítások egy Azure-szolgáltatáshoz az Azure DNS használatával történő megadását](../articles/dns/dns-custom-domain.md#app-service-web-apps) ismertető cikkben talál.
>
>

Jelentkezzen be a tartományszolgáltatója webhelyére.

Keresse meg a DNS-rekordok kezelésére szolgáló oldalt. Minden tartományszolgáltató saját felülettel rendelkezik a DNS-rekordok kezelésére, ezért tekintse meg a szolgáltatói dokumentációt. A webhely **Tartománynév**, **DNS** vagy **Névkiszolgáló kezelése** címkével ellátott területeit keresse. 

A DNS-rekordok oldala gyakran a fiókadatok megtekintésekor jelenik meg, majd itt keresse meg például a **Saját tartományok** hivatkozást. Lépjen erre az oldalra, és keressen egy **Zónafájl**, **DNS-rekordok**, **Speciális konfiguráció** vagy hasonló nevű hivatkozást.

A következő képernyőkép egy DNS-rekordokat tartalmazó oldalra mutat példát:

![DNS-rekordokat tartalmazó oldal példája](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

A példaként szolgáló képernyőképen a **Hozzáadás** lehetőséggel hozhat létre rekordokat. Egyes szolgáltatók eltérő hivatkozásokat használnak a különböző rekordtípusok hozzáadásához. Erről is a szolgáltató dokumentációjában talál további információt.

> [!NOTE]
> Bizonyos szolgáltatók (például a GoDaddy) esetében a DNS-rekordok módosításai csak a külön **Módosítások mentése** hivatkozás kiválasztása után lépnek életbe. 
