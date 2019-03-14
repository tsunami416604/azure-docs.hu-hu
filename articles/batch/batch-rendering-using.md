---
title: Funkciók – Azure Batch Rendering
description: Azure Batch renderelési képességeket használata
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 2dff44f0b5b4b02c39c4c63f23ff64d55ca9d833
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57789636"
---
# <a name="using-azure-batch-rendering"></a>Az Azure Batch rendering használatával

Többféleképpen is lehet használni az Azure Batch rendering:

* API-kat:
  * A Batch API-k használatával kód írása.  A fejlesztők beépíthetik az Azure Batch-funkciók a meglévő alkalmazások vagy a munkafolyamat, hogy felhőbeli vagy helyszíni alapján.
* Parancssori eszközök:
  * A [Azure parancssori felületével](https://docs.microsoft.com/cli/azure/) vagy [PowerShell](https://docs.microsoft.com/powershell/azure/overview) használható parancsfájl a Batch használatát.
  * Ilyen például a [Batch parancssori felületi sablon támogatása](https://docs.microsoft.com/azure/batch/batch-cli-templates) egyszerűen alkalmazhatók a készletek létrehozásához és feladatok elküldéséhez.
* A Batch Explorer felhasználói felületén:
  * [A Batch Explorer](https://github.com/Azure/BatchLabs) , kezelhető és figyelhető a Batch-fiókok lehetővé teszi többplatformos ügyféleszköz.
  * A renderelési alkalmazások mindegyike esetében a készlet és -feladat sablonok számos olyan megadott, amely használható, könnyedén hozhat létre a készletek és feladatok elküldéséhez.  Az alkalmazás felhasználói felületén, sablonok készletét szerepel a GitHub hozzáférnek a sablonfájlokat.
  * Egyéni sablonokat hozhat létre teljesen vagy a megadott sablonok GitHub-másolható, és módosítani.
* Ügyféloldali alkalmazás modulok:
  * Beépülő modulok érhetők el, amelyek engedélyezik a Batch rendering közvetlenül az ügyfél szakterület való használatra, és modellező alkalmazásokban.  A beépülő modulok főként meghívni a Batch Explorer alkalmazás a jelenlegi 3D modell környezeti információkat és szolgáltatásokat tartalmaz, amelyek az eszközök kezeléséhez.

A legjobb módszer az Azure Batch rendering és legegyszerűbb módja a végfelhasználók számára, akik nem fejlesztők és a nem Azure-szakértőket, a Batch Explorer alkalmazás használatát, vagy közvetlenül, vagy a beépülő modul ügyfélalkalmazás meghívása.

## <a name="using-batch-explorer"></a>A Batch Explorer használata

A Batch Explorer segítségével hajtsa végre a renderelési lásd: részletes útmutató a [Blender oktatóanyag](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender).

### <a name="download-and-install"></a>Töltse le és telepítse

A Batch Explorer [érhető el](https://azure.github.io/BatchExplorer/) Windows, OSX és Linux rendszeren.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Sablonok használata a készletek létrehozása és a feladatok futtatása

A sablonok egy átfogó érhető el, amely megkönnyíti a készletek létrehozásához és elküldéséhez a feladatok a különböző renderelési alkalmazások készletek létrehozásához szükséges összes tulajdonság megadása nélkül, feladatok és tevékenységek közvetlenül a Batch Explorer használata A Batch.  A Batch Explorer a rendelkezésre álló sablonok is látható, és tárolt [egy GitHub-adattár](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![A Batch Explorer gyűjteménye](./media/batch-rendering-using/batch-explorer-gallery.png)

A sablonok olyan, feltéve, hogy gondoskodjon arról, hogy az alkalmazások a Marketplace-en renderelési Virtuálisgép-rendszerképek megtalálható összes.  Több sablon létezik minden alkalmazáshoz, beleértve a készlet sablonok gondoskodjon arról, hogy a Processzor és GPU-készletek, Windows és Linux-készlet; feladat sablonok Blender renderelésre és a V-Ray elosztott renderelési mozaik elrendezésű, vagy a teljes keret tartoznak. A megadott sablonok készletét kibontásra váró idővel méretformátumok figyelembe vétele, más Batch képességei, például a készlet automatikus méretezését.

Emellett lehetőség az egyéni sablonokhoz előállított, teljesen új vagy a megadott sablonok módosításával. Egyéni sablonok Batch Explorer "Gyűjtemény" szakaszában a "Helyi sablonok"-elem kiválasztásával használható.

### <a name="file-system-and-data-movement"></a>Rendszer- és adatátviteli fájl

A Batch Explorer "Adatok" című szakaszában lehetővé teszi, hogy a fájlok egy helyi fájlrendszer és az Azure Storage-fiókok között kell másolni.

## <a name="client-application-plug-ins"></a>Ügyféloldali alkalmazás beépülő modulok

Beépülő modulok egyes az ügyfélalkalmazások számára érhetők el.  A beépülő modulok lehetővé teszik a készletek és feladatok hozhatók létre közvetlenül az alkalmazásból, vagy hívja a Batch Explorer.

* [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>További lépések

A példák a Batch rendering próbálja ki a két oktatóanyagok:

* [Renderelés az Azure CLI használatával](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Renderelés, a Batch Explorer használata](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)