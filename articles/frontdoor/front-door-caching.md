---
title: Azure bejárati ajtó - gyorsítótárazás | Microsoft dokumentumok
description: Ez a cikk segít megérteni, hogy az Azure Front Door hogyan figyeli a háttérrendszerek állapotát
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
ms.openlocfilehash: d4fed878e2c0b1430e963f43743fd772493d3270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471744"
---
# <a name="caching-with-azure-front-door"></a>Gyorsítótárazás az Azure bejárati ajtajával
A következő dokumentum a ház elülső ajtajának működését határozza meg olyan útválasztási szabályokkal, amelyek engedélyezték a gyorsítótárazást. Bejárati ajtó egy modern Content Delivery Network (CDN), és így együtt dinamikus site gyorsítás és terheléselosztás, ez is támogatja a gyorsítótárazás idotartama, mint bármely más CDN.

## <a name="delivery-of-large-files"></a>Nagy fájlok kézbesítése
Az Azure Front Door nagy fájlokat biztosít a fájlméret korlátozása nélkül. A Bejárati ajtó az objektumdarabolásnak nevezett technikát használja. Amikor nagyméretű fájlokat igényelnek, a Front Door lekéri a fájl kisebb darabjait a háttérrendszerről. Ha a rendszer teljes vagy bájttartományra vonatkozó fájlkérelmet kap, a Front Door-környezet 8 MB-os darabokban kéri le a fájlt a háttérrendszerről.

</br>Miután az adattömb megérkezik a bejárati ajtó környezetben, a gyorsítótárba kerül, és azonnal kiszolgálja a felhasználónak. Bejárati ajtó, majd előre fetches a következő darab párhuzamosan. Ez az előzetes lekérés biztosítja, hogy a tartalom egy adattömböt maradjon a felhasználó előtt, ami csökkenti a késést. Ez a folyamat addig folytatódik, amíg a teljes fájlt le nem tölti (ha szükséges), az összes bájttartomány elérhető (ha szükséges), vagy az ügyfél meg nem szakítja a kapcsolatot.

</br>A bájttartomány-kérelemmel kapcsolatos további információkért olvassa el [a 7233.RFC](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
A Bejárati ajtó gyorsítótárazza az adattömböket, ahogy azok beérkeznek, így a teljes fájlt nem kell gyorsítótárazni a bejárati ajtó gyorsítótárában. A fájlra vagy bájtos tartományokra vonatkozó további kérelmek et a gyorsítótárból szolgálják ki. Ha nem az összes adattömb van gyorsítótárazva, az előzetes lekérés a háttérrendszerből származó adattömbök kérésére szolgál. Ez az optimalizálás a háttér-képernyő bájttartomány-kérelmek támogatásának képességén alapul; ha a háttérszolgáltatás nem támogatja a bájttartomány-kérelmeket, ez az optimalizálás nem hatékony.

## <a name="file-compression"></a>Fájltömörítés
A Bejárati ajtó dinamikusan tömöríti a peremhálózaton lévő tartalmakat, ami kisebb és gyorsabb választ eredményez az ügyfelek számára. Minden fájl jogosult a tömörítésre. A fájloknak azonban olyan MIME típusúnak kell lenniük, amely jogosult a tömörítési listára. Jelenleg a Bejárati ajtó nem teszi lehetővé a lista módosítását. Az aktuális lista:</br>
- "alkalmazás/eot"
- "alkalmazás/betűtípus"
- "alkalmazás/betűtípus-sfnt"
- "alkalmazás/javascript"
- alkalmazás/json
- "alkalmazás/opentype"
- "alkalmazás/otf"
- "alkalmazás/pkcs7-pantomim"
- "alkalmazás/truetype"
- "alkalmazás/ttf",
- "alkalmazás/vnd.ms-fontobject"
- "alkalmazás/xhtml+xml"
- "alkalmazás/xml"
- "alkalmazás/xml+rss"
- "alkalmazás/x-font-opentype"
- "alkalmazás/x-font-truetype"
- "alkalmazás/x-font-ttf"
- "alkalmazás/x-httpd-cgi"
- "alkalmazás/x-mpegurl"
- "alkalmazás/x-opentype"
- "alkalmazás/x-otf"
- "alkalmazás/x-perl"
- "alkalmazás/x-ttf"
- "alkalmazás/x-javascript"
- "font/eot"
- "betűtípus/ttf"
- "betűtípus/otf"
- "betűtípus/opentype"
- "kép/svg+xml"
- "szöveg/css"
- "szöveg/csv"
- "szöveg/html"
- "szöveg/javascript"
- "szöveg/js", "szöveg/sima"
- "szöveg/richtext"
- "szöveg/tabulátor-elválasztott értékek"
- "szöveg/xml"
- "szöveg/x-script"
- "szöveg/x-összetevő"
- "szöveg/x-java-source"

Ezenkívül a fájlnak 1 KB és 8 MB közötti méretűnek kell lennie.

Ezek a profilok a következő tömörítési kódolásokat támogatják:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli között](https://en.wikipedia.org/wiki/Brotli)

Ha egy kérelem támogatja a gzip és brotli tömörítést, a Brotli-tömörítés élvez elsőbbséget.</br>
Ha egy eszközre vonatkozó kérelem tömörítést határoz meg, és a kérelem gyorsítótár-tévesztést eredményez, a Bejárati ajtó közvetlenül a POP-kiszolgálón hajtja végre az eszköz tömörítését. Ezt követően a tömörített fájl a gyorsítótárból jelenik meg. Az eredményül kapott cikk átadás-kódolással kerül visszaadásra: darabolva.

## <a name="query-string-behavior"></a>Lekérdezési karakterlánc viselkedése
A Bejárati ajtó segítségével szabályozhatja, hogy a rendszer hogyan gyorsítótárazzata a fájlokat egy lekérdezési karakterláncot tartalmazó webes kérelemhez. A lekérdezési karakterláncot tartalmazó webes kérelemben a lekérdezési karakterlánc a kérelemnek az a része, amely egy kérdőjel (?) után következik be. A lekérdezési karakterlánc egy vagy több kulcs-érték párt tartalmazhat, amelyekben a mező nevét és értékét egyenlőségjel (=) választja el egymástól. Minden kulcs-érték párt egy -mampersand (&) választ el egymástól. Például: `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Ha egy kérelem lekérdezési karakterláncában egynél több kulcs-érték pár van, a sorrendjük nem számít.
- **Lekérdezési karakterláncok figyelmen kívül hagyása**: Alapértelmezett mód. Ebben a módban a Bejárati ajtó átadja a lekérdezési karakterláncokat a kérelmezőa konkretor az első kérés, és gyorsítótárazza az eszközt. A bejárati ajtó környezetből kiszolgált eszköz minden további kérése figyelmen kívül hagyja a lekérdezési karakterláncokat, amíg a gyorsítótárazott eszköz le nem jár.

- **Minden egyedi URL gyorsítótárazása:** Ebben a módban minden egyedi URL-címmel rendelkező kérelem, beleértve a lekérdezési karakterláncot is, egyedi eszközként lesz kezelve, saját gyorsítótárral. Például a válasz a háttérrendszer egy `www.example.ashx?q=test1` kérelem gyorsítótárba a bejárati ajtaját környezetben, és visszaadja a későbbi gyorsítótárak ugyanazzal a lekérdezési karakterlánccal. A kérelem `www.example.ashx?q=test2` a gyorsítótárba, mint egy külön eszköz saját time-to-live beállítást.

## <a name="cache-purge"></a>Gyorsítótár-kiürítés
A Bejárati ajtó gyorsítótárazza az eszközöket, amíg az eszköz élni való lejárati ideje (TTL) le nem jár. Miután az eszköz TTL lejár, amikor egy ügyfél kéri az eszközt, a Bejárati ajtajának környezet lekéri az eszköz egy új frissített példányát az ügyfélkérelem kiszolgálásához és a gyorsítótár frissítésének tárolásához.
</br>Az ajánlott eljárás annak biztosítása érdekében, hogy a felhasználók mindig megkapják az eszközök legújabb példányát, hogy minden egyes frissítéshez verziószámba adják az eszközöket, és új URL-ként teszik közzé őket. A Front Door azonnal lekéri az új eszközöket a következő ügyfélkérelmekhez. Előfordulhat, hogy szeretné kiüríteni a gyorsítótárazott tartalmat az összes peremhálózati csomópontról, és mindet kényszeríteni az új frissített eszközök lekéréséhez. Ennek oka lehet a webalkalmazás frissítése, vagy a helytelen adatokat tartalmazó eszközök gyors frissítése.

</br>Válassza ki, hogy milyen eszközöket szeretne kiüríteni a peremhálózati csomópontokról. Ha törölni szeretné az összes eszköz törlését, kattintson az Összes törlése jelölőnégyzetre. Ellenkező esetben írja be a kiüríteni kívánt eszközök elérési útját a Görbe mezőbe. Az alábbi formátumok at támogatja az elérési út.
1. **Egyútvonal-kiürítés**: Az egyes eszközök kiürítése az eszköz teljes elérési útjának megadásával (a protokoll és a tartomány nélkül), a fájlkiterjesztéssel, például /pictures/strasbourg.png;
2. **Helyettesítő karakter kiürítése**\*: Csillag ( ) használható helyettesítő karakterként. Ürítse ki az összes mappát, almappát és fájlt a végpont alatt a /\* az elérési úton,\*vagy ürítse\*ki az összes almappát és fájlt egy adott mappa alatt a mappa megadásával, majd a / , például a /pictures/ .
3. **Gyökértartomány kiürítése**: Ürítse ki a végpont gyökerét az elérési úton lévő "/" kapcsolóval.

A bejárati ajtón lévő gyorsítótár-tisztítások nem különböznek a kis- és nagybetűktől. Emellett ezek lekérdezési karakterlánc agnosztikus, ami azt jelenti, hogy egy URL-cím törlése törli az összes lekérdezés-karakterlánc variációk is. 

## <a name="cache-expiration"></a>Gyorsítótár lejárata
A fejlécek következő sorrendje alapján állapítható meg, hogy egy elem mennyi ideig lesz tárolva a gyorsítótárban:</br>
1. Cache-Control: s-maxage\<= másodperc>
2. Cache-Control: max-age\<= másodperc>
3. Lejár: \<http-date>

Cache-Control válaszfejlécek, amelyek azt jelzik, hogy a válasz nem lesz gyorsítótárazva, mint például a Cache-Control: private, Cache-Control: no-cache, és a Cache-Control: no-store a rendszer tiszteletben tartja. Ha azonban egy POP-ban több kérelem van egy POP-on ugyanarra az URL-re vonatkozóan, megoszthatják a választ. Ha nincs gyorsítótár-vezérlés jelen az alapértelmezett viselkedés, hogy az AFD gyorsítótárazza az erőforrást X idő, amikor X véletlenszerűen szedett között 1-3 nap.

## <a name="request-headers"></a>Kérésfejlécek

A következő kérelemfejlécek nem lesznek továbbtovábbítva a háttér-gyorsítótárazás használatakor.
- Tartalom hossza
- Átvitel-kódolás

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
