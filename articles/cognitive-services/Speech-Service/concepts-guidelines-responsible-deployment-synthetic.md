---
title: A szintetikus hangtechnológia felelősségteljes bevezetésére vonatkozó irányelvek
titleSuffix: Azure Cognitive Services
description: A Microsoft általános tervezési irányelvei a szintetikus hangtechnológia használatára vonatkozóan. Ezeket olyan tanulmányokban fejlesztették ki, amelyeket a Microsoft hangalapú tehetséggel, fogyasztókkal, valamint beszédzavarokkal rendelkező egyénekkel végzett a szintetikus hang felelős fejlődésének irányítására.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836765"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>A szintetikus hangtechnológia felelősségteljes bevezetésére vonatkozó irányelvek
Az alábbiakban a Microsoft szintetikus hangtechnológiával kapcsolatos általános tervezési irányelveit olvashatja. Ezeket olyan tanulmányokban fejlesztették ki, amelyeket a Microsoft hangalapú tehetséggel, fogyasztókkal, valamint beszédzavarokkal rendelkező egyénekkel végzett a szintetikus hang felelős fejlődésének irányítására.

## <a name="general-considerations"></a>Általános megfontolások
A szintetikus beszédtechnológia üzembe helyezéséhez az alábbi irányelvek vonatkoznak a legtöbb forgatókönyvre.

### <a name="disclose-when-the-voice-is-synthetic"></a>Nyilvánosságra hozni, ha a hang szintetikus
Annak nyilvánosságra hozatala, hogy a hang számítógép által generált nem csak csökkenti a káros kimenetelét a megtévesztés, hanem növeli a bizalmat a szervezet nyilvánított a hang. További információ [a nyilvánosságra hozatalról.](concepts-disclosure-guidelines.md)

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Válassza ki a forgatókönyvnek megfelelő hangtípusokat
Gondosan fontolja meg a felhasználás összefüggésében, és a lehetséges károk at szintetikus hang használatával. Például a magas hűségű szintetikus hangok nem feltétlenül megfelelőek a magas kockázatú helyzetekben, például személyes üzenetek, pénzügyi tranzakciók vagy összetett helyzetek esetén, amelyek emberi alkalmazkodóképességet vagy empátiát igényelnek. A felhasználók nak eltérő elvárásaik lehetnek a hangtípusokkal kapcsolatban is. Például, ha hallgatja az érzékeny híreket, hogy olvassa el a szintetikus hang, egyes felhasználók inkább egy empatikus és ember-szerű olvasata a híreket, míg mások inkább egy monoton, elfogulatlan hang. Fontolja meg az alkalmazás tesztelését a felhasználói beállítások jobb megértése érdekében.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Legyen átlátható a képességek és korlátozások
A felhasználók nagyobb valószínűséggel magasabb elvárásokat, amikor kölcsönhatásban áll a hi-fi szintetikus hangügynökök. Következésképpen, ha a rendszer képességei nem felelnek meg ezeknek az elvárásoknak, a bizalom szenvedhet, és kellemetlen, vagy akár káros élményeket eredményezhet.

### <a name="provide-optional-human-support"></a>Opcionális emberi támogatás biztosítása
Kétértelmű, tranzakciós forgatókönyvek (például egy telefonos ügyfélszolgálat) esetén a felhasználók nem mindig bíznak meg a számítógép-ügynökben, hogy megfelelően válaszoljon a kéréseikre. Ilyen helyzetekben emberi támogatásra lehet szükség, függetlenül a rendszer hangjának vagy képességeinek reális minőségétől.

## <a name="considerations-for-voice-talent"></a>A hangalapú tehetségekkel kapcsolatos szempontok
Amikor a hangalapú tehetségekkel, például hangszereplőkkel dolgozik a szintetikus hangok létrehozásához, az alábbi irányelv vonatkozik.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Érdemi hozzájárulás a hangalapú tehetségektől
A hangalapú tehetségek elvárják, hogy ellenőrizzék a hangbetűtípusukat (hogyan és hol fogják használni), és bármikor kompenzálják őket. A rendszertulajdonosoknak ezért kifejezett írásbeli engedélyt kell kérniük a hangalapú tehetségektől, és egyértelmű szerződéses feltételekkel kell rendelkezniük a használati esetekre, a használat időtartamára, a kártérítésre és így tovább. Némely hang tehetség van nem tudatában van a lappangó kaján használ -ból technológia és legyen művelt mellett rendszer tulajdonosok körülbelül a képességek -ból technológia. Ha többet szeretne megtudni a hangalapú tehetségről és a [beleegyezésről,](https://aka.ms/disclosure-voice-talent)olvassa el a Voice Talent ről szóló tájékoztatónkat.


## <a name="considerations-for-those-with-speech-disorders"></a>Figyelembe véve a beszédzavarok
Amikor beszédzavarban szenvedő személyekkel dolgozik, szintetikus hangtechnológiát hoz létre vagy alkalmaz, az alábbi irányelvek érvényesek.

### <a name="provide-guidelines-to-establish-contracts"></a>Iránymutatások at kell adni a szerződések létrehozásához
Adjon útmutatást a szintetikus hangot használó személyekkel kötött szerződések létrehozásához a beszédben való segítségnyújtáshoz. A szerződésnek meg kell fontolnia a hangtulajdonos felek, a használat időtartama, a tulajdonjog-átruházási kritériumok, a hangbetűtípus törlésére vonatkozó eljárások, valamint a jogosulatlan hozzáférés megakadályozásának módját. Ezen túlmenően, lehetővé teszi a szerződés átadása hang font tulajdonjogát a halál után a családtagok, ha ez a személy adott engedélyt.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>A beszédminták inkonzisztenciáinak figyelembevétele
Azoknál az egyéneknél, akik beszédzavarokkal rendelkeznek, akik saját hangbetűtípusaikat rögzítik, a beszédmintázatuk inkonzisztenciái (szürkület vagy bizonyos szavak kiejtésének képtelensége) bonyolíthatják a felvételi folyamatot. Ezekben az esetekben a szintetikus hangtechnológiának és a rögzítési munkamenetnek be kell fogadnia azokat (azaz szüneteket és további számú rögzítési munkamenetet kell biztosítania).

### <a name="allow-modification-over-time"></a>Módosítás engedélyezése idővel
Egyének beszédzavarok vágy, hogy a frissítéseket a szintetikus hang, hogy tükrözze az öregedés (például egy gyermek eléri a pubertás). Az egyének stilisztikai beállításokkal is rendelkezhetnek, amelyek idővel változnak, és módosíthatják a hangmagasságot, az ékezetet vagy más hangjellemzőket.


## <a name="reference-docs"></a>Referenciadokumentumok

* [Közzététel a Voice Talent számára](https://aka.ms/disclosure-voice-talent)
* [Gating – áttekintés](concepts-gating-overview.md)
* [Hogyan lehet nyilvánosságra hozni](concepts-disclosure-guidelines.md)
* [Közzétételi tervezési minták](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>További lépések

* [Közzététel a Voice Talent számára](https://aka.ms/disclosure-voice-talent)
* [Hogyan lehet nyilvánosságra hozni](concepts-disclosure-guidelines.md)
* [Közzétételi tervezési minták](concepts-disclosure-patterns.md)
