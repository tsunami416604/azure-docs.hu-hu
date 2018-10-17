---
title: Az Azure Batch AI szolgáltatás – mesterséges intelligencia betanítása | Microsoft Docs
description: Megismerheti, hogyan használható a felügyelt Azure Batch AI szolgáltatás mesterséges intelligenciák (AI) és egyéb gépi tanulási modellek tanításához GPU-k és processzorok fürtjein.
services: batch-ai
documentationcenter: ''
author: alexsuttonms
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: 98497812e75d07fc153e0e351331c05484164fdd
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052699"
---
# <a name="what-is-azure-batch-ai"></a>Mi az az Azure Batch AI?

Az Azure Batch AI egy felügyelt szolgáltatás, amely segít az adatelemzőknek és MI-kutatóknak nagy léptékben betanítani és tesztelni gépi tanulási és MI-modelleket az Azure-ban, anélkül, hogy összetett infrastruktúra felügyeletére lenne szükség. Csak adja meg a számítási erőforrásokat, a futtatni kívánt feladatokat, a modell bemeneti és kimeneti adatainak tárolási helyét, a Batch AI pedig elvégzi a többit.

A Batch AI szolgáltatást használhatja önállóan vagy egy nagyobb fejlesztési munkafolyamat részeként modellbetanításra:

* A Batch AI-t önmagában használhatja gépi tanulási algoritmusok és MI-modellek betanítására, tesztelésére és kötegelt pontozására [GPU-](../virtual-machines/linux/sizes-gpu.md) és CPU-fürtökön. 

* Egy Batch AI-fürtöt meghatározhat egy munkafolyamatban az [Azure Machine Learninggel](../machine-learning/service/overview-what-is-azure-ml.md) vagy egyéb [Azure AI Platform-eszközökkel](https://azure.microsoft.com/overview/ai-platform/). Az Azure ML szolgáltatás adatelőkészítési, kísérletezési és feladatelőzményekkel kapcsolatos eszközök széles kínálatát teszi elérhetővé. Az Azure ML segítségével betanított modelljeit tárolókba csomagolhatja, és üzembe helyezheti következtetés céljából vagy IoT-eszközökön.  

## <a name="train-machine-learning-and-ai-models"></a>Gépi tanulás és mesterségesintelligencia-modellek betanítása

A Batch AI szolgáltatást használhatja gépi tanulási modellek, mély neurális hálók (mélytanulás) és más MI-megoldások betanítására. A Batch AI a következő népszerű, nyílt forráskódú MI-kódtárakat és keretrendszereket támogatja: [TensorFlow](https://github.com/tensorflow/tensorflow), [PyTorch](https://github.com/pytorch/pytorch), [Chainer](https://github.com/chainer/chainer) és [Microsoft Cognitive Toolkit (CNTK)](https://github.com/Microsoft/CNTK).

Miután azonosította a problématerületet és előkészítette az adatokat, a Batch AI szolgáltatással interaktív módon tesztelheti modelljeivel kapcsolatos elgondolásait. Amikor pedig készen áll arra, hogy nagyobb léptékben is kísérletezzen, az MPI vagy más kommunikációs kódtárak segítségével elindíthatja a feladatokat több GPU-n, és párhuzamosan további kísérleteket futtathat.

A Batch AI segítségével többféle módon taníthat be modelleket nagyobb léptékben. Például: 

|  |  |
|---------|---------|
| **Betanítás elosztása**<br/>![Elosztott betanítás](./media/overview/distributed-training.png)  | Vertikálisan felskálázhatja egyetlen feladat betanítását több, hálózaton összekötött GPU-ra, ha nagyobb hálózatot kíván nagyobb mennyiségű adattal betanítani.|
| **Kísérlet**<br/>![Experimentation](./media/overview/experimentation.png) | A betanítást horizontálisan felskálázhatja több feladattal. Új ötletek kipróbálásánál a pontosság és teljesítmény optimalizálásához futtathat paraméteres frissítéseket vagy finomhangolhat hiperparamétereket. |
| **Végrehajtás párhuzamosan**![Párhuzamos végrehajtás](./media/overview/parallel-execution.png) | Betaníthat vagy pontozhat egyszerre több modellt is, egy kiszolgálóflottán párhuzamosan futtatva a feladatokat, így gyorsabban végezhet velük.|

A modell betanítása után a Batch AI segítségével tesztelheti a modellt, ha ez nem volt a betanítási szkript része, vagy kötegelt pontozást végezhet.

## <a name="how-it-works"></a>Működés

A Batch AI SDK-k, parancssori szkriptek vagy az Azure Portal segítségével kezelheti a számítási erőforrásokat és ütemezheti az MI-betanítási és tesztelési feladatokat: 

* **Virtuális gépek fürtjeinek kiépítése és méretezése** – Adja meg a csomópontok (virtuális gépek) számát, és válassza ki a betanítási igényeinek megfelelő GPU-kompatibilis vagy más virtuálisgép-méretet. A csomópontok számát felfelé vagy lefelé automatikusan vagy manuálisan is skálázhatja, így mindig csak akkor használ fel erőforrásokat, amikor szüksége van rájuk. 

* **Függőségek és tárolók kezelése** – Alapértelmezés szerint a Batch AI-fürtök linuxos virtuálisgép-rendszerképeket futtatnak, amelyeken a függőségek előre fel vannak telepítve a tárolóalapú betanítási keretrendszerek GPU-n vagy CPU-n történő futtatásához. Ha további konfigurálásra van szüksége, használhat egyéni rendszerképeket vagy futtathat indítási szkripteket.

* **Adatok elosztása** – Az Azure Files, az Azure Blob Storage vagy egy felügyelt NFS-kiszolgáló közül választhat akár többet is a bemeneti adatok és szkriptek, valamint a feladatok kimeneti adatainak tárolására. A Batch AI támogatja az egyéni tárolási megoldásokat, beleértve a nagy teljesítményű párhuzamos fájlrendszereket is. Egyszerű konfigurációs fájlok használatával csatlakoztathatja a tárak fájlrendszereit a fürtcsomópontokhoz és a feladattárolókhoz.

* **Feladatok ütemezése** – Feladatokat küldhet egy prioritásalapú feladat-várólistába a fürtök erőforrásainak megosztásához, illetve az alacsony prioritású virtuális gépek és fenntartott példányok előnyeinek kihasználása érdekében.

* **Hibák kezelése** – Figyelheti a feladatok állapotát, és VM-hibák esetén újraindíthatja az esetleg hosszan futó feladatokat.

* **Eredmények összegyűjtése** – Könnyedén elérheti a kimeneti naplókat, az stdout és stderr kimenetet, valamint a betanított modelleket. A Batch AI-feladatokat konfigurálhatja úgy is, hogy kimenetüket közvetlenül a csatlakoztatott tárolóra írják.

Azure-szolgáltatásként a Batch AI támogatja az olyan közösen használt biztonsági eszközöket, mint például a szerepköralapú hozzáférés-vezérlés (role-based access control, RBAC) és az Azure-beli virtuális hálózatok.  

## <a name="next-steps"></a>További lépések

* További információk a gépi tanulási és MI-modellek betanításával kapcsolatos [Batch AI-erőforrásokról](resource-concepts.md).

* [Egy mintául szolgáló mélytanulási modell betanításának](quickstart-tensorflow-training-cli.md) első lépései a Batch AI segítségével.

* Népszerű MI-keretrendszerekhez készült [betanítási módszerek](https://github.com/Azure/BatchAI/blob/master/recipes) mintáinak megtekintése.
