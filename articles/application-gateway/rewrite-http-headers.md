---
title: Az Azure Application Gateway HTTP-fejlécek újraírási |} A Microsoft Docs
description: Ez a cikk áttekintést teszi, hogy az Azure Application Gateway HTTP-fejlécek újraírása
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/11/2019
ms.author: absha
ms.openlocfilehash: 20c484779e7ffe74ae01e33472b4cf8761d81b66
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682680"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Az Application Gateway HTTP-fejlécek újraírása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP-fejlécek engedélyezése az ügyfél és a kiszolgáló át a kérelem vagy válasz további információkat. A HTTP-fejlécek újraírását segítségével számos fontos forgatókönyv például a biztonsági fejléc mezőket is, például HSTS elvégzéséhez / X-XSS-védelem válaszfejléc eltávolítása mezők, amelyek felfedhet bizalmas adatokat, távolítsa el adatait X-továbbított – a fejlécek, és így tovább. Az Application gateway támogatja a funkció hozzá, távolíthatja el vagy frissítse a HTTP-kérelmek és válaszfejlécek történt a kérés és válasz-csomagok áthelyezése az ügyfél és a háttérkiszolgáló készletek között. Ez lehetővé teszi a, adja meg annak érdekében, hogy csak bizonyos feltételek teljesülése esetén, a rendszer a megadott fejlécek újraírja feltételeket. A funkció is támogatja több [kiszolgálói változók](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) ami segít a kérelmek és válaszok, ezáltal lehetővé téve, hogy a nagy teljesítményű újraírási szabályok kapcsolatos további információk tárolására.
> [!NOTE]
>
> A HTTP-fejléc újraírási támogatás érhető el csak a [új Termékváltozat [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

![A fejlécek újraírását](media/rewrite-http-headers/rewrite-headers.png)

## <a name="headers-supported-for-rewrite"></a>A fejlécek esetében támogatott újraírása

A funkció lehetővé teszi a kérések és a gazdagép, a kapcsolat és a frissítés fejlécek ablakrácsok választ minden fejléc újraírási. Az application gateway használatával hozzon létre egyéni fejlécek, és hozzáadhatja őket a kérés és a válaszok történő továbbítása. 

## <a name="rewrite-conditions"></a>Feltételek újraírása

Egy vagy több feltételek teljesülése esetén csak a feltételek kiértékelése a HTTP (S)-kérelmek és válaszok tartalmát, és hajtsa végre a fejléc újraírási átírása használatával. A következő 3 típusú változót az application gateway által használt kiértékeljük a tartalmak a HTTP (S)-kérelmek és válaszok:

- A kérelem HTTP-fejlécek
- A válasz HTTP-fejlécek
- Application gateway kiszolgálói változók

Egy feltétel segítségével kiértékelheti, hogy a megadott változó megtalálható, hogy a megadott változó pontosan egy adott érték, vagy ha a megadott változó pontosan egy adott minta-e. [Perl kompatibilis reguláris kifejezések (PCRE) könyvtár](https://www.pcre.org/) Reguláriskifejezés-mintának megfelelő feltételeiben végrehajtására szolgál. A reguláris kifejezési szintaxist kapcsolatos további információkért tekintse meg a [Perl reguláris kifejezések ember lap](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Műveletek újraírása

Újraírási műveletek segítségével adja meg a kérelmek és válaszfejlécek, újraírási kívánt és az eredeti fejlécek kell írni az új érték. Hozzon létre egy új fejlécbe, egy meglévő fejlécre értékének módosítása vagy törlése egy meglévő fejlécre. A következő típusú értékeket egy új vagy egy meglévő fejléc értéke lehet beállítani:

- Szöveg 
- Kérelem fejléce: Adja meg a fejléc, az szintaxissal kell {http_req_*headerName*}
- Válasz fejléce: Adjon meg egy válasz fejléce, használja a szintaxist kell {http_resp_*headerName*}
- Kiszolgálói változó: Adjon meg egy, a szintaxissal kell {var_*serverVariable*}
- Szöveg, a kérelem fejléce, a válasz fejléce és a egy kiszolgálói változó kombinációja.

## <a name="server-variables"></a>Kiszolgálói változók

Az Application gateway kiszolgálói változókat használ a kapcsolaton, például az ügyfél IP-cím vagy webes böngésző típusa kiszolgáló, a kapcsolat az ügyfél és a jelenlegi kérelem hasznos adatainak tárolásához. Dinamikusan, módosítsa ezeket a változókat, például fel az új oldal betöltésekor vagy a képernyő. Ezek a kiszolgáló-változók használatával újraírási feltételek kiértékelése és a fejlécek újraírási. 

Az Application gateway támogatja a következő kiszolgálói változó:

| Támogatott kiszolgálói változók | Leírás                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | "X-továbbított – az" ügyfél kérelem fejléce mező tartalmazza a `client_ip` (Ez az alábbi táblázat részletesen) változó (IP1, IP2, IP3,...) formátumban rá hozzáfűzve. Ha az "X-továbbított – a" mezőben nem szerepel az ügyfél-kérelem fejléce a `add_x_forwarded_for_proxy` változó megegyezik a `$client_ip` változó. Ez a változó különösen hasznos forgatókönyvekben, ahol ügyfelek írja újra az Application Gateway által beállított X – továbbított – a fejléc, úgy, hogy a fejlécet tartalmazza, csak az IP-címet a portadatokat nélkül kíván. |
| ciphers_supported          | az ügyfél által támogatott rejtjel listáját adja vissza          |
| ciphers_used               | a létrehozott SSL-kapcsolathoz használt Rejtjelek-karakterláncot ad vissza |
| client_ip                  | IP-cím, amelyről az application gateway az a kérelem érkezett. ügyfél. Ha van egy fordított proxy előtt az application gateway és az eredeti ügyfelet, majd *client_ip* a fordított proxy IP-címét adja vissza. |
| client_port                | ügyfél-port                                                  |
| client_tcp_rtt             | információ az ügyfél; TCP-kapcsolat a rendszer támogatja a TCP_INFO szoftvercsatorna-lehetőség érhető el |
| client_user                | HTTP-hitelesítés használata esetén a felhasználónév a hitelesítéshez megadott |
| gazdagép                       | Ebben a sorrendben elsőbbségi: állomásnév, a kérelem-sor vagy az állomás nevét a "Host" kérelem fejléce mező, vagy a kiszolgáló neve egyezik a kérelem |
| cookie_*name*              | a *neve* cookie-k                                            |
| http_method                | a módszer, hogy az URL-cím kérésére. Például GET, POST stb. |
| http_status                | munkamenet-állapot, például: 200-as, 400, 403-as stb.                       |
| http_version               | kérelem protokoll, általában "HTTP 1.0", "HTTP/1.1" vagy "HTTP és 2.0-s" |
| QUERY_STRING               | a lista a változó értékének párok olvashat a "?" a kért URL-címben. |
| received_bytes             | kérés hossza (beleértve a kérelem-sor, a fejlécet és a kérelem törzsében) |
| request_query              | a kérelem-sor argumentumai                                |
| request_scheme             | kérelem séma, "http" vagy "https"                            |
| request_uri                | teljes eredeti kérelem URI-t (argumentumok)                   |
| sent_bytes                 | egy az ügyfélnek küldött bájtok száma                             |
| server_port                | annak a kiszolgálónak, amely egy kérés elfogadva                 |
| ssl_connection_protocol    | adja vissza egy létrehozott SSL-kapcsolat által használt protokoll        |
| ssl_enabled                | "a" Ha a kapcsolat működik SSL módban, vagy egyéb üres karakterlánc |

## <a name="rewrite-configuration"></a>Konfigurációs újraírása

HTTP-fejléc újraírási konfigurálásához meg kell:

1. A szükséges a http-fejlécek újraírási új objektumokat hozhat létre:

   - **Művelet újraírási**: a kérés és a kérés üzenetfejlécének mezői újraírási kívánt és az eredeti fejlécek kell írni az új érték adható meg. Ha szeretné társítani egy vagy több újraírási feltétel egy újraírási művelet.

   - **A feltétel újraírási**: Egy opcionális konfigurációs. újraírási feltétel kerül, ha a program a HTTP (S)-kérelmek és válaszok tartalmát értékeli. A újraírási feltétel társított újraírási művelet végrehajtásához a döntés-e a HTTP (S) kérelem vagy válasz párosítva a újraírási feltétel alapul. 

     Ha egynél több feltételek társított egy műveletet, majd a művelet lesz végrehajtva csak akkor, ha a feltételek mindegyike teljesül, azaz, egy logikai és művelet történik.

   - **Újraírási szabályt**: újraírási szabályt tartalmaz több újraírási művelet – újraírási feltétel kombinációi.

   - **Feladatütemezési szabály**: segít megállapítani, hogy a sorrendet, amelyben a különböző újraírási szabályok végrehajtásra kerülhetnek. Ez akkor hasznos, ha több újraírási szabályok újraírási készlet. A szabály feladatütemezési alacsonyabb értékkel rendelkező átírási szabály lekérdezi először hajtott végre. Ha megadja a ugyanaz a szabály során, az két újraírási szabályok a végrehajtás sorrendje nem determinisztikus lesz.

   - **Set újraírási**: több újraírási szabályok, amely hozzá lesz rendelve egy kérelem-útválasztási szabályt tartalmaz.

2. Akkor kell csatolni a újraírási set (*rewriteRuleSet*) az útválasztási szabályt. Ennek az oka az átfogalmazás konfigurációs van csatolva a forrás figyelőt az útválasztási szabályt. Egy alapszintű útválasztási szabályt használatakor a fejléc újraírási konfigurációs társítva a forrás-figyelő és egy globális fejléce módosítsa úgy. -Alapú útválasztási szabály használata esetén a fejléc újraírási konfiguráció az URL-Címtérkép van definiálva. Ezért csak vonatkozik egy helyet a megadott elérési út területéhez.

Több http-fejléc újraírási csoportok hozhat létre, és minden egyes újraírási set több kérésfigyelőt is alkalmazható. Azonban csak az egyik értéke egy adott hallgató újraírási is alkalmazhat.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Néhány fejléc újraírási igénylő gyakori helyzetek szerepelnek, alatt.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Port-adatok eltávolítása az X-továbbított – a fejléc

Az Application gateway szúrja be az összes kérelem X-továbbított – a fejléc, továbbítja a kéréseket a háttér előtt. Ez a fejléc formátuma IP:port vesszővel tagolt listája. Azonban lehetnek olyan forgatókönyvek, ahol a háttérkiszolgálók igényelnek-e a fejléc csak az IP-címeket tartalmazza. Az ilyen forgatókönyvek megvalósítására, fejléce módosítsa úgy a portadatokat eltávolítása az X-továbbított – a fejléc használható. Ennek egyik módja, hogy a fejléc add_x_forwarded_for_proxy kiszolgálói változó beállítása. 

![Távolítsa el a porthoz](media/rewrite-http-headers/remove-port.png)

### <a name="modify-the-redirection-url"></a>Az átirányítási URL-cím módosítása

A háttéralkalmazás átirányítás választ küld, amikor érdemes átirányítja az ügyfelet egy másik, mint a háttéralkalmazás által megadott URL-címet. Egy ilyen forgatókönyv esetén egy app service az application gateway mögött lévő üzemeltetett, és az ügyfél egy átirányítási ehhez a relatív elérési út (irányítja át a contoso.azurewebsites.net/path1 contoso.azurewebsites.net/path2) igényel. 

Mivel az app service egy több-bérlős szolgáltatás, az állomásfejléc található használja a kérést a megfelelő végpontra irányíthatja. App services rendelkezik egy alapértelmezett tartományneve *. azurewebsites.net (például: contoso.azurewebsites.net), amely különbözik az Alkalmazásátjáró tartomány nevét (például: contoso.com). Mivel az eredeti kérést az ügyfél rendelkezik az Alkalmazásátjáró a contoso.com tartománynevet, a gazdagép nevét, az application gateway változik a hostname contoso.azurewebsites.net, úgy, hogy az app service irányíthatja a megfelelő végpontra való. Az app Service-ben átirányítás választ küld, ha azt használja ugyanazt az állomásnevet a válasz location fejléce egy, a kérelmet az alkalmazásátjáróról érkező kap. Ezért az ügyfél teszi a kérést contoso.azurewebsites.net/path2, az application gatewayhez (contoso.com/path2) lépéseket megkerülve közvetlenül. Az application gateway megkerülése nem kívánatos. 

A probléma megoldhatók az eszköznév beállítása az application gateway tartománynévvel location fejlécébe. Ehhez létrehozhat egy átírási szabály olyan feltétellel, ha a válasz location fejlécébe azurewebsites.net tartalmaz megadásával visszaadó `(https?):\/\/.*azurewebsites\.net(.*)$` a mintát, és végrehajt egy műveletet a location fejlécet, hogy az application gateway újraírási állomásnév megadásával `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` a fejléc értékeként.

![Location fejlécet módosítása](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Biztonsági rések elkerülése érdekében a HTTP biztonsági fejlécek megvalósítása

Számos biztonsági rések lehet meghatározni a kérelem-válasz szükséges fejlécek alkalmazásával. Biztonsági fejléc ezek némelyike X-XSS-védelem, a Strict-átviteli-biztonság, a tartalom-Security-házirend, stb. Az application gateway segítségével állítsa be ezeket a fejléceket minden jelöli.

![Biztonsági fejléc](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Törölje a nem kívánt fejlécek

Előfordulhat, hogy el kívánja távolítani ezeket a fejléceket, a HTTP-válasz, amelyek feltárják a bizalmas adatokat, például a háttér-kiszolgáló nevét, operációs rendszer, a szalagtár adatait és stb. Az application gateway segítségével távolítsa el ezeket.

![Fejléc törlése](media/rewrite-http-headers/remove-headers.png)

### <a name="check-presence-of-a-header"></a>Fejléc meglétének ellenőrzése

A HTTP- kérés fejlécében vagy kiszolgálói változó meglétének fejléc ki. Ez akkor hasznos, ha szeretne egy fejléc újraírási csak akkor végre egy bizonyos fejlécben megtalálható.

![Fejléc meglétének ellenőrzése](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Korlátozások

- A kapcsolat, a frissítés és a gazdagép fejlécek újraírását még nem támogatott.

- A fejlécnevek tartalmazhat bármilyen alfanumerikus karakterrel és adott szimbólumok meghatározott [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Azonban jelenleg nem támogatjuk a "aláhúzás" (\_) a fejléc neve speciális karaktert. 

## <a name="need-help"></a>Segítség

Írjon nekünk az [ AGHeaderRewriteHelp@microsoft.com ](mailto:AGHeaderRewriteHelp@microsoft.com) abban az esetben ez a képesség segítségre van szüksége.

## <a name="next-steps"></a>További lépések

HTTP-fejlécek újraírási kezelésével kapcsolatos információkért lásd:

-  [Az Azure portal használatával HTTP-fejlécek újraírása](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
-  [Azure PowerShell-lel újraírási HTTP-fejlécek](add-http-header-rewrite-rule-powershell.md)
