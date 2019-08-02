---
title: Tervezési alapelvek az Acoustics Simulationnel
titlesuffix: Azure Cognitive Services
description: Ez a fogalmi áttekintés azt ismerteti, hogyan tartalmazza a Project Acoustics a hangtervezés folyamatát.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: c7e6f17d3e7b9712dd853bcf309bb73fa10ac156
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704843"
---
# <a name="project-acoustics-design-process-concepts"></a>A Project akusztikai tervezési folyamatával kapcsolatos fogalmak

Ez a fogalmi áttekintés azt ismerteti, hogyan foglalja magában a Project akusztika a fizikai akusztikai szimulációt a hangtervezési folyamat során.

## <a name="sound-design-with-audio-dsp-parameters"></a>Hang-kialakítás hangalapú DSP-paraméterekkel

a 3D interaktív címek a hangmotorban üzemeltetett hangalapú digitális jelfeldolgozó (DSP) blokkok használatával érik el az adott hangot. Ezek a blokkok az egyszerű keveréstől, a visszaverődéstől, az ECHO, a késleltetéstől, a kiegyenlítőtől, a tömörítéstől és a korlátozástól, valamint más effektusok függően különbözőek. Ezeknek a hatásoknak a kiválasztásával, megszervezésével és beállításával a hangtervező feladata, hogy ki épít egy hanggráfot, amely a felhasználói élmény esztétikai és Játékmeneti céljait éri el.

Interaktív cím esetén, ahogy a hangok és a figyelők a 3D-s térben mozognak, hogyan alkalmazkodnak ezekhez a paraméterek a változó feltételekhez? A hangtervező gyakran rendezi a köteteket a paraméterek változásának elindítására szolgáló térben, hogy megváltoztassa a visszaverődés hatásának változásait, például a hangokat, vagy ha a figyelő a jelenet egyik részéből egy másikba kerül. Az akusztikai rendszerek szintén elérhetők, amelyekkel automatizálható néhány ilyen hatás.

a 3D-címek olyan világító és kinematikus fizikai rendszereket használnak, amelyek a fizika által motivált, de tervezői célokra vannak kialakítva, így a merítési és a Játékmeneti célok kombinációja is elérhető. A vizuális tervező nem állít be egyéni képpontos értékeket, hanem a vizuális esztétika és a CPU-költségek kiszűrésére szolgáló 3D-modelleket, anyagokat és fényátviteli rendszereket állítja be. Mi lenne az egyenértékű folyamat a hanggal? A Project akusztika a kérdés feltárásának első lépése. Először is megtudhatjuk, mit jelent az akusztikai energia átvitele egy szóközzel.

![Képernyőfelvétel a AltSpace jelenetről a reverb zónákkal](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Impulzusos válaszok: Két pont akusztikus csatlakoztatása a térben

Ha már ismeri a hangtervezést, az akusztikai impulzusos válaszokat is megismerheti. Az akusztikus impulzusos válaszok egy hang átvitelét modellezik a forrásról a figyelőre. Ezért az Impulzusos válaszok rögzíthetik a helyiségek akusztikaának minden érdekes hatását, például az elzáródást és a visszhangot. Az Impulzusos válaszok olyan hatékony tulajdonságokkal is rendelkeznek, amelyek lehetővé teszik a hangalapú DSP-effektusok méretezését. Két hangjelzés együttes hozzáadása és egy impulzusos válasz feldolgozása ugyanazt az eredményt adja, mint az Impulzusos válasz alkalmazása az egyes jelekre, és hozzáadja az eredményeket. Az akusztikai propagálás és az Impulzusos válaszok szintén nem függnek a feldolgozott hangtól, csak a modellezett jelenetnél, valamint a forrás-és a figyelő helyein. Röviden, egy impulzusos válasz a jelenet hatását a hangpropagálásra is hatással van.

Az Impulzusos válaszok rögzítik az összes érdekes helyiség akusztikai hatását, és a szűrővel hatékonyan is alkalmazhatjuk a hanganyagot, és a méréstől vagy a szimulációtól kaphatunk impulzusos válaszokat. De mi történik, ha nem szeretnénk, hogy az akusztika pontosan illeszkedjen a fizikához, hanem inkább a jelenet érzelmi igényeihez igazodva? A képpont-értékekhez hasonlóan az Impulzusos válasz csak a több ezer számból álló lista, így lehetséges, hogy az esztétikai igényeknek megfelelően módosítható? És mi a teendő, ha olyan elzáródást/akadályt szeretnénk használni, amely gördülékenyen változik az átjárók vagy az akadályok mögött, hány impulzusos válaszra van szükségünk a zökkenőmentes működéshez? Mi a teendő, ha a forrás gyorsan mozog? Hogyan lehet közbeszúrást végezni?

A szimulációs és az Impulzusos válaszokat nehéz felvenni az interaktív címekbe tartozó akusztika egyes aspektusaira. De továbbra is készíthetünk olyan hangátviteli rendszereket, amelyek támogatják a tervezői beállításokat, ha a szimulációról a jól ismert hangalapú DSP Effect paraméterekkel tudjuk összeállítani az Impulzusos válaszokat.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>A szimulációs és a hangalapú DSP összekapcsolása paraméterekkel

Egy impulzusos válasz minden érdekes (és minden érdektelen) akusztikai hatást tartalmaz. A hangdsp-blokkok, ha a paraméterek megfelelően vannak beállítva, érdekes akusztikai hatást képesek megjeleníteni. Az akusztikus szimuláció használatával hangalapú DSP-blokkot lehet vezetni a hangátviteli műveletek 3D-jelenetekben történő automatizálásához. Ez a mérés jól ismert bizonyos gyakori és fontos akusztikai hatásokhoz, beleértve az elzáródást, az elzáródást, a portált és a visszaverődést.

Ha azonban a szimuláció közvetlenül a hangalapú DSP-paraméterekhez csatlakozik, hol van a tervező beállítása? Mi volt a nyereség? Emellett jelentős mennyiségű memóriát biztosítunk az Impulzusos válaszok elvetésével és néhány DSP-paraméter megtartásával. Továbbá ahhoz, hogy a tervező kihasználja a végeredményt, csak meg kell keresnie a tervezőt a szimuláció és a hangalapú DSP használatával.

![Gráf és stilizált impulzusos válasz paraméterekkel átfedésben](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Hangkialakítás a hangalapú DSP-paraméterek átalakításával a szimulációból

Gondolja át, milyen hatással van a napszemüveg a világ nézetére. A szemüvegek egy világos napon csökkentik a fényt, hogy kényelmesebbek legyenek. A sötét helyiségekben előfordulhat, hogy egyáltalán nem fog tudni semmit látni. A szemüvegek nem állítanak be bizonyos szintű fényerőket minden helyzetben; csak minden sötétebbet tesznek.

Ha a hanganyagot az elzáródási és a visszaverődési paraméterek használatával szimuláljuk, akkor a szimulátor után hozzáadhatunk egy szűrőt a DSP által megjelenített paraméterek módosításához. A szűrő nem kényszeríti ki egy bizonyos szintű elzáródás vagy reverb farok hosszát, hasonlóan a napszemüveghez, hogy ne legyenek minden helyiségben azonos fényerő. Előfordulhat, hogy a szűrő minden occluder occlude kevesebbet. Vagy további occlude. Egy "elsötétítő" elzáródási paraméter hozzáadásával és módosításával a nagyméretű, nyitott szobák továbbra sem tudnak semmilyen elzáródást, míg az átjárók közepestől erős elzáródási hatásra növekednek, miközben megőrzik a simaságot a váltás során ezt a szimuláció biztosítja.

Ebben a paradigmában a tervező feladata megváltoztatja az akusztikus paraméterek kiválasztását minden esetben, a szűrők kiválasztására és módosítására, hogy azok a szimulációból származó legfontosabb DSP-paraméterekre vonatkozzanak. Ez a művelet emeli a tervező tevékenységeit, amelyek a szűk környezetekben a zökkenőmentes áttérések beállításával kapcsolatosak, és a keverési és a visszaverődési hatások intenzitásának, valamint a különböző források jelenlétének magasabb szempontjaira vonatkoznak. Természetesen, ha a helyzet igényt kielégít, az egyik szűrő mindig elérhető, így egyszerűen visszatérhet egy adott forráshoz tartozó DSP-paraméterek kiválasztására egy adott helyzetben.

## <a name="sound-design-in-project-acoustics"></a>Hangkialakítás a Project Akusztikaében

A Project Acoustics csomag a fent ismertetett összetevők mindegyikét integrálja: egy szimulátort, egy kódolót, amely Kinyeri a paramétereket, és létrehozza az akusztikai eszközt, az audió DSP-t és a kiválasztott szűrőket. A hangalapú kialakítás a Project Acoustics használatával olyan paraméterek kiválasztását vonja maga után, amelyek a szimulációból származtatott elzáródási és visszaverődési paramétereket módosítják

## <a name="next-steps"></a>További lépések
* Próbálja ki a tervezési paradigmát a [projekt akusztikai útmutatója az Unity](unity-quickstart.md) vagy az [Unreal projekthez](unreal-quickstart.md) készült Acoustics gyors üzembe helyezéséhez
* Ismerje meg a [projekt akusztikai tervezési vezérlőit](unity-workflow.md) az egységhez vagy a [projekt akusztikai kialakításához az Unreal](unreal-workflow.md) -hez

