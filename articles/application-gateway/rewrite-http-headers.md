---
title: Az Azure Application Gateway HTTP-fejlécek újraírási |} A Microsoft Docs
description: Ez a cikk az Azure Application Gateway HTTP-fejlécek újraírását áttekintést nyújt.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/29/2019
ms.author: absha
ms.openlocfilehash: 9160d300270bf1ab5043bee632d27bcc4b7bf332
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476032"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Az Application Gateway HTTP-fejlécek újraírása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP-fejlécek engedélyezése egy ügyfél és kiszolgáló át egy kérelem vagy válasz további információkat. Ezek a fejlécek újraírásával végezheti a fontos feladatok, például a biztonsággal kapcsolatos üzenetfejlécének mezői például HSTS / X-XSS-védelem hozzáadása, eltávolítása, amelyek bizalmas információkat fedhet válasz üzenetfejlécének mezői és portok adatait a eltávolítása X-továbbított – a fejlécek.

Application Gateway lehetővé teszi hozzá, távolíthatja el vagy frissítse a HTTP-kérelmek és válaszfejlécek történt a kérelem és válasz-csomagok áthelyezése az ügyfél és a háttér-készletek között. És ez lehetővé teszi, hogy adja meg annak érdekében, hogy csak bizonyos feltételek teljesülése esetén, a rendszer a megadott fejlécek újraírja feltételeket.

Az Application Gateway támogatja több [kiszolgálói változók](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) , amellyel további információt a kérelmek és válaszok tárolja. Ez megkönnyíti az hozhat létre hatékony újraírási szabályok.

> [!NOTE]
>
> A HTTP-fejléc újraírási támogatás érhető el csak a [Standard_V2 és WAF_v2 Termékváltozat](application-gateway-autoscaling-zone-redundant.md).

![A fejlécek újraírását](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Támogatott fejlécek

Módosíthatja a kérelmek és válaszok, a gazdagép, a kapcsolat és frissítés fejlécek kivételével az összes fejléc. Az application gateway használatával hozzon létre egyéni fejlécek, és hozzáadhatja őket a kérelmeket és válaszokat, keresztül.

## <a name="rewrite-conditions"></a>Feltételek újraírása

Újraírási feltételek kiértékelése HTTP (S)-kérelmek és válaszok tartalmát, és végezze el a fejléce módosítsa úgy, csak ha az egyik használható, vagy további feltételek teljesülnek. Az application gateway kiértékeljük a tartalmak HTTP (S)-kérelmek és válaszok az ilyen típusú változót használja:

- A kérelem HTTP-fejléceket.
- A válasz HTTP-fejléceket.
- Application Gateway kiszolgálói változókat.

Egy feltétel segítségével kiértékelheti, hogy egy adott változóban megtalálható, hogy egy adott változóban megfelel egy adott érték, vagy ha egy adott változóban egy adott mintának megfelelő-e. Használja a [Perl kompatibilis reguláris kifejezések (PCRE) könyvtár](https://www.pcre.org/) Reguláriskifejezés-mintának megfelelő feltételeiben beállításához. Reguláris kifejezési szintaxist kapcsolatos további információkért tekintse meg a [Perl főoldala a reguláris kifejezések](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Műveletek újraírása

Újraírási műveletek használatával adja meg a kérelmek és válaszfejlécek, újraírási kívánt és a fejlécek új értékét. Akkor hozzon létre egy új fejlécbe, az módosíthatja egy meglévő fejlécre értékét, vagy egy meglévő fejlécre törlése. Az ilyen típusú értékeket egy új vagy egy meglévő fejléc értéke beállítható:

- Szöveg.
- Kérelem fejléce. Adja meg a fejléc, az szintaxissal kell {http_req_*headerName*}.
- Válaszfejléc. Adjon meg egy válasz fejléce, használja a szintaxist kell {http_resp_*headerName*}.
- Kiszolgálói változó. Adjon meg egy, a szintaxissal kell {var_*serverVariable*}.
- A szöveg, fejléc, a válasz fejléce és a egy kiszolgálói változó kombinációja.

## <a name="server-variables"></a>Kiszolgálói változók

Az Application Gateway kiszolgálói változókat használ a kapcsolat a kiszolgáló hasznos információkat, a kapcsolat, az ügyfél és az aktuális kérelem tárolásához. Tárolt adatok közé tartoznak az ügyfél IP-cím és a webes böngésző típusa. Kiszolgálói változók dinamikusan, például egy új oldal betöltésekor vagy módosítása egy űrlap közzétételekor. Ezek a változók használatával újraírási feltételek kiértékelése és újraírási fejlécek.

Az Application gateway támogatja a kiszolgáló változókat:

| Változó neve | Leírás                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Az X-továbbított – az ügyfél kérést fejlécmezőt a `client_ip` változót (lásd az ebben a táblában később magyarázata) hozzáfűzi azt a a formátum IP1, IP2, IP3 és így tovább. Ha az X-továbbított – a mező nem szerepel az ügyfél-kérelem fejléce a `add_x_forwarded_for_proxy` változó megegyezik a `$client_ip` változó. Ez a változó különösen hasznos, ha meg szeretné írja újra az X-továbbított – a fejléc, állítsa az Application Gateway által, úgy, hogy a fejlécet tartalmazza, csak az IP-címet a portadatokat nélkül. |
| ciphers_supported          | Az ügyfél által támogatott titkosítások listája.          |
| ciphers_used               | A Rejtjelek létrehozott SSL-kapcsolathoz használt karakterlánc. |
| client_ip                  | Az IP-cím, amelyről az application gateway az a kérelem érkezett. ügyfél. Ha az application gateway és az eredeti ügyfelet, mielőtt a fordított proxy *client_ip* a fordított proxy IP-címét adja vissza. |
| client_port                | Az ügyfél-portot.                                                  |
| client_tcp_rtt             | Információ arról, hogy az ügyfél a TCP-kapcsolat. A rendszer támogatja a TCP_INFO szoftvercsatorna-lehetőség érhető el. |
| client_user                | HTTP-hitelesítés használata esetén a felhasználó nevét megadva, a hitelesítéshez. |
| host                       | Ebben a sorrendben elsőbbségi: az állomás nevét a kérelem-sor, a kérelem fejléc Host mezője állomás nevét vagy a kiszolgáló neve egyezik a kérelem. |
| cookie_*name*              | A *neve* cookie-t.                                            |
| http_method                | a módszer, hogy az URL-cím kérésére. Ha például beolvasása, vagy OSSZA meg. |
| http_status                | A munkamenet-állapot. Ha például 200-as, 400-as vagy 403-as.                       |
| http_version               | A kérelem protokollt. Általában a HTTP 1.0, HTTP/1.1-es vagy a HTTP és 2.0-s. |
| QUERY_STRING               | A következő változó/érték párok listáját a "?" a kért URL-címben. |
| received_bytes             | A kérelem (beleértve a kérelem-sor, fejléc és kéréstörzs) hossza. |
| request_query              | A kérelem-sor argumentumai.                                |
| request_scheme             | A kérelem séma: http vagy HTTPS protokollt.                            |
| request_uri                | A teljes eredeti kérés URI azonosítója (az argumentumok).                   |
| sent_bytes                 | Az ügyfélnek küldött bájtok száma.                             |
| server_port                | A port, a kiszolgáló, amely egy kérés elfogadva.                 |
| ssl_connection_protocol    | Egy létrehozott SSL-kapcsolat által használt protokoll.        |
| ssl_enabled                | "A" Ha a kapcsolat SSL módban működik. Ellenkező esetben üres karakterláncot. |

## <a name="rewrite-configuration"></a>Konfigurációs újraírása

HTTP-fejléc újraírási konfigurálásához hajtsa végre az alábbi lépéseket kell.

1. A HTTP-fejléc újraírási szükséges objektumok létrehozása:

   - **Művelet újraírási**: A kérés és a kérés üzenetfejlécének mezői újraírási kívánt és az új értéket a fejlécek meghatározására szolgál. Társíthatja egy vagy több újraírási feltételek újraírási művelettel.

   - **A feltétel újraírási**: Választható konfiguráció. Újraírási feltételek kiértékelése a HTTP (S)-kérelmek és válaszok tartalmát. Az újraírási művelet fordul elő, ha a HTTP (S) kérelem vagy válasz megegyezik a újraírási feltétel.

     Ha egynél több feltétel társítása egy műveletet, a művelet csak akkor, ha a feltételek mindegyike teljesül következik be. Más szóval a művelet egy logikai és műveletet.

   - **Újraírási szabályt**: Több újraírási műveletet tartalmaz / újraírási feltétel kombinációi.

   - **Feladatütemezési szabály**: Segít a újraírási szabályok hajtsa végre a sorrend határozza meg. Ez a konfiguráció akkor hasznos, ha több újraírási szabályok újraírási készlet rendelkezik. Egy szabály feladatütemezési alacsonyabb értékkel rendelkező átírási szabály első fut. Ha két újraírási szabályok szabály ugyanabban a sorrendben rendeli, a végrehajtás sorrendje nem determinisztikus.

   - **Set újraírási**: Több újraírási szabályok, amelyek lesz társítva a egy kérelem-útválasztási szabályt tartalmaz.

2. Az újraírási set csatolása (*rewriteRuleSet*) útválasztási szabályban. Az újraírási konfigurációs van csatolva, a forrás figyelőt az útválasztási szabályt. Ha egy alapszintű útválasztási szabályt használ, a fejléc újraírási konfigurációs társítva a forrás-figyelő és egy globális fejléce módosítsa úgy. -Alapú útválasztási szabály használatakor a fejléc újraírási konfiguráció az URL-Címtérkép van definiálva. Ebben az esetben csak vonatkozik egy helyet a megadott elérési út területéhez.

Hozzon létre több HTTP-fejléc újraírási csoportokat, és minden egyes újraírási több kérésfigyelőt beállítása a alkalmazni. Azonban csak az egyik értéke egy adott hallgató újraírási alkalmazhat.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Az alábbiakban néhány gyakori helyzet fejléce módosítsa úgy a.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Port-adatok eltávolítása az X-továbbított – a fejléc

Az Application Gateway egy X-továbbított – a fejléc összes kérelem szúr be továbbítja a kéréseket a háttér előtt. Ez a fejléc egy IP-portok vesszővel tagolt listája. Előfordulhat, hogy a forgatókönyvek, amelyben a háttér-kiszolgálókat csak kell az IP-címeket tartalmazó fejlécek. Használhatja a fejléce módosítsa úgy a portadatokat eltávolítása az X-továbbított – a fejléc. Ennek egyik módja, hogy a fejléc beállítása a add_x_forwarded_for_proxy kiszolgálói változó:

![Távolítsa el a porthoz](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Egy átirányítási URL-cím módosítása

A háttéralkalmazás átirányítás választ küld, amikor érdemes átirányítja az ügyfelet egy másik fut, a háttér-alkalmazás által megadott URL-címet. Például érdemes alkalmazni, ha egy app service az application gateway mögött található, és megköveteli, hogy a relatív elérési út átirányítás tegye az ügyfél. (Például egy irányítja át a contoso.azurewebsites.net/path1 contoso.azurewebsites.net/path2.)

Mivel az App Service egy több-bérlős szolgáltatás, az állomás fejlécével a kérésben segítségével irányítsa át a kérést a megfelelő végpontra. App services rendelkezik egy alapértelmezett tartományneve *. azurewebsites.NET webhelyet (például: contoso.azurewebsites.net), amely különbözik az Alkalmazásátjáró tartomány nevét (például: contoso.com). Mivel az ügyfél eredeti kérése az application gateway tartománynevet (contoso.com), az állomásnév, az application gateway az állomásnév contoso.azurewebsites.net változik. Ez a változás, hogy az app Service-ben is irányítsa át a kérést a megfelelő végpontra való áttelepíteni.

Az app Service-ben átirányítás választ küld, ha azt használja ugyanazt az állomásnevet a válasz location fejléce egy, a kérelmet az alkalmazásátjáróról érkező kap. Így az ügyfél fogja elérhetővé tenni a kérést contoso.azurewebsites.net/path2 az application gatewayhez (contoso.com/path2) lépéseket megkerülve közvetlenül. Az application gateway megkerülése nem kívánatos.

A probléma megoldható az eszköznév beállítása az application gateway tartománynévvel location fejlécébe.

Cserélje ki a hostname lépései a következők:

1. Hozzon létre egy feltételt, amely kiértékeli, ha a válasz location fejlécébe azurewebsites.net tartalmaz egy újraírási szabályt. Adja meg a minta `(https?):\/\/.*azurewebsites\.net(.*)$`.
1. Egy műveletet a location fejlécet újraírási úgy is, hogy az application gateway állomásnév. Ehhez adjon `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` a fejléc értékeként.

![Location fejlécet módosítása](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Biztonsági rések elkerülése érdekében a HTTP biztonsági fejlécek megvalósítása

Számos biztonsági rések szükséges fejlécek az alkalmazás válasza az életbe léptetésével oldható meg. Ezeket a biztonsági fejléceket X-XSS-védelem, a Strict-átviteli-biztonság és a tartalom-Security-Policy tartalmazza. Az Application Gateway segítségével állítsa be ezeket a fejléceket minden jelöli.

![Biztonsági fejléc](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Törölje a nem kívánt fejlécek

Előfordulhat, hogy szeretné, hogy a HTTP-választ a bizalmas információk felfedése fejlécek eltávolítása. Például előfordulhat, hogy eltávolítani kívánt információkat, például a háttér-kiszolgáló nevét, operációs rendszer vagy a szalagtár adatait. Az application gateway segítségével távolítsa el ezeket a fejléceket:

![Fejléc törlése](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Fejléc meglétének ellenőrzése

Egy HTTP- kérés fejlécében vagy kiszolgálói változó meglétének fejléc ki. Ezt a próbaidőszakot akkor hasznos, ha szeretne végezni egy fejléc újraírási csak akkor, ha egy bizonyos fejlécben megtalálható.

![Fejléc meglétének ellenőrzése](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Korlátozások

- Ha választ ugyanazzal a névvel több fejlécek, újraírásával ezeket a fejléceket egyik értékét eredményez a többi fejlécek elvetését a válaszban. Általában ez fordulhat Set-cookie-k fejléccel, mert egynél több Set-Cookie-fejléc rendelkezhet egy adott válaszként. Egy ilyen forgatókönyv esetén egy app service az application gateway szolgáltatással használja, és konfigurálta a cookie-alapú munkamenet-affinitás az application gateway-en. Ebben az esetben a válasz 2 Set-cookie-k fejléceket tartalmazza: egyet, azaz az app service által használt `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` és a egy másikat az application gateway-affinitást, azaz a `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`. A Set-cookie-k fejlécek ebben a forgatókönyvben egyik újraírását eredményezhet a eltávolítása a Set-cookie-k fejléc a válaszból.

- A kapcsolat, a frissítés és a gazdagép fejlécek újraírását jelenleg nem támogatott.

- A fejlécnevek tartalmazhat bármilyen alfanumerikus karaktereket és az adott szimbólumok meghatározott [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Jelenleg nem támogatottak az aláhúzás (\_) fejléc neve speciális karaktert.

## <a name="next-steps"></a>További lépések

HTTP-fejlécek újraírási kezelésével kapcsolatos információkért lásd:

- [Az Azure portal használatával HTTP-fejlécek újraírása](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Azure PowerShell-lel újraírási HTTP-fejlécek](add-http-header-rewrite-rule-powershell.md)
