---
title: Renderelési képességek használata - Azure Batch
description: Az Azure Batch leképezési képességeinek használata. Próbálja meg a Batch Explorer alkalmazást közvetlenül vagy egy ügyfélalkalmazás beépülő moduljából meghívni.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 03/05/2020
ms.topic: conceptual
ms.openlocfilehash: f3b2e641ab187514a7900b2ab7cc75068df00252
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672011"
---
# <a name="using-azure-batch-rendering"></a>Az Azure Batch rendereléshasználata

Az Azure Batch renderelése számos módon használható:

* API-k:
  * Kódot írhat a Batch API-k bármelyikével.  A fejlesztők integrálhatják az Azure Batch-képességeket meglévő alkalmazásaikba vagy munkafolyamataikba, akár felhőben, akár helyszíni környezetben.
* Parancssori eszközök:
  * Az [Azure parancssori vagy](https://docs.microsoft.com/cli/azure/) [PowerShell](https://docs.microsoft.com/powershell/azure/overview) parancsfájl batch használatra használható.
  * A Batch [CLI sablon támogatása](https://docs.microsoft.com/azure/batch/batch-cli-templates) különösen megkönnyíti a gyűjtők létrehozását és a feladatok elküldését.
* Batch Explorer felhasználói felülete:
  * [A Batch Explorer](https://github.com/Azure/BatchLabs) egy platformfüggetlen ügyféleszköz, amely lehetővé teszi a Batch-fiókok kezelését és figyelését is.
  * Minden renderelési alkalmazáshoz számos készlet- és feladatsablon áll rendelkezésre, amelyek segítségével könnyen létrehozhat készleteket és küldhet el feladatokat.  A sablonok egy készlete szerepel az alkalmazás felhasználói felületén, a sablonfájlok a GitHubról érhetők el.
  * Az egyéni sablonok teljesen létrehozhatóak, vagy a GitHubhoz mellékelt sablonok másolhatók és módosíthatók.
* Ügyfélalkalmazás beépülő moduljai:
  * Beépülő modulok állnak rendelkezésre, amelyek lehetővé teszik a Batch renderelés takarásos használható közvetlenül az ügyfél tervezési és modellezési alkalmazások.  A beépülő modulok főként a Batch Explorer alkalmazást hívja meg az aktuális 3D modell kontextuális információival, és olyan funkciókat tartalmaznak, amelyek segítenek az eszközök kezeléséhez.

A legjobb módja annak, hogy megpróbálja az Azure Batch renderelés és a legegyszerűbb módja a végfelhasználók, akik nem fejlesztők, és nem Azure-szakértők, hogy használja a Batch Explorer alkalmazást, akár közvetlenül, vagy egy ügyfélalkalmazás beépülő modul.

## <a name="using-batch-explorer"></a>A Batch Explorer használata

A Batch Explorer rendereléshez való használatáról a [Blender oktatóanyaga](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)című témakörben részletes oktatóanyagról van szó.

### <a name="download-and-install"></a>Letöltés és telepítés

A Batch Explorer [letöltései](https://azure.github.io/BatchExplorer/) Windows, OSX és Linux rendszeren érhetők el.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Sablonok használata készletek létrehozásához és feladatok futtatásához

A Batch Explorer segítségével átfogó sablonok állnak rendelkezésre, amelyek megkönnyítik a készletek létrehozását és a különböző renderelési alkalmazások feladatainak elküldését anélkül, hogy meg kellene adniuk a készletek, feladatok és feladatok közvetlen létrehozásához szükséges összes tulajdonságot Kötegelt.  A Batch Explorerben elérhető sablonok tárolása és láthatóvá válik [egy GitHub-tárházban.](https://github.com/Azure/BatchExplorer-data/tree/master/ncj)

![Kötegelt intéző gyűjteménye](./media/batch-rendering-using/batch-explorer-gallery.png)

Sablonok vannak biztosítva, amely gondoskodik a Marketplace-renderelés virtuálisgép-lemezképeken található összes alkalmazás.  Minden alkalmazáshoz több sablon létezik, beleértve a CPU- és GPU-készletek, a Windows és a Linux készletek számára szolgáló készletsablonokat; A feladatsablonok közé tartozik a teljes képkocka- vagy csempézett Turmixgép-renderelés és a V-Ray elosztott renderelés. A mellékelt sablonok készlete idővel ki bővül, hogy kielégedjen más Batch-képességekkel, például a készlet automatikus méretezésével.

Lehetőség van egyéni sablonok létrehozására is, a semmiből vagy a megadott sablonok módosításával. Az egyéni sablonok a Batch Explorer "Galéria" részében található "Helyi sablonok" elem kiválasztásával használhatók.

### <a name="file-system-and-data-movement"></a>Fájlrendszer és adatmozgás

A Batch Explorer "Adatok" szakasza lehetővé teszi a fájlok másolását a helyi fájlrendszer és az Azure Storage-fiókok között.

## <a name="client-application-plug-ins"></a>Ügyfélalkalmazás beépülő moduljai

Beépülő modulok érhetők el néhány ügyfélalkalmazáshoz.  A beépülő modulok lehetővé teszik, hogy a készletek és a feladatok közvetlenül az alkalmazásból jönnek létre, vagy meghívja a Batch Explorert.

* [Turmixgép 2,79](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Turmixgép 2,8+](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender28)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>További lépések

A Batch renderelés példáit próbálja ki a két oktatóanyag:

* [Renderelés az Azure CLI használatával](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Renderelés a Batch Explorer használatával](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)