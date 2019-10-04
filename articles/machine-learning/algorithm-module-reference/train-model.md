---
title: 'Betanítási modell: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning szolgáltatásban a **Train Model** modult egy besorolási vagy regressziós modell betanításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 08b551e766632949db350478fa8d3725906c8af8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128449"
---
# <a name="train-model-module"></a>Tanítási modell modulhoz

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal betaníthatja a besorolási vagy regressziós modellt. A képzés a modell meghatározása és a hozzá tartozó paraméterek beállítása után zajlik, és címkézett adatokra van szükség. A betanítási **modell** használatával egy meglévő modell újratanítását is elvégezheti új adattal. 

## <a name="how-the-training-process-works"></a>A betanítási folyamat működése

Azure Machine Learning a Machine learning-modellek létrehozása és használata általában egy három lépésből álló folyamat. 

1. A modell konfigurálásához válasszon egy adott típusú algoritmust, és határozza meg a paramétereit vagy hiperparaméterek beállítása. Válassza ki a következő típusok bármelyikét: 

    + A besorolási modellek neurális hálózatok, Döntési fák és döntési erdők, valamint más algoritmusok alapján.
    + **Regressziós** modellek, amelyek standard lineáris regressziót tartalmazhatnak, vagy amelyek más algoritmusokat használnak, beleértve a neurális hálózatokat és a Bayes-regressziót is.  

2. Adja meg a címkével ellátott adatkészletet, és az algoritmussal kompatibilis adatokat tartalmaz. Kapcsolja össze az adattípust és a modellt a **modell**betanításához.

    A létrehozott képzések egy adott bináris formátum, a iLearner, amely az adatokból beszerzett statisztikai mintákat foglalja magában. Ezt a formátumot nem lehet közvetlenül módosítani vagy olvasni; más modulok azonban használhatják ezt a betanított modellt. 
    
    Megtekintheti a modell tulajdonságait is. További információ: Results (eredmények) szakasz.

3. A betanítás befejezése után használja a betanított modellt az egyik [pontozási modullal](./score-model.md), hogy előrejelzéseket készítsen az új adatmennyiségekről.

## <a name="how-to-use-train-model"></a>A **Train Model** használata  
  
1.  Azure Machine Learning a besorolási modell vagy a regressziós modell konfigurálása.
    
2. Adja hozzá a betanítási **modell** modult a kísérlethez.  Ez a modul a **Machine learning** kategóriában található. Bontsa ki a **vonat**elemet, majd húzza a **Train Model** modult a kísérletbe.
  
3.  A bal oldali bemeneten csatolja a képzetlen üzemmódot. Csatolja a betanítási adatkészletet a betanítási **modell**jobb oldali bemenetéhez.

    A betanítási adatkészlet címkét tartalmazó oszlopot kell tartalmaznia. A címkék nélküli sorok figyelmen kívül lesznek hagyva.
  
4.  A **label (címke) oszlopban**kattintson az **oszlop kiválasztásának indítása**elemre, és válasszon ki egy olyan oszlopot, amely tartalmazza a modell által betanítható eredményeket.
  
    - Besorolási problémák esetén a Label oszlopnak **kategorikus** értékeket vagy **különálló** értékeket kell tartalmaznia. Néhány példa lehet igen/nem minősítés, a betegség besorolási kódja vagy neve, vagy egy bevételi csoport.  Ha nem kategorikus oszlopot választ, a modul a betanítás során hibát ad vissza.
  
    -   A regressziós problémák esetén a Label oszlopnak a válasz változót jelölő **numerikus értékeket** kell tartalmaznia. Ideális esetben a numerikus adat folyamatos méretezést jelent. 
    
    Ilyen lehet például a kreditek kockázati pontszáma, a merevlemez meghibásodásának tervezett ideje, vagy egy adott napon vagy időpontban a Call Center felé irányuló hívások előre jelzett száma.  Ha nem választ egy numerikus oszlopot, hibaüzenetet kaphat.
  
    -   Ha nem határozza meg, hogy melyik címke oszlopot szeretné használni, Azure Machine Learning megpróbálja kikövetkeztetni, hogy melyik a megfelelő címke oszlop az adatkészlet metaadatainak használatával. Ha nem a megfelelő oszlopot választja, a kiválasztó oszlop segítségével javítsa ki.
  
    > [!TIP] 
    > Ha problémája van az oszlop Választójának használatával, tekintse meg a tippek az [Oszlopok kiválasztása](./select-columns-in-dataset.md) az adatkészletben című cikket. Ismertet néhány gyakori forgatókönyvet és tippet a **with Rules** és a **Name** beállítások használatával.
  
5.  Futtassa a kísérletet. Ha sok adattal rendelkezik, ez hosszabb időt is igénybe vehet.

## <a name="bkmk_results"></a>Eredmények

A modell képzése után:

+ A modell paramétereinek és a szolgáltatás súlyozásának megtekintéséhez kattintson a jobb gombbal a kimenetre, és válassza a **Megjelenítés**lehetőséget.
+ Ha más kísérletekben szeretné használni a modellt, kattintson a jobb gombbal a modellre, és válassza a **modell mentése**lehetőséget. Adja meg a modell nevét. 

    Ez a modellt olyan pillanatképként menti, amely nem frissül a kísérlet ismételt futtatásával.
+ Ha a modellt az új értékek előrejelzéséhez szeretné használni, akkor a [pontszám modell](./score-model.md) modulhoz kell csatlakoznia, az új bemeneti adatokkal együtt.


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 