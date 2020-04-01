---
title: 'Oktatóanyag: Statikus webhely üzemeltetése a Blob storage-ban – Azure Storage'
description: Ismerje meg, hogyan konfigurálhat egy tárfiókot a statikus webhelyüzemeltetéséhez, és hogyan helyezhet üzembe egy statikus webhelyet az Azure Storage-ba.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 03850315a05f569d2c6ba9405b6ec38bb6b1305d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78330395"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Oktatóanyag: Statikus webhely üzemeltetése a Blob Storage webhelyen

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre és helyezhet üzembe statikus webhelyet az Azure Storage-ba. Ha végzett, lesz egy statikus webhelye, amelyet a felhasználók nyilvánosan elérhetnek. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Statikus webhely-üzemeltetés konfigurálása
> * A Hello World webhely üzembe helyezése

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Győződjön meg arról, hogy hozzon létre egy általános célú v2 standard tárfiókot. A statikus webhelyek nem érhetők el más típusú tárfiókban.

Ez az oktatóanyag a [Visual Studio Code programot](https://code.visualstudio.com/download)használja, amely egy ingyenes eszköz a programozók számára a statikus webhely létrehozásához és egy Azure Storage-fiókba való üzembe helyezéséhez.

A Visual Studio-kód telepítése után telepítse az Azure Storage előzetes bővítményét. Ez a bővítmény integrálja az Azure Storage felügyeleti funkcióit a Visual Studio-kóddal. A bővítmény használatával telepíti a statikus webhelyet az Azure Storage-ba. A bővítmény telepítése:

1. Indítsa el a Visual Studio Code-ot.
2. Az eszköztáron kattintson a **Bővítmények gombra.** Keressen az *Azure Storage,* és válassza ki az **Azure Storage-bővítményt** a listából. Ezután kattintson a **Telepítés** gombra a bővítmény telepítéséhez.

    ![Az Azure Storage-bővítmény telepítése a VS Code szolgáltatásban](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A kezdéshez jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="configure-static-website-hosting"></a>Statikus webhely-üzemeltetés konfigurálása

Az első lépés az, hogy konfigurálja a tárfiókot egy statikus webhely üzemeltetéséhez az Azure Portalon. Amikor a fiókját statikus webhely-üzemeltetésre konfigurálja, az Azure Storage automatikusan létrehoz egy *$web*nevű tárolót. A *$web* tároló tartalmazza a statikus webhely fájljait. 

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/) a webböngészőben. 
1. Keresse meg a tárfiókot, és jelenítse meg a fiók áttekintését.
1. Válassza **a Statikus webhely** lehetőséget a statikus webhelyek konfigurációs lapjának megjelenítéséhez.
1. Válassza **az Engedélyezve lehetőséget** a statikus webhely-üzemeltetés engedélyezéséhez a tárfiókhoz.
1. A **Dokumentum indexelése mezőben** adja meg az *index.html*alapértelmezett indexlapját. Az alapértelmezett indexlap akkor jelenik meg, amikor a felhasználó a statikus webhely gyökerére navigál.  
1. A **Hiba dokumentum elérési útja** mezőben adja meg a *404.html*alapértelmezett hibalapját. Az alapértelmezett hibalap akkor jelenik meg, amikor a felhasználó olyan oldalra próbál navigálni, amely nem létezik a statikus webhelyen.
1. Kattintson a **Mentés** gombra. Az Azure Portal most megjeleníti a statikus webhely-végpont. 

    ![Statikus webhely-üzemeltetés engedélyezése tárfiókhoz](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>A Hello World webhely üzembe helyezése

Ezután hozzon létre egy Hello World weboldalt a Visual Studio-kóddal, és telepítse az Azure Storage-fiókjában üzemeltetett statikus webhelyre.

1. Hozzon létre egy saját *webhely* nevű üres mappát a helyi fájlrendszerben. 
1. Indítsa el a Visual Studio-kódot, és nyissa meg az imént létrehozott mappát az **Intéző** panelről.

    ![Mappa megnyitása a Visual Studio-kódban](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Hozza létre az alapértelmezett indexfájlt a *saját webhely* mappájában, és adja el *az index.html névnek.*

    ![Az alapértelmezett indexfájl létrehozása a Visual Studio-kódban](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Nyissa meg az *index.html fájlt* a szerkesztőben, illessze be a következő szöveget a fájlba, és mentse azt:

    ```
    <h1>Hello World!</h1>
    ```

1. Hozza létre az alapértelmezett hibafájlt, és nevezze el *404.html*.
1. Nyissa meg a *404.html* fájlt a szerkesztőben, illessze be a következő szöveget a fájlba, és mentse azt:

    ```
    <h1>404</h1>
    ```

1. Kattintson a jobb gombbal az **Intéző** panel *saját webhely* mappája alatt, és válassza a **Telepítés statikus webhelyre lehetőséget a** webhely telepítéséhez. A rendszer kéri, hogy jelentkezzen be az Azure-ba az előfizetések listájának lekéréséhez.

1. Válassza ki azt az előfizetést, amely azt a tárfiókot tartalmazza, amelyhez engedélyezte a statikus webhelyüzemeltetését. Ezután válassza ki a tárfiókot, amikor a rendszer kéri.

A Visual Studio-kód most feltölti a fájlokat a webes végpontra, és megjeleníti a sikeres állapotsort. Indítsa el a webhelyet, hogy megtekinthesse az Azure-ban.

Sikeresen befejezte az oktatóanyagot, és üzembe helyezett egy statikus webhelyet az Azure-ba.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan konfigurálhatja az Azure Storage-fiókot a statikus webhely-üzemeltetéshez, és hogyan hozhat létre és helyezhet üzembe statikus webhelyet egy Azure-végpontra.

Ezután ismerje meg, hogyan konfigurálhat egyéni tartományt a statikus webhelyével.

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzárendelése Az Azure Blob Storage-végponthoz](storage-custom-domain-name.md)
