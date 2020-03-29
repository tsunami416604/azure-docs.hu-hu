---
title: Renderelési alkalmazások használata – Azure Batch
description: A renderelési alkalmazások használata az Azure Batch-el. Ez a cikk röviden ismerteti az egyes renderelési alkalmazások futtatásának módját.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: dc0ce23c90a4ba6575ba26b37d97f94ba8fa1f63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75390485"
---
# <a name="rendering-applications"></a>Renderelési alkalmazások

A renderelési alkalmazások kötegelt feladatok és feladatok létrehozásával használatosak. A feladat parancssori tulajdonság a megfelelő parancssort és paramétereket adja meg.  A feladatfeladatok létrehozásának legegyszerűbb módja a Batch Explorer sablonjainak használata a [cikkben](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer)meghatározottmódon.  A sablonok megtekinthetők és szükség esetén módosíthatók a létrehozott verziók.

Ez a cikk röviden ismerteti az egyes renderelési alkalmazások futtatásának módját.

## <a name="rendering-with-autodesk-3ds-max"></a>Renderelés az Autodesk 3ds Max-mal

### <a name="renderer-support"></a>Renderelő támogatása

A 3ds Max-ba épített renderelőkön kívül a következő renderelők is elérhetők a renderelési virtuális gép lemezképein, és a 3ds Max jelenetfájl ban is hivatkozhatnak:

* Autodesk Arnold
* Káosz Csoport V-Ray

### <a name="task-command-line"></a>Feladat parancssora

Az `3dsmaxcmdio.exe` alkalmazás meghívása parancssori renderelésre egy készletcsomóponton.  Ez az alkalmazás a feladat futtatásakor az elérési úton van. Az `3dsmaxcmdio.exe` alkalmazás ugyanazokat a rendelkezésre álló paramétereket, mint az `3dsmaxcmd.exe` alkalmazás, amely dokumentálja a [3ds Max súgó dokumentáció](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (Renderelés | Parancssori renderelési szakasz).

Példa:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Megjegyzések:

* Nagy gondot kell fordítani annak biztosítására, hogy a tárgy fájlok találhatók.  Győződjön meg arról, hogy az útvonalak helyesek `-bitmapPath` és relatívak az **Eszközkövetés** ablakban, vagy használja a paramétert a parancssorban.
* Ellenőrizze, hogy vannak-e problémák a rendereléssel, például `stdout.txt` nem lehet megtalálni az eszközöket, ha a feladat futtatásakor ellenőrzi a 3ds Max által írt fájlt.

### <a name="batch-explorer-templates"></a>Batch Explorer sablonok

A készlet- és feladatsablonok a **Tártár** a kötegelt intézőben elemből érhetők el.  A sablonforrás-fájlok a [GitHub Batch Explorer adattárában](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax)érhetők el.

## <a name="rendering-with-autodesk-maya"></a>Renderelés az Autodesk Maya-val

### <a name="renderer-support"></a>Renderelő támogatása

A Mayába épített renderelőkön kívül a következő renderelők is elérhetők a renderelési virtuális gép lemezképein, és a 3ds Max jelenetfájl ban is hivatkozhatnak:

* Autodesk Arnold
* Káosz Csoport V-Ray

### <a name="task-command-line"></a>Feladat parancssora

A `renderer.exe` parancssori renderelő a feladat parancssorában használatos. A parancssori renderelő a [Maya súgójában](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4)van dokumentálva.

A következő példában egy feladat-előkészítési feladat segítségével másolja a jelenetfájlokat és -eszközöket a feladat-előkészítési munkakönyvtárba, egy kimeneti mappát használ a renderelési lemezkép tárolására, és a 10-es keret jelenik meg.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

V-Ray renderelés esetén a Maya jelenetfájl általában V-Ray-t ad meg renderelőként.  A parancssorban is megadható:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Arnold renderelés, a maja jelenet fájl általában meg Arnold, mint a renderelő.  A parancssorban is megadható:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Batch Explorer sablonok

A készlet- és feladatsablonok a **Tártár** a kötegelt intézőben elemből érhetők el.  A sablonforrás-fájlok a [GitHub Batch Explorer adattárában](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya)érhetők el.

## <a name="next-steps"></a>További lépések

Használja a készlet és a feladat sablonokat az [adattár a GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) a Batch Explorer használatával.  Szükség esetén hozzon létre új sablonokat, vagy módosítsa a megadott sablonok egyikét.