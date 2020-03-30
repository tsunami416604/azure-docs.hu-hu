---
title: HTTP-fejlécek újraírása az Azure Application Gateway alkalmazással | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt a HTTP-fejlécek átírásáról az Azure Application Gateway alkalmazásban
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: d0b28770940f0e1adeec16aa89cd087299bd4abc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132997"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>HTTP-fejlécek újraírása alkalmazásátjáróval

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A HTTP-fejlécek lehetővé teszik, hogy az ügyfél és a kiszolgáló további információkat adjon át kéréssel vagy válaszokkal. A fejlécek újraírásával fontos feladatokat végezhet el, például a biztonsággal kapcsolatos fejlécmezőket, például a HSTS/ X-XSS-Protection-t, a válaszfejlécmezőket, amelyek bizalmas információkat fedhetnek fel, valamint a portadatok eltávolítását az X-Forwarded-For fejlécekből.

Az Application Gateway lehetővé teszi HTTP-kérelmek és -válaszok fejlécének hozzáadását, eltávolítását vagy frissítését, miközben a kérelem- és válaszcsomagok az ügyfél és a háttérkészlet között mozognak. Ezenkívül lehetővé teszi feltételek megadását, hogy a megadott fejlécek átírására csak ezen feltételek teljesülése esetén legyen lehetőség.

Az Application Gateway számos [kiszolgálóváltozót](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) is támogat, amelyek segítenek a kérésekkel és a válaszokkal kapcsolatos további információk tárolásában. Ez megkönnyíti a hatékony újraírási szabályok létrehozását.

> [!NOTE]
>
> A HTTP-fejléc újraírási támogatása csak a [Standard_V2 és WAF_v2 termékváltozathoz](application-gateway-autoscaling-zone-redundant.md)érhető el.

![Fejlécek újraírása](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Támogatott fejlécek

A kérelmekben és válaszokban lévő összes fejlécet átírhatja, kivéve a Gazdagép, a Kapcsolat és a Frissítés fejléceket. Az alkalmazásátjáró segítségével egyéni fejléceket is létrehozhat, és hozzáadhatja őket a rajta keresztül irányított kérésekhez és válaszokhoz.

## <a name="rewrite-conditions"></a>Újraírási feltételek

Az újraírási feltételek segítségével kiértékelheti a HTTP(S) kérések és -válaszok tartalmát, és csak akkor hajthat végre fejléc-újraírást, ha egy vagy több feltétel teljesül. Az alkalmazásátjáró az alábbi típusú változók at használja a HTTP(S) kérések és válaszok tartalmának kiértékelésére:

- HTTP-fejlécek a kérelemben.
- HTTP-fejlécek a válaszban.
- Alkalmazásátjáró-kiszolgáló változók.

Egy feltétellel kiértékelheti, hogy egy adott változó jelen van-e, hogy egy adott változó megfelel-e egy adott értéknek, vagy hogy egy adott változó megfelel-e egy adott mintának. A [Perl-kompatibilis reguláris kifejezések (PCRE) könyvtár](https://www.pcre.org/) segítségével beállíthatja a reguláris kifejezésminta egyezést a feltételekben. A reguláris kifejezések szintaxisáról a [Perl reguláris kifejezések főoldalán](https://perldoc.perl.org/perlre.html)olvashat.

## <a name="rewrite-actions"></a>Műveletek újraírása

Az újraírási műveletek segítségével megadhatja az újraírni kívánt kérés- és válaszfejléceket, valamint a fejlécek új értékét. Létrehozhat új fejlécet, módosíthatja egy meglévő fejléc értékét, vagy törölhet egy meglévő t. Egy új fejléc vagy egy meglévő fejléc értéke az alábbi típusú értékekre állítható be:

- Szöveg.
- Kérelem fejléce. A kérelemfejléc megadásához a(z) {http_req_ headerName } szintaxist kell*használnia.*
- Válaszfejléc. Válaszfejléc megadásához a(z) {http_resp_ headerName } szintaxist kell*használnia.*
- Kiszolgáló változó. Kiszolgálóváltozó megadásához a(z) {var_*serverVariable*} szintaxist kell használnia.
- Szöveg, kérésfejléc, válaszfejléc és kiszolgálóváltozó kombinációja.

## <a name="server-variables"></a>Kiszolgálói változók

Az Application Gateway kiszolgálóváltozók segítségével tárolja a kiszolgálóval, az ügyféllel való kapcsolattal és a kapcsolataktuális kérésével kapcsolatos hasznos információkat. A tárolt adatok közé tartozik például az ügyfél IP-címe és a webböngésző típusa. A kiszolgálóváltozók dinamikusan változnak, például új oldal betöltésekor vagy űrlap feladásakor. Ezekkel a változókkal kiértékelheti az újraírási feltételeket, és átírhatja a fejléceket.

Az alkalmazásátjáró a következő kiszolgálóváltozókat támogatja:

| Változó neve | Leírás                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Az X-Forwarded-For client request `client_ip` fejlécmezője a változóval (lásd a táblázat későbbi részében található magyarázatot) IP1, IP2, IP3 és így tovább formátumban fűzte hozzá. Ha az X-Forwarded-For mező nem szerepel az `add_x_forwarded_for_proxy` ügyfélkérelem fejlécében, a változó megegyezik a `$client_ip` változóval. Ez a változó különösen akkor hasznos, ha az Application Gateway által beállított X-Forwarded-For fejlécet úgy szeretné újraírni, hogy a fejléc csak a portadatok nélküli IP-címet tartalmazza. |
| ciphers_supported          | Az ügyfél által támogatott titkosítások listája.          |
| ciphers_used               | A létrehozott TLS-kapcsolathoz használt titkosítási karakterlánc. |
| client_ip                  | Annak az ügyfélnek az IP-címe, amelytől az alkalmazásátjáró megkapta a kérést. Ha az alkalmazásátjáró és az eredeti ügyfél előtt fordított proxy van, *client_ip* a fordított proxy IP-címét adja vissza. |
| client_port                | Az ügyfélport.                                                  |
| client_tcp_rtt             | Információ az ügyfél TCP-kapcsolatáról. Olyan rendszereken érhető el, amelyek támogatják a TCP_INFO foglalatopciót. |
| client_user                | HTTP-hitelesítés használata esetén a hitelesítéshez megadott felhasználónév. |
| gazda                       | Ebben a sorrendben: az állomásnév a kérelemsorból, az állomásnév az Állomáskérelem fejlécmezőjéből, vagy a kérésnek megfelelő kiszolgálónév. |
| cookie_*név*              | A *süti neve.*                                            |
| http_method                | Az URL-kérelem benyújtásához használt módszer. Például GET vagy POST. |
| http_status                | A munkamenet állapota. Például 200, 400 vagy 403.                       |
| http_version               | A kérelem protokoll. Általában HTTP/1.0, HTTP/1.1 vagy HTTP/2.0. |
| query_string               | A kért URL-ben a "?" értéket követő változó/értékpárok listája. |
| received_bytes             | A kérelem hossza (beleértve a kérelemsort, a fejlécet és a kérelemtörzset). |
| request_query              | A kérelemsor argumentumai.                                |
| request_scheme             | A kérelemséma: http vagy https.                            |
| request_uri                | A teljes eredeti kérelem URI (argumentumokkal).                   |
| sent_bytes                 | Az ügyfélnek küldött bájtok száma.                             |
| server_port                | A kérést fogadó kiszolgáló portja.                 |
| ssl_connection_protocol    | A létrehozott TLS-kapcsolat protokollja.        |
| ssl_enabled                | "Be" ha a kapcsolat TLS módban működik. Ellenkező esetben egy üres karakterlánc. |

## <a name="rewrite-configuration"></a>Konfiguráció újraírása

A HTTP-fejléc újraírásának konfigurálásához végre kell hajtsa végre ezeket a lépéseket.

1. Hozza létre a HTTP-fejléc újraírásához szükséges objektumokat:

   - **Újraírási művelet:** Az újraírni kívánt kérés- és kérésfejléc-mezők és a fejlécek új értékének megadására szolgál. Egy vagy több újraírási feltételt újraírási művelethez társíthat.

   - **Újraírási feltétel**: Választható konfiguráció. Az újraírási feltételek kiértékelik a HTTP(S) kérések és válaszok tartalmát. Az újraírási művelet akkor történik meg, ha a HTTP(S) kérés vagy válasz megfelel az újraírási feltételnek.

     Ha egynél több feltételt társít egy művelethez, a művelet csak akkor történik meg, ha az összes feltétel teljesül. Más szóval a művelet logikai ÉS művelet.

   - **Újraírási szabály**: Több újraírási műveletet /újraírásfeltétel-kombinációt tartalmaz.

   - **Szabálysorozat**: Segít meghatározni az újraírási szabályok végrehajtásának sorrendjét. Ez a konfiguráció akkor hasznos, ha egy újraírási készletben több újraírási szabály is szerepel. Először egy alacsonyabb szabálysorozat-értékkel rendelkező újraírási szabály fut. Ha ugyanazt a szabálysorozatot két újraírási szabályhoz rendeli, a végrehajtás sorrendje nem determinisztikus.

   - **Újraírási készlet**: Több újraírási szabályt tartalmaz, amelyek egy kérelem-útválasztási szabályhoz lesznek társítva.

2. Csatlakoztassa az újraíró készletet (*átírási szabálykészletet)* egy útválasztási szabályhoz. Az újraírási konfiguráció az útválasztási szabályon keresztül csatlakozik a forrásfigyelőhöz. Ha alapútválasztási szabályt használ, a fejléc újraírási konfigurációja egy forrásfigyelőhöz van társítva, és globális fejléc-újraírás. Útalapú útválasztási szabály használata esetén a fejléc újraírási konfigurációja az URL-elérési út térképén lesz definiálva. Ebben az esetben csak a webhely adott elérési útterületén érvényes.
   > [!NOTE]
   > Az URL újraírása módosítja a fejléceket; nem módosítja az elérési út URL-címét.

Több HTTP-fejléc újraírási készletet hozhat létre, és minden újraírási készletet több figyelőre alkalmazhat. De csak egy újraírási készletet alkalmazhat egy adott figyelőhöz.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Íme néhány gyakori forgatókönyv a fejlécújraírás használatára.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Portadatok eltávolítása az X-Forwarded-For fejlécből

Az Application Gateway beszúr egy X-Forwarded-For fejlécet az összes kérelembe, mielőtt továbbítané a kérelmeket a háttérrendszernek. Ez a fejléc az IP-portok vesszővel tagolt listája. Előfordulhatnak olyan esetek, amikor a háttérkiszolgálóknak csak a fejléceknek kell IP-címeket tartalmazniuk. A fejléc újraírásával eltávolíthatja a portadatokat az X-Forwarded-For fejlécből. Ennek egyik módja a fejléc beállítása a add_x_forwarded_for_proxy kiszolgálóváltozóra:

![Port eltávolítása](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Átirányítási URL-cím módosítása

Amikor egy háttéralkalmazás átirányítási választ küld, érdemes lehet átirányítani az ügyfelet egy másik URL-címre, mint a háttéralkalmazás által megadott. Ezt például akkor érdemes megtennie, ha egy alkalmazásszolgáltatás egy alkalmazásátjáró mögött található, és az ügyfélnek át kell haladnia a relatív elérési útjára. (Például egy átirányítás contoso.azurewebsites.net/path1-ról contoso.azurewebsites.net/path2.)

Mivel az App Service egy több-bérlős szolgáltatás, a kérelemben lévő gazdagépfejlécet használja a kérelem megfelelő végpontra való továbbításához. Az alkalmazásszolgáltatások alapértelmezett tartományneve *.azurewebsites.net (mondjuk contoso.azurewebsites.net), amely eltér az alkalmazásátjáró tartománynevétől (például contoso.com). Mivel az ügyfél eredeti kérése az alkalmazásátjáró tartományneve (contoso.com) az állomásnév, az alkalmazásátjáró az állomásnevet contoso.azurewebsites.net módosítja. Ez a módosítás, hogy az alkalmazásszolgáltatás a kérést a megfelelő végpontra irányíthassa.

Amikor az alkalmazásszolgáltatás átirányítási választ küld, ugyanazt az állomásnevet használja a válasz helyfejlécében, mint az alkalmazásátjárótól kapott kérelemben. Így az ügyfél közvetlenül a contoso.azurewebsites.net/path2, ahelyett, hogy az alkalmazásátjárón (contoso.com/path2) keresztül. Az alkalmazásátjáró megkerülése nem kívánatos.

A probléma úgy oldható meg, hogy a helyfejlécben lévő állomásnevet az alkalmazásátjáró tartománynevére állítja be.

Az állomásnév cseréjének lépései:

1. Hozzon létre egy újraírási szabályt egy feltétellel, amely kiértékeli, hogy a válasz helyfejléce tartalmaz-e azurewebsites.net. Adja meg `(https?):\/\/.*azurewebsites\.net(.*)$`a mintát .
1. Hajtson végre egy műveletet a helyfejléc újraírásához, hogy az az alkalmazásátjáró állomásnevét tartalmazza. Ehhez adja `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` meg a fejléc értékét.

![Helyfejléc módosítása](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Biztonsági HTTP-fejlécek megvalósítása a biztonsági rések megelőzése érdekében

Számos biztonsági rést úgy oldhat meg, hogy a szükséges fejléceket alkalmazza az alkalmazásválaszban. Ezek a biztonsági fejlécek közé tartozik az X-XSS-Protection, a Strict-Transport-Security és a Content-Security-Policy. Az Application Gateway segítségével beállíthatja ezeket a fejléceket az összes válaszhoz.

![Biztonsági fejléc](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Nem kívánt fejlécek törlése

Előfordulhat, hogy el szeretné távolítani azokat a fejléceket, amelyek bizalmas információkat tárnak fel egy HTTP-válaszból. Előfordulhat például, hogy el szeretné távolítani az olyan információkat, mint a háttérkiszolgáló neve, az operációs rendszer vagy a tár adatai. Az alkalmazásátjáró segítségével eltávolíthatja ezeket a fejléceket:

![Fejléc törlése](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Fejléc jelenlétének ellenőrzése

Kiértékelheti a HTTP-kérelmek et vagy válaszfejléceket fejléc vagy kiszolgálóváltozó jelenlétére vonatkozóan. Ez a kiértékelés akkor hasznos, ha csak akkor szeretne fejlécújraírást végezni, ha egy bizonyos fejléc van jelen.

![Fejléc jelenlétének ellenőrzése](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Korlátozások

- Ha egy válasznak egynél több azonos nevű fejléce van, akkor az egyik fejléc értékének újraírása a többi fejléc eldobását eredményezi a válaszban. Ez általában a Set-Cookie fejlécével fordulhat elő, mivel egy válaszban több Set-Cookie fejléc is lehet. Az egyik ilyen forgatókönyv az, ha egy alkalmazásszolgáltatást egy alkalmazásátjáróval használ, és cookie-alapú munkamenet-affinitást konfigurált az alkalmazásátjárón. Ebben az esetben a válasz két Set-Cookie fejlécet fog tartalmazni: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` az egyiket például az alkalmazásszolgáltatás `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`használja: és egy másikat az alkalmazásátjáró-affinitással, például . Ebben a forgatókönyvben a Set-Cookie fejlécek egyikének újraírása azt eredményezheti, hogy eltávolítja a másik Set-Cookie fejlécet a válaszból.

- A Kapcsolat, frissítés és állomásfejlécek újraírása jelenleg nem támogatott.

- A fejlécnevek az [RFC 7230-ban](https://tools.ietf.org/html/rfc7230#page-27)meghatározott alfanumerikus karaktereket és szimbólumokat tartalmazhatnak. Jelenleg nem támogatjuk az\_aláhúzás ( ) speciális karaktert a fejlécnevekben.

## <a name="next-steps"></a>További lépések

A HTTP-fejlécek átírásáról a következő témakörökben olvashat:

- [HTTP-fejlécek újraírása az Azure Portal használatával](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [HTTP-fejlécek újraírása az Azure PowerShell használatával](add-http-header-rewrite-rule-powershell.md)
