---
title: Beszéd szöveggel kapcsolatos gyakori kérdések
titleSuffix: Azure Cognitive Services
description: Válaszok a szöveges szolgáltatás beszédével kapcsolatos gyakori kérdésekre.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: 0fe5b1350fdcf53eeda11f6de430d8d02a24fdbf
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397759"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Beszéd szöveggel kapcsolatos gyakori kérdések

Ha nem talál választ a kérdéseire ebben a GYIK-ban, tekintse meg az [egyéb támogatási lehetőségeket](support.md).

## <a name="general"></a>Általános kérdések

**K: mi a különbség az alapmodell és a szöveges modell egyéni beszéde között?**

**A**: az alapmodell a Microsoft tulajdonában lévő adataival lett kiképezve, és már telepítve van a felhőben. Egyéni modell használatával úgy alakíthatja ki a modelleket, hogy jobban illeszkedjen egy adott környezethez, amely adott környezeti zajt vagy nyelvet tartalmaz. A gyári szintekhez, autókhoz vagy zajos utcákhoz alkalmazkodó akusztikai modellre van szükség. A biológia, a fizika, a radiológia, a terméknév és az egyéni betűszók által használt témákhoz alkalmazkodó nyelvi modellre van szükség.

**K: hol kezdjek, ha alapmodellt szeretnék használni?**

**A**: először szerezze be az [előfizetési kulcsot](overview.md#try-the-speech-service-for-free). Ha szeretne REST-hívásokat kezdeményezni az előtelepített alapmodellekhez, tekintse meg a [REST API-kat](rest-apis.md). Ha WebSockets-t szeretne használni, [töltse le az SDK](speech-sdk.md)-t.

**K: mindig létre kell hozni egy egyéni beszédfelismerési modellt?**

**A**: nem. Ha az alkalmazás általános, napról napra kiterjedő nyelvet használ, nem kell testreszabnia a modellt. Ha az alkalmazás olyan környezetben van használatban, ahol kevés vagy nincs háttérzaj, nem kell testreszabnia a modellt.

Üzembe helyezhet alapkonfigurációt és testreszabott modelleket a portálon, majd elvégezheti a pontossági tesztek futtatását. Ezzel a funkcióval mérhető az alapmodell pontossága, és egy egyéni modell is megadható.

**K: Honnan tudom, hogy az adatkészlet vagy a modell feldolgozása befejeződött?**

**A**: jelenleg a modell vagy adatkészlet állapota a táblában az egyetlen lehetőség, hogy megismerje. Ha a feldolgozás befejeződött, az állapot **sikeres**lesz.

**K: Létrehozhatok több modellt is?**

**A**: a gyűjteményben megadható modellek száma nincs korlátozva.

**K: észrevettem, hogy hiba történt. Hogyan megszakítja az adatimportálást vagy a modell létrehozásának folyamatát?**

**A**: jelenleg nem állítható vissza akusztikai vagy nyelvi adaptációs folyamat. Az importált adattípusokat és modelleket törölheti, ha azok egy terminál-állapotban vannak.

**K: mi a különbség a keresési és a diktálási modell és a társalgási modell között?**

Válasz **: több**alapmodell közül választhat a Speech Service-ben. A társalgási modell hasznos lehet a beszélgetési stílusban beszélt beszéd felismeréséhez. Ez a modell ideális a telefonhívások átírásához. A keresési és a diktálási modell ideális a hangvezérelt alkalmazások számára. Az univerzális modell egy új modell, amelynek célja mindkét forgatókönyv kezelése. Az univerzális modell jelenleg a legtöbb területi szinten a társalgási modell minőségi szintjén van vagy magasabb.

**K: frissíthetem a meglévő modellt (a modell halmozását)?**

**A: a**meglévő modellek nem frissíthetők. Megoldásként egyesítse a régi adatkészletet az új adatkészlettel, és módosítsa újra.

A régi adatkészletet és az új adatkészletet egyetlen. zip fájlban (akusztikai adatokat) vagy egy. txt fájlban (nyelvi adatokat) kell egyesíteni. Az adaptáció befejezése után az új, frissített modellt újra kell telepíteni új végpont beszerzéséhez.

**K: ha elérhető egy alapkonfiguráció új verziója, az üzembe helyezésem automatikusan frissül?**

**A: az üzemelő**példányok nem frissülnek automatikusan.

Ha kiigazította és telepítette az alapkonfiguráció 1.0-s verziójával rendelkező modellt, akkor az üzemelő példány a következőképpen marad. Az ügyfelek leszerelik az üzembe helyezett modellt, újra alkalmazkodnak az alapkonfiguráció újabb verziójához, és újból üzembe helyezik azokat.

**K: le tudom tölteni a modellt, és helyileg futtatom?**

**A: a**modellek nem tölthetők le és nem hajthatók végre helyileg.

**K: a rendszer naplózza a kérelmeket?**

**A**: alapértelmezés szerint a kérelmek nincsenek naplózva (sem hang, sem átírás). Ha szükséges, kiválaszthatja a *naplózási tartalmat ebből a végpontból* beállítást, ha [Egyéni végpontot hoz létre](how-to-custom-speech-deploy-model.md) a nyomkövetés engedélyezéséhez. Ezt követően a rendszer a biztonságos tárolóban naplózza a kérelmeket az Azure-ban.

**K: a kérelmek szabályozva vannak?**

**A**: lásd a [Speech Services kvótáit és korlátozásait](speech-services-quotas-and-limits.md).

**K: hogyan kell fizetnem a kettős csatornás hangért?**

Válasz **: Ha**az egyes csatornákat külön küldi el (mindegyik csatornát a saját fájljában), a rendszer az egyes fájlok időtartamára díjat számít fel. Ha egyetlen fájlt küld el minden egyes csatornával együtt, akkor az adott fájl időtartamára kell fizetnie. A díjszabással kapcsolatos részletekért tekintse meg az [Azure Cognitive Services díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Ha további adatvédelmi kérdései vannak, amelyek tiltják a Custom Speech Service használatát, lépjen kapcsolatba az egyik támogatási csatornával.

## <a name="increasing-concurrency"></a>Párhuzamosság növelése
Lásd: [a Speech Services kvótái és korlátai](speech-services-quotas-and-limits.md).


## <a name="importing-data"></a>Adatok importálása

**K: mekkora a korlátja egy adatkészlet méretének, és miért korlátozza a korlátot?**

**A**: a korlátot a http-feltöltéshez használt fájl méretének korlátozása okozza. Lásd a tényleges korlátot a [Speech Services kvótái és korlátai](speech-services-quotas-and-limits.md) című részben.

**K: használhatom a szövegfájlom zip-fájljait, hogy feltölthető legyen egy nagyobb szövegfájl?**

**A**: nem. Jelenleg csak a nem tömörített szövegfájlok engedélyezettek.

**K: az Adatjelentés szerint a hosszúságú kimondott szöveg sikertelen volt. Mi a probléma?**

**A**: nem sikerült feltölteni a hosszúságú kimondott szöveg 100 százalékát egy fájlban, nem probléma. Ha a hosszúságú kimondott szöveg túlnyomó többsége akusztikai vagy nyelvi adatkészletekben (például több mint 95 százalék) sikeresen importálva lett, az adatkészlet használható. Azt azonban javasoljuk, hogy próbálja meg megérteni, miért nem sikerült a hosszúságú kimondott szöveg és elhárítani a problémákat. A leggyakoribb problémák (például a formázási hibák) könnyen orvosolhatók.

## <a name="creating-an-acoustic-model"></a>Akusztikai modell létrehozása

**K: mennyi akusztikai adatmennyiségre van szükségem?**

**A**: javasoljuk, hogy 30 perc és egy órányi akusztikai érték között kezdjen.

**K: milyen adatokat kell gyűjteni?**

**A**: gyűjti az alkalmazási forgatókönyvnek leginkább megfelelő adatokat, és a lehető leghamarabb használja a használati esetet. Az adatgyűjtésnek meg kell egyeznie a célalkalmazás és a felhasználók számára az eszközök, a környezetek és a hangszórók típusai alapján. Általánosságban elmondható, hogy a lehető legszélesebb körben gyűjtsön adatokat a különböző hangszórókból.

**K: Hogyan gyűjthetek akusztikus adatokat?**

**A**: létrehozhat egy önálló adatgyűjtési alkalmazást, vagy használhatja a polcon kívüli hangrögzítési szoftvert is. Létrehozhatja az alkalmazás egy olyan verzióját is, amely naplózza a hangadatokat, majd az adatok használatával.

**K: szeretném magam átírni az adaptációs adatszolgáltatásokat?**

Válasz **: igen**! Saját maga is lemásolhatja, vagy használhat Professional transzkripciós szolgáltatást. Egyes felhasználók előnyben részesítettek a professzionális átiratokkal, mások pedig a közösségi használják, vagy magukra az átírásokra.

## <a name="accuracy-testing"></a>Pontosság tesztelése

**K: használhatom az egyéni akusztikai modellem offline tesztelését egyéni nyelvi modell használatával?**

Válasz **: igen**, egyszerűen válassza ki az egyéni nyelvi modellt a legördülő menüben az offline teszt beállításakor.

**K: használhatom az egyéni nyelvi modell offline tesztelését egyéni akusztikai modell használatával?**

Válasz **: igen**, egyszerűen válassza ki az egyéni akusztikai modellt a legördülő menüben az offline teszt beállításakor.

**K: mi a Word Error Rate (WER) és hogyan számítható ki?**

**A**: a wer a beszédfelismerés értékelési mérőszáma. A WER a hibák teljes száma, amely tartalmazza a beszúrásokat, a törléseket és a behelyettesítéseket, a hivatkozás átírásakor a szavak teljes száma szerint elosztva. További információ: [Word Error Rate](https://en.wikipedia.org/wiki/Word_error_rate).

**K: Hogyan megállapítani, hogy jók-e a pontossági tesztek eredményei?**

**A**: az eredmények az alapmodell és a testre szabott modell összehasonlítását mutatják. Érdemes megverni az alapmodellt, hogy a testreszabási lehetőségek is hasznosak legyenek.

**K: Hogyan meghatározhatja az alapmodell WERjét, hogy megtudjam, van-e javulás?**

**A**: az offline teszt eredményei az egyéni modell alapkonfigurációjának pontosságát és az alapkonfigurációt mutatják be.

## <a name="creating-a-language-model"></a>Nyelvi modell létrehozása

**K: mennyi szöveges adatokra van szükség a feltöltéshez?**

Válasz **: attól**függ, hogy az alkalmazásban használt szókincs és kifejezések milyen eltérőek a kezdő nyelvi modelltől. Minden új szó esetében hasznos lehet a szavak használatának lehető legtöbb példáját megadnia. Az alkalmazásban használt általános kifejezések, beleértve a nyelvi adatkifejezéseket is, hasznosak lehetnek, mivel azt jelzi, hogy a rendszer a jelen feltételeket is figyeli. Gyakori, hogy legalább 100, és általában több száz vagy több hosszúságú kimondott szöveg van a nyelvi adatkészletben. Továbbá, ha a lekérdezések bizonyos típusai gyakoribbak, mint mások, a közös lekérdezések több példányát is beillesztheti az adatkészletbe.

**K: csak egy szólistát tölthetek fel?**

**A**: a szavak listájának feltöltése felveszi a szavakat a szókincsbe, de nem fogja tanítani a rendszerre, hogy a szavakat általában hogyan használják. Ha teljes vagy részleges hosszúságú kimondott szöveg (mondatokat vagy kifejezéseket) biztosít a felhasználók számára, akkor a nyelvi modell megismerheti az új szavakat és azok használatát. Az egyéni nyelvi modell nem csupán az új szavak a rendszerhez való hozzáadására, hanem az alkalmazáshoz tartozó ismert szavak valószínűségének módosítására is jó. A teljes hosszúságú kimondott szöveg biztosítása segít a rendszeren a jobb megismerésben.

## <a name="tenant-model-custom-speech-with-microsoft-365-data"></a>Bérlői modell (Custom Speech Microsoft 365 adattal)

**K: milyen információk szerepelnek a bérlői modellben, és hogyan jön létre?**

**A:** A bérlői modell olyan [nyilvános csoportos](https://support.microsoft.com/office/learn-about-microsoft-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) e-mailek és dokumentumok használatával készült, amelyeket bárki láthat a szervezetében.

**K: milyen beszédfelismerési élményt fejlesztenek a bérlői modell?**

**A:** Ha a bérlői modell engedélyezése, létrehozása és közzététele megtörténik, a rendszer a beszédfelismerési szolgáltatással létrehozott vállalati alkalmazások felismerését javítja. Ez egy felhasználói Azure AD-tokent is továbbít, amely a vállalati tagságot jelzi.

A Microsoft 365 beépített beszédfelismerési élmények, például a diktálás és a PowerPoint-feliratok nem változnak, amikor létrehoz egy bérlői modellt a Speech Service-alkalmazásokhoz.

## <a name="next-steps"></a>További lépések

- [Hibaelhárítás](troubleshooting.md)
- [Kibocsátási megjegyzések](releasenotes.md)
