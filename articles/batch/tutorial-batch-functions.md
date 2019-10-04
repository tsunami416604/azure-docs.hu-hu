---
title: Eseményindító Batch-feladat, Azure Functions használatával
description: Oktatóanyag – OCR vonatkozik beolvasott dokumentumokat, a rendszer hozzáadja azt egy storage-blobba
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: d5a5197227ff62ca0c610e2c4e269480690d3faf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342063"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Oktatóanyag: Eseményindító Batch-feladat, Azure Functions használatával

Ebben az oktatóanyagban elsajátíthatja a Batch-feladat az Azure Functions szolgáltatással való lesz. Végigvesszük egy példa, amelyben egy Azure Storage blob-tárolóba felvett dokumentumok rendelkezik optikai karakterfelismerés (OCR) alkalmaz, az Azure Batch-n keresztül. Az optikai Karakterfelismerés feldolgozási egyszerűsítésére, konfigurálunk, egy Azure-függvényt, amely a Batch OCR feladatot futtat minden alkalommal, amikor egy fájlt adnak hozzá a blob-tároló.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
* Egy Azure Batch-fiók és egy társított Azure Storage-fiók. Lásd: [Batch-fiók létrehozása](quick-create-portal.md#create-a-batch-account) hogyan hozhat létre és -fiók összekapcsolása további tájékoztatást.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Hozzon létre egy Batch-készlet és a Batch-feladat a Batch Explorer használata

Ebben a szakaszban való hozza létre a Batch-készlet és a kötegelt feldolgozás OCR-feladatokat a Batch Explorer használni kívánt. 

### <a name="create-a-pool"></a>Készlet létrehozása

1. Jelentkezzen be hitelesítő adataival az Azure Batch Explorer.
1. Hozzon létre egy készlet kiválasztásával **készletek** a bal oldali sávon, majd a **Hozzáadás** gomb fölött a keresési képernyőn. 
    1. Válasszon egy Azonosítót, és megjelenítendő nevét. Fogjuk használni `ocr-pool` ebben a példában.
    1. A méretezési csoport típusa **rögzített méretű**, és állítsa be a dedikált csomópontok száma 3.
    1. Válassza ki **Ubuntu 18.04-LTS** operációs rendszerként.
    1. Válasszon `Standard_f2s_v2` , a virtuális gép méretét.
    1. Az indítási tevékenység engedélyezze és adja hozzá a a parancs `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`. Meg kell adni a felhasználó identitását, **feladat alapértelmezett felhasználó (rendszergazdai)** , amely lehetővé teszi, hogy a parancsok használata tartalmazza az indítási tevékenységeket `sudo`.
    1. Kattintson az **OK** gombra.
### <a name="create-a-job"></a>Feladat létrehozása

1. Hozzon létre egy feladatot a készleten kiválasztásával **feladatok** a bal oldali sávon, majd a **Hozzáadás** gomb fölött a keresési képernyőn. 
    1. Válasszon egy Azonosítót, és megjelenítendő nevét. Fogjuk használni `ocr-job` ebben a példában.
    1. Állítsa a készlet `ocr-pool`, vagy bármi nevét, a készlet számára is választott.
    1. Kattintson az **OK** gombra.


## <a name="create-blob-containers"></a>Blob tárolók létrehozása

Ide fog létrehozni, amely tárolja a bemeneti és kimeneti fájlok a OCR kötegelt blob-tárolók.

1. Jelentkezzen be hitelesítő adataival az Azure Storage Explorerben.
1. A Batch-fiókhoz társított tárfiókba használatával hozzon létre két blob-tárolók (egy bemeneti fájlokhoz, egyet a kimeneti fájlokat) a következő témakörben talál [hozzon létre egy blobtárolót](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

Ebben a példában a bemeneti tárolóba nevű `input` , és ahol minden dokumentum nélkül OCR kezdetben feltöltött feldolgozásra. A kimeneti tárolóhoz nevű `output` , és ahol a a kötegelt feldolgozás OCR-rel feldolgozott dokumentumok ír.  
    * Ebben a példában a bemeneti tárolóba Felhívjuk `input`, és a kimeneti tárolóhoz `output`.  
    * A bemeneti tárolóba, ahol minden dokumentumok nélkül OCR kezdetben feltöltésekor.  
    * A kimeneti tárolóhoz, ahol a Batch-feladat írja a dokumentumok OCR-rel.  

A kimeneti tároló közös hozzáférésű jogosultságkód létrehozása a Storage Explorerben. Ehhez kattintson a jobb gombbal a a kimeneti tárolóhoz, majd válasszon **közös hozzáférésű Jogosultságkód beolvasása...** . A **engedélyek**, ellenőrizze **írási**. Nincsenek más engedélyek szükségesek.  

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Ebben a szakaszban az Azure-függvény, amely elindítja a OCR kötegelt, amikor a bemeneti tárolóba bekerül egy fájl fog létrehozni.

1. Kövesse a [Azure blobtároló által aktivált függvény létrehozása](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) függvény létrehozásához.
    1. Ha a rendszer kéri egy tárfiók, használja ugyanazt a tárfiókot a Batch-fiókhoz csatolva.
    1. A **futtatókörnyezeti verem**, válassza ki a .NET. A függvény azt fog írni C# kihasználhatja a Batch .NET SDK-t.
1. A blob által aktivált függvény létrehozása után használja a [ `run.csx` ](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) és [ `function.proj` ](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) a Githubról a függvényben.
    * `run.csx` a bemeneti blob-tárolóba új blob hozzáadásakor futtatja.
    * `function.proj` a külső kódtárak a függvénykódban, például a Batch .NET SDK sorolja fel.
1. Módosítsa a változókat a helyőrző értékét az `Run()` funkcióját a `run.csx` fájlt, hogy a Batch- és storage hitelesítő adatait. Az Azure Portalon találhatja meg a Batch- és tárolási fiók hitelesítő adatait a **kulcsok** szakaszában a Batch-fiókhoz.
    * Beolvasni a Batch- és tárolási fiók hitelesítő adatait az Azure Portalon, a **kulcsok** szakaszában a Batch-fiókhoz. 

## <a name="trigger-the-function-and-retrieve-results"></a>Aktiválja a függvényt, és az eredmények lekérése

Töltse fel a vizsgált fájlok egy részének vagy egészének a [ `input_files` ](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) könyvtárat a Githubon a bemeneti tárolóba. Ellenőrizze, hogy egy feladat hozzáadása a Batch Explorer figyelése `ocr-pool` fájl esetében. Néhány másodperc elteltével a alkalmazni OCR-rel fájl kerül a kimeneti tárolóhoz. A fájl nem majd látható és a Storage Explorer lekérhető.

Emellett megtekinthet a naplók fájl alján, az Azure Functions Webalkalmazás-szerkesztő ablakban Itt láthatja az alábbihoz hasonló üzenetek minden fájlhoz a bemeneti tárolóba feltöltött:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-06-21T20:02:35.129 [Information] Name of output text file: <outputTxtFile>
2019-06-21T20:02:35.130 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

A kimeneti fájlok letöltése a Storage Explorer a helyi gépre, először válassza ki a kívánt fájlokat, majd válassza ki a **letöltése** a felső szalagon. 

> [!TIP]
> A letöltött fájlok kereshető akkor, ha a PDF-olvasó megnyitása.

## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket: 

> [!div class="checklist"]
> * Készletek és feladatok létrehozásához Batch Explorer használatával
> * Blob-tárolók és a egy közös hozzáférésű jogosultságkód (SAS) létrehozása a Storage Explorer használatával
> * Egy Azure blob által aktivált függvény létrehozása
> * Bemeneti fájlok feltöltése a Storage-ba
> * Tevékenységek végrehajtásának figyelése
> * Kimeneti fájlok lekérése

* .NET API használatával történő ütemezésére és feldolgozására a Batch számítási feladatot további példákért lásd [a mintákat a Githubon](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* További Azure Functions eseményindítók, amelyek segítségével a Batch számítási feladatok futtatásához, olvassa el [az Azure Functions dokumentációját](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).
