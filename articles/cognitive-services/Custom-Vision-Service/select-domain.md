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
ms.openlocfilehash: 0dbd6ea13069b72e6bca5c065af92568a5c7cdb8
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/01/2021
ms.locfileid: "97844943"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Tartomány kiválasztása Custom Vision projekthez

A Custom Vision projekt beállítások lapján választhat egy tartományt a projekthez. Válassza ki a forgatókönyvhöz legközelebb eső tartományt. Ha ügyféloldali kódtár vagy REST API használatával fér hozzá Custom Visionhoz, meg kell adnia egy tartományi azonosítót a projekt létrehozásakor. Lekérheti a tartományi azonosítók listáját a [lekérési tartományokkal](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab), vagy használhatja az alábbi táblázatot.

## <a name="image-classification"></a>Képbesorolás

|Tartomány|Cél|
|---|---|
|__Általános__| A képbesorolási feladatok széles körére optimalizált. Ha a többi tartomány egyike sem megfelelő, vagy nem biztos benne, hogy melyik tartományt szeretné kiválasztani, válassza az általános tartományt. ID `ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__Élelmiszer__|Az ételek fényképeihez optimalizált, ahogy azt egy étterem menüjében láthatja. Ha egyéni gyümölcsökből vagy zöldségekből származó fényképeket szeretne osztályozni, használja az élelmiszer-tartományt. ID `c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__Arcrészek__|Felismerhető tereptárgyak számára optimalizált, természetes és mesterséges is. Ez a tartomány akkor működik a legjobban, ha a tereptárgy jól látható a fényképben. Ez a tartomány akkor is működik, ha a tereptárgyat az előttük lévő személyek kis mértékben akadályozzák. ID `ca455789-012d-4b50-9fec-5bb63841c793`|
|__Retail__|A vásárlási katalógusban vagy a vásárlási webhelyen található rendszerképekre optimalizált. Ha a ruhák, nadrágok és ingek között nagy pontosságú osztályozást szeretne, használja ezt a tartományt. ID `b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__Kompakt tartományok__| Az Edge-eszközök valós idejű besorolásának korlátaira optimalizált.|

## <a name="object-detection"></a>Objektumészlelés

|Tartomány|Cél|
|---|---|
|__Általános__| Az objektum-észlelési feladatok széles körére optimalizált. Ha a többi tartomány egyike sem megfelelő, vagy nem biztos benne, hogy melyik tartományt szeretné kiválasztani, válassza az általános tartományt. ID `da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__Embléma__|A képeken található márka emblémák keresésére optimalizált. ID `1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__Termékek a polcokon__|A polcokon található termékek észlelésére és besorolására optimalizált. ID `3780a898-81c3-4516-81ae-3a139614e1f3`|
|__Kompakt tartományok__| Az Edge-eszközök valós idejű objektum-észlelésének korlátaira optimalizált.|

## <a name="compact-domains"></a>Kompakt tartományok

A kompakt tartományok által generált modellek helyileg is futtathatók. A Custom Vision 3,4 nyilvános előzetes verzió API-ban a GetDomains API meghívásával lekérheti a kompakt tartományokhoz tartozó exportálható platformok listáját.

A modell teljesítménye a kiválasztott tartománytól függ. Az alábbi táblázatban bejelentjük a modell méretét és következtetéseit az Intel Desktop CPU-ban és az NVidia GPU \[ 1-ben \] . Ezek a számok nem tartalmazzák az előfeldolgozást és a utófeldolgozó időt.

|Feladat|Tartomány|ID (Azonosító)|Modell mérete|CPU-következtetési idő|GPU-következtetési idő|
|---|---|---|---|---|---|
|Besorolás|General (compact) (Általános (kompakt))|`0732100f-1a38-4e49-a514-c9b44c697ab5`|5 MB|13 MS|5 MS|
|Objektumészlelés|General (compact) (Általános (kompakt))|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35 MS|5 MS|
|Objektumészlelés|Általános (Compact) [S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27 MS|7 MS|

>[!NOTE]
>Az objektumok észleléséhez szükséges __általános (Compact)__ tartomány speciális utófeldolgozó logikát igényel. A részletekért tekintse meg az exportált zip-csomagban található példaként szolgáló parancsfájlt. Ha a utófeldolgozó logikát nem használó modellre van szüksége, használja az __általános (Compact) [S1]__.

>[!IMPORTANT]
>Nincs garancia arra, hogy az exportált modellek pontosan ugyanazt eredményezik, mint az előrejelzési API a felhőben. A futó platform enyhe eltérése vagy az előfeldolgozás megvalósítása nagyobb különbséget eredményezhet a modell kimenetében. Az előfeldolgozási logika részleteiért tekintse meg ezt a [dokumentumot](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5 – 2690 CPU és NVIDIA Tesla M60
