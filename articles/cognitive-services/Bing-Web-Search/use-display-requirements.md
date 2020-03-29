---
title: A Bing Search API-k követelményeinek használata és megjelenítése
titleSuffix: Azure Cognitive Services
description: A Bing Search API-k keresési eredményeinek az alkalmazásokban való megjelenítésének követelményei.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 5575668f164b97142e7c4b2ddb2608c3173426a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "60499885"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Bing Search API – követelmények használata és megjelenítése

Ezek a használati és megjelenítési követelmények a következő Bing Search API-kból származó tartalom és kapcsolódó információk minden implementációjára vonatkoznak, beleértve a kapcsolatokat, a metaadatokat és más jeleket.

- Bing – Egyéni keresés
- Bing – Entitáskeresés
- Bing – Képkeresés
- Bing – Hírkeresés
- Bing – Videokeresés
- Bing vizuális keresés
- Bing – Internetes keresés
- Bing – Helyesírás-ellenőrzés
- Bing – Automatikus kiegészítés

## <a name="definitions"></a>Meghatározások


|Időtartam  |Leírás  |
|---------|---------|
|Válasz     | A válaszban visszaadott eredmények kategóriája. A Bing Web Search API válaszai például válaszokat tartalmazhatnak a weblapok eredményének, a képnek, a videónak, a vizualizációnak és a híreknek a kategóriáiban. |
|Válasz     | A keresési API-ra irányuló egyetlen hívásra kapott válaszok és a kapcsolódó adatok. |
|Eredmény    | Információtétel a válaszban. Például az egyetlen hírcikkhez kapcsolódó adathalmaz egy hírválasz eredménye. |
|API-k keresése    | a Bing egyéni keresés, az entitáskeresés, a képkeresés, a hírkeresés, a videókeresés, a vizuális keresés, a helyi üzleti keresés és a webes keresési API-k együttesen. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Bing helyesírás-ellenőrzés és a Bing Autosuggest API-korlátozások

nem:

- Másolja, tárolja vagy gyorsítótárazza a Bing helyesírás-ellenőrzéséből vagy a Bing automatikus javaslati API-kból kapott adatokat.
- A Bing helyesírás-ellenőrzéstől vagy a Bing automatikus javaslati API-któl kapott adatokat bármely gépi tanulás vagy hasonló algoritmikus tevékenység részeként használhatja. Ne használja ezeket az adatokat olyan új vagy meglévő szolgáltatások betanítására, értékelésére vagy fejlesztésére, amelyeket Ön vagy harmadik felek kínálhatnak.

## <a name="bing-search-apis"></a>Bing Search API-k

> [!NOTE]
> Az ebben a szakaszban szereplő követelmények csak a keresési API-kra vonatkoznak, amelyek nem tartalmazzák a Bing helyesírás-ellenőrzést vagy a Bing automatikus javaslatot. 

### <a name="internet-search-experience-requirements"></a>Internetes keresési élményre vonatkozó követelmények

A válaszokban visszaadott összes adat csak internetes keresési élményben használható fel. Az internetes keresési élmény a következő tartalmat jelenti: 

- Releváns és reagál a végfelhasználó közvetlen lekérdezésére, vagy a keresési érdeklődésés szándékuk egyéb jelzésére (például egy felhasználó által jelzett keresési lekérdezésre). 

- Segít a felhasználóknak megtalálni és megkeresni a válasz adatforrásait. Például kattintható hivatkozásokat a válaszban lévő hivatkozásokból.

- Több találatot tartalmaz a felhasználó számára, hogy kiválasszon. 

- Olyan elhelyezésben vannak, amely lehetővé teszi a felhasználók számára a keresést.

- Látható jelzést tartalmaz arra vonatkozóan, hogy a tartalom internetes keresési eredmény. Például egy nyilatkozatot, hogy a tartalom "az interneten".

- Tartalmaz minden egyéb megfelelő intézkedést annak biztosítására, hogy a Bing Search API-adatai ne sértsék meg a vonatkozó törvényeket vagy harmadik felek jogait. Konzultáljon jogi tanácsadóival, hogy megtudja, milyen intézkedések lehetnek megfelelőek.

Az egyetlen kivétel ezek alól az internetes keresési élmény követelmények URL-felderítés, acikk későbbi részében leírtak szerint. 

### <a name="restrictions"></a>Korlátozások

nem:

- A válaszokból származó adatok másolása, tárolása vagy gyorsítótárazása (kivéve a [szolgáltatás folyamatossága](#continuity-of-service)által megengedett mértékben történő megőrzést . 

- A keresési API-kból kapott adatokat bármely gépi tanulás vagy hasonló algoritmikus tevékenység részeként használja. Ne használja ezeket az adatokat olyan új vagy meglévő szolgáltatások betanítására, értékelésére vagy fejlesztésére, amelyeket Ön vagy harmadik felek kínálhatnak.

- Módosítsa az eredmények tartalmát (kivéve, ha azokat olyan módon formázza, amely nem sért meg semmilyen más követelményt), kivéve, ha azt a törvény előírja, vagy amelyet a Microsoft elfogad. 

- Hagyja ki az eredménytartalomhoz társított hozzárendelési információkat és URL-eket.

- A megrendelés vagy rangsorolás megadása esetén a válaszban megjelenő eredmények átrendezése, beleértve a mulasztást is, kivéve, ha azt a törvény előírja vagy a Microsoft beleegyezik. 

    > [!NOTE]
    > Ez a követelmény nem vonatkozik a Bing egyéni keresési API portálján keresztül megvalósított újrarendelésre.

- A válasz bármely részében más tartalmakat úgy jeleníthet meg, hogy a felhasználó azt higgye, hogy a másik tartalom a válasz része. 

- Olyan hirdetések megjelenítése, amelyeket a Microsoft nem biztosít olyan oldalakon, amelyek a válasz bármely részét megjelenítik. 

- Bármilyen hirdetés megjelenítése a válaszokkal rendelkező oldalakon:
    - A Bing-kép, a hírkeresés, a videokeresés vagy a vizuális keresési API-kból
    - Ezek szűrt vagy korlátozott elsősorban (vagy kizárólag) a kép, hírek és / vagy videó vagy vizuális keresési eredményeket.

### <a name="notices-and-branding"></a>Közlemények és márkajelzés 
Tegye:

- Jól láthatóan tartalmaz egy funkcionális hivatkozást a [Microsoft adatvédelmi nyilatkozatára,](https://go.microsoft.com/fwlink/?LinkId=521839)amely a felhasználói élmény (UX) minden pontjához közel található, és lehetővé teszi a felhasználó számára a keresési lekérdezés bevitelét. A hivatkozás címkézése Microsoft **adatvédelmi nyilatkozata**.

- Jól láthatóan jeleníti meg a Bing márkajelzést, amely megfelel a [Bing védjegyhasználati irányelveinek,](https://go.microsoft.com/fwlink/?linkid=833278)a felhasználói felület minden olyan pontjának közelében, amely lehetővé teszi a felhasználó számára a keresési lekérdezés bevitelét. Az ilyen márkajelzésnek egyértelműen fel kell tüntetnie a felhasználó számára, hogy a Microsoft működteti az internetes keresési élményt.

- A Bing Web Search, Image Search, News Search, Video Search és Visual Search API-kból megjelenített egyes válaszokat (vagy azok egy részét) a Microsoftnak tulajdoníthatja, kivéve, ha a Microsoft írásban másként rendelkezik. Ezt a [Bing védjegyhasználati irányelvei ismertetik.](https://go.microsoft.com/fwlink/?linkid=833278) 

nem:

- A Bing Egyéni keresési API-ból a Microsoftnak megjelenített attribútumválaszok (vagy a válaszok egy része) – kivéve, ha a Microsoft írásban másként rendelkezik az Ön konkrét használatára vonatkozóan.

### <a name="transferring-responses"></a>Válaszok átvitele

Ha engedélyezi, hogy egy felhasználó egy Keresési API-ból választ továbbítson egy másik felhasználónak, például üzenetküldő alkalmazáson vagy közösségi média közzétételen keresztül, a következőket kell alkalmazni: 

- Az átadott válaszoknak:
  - Olyan tartalomból áll, amely nem módosul az átadó felhasználónak megjelenített válaszok tartalmától. A formázás módosítása megengedett.
  - Nem tartalmaz semmilyen adatot a metaadat-űrlapon.
  - A Bing Web, a Image, a News, a Video és a Visual API-k ból érkező válaszok esetén a bing által működtetett internetes keresési élményen keresztül kapott válasz megjelenítési nyelve. Megjelenítheti például a "Powered by Bing" vagy a "További információ erről a képről a Bingen" nyelvet, vagy használhatja a Bing emblémát.
  - A Bing egyéni keresési API-ból érkező válaszok esetén a megjelenítési nyelv jelzi, hogy a válasz egy internetes keresési felületen keresztül érkezett. Megjelenítheti például a következő nyelvet: "További információ erről a keresési eredményről".
  - Jól láthatóan jelenítse meg a válasz létrehozásához használt teljes lekérdezést.
  - Adjon meg egy jól látható vagy hasonló hozzárendelést a válasz alapjául szolgáló forráshoz, akár közvetlenül, akár a keresőmotoron (bing.com, m.bing.com vagy adott esetben az egyéni keresési szolgáltatáson keresztül).
- A válaszok átvitelét nem automatizálhatja. Az átvitelt olyan felhasználói művelettel kell kezdeményezni, amely egyértelműen a válasz átvitelének szándékát egyértelműen kinyilvánította.
- Csak az átvivő felhasználó lekérdezésére válaszul megjelenített válaszok átvitelét engedélyezheti a felhasználó számára.

### <a name="continuity-of-service"></a>A szolgáltatás folytonossága 

Ne másolja, tárolja vagy gyorsítótárazza a Keresési API-válaszokból származó adatokat. A szolgáltatáshoz való hozzáférés és az adatok leképezése folyamatosságának érdekében azonban az eredményeket kizárólag a következő feltételek mellett őrizheti meg:

#### <a name="device"></a>Eszköz

Engedélyezheti, hogy a felhasználó megőrizze az eredményeket az eszközön a lekérdezés időpontjától 24 órával, vagy (ii) addig, amíg a felhasználó egy másik lekérdezést nem küld a frissített eredményekhez, feltéve, hogy a megőrzött eredmények csak a következők:

- Annak lehetővé tétele, hogy a felhasználó hozzáférhessen az adott eszközön a felhasználónak korábban visszaadott eredményekhez (például szolgáltatásmegszakítás esetén).
- A proaktív lekérdezésre visszaadott eredmények tárolása a felhasználó igényeinek megfelelően személyre szabva, a felhasználó jelzései alapján (például a szolgáltatás várható megszakítása esetén).

#### <a name="server"></a>Kiszolgáló

Az ön által szabályozta kiszolgálón egyetlen felhasználóra vonatkozó eredményeket biztonságosan megőrizheti, és csak a megőrzött eredményeket jelenítheti meg:

- Annak lehetővé tétele, hogy a felhasználó hozzáférhessen egy korábbi jelentéshez a megoldásban az adott felhasználónak korábban visszaadott eredményekről. Az eredmények (i) nem őrizhetők meg a végfelhasználó kezdeti lekérdezésének időpontjától számított 21 napnál tovább, és (ii) nem jeleníthetők meg a felhasználó új vagy ismétlődő lekérdezésére válaszul.
- A proaktív lekérdezésre visszaadott eredmények tárolása a felhasználó igényeinek megfelelően személyre szabva, a felhasználó jelzései alapján. Ezeket az eredményeket a lekérdezés időpontjától 24 órával, vagy (ii) addig tárolhatja, amíg a felhasználó egy másik lekérdezést nem küld a frissített eredményekhez.

Ha megmarad, egy adott felhasználó eredményei nem keveredhetnek egy másik felhasználó eredményeivel. Ez azt illeti, az eredmények minden felhasználó meg kell őrizni, és külön-külön szállítják.

### <a name="general"></a>Általános kérdések 

A megőrzött eredmények bemutatásához:

- A lekérdezés elküldésének időpontjáról egyértelmű, látható értesítést kell megadnia.
- Mutasson be a felhasználónak egy gombot vagy hasonló eszközöket az újbóli lekérdezéshez és a frissített eredmények eléréséhez. 
- Az eredmények megjelenítésében tartsa meg a Bing márkajelzést.
- Törölje (és szükség esetén frissítse az új lekérdezést) a tárolt eredményeket a megadott időkereteken belül.

### <a name="non-display-url-discovery"></a>Nem megjelenített URL-felderítés 

A keresési válaszokat csak arra használhatja, hogy felfedezzék a felhasználó vagy az ügyfél lekérdezésére reagáló információforrások URL-jeit. Ezeket az URL-címeket átmásolhatja egy jelentésben vagy az Ön által megadott hasonló válaszokban:

- Csak az adott felhasználó vagy ügyfél, válaszul, hogy a lekérdezés.
- Csak akkor, ha jelentős további értékes tartalmat tartalmaz, amely releváns a lekérdezéshez.

A keresési API-k korábbi szakaszai nem vonatkoznak erre a nem megjelenített használatra, kivéve a következőket: 

- A keresési válaszból származó vagy abból származó adatokat, tartalmakat nem gyorsítótárazhatja, másolhatja és nem tárolja, kivéve a korábban ismertetett korlátozott URL-másolást.
- Győződjön meg arról, hogy a Keresési API-któl kapott adatok (beleértve az URL-eket is) használata nem sérti a vonatkozó törvényeket vagy harmadik felek jogait.
- Ne használja a keresési API-kból kapott adatokat (beleértve az URL-eket is) semmilyen keresési index, gépi tanulás vagy hasonló algoritmikus tevékenység részeként. Ne használja ezeket az adatokat olyan betanítási, kiértékelésére vagy javítására, amelyet Ön vagy harmadik fél kínálhat.

## <a name="gdpr-compliance"></a>GDPR-megfelelőség  

Az Európai Unió általános adatvédelmi rendeletének (GDPR) hatálya alá tartozó, a Keresési API-kra, a Bing Helyesírás-ellenőrző API-ra vagy a Bing Autosuggest API-ra irányuló hívásokkal kapcsolatos személyes adatok tekintetében Ön és a Microsoft adatvédelmi rendelet értelmében független adatkezelők számára. Ön függetlenül felelős a GDPR-nak való megfelelésért.  

