---
title: 'Oktatóanyag: A Blob storage, Azure Storage statikus webhely üzemeltetése'
description: Megtudhatja, hogyan konfigurálhatja a statikus webhely üzemeltetése egy storage-fiókját, és a egy statikus webhelyet üzembe helyezni az Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: tamram
ms.custom: seodec18
ms.openlocfilehash: eb472465c0d35150f2a13563058905751219411d
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976460"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Oktatóanyag: A Blob Storage statikus webhely üzemeltetése

Ez az oktatóanyag egy sorozat első része. Abban megismerheti, hogyan készíthet és helyezhet üzembe egy statikus webhely az Azure Storage. Ha elkészült, kell, hogy a felhasználók nyilvánosan statikus webhely. 

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Konfigurálja a statikus webhely üzemeltetése
> * Webhely üzembe helyezése "Hello World"

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Ebben az oktatóanyagban [Visual Studio Code](https://code.visualstudio.com/download), egy ingyenes eszköz programozóknak a statikus webhely létrehozása és üzembe helyezése az Azure Storage-fiókot.

Miután telepítette a Visual Studio Code-ot, az Azure Storage előzetes bővítmény telepítése. Ez a bővítmény a Visual Studio Code integrálható a Azure Storage felügyeleti funkciókat. A bővítmény használatával fog a statikus webhely telepítése az Azure Storage. A bővítmény telepítéséhez:

1. Indítsa el a Visual Studio Code-ot.
2. Kattintson az eszköztár **bővítmények**. Keresse meg *Azure Storage*, és válassza ki a **Azure Storage** bővítmény a listából. Kattintson a **telepítése** gomb a bővítmény telepítésére.

    ![A VS Code-ban az Azure Storage-bővítményének telepítése](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be a [az Azure portal](https://portal.azure.com/) a kezdéshez.

## <a name="configure-static-website-hosting"></a>Konfigurálja a statikus webhely üzemeltetése

Az első lépés, hogy konfigurálja storage-fiókját az Azure Portalon statikus webhely üzemeltetésére. A fiók statikus webhelyüzemeltetésre konfigurálja, az Azure Storage automatikusan létrehoz egy tárolót *$web*. A *$web* tároló a statikus webhely-fájlokat tartalmazza. 

1. Nyissa meg a [az Azure portal](https://portal.azure.com/) a böngészőben. 
1. Keresse meg a tárfiók, és megjeleníti a fiók áttekintése.
1. Válassza ki **statikus webhely** statikus webhelyek a konfigurációs lap megjelenítéséhez.
1. Válassza ki **engedélyezve** statikus webhelyüzemeltetésre a storage-fiók engedélyezése.
1. Az a **Indexdokumentum nevének** mezőben adja meg egy alapértelmezett indexet lapján *index.html*. Az alapértelmezett index lap jelenik meg, amikor a felhasználók a statikus webhely gyökeréhez.  
1. Az a **hibadokumentum elérési útjának** mezőben adja meg, egy alapértelmezett hibalap *404. html*. Amikor egy felhasználó megpróbál egy oldal, amely nem szerepel a statikus webhely keresse meg az alapértelmezett hibalap jelenik meg.
1. Kattintson a **Save** (Mentés) gombra. Az Azure Portalon mostantól megjeleníti a statikus webhely végpontot. 

    ![Statikus webhely üzemeltetése egy storage-fiók engedélyezése](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Webhely üzembe helyezése "Hello World"

Ezután hozzon létre egy "Hello World" weboldal a Visual Studio Code használatával, és helyezze üzembe azt a statikus webhely, az Azure Storage-fiókban tárolt.

1. Hozzon létre egy nevű üres mappát *segítségével* a helyi fájlrendszerben. 
1. Indítsa el a Visual Studio Code-ot, és nyissa meg a mappát, amely az imént létrehozott a **Explorer** panel.

    ![Mappa megnyitása a Visual Studio Code-ban](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Az alapértelmezett index fájlt létrehozni a a *segítségével* mappát, és nevezze el *index.html*.

    ![Az alapértelmezett index fájl létrehozása a Visual Studio Code-ban](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Nyissa meg *index.html* a szerkesztőben, illessze be a következő szöveget a fájlba, és mentse:

    ```
    <h1>Hello World!</h1>
    ```

1. Hozzon létre az alapértelmezett hiba történt a fájl *404. html*.
1. Nyissa meg *404. html* a szerkesztőben, illessze be a következő szöveget a fájlba, és mentse:

    ```
    <h1>404</h1>
    ```

1. Területen kattintson a jobb gombbal a *segítségével* mappájában a **Explorer** panelen, majd válassza ki **üzembe helyezés az statikus webhely...**  , a webhelyet üzembe helyezni. A rendszer bekéri jelentkezzen be Azure-előfizetések listájának lekéréséhez.

1. Válassza ki, amelyhez statikus webhelyüzemeltetésre engedélyezve a storage-fiókot tartalmazó előfizetést. Ezután válassza ki a tárfiókot, amikor a rendszer kéri.

A Visual Studio Code most a fájlok feltöltése a webes végpontra, és a sikeres állapotsor megjelenítése. Indítsa el a webhelyet, megtekintheti azokat az Azure-ban.

![Statikus webhely-üzembehelyezési megtekintése az Azure-ban](media/storage-blob-static-website-host/view-static-website-endpoint.png)

Az oktatóanyag teljesítése sikeres és a egy statikus webhely telepítése az Azure-bA.

## <a name="next-steps"></a>További lépések

Az első rész a jelen oktatóanyag bemutatta, statikus webhely üzemeltetése az Azure Storage-fiók konfigurálása, és hogyan hozhat létre, és a egy statikus webhely üzembe helyezése egy Azure-végpont.

Most folytassa második, itt konfigurálhatja egy egyéni tartomány SSL-lel az Azure CDN a statikus webhely.

> [!div class="nextstepaction"]
> [Statikus webhely egyéni tartomány SSL engedélyezése az Azure CDN használatával](storage-blob-static-website-custom-domain.md)
