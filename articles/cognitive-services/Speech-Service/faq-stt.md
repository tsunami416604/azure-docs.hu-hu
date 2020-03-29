---
title: Beszéd szöveghez gyakran ismételt kérdések
titleSuffix: Azure Cognitive Services
description: Válaszok a Beszéd szövegként szolgáltatással kapcsolatos gyakori kérdésekre.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/4/2019
ms.author: panosper
ms.openlocfilehash: a279aebdd19ebd3a41ddad0c1c279937e00838c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168464"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Beszéd szöveghez gyakran ismételt kérdések

Ha ebben a gyIK-ben nem talál választ kérdéseire, tekintse meg [az egyéb támogatási lehetőségeket.](support.md)

## <a name="general"></a>Általános kérdések

**K: Mi a különbség az alapmodell és az egyéni beszédfelismerési modell között?**

**A**: Az alapmodell a Microsoft tulajdonában lévő adatok használatával lett betanítva, és már telepítve van a felhőben. Az egyéni modell segítségével úgy igazíthatja a modellt, hogy jobban illeszkedjen egy adott környezethez, amely adott környezeti zajt vagy nyelvet biztosít. Gyári padlók, autók, vagy zajos utcák lenne szükség egy adaptált akusztikus modell. Az olyan témák, mint a biológia, a fizika, a radiológia, a terméknevek és az egyéni rövidítések adaptált nyelvi modellt igényelnek.

**K: Hol kezdjem, ha alapmodellt szeretnék használni?**

**A**: Először kap egy [előfizetési kulcsot](get-started.md). Ha REST-hívásokat szeretne kezdeményezni az előre telepített alapmodellekhez, olvassa el a [REST API-kcímű témakört.](rest-apis.md) Ha websocketeket szeretne használni, [töltse le az SDK-t.](speech-sdk.md)

**K: Mindig egyéni beszédmodellt kell létrehoznom?**

**A**: Nem. Ha az alkalmazás általános, napi nyelvet használ, nem kell testreszabnia a modellt. Ha az alkalmazást olyan környezetben használja, ahol kevés vagy nincs háttérzaj, nem kell testreszabni a modellt.

Az alapkonfigurációt és a testreszabott modelleket üzembe helyezheti a portálon, majd futtathatja a pontossági teszteket. Ezzel a funkcióval mérheti az alapmodell és az egyéni modell pontosságát.

**K: Honnan tudom, hogy az adatkészlet vagy modell feldolgozása befejeződött?**

**A**: Jelenleg a modell vagy adatkészlet állapota a táblázatban az egyetlen módja annak, hogy tudja. Ha a feldolgozás befejeződött, az állapot **sikeres.**

**K: Létrehozhatok egynél több modellt?**

**A**: Nincs korlátozva a modellek száma lehet a gyűjteményben.

**K: Rájöttem, hogy hibáztam. Hogyan szakíthatom meg a folyamatban lévő adatimportálást vagy modelllétrehozást?**

**A**: Jelenleg nem lehet visszavonni egy akusztikai vagy nyelvi adaptációs folyamatot. Az importált adatokat és modelleket törölheti, ha terminálállapotban vannak.

**K: Mi a különbség a Keresés és diktálás modell és a Társalgási modell között?**

**A**: A beszédfelismerési szolgáltatásban több alapmodell közül is választhat. A társalgási modell hasznos a társalgási stílusban beszélt beszéd felismerésére. Ez a modell ideális telefonhívások átírására. A keresés és diktálás modell ideális hang-aktivált alkalmazások. Az univerzális modell egy új modell, amelynek célja, hogy mindkét forgatókönyv. Az univerzális modell jelenleg a társalgási modell minőségi szintjén vagy felett van a legtöbb területi beállításban.

**K: Frissíthetem a meglévő modellemet (modellhalmozás)?**

**A**: Meglévő modell nem frissíthető. Megoldásként kombinálja a régi adatkészletet az új adatkészlettel, és alakítsa át újra.

A régi adatkészletet és az új adatkészletet egyetlen .zip fájlban (akusztikai adatok esetén) vagy .txt fájlban (nyelvi adatok esetén) kell egyesíteni. Az adaptáció befejezése után az új, frissített modellt újra kell telepíteni egy új végpont

**K: Ha egy alapterv új verziója érhető el, a központi telepítés automatikusan frissül?**

**A**: A központi telepítések NEM frissülnek automatikusan.

Ha adaptált és üzembe helyezett egy modellt az alapkonfiguráció v1.0-val, az üzembe helyezés változatlan marad. Az ügyfelek leszerelhetik az üzembe helyezett modellt, újraalkalmazkodhatnak az alapterv újabb verziójával, és újratelepíthetik.

**K: Letölthetem a modellt, és helyileg futtathatom?**

**A**: A modellek nem tölthetők le és nem hajthatók végre helyileg.

**K: Naplózzák a kéréseimet?**

**A**: A központi telepítés létrehozásakor választhat a nyomkövetés kikapcsolásához. Ezen a ponton nem lesz hangvagy átirat. Ellenkező esetben a kérelmek általában biztonságos tárolóban vannak bejelentkezve az Azure-ban.

**K: A kéréseim szabályozottak?**

**A**: A REST API 5 másodpercenként 25-re korlátozza a kérelmeket. Részletek találhatók oldalainkon [a beszéd a szöveg](speech-to-text.md).

**K: Hogyan kell fizetni a kétcsatornás hangért?**

**V:** Ha minden csatornát külön küld be (minden csatorna a saját fájljában), akkor az egyes fájlok időtartamára díjat számítunk fel. Ha egyetlen fájlt küld be, és minden csatorna együtt multiplexel, akkor az egyetlen fájl időtartamára díjat számítunk fel. Az árakról az Azure [Cognitive Services díjszabási oldalán](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)olvashat.

> [!IMPORTANT]
> Ha további adatvédelmi aggályai vannak, amelyek tiltják az egyéni beszédszolgáltatás használatát, lépjen kapcsolatba valamelyik támogatási csatornával.

## <a name="increasing-concurrency"></a>Növekvő egyidejűség

**K: Mi a teendő, ha az üzembe helyezett modellhez magasabb egyidejűségre van szükségem, mint a milyen a portálon?**

**A**: A modell 20 egyidejű kérelem lépésekben skálázható.

A szükséges információkkal hozzon létre egy támogatási kérelmet az [Azure támogatási portálon.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) Ne tegye az információkat a [támogatási oldalon](support.md)említett nyilvános csatornákon (GitHub, Stackoverflow, ...).

Az ***egyéni modellek***egyidejűsítésének növeléséhez a következő információkra van szükségünk:

- Az a régió, ahol a modell telepítve van,
- az üzembe helyezett modell végpontazonosítója:
  - Megvan az [egyéni beszéd portál,](https://aka.ms/customspeech)
  - bejelentkezés (ha szükséges),
  - válassza ki a projektet és a telepítést,
  - válassza ki azt a végpontot, amelyhez az egyidejűség növelésére van szüksége,
  - másolja `Endpoint ID`a.

Az ***alapmodell***egyidejűsítésének növeléséhez a következő információkra van szükségünk:

- A szolgáltatás ának régiója,

és vagy

- hozzáférési jogkivonat az előfizetéshez (lásd [itt](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#how-to-get-an-access-token)),

vagy

- az előfizetés erőforrás-azonosítója:
  - Nyissa meg az [Azure portalt](https://portal.azure.com),
  - kijelölés `Cognitive Services` a keresőmezőben,
  - a megjelenített szolgáltatások közül válassza ki azt a beszédszolgáltatást, amelyhez növelni szeretné az egyidejűséget,
  - jelenik `Properties` meg a szolgáltatáshoz,
  - másolja a `Resource ID`teljes .

## <a name="importing-data"></a>Adatok importálása

**K: Mi az adatkészlet méretének korlátozása, és miért a korlát?**

**A**: Az adatkészlet aktuális korlátja 2 GB. A korlát oka, hogy a korlátozás a méret egy fájl HTTP-feltöltés.

**K: Zip-elhetem a szövegfájlokat, hogy nagyobb szövegfájlt tölthessek fel?**

**A**: Nem. Jelenleg csak tömörítetlen szövegfájlok engedélyezettek.

**K: Az adatjelentés azt mondja, hogy sikertelen kimondott szövegek voltak. Mi a probléma?**

**V:** Nem sikerült feltölteni a kimondott szöveg 100 százalékát egy fájlban, nem jelent problémát. Ha az akusztikai vagy nyelvi adatkészletek kimondott szövegének túlnyomó többsége (például több mint 95 százalék) sikeresen importálva van, az adatkészlet használható lehet. Azonban azt javasoljuk, hogy próbálja megérteni, hogy miért a kimondott szöveg sikertelen, és a problémák megoldásához. A leggyakoribb problémákat, például a formázási hibákat könnyű kijavítani.

## <a name="creating-an-acoustic-model"></a>Akusztikus modell létrehozása

**K: Mennyi akusztikai adatra van szükségem?**

**A**: Javasoljuk, hogy 30 perc és egy óra közötti akusztikai adatokkal kezdje.

**K: Milyen adatokat kell gyűjtenem?**

**A**: Olyan adatokat gyűjthet, amelyek a lehető legközelebb állnak az alkalmazásforgatókönyvhöz, és a lehető legjobb anameddigek. Az adatgyűjtésnek meg kell egyeznie a célalkalmazással és a felhasználókkal az eszközök vagy eszközök, a környezetek és a hangszórók típusai tekintetében. Általánosságban elmondható, hogy a lehető legszélesebb körű hangszórókból kell adatokat gyűjteni.

**K: Hogyan gyűjthetek akusztikus adatokat?**

**A**: Létrehozhat egy önálló adatgyűjtési alkalmazást, vagy használhat használatra kész hangrögzítő szoftvert. Létrehozhatja az alkalmazás egy verzióját is, amely naplózza a hangadatokat, majd felhasználja az adatokat.

**K: Magam nak kell átírnom az adaptációs adatokat?**

**A**: Igen! Átírhatja saját maga, vagy használhat professzionális átírási szolgáltatást. Némely használók jobban szeret profi transcribers és másikak használ crowdsourcing vagy csinál a átiratok maguk.

## <a name="accuracy-testing"></a>Pontossági vizsgálat

**K: Elvégezhetem az egyéni akusztikai modell offline tesztelését egyéni nyelvi modell használatával?**

**A**: Igen, csak válassza ki az egyéni nyelvi modellt a legördülő menüben, amikor beállítja az offline tesztet.

**K: Végrehajthatom az egyéni nyelvi modell offline tesztelését egyéni akusztikai modell használatával?**

**A**: Igen, csak válassza ki az egyéni akusztikai modellt a legördülő menüben, amikor beállítja az offline tesztet.

**K: Mi a szóhibaarány (WER), és hogyan számítják ki?**

**A**: Wer a beszédfelismerés értékelési mérőszáma. A WER a hibák teljes száma, amely magában foglalja a beszúrásokat, törléseket és helyettesítéseket, osztva a referencia-átírásban lévő szavak teljes számával. További információ: [word error rate](https://en.wikipedia.org/wiki/Word_error_rate).

**K: Hogyan állapítható meg, hogy a pontossági teszt eredményei jók-e?**

**A**: Az eredmények az alapmodell és a testreszabott modell összehasonlítását mutatják. Meg kell törekednie, hogy megverte az alapmodell, hogy testreszabási érdemes.

**K: Hogyan állapíthatom meg az alapmodell WER-ét, hogy lássam, volt-e javulás?**

**A**: Az offline teszteredmények az egyéni modell alapkonfigurációs pontosságát és a kiindulási értékhez képest imitáltak.

## <a name="creating-a-language-model"></a>Nyelvi modell létrehozása

**K: Mennyi szöveges adatot kell feltöltenem?**

**A**: Attól függ, hogy mennyire különböznek az alkalmazásban használt szókincs és kifejezések a kezdőnyelvi modellektől. Minden új szó esetében hasznos, ha a lehető legtöbb példát adja meg ezeknek a szavaknak a használatára. Az alkalmazásban használt gyakori kifejezések, beleértve a nyelvi adatokban lévő kifejezéseket is, azért is hasznos, mert azt mondja a rendszernek, hogy figyelje ezeket a kifejezéseket is. Gyakori, hogy legalább 100, és általában több száz vagy több utterances a nyelvi adatkészletben. Ha bizonyos típusú lekérdezések várhatóan gyakoribbak, mint mások, a gyakori lekérdezések több példányát is beillesztheti az adatkészletbe.

**K: Feltölthetek egy szólistát?**

**A**: A szavak listájának feltöltése hozzáadja a szavakat a szókincshez, de nem tanítja meg a rendszert a szavak jellemzően használt módjára. Azáltal, hogy teljes vagy részleges kimondott szöveg (mondatok vagy kifejezések a dolgok, hogy a felhasználók valószínűleg mondani), a nyelvi modell megismerhetik az új szavakat, és hogyan használják őket. Az egyéni nyelvi modell nem csak új szavak hozzáadásához alkalmas a rendszerhez, hanem az alkalmazás ismert szavainak valószínűségének beállításához is. Teljes utterances segít a rendszer jobban tanulnak.

## <a name="tenant-model-custom-speech-with-office-365-data"></a>Bérlői modell (Egyéni beszéd az Office 365-adatokkal)

**K: Milyen információk szerepelnek a bérlői modellben, és hogyan jönlétre?**

**A.** A bérlői modell [nyilvános csoportos](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) e-mailek és dokumentumok használatával készült, amelyeket a szervezet en bárki láthatja.

**K: Milyen beszédélmények javítják a bérlői modell?**

**A.** Ha a bérlői modell engedélyezve van, létre jön és közzé van téve, a rendszer a beszédfelismerési szolgáltatás használatával létrehozott vállalati alkalmazások felismerésének javítására használja; amely egy felhasználói AAD-tokent is átad, amely tagságot jelez a vállalatnak.

Az Office 365-be beépített beszédfelismerési élmények, például a diktálás és a PowerPoint feliratozása nem módosulnak, amikor bérlői modellt hoz létre a beszédfelismerési szolgáltatásalkalmazásaihoz.

## <a name="next-steps"></a>További lépések

- [hibaelhárítással](troubleshooting.md)
- [Kibocsátási megjegyzések](releasenotes.md)
