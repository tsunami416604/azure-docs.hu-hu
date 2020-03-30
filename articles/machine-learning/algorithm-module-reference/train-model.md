---
title: 'Betanítási modell: Modul hivatkozási'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a **Betanítási modell** modult az Azure Machine Learningben egy besorolási vagy regressziós modell betanításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 0a9728e05aee27e74054a77e2c9be7dc08968207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455859"
---
# <a name="train-model-module"></a>Betanítási modell modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal betanítása osztályozási vagy regressziós modell. A betanítás a modell definiálása és paramétereinek beállítása után történik, és címkézett adatokat igényel. A **Betanítási modell** segítségével is betaníthat egy meglévő modellt új adatokkal. 

## <a name="how-the-training-process-works"></a>A képzési folyamat működése

Az Azure Machine Learningben a gépi tanulási modell létrehozása és használata általában három lépésből áll. 

1. A modellkonfigurálása egy adott algoritmustípus kiválasztásával, valamint annak paramétereinek vagy hiperparamétereinek meghatározásával. Válasszon az alábbi modelltípusok közül: 

    + **Besorolási** modellek, neurális hálózatok, döntési fák, döntési erdők és egyéb algoritmusok alapján.
    + **Regressziós** modellek, amelyek standard lineáris regressziót tartalmazhatnak, vagy amelyek más algoritmusokat használnak, beleértve a neurális hálózatokat és a bayes-i regressziót.  

2. Adjon meg egy adatkészletet, amely címkével van ellátva, és az algoritmussal kompatibilis adatokkal rendelkezik. Csatlakoztassa az adatokat és a modellt is a **Betanítási modellhez.**

    Milyen betanítási termel egy adott bináris formátum, az iLearner, amely magában foglalja a statisztikai mintákat tanult az adatokból. Ezt a formátumot nem lehet közvetlenül módosítani vagy olvasni; azonban más modulok is használhatják ezt a betanított modellt. 
    
    Megtekintheti a modell tulajdonságait is. További információt az Eredmények szakaszban talál.

3. A betanítás befejezése után használja a betanított modellt az egyik [pontozási modullal,](./score-model.md)hogy előrejelzéseket készítsen az új adatokról.

## <a name="how-to-use-train-model"></a>A Train Model használata 
  
1.  Az Azure Machine Learningben konfiguráljon egy besorolási modellt vagy regressziós modellt.
    
2. Adja hozzá a **Train Model** modult a folyamathoz.  Ez a modul a **Machine Learning** kategóriában található. Bontsa ki a **Train**csomópontot, majd húzza a **Train Model** modult a folyamatba.
  
3.  A bal oldali bemeneten csatlakoztassa a képzetlen módot. Csatolja a betanítási adatkészletet a Train Model jobb oldali **bemenetéhez.**

    A betanítási adatkészletnek tartalmaznia kell egy címkeoszlopot. A rendszer figyelmen kívül hagyja a címkék nélküli sorokat.
  
4.  A **Címke oszlopban**kattintson a jobb oldali modulpanel **oszlopának szerkesztése** gombjára, és válasszon egy oszlopot, amely tartalmazza a modell által betanításra használható eredményeket.
  
    - Besorolási problémák esetén a címkeoszlopnak **kategorikus** értékeket vagy különálló értékeket kell **tartalmaznia.** Néhány példa lehet egy igen/nem minősítés, egy betegség besorolási kódja vagy neve, vagy egy jövedelemcsoport.  Ha nem kategorikus oszlopot választ, a modul a betanítás során hibaüzenetet ad vissza.
  
    -   Regressziós problémák esetén a címkeoszlopnak **numerikus** adatokat kell tartalmaznia, amelyek a válaszváltozót jelölik. Ideális esetben a numerikus adatok folyamatos skálát jelölnek. 
    
    Ilyen lehet például a hitelkockázati pontszám, a merevlemez meghibásodásának várható ideje, vagy a hívásközpontba irányuló hívások előre jelzett száma egy adott napon vagy időpontban.  Ha nem választ numerikus oszlopot, hibaüzenet jelenhet meg.
  
    -   Ha nem adja meg, hogy melyik címkeoszlopot használja, az Azure Machine Learning megpróbálja kikövetkeztetni, hogy melyik a megfelelő címkeoszlop az adatkészlet metaadatainak használatával. Ha nem a megfelelő oszlopot választja, az oszlopkijelölővel javítsa ki.
  
    > [!TIP] 
    > Ha nem tudja használni az Oszlopválasztót, a tippeket az [Oszlopok kijelölése](./select-columns-in-dataset.md) az adatkészletben című témakörben talál. Néhány gyakori forgatókönyvet és tippet ír le a **SZABÁLYOKKAL** és név **szerint** beállítás használatához.
  
5.  Küldje el a folyamatot. Ha sok adata van, ez eltarthat egy ideig.

## <a name="results"></a><a name="bkmk_results"></a>Eredmények

A modell betanítása után:


+ Ha a modellt más folyamatokban szeretné használni, jelölje ki a modult, és válassza az **Adatkészlet regisztrálása** ikont a jobb oldali **panel Kimenetek** lapján. A modulpalettán mentett modelleket az Adatkészletek területen **érheti**el.

+ A modell használata az új értékek előrejelzéséhez csatlakoztassa azt a [Score Model](./score-model.md) modulhoz, az új bemeneti adatokkal együtt.


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 