---
title: Tervezési alapelvek az Acoustics Simulationnel
titlesuffix: Azure Cognitive Services
description: Ez a koncepcionális áttekintés bemutatja, hogy a Project Acoustics hogyan építi be az akusztikai szimulációt a hangtervezési folyamatba.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 11e1e3f45b5198ddedb6c31fcd354185adef445d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854293"
---
# <a name="project-acoustics-design-process-concepts"></a>Projekt akusztikatervezési folyamat fogalmai

Ez a koncepcionális áttekintés bemutatja, hogy a Project Acoustics hogyan építi be a fizikai akusztikai szimulációt a hangtervezési folyamatba.

## <a name="sound-design-with-audio-dsp-parameters"></a>Hangkialakítás audio DSP paraméterekkel

A 3D interaktív címek az audio digitális jelfeldolgozó (DSP) blokkok segítségével érik el a hangjukat. Ezek a blokkok tartomány összetettsége egyszerű keverés, a visszhang, visszhang, késleltetés, kiegyenlítés, tömörítés és korlátozás, és egyéb hatások. Kiválasztása, rendezése, és a paraméterek beállítása ezeket a hatásokat a feladata a hang tervező, aki épít egy audio grafikon, amely eléri az esztétikai és játékcéljait a tapasztalat.

Egy interaktív címben, ahogy a hangok és a hallgató mozog nak a 3D-s térben, hogyan alkalmazkodnak ezek a paraméterek a változó körülményekhez? A hangtervező gyakran rendezi kötetek az egész tér, amelyek programozott kiváltó paraméter változások at változások at reverberation hatások, például, vagy kacsa hangok a mix, mint a hallgató mozog az egyik része a jelenet a másikra. Akusztikai rendszerek is rendelkezésre állnak, amelyek automatizálják néhány ilyen hatások.

A 3D-s címek olyan világítási és kinematikus fizikai rendszereket használnak, amelyek fizika-motiváltak, de a tervező által beállított a merítés és a játékmenet imázsának keverékét. A vizuális tervező nem állít be egyedi képpontértékeket, hanem inkább olyan 3D modelleket, anyagokat és könnyű közlekedési rendszereket állít be, amelyek fizikailag mind a vizuális esztétika és a CPU-költségek lecserélésére. Mi lenne az egyenértékű folyamat audio? Project Akusztika az első lépés a feltárása ezt a kérdést. Először is megérintjük, mit jelent az akusztikai energiát egy térben keresztül szállítani.

![Az AltSpace jelenet zengetési zónákkal átfedésével eltitkezve](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Impulzusválaszok: Akusztikailag összekötve két pontot az űrben

Ha ismeri a hangtervezést, akkor talán ismeri az akusztikus impulzusválaszokat. Az akusztikai impulzusválasz a hang átvitelét modellezi a forrásból a hallgatóhoz. Ezért egy impulzus válasz képes megragadni minden érdekes hatása szoba akusztika, mint az elzáródás és a visszhang. Impulzus válaszok is van bizonyos erős tulajdonságokkal, amelyek lehetővé teszik a hang DSP hatások skála. Két hangjel együttes hozzáadása és impulzusválasz-feldolgozás ugyanazt az eredményt adja, mint ha az impulzusválaszt külön-külön alkalmaznák az egyes jelekre, és hozzáadják az eredményeket. Az akusztikai szaporítás és az impulzusválaszok szintén nem függnek a feldolgozott hangtól, csak a modellező jelenettől, valamint a forrás- és figyelőhelyektől. Röviden, egy impulzus válasz desztillálja a jelenet hatását a hang terjedésére.

Az impulzusválasz minden érdekes szoba akusztikai effektust rögzít, és szűrővel hatékonyan alkalmazhatjuk a hangra, és impulzusválaszokat kaphatunk a mérésből vagy szimulációból. De mi van, ha nem akarjuk, hogy az akusztika pontosan egyezzen a fizikával, hanem inkább úgy formázza, hogy megfeleljen egy jelenet érzelmi igényeinek? De hasonlóan a pixelértékekhez, az impulzusválasz csak egy több ezer számból álló lista, hogyan állíthatjuk be az esztétikai igényekhez? És mi van, ha azt akarjuk, hogy elzáródás / akadály, hogy zökkenőmentesen változik, miközben áthalad ajtók vagy akadályok mögött, hány impulzus válaszok van szükségünk, hogy a sima hatás? Mi van, ha a forrás gyorsan mozog? Hogyan interpolálunk?

Úgy hangzik, nehéz használni szimuláció és impulzus válaszok bizonyos aspektusait akusztika interaktív címek. De még mindig építhetünk egy audio közlekedési rendszert, amely támogatja a tervezői beállításokat, ha a szimulációból származó impulzusválaszainkat összekapcsoljuk a jól ismert audio DSP hatás paraméterekkel.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Szimuláció csatlakoztatása hangDSP-hez paraméterekkel

Az impulzusválasz minden érdekes (és minden érdektelen) akusztikai hatást tartalmaz. Audio DSP blokkok, ha a paraméterek megfelelően vannak beállítva, teszi érdekes akusztikus hatást. Az akusztikai szimuláció használata egy hang DSP blokk meghajtására a 3D-s jelenetben történő hangátvitel automatizálásához csak az audio DSP paraméterek impulzusválaszból történő mérésének kérdése. Ez a mérés jól ismert bizonyos gyakori és fontos akusztikai hatások, beleértve az elzáródás, elzáródás, portalling, és a visszhang.

De ha a szimuláció közvetlenül kapcsolódik az audio DSP paraméterekhez, hol van a tervezői korrekció? Mit nyertünk? Nos, jelentős mennyiségű memóriát nyerünk vissza úgy, hogy elvetjük az impulzusválaszokat, és megtartunk néhány DSP paramétert. És hogy a tervező némi hatalmat a végeredmény, már csak meg kell találni a módját, hogy helyezze be a tervező között a szimuláció és az audio DSP.

![Stilizált impulzusválaszt tartalmazó grafikon kirakott paraméterekkel](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Hangtervezés az audio DSP paraméterek szimulációból történő átalakításával

Fontolja meg a hatása a napszemüveg van a kilátás a világ. Egy fényes napon a szemüveg csökkentheti a fényt, hogy valami kényelmesebb. Egy sötét szobában, lehet, hogy nem is látsz semmit. A szemüveg nem állít be egy bizonyos fényerőt minden helyzetben; csak mindent sötétebbé tesznek.

Ha szimulációt használunk az audio DSP-nk meghajtására elzáródási és reverberation paraméterek kel, a szimulátor után hozzáadhatunk egy szűrőt, hogy módosítsuk a DSP által "látott" paramétereket. A szűrő nem kényszeríti egy bizonyos szintű elzáródás vagy zengetés farok hossza, ugyanúgy, mint napszemüveg nem teszi minden szobában ugyanazt a fényerőt. A szűrő lehet, hogy csak, hogy minden elzár elzáródás kevesebb. Vagy elzáródás több. Egy "sötétedő" elzáródási paraméterszűrő hozzáadásával és beállításával a nagy, nyitott helyiségeknek még mindig kevés vagy semmilyen elzáródási hatásuk lenne, míg az ajtók közepesről erős elzáróhatásra nőnének, miközben megtartják a sima sági átmeneteket a szimuláció által biztosított adatok.

Ebben a paradigmában a tervező feladata az akusztikai paraméterek kiválasztásától minden egyes helyzethez, a szűrők kiválasztásáig és beállításáig változik, hogy a szimulációból származó legfontosabb DSP paraméterekre vonatkozzon. Ez emeli a tervező tevékenységét a szűk aggodalmak létrehozása sima átmenetek a magasabb aggodalmak intenzitása elzáródás és visszhanghatások és a források jelenléte a mix. Természetesen, ha a helyzet megköveteli, egy szűrő mindig elérhető, hogy egyszerűen menjen vissza a DSP paraméterek kiválasztására egy adott forrás egy adott helyzetben.

## <a name="sound-design-in-project-acoustics"></a>Hangtervezés a Project Akusztikában

A Project Acoustics csomag integrálja a fent leírt összetevők mindegyikét: egy szimulátort, egy kódolót, amely kinyeri a paramétereket, és létrehozza az akusztikai eszközt, az audio DSP-t és a szűrők választékát. A Project Acoustics hangtervezése magában foglalja a paraméterek kiválasztását a szűrőkhöz, amelyek a szimulációból származó és az audio DSP-re alkalmazott elzáródási és visszhangi paramétereket módosítják, a dinamikus vezérlőket a játékszerkesztőn és az audiomotoron belül téve.

## <a name="next-steps"></a>További lépések
* Próbálja ki a tervezési paradigmát a [Project Acoustics rövid útmutatóval az Unity-hez](unity-quickstart.md) vagy a Project [Acoustics rövid útmutatóval az Unreal számára](unreal-quickstart.md)
* Fedezze fel a [Project Acoustics tervezési vezérlőit a Unity-hez](unity-workflow.md) vagy a [Project Acoustics tervezési vezérlőit az Unreal-hoz](unreal-workflow.md)

