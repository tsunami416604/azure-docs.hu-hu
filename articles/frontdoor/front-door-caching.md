---
title: Az Azure bejárati ajtajának Service - gyorsítótárazás |} A Microsoft Docs
description: Ez a cikk segít megérteni, hogyan Azure bejárati ajtajának szolgáltatás állapotát követi figyelemmel a háttérkiszolgálókon
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 6c62e2e559749ae8dc29e86d9c2414c28b487995
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965619"
---
# <a name="caching-with-azure-front-door-service"></a>Az Azure bejárati ajtajának szolgáltatással gyorsítótárazás
A következő dokumentumban az útválasztási szabályokat, amelyeken engedélyezve gyorsítótárazási viselkedés határozza bejárati ajtajának.

## <a name="delivery-of-large-files"></a>Nagy fájlok kézbesítése
Azure bejárati ajtajának szolgáltatás egy korlát nélküli fájl mérete nagy fájlok tesz lehetővé. Bejárati ajtajának objektum darabolás nevezett technika használ. Nagy fájlok igénylésekor bejárati ajtajának kisebb darabokra a fájl a háttérbeli kérdezi le. Miután egy teljes vagy bájttartomány-fájl kérelmet kapott, a bejárati ajtajának környezet 8 MB-os blokkonként háttérrendszerből lekéri a fájlt.

</br>Után az adattömbök megérkezik a bejárati ajtajának környezet, a gyorsítótárba, és azonnal szolgálja ki a felhasználó számára. Bejárati ajtajának majd előre lekéri a következő adattömbök párhuzamosan. Az előzetes betöltési biztosítja, hogy a tartalom marad egy adattömb előre a felhasználót, ami csökkenti a késést. Ez a folyamat folytatódik, amíg a teljes fájl letöltése (ha szükséges), az összes bájttartományok érhetők el (ha szükséges), vagy az ügyfél megszakítja a kapcsolatot.

</br>Bájttartomány-kéréssel kapcsolatos további információkért olvassa el [RFC 7233](http://www.rfc-base.org/rfc-7233.html).
Bejárati ajtajának gyorsítótárazza a minden olyan adattömböket, fogadásuk, és így nem kell a teljes fájlt a bejárati ajtajának gyorsítótár a gyorsítótárban. A fájl vagy bájt címtartományok esetében későbbi kérelmeket a szolgáltatás a gyorsítótárból szolgálja ki. Ha nem összes adatrészletének lettek gyorsítótárazva, adattömbök kérhet a háttérrendszer lehívását használja. Az optimalizálás támaszkodik a háttérrendszer képességét bájttartomány-kérelmek; támogatásához Ha a háttérrendszer nem támogatja a bájttartomány-kérelmek, az optimalizálás nem elég hatékony.

## <a name="file-compression"></a>Fájltömörítés
Bejárati ajtajának dinamikusan tömörítheti a peremhálózaton, a tartalom, így kisebb és gyorsabban választ az ügyfelek számára. Fájlok tömörítési kerülnek. Azonban a fájl a MIME-típus, amely jogosult a tömörítés lista kell lennie. Bejárati ajtajának jelenleg nem engedélyezi az erre a listára módosítható. Az aktuális listáját a következő:</br>
- "application/eot"
- "application/betűtípus"
- "application/betűtípus-sfnt"
- "application/javascript"
- az "application/json"
- az "application/opentype"
- "application/otf"
- "application/pkcs7-mime"
- a "application/truetype"
- "application/ttf"
- "application/vnd.ms-fontobject"
- "application/xhtml + xml"
- "application/xml"
- "application/xml + rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- az "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-ttf"
- "application/x-javascript"
- "betűtípus/eot"
- "betűtípus/ttf"
- "betűtípus/otf"
- "betűtípus/opentype"
- "lemezkép/svg + xml"
- a "text/css"
- "text/csv"
- "text/html"
- a "text/javascript"
- "szöveg/js", "text/plain"
- "szöveg/richtext"
- "szöveg/lapon tagolt"
- "szöveg/xml"
- "szöveg/x-parancsfájl"
- "szöveg/x-component"
- "szöveg/x-java-source"

A fájl ezenkívül is állhat 1 KB-os és a 8 MB méretű, a határokat is beleértve.

Ezek a profilok az alábbi tömörítési kódolásokat támogatják:
- [a gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [brotli](https://en.wikipedia.org/wiki/Brotli)

Ha egy kérés támogatja a gzip és Brotli tömörítés, Brotli tömörítési élvez elsőbbséget.</br>
Amikor egy kérelem egy eszköz a tömörítés és a kérés eredményét adja meg a gyorsítótár-tévesztés, bejárati ajtajának az eszköz tömörítési közvetlenül a POP-kiszolgálón hajtja végre. A tömörített fájlt a későbbiekben a gyorsítótár szolgáltatja. Az eredményül kapott elemet a rendszer visszaadja az átviteli kódolást: darabolásos.

## <a name="query-string-behavior"></a>Lekérdezési karakterlánc viselkedés
Bejárati ajtajának szabályozhatja, hogyan kerül a gyorsítótárba webes egy lekérdezési karakterláncot tartalmazó kérelmek. Egy lekérdezési karakterláncot tartalmazó webes kérés esetén a lekérdezési karakterláncban része, amely a kérelemhez, amely egy kérdőjelet (?) után kerül sor. A lekérdezési karakterlánc tartalmazhat egy vagy több kulcs-érték párok, amelyek a mező nevének és értékének egyenlőségjelnek (=) elválasztva. Minden kulcs-érték pár elválasztott ampersand (&). Például: http://www.contoso.com/content.mov?field1=value1&field2=value2. Ha egynél több kulcs-érték pár egy kérelem egy lekérdezési karakterláncban, a sorrendjük nem számít.
- **Lekérdezési karakterláncok kihagyása**: alapértelmezett mód. Ebben a módban bejárati ajtajának adja át a lekérdezési karakterláncok a kérelmező, a háttérkiszolgáló az első kérés a, és az eszköz gyorsítótárazza. Az összes további kérelmet az eszköz számára a bejárati ajtajának-környezetben szolgáltatott, a lekérdezési karakterláncok kihagyása, amíg le nem jár a gyorsítótárazott objektumhoz.

- **Minden egyedi URL gyorsítótárazása**: Ebben a módban egy egyedi URL-címet, a lekérdezési karakterlánc, beleértve az egyes kérelmek egyedi saját gyorsítótár-eszközként kezelni. Például a háttérbeli vonatkozó kérést a válasz `www.example.ashx?q=test1` is a bejárati ajtajának környezet gyorsítótárazza, és az ezt követő gyorsítótárakhoz, az ugyanabban a lekérdezési karakterláncban adja vissza. Egy kérelem `www.example.ashx?q=test2` külön eszközként van gyorsítótárazva a saját time-to-live-beállítással.

## <a name="cache-purge"></a>Gyorsítótár kiürítése
Bejárati ajtajának eszközök gyorsítótárazzák, amíg az eszköz ideje-élettartam (TTL) le nem jár. Az eszköz TTL lejárata után, amikor egy ügyfél igényel az eszköz, a bejárati ajtajának környezetet fogja lekérni frissített másolatát az ügyfél kérése kiszolgálása érdekében az eszköz és a tároló a gyorsítótár frissítése.
</br>Az ajánlott eljárás, hogy a felhasználók mindig a legfrissebb verzióját a eszközöket szerezze be az eszközök frissítése a verzióra, és közzéteheti őket az új URL-címként. Bejárati ajtajának azonnal lekéri az új eszközök a következő az ügyféli kérelmek részére. Néha érdemes minden élcsomópontokból gyorsítótárazott tartalom végleges, és kényszerítheti az összeset lekérni frissített új eszközök. Ez a webes alkalmazások, illetve gyorsan helytelen adatokat tartalmazó frissítési eszközök frissítések oka lehet.

</br>Válassza ki a élcsomópontokból véglegesen törölni kívánt milyen eszközöket. Ha szeretné, törölje az összes eszköz, kattintson az összes jelölőnégyzetet a kiürítés. Ellenkező esetben adja meg az elérési út szövegmezőben véglegesen törölni kívánt minden egyes eszköz elérési útját. Az elérési út által támogatott formátumok alatt.
1. **Egyetlen URL-cím kiürítése**: kiterjesztésű fájl, például /pictures/strasbourg.png; a teljes URL-cím megadásával eszköz egyedi végleges törlése
2. **A helyettesítő karakteres kiürítés**: csillag (\*) helyettesítő karakter is használható. Összes mappa, almappák és fájlok alatt a végpont kiürítése /\* végleges törlés vagy az elérési utat az összes almappák és fájlok egy adott mappában a mappa megadásával követ /\*, például /képek/japán.png\*.
3. **Legfelső szintű tartomány kiürítése**: végleges törlése a végpontot az "/" az elérési út gyökerében.

Kezdettől fogva a gyorsítótár pon és nagybetűk nincsenek megkülönböztetve. Emellett azok a lekérdezési karakterlánc független, ami azt jelenti, egy URL-cím törlése véglegesen törli az összes lekérdezési karakterlánc-változatok azt. 

## <a name="cache-expiration"></a>Gyorsítótár elévülési ideje
A következő sorrendben fejlécek használt érdekében, hogy mennyi ideig lesz egy elemet a gyorsítótárban tárolt:</br>
1. A Cache-Control: s-maxage =<seconds>
2. A Cache-Control: maxage =<seconds>
3. Lejárat: < http-időpontja >

A Cache-Control válaszfejlécek, amelyek jelzik, hogy a válasz nem gyorsítótárazható, például a Cache-Control: magán, Cache-Control: no-cache és a gyorsítótár-vezérlés: no-store összes régió megfelel. Ha több kérés átvitel közben a jelenléti pontra Irányíthatja az azonos URL-címen, azonban előfordulhat, hogy a válasz közös.


## <a name="request-headers"></a>Kérelemfejlécek

A következő kérelemfejlécek nem lesznek továbbítva a háttérrendszernek gyorsítótár használatakor.
- Engedélyezés
- Content-Length
- Transfer-Encoding

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozzon létre egy bejárati ajtajának](quickstart-create-front-door.md).
- Ismerje meg, [bejárati ajtajának működése](front-door-routing-architecture.md).