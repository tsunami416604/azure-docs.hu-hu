---
title: Fuzzy keresés
titleSuffix: Azure Cognitive Search
description: Valósítson meg egy "érted" keresési élményt egy elgépelt kifejezés vagy elírás automatikus javításához.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 95f6befa7e97984ad64a3ffb2a9b91d343945cbb
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000660"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Fuzzy keresés az elírások és elírások kijavítására

Az Azure Cognitive Search támogatja az intelligens keresés, egy fajta lekérdezés, amely kompenzálja az elírások és elgépelt kifejezések a bemeneti karakterláncban. Ezt úgy éri el, hogy hasonló összetételű kifejezéseket keres. A keresés kiterjesztése a közeli találatra automatikusan korrigálja az elírást, ha az eltérés csak néhány rossz helyre tett karakter. 

## <a name="what-is-fuzzy-search"></a>Mi az a homályos keresés?

Ez egy terjeszkedési gyakorlat, amely hasonló összetételű feltételekkel egyezést hoz létre. Ha egy fuzzy keresés van megadva, a motor létrehoz egy grafikont (alapuló [determinisztikus véges automaton elmélet)](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)hasonlóan összeállított kifejezések, az összes egész kifejezéseket a lekérdezésben. Ha például a lekérdezés három kifejezést tartalmaz a "Washington egyetem", a `search=university~ of~ washington~` lekérdezés minden kifejezéséhez létrejön egy grafikon (az intelligens keresésben nincs stop-word eltávolítás, így az "of" egy grafikont kap).

A grafikon minden kifejezésből legfeljebb 50 kibontásból vagy permutációból áll, amelyek a folyamat helyes és helytelen változatait is rögzítik. A motor ezután a válasz ban a legfontosabb releváns egyezéseket adja vissza. 

Egy olyan kifejezés, mint az "egyetem", a grafikon lehet "unversty, universty, egyetem, univerzum, inverz". Minden olyan dokumentum, amely megfelel a grafikonon szereplő dokumentumoknak, szerepelni fog az eredményekközött. Ellentétben más lekérdezésekkel, amelyek elemzik a szöveget, hogy kezeljék az ugyanazon szó különböző formáit ("egerek" és "egér"), az intelligens lekérdezések összehasonlítása névértéken, a szöveg nyelvi elemzése nélkül kerül elemzésre. "Universe" és "inverz", amelyek szemantikailag különböző, majd egyezik, mert a szintaktikai eltérések kicsik.

Az egyezés akkor sikerül, ha az eltérések két vagy kevesebb szerkesztésre korlátozódnak, ahol a szerkesztés beszúrt, törölt, helyettesített vagy átültetett karakter. A differenciált megadó karakterlánc-korrekciós algoritmus a [Damerau-Levenshtein távolságmérő,](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) amelyet "a két szó másikba váltásához szükséges műveletek minimális száma (beszúrások, törlések, helyettesítések vagy két szomszédos karakter átültetése) írnak le.The string correction algorithm that specifies the differential is the Damerau-Levenshtein distance metric, described as the "minimum number of operations (insertis, delerations, szubsztitálás, vagy átültetéskét szomszédos karakterek) szükséges változtatni az egyik szót a másikba ". 

Az Azure Cognitive Search-ben:

+ Az intelligens lekérdezés teljes kifejezésekre vonatkozik, de az AND konstrukciókon keresztül is támogathatja a kifejezéseket. Például a "Unviersty~ of~ "Wshington~" megegyezik a "University of Washington"-ban.

+ A szerkesztés alapértelmezett távolsága 2. Az érték `~0` azt jelenti, hogy nincs kibontás (csak a pontos `~1` kifejezés tekinthető egyezésnek), de megadhat egy fokú különbséget vagy egy szerkesztést. 

+ Egy homályos lekérdezés legfeljebb 50 további permutációt bővíthet ki. Ez a korlát nem konfigurálható, de a szerkesztési távolság 1-re való csökkentésével hatékonyan csökkenthető a bővítések száma.

+ A válaszok olyan dokumentumokból állnak, amelyek releváns egyezést tartalmaznak (legfeljebb 50).

Együttesen a grafikonok az indexben lévő tokenekkel való egyezési feltételekként kerülnek elküldésre. Képzelheti, hogy az intelligens keresés eredendően lassabb, mint más lekérdezési űrlapok. Az index mérete és összetettsége meghatározhatja, hogy az előnyök elegendőek-e a válasz késésének ellensúlyozásához.

> [!NOTE]
> Mivel a homályos keresés általában lassú, érdemes lehet megvizsgálni az olyan alternatívákat, mint az n-gram indexelés, a rövid karaktersorozatok progressziójával (két és három karaktersorozat bigram és trigram tokenek esetén). A nyelvtől és a lekérdezési felülettől függően az n-gram jobb teljesítményt eredményezhet. A kompromisszum az, hogy az n-gram indexelés nagyon tárolási intenzív és sokkal nagyobb indexeket generál.
>
> Egy másik alternatíva, amely akkor úgy, ha azt szeretné kezelni, csak a legpéldátlanabb esetekben lenne [szinonima térkép](search-synonyms.md). Például a "keresés" leképezése "serach, serch, sarch" vagy "retrieve" a "retreive" kifejezésre.

## <a name="indexing-for-fuzzy-search"></a>Intelligens keresés indexelése

Az elemzők nem használatosak a lekérdezés feldolgozása során egy kibontási gráf létrehozásához, de ez nem jelenti azt, hogy az elemzőket figyelmen kívül kell hagyni az intelligens keresési forgatókönyvekben. Végül is az indexelés során analizátorokat használnak a tokenek létrehozásához, amelyek egyeztetése történik, függetlenül attól, hogy a lekérdezés szabad formában, szűrt keresés, vagy egy fuzzy keresés egy grafikon bemenetként. 

Általában, ha az analizátorok at mezőalapú alapon, a döntés, hogy finomítsa az elemzési lánc alapja az elsődleges használati eset (a szűrő vagy a teljes szöveges keresés), nem pedig speciális lekérdezési formák, mint a fuzzy keresés. Emiatt nincs külön analizátor ajánlás a fuzzy kereséshez. 

Ha azonban a tesztlekérdezések nem a várt egyezéseket eredményezik, megpróbálhatja az indexelési elemzőt, és [egy nyelvi elemzőre állítja,](index-add-language-analyzers.md)hogy jobb eredményeket érjen-e el. Egyes nyelvek, különösen a magánhangzó mutációkkal rendelkezők, élvezhetik a Microsoft természetes nyelvi processzorai által létrehozott inflexiós és szabálytalan szóformák előnyeit. Bizonyos esetekben a megfelelő nyelvi analizátor használata különbséget tehet abban, hogy egy kifejezés tokenizedje a felhasználó által megadott értékkel kompatibilis módon történik-e.

## <a name="how-to-use-fuzzy-search"></a>Fuzzy keresés használata

Az intelligens lekérdezések a Teljes Lucene lekérdezés szintaxisával készülnek, és a [Lucene-lekérdezéselemzőre hivatkoznak.](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)

1. Állítsa be a teljes Lucene-elemzőt a lekérdezésben (`queryType=full`).

1. Ha szükséges, a kérést adott mezőkre`searchFields=<field1,field2>`is ki kell terjeszteni, ezzel a paraméterrel ( ). 

1. Fűzte hozzá a`~`tilde ( ) operátort`search=<string>~`a teljes kifejezés ( ) végén.

   Ha meg szeretné adni a szerkesztési távolságot ( ) szeretné megadni,`~1`adjon meg egy nem kötelező paramétert, egy 0 és 2 közötti számot (alapértelmezett), ha meg szeretné adni a szerkesztési távolságot ( ). A "kék~" vagy a "kék~1" például a "kék", a "blues" és a "glue" szót adja vissza.

Az Azure Cognitive Search a kifejezés és a távolság (legfeljebb 2) mellett nincs további paramétereket a lekérdezésben.

> [!NOTE]
> A lekérdezés feldolgozása során az intelligens lekérdezések nem mennek át [lexikális elemzésnek.](search-lucene-query-architecture.md#stage-2-lexical-analysis) A lekérdezésbemenet közvetlenül hozzáadódik a lekérdezési fához, és kibontva hozzon létre egy kifejezésgrafikont. Az egyetlen végrehajtott átalakítás az alacsonyabb burkolat.

## <a name="how-to-test-fuzzy-search"></a>Fuzzy search tesztelése

Az egyszerű teszteléshez javasoljuk, hogy [a Kereséskezelő](search-explorer.md) vagy a [Postman](search-get-started-postman.md) egy lekérdezési kifejezésen keresztül idítsa. Mindkét eszköz interaktív, ami azt jelenti, hogy gyorsan végigléphet egy kifejezés több változatán, és kiértékelheti a visszaadott válaszokat.

Ha az eredmények nem egyértelműek, a [leütéskiemelésesegíthet](search-pagination-page-layout.md#hit-highlighting) az egyezés azonosításában a válaszban. 

> [!Note]
> A találatkiemelés használata az intelligens egyezések azonosítására korlátozott, és csak az alapvető fuzzy kereséshez működik. Ha az index pontozási profillal rendelkezik, vagy ha a lekérdezést további szintaxissal rétegezi, előfordulhat, hogy a leütés kiemelése nem tudja azonosítani az egyezést. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>1. példa: fuzzy keresés a pontos kifejezéssel

Tegyük fel, hogy `"Description"` a következő karakterlánc létezik a keresési dokumentum egy mezőjében:`"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Kezdje egy fuzzy kereséssel a "különleges" kifejezéssel, és adja hozzá a leíró leemelést a Leírás mezőhöz:

    search=special~&highlight=Description

A válaszban, mivel leadott leemelést adott hozzá, a formázás a "speciális" kifejezésre lesz alkalmazva.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Próbálja meg újra a kérelmet, elírás "különleges" azáltal, hogy ki több betű ("pe"):

    search=scial~&highlight=Description

Eddig nem változott a válasz. Az alapértelmezett 2 fokos távolság, eltávolítása két karakter "pe" a "különleges" továbbra is lehetővé teszi a sikeres mérkőzés az adott kifejezés.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Még egy kérés, tovább módosíthatja a keresési kifejezést egy utolsó karakter kivéve, összesen három törlés (a "különleges" a "scal"):

    search=scal~&highlight=Description

Figyelje meg, hogy ugyanazt a választ adja vissza, de most ahelyett, hogy a "speciális", a fuzzy egyezés az "SQL".

            "@search.score": 0.4232868,
            "@search.highlights": {
                "Description": [
                    "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
                ]

Ennek a kibővített példának az a lényege, hogy bemutassa, milyen egyértelmű, hogy a találatkiemelés kétértelmű eredményeket hozhat. A rendszer minden esetben ugyanazt a dokumentumot adja vissza. Ha a dokumentumazonosítókra támaszkodott volna az egyezés ellenőrzéséhez, akkor lehet, hogy kihagyta volna a "különleges" és az "SQL" közötti váltást.

## <a name="see-also"></a>Lásd még

+ [A teljes szöveges keresés működése az Azure Cognitive Search szolgáltatásban (lekérdezéselemzési architektúra)](search-lucene-query-architecture.md)
+ [Kereséskezelő](search-explorer.md)
+ [Lekérdezés a .NET-ben](search-query-dotnet.md)
+ [Lekérdezés a REST-ben](search-create-index-rest-api.md)
