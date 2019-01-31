---
Cím: Adatok kiértékelése titleSuffix: Azure Machine Learning Studio description: Négy feltétel az adatokat meg kell felelnie ahhoz, hogy készen áll az adatelemzéshez. Ez a videó rendelkezik konkrét példák nyújtanak segítséget az alapszintű adatok kiértékelése.
szolgáltatások: gépi tanulási ms.service: gépi tanulási ms.subservice: studio ms.topic: cikk

Szerző: garyericson ms.author: garye ms.custom: seodec18 ms.date: 01/03/2018
---
# <a name="is-your-data-ready-for-data-science"></a>Készen állnak adatai az elemzésre?
## <a name="video-2-data-science-for-beginners-series"></a>2. Videó: Adatelemzés kezdőknek sorozat
Útmutató: az adatok, hogy megfelel-e, készen áll az adatelemzés alapvető feltételek.

A lehető leghatékonyabban a sorozat, tekintse meg az összes. [Nyissa meg a videók listájában](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Ez a sorozat egyéb videók
*Adatelemzés kezdőknek* egy gyors bevezetőt az öt rövid videóban a rendszer.

* 1. Videó: [A 5 kérdés adatelemzés választ ad](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 perc 14 mp)*
* 2. Videó: Készen állnak adatai az elemzésre?
* 3. Videó: [Tegyen fel kérdést az adatok a válasz](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 perc 17 másodperc)*
* 4. Videó: [Egy egyszerű modellel válasz előrejelzése](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 perc 42 másodperc)*
* 5. Videó: [Más emberek munkájának lemásolása az adatelemzéshez való másolása](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 másodperc)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>A szövegben: Készen állnak adatai az elemzésre?
Üdvözli az "Az adatok készen áll a data science?" az a sorozat második videó *adatelemzés kezdőknek*.  

Előtt adatelemzési adhat meg a kívánt válaszokat, akkor adjon meg néhány kiváló minőségű nyersanyagok dolgozhat. Csakúgy, mint egy kétpizzás, annál jobbak az összetevők, először a, annál jobb végtermékben elvégzése. 

## <a name="criteria-for-data"></a>Adatok feltételeit
Az adatelemzés vannak bizonyos összetevők, például együtt kell lekérni:

* Megfelelő
* Csatlakozva
* Pontos
* Elegendő használata

## <a name="is-your-data-relevant"></a>Fontos az adatok?
Ezért az első összetevő - kell nyújtaniuk, amely az adatokat.

![A vonatkozó adatokat, és irreleváns adatok - adatok kiértékelése](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

A bal oldalon a tábla egy Boston sáv, az utolsó játék Red Sox batting átlagos és az ár a legközelebbi kényelmi tárolójában tej kívül tesztelt hét személyek vér alkohol szintjét mutatja be.

Ez az összes tökéletesen jogos adatokat. Ez azt csak tartalék, hogy nem megfelelő. Ezek a számok között sem nyilvánvaló kapcsolat áll fenn. Ha valaki kaptunk tej és a piros Sox batting átlagos aktuális ára, nincs sikerült kitalálni a vér alkohol tartalmak lehetőség.

Most nézzük meg a tábla a jobb oldalon. Mindenki, aki a szervezet ezúttal háttértár és ital már volt száma mérték.  Az egyes sorokban szereplő számok most kapcsolódnak egymáshoz. Ha e kaptunk saját szervezet háttértár és a már korábban Margaritas számát, sikerült választja ki a saját vér cikkekből alkohol tartalom.

## <a name="do-you-have-connected-data"></a>Tegye csatlakoztatta adatokat?
A következő összetevője csatlakoztatott adatok.

![Csatlakoztatott vagy leválasztott adat - adatok feltételeinek, készen áll az adatok](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Íme néhány fontos adatokat hamburgers minőségét: rács hőmérséklet patty súly és a helyi élelmiszer újság minősítése. Azonban megfigyelheti a bal oldalon található a táblázatban a hiányosságok pótlásában.

A legtöbb adatkészletek néhány érték hiányzik. Gyakori, hogy ehhez hasonló lyuk, és többféleképpen őket. De ha túl sok hiányzik, az adatok a következő módon Svájc sajtok iránti szenvedélyének kezdődik.

A tábla a bal oldali tekinti meg, ha nincs, hogy mennyi a hiányzó adatokat, legyen nehezen rács hőmérséklet és patty súly között kapja meg bármilyen típusú kapcsolat. Ez a példa bemutatja a leválasztott adatokat.

A jobb oldali tábla, megtelt, és kész - csatlakoztatott adatok egy példát.

## <a name="is-your-data-accurate"></a>Az adatok pontos van?
A következő összetevője pontosságát. Íme négy célok érni.

![Hibás adatokat – adatok feltételek és pontos adatok](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Tekintse meg a cél a jobb felső sarokban. Nincs megfelelő bikák szem köré szoros csoportosítása. Természetesen ez pontos. Furcsa viselkedése a nyelvet, az adatelemzés, az alatta a cél jobb teljesítményt is figyelembe veszi pontos.

Ha Ön hozzá van rendelve, ezek a nyilak közepén, jelennének meg, hogy nagyon közelében van bikák szemmel. A nyilak helyezkednek el minden körül a cél, így azokat a rendszer nem pontos, de azok még eltérése a bikák szem, így azokat a rendszer pontos.

Most nézzük meg a bal felső cél. Itt a nyilak találati nagyon egymáshoz, szoros csoportosítást. Pontos zajlik, de pontatlan, mert a center módja a bikák szem ki. A nyilak a bal alsó célzott pontos és nem pontos is. Ez archer további eljárás kell.

## <a name="do-you-have-enough-data-to-work-with"></a>Elég adatokra van?
Végül összetevő #4 elegendő adat áll rendelkezésre.

![Elég adat elemzéshez van? Az adatok kiértékelése](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Úgy gondolja, hogy az egyes adatpontok, hogy az egy festmény egy ecsetvonás a táblában. Ha csak néhány őket, lehet, hogy a rajzolást intelligens – nehéz mondja el, Mi ez.

Néhány további ecset körvonalak ad hozzá, ha a rajzolást elindít egy kicsit élesebb beolvasásához.

Ha Jéghegy elegendő körvonalak, csak láthatja elegendő az egyes átfogó döntéseket hozhat. Ennyi az egész valahol szeretném előfordulhat, hogy látogassa meg? Világos úgy tűnik, tűnik tiszta víz – Igen, hol fogom szabadságon is.

További adatok hozzáadása a kép világosabb lesz, és döntéseket hozhat részletesebb. Most, tekintse meg a bal oldali Bank három hotels. Egy, az előtérben architekturális jellemzője is láthatja. Választhatja azt is, miatt a nézet a harmadik emelet maradjon.

A megfelelő, a csatlakoztatott, a pontos adatokat, és ahhoz, hogy rendelkezik minden összetevője szükséges néhány kiváló minőségű adatelemzés.

Ügyeljen arra, hogy tekintse meg a további négy videókat *adatelemzés kezdőknek* a Microsoft Azure gépi tanulás.

## <a name="next-steps"></a>További lépések
* [Egy első adatelemzési kísérlet a Machine Learning Studio kipróbálása](create-experiment.md)
* [Bevezetés a gépi tanulás a Microsoft Azure](what-is-machine-learning.md)
