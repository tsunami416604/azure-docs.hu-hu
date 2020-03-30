---
title: 'Anomáliadetektálási modell betanítása: modul hivatkozási'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Train Anomaly Detection Model modul betanított anomáliadetektálási modell létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502970"
---
# <a name="train-anomaly-detection-model"></a>Anomáliadetektálási modell betanítása

Ez a cikk ismerteti, hogyan használhatja a **betanítási anomáliaészlelési modell** modul az Azure Machine Learning designer (előzetes verzió) egy betanított anomáliaészlelési modell létrehozásához.

A modul az anomáliadetektálási modell modell modell és egy címkézetlen adatkészlet modellparaméterkészletét adja meg bemenetként. Egy betanított anomáliadetektálási modellt ad vissza, a betanítási adatok címkéivel együtt.  

A tervezőben található anomáliadetektálási algoritmusokról az alábbi témakörökben olvashat bővebben: 

+ [PCA-alapú anomáliadetektálás](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>A vonatanomália-észlelési modell konfigurálása 

1.  Adja hozzá a **Train Anomaly Detection Model** modult a tervező ben a folyamathoz. Ez a modul az **Anomáliadetektálás** kategóriában található.

2. Csatlakoztassa az anomáliadetektálásra tervezett modulok egyikét, például a [PCA-alapú anomáliadetektálást](pca-based-anomaly-detection.md)

    Más típusú modellek nem támogatottak; a folyamat futtatásakor a hiba jelenik meg: Minden modellnek azonos tanulótípussal kell rendelkeznie.  

3.  Konfigurálja az anomáliadetektálási modult a címkeoszlop kiválasztásával és az algoritmusra jellemző egyéb paraméterek beállításával.  

4.  Betanítási adatkészlet csatolása a **train anomáliadetektálási modell**jobb oldali bemenetéhez.  

5.  Küldje el a folyamatot.  

## <a name="results"></a>Results (Eredmények)

A képzés befejezése után:

+ A modell paramétereinek megtekintéséhez kattintson a jobb gombbal a modulra, és válassza a **Visualize parancsot.** 

+ Előrejelzések létrehozásához használja [a Score Model](score-model.md) új bemeneti adatokat.

+ A betanított modell pillanatképének mentéséhez jelölje ki a modult, és kattintson az **Adatkészlet regisztrálása** ikonra a jobb oldali panel **kimenetek+naplók** lapján.   

 
## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 

A tervezőmodulokra jellemző hibák listáját [a tervező (előzetes verzió) Kivételek és hibakódok](designer-error-codes.md) című témakörben tekintheti meg.
'