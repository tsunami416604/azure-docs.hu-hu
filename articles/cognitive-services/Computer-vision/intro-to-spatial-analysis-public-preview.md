---
title: Computer Vision térbeli elemzés bemutatása
titleSuffix: Azure Cognitive Services
description: Ez a dokumentum ismerteti a Computer Vision térbeli elemzési tárolójának alapvető fogalmait és funkcióit.
services: cognitive-services
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: e017fac551e3122cc6586b32423ff166462ccad8
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513333"
---
# <a name="introduction-to-computer-vision-spatial-analysis"></a>Computer Vision térbeli elemzés bemutatása

Computer Vision térbeli elemzés az Azure Computer Vision Cognitive Services új funkciója, amely segít a szervezeteknek a fizikai helyük értékének maximalizálásában azáltal, hogy egy adott területen belül megismerik a személyek mozgásait és jelenlétét. Lehetővé teszi a CCTV vagy a térfigyelő kamerákból származó videók betöltését, AI-képességek futtatásával kinyerheti az elemzési eredményeket a videó streamekről, és más rendszerek által használt eseményeket is létrehozhat. A kamera streamből való bevitelsel egy mesterséges intelligencia-képesség olyan dolgokhoz is elvégezhető, mint például a felhasználók számának megszámlálása, illetve a közösségi elhatárolja vonatkozó irányelvek betartásának mértéke.

## <a name="the-basics-of-spatial-analysis"></a>A térbeli elemzés alapjai

Napjainkban a térbeli elemzés alapvető képességei mind egy olyan folyamatra épülnek, amely videó betöltését, észleli a videóban lévő személyeket, nyomon követi az embereket, ahogy az idő múlásával mozognak, és eseményeket generálnak, mivel az emberek a fontos régiókkal működnek együtt.

## <a name="spatial-analysis-terms"></a>Térbeli elemzési feltételek

| Időszak | Definíció |
|------|------------|
| Emberek észlelése | Ez az összetevő a "hol vannak a képen emberek?" kérdésre ad választ? Megkeresi az embereket egy képen, és átadja a határoló dobozt, amely feltünteti az egyes személyek helyét a People Tracking összetevőnek. |
| Személyek követése | Ez az összetevő összekapcsolja a személyek észleléseit az idő múlásával, ahogy a felhasználók egy kamera előtt mozognak. Időbeli logikát használ arról, hogy az emberek általában hogyan helyezik át a felhasználók általános megjelenésével kapcsolatos alapvető információkat. Nem követheti nyomon a több kamerán dolgozó személyeket, és nem azonosíthatja azokat a személyeket, akik körülbelül egy percen belül eltűntek. Az emberek követése nem használ olyan biometrikus jelölőket, mint az Arcfelismerés vagy a testtartás követése. |
| Érdekes régió | Ez egy, a bemeneti videóban definiált zóna vagy vonal a konfiguráció részeként. Ha egy személy a videó régióját használja, a rendszer létrehoz egy eseményt. A PersonCrossingLine skill esetében például egy sor van definiálva a videóban. Ha egy személy keresztezi az eseményt, az esemény létrejön. |
| Esemény | Az esemény a térbeli elemzés elsődleges kimenete. Az egyes képességek időnként egy adott eseményt bocsátanak ki (pl.: percenként egyszer) vagy egy adott trigger bekövetkeztekor. Az esemény információt tartalmaz arról, hogy mi történt a bemeneti videóban, de nem tartalmaz képeket vagy videókat. A PeopleCount-képesség például kibocsáthat egy eseményt, amely a frissített számot tartalmazza minden alkalommal, amikor megváltoznak a személyek száma (trigger) vagy percenként (rendszeres időközönként). |

## <a name="example-use-cases-for-spatial-analysis"></a>Példa a térbeli elemzések használati eseteire

A következő példák olyan használati eseteket mutatnak be, amelyeket a térbeli elemzés megtervezése és tesztelése során figyelembe vettünk.

**Közösségi** túllépéses megfelelőség – egy Office-tárterület több kamerával is rendelkezik, amelyek térbeli elemzéssel figyelik a közösségi túllépések megfelelőségét a személyek közötti távolság mérésével. A létesítmények kezelője használhatja az intenzitástérképei-t, amely összesített statisztikáit jeleníti meg a társadalmi elhatároljak megfelelőségének időbeli alakulásáról a munkaterület módosításához és a társadalmi elvonások megkönnyítéséhez.

A **Shopper elemzése** – a szupermarketek a terméken rámutató kamerákat használnak az adattárolási forgalom változásainak megmérésére. A System lehetővé teszi, hogy a Store Manager azonosítsa, hogy mely új termékek hajtják meg a legtöbb változást a részvételre.

**Üzenetsor-kezelés** – a pénztári várólistákra rámutató kamerák olyan riasztásokat biztosítanak a kezelők számára, amikor a várakozási idő túl sokáig tart, így több sort nyithat meg. A várólista-lemondás előzményeinek betekintést nyújt a fogyasztói viselkedésbe.

A **foglaltság & elemzések kiépítése** – az irodaház a lépés hangja mérésére és a munkahelyen a felhasználók használatára összpontosító kamerákat használ. Az eredmények lehetővé teszik az épületfelügyeleti számára a szolgáltatás és az elrendezés módosítását az utasok jobb kiszolgálásához.

**Minimális személyzeti észlelés** – egy adatközpontban a kamerák figyelik a tevékenységeket a kiszolgálókon. Ha az alkalmazottak fizikailag rögzítik a bizalmas berendezéseket, a javítás során mindig két személynek kell jelen lennie a biztonsági okokból. A kamerák segítségével ellenőrizheti, hogy a rendszer követi-e ezt az útmutatót.

**Munkahelyi optimalizálás** – egy gyors alkalmi étteremben a konyhában található kamerákkal összesített adatokat lehet előállítani az alkalmazotti munkafolyamatról. Ezt a kezelők a csapat folyamatainak és képzésének javítására használják.

## <a name="considerations-when-choosing-a-use-case"></a>Használati esetek kiválasztásakor megfontolandó szempontok

A **kritikus biztonsági riasztások elkerülése** – a térbeli elemzés nem a kritikus biztonsági valós idejű riasztásokhoz lett tervezve. Nem szabad olyan helyzetekben támaszkodni, amikor valós idejű riasztásokra van szükség ahhoz, hogy elkerülje a sérülést, például egy nehéz gép kikapcsolását, ha van ilyen személy. A kockázat csökkentése érdekében statisztikai és beavatkozási feladatokkal csökkentheti a kockázatos viselkedést, például a korlátozott/tiltott területek beléptetését.

**Kerülje a foglalkoztatással kapcsolatos döntések elvégzését** – a térbeli elemzések olyan valószínűségi mérőszámokat biztosítanak, amelyek egy adott térben lévő személyek helyével és mozgásával kapcsolatosak. Habár ez az adat hasznos lehet az összesített folyamatok fejlesztéséhez, az adat nem az egyéni munkavégző teljesítményének megfelelő mutatója, és nem használható fel a foglalkoztatással kapcsolatos döntések meghozatalára.

**Ne használja az egészségügyi ellátással kapcsolatos döntéseket** – a térbeli elemzések a személyek mozgásával kapcsolatos valószínűségi és részleges adatfeldolgozást biztosítanak. Az adat nem alkalmas az egészségüggyel kapcsolatos döntések meghozatalára.

**A védett tárolóhelyek használatának elkerülése** – az egyéni adatok védelme a kamera helyeinek és helyeinek kiértékelésével, a szögek és az érdeklődési körök módosításával, így nem hagyhatja figyelmen kívül a védett területeket, például a mellékhelyiségeket.

**Körültekintően tekintse meg az iskolákban vagy az idősek ápolási lehetőségeit** – a térbeli elemzések nem tesztelték nagy mértékben a 18. vagy felnőtt korú 65-kor alatti kiskorúakat tartalmazó adatmennyiséget. Javasoljuk, hogy az ügyfelek alaposan értékeljék a hibák sebességét azokban a környezetekben, ahol ezek a korok dominálnak.

**Alaposan fontolja meg a nyilvános helyeken való használatát** – a kamera helyeinek és helyeinek kiértékelése, a szögek és az érdeklődési körök kiértékelése a nyilvános terekből való gyűjtés minimalizálásához. A nyilvános terekben (például utcákban és parkokban) a világítás és az időjárási viszony jelentős hatással lehet a térbeli elemzési rendszer teljesítményére, és rendkívül nehéz a nyilvános helyeken való hatékony közzétételt biztosítani.

## <a name="spatial-analysis-gating-for-public-preview"></a>Nyilvános előzetes verzió térbeli elemzési kapuzás

Annak biztosítása érdekében, hogy a rendszer a térbeli elemzést használja, az alkalmazási folyamat keretében elérhetővé tesszük ezt a technológiát az ügyfeleknek. A térbeli elemzéshez való hozzáféréshez először ki kell töltenie az online bevitel űrlapot. [Kezdje el az alkalmazást itt](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

A térbeli elemzés nyilvános előzetes verziójához való hozzáférésre a Microsoft saját belátása szerint a jogosultsági feltételek, a beléptetési folyamat és a rendelkezésre állás alapján korlátozott számú ügyfél támogatására jogosult. A nyilvános előzetes verzióban olyan ügyfeleket keresünk, akik jelentős kapcsolattal rendelkeznek a Microsofttal, és akik a javasolt felhasználási esetekben, valamint a felelősségteljes AI-kötelezettségvállalásokkal összhangban álló további forgatókönyvekkel foglalkoznak.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A térbeli elemzés jellemzői és korlátai](https://docs.microsoft.com/legal/cognitive-services/computer-vision/accuracy-and-limitations?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)
