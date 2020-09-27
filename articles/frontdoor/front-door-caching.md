---
title: Azure bejárati ajtó – gyorsítótárazás | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan használhatók a bejárati ajtók a gyorsítótárazást engedélyező útválasztási szabályokkal.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2020
ms.author: duau
ms.openlocfilehash: 9279b3e77147449ae0ede0cc0b76e57f130c9a44
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398031"
---
# <a name="caching-with-azure-front-door"></a>Gyorsítótárazás az Azure bejárati ajtaján
A következő dokumentum a bejárati ajtó működésének módját határozza meg az olyan útválasztási szabályokkal, amelyeken engedélyezve van a gyorsítótárazás. A bejárati ajtó egy modern Content Delivery Network (CDN), és a dinamikus hely gyorsításával és a terheléselosztással együtt a gyorsítótárazási viselkedést is támogatja, ugyanúgy, mint bármely más CDN.

## <a name="delivery-of-large-files"></a>Nagyméretű fájlok kézbesítése
Az Azure bejárati ajtaja nagy méretű fájlokat biztosít a fájlméret nélküli korlát nélkül. A bejárati ajtó egy objektum-darabolás nevű technikát használ. Amikor nagyméretű fájlokat igényelnek, a Front Door lekéri a fájl kisebb darabjait a háttérrendszerről. Ha a rendszer teljes vagy bájttartományra vonatkozó fájlkérelmet kap, a Front Door-környezet 8 MB-os darabokban kéri le a fájlt a háttérrendszerről.

</br>Miután a rendszer beolvasta a tömböt az első ajtós környezetben, a gyorsítótárba kerül, és azonnal kiszolgálja a felhasználónak. A bejárati ajtó ezután előre lekéri a következő adatrészletet párhuzamosan. Ez az előzetes beolvasás biztosítja, hogy a tartalom a felhasználó előtt egy darabban maradjon, ami csökkenti a késést. Ez a folyamat addig folytatódik, amíg a teljes fájl le nem töltődik (ha szükséges), az összes bájtos tartomány elérhető (ha szükséges), vagy az ügyfél leállítja a csatlakozást.

</br>A byte-Range kérelemmel kapcsolatos további információkért olvassa el a következőt: [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
A bejárati ajtó gyorsítótárba helyezi a kapott adattömböket, így a teljes fájlt nem kell gyorsítótárazni a bejárati ajtó gyorsítótárában. A fájl-vagy byte-tartományokra vonatkozó további kérelmek a gyorsítótárból lesznek kézbesítve. Ha nem az összes adathalmaz gyorsítótárazva van, a rendszer a háttérbeli adattömböket kéri le. Ez az optimalizálás arra támaszkodik, hogy a háttérrendszer képes támogatni a bájtos tartományokra vonatkozó kérelmeket; Ha a háttérrendszer nem támogatja a bájtok közötti kérelmeket, ez az optimalizálás nem érvényes.

## <a name="file-compression"></a>Fájltömörítés
A bejárati ajtó dinamikusan tömörítheti a tartalmat a peremhálózat szélén, így kevesebb és gyorsabb választ kaphat az ügyfeleknek. Az összes fájl tömörítésre alkalmas. A fájlnak azonban olyan MIME-típusnak kell lennie, amely jogosult a tömörítési listára. Jelenleg a bejárati ajtó nem teszi lehetővé a lista módosítását. Az aktuális lista:</br>
- "alkalmazás/EOT"
- "alkalmazás/betűkészlet"
- "alkalmazás/betűkészlet – sfnt"
- "alkalmazás/JavaScript"
- alkalmazás/json
- "alkalmazás/OpenType"
- "alkalmazás/OTF"
- "Application/PKCS7 – MIME"
- "alkalmazás/TrueType"
- "Application/TTF",
- "Application/vnd. MS-fontobject"
- "Application/XHTML + XML"
- "Application/XML"
- "alkalmazás/XML + RSS"
- "application/x-font-OpenType"
- "application/x-font-TrueType"
- "application/x-font-TTF"
- "application/x-httpd-CGI"
- "application/x-mpegurl"
- "application/x-OpenType"
- "application/x-OTF"
- "application/x-Perl"
- "application/x-TTF"
- "alkalmazás/x-JavaScript"
- "font/EOT"
- "betűkészlet/TTF"
- "font/OTF"
- "betűkészlet/OpenType"
- "rendszerkép/SVG + XML"
- "text/CSS"
- "text/CSV"
- "text/html"
- "text/JavaScript"
- "text/js", "text/plain"
- "text/RichText"
- "szöveg/tabulátorral tagolt értékek"
- "text/XML"
- "text/x-script"
- "text/x-Component"
- "text/x-Java-Source"

Emellett a fájlnak 1 KB és 8 MB méretűnek kell lennie, beleértve a-t is.

Ezek a profilok a következő tömörítési kódolásokat támogatják:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Ha egy kérelem támogatja a gzip és a Brotli tömörítést, a Brotli-tömörítés elsőbbséget élvez.</br>
Ha egy adategységre vonatkozó kérelem meghatározza a tömörítést, és a kérés gyorsítótár-hiányt eredményez, a bejárati ajtó közvetlenül a POP-kiszolgálón végzi el az eszköz tömörítését. Ezt követően a tömörített fájlt a rendszer a gyorsítótárból kézbesíti. Az eredményül kapott tételt egy átvitel – Encoding: darabolásos érték adja vissza.

## <a name="query-string-behavior"></a>Lekérdezési karakterlánc viselkedése
A bejárati ajtó segítségével szabályozhatja, hogy a rendszer hogyan gyorsítótárazza a fájlokat a lekérdezési karakterláncot tartalmazó webes kérelmek esetében. Lekérdezési karakterláncot tartalmazó webes kérelem esetén a lekérdezési karakterlánc a kérelemnek a kérdőjel (?) utáni részét jelöli. A lekérdezési karakterláncok tartalmazhatnak egy vagy több kulcs-érték párokat, amelyekben a mező nevét és értékét egy egyenlőségjel (=) választja el egymástól. A kulcs-érték párokat egy jel (&) választja el egymástól. Például: `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Ha egy kérelem lekérdezési karakterláncában egynél több kulcs-érték pár szerepel, a rendelésük nem számít.
- **Lekérdezési karakterláncok figyelmen kívül hagyása**: ebben a módban a bejárati ajtó továbbítja a lekérdezési karakterláncokat a kérelmezőtől az első kérelemben található háttérbe, és gyorsítótárazza az eszközt. Az eszköz bejárati környezetből kiszolgált összes további kérelme figyelmen kívül hagyja a lekérdezési karakterláncokat, amíg a gyorsítótárazott eszköz le nem jár.

- **Gyorsítótár – minden egyedi URL-cím**: ebben a módban minden egyedi URL-címmel rendelkező kérelem, beleértve a lekérdezési karakterláncot, a saját gyorsítótárral rendelkező egyedi objektumként lesz kezelve. Például a rendszer a (z) rendszerre irányuló kérelem esetében a háttérbeli választ `www.example.ashx?q=test1` gyorsítótárazza a bejárati ajtó környezetében, és ugyanezen lekérdezési karakterlánccal visszaadja a későbbi gyorsítótárak esetében. A szolgáltatásra irányuló kérelmet `www.example.ashx?q=test2` külön eszközként gyorsítótárazza a saját élettartama beállítással.

## <a name="cache-purge"></a>Gyorsítótár kiürítése

A bejárati ajtó gyorsítótárazza az eszközöket, amíg az eszköz élettartama (TTL) lejár. Miután az objektum ÉLETTARTAMa lejár, amikor egy ügyfél kéri az eszközt, az előtérben lévő környezet lekérdezi az eszköz új példányát az ügyfél kérelmének kiszolgálásához, és tárolja a gyorsítótár frissítését.

Az ajánlott eljárás annak biztosítására, hogy a felhasználók mindig megkapják az adategységek legújabb példányát, hogy minden egyes frissítéshez a saját eszközeiket, és azokat új URL-ként tegye közzé. A bejárati ajtó azonnal lekéri az új eszközöket a következő ügyfelek kéréseire. Előfordulhat, hogy a gyorsítótárazott tartalmat törölni kívánja az összes peremhálózati csomópontról, és az összeset kényszeríti az új eszközök beolvasására. Ennek oka lehet a webalkalmazás frissítései, vagy a helytelen adatokat tartalmazó eszközök gyors frissítése.

Válassza ki azokat az eszközöket, amelyeket ki szeretne üríteni a peremhálózati csomópontokból. Az összes eszköz törléséhez válassza **az összes törlése**lehetőséget. Ellenkező esetben az **elérési út**mezőben adja meg a kiüríteni kívánt eszközök elérési útját.

Ezek a formátumok a kiüríteni kívánt elérési utak listája esetén támogatottak:

- **Egyetlen útvonal kiürítése**: az egyes eszközök kiürítése az eszköz teljes elérési útjának megadásával (a protokoll és a tartomány nélkül), például:/Pictures/strasbourg.png;
- **Helyettesítő karakteres törlés**: \* a csillag () helyettesítő karakterként is használható. Kiüríti az összes mappát, almappát és fájlt egy végpont alatt, \* az elérési úton, illetve az összes almappa és fájl kiürítése egy adott mappában, a mappa és a \* (például/Pictures/ \* ) után.
- **Gyökértartomány kiürítése**: Ürítse ki a végpont gyökerét az elérési úton található "/" értékkel.

> [!NOTE]
> **Helyettesítő karakteres tartományok törlése**: az ebben a szakaszban tárgyalt, a kiürítéshez használt gyorsítótárazott útvonalak megadása nem vonatkozik a bejárati ajtóhoz társított helyettesítő karakteres tartományokra. Jelenleg nem támogatott a helyettesítő karakteres tartományok közvetlen törlése. Az egyes altartományokból származó útvonalakat a terjesztésipont megcélzó altartomány és a kiürítési útvonal megadásával törölheti. Ha például a bejárati ajtóm `*.contoso.com` , begépelve törölhetem az altartományom eszközeit `foo.contoso.com` `foo.contoso.com/path/*` . Jelenleg a kiürítési tartalom elérési útján megadott állomásnevek megadása a helyettesítő karakterek altartományára imited, ha van ilyen.
>

A bejárati ajtón a gyorsítótár kiürítése kis-és nagybetűk megkülönböztetése nélkül történik. Emellett a lekérdezési karakterláncokat is használják, ami azt jelenti, hogy az URL-cím ürítése törli az összes lekérdezési karakterlánc-változatot. 

## <a name="cache-expiration"></a>Gyorsítótár lejárata
A rendszer a következő fejlécek sorrendjét használja annak meghatározásához, hogy mennyi ideig tárolja a rendszer az elemeket a gyorsítótárban:</br>
1. Gyorsítótár-vezérlő: s-maxage =\<seconds>
2. Cache-Control: Max-Age =\<seconds>
3. Lejár \<http-date>

Cache-Control Response fejlécek, amelyek azt jelzik, hogy a válasz nem lesz gyorsítótárazva, például a Cache-Control: Private, Cache-Control: no-cache és Cache-Control: No-Store tiszteletben.  Ha nincs gyorsítótár-vezérlő, az alapértelmezett viselkedés az, hogy a AFD gyorsítótárazza az erőforrást X időtartamra, ahol az X véletlenszerűen 1 és 3 nap közötti értéket vesz fel.

## <a name="request-headers"></a>Kérésfejlécek

A következő kérések fejléceit a rendszer nem továbbítja a háttérbe gyorsítótárazás használatakor.
- Content-Length
- Átvitel – kódolás

## <a name="cache-duration"></a>Gyorsítótár időtartama

A gyorsítótár időtartama a bejárati tervezőben és a szabályok motorjában is konfigurálható. A Frontdoor Designerben beállított gyorsítótár időtartama a gyorsítótár minimális időtartama. Ez a felülbírálás nem fog működni, ha a forrás gyorsítótár-vezérlő fejléce nagyobb TTL-értékkel rendelkezik, mint a felülbírálás értéke. 

A gyorsítótár időtartamának beállítása a Rules Engine használatával igaz gyorsítótár-felülbírálás, ami azt jelenti, hogy a felülbírálási értéket fogja használni, függetlenül attól, hogy mi a forrás válasz fejléce.

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
