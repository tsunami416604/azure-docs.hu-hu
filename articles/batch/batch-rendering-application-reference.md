---
title: Az Azure Batch renderelési alkalmazások használata
description: Az Azure Batch renderelési alkalmazások használata
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 500246dc98618aead11ba539ce4485d25ac62941
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "40034777"
---
# <a name="rendering-applications"></a>Renderelő alkalmazásokkal

Renderelő alkalmazásokkal használja a Batch-feladatok és tevékenységek létrehozása. A tevékenység parancssori tulajdonságának megfelelő parancssor és paraméterek megadása.  Hozzon létre a feldolgozás feladatokat legegyszerűbb módja az, hogy használja a Batch Explorer sablonok megadott [Ez a cikk](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer).  A sablonok tekinthet meg, és szükség esetén létrehozott verziók módosítani.

A cikk ismerteti az egyes renderelési alkalmazások futtatása rövid leírását.

## <a name="rendering-with-autodesk-3ds-max"></a>Renderelése az Autodesk 3ds Max

### <a name="renderer-support"></a>Megjelenítő támogatása

Mellett a leképezőket beépített 3ds Max, a következő jelentéselemeket a renderelési Virtuálisgép-rendszerképek elérhetők, és hivatkozhat a 3ds Max-jelenet fájlt:

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Tevékenység parancssora

Meghívása a `3dsmaxcmdio.exe` alkalmazásnak, hogy parancssori renderelési készlet csomóponton.  Ez az alkalmazás elérési úton van, a feladat futtatásakor. A `3dsmaxcmdio.exe` alkalmazás rendelkezik, elérhető ugyanazokat a paramétereket a `3dsmaxcmd.exe` alkalmazás, amelynek a leírása itt található a [3ds Max súgódokumentációval](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (megjelenítése |} Parancssori renderelési szakaszt).

Példa:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Megjegyzések:

* Nagy gondot kell fordítani, annak érdekében, hogy az adategység-fájlok találhatók.  Győződjön meg, hogy az elérési utak megfelelő és a relatív a **eszköz követési** ablakban, vagy használja a `-bitmapPath` paraméter a parancssorban.
* A renderelési, például keresse meg az eszközök, úgy, hogy nem tud problémái vannak a `stdout.txt` 3ds által írt fájlok maximális száma, a feladat futtatásakor.

### <a name="batch-explorer-templates"></a>A Batch Explorer sablonok

Készlet és a feladat-sablonok érhetők el a **katalógus** a Batch Explorer.  A sablon forrásfájlok érhetők el a [Batch Explorer data adattárat a Githubon](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Az Autodesk Maya megjelenítése

### <a name="renderer-support"></a>Megjelenítő támogatása

Mellett a Maya épített leképezőkhöz a következő leképezőket a renderelési Virtuálisgép-rendszerképek elérhetők, és hivatkozhat a 3ds Max-jelenet fájlt:

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Tevékenység parancssora

A `renderer.exe` parancssori megjelenítő szerepel a tevékenység parancssora. A parancssori megjelenítő leírása itt található [a Maya alkalmazáshoz súgó](http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

A következő példában feladat-előkészítési tevékenységet a feladat-előkészítési munkakönyvtár másolja a jelenetfájlok és az eszközök segítségével, kimeneti mappa a renderelési lemezkép tárolására szolgál, és 10 keret jelenik meg.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

V-Ray renderelést, a Maya jelenetfájl általában megadni V-Ray, a megjelenítő.  Azt is megadható a parancssorban:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Az Arnold renderelése, a Maya jelenetfájl általában megadni Arnold, a megjelenítő.  Azt is megadható a parancssorban:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>A Batch Explorer sablonok

Készlet és a feladat-sablonok érhetők el a **katalógus** a Batch Explorer.  A sablon forrásfájlok érhetők el a [Batch Explorer data adattárat a Githubon](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>További lépések

A készlet és -feladat sablonokat használni a [a GitHub adattár](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) Batch Explorer használatával.  Ha szükséges, hozzon létre új sablonokat, vagy módosítsa a megadott sablonok egyikét.