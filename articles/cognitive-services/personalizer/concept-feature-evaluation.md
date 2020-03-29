---
title: Funkcióértékelés - Personalizer
titleSuffix: Azure Cognitive Services
description: Amikor az Azure Portalon futtat egy kiértékelést a Personalizer erőforrásában, a Personalizer tájékoztatást nyújt arról, hogy a környezet és a műveletek milyen funkciói befolyásolják a modellt.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 31243b5e9da55aafbc376fa416c1b00a4499c116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70242414"
---
# <a name="feature-evaluation"></a>Szolgáltatások értékelése

Amikor az [Azure Portalon](https://portal.azure.com)futtat egy kiértékelést a Personalizer erőforrásban, a Personalizer tájékoztatást nyújt arról, hogy a környezet és a műveletek milyen funkciói befolyásolják a modellt. 

Ez a következők érdekében hasznos:

* Képzeld el, hogy további funkciókat is használhatsz, és ihletet meríthetsz amodell ben fontosabb funkciókból.
* Tekintse meg, hogy milyen funkciók nem fontosak, és potenciálisan távolítsa el őket, vagy elemezze tovább, hogy mi befolyásolhatja a használatot.
* Útmutatást nyújt szerkesztői vagy gyógyító csapatok nak az új tartalmakról vagy termékekről, amelyeket érdemes behozni a katalógusba.
* Elháríthatja azokat a gyakori problémákat és hibákat, amelyek akkor fordulnak elő, amikor a szolgáltatásokat elküldi a Personalizer szolgáltatásnak.

A fontosabb funkciók erősebb súlyokkal rendelkeznek a modellben. Mivel ezek a funkciók erősebb súllyal, általában jelen vannak, amikor Personalizer szerez magasabb jutalmakat.

## <a name="getting-feature-importance-evaluation"></a>A funkciók fontossági értékelésének beszerzése

A szolgáltatásfontossági eredmények megtekintéséhez le kell futtatnia egy értékelést. A kiértékelés az értékelési időszak ban megfigyelt jellemzőnevek alapján hozza létre az ember által olvasható jellemzőcímkéket.

Az eredményül kapott információ a szolgáltatás fontosságát képviseli az aktuális Personalizer online modell. Az értékelés elemzi jellemző fontosságát a modell mentett végén az értékelési időszak, miután átesett az összes képzés során végzett értékelés, a jelenlegi online tanulási politika. 

A szolgáltatás fontossági eredmények nem jelentenek más szabályzatokat és modelleket, amelyeket a kiértékelés során teszteltek vagy hoztak létre.  Az értékelés nem tartalmazza a Personalizer-nak az értékelési időszak lejárta után küldött funkciókat.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>A jellemzőfontosság kiértékelésének értelmezése

A Personalizer a funkciókat a hasonló jelentőségű funkciók "csoportjainak" létrehozásával értékeli ki. Az egyik csoport róluk azt lehet mondani, hogy összességében nagyobb jelentőséggel bírnak, mint mások, de a csoporton belül a funkciók sorrendje ábécésorrendben van.

Az egyes szolgáltatásokkal kapcsolatos információk a következők:

* Azt jelzi, hogy a szolgáltatás környezetből vagy műveletekből származik-e.
* Jellemzőkulcs és érték.

Egy fagylaltozó rendelési alkalmazás például nagyon fontos funkcióként láthatja a "Context.Weather:Hot" című alkalmazást.

A Personalizer olyan funkciók korrelációit jeleníti meg, amelyek együttes figyelembevételével magasabb jutalmakat eredményeznek.

Például a "Context.Weather:Hot *with* Action.MenuItem:IceCream" és a "Context.Weather:Cold *with* Action.MenuItem:WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>A funkciókiértékelés alapján végrehajtható műveletek

### <a name="imagine-additional-features-you-could-use"></a>Képzeljen el további funkciókat, amelyeket használhat

Merítsen ihletet a modell fontosabb funkcióiból. Ha például egy videomobilalkalmazásban a "Context.MobileBattery:Low" jelenik meg, akkor azt gondolhatja, hogy a kapcsolat típusa arra is ráveheti az ügyfeleket, hogy egy videoklipet lássanak a másikkal szemben, majd a kapcsolat típusával és sávszélességével kapcsolatos funkciókat is hozzáadhat az alkalmazáshoz.

### <a name="see-what-features-are-not-important"></a>Nézze meg, milyen funkciók nem fontosak

Potenciálisan távolítsa el a lényegtelen funkciókat, vagy tovább elemezze, hogy mi befolyásolhatja a használatot. A funkciók több okból is alacsonyak lehetnek. Az egyik lehet, hogy valóban a funkció nem befolyásolja a felhasználói viselkedést. De ez azt is jelentheti, hogy a funkció nem nyilvánvaló, hogy a felhasználó. 

Például egy videowebhely láthatja, hogy az "Action.VideoResolution=4k" egy alacsony fontosságú funkció, amely ellentmond a felhasználói kutatásoknak. Ennek oka lehet, hogy az alkalmazás nem is említi, vagy azt mutatják, a videó felbontása, így a felhasználók nem változtatják meg a viselkedésüket alapján.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Útmutatás a szerkesztői vagy a kurzorcsapatok számára

Útmutatást nyújt az új tartalmakhoz vagy termékekhez, amelyeket érdemes behozni a katalógusba. Personalizer célja, hogy egy eszköz, amely növeli az emberi betekintést és a csapatok. Ennek egyik módja az, hogy tájékoztatást nyújt a szerkesztői csoportoknak arról, hogy mi ről szól a viselkedést meghajtó termékekről, cikkekről vagy tartalmakról. Például a videóalkalmazás-forgatókönyv azt mutathatja, hogy van egy fontos funkció, az "Action.VideoEntities.Cat:true", amely arra kéri a szerkesztői csapatot, hogy hozzanak be több macskás videót.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Gyakori problémák és hibák elhárítása

A gyakori problémákat és hibákat az alkalmazáskódjának módosításával lehet kijavítani, így az nem küld nem megfelelő vagy helytelenül formázott funkciókat a Personalizer-nek. 

A szolgáltatások küldésekor gyakori hibák a következők:

* Személyazonosításra alkalmas adatok küldése. Egy személyre jellemző személyazonosításra használt személy (például név, telefonszám, hitelkártyaszámok, IP-címek) nem használható a Personalizer segítségével. Ha az alkalmazásnak nyomon kell követnie a felhasználókat, használjon nem azonosító UUID azonosítót vagy más UserID számot. A legtöbb esetben ez is problematikus.
* Nagy számú felhasználó esetében nem valószínű, hogy az egyes felhasználók interakciói nagyobb súlyt fognak mérni, mint a lakosság összes interakciója, így a felhasználói azonosítók küldése (még akkor is, ha nem személyazonosításra alkalmas adatok) valószínűleg nagyobb zajt ad a modellnek, mint érték.
* Dátum-idő mezők küldése pontos időbélyegként a felvázolt időértékek helyett. A context.TimeStamp.Day=Monday vagy a "Context.TimeStamp.Hour"="13" funkciók hasznosabbak. Az egyes szolgáltatások legbőlegelhetően 7 vagy 24 jellemzőértékben lesznek. De "Context.TimeStamp":"1985-04-12T23:20:50.52Z" annyira pontos, hogy nem lesz módja annak, hogy tanulni belőle, mert soha nem fog megtörténni újra.

## <a name="next-steps"></a>További lépések

A Personalizer segítségével [megismerheti a méretezhetőséget és](concepts-scalability-performance.md) a teljesítményt.

