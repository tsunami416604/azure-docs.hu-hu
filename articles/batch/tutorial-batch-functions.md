---
title: Kötegelt feladat aktiválása az Azure Functions használatával
description: Oktatóanyag – OCR alkalmazása a beolvasott dokumentumokra, amint hozzávannak adva egy tárolóblobhoz
author: LauraBrenner
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: a967fdc14b85f294ee11cbcc57a8d2280dba38e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77017190"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Oktatóanyag: Kötegelt feladat aktiválása az Azure Functions használatával

Ebben az oktatóanyagban megtudhatja, hogyan indíthatja el a kötegelt feladatot az Azure Functions használatával. Egy példát mutatunk be, amelyben az Azure Storage blobtárolóhoz hozzáadott dokumentumok optikai karakterfelismeréssel (OCR) rendelkeznek az Azure Batch-en keresztül. Az OCR-feldolgozás egyszerűsítése érdekében konfigurálunk egy Azure-függvényt, amely egy Batch OCR-feladatot futtat minden alkalommal, amikor egy fájlt hozzáadanak a blobtárolóhoz.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik ilyen, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* Egy Azure Batch-fiók és egy társított Azure Storage-fiók. A fiókok létrehozásáról és csatolásáról a [Kötegfiók létrehozása](quick-create-portal.md#create-a-batch-account) című témakörben talál további információt.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Kötegkészlet és kötegelt feldolgozás létrehozása a Batch Explorer használatával

Ebben a szakaszban a Batch Explorer segítségével hozza létre az OCR-feladatokat futtató kötegkészletet és kötegelt feldolgozást. 

### <a name="create-a-pool"></a>Készlet létrehozása

1. Jelentkezzen be a Batch-kezelőbe az Azure-hitelesítő adatok használatával.
1. Hozzon létre egy készletet a bal oldali sávon lévő **Készletek,** majd a keresési űrlap feletti **Hozzáadás** gomb kiválasztásával. 
    1. Válasszon azonosítót és megjelenítendő nevet. Ezt a `ocr-pool` példát fogjuk használni.
    1. Állítsa a méretezési típust **Rögzített méretre,** és állítsa a dedikált csomópontszámot 3-ra.
    1. Válassza az **Ubuntu 18.04-LTS operációs** rendszert.
    1. Válassza `Standard_f2s_v2` ki a virtuális gép méretét.
    1. Engedélyezze a kezdő feladatot, és adja hozzá a parancsot `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`. Ügyeljen arra, hogy a felhasználói identitást **feladat alapértelmezett felhasználóként (rendszergazdaként)** állítsa be, amely lehetővé teszi, hogy az indítási feladatok parancsokat tartalmazzanak a alkalmazással. `sudo`
    1. Válassza **az OK gombot.**
### <a name="create-a-job"></a>Feladat létrehozása

1. Hozzon létre egy feladatot a készleten a bal oldali sávon lévő **Feladatok,** majd a keresési űrlap feletti **Hozzáadás** gomb kiválasztásával. 
    1. Válasszon azonosítót és megjelenítendő nevet. Ezt a `ocr-job` példát fogjuk használni.
    1. Állítsa a `ocr-pool`készletet a, vagy bármilyen nevet, amelyet a készlethez választott.
    1. Válassza **az OK gombot.**


## <a name="create-blob-containers"></a>Blob-tárolók létrehozása

Itt hozhat létre blobtárolókat, amelyek az OCR kötegelt feladat bemeneti és kimeneti fájljait tárolják.

1. Jelentkezzen be a Storage Explorerbe az Azure-hitelesítő adatokhasználatával.
1. A Batch-fiókhoz kapcsolódó tárfiók használatával hozzon létre két blobtárolót (egyet a bemeneti fájlokhoz, egyet a kimeneti fájlokhoz) a [Blob-tároló létrehozása](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container)című szakasz lépéseit követve.

Ebben a példában a `input` bemeneti tároló neve, és ahol az összes dokumentumot nem OCR eredetileg feltöltött feldolgozásra. A kimeneti `output` tároló neve, és a kötegelt feladat az OCR-rel feldolgozott dokumentumokat írja.  
    * Ebben a példában a bemeneti `input`tárolót és `output`a kimeneti tárolót hívjuk meg.  
    * A bemeneti tároló, ahol az összes dokumentumot nem OCR kezdetben feltöltött.  
    * A kimeneti tároló, ahol a kötegelt feladat írási dokumentumok OCR.  

Hozzon létre egy megosztott hozzáférési aláírást a kimeneti tárolóhoz a Storage Explorer ben. Ehhez kattintson a jobb gombbal a kimeneti tárolóra, és válassza a **Megosztott hozzáférés aláírása beszerezni...**. Az **Engedélyek csoportban**jelölje be az **Írás**jelölőnégyzetet. Nincs szükség más engedélyekre.  

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Ebben a szakaszban hozza létre az Azure-függvény, amely elindítja az OCR kötegelt feladat, amikor egy fájl feltöltése a bemeneti tárolóba.

1. Kövesse az [Azure Blob storage által aktivált függvény létrehozása](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) című lépéseit egy függvény létrehozásához.
    1. Amikor a rendszer egy tárfiókot kér, használja ugyanazt a tárfiókot, amelyet a Batch-fiókhoz kapcsolt.
    1. A **futásidejű verem**hez válassza a .NET lehetőséget. A függvényt C# -ban írjuk, hogy kihasználhassuk a Batch .NET SDK-t.
1. A blob által aktivált függvény létrehozása [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) után [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) használja a és a GitHub a függvényben.
    * `run.csx`akkor fut, amikor egy új blob ot ad hozzá a bemeneti blob tárolóhoz.
    * `function.proj`felsorolja a külső könyvtárakat a függvénykódban, például a Batch .NET SDK-t.
1. Módosítsa a változók helyőrző értékeit `Run()` a `run.csx` fájl függvényében, hogy azok tükrözzék a kötegelt és tárolási hitelesítő adatokat. A Batch- és tárfiók hitelesítő adatait az Azure Portalon találhatja meg a Batch-fiók **Kulcsok** szakaszában.
    * A Batch- és tárfiók hitelesítő adatainak lekérése az Azure Portalon a Batch-fiók **Kulcsok** szakaszában. 

## <a name="trigger-the-function-and-retrieve-results"></a>A függvény aktiválása és az eredmények beolvasása

Töltse fel a beolvasott fájlokat [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) a GitHub könyvtárából a bemeneti tárolóba. A Batch Explorer figyelése annak `ocr-pool` ellenőrzéséhez, hogy egy feladat minden fájlhoz hozzáadódik-e. Néhány másodperc múlva a fájl OCR alkalmazott hozzáadódnak a kimeneti tárolóhoz. A fájl ezután látható és visszakereshető a Storage Explorer programban.

Ezenkívül megtekintheti az Azure Functions webszerkesztő ablakának alján található naplófájlt, ahol az ilyen üzeneteket láthatja a bemeneti tárolóba feltöltött minden fájlhoz:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Ha le szeretné tölteni a kimeneti fájlokat a Storage Explorer programból a helyi számítógépre, először jelölje ki a kívánt fájlokat, majd válassza a **Letöltés** lehetőséget a felső menüszalagon. 

> [!TIP]
> A letöltött fájlok kereshetőek, ha PDF-olvasóban nyitják meg őket.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Gyűjtők és feladatok létrehozása kötegelt intézővel
> * Blobtárolók és megosztott hozzáférésű aláírás (SAS) létrehozása a Storage Explorer használatával
> * Blob által aktivált Azure-függvény létrehozása
> * Bemeneti fájlok feltöltése a Storage-ba
> * Tevékenységek végrehajtásának figyelése
> * Kimeneti fájlok lekérése

* A .NET API használatával ütemezheti és dolgozhatja fel a Batch-munkaterhelések használatát, tekintse meg [a mintákat a GitHubon.](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) 

* További Azure Functions-eseményindítók megtekintéséhez, amelyek segítségével futtathatja a Batch számítási feladatok, tekintse meg [az Azure Functions dokumentációját.](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings)
