---
title: Tartomány kiválasztása egyéni vision projekthez – Computer Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan választhat ki egy tartományt a projekthez a Custom Vision Szolgáltatásban.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1569b6081adad4cae0855f9adfb4e14e910bf819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899450"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Tartomány kiválasztása egyéni vision projekthez

Az Egyéni vision projekt beállításpaneljén kiválaszthat egy tartományt a projekthez. Válassza ki a forgatókönyvhöz legközelebb eső tartományt.

## <a name="image-classification"></a>Képbesorolás

|Domain|Cél|
|---|---|
|__Általános__| Képbesorolási feladatok széles körére optimalizálva. Ha a többi tartomány egyike sem megfelelő, vagy nem biztos abban, hogy melyik tartományt válassza, válassza az Általános tartományt.|
|__Élelmiszer__|Az ételek fényképeire optimalizálva, ahogy az éttermi menüben látni fogja őket. Ha az egyes gyümölcsökről vagy zöldségekről készült fényképeket szeretné osztályozni, használja az Élelmiszer domaint.|
|__Tereptárgyak__|Felismerhető tereptárgyakra optimalizálva, természetes és mesterséges. Ez a domain akkor működik a legjobban, ha a mérföldkő jól látható a fényképen. Ez a domain akkor is működik, ha a mérföldkő kissé akadályozza az emberek előtte.|
|__Kiskereskedelem__|Bevásárlókatalógusban vagy vásárlási webhelyen található képekre optimalizálva. Ha azt szeretnénk, nagy pontosságú osztályozása között ruhák, nadrágok, és ingek, használja ezt a tartományt.|
|__Tömörített tartományok__| A peremhálózati eszközök valós idejű besorolásának korlátaira optimalizálva.|

## <a name="object-detection"></a>Objektumészlelés

|Domain|Cél|
|---|---|
|__Általános__| Objektumészlelési feladatok széles körére optimalizálva. Ha a többi tartomány egyike sem megfelelő, vagy nem biztos abban, hogy melyik tartományt válassza, válassza az Általános tartományt.|
|__Embléma__|Márkalogók keresésére optimalizálva a képeken.|
|__Termékek a polcokon__|Termékek polcokon való észlelésére és osztályozására optimalizálva.|
|__Tömörített tartományok__| A peremhálózati eszközök valós idejű objektumészlelési korlátaira optimalizálva.|

## <a name="compact-domains"></a>Tömörített tartományok

A kompakt tartományok által létrehozott modellek exportálhatók helyi futtatásra. A modell teljesítménye a kijelölt tartománytól függően változik. Az alábbi táblázatban az Intel Desktop CPU és az NVidia GPU \[\]1 modellméretét és következtetési idejét jelentjük. 

> [!NOTE]
> Ezek a számok nem tartalmazzák az előfeldolgozási és utófeldolgozási időt.

|Tevékenység|Domain|Modell mérete|Cpu-következtetések ideje|GPU-következtetések ideje|
|---|---|---|---|---|
|Osztályozás|General (compact) (Általános (kompakt))|5 MB|13 ezres|5 ms|
|Objektumészlelés|General (compact) (Általános (kompakt))|45 MB|35 ms|5 ms|
|Objektumészlelés|Általános (kompakt) [S1]|14 MB|27 ezres|7 ezres|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (Vision AI Dev Kit)

Ha egy kompakt tartomány van kiválasztva egy extra lehetőség "Export képességek" biztosított, amely lehetővé teszi a különbséget az "alapvető platformok" és a "Vision AI Dev Kit".

Az _Exportálási lehetőségek csoportban_ a következő két lehetőség közül választhat:

- Alapvető platformok (Tensorflow, CoreML, ONNX stb.)
- Vision AI Dev Kit.

Ha a _Vision AI Dev Kit lehetőséget választja,_ az _Általános_, _Tájékozódási pontok_és _Kiskereskedelmi,_ de nem az _Élelmiszer_ kompakt tartományok érhetők el a képbesoroláshoz, míg az _Általános (kompakt)_ és _az Általános (kompakt) [S1]_ egyaránt elérhető az objektumészleléshez.

>[!NOTE]
>Az objektumészlelés __általános (kompakt)__ tartománya speciális utófeldolgozási logikát igényel. A részleteket lásd egy példa script az exportált zip csomag. Ha utófeldolgozási logika nélküli modellre van szüksége, használja az __Általános (kompakt) [S1]__.

>[!IMPORTANT]
>Nincs garancia arra, hogy az exportált modellek adja meg pontosan ugyanazt az eredményt, mint az előrejelzési API a felhőben. A futó platform vagy az előfeldolgozási megvalósítás kismértékű különbsége nagyobb különbséget okozhat a modell kimenetében. Az előfeldolgozási logika részleteit lásd [ebben a dokumentumban.](python-tutorial.md)

\[1\] Intel Xeon E5-2690 CPU és NVIDIA Tesla M60
