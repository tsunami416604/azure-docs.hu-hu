---
title: Az Azure Batch AI szolgáltatás kvótái és korlátozásai |} A Microsoft Docs
description: Alapértelmezett Azure Batch AI kvóták, korlátozások és megkötések ismertetése, és növeli a hogyan kvótát
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: e4b6d115aebfd96d127e0d72c0c99188bedd93e6
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057093"
---
# <a name="batch-ai-service-quotas-and-limits"></a>A Batch AI szolgáltatás kvótái és korlátai

Mint Azure-szolgáltatásokat, korlátozva van az egyes erőforrásokat a Batch AI szolgáltatással kapcsolatos. Batch AI, ezek a korlátok a következők az előfizetés szintjén minden egyes régióban, ahol a szolgáltatás alkalmazott alapértelmezett kvóta [elérhető](https://azure.microsoft.com/global-infrastructure/services/). Ez a cikk ismerteti azokat az alapértelmezett beállításokat, és hogyan kérheti a kvóta növeli.

Kvóta ne feledje, tervezési és a méretezés a Batch AI-erőforrások. Például ha a fürt nem éri el a csomópontok megadott célszámát, majd előfordulhat, hogy elérte egy Batch AI magos korlát az előfizetéshez.

Ha azt tervezi, éles számítási feladatok futtatása a Batch AI, szükség lehet egy vagy több az alapértelmezettnél eggyel a kvóták növelése érdekében.

> [!NOTE]
> Kvóta kreditjét, nem kapacitás garantálja. Ha nagyméretű kapacitásigények, lépjen kapcsolatba az Azure-támogatás.
> 
> 

## <a name="resource-quotas"></a>Erőforráskvóták

A Batch AI a magok számát és a egy előfizetésben régiónként engedélyezett a fürtök számának alapértelmezett kvóta korlátozva van.

| **Erőforrás** | **Alapértelmezett korlát** | **Felső korlát** |
| --- | --- | --- |
| Dedikált magok régiónként | 10 – 24 | N/A<sup>1</sup> |
| Alacsony prioritású magok régiónként | 10 – 24 | N/A<sup>2</sup> |
| Fürtök régiónként | 20 | 200-as<sup>3</sup> |

> [!NOTE]
> Alapértelmezett korlátok előfizetés típusától függően eltérőek lehetnek.

<sup>1</sup> a Batch AI előfizetésenként dedikált magok száma növelhető, de a maximális száma nincs megadva. Lépjen kapcsolatba a növelési lehetőségek tekintetében az Azure ügyfélszolgálatától.

<sup>2</sup> Batch AI előfizetésenként alacsony prioritású magok száma növelhető, de a maximális száma nincs megadva. Lépjen kapcsolatba a növelési lehetőségek tekintetében az Azure ügyfélszolgálatától.

<sup>3</sup> Azure forduljon az ügyfélszolgálathoz, ha meghaladja ezt a korlátot növelni szeretné.

## <a name="other-limits"></a>Egyéb korlátok

Az alábbiakban a szigorú korlátok, amelyek nem lépheti túl a találati egyszer.

| **Erőforrás** | **Felső korlát** |
| --- | --- |
| Az adott erőforráscsoport esetében maximális munkaterületek | 800 |
| A fürt maximális mérete | 100 csomópont |
| Maximális GPU MPI folyamatok száma csomópontonként | 1 – 4 |
| Csomópontonkénti maximális GPU-feldolgozók | 1 – 4 |
| Maximális élettartama | 7 nap<sup>1</sup> |
| Csomópontonkénti maximális paraméter-kiszolgálók | 1 |

<sup>1</sup> maximális élettartamát az idő vonatkozik, hogy egy feladat elkezdi fut, és ha ez befejeződik. Befejezett feladatok korlátlan ideig megőrződnek; nem érhető el a maximális élettartamon belül nem befejezett feladatok adatait.

## <a name="view-batch-ai-quotas"></a>Batch AI-kvóták megtekintése

Az aktuális a Batch AI előfizetés kvótái megtekintheti a [az Azure portal][portal].

1. A bal oldali panelen kattintson a **minden szolgáltatás**. Majd keresse meg az **Batch AI** és kattintással nyissa meg a szolgáltatás.
2. Kattintson a **használat + kvóták** a Batch AI menü.
3. Válassza ki az előfizetését, a kvótakorlát megtekintéséhez.

## <a name="increase-a-batch-ai-cores-quota"></a>Egy Batch AI magkvóta növeléséhez

Kövesse az alábbi lépéseket egy kvótát növelheti a Batch AI előfizetés használatának a [az Azure portal][portal]. 

1. A bal oldali panelen kattintson a **minden szolgáltatás**. Majd keresse meg az **Batch AI** és kattintással nyissa meg a szolgáltatás.
2. Kattintson a **új támogatási kérelem** a Batch AI menü.
3. A **alapjai**:
   
    a. **Probléma típusa** > **kvóta**
   
    b. **Előfizetés** > Válassza ki az előfizetését.
   
    c. **Kvóta típusa** > **Batch AI**
   
    d. **Támogatási csomag** > Válassza ki a támogatási csomagot.

    Kattintson a **Tovább** gombra.
4. A **probléma**:
   
    a. Válassza ki a **súlyossági** a következők szerint a [üzletmenetre gyakorolt hatás][support_sev].
   
    b. A **kvóta részletei**, adja meg a helyet, a kvóta típusa és az erőforrás típusa. Adja meg az új határértéket szeretne igényelni. Kattintson a **menti és folytatja azt**.

    c. Nem kötelező – a növekedés okát kapcsolatos további információkat az érintett fájlokat feltölteni.
   
    Kattintson a **Tovább** gombra.
5. A **kapcsolattartási adatok**:
   
    a. Válassza ki a **elsődleges kapcsolattartási módszert**.
   
    b. Győződjön meg arról, és adja meg a szükséges kapcsolattartási adatokat.
   
    Kattintson a**Create** (Létrehozás) gombra a támogatási kérelem elküldéséhez.

Miután a támogatási kérelem elküldése, az Azure-támogatás felveszi Önnel a kapcsolatot. A kérelem befejezése legfeljebb 2 munkanapon is igénybe vehet.


## <a name="next-steps"></a>További lépések

Azután, hogy ismeri a kvótakorlát, ismerje meg az alábbi cikkeket a Batch AI használatának első lépései.

> [!div class="nextstepaction"]
> [A Batch AI gyors üzembe helyezési útmutató](quickstart-tensorflow-training-cli.md)
> [Batch AI-receptek](https://github.com/Azure/BatchAI/tree/master/recipes)
> [tudjon meg többet a Batch AI-erőforrások](resource-concepts.md)

[portal]: https://portal.azure.com
[support_sev]: http://aka.ms/supportseverity