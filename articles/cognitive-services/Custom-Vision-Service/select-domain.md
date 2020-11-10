---
title: Tartomány kiválasztása Custom Vision projekthez – Computer Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan választhat tartományokat a projekthez a Custom Vision Service.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 87b9e4a3ca7151b3666928b00add175eddeea050
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409382"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Tartomány kiválasztása Custom Vision projekthez

A Custom Vision projekt Beállítások paneljén kiválaszthatja a projekt tartományát. Válassza ki a forgatókönyvhöz legközelebb eső tartományt.

## <a name="image-classification"></a>Képbesorolás

|Tartomány|Rendeltetés|
|---|---|
|__Általános__| A képbesorolási feladatok széles körére optimalizált. Ha a többi tartomány egyike sem megfelelő, vagy nem biztos benne, hogy melyik tartományt szeretné kiválasztani, válassza ki az általános tartományt.|
|__Élelmiszer__|Az ételek fényképeihez optimalizált, ahogy azt egy étterem menüjében láthatja. Ha egyéni gyümölcsökből vagy zöldségekből származó fényképeket szeretne osztályozni, használja az élelmiszer-tartományt.|
|__Arcrészek__|Felismerhető tereptárgyak számára optimalizált, természetes és mesterséges is. Ez a tartomány akkor működik a legjobban, ha a tereptárgy jól látható a fényképben. Ez a tartomány akkor is működik, ha a tereptárgyat az előttük lévő személyek kis mértékben akadályozzák.|
|__Retail__|A vásárlási katalógusban vagy a vásárlási webhelyen található rendszerképekre optimalizált. Ha a ruhák, nadrágok és ingek között nagy pontosságú osztályozást szeretne, használja ezt a tartományt.|
|__Kompakt tartományok__| Az Edge-eszközök valós idejű besorolásának korlátaira optimalizált.|

## <a name="object-detection"></a>Objektumészlelés

|Tartomány|Rendeltetés|
|---|---|
|__Általános__| Az objektum-észlelési feladatok széles körére optimalizált. Ha a többi tartomány egyike sem megfelelő, vagy nem biztos benne, hogy melyik tartományt szeretné kiválasztani, válassza ki az általános tartományt.|
|__Embléma__|A képeken található márka emblémák keresésére optimalizált.|
|__Termékek a polcokon__|A polcokon található termékek észlelésére és besorolására optimalizált.|
|__Kompakt tartományok__| Az Edge-eszközök valós idejű objektum-észlelésének korlátaira optimalizált.|

## <a name="compact-domains"></a>Kompakt tartományok

A kompakt tartományok által generált modellek helyileg is futtathatók. A Custom Vision 3,4 nyilvános előzetes verzió API-ban a GetDomains API meghívásával lekérheti a kompakt tartományokhoz tartozó exportálható platformok listáját.

A modell teljesítménye a kiválasztott tartománytól függ. Az alábbi táblázatban bejelentjük a modell méretét és következtetéseit az Intel Desktop CPU-ban és az NVidia GPU \[ 1-ben \] . Ezek a számok nem tartalmazzák az előfeldolgozást és a utófeldolgozó időt.

|Feladat|Tartomány|Modell mérete|CPU-következtetési idő|GPU-következtetési idő|
|---|---|---|---|---|
|Osztályozás|General (compact) (Általános (kompakt))|5 MB|13 MS|5 MS|
|Objektumészlelés|General (compact) (Általános (kompakt))|45 MB|35 MS|5 MS|
|Objektumészlelés|Általános (Compact) [S1]|14 MB|27 MS|7 MS|

>[!NOTE]
>Az objektumok észleléséhez szükséges __általános (Compact)__ tartomány speciális utófeldolgozó logikát igényel. A részletekért tekintse meg az exportált zip-csomagban található példaként szolgáló parancsfájlt. Ha a utófeldolgozó logikát nem használó modellre van szüksége, használja az __általános (Compact) [S1]__.

>[!IMPORTANT]
>Nincs garancia arra, hogy az exportált modellek pontosan ugyanazt eredményezik, mint az előrejelzési API a felhőben. A futó platform enyhe eltérése vagy az előfeldolgozás megvalósítása nagyobb különbséget eredményezhet a modell kimenetében. Az előfeldolgozási logika részleteiért tekintse meg ezt a [dokumentumot](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5 – 2690 CPU és NVIDIA Tesla M60
