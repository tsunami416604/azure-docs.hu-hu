---
title: Útmutató a szintetikus hangtechnológia felelős üzembe helyezéséhez
titleSuffix: Azure Cognitive Services
description: A Microsoft általános tervezési irányelvei a szintetikus hangtechnológia használatához. Ezek olyan tanulmányokban lettek kifejlesztve, amelyeket a Microsoft a hangtehetségekkel, a fogyasztókkal, valamint a beszédfelismerési rendellenességekkel foglalkozó személyekkel folytatott a szintetikus hang felelősségteljes fejlesztése érdekében.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836765"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Útmutató a szintetikus hangtechnológia felelős üzembe helyezéséhez
Íme a Microsoft általános tervezési irányelvei a szintetikus hangtechnológia használatához. Ezek olyan tanulmányokban lettek kifejlesztve, amelyeket a Microsoft hangtehetségekkel, fogyasztókkal, valamint a beszédfelismerési rendellenességekkel foglalkozó személyekkel folytatott a szintetikus hang felelősségteljes fejlesztése érdekében.

## <a name="general-considerations"></a>Általános megfontolások
A szintetikus beszédfelismerési technológiák üzembe helyezéséhez a következő irányelvek vonatkoznak a legtöbb forgatókönyvre.

### <a name="disclose-when-the-voice-is-synthetic"></a>Kitakarás, ha a hang szintetikus
Ha azt kívánja, hogy egy hang ne legyen a számítógép által létrehozva, akkor az nem csupán a félrevezető következményekkel jár, hanem növeli a bizalmat a szervezeten belül is. További információ a [közzétételről](concepts-disclosure-guidelines.md).

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Válassza ki a megfelelő hangtípusokat a forgatókönyvhöz
Körültekintően vegye figyelembe a használat kontextusát és a szintetikus hang használatával járó lehetséges károkat. Előfordulhat például, hogy a nagy megbízhatóságú szintetikus hangok nem megfelelőek a magas kockázatú helyzetekben, például a személyes üzenetkezelés, a pénzügyi tranzakciók vagy az emberi alkalmazkodást vagy az empátiát igénylő összetett helyzetekben. Előfordulhat, hogy a felhasználók a hangtípusokra vonatkozóan eltérő elvárásokat is tartalmazhatnak. Például, ha egy szintetikus hang alapján olvas be bizalmas híreket, néhány felhasználó inkább empatikus és emberi jellegűebb olvasást szeretne a hírekből, míg mások inkább monoton, elfogulatlan hangvételt igényelnek. Vegye fontolóra az alkalmazás tesztelését a felhasználói beállítások jobb megismerése érdekében.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Átlátható legyen a képességek és korlátozások
A felhasználók nagyobb valószínűséggel nagyobb elvárásokkal járnak, amikor a nagy megbízhatóságú szintetikus hanganyagokkal kommunikálnak. Ennek következtében, ha a rendszerképességek nem felelnek meg az elvárásoknak, a bizalmi kapcsolat romolhat, és kellemetlen, vagy akár káros élményt is eredményezhet.

### <a name="provide-optional-human-support"></a>Opcionális emberi támogatás biztosítása
A nem egyértelmű, tranzakciós forgatókönyvek (például a Call Support Center) esetében a felhasználók nem mindig bíznak a számítógép-ügynökben, hogy megfelelően válaszoljanak a kéréseiket. Ezekben az esetekben szükség lehet az emberi támogatásra, függetlenül a rendszer hangjának vagy funkciójának reális minőségétől.

## <a name="considerations-for-voice-talent"></a>A hangalapú tehetségek szempontjai
A hangtehetségek, például a hangszínészek, a szintetikus hangok létrehozásához a következő iránymutatás vonatkozik.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Értelmes beleegyezett a hangtehetségből
A hangtehetségek elvárják, hogy szabályozzák a hangbetűkészletét (hogyan és hol lesznek használatban), és hogy a használat során kompenzálva legyenek. A rendszertulajdonosoknak ezért explicit írásos engedélyt kell kapniuk a hangtehetségtől, és egyértelmű szerződéses specifikációkat kell használniuk a használati esetekről, a használat időtartamáról, a kárenyhítésről stb. Bizonyos hangtehetségek nem ismerik a technológia lehetséges kártékony felhasználását, és a rendszertulajdonosok által a technológia képességeivel kapcsolatos ismeretekkel kell rendelkezniük. A hangtehetségről és a belefoglalásról további információért olvassa el a hangvételi [feladatokkal kapcsolatos](https://aka.ms/disclosure-voice-talent)tudnivalókat.


## <a name="considerations-for-those-with-speech-disorders"></a>A beszédfelismerési rendellenességekkel kapcsolatos megfontolások
Ha beszédfelismerési rendellenességgel rendelkező személyekkel dolgozik, a szintetikus hangtechnológia létrehozásához vagy üzembe helyezéséhez a következő irányelvek érvényesek.

### <a name="provide-guidelines-to-establish-contracts"></a>A szerződések létrehozásához szükséges irányelvek megadása
Útmutatást nyújt a szerződések létrehozásához a szintetikus hangvételt használó személyekkel való kommunikációhoz. A szerződésnek meg kell fontolnia azon felek megadását, akik a hangvételt, a használat időtartamát, a tulajdonjog-átadási feltételeket, a hangbetűkészlet törlésének eljárásait, valamint a jogosulatlan hozzáférés megelőzésének módját. Emellett engedélyezheti a hangbetűkészletek tulajdonjogának a családtagok halála utáni átruházását, ha az adott személy engedélyt kapott.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>A beszéd mintázatok következetlenségének fiókja
A saját hangbetűkészleteit rögzítő beszédes problémákkal rendelkező személyek esetében a beszédfelismerési minta (az adathígtrágya vagy bizonyos szavak kimondása) inkonzisztenciaa megnehezítheti a rögzítési folyamatot. Ezekben az esetekben a szintetikus hangtechnika és a rögzítési munkamenetek (azaz a szüneteket és a rögzítési munkamenetek további számát biztosítják).

### <a name="allow-modification-over-time"></a>Módosítás időbeli változásának engedélyezése
Azok a személyek, akik beszédi rendellenességeket kívánnak készíteni a szintetikus hangon, hogy tükrözzék az öregedést (például egy gyermek a pubertást érik el). A magánszemélyek is rendelkezhetnek olyan stilisztikai beállításokkal, amelyek idővel változnak, és előfordulhat, hogy módosítani szeretnék a szurok, a hangsúly vagy más hangjellemzőket.


## <a name="reference-docs"></a>Segédanyagok

* [A hangalapú tehetségek közzététele](https://aka.ms/disclosure-voice-talent)
* [A kapuzás áttekintése](concepts-gating-overview.md)
* [A közzététel módja](concepts-disclosure-guidelines.md)
* [Közzétételi tervezési minták](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>További lépések

* [A hangalapú tehetségek közzététele](https://aka.ms/disclosure-voice-talent)
* [A közzététel módja](concepts-disclosure-guidelines.md)
* [Közzétételi tervezési minták](concepts-disclosure-patterns.md)
