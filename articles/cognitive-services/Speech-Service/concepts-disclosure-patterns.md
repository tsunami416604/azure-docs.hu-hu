---
title: Közzétételi tervezési minták
titleSuffix: Azure Cognitive Services
description: Tervezési minták és bevált gyakorlatok a közzétételhez.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: 3e7d8ee2b156a30b11cda79798a8af8a8ecf4f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776622"
---
# <a name="disclosure-design-patterns"></a>Közzététel-tervezési minták
Most, hogy&#39;meg határozta meg a megfelelő [szintű nyilvánosságra hozatala](concepts-disclosure-guidelines.md#disclosure-assessment) a szintetikus hang tapasztalat, ez&#39;egy jó ideje, hogy vizsgálja meg a lehetséges tervezési mintákat.
## <a name="overview"></a>Áttekintés
Van egy spektruma közzétételi tervezési minták at lehet alkalmazni, hogy a szintetikus hang élményt. Ha a közzétételi értékelés eredménye "Nagy nyilvánosságot kapott", javasoljuk a [**kifejezett nyilvánosságra hozatalt**](#explicit-disclosure), ami azt jelenti, hogy a szintetikus hang eredetét nyíltan közöljük. [**Az implicit közzététel**](#implicit-disclosure) olyan jelzéseket és interakciós mintákat tartalmaz, amelyek a hangélmények számára előnyösek, függetlenül attól, hogy a szükséges közzétételi szintek magasak vagy alacsonyak-e.
![A közzétételi minták spektruma](media/responsible-ai/disclosure-patterns/affordances.png)






| Explicit közzétételi minták                                                                                                                                                                                    | Implicit közzétételi minták                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Átlátszó bevezetés](#transparent-introduction)<br> [Verbális átlátszó bevezetés](#verbal-transparent-introduction)<br>  [Explicit szövegsor](#explicit-byline)<br>  [Testreszabás és kalibrálás](#customization-and-calibration)<br> [Szülői közzététel](#parental-disclosure)<br> [Lehetőségek biztosítása arra, hogy többet megtudjon arról, hogyan készült a hang](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [A képességek nyilvánosságra hozatala](#capability-disclosure)<br>[Implicit dákók és visszajelzések](#implicit-cues--feedback)<br> [Társalgási átláthatóság](#conversational-transparency) |



Az alábbi táblázat segítségével közvetlenül a szintetikus hangra vonatkozó mintákra hivatkozhat. A diagram egyéb feltételei is vonatkozhatnak a forgatókönyvre:<br/>



| Ha a szintetikus hangélmény... | Javaslatok | Tervezési minták |
| --- | --- | --- |
| Nagy közzétételt igényel  | Legalább egy explicit minta és implicit dákó előre, hogy a felhasználók társítások létrehozásában. |[Explicit közzététel](#explicit-disclosure)<br>[Implicit közzététel](#implicit-disclosure)  |
| Alacsony közzétételt igényel | A közzététel lehet minimális vagy szükségtelen, de előnyös lehet néhány implicit minta. | [A képességek nyilvánosságra hozatala](#capability-disclosure)<br>[Társalgási átláthatóság](#conversational-transparency)  |
| Magas szintű elkötelezettség | Építsen hosszú távra, és több belépési pontot kínáljon a felhasználói út során. Erősen ajánlott, hogy egy bevezetési élményt. | [Átlátszó bevezetés](#transparent-introduction)<br>[Testreszabás és kalibrálás](#customization-and-calibration)<br>[A képességek nyilvánosságra hozatala](#capability-disclosure) |
| A gyermekeket foglalja magában elsődleges célközönségként | Megcélozni a szülőket, mint elsődleges közzétételi közönséget, és biztosítják, hogy hatékonyan kommunikálhassák a gyermekekkel a közzétételt.  | [Szülői közzététel](#parental-disclosure)<br>[Verbális átlátszó bevezetés](#verbal-transparent-introduction)<br> [Implicit közzététel](#implicit-disclosure)<br> [Társalgási átláthatóság](#conversational-transparency)  |
| Tartalmazza a vak felhasználókat vagy a gyengén látó kit, mint elsődleges célközönséget  | Legyen minden felhasználót bevonni, és győződjön meg arról, hogy a vizuális közzététel bármely formájához alternatív szöveg vagy hanghatások társulnak. Tartsa be a kisegítő lehetőségekre vonatkozó szabványokat a kontrasztarány és a kijelző méretéhez. Használja a hallási jelzéseket a közzététel közléséhez.  | [Verbális átlátszó bevezetés](#verbal-transparent-introduction) <br>[Hangjelzés](#implicit-cues--feedback)<br>[Haptic dákók](#implicit-cues--feedback)<br>[Társalgási átláthatóság](#conversational-transparency)<br>[Akadálymentesítési szabványok](https://www.microsoft.com/accessibility) |
| Képernyő nélküli, eszköz nélküli vagy hangként használja a hangot elsődleges vagy egyetlen interakciós módként | Használja a hallási jelzéseket a közzététel közléséhez. | [Verbális átlátszó bevezetés](#verbal-transparent-introduction) <br> [Hangjelzés](#implicit-cues--feedback)  |
| Potenciálisan több felhasználót/hallgatót foglal magában (pl. személyi asszisztens több háztartásban)  | Legyen tudatában a különböző felhasználói környezeteknek és megértési szinteknek, és több lehetőséget kínáljon a felhasználói út során a közzétételre.  | [Átlátszó bevezetés (return user)](#transparent-introduction)<br> [Lehetőségek biztosítása arra, hogy többet megtudjon arról, hogyan készült a hang](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Társalgási átláthatóság](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Kifejezett közzététel
Ha a szintetikus hang élménye nagy nyilvánosságra hozatalt igényel, a legjobb, ha az alábbi explicit minták közül legalább egyet használ a szintetikus természet egyértelműen megalapozásához.
### <a name="transparent-introduction"></a>Átlátszó bevezetés

A hangélmény megkezdése előtt mutassa be a digitális asszisztenst azáltal, hogy teljes mértékben átláthatóvá válik a hangja eredetét és képességeit illetően. A minta használatának optimális pillanata egy új felhasználó bevezetésekor vagy új funkciók bevezetésekor a visszatérő felhasználó számára. Implicit célzásokra a bevezetés során a felhasználók mentális modellt alkotnak a digitális ügynök szintetikus természetéről.

#### <a name="first-time-user-experience"></a>Első felhasználói élmény

![Átlátható bevezetés az első futtatás során](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*A szintetikus hang egy új felhasználó bevezetése közben kerül bevezetésre.*

Javaslatok
- Írja le, hogy a hang &quot;mesterséges&quot;(pl. digitális)
- Írja le, mire képes az ügynök
- Explicit módon adja meg a&#39;eredetének hangját
- Kínáljon belépési pontot, hogy többet tudjon meg a szintetikus hangról

#### <a name="returning-user-experience"></a>Visszatérő felhasználói élmény

Ha egy felhasználó kihagyja a bevezetési élményt, továbbra is kínáljon belépési pontokat az Átlátszó bevezetés élményhez, amíg a felhasználó először nem indítja el a hangot.
<br/>

![Átlátható bevezetés a visszaküldési felhasználói élmény során](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Adjon meg egy konzisztens belépési pontot a szintetikus hang élményéhez. Lehetővé teszi a felhasználó számára, hogy visszatérjen a bevezetési élményhez, amikor először indítja el a hangot a felhasználói út bármely pontján.*


### <a name="verbal-transparent-introduction"></a>Verbális átlátszó bevezetés

A digitális asszisztens&#39;hangjának eredetét tartalmazó szóbeli üzenet önmagában is elég egyértelmű ahhoz, hogy nyilvánosságra kerülésre kerül. Ez a minta a legjobb a magas közzétételi forgatókönyvek, ahol a hang az egyetlen mód a beavatkozás áll rendelkezésre.
<br/>

![Verbálisan beszélt átlátszó bevezetés](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Használjon átlátszó bevezetést, ha vannak olyan pillanatok a felhasználói élményben, amikor előfordulhat, hogy már bevezet vagy attribútumként egy személyt&#39;hangja.*


![Verbálisan beszélt átlátható bevezetése első személyben](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*A további átláthatóság érdekében a hangszereplő az első személyben közzéteheti a szintetikus hang eredetét.*

### <a name="explicit-byline"></a>Explicit szövegsor

Ezt a mintát akkor használja, ha a felhasználó egy hanglejátszóval vagy interaktív összetevővel fog interakcióba lépni a hang aktiválásához.


![Explicit byline egy hírmédia forgatókönyv](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Egy explicit byline a hozzárendelése, ahol a hang jött.*

Javaslatok

- Ajánlat belépési pont, hogy többet tudjon meg a szintetizált hang

### <a name="customization-and-calibration"></a>Testreszabás és kalibrálás

A felhasználók szabályozhatják, hogy a digitális asszisztens hogyan reagáljon rájuk (azaz hogyan szóljon a hang).  Amikor a felhasználó a saját feltételei szerint és meghatározott célokat szem előtt tartva lép kapcsolatba egy rendszerrel, akkor definíció szerint már megértette, hogy&#39;nem valós személy.

#### <a name="user-control"></a>Felhasználói vezérlő

Olyan lehetőségeket kínálhat, amelyek jelentős és észrevehető hatást gyakorolnak a szintetikus hangélményre.

![Felhasználói beállítások](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*A felhasználói beállítások lehetővé teszik a felhasználók számára, hogy testre szabják és javítsák felhasználói élményt.*

Javaslatok

- A felhasználók testre szabhatják a hangot (pl. nyelv és hangtípus kiválasztása)
- A felhasználók megtaníthatják a rendszernek, hogy reagáljon az ő egyedi hangjára (pl. hangkalibrálás, egyéni parancsok)
- Optimalizálás a felhasználó által generált vagy környezetfüggő interakciókhoz (pl. emlékeztetők)

#### <a name="persona-customization"></a>Személy testreszabása

Kínálnak a digitális asszisztens&#39;hangjának testreszabására. Ha a hang egy hírességen vagy egy széles körben felismerhető személyen alapul, fontolja meg a vizuális és szóbeli bemutatkozások használatát, amikor a felhasználók megtekinthetik a hangot.

![A hang testreszabása](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Felajánlás a képesség, hogy válasszon egy sor hangok segít közvetíteni a mesterséges természet.*

Javaslatok
- Lehetővé teszi a felhasználók számára, hogy megtekintsék az egyes hangok hangját
- Használjon hiteles bevezetőt minden hanghoz
- Ajánlat belépési pontokat, hogy többet tudjon meg a szintetizált hang

### <a name="parental-disclosure"></a>Szülői közzététel

A MELLETT, hogy megfelel a COPPA előírásoknak, adjon tájékoztatást a szülőknek, ha az elsődleges célközönség kisgyermekek és az expozíciós szint magas. Érzékeny célokra, fontolja meg gating a tapasztalat, amíg egy felnőtt elismerte a szintetikus hang használatát. Buzdítsd a szülőket, hogy közöljék az üzenetet gyermekeikkel.

![Közzététel a szülők számára](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*A szülők számára optimalizált átlátható bevezetés biztosítja, hogy a felnőtt et már azelőtt felkell ismerni a hang szintetikus jellegéről, mielőtt a gyermek kölcsönhatásba lépne vele.*

Javaslatok

- A szülők megcélzása a közzététel elsődleges célközönségeként
- Buzdítsd a szülőket, hogy közöljék a tájékoztatásról gyermekeikkel
- Ajánlat belépési pontokat, hogy többet tudjon meg a szintetizált hang
- A tapasztalatot azzal, &quot;hogy&quot; egy egyszerű védintézkedési kérdést tesz fel a szülőknek, hogy megmutassák, elolvasták a

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Lehetőségek biztosítása arra, hogy többet megtudjon arról, hogyan készült a hang

Környezetfüggő belépési pontokat kínálhat egy olyan oldalra, előugró ablakra vagy külső webhelyre, amely további információt nyújt a szintetikus hangtechnológiáról. Például megadhat egy hivatkozást, hogy többet tudjon meg a bevezetés során, vagy amikor a felhasználó további információkat kér a beszélgetés során.

![Belépési pont, hogy többet](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Példa egy belépési pontra, amely lehetőséget nyújt arra, hogy többet tudjon meg a szintetizált hangról.*

Amint a felhasználó több információt kér a szintetikus hangról, az elsődleges cél az, hogy oktassa őket a szintetikus hang eredetéről, és átlátható legyen a technológiával kapcsolatban.

![További információk biztosítása a felhasználóknak a szintetikus hangról](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*További információk egy külső webhely súgójában ajánlhatók fel.*

Javaslatok

- Egyszerűsítse az összetett fogalmakat, és kerülje a jogi és technikai zsargon használatát
- Ne temetje el ezt a tartalmat az adatvédelemés a használati feltételek nyilatkozataiban
- A tartalom tömören tartása és képek használata, ha rendelkezésre áll

## <a name="implicit-disclosure"></a>Implicit közzététel

A konzisztencia a kulcs a közzététel implicit módon eléréséhez a felhasználói út során. A vizuális és hallási jelzések konzisztens használata az eszközök és az interakciómód között segíthet az implicit minták és az explicit közzététel közötti társítások létrehozásában.

![Implicit jelzések konzisztenciája](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Implicit dákók & visszajelzések

Az antropomorfizmus különböző módokon nyilvánulhat meg, az ügynök tényleges vizuális ábrázolásától kezdve a hangig, a hangokig, a fénymintázatokig, a pattogó formákig, vagy akár egy eszköz rezgéséig. Meghatározásakor a persona, tőkeáttétel implicit célzásokat és visszajelzésminták helyett célja egy nagyon ember-szerű avatar. Ez az egyik módja annak, hogy minimálisra csökkentsék a konkrétabb közzététel szükségességét.

![Vizuális jelzések és visszajelzések](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Ezek a jelek segítenek antropomorfizálni az anyagot anélkül, hogy túl emberszerűek lenneek. Ők is válhatnak hatékony közzétételi mechanizmusok saját, ha következetesen használják az idő múlásával.*

Vegye figyelembe a következő típusú dákók beépítésekor a felhasználói élmény különböző módjait:

| Vizuális jelzések                                                                                                                                                               | Hangjelzés                                                      | Haptic dákók |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Reszponzív valós idejű jelzések (pl. animációk)<br> Nem képernyős jelzések (pl. fények és minták az eszközön)<br>  | Sonicon (pl. egy rövid, jellegzetes hang, zenei hangsorok sorozata) | Rezgés   |

### <a name="capability-disclosure"></a>A képességek nyilvánosságra hozatala

A közzététel hallgatólagosan elérhető, ha pontos elvárásokat állítunk fel arra vonatkozóan, hogy a digitális asszisztens mire képes. Mintaparancsokat adhat meg, hogy a felhasználók megtanulják, hogyan kommunikálhatnak a digitális asszisztenssel, és kontextuális segítséget nyújthatnak a szintetikus hangról az élmény korai szakaszában.

![Vizuális jelzések és visszajelzések](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Társalgási átláthatóság

Ha a beszélgetések váratlan útvonalakba esnek, fontolja meg az alapértelmezett válaszok kidolgozását, amelyek segíthetnek az elvárások visszaállításában, az átláthatóság megerősítésében és a felhasználók sikeres elérési utak felé terelésében. Vannak lehetőségek, hogy használja explicit közzététel beszélgetés is.

![Nem várt útvonalak kezelése](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Off-task &quot;vagy&quot; személyes kérdések et az ügynök egy jó ideje, hogy emlékeztesse a felhasználókat a szintetikus jellege az ügynök, és irányítja őket, hogy vegyenek részt vele megfelelően, vagy átirányítani őket egy valós személy.

![Feladatkérdések kezelése](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Mikor kell közzétenni

A felhasználói út során számos közzétételi lehetőség van. Tervezze meg az első használat, a második használat, az &quot;&quot; n-edik használat..., hanem az átláthatóság kiemelésének kudarcát is , például amikor a rendszer hibát követ el, vagy amikor a felhasználó felfedezi az ügynök korlátozását,&#39;képességeit.

![Közzétételi lehetőségek a felhasználói út során](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Példa szabványos digitális asszisztens felhasználói útra, amely különböző közzétételi lehetőségeket emel ki.

### <a name="up-front"></a>Kezdeti

Az optimális pillanat a nyilvánosságra hozatal az első alkalom, egy személy kölcsönhatásba lép a szintetikus hang.A személyi hangasszisztens-forgatókönyv esetén ez a bevezetés során, vagy az első alkalommal, amikor a felhasználó gyakorlatilag kicsomagolja a felhasználói élményt. Más esetekben ez lehet az első alkalom, hogy egy szintetikus hang beolvassa a tartalmat egy webhelyen, vagy az első alkalommal, amikor a felhasználó egy virtuális karakterrel lép kapcsolatba.

- [Átlátszó bevezetés](#transparent-introduction)
- [A képességek nyilvánosságra hozatala](#capability-disclosure)
- [Testreszabás és kalibrálás](#customization-and-calibration)
- [Implicit dákók](#implicit-cues--feedback)

### <a name="upon-request"></a>Kérésre

A felhasználók számára lehetővé kell tenni, hogy könnyen hozzáférjenek a további információkhoz, ellenőrizzék a beállításokat, és kérésre a felhasználói út bármely pontján átlátható kommunikációt kapjanak.

- [Lehetőségek biztosítása arra, hogy többet megtudjon arról, hogyan készült a hang](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Testreszabás és kalibrálás](#customization-and-calibration)
- [Társalgási átláthatóság](#conversational-transparency)

### <a name="continuously"></a>Folyamatosan

Használja az implicit tervezési mintákat, amelyek folyamatosan fokozzák a felhasználói élményt.

- [A képességek nyilvánosságra hozatala](#capability-disclosure)
- [Implicit dákók](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Ha a rendszer meghibásodik

Használja a közzétételt, mint lehetőséget, hogy nem kecsesen.

- [Társalgási átláthatóság](#conversational-transparency)
- [Lehetőségek biztosítása arra, hogy többet megtudjon arról, hogyan készült a hang](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Átadás az embernek](#conversational-transparency)



## <a name="additional-resources"></a>További források
- [A Microsoft robotolói irányelvei](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Cortana tervezési irányelvei](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [A Microsoft Windows UWP beszédtervezési irányelvei](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [A Microsoft Windows Mixed Reality hangvezérlési irányelvei](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>Referenciadokumentumok

* [Közzététel a Voice Talent számára](https://aka.ms/disclosure-voice-talent)
* [A szintetikus hangtechnológia felelősségteljes bevezetésére vonatkozó irányelvek](concepts-guidelines-responsible-deployment-synthetic.md)
* [Gating – áttekintés](concepts-gating-overview.md)
* [Hogyan lehet nyilvánosságra hozni](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>További lépések

* [Közzététel a Voice Talent számára](https://aka.ms/disclosure-voice-talent)
