---
title: Gépi tanulási folyamatok hibakeresése és hibaelhárítása
titleSuffix: Azure Machine Learning
description: A gépi tanulási folyamatok hibakeresése és hibaelhárítása a Pythonhoz készült Azure Machine Learning SDK-ban. Ismerje meg a folyamatok fejlesztésével kapcsolatos gyakori buktatókat, valamint a távoli végrehajtás előtt és közben a parancsfájlok hibakeresését segítő tippeket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 10/03/2019
ms.openlocfilehash: fc19e864f00489d3ebc0162705af864785af0811
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497071"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Gépi tanulási folyamatok hibakeresése és hibaelhárítása
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan végezhet hibakeresést és hibaelhárítást a [gépi tanulási folyamatokban](concept-ml-pipelines.md) a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-ban.

A következő szakaszokban áttekintheti a folyamatok összeállításakor előforduló gyakori buktatókat, valamint a folyamatokban futó kód hibakereséséhez szükséges különböző stratégiákat. Ha nem sikerül a folyamat futtatása a várt módon, kövesse az alábbi tippeket. 

## <a name="testing-scripts-locally"></a>Parancsfájlok helyi tesztelése

A folyamat egyik leggyakoribb meghibásodása, hogy egy csatolt parancsfájl (adattisztítási parancsfájl, pontozási parancsfájl stb.) nem a kívánt módon fut, vagy futásidejű hibákat tartalmaz a távoli számítási környezetben, amelyek nehezen tudnak hibakeresést végezni az Azure-beli munkaterületen. Learning Studio. 

Maguk a folyamatok nem futtathatók helyileg, de a parancsfájlok a helyi gépen való elkülönítése lehetővé teszi a gyorsabb hibakeresést, mivel nem kell megvárnia a számítási és a környezeti felépítési folyamatra. Ehhez szükség van egy fejlesztési munkára:

* Ha az adatai egy felhőalapú adattárban találhatók, le kell töltenie az adatait, és elérhetővé kell tennie azt a parancsfájl számára. Az adat kis mintájának használatával lerövidítheti a futtatókörnyezetet, és gyors visszajelzést kaphat a parancsfájlok működéséről
* Ha egy közbenső folyamat lépését kísérli meg szimulálni, előfordulhat, hogy manuálisan kell létrehoznia az adott parancsfájlnak az előző lépésből várt típusú objektumokat.
* Emellett meg kell határoznia a saját környezetét, és replikálnia kell a távoli számítási környezetben meghatározott függőségeket.

Ha a parancsfájl beállítása a helyi környezetben való futtatásra van beállítva, sokkal egyszerűbb a hibakeresési feladatok végrehajtása, például:

* Egyéni hibakeresési konfiguráció csatolása
* A végrehajtás felfüggesztése és az objektum állapotának vizsgálata
* Olyan típusú vagy logikai hibák, amelyek nem lesznek elérhetők a futtatókörnyezetig

> [!TIP] 
> Ha ellenőrizni szeretné, hogy a parancsfájl a várt módon fut-e, egy jó következő lépés futtatja a szkriptet egy egylépéses folyamaton, mielőtt egy több lépésből álló folyamaton próbálja futtatni azt.

## <a name="debugging-scripts-from-remote-context"></a>Parancsfájlok hibakeresése távoli környezetből

A parancsfájlok helyi tesztelése nagyszerű módja annak, hogy a folyamat megkezdése előtt hibakeresést végezzen a fő kódrészletek és az összetett logika között, de egy bizonyos ponton valószínűleg a szkripteket kell hibakeresést végeznie a tényleges folyamat futtatásakor, különösen ha a rendszer a probléma diagnosztizálását végzi a folyamat lépései közötti interakció során. Javasoljuk, hogy az `print()` utasítások liberális használatát adja meg a lépés parancsfájljaiban, hogy a távoli végrehajtás során az objektum állapota és a várt értékek láthatók legyenek, hasonlóan a JavaScript-kód hibakereséséhez.

A naplófájl `70_driver_log.txt` a következőket tartalmazza: 

* A parancsfájl végrehajtása során kinyomtatott utasítások
* A parancsfájl verem-nyomkövetése 

Ha a portálon szeretné megkeresni a többi naplófájlt, először kattintson a munkaterületen található folyamatra.

![Folyamatok lapja a portálon](./media/how-to-debug-pipelines/pipeline-1.png)

Navigáljon a folyamat szülőjének futtatásához.

![Folyamatok fölérendelt futtatása](./media/how-to-debug-pipelines/pipeline-2.png)

Kattintson a futtatási AZONOSÍTÓra az adott lépéshez.

![Folyamatok lapja a portálon](./media/how-to-debug-pipelines/pipeline-3.png)

Navigáljon a **naplók** lapra. Egyéb naplók a környezeti rendszerkép létrehozási folyamatával és a lépés-előkészítési parancsfájlokkal kapcsolatos információkat tartalmaznak.

![Folyamatok lapja a portálon](./media/how-to-debug-pipelines/pipeline-4.png)

> [!TIP]
> A *közzétett folyamatokra* vonatkozó futtatások a portál munkaterületének **folyamatok** lapján találhatók. A *nem közzétett folyamatokra* vonatkozó futtatások a **kísérletekben**találhatók.

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Az alábbi táblázat a folyamat fejlesztése során felmerülő gyakori problémákat tartalmazza, és lehetséges megoldásokat tartalmaz.

| Probléma | Lehetséges megoldás |
|--|--|
| Nem lehet átadni az adat`PipelineData` könyvtárba | Győződjön meg arról, hogy létrehozott egy könyvtárat a parancsfájlban, amely megfelel annak, ahol a folyamat a lépés kimeneti adatait várja. A legtöbb esetben a bemeneti argumentum meghatározza a kimeneti könyvtárat, majd explicit módon létrehozza a könyvtárat. A kimeneti könyvtár létrehozásához használja a `os.makedirs(args.output_dir, exist_ok=True)`. Tekintse meg az [oktatóanyagot](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) egy pontozási parancsfájl példája, amely ezt a kialakítási mintát mutatja. |
| Függőségi hibák | Ha helyileg fejlesztett ki és tesztelt parancsfájlokat, de függőségi problémákat tapasztal, amikor távoli számítási folyamaton fut, ügyeljen arra, hogy a számítási környezet függőségei és verziói megfeleljenek a tesztkörnyezet feltételeinek. |
| Nem egyértelmű hibák a számítási célokkal | A számítási célok törlése és újbóli létrehozása a számítási célokkal kapcsolatos bizonyos problémák megoldására szolgál. |
| A folyamat nem használja újra a lépéseket | Az ismételt használat alapértelmezés szerint engedélyezve van, de gondoskodjon arról, hogy ne tiltsa le egy folyamat lépéseiben. Ha az újbóli használat le van tiltva, a lépésben szereplő `allow_reuse` paraméter értéke `False`. |
| A folyamat feleslegesen fut újra | Annak biztosítása érdekében, hogy a lépések csak akkor fussanak újra, amikor a mögöttes adatokat vagy parancsfájlokat módosítják, az egyes lépésekhez adja meg a címtárakat Ha ugyanazt a könyvtárat használja több lépéshez, előfordulhat, hogy szükségtelen ismétléseket tapasztal. A folyamat lépéseit tartalmazó objektum `source_directory` paraméterének használatával mutasson erre a lépésre az elkülönített könyvtárra, és győződjön meg arról, hogy nem ugyanazt a `source_directory` elérési utat használja több lépéshez. |

## <a name="next-steps"></a>További lépések

* Tekintse meg az SDK-referenciát a [azureml-pipeline-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) csomag és a [azureml-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) csomag súgójában.

* Kövesse a [speciális oktatóanyagot](tutorial-pipeline-batch-scoring-classification.md) a Batch-pontozási folyamatok használatáról.
