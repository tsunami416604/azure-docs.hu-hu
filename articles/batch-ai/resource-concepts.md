---
title: Batch AI-erőforrások – Azure |} A Microsoft Docs
description: Munkaterületek, fürtök, fájlkiszolgálók, kísérletek és feladatok a Microsoft Azure-ban a Batch AI szolgáltatás áttekintése.
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/15/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 4454448809411c6390bc1f65c33b4abe76094ded
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407745"
---
# <a name="overview-of-resources-in-batch-ai"></a>A Batch AI-erőforrások áttekintése

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Amikor először indítja el a Batch AI szolgáltatással, érdemes tudni, hogy a rendelkezésre álló Batch AI-erőforrások. Az egyéb Azure-szolgáltatásokkal, egy vagy több Azure-beli létrehozásakor a Batch AI-erőforrások *erőforráscsoportok*. Hozzon létre legalább egy Batch AI *munkaterületek* egy erőforráscsoportban. Az egyes munkaterületeken vegyesen tartalmazza a Batch AI *fürtök*, *fájlkiszolgálók*, és *kísérletek*. Egy Batch AI kísérletet magában foglalja egy csoportja *feladatok*.

Az alábbi képen egy példa erőforrás hierarchia a Batch AI. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

Az alábbi szakaszokban részletes információkat olvashat a Batch AI-erőforrások lépjen.

## <a name="workspace"></a>Munkaterület

Egy Batch AI-munkaterületnek legfelső szintű gyűjteménye, a többi, a Batch AI-erőforrások. Munkaterületek segítségével munkahelyi külön tartozó különböző csoportokhoz vagy projektekhez. Például létrehozhat egy fejlesztési és tesztelési munkaterület.

## <a name="cluster"></a>Fürt

A Batch AI-fürt tartalmazza a számítási erőforrások futó feladatok. A fürt összes csomópontja rendelkezik ugyanazon virtuális gép mérete és operációsrendszer-lemezkép. A Batch AI-fürtök létrehozására, amelyeket a különböző igények számos lehetőséget kínál. Általában egy másik fürtön különböző kategóriájú projekt befejezéséhez szükséges feldolgozási beállítása. A Batch AI-fürtök felfelé és lefelé méretezési igény és a költségvetés alapján. További információkért lásd: [használata a Batch AI-fürtök](clusters.md).

## <a name="file-server"></a>Fájlkiszolgáló

Szükség esetén hozzon létre egy fájlkiszolgáló a Batch AI tárolja az adatokat, parancsfájlok, képzés és a kimeneti naplók. Egy Batch AI-fájlkiszolgáló egy felügyelt egycsomópontos NFS, amely a feladatok egy egyszerű és központilag elérhető tárolási helyét adja meg a fürtcsomópontokon automatikusan csatlakoztathatók. A legtöbb esetben csak egy fájlkiszolgáló van szükség a munkaterületen, és meg is szét az adatokat a betanítási feladatokhoz más könyvtárban. Ha NFS nem megfelelő, a számítási feladatokhoz, Batch AI támogatja-e más tárolási lehetőségeket, ideértve a [Azure Storage](use-azure-storage.md) vagy egyéni megoldásokkal, például egy Gluster vagy Lustre fájlrendszer.

## <a name="experiment"></a>Kísérlet

Kísérlet egy csoportja, a kapcsolódó feladatok, amelyek lekérdezéséhez, és együtt kezelheti. Minden munkaterülethez több kísérletek, előfordulhat, ahol minden egyes kísérlet próbál egy adott probléma megoldásához.

## <a name="job"></a>Feladat

Egy feladat egy adott feladat vagy parancsfájlt, amely kell végrehajtani, például a deep learning-modell betanításához. Minden egyes feladat egy adott parancsfájl a munkaterület egy fürtön hajt végre. (A parancsfájl lehet tárolni egy Batch AI-fájlkiszolgáló vagy más tárolási megoldás.) Minden egyes Batch AI-feladat társítva keretrendszer típussal rendelkező: Tensorflow-hoz, Horovod, CNTK, Caffe, Caffe2, pyTorch, Chainer, egyéni MPI vagy egyéni. Minden keretrendszer a Batch AI szolgáltatás állítja be a szükséges infrastruktúrát, és kezeli a feladat csak feldolgozza. Minden egyes kísérlet hasonló, néhány változtatást, hogy a különböző paraméterek szereplőkkel több feladat is rendelkezhet.

## <a name="next-steps"></a>További lépések

* Futtassa az első [Batch AI betanítási feladat](quickstart-tensorflow-training-cli.md).

* A különböző keretrendszerekhez való [betanítási módszerek](https://github.com/Azure/BatchAI/tree/master/recipes) mintáinak megtekintése.