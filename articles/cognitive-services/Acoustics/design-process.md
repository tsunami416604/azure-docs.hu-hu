---
title: Tervezési alapelvek az Acoustics Simulationnel
titlesuffix: Azure Cognitive Services
description: A fogalmi áttekintése ismerteti, hogyan magában foglalja a projekt Akusztika akusztikai szimuláció tervezni a folyamathoz.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 4a1a0b15da091a1c020eb132f6b14b9ee14d334c
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316068"
---
# <a name="project-acoustics-design-process-concepts"></a>Projekt Akusztika tervezési folyamat alapelvei

A fogalmi áttekintése ismerteti, hogyan Akusztika projekt tartalmazza az eredményes tervezési folyamatba fizikai akusztikai szimuláció.

## <a name="sound-design-with-audio-dsp-parameters"></a>Hang DSP paraméterekkel tervezni

3D interaktív-címei az adott hang hang digitális jelfeldolgozó (DSP) blokkokat audio motor üzemeltetett használatával érhet el. Ezek blokkok tartomány vegyítése, reverberation, echo, késleltetés, kiegyenlítés, tömörítés, korlátozása, és más hatás összetettebbé válnak. Válassza ki, elrendezése, majd a következmények paramétereinek beállítása feladata az eredményes Tervező, akik összeállít egy hang gráfokon esztétikai és játékélményt célja, a felhasználói élményt biztosít.

Egy interaktív címben, hangok és figyelő áthelyezése során a 3D terület, hogyan hajtsa végre ezeket a paramétereket alkalmazkodhat a változó körülményekhez? A hangfájl designer gyakran szervez programozott elérése reverberation hatások, a módosítások például, vagy a kombinációs kacsa hangok szerint a figyelő egy részét a jelenet helyez át egy másik paraméterek módosításainak aktiválhat kötetek során a hely. Akusztika rendszereket is elérhető, amelyek segítségével automatizálható néhány következmények.

3D címek világító és kinematikus fizika rendszerek fizika indokolt, de a Tervező igazított többféle, szakirányú és játékélményt célok eléréséhez használja. Vizuális tervezővel egyes képpontos értékek nem állítja be, de inkább beállítja a 3D-modellek, anyagok és könnyű adatátviteli rendszerek, amelyek minden fizikailag alapú kereskedelmi visual esztétikára és CPU-költségek. Mi lenne a megfelelő folyamat Audio? Projekt Akusztika egy első lépése a feltárása, ezt a kérdést. Először azt kell touch beszélget átviteli akusztikus energia használatával adhatja meg.

![Képernyőkép a AltSpace jelenet átfedett visszhang zónák](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Lökőfeszültségű válaszok: Akusztikailag csatlakozás hely között

Ha ismeri a kialakítással hang, jól ismernek akusztikai lökőfeszültségű válaszokat. A modellek egy figyelőt a forrásból hangot átvitelét akusztikai lökőfeszültségű választ. Ezért a lökőfeszültségű választ minden hely Akusztika például hangelnyelés és reverberation érdekes hatásának rögzítheti. Lökőfeszültségű válaszokat is bizonyos hatékony tulajdonságok, amelyek lehetővé teszik a skálázható hang DSP hatások. Két hang jelek együtt hozzáadása, és a egy lökőfeszültségű válasz feldolgozása biztosítja ugyanazt az eredményt a lökőfeszültségű válasz külön-külön mindegyik jel alkalmazása, és vegye fel az eredményeket. Akusztikai propagálás és lökőfeszültségű válaszokat is nem függ a feldolgozása folyamatban van modellezve a helyszín csak a hang- és a forrás- és a figyelő helyeken. Röviden lökőfeszültségű választ mondatokból eredményes propagálása a jelenet hatása.

Lökőfeszültségű választ minden érdekes szoba akusztikai érvénybe rögzíti, és azt alkalmazhatja hang hatékonyan egy szűrővel kapunk lökőfeszültségű válaszok mérési vagy szimulátor. De mi történik, ha azt nem teljesen szeretné a fizika pontosan egyeznie a Akusztika, de ahelyett, hogy a jelenet érzelmi igényeinek megfelelő elképzelt? Azonban sokkal képpontos értékeket, például lökőfeszültségű választ csak számok ezer listája, a hogyan azt valószínűleg módosítani tudja azt esztétikai igényeinek? És mi történik, ha azt szeretné, hogy nem érhető el vagy akadály, amely zökkenőmentesen megadásának átjáróknak vagy mögött akadályokat, miközben hány lökőfeszültségű válaszok szükségünk beolvasni egy zökkenőmentes érvénybe? Mi történik, ha a forrás áthelyezi gyors? Hogyan tegye azt interpolálja?

Úgy tűnik, nehéz a szimuláció és lökőfeszültségű válaszok használata interaktív címben Akusztika egyes funkcióit. De még mindig olyan egy hang átviteli rendszer, amely támogatja a tervező beállításainak, ha össze tudjuk kapcsolni a lökőfeszültségű válaszokat a szimuláció az ismerős hang DSP érvénybe paraméterekkel.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Szimuláció csatlakozik hang DSP paraméterekkel

Lökőfeszültségű választ tartalmaz minden érdekes (és minden kevésbé fontos) akusztikus érvénybe. Hang DSP-címblokkokat, a paraméterek megfelelőek-e, amikor is leképezési érdekes akusztikus érvénybe. Akusztikus szimuláció egy hang DSP letiltása egy 3D jelenet hang átviteli automatizálása érdekében használata csak a hang DSP paraméterek lökőfeszültségű választ a mérési kérdése. Ez a mérés bizonyos gyakori és a fontos akusztikus hatások hangelnyelés, akadály, portalling és reverberation többek között a jól ismert.

De ha a szimuláció közvetlenül csatlakozik a hang DSP-paraméterek, ahol a Tervező módosítás? Mi volt hogy kapjanak? Hogy próbál a jeggyel rendszer jelentős mennyiségű memóriát vissza elvetése lökőfeszültségű válaszokat, hogy vonzó néhány DSP paramétert. És bizonyos teljesítményt az a Tervező biztosítanak a végső eredményt keresztül, azt kell csak megtalálni a tervezőben a szimuláció és a hanganyag DSP közötti beszúrása.

![Graph-stilizált lökőfeszültségű válasz átfedett paraméterekkel](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>A szimuláció hang DSP paraméterek átalakításával keletkező tervezni

Fontolja meg a napszemüvegeket a világ nézetének van hatással. Világos napon a szemüveg valami otthonosabban csökkentheti a izzólámpákhoz. Sötét szoba előfordulhat, hogy nem kell minden látható. A szemüveg nem állít be bizonyos fokú fényerő minden helyzetben; Ügyeljen rá, minden sötétebb.

Szimuláció használatával meghajtó a hang DSP hangelnyelés és reverberation paraméterek használatával, ha azt szűrőt is hozzáadhat a szimulátor a DSP "látja" paraméterek módosítása után. A szűrő nem lenne hangelnyelés bizonyos szintű kényszerítése vagy visszhang tail hosszát, sokkal például napszemüvegeket ne minden hely azonos fényereje. Előfordulhat, hogy a szűrő ügyeljen minden occluder occlude kisebb. Vagy több occlude. Hozzáadásával és módosításával "elsötétítő" hangelnyelés paraméter egy szűrőt, nagy, nyissa meg a helyiségek lenne továbbra is kevés, nem hatása hangelnyelés, átjáróknak növelné a közepes egy erős hangelnyelés jelenti, hogy a simaság érvényben megőrzése átmenetek során hogy a szimuláció biztosít.

A az összeállítást a Tervező feladat akusztikai paramétereinek minden esetben a legfontosabb DSP paraméterek szimuláció érkező alkalmazandó kiválasztásával és a módosításának szűrők kiválasztása módosítja. A Tervező tevékenységek átmeneteket magasabb fontos szempont a hangelnyelés és reverberation hatás és a többféle forrásból jelenléte beállításának keskeny fontos szempont a emelt azt. Természetesen a helyzet igénylést, amikor egy szűrő mindig elérhető, hogy egyszerűen lépjen vissza a DSP-paraméterek egy adott forrás kiválasztása egy adott helyzetben.

## <a name="sound-design-in-project-acoustics"></a>A projekt Akusztika tervezni

A projekt Akusztika csomag integrálható a fent leírt összetevők: szimulátort, -kódolót, amely kinyeri a paraméterek, és létrehozza a Akusztika eszköz, a DSP hang és a egy kijelölt szűrők. A projekt Akusztika tervezni a szimuláció származik, és a alkalmazni a hang DSP elérhetővé tett belül a játék-szerkesztő és a hang-motor a dinamikus vezérlőkkel hangelnyelés és reverberation paraméterek be, hogy a szűrők lehetőséget választva paramétereinek jár.

## <a name="next-steps"></a>További lépések
* A Tervező paradigmát használatával kipróbálhatja a [a Unity Project Akusztika rövid](unity-quickstart.md) vagy a [projekt Akusztika rövid, az Unreal](unreal-quickstart.md)
* Fedezze fel a [projekt Akusztika vezérlők tervezése Unity](unity-workflow.md) vagy a [projekt Akusztika Unreal vezérlők tervezése](unreal-workflow.md)

