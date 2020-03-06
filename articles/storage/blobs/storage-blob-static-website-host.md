---
title: 'Oktatóanyag: Blob storage, Azure Storage a statikus webhely üzemeltetése'
description: Megtudhatja, hogyan konfigurálhatja a statikus webhely üzemeltetése egy storage-fiókját, és a egy statikus webhelyet üzembe helyezni az Azure Storage.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 03850315a05f569d2c6ba9405b6ec38bb6b1305d
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330395"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Oktatóanyag: Blob Storage-statikus webhely üzemeltetése

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és helyezhet üzembe statikus webhelyeket az Azure Storage-ban. Ha elkészült, kell, hogy a felhasználók nyilvánosan statikus webhely. 

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Konfigurálja a statikus webhely üzemeltetése
> * Webhely üzembe helyezése "Hello World"

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Mindenképpen hozzon létre egy általános célú v2-es standard Storage-fiókot. A statikus webhelyek semmilyen más típusú Storage-fiókban nem érhetők el.

Ez az oktatóanyag a [Visual Studio Code](https://code.visualstudio.com/download), a programozók számára ingyenes eszközt, a statikus webhely felépítését és az Azure Storage-fiókba való üzembe helyezését használja.

Miután telepítette a Visual Studio Code-ot, az Azure Storage előzetes bővítmény telepítése. Ez a bővítmény a Visual Studio Code integrálható a Azure Storage felügyeleti funkciókat. A bővítmény használatával fog a statikus webhely telepítése az Azure Storage. A bővítmény telepítéséhez:

1. Indítsa el a Visual Studio Code-ot.
2. Az eszköztáron kattintson a **bővítmények**elemre. Keressen rá az *Azure Storage*szolgáltatásra, és válassza ki az **Azure Storage** -bővítményt a listából. Ezután kattintson a **telepítés** gombra a bővítmény telepítéséhez.

    ![A VS Code-ban az Azure Storage-bővítményének telepítése](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A kezdéshez jelentkezzen be a [Azure Portalba](https://portal.azure.com/) .

## <a name="configure-static-website-hosting"></a>Konfigurálja a statikus webhely üzemeltetése

Az első lépés, hogy konfigurálja storage-fiókját az Azure Portalon statikus webhely üzemeltetésére. Ha a fiókját statikus webhely-üzemeltetésre konfigurálja, az Azure Storage automatikusan létrehoz egy *$web*nevű tárolót. A *$web* tároló a statikus webhely fájljait fogja tartalmazni. 

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) a böngészőben. 
1. Keresse meg a tárfiók, és megjeleníti a fiók áttekintése.
1. Válassza a **statikus webhely** lehetőséget a statikus webhelyek konfigurációs oldalának megjelenítéséhez.
1. Válassza az engedélyezve lehetőséget a statikus webhely-üzemeltetési **funkció** engedélyezéséhez a Storage-fiókhoz.
1. Az **index dokumentum neve** mezőben adja meg az *index. html*alapértelmezett index lapját. Az alapértelmezett index lap jelenik meg, amikor a felhasználók a statikus webhely gyökeréhez.  
1. A **hiba-dokumentum elérési útja** mezőben a *404. html*alapértelmezett hibaüzenetét kell megadnia. Amikor egy felhasználó megpróbál egy oldal, amely nem szerepel a statikus webhely keresse meg az alapértelmezett hibalap jelenik meg.
1. Kattintson a **Save** (Mentés) gombra. Az Azure Portalon mostantól megjeleníti a statikus webhely végpontot. 

    ![Statikus webhely üzemeltetése egy storage-fiók engedélyezése](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Webhely üzembe helyezése "Hello World"

Ezután hozzon létre egy "Hello World" weboldal a Visual Studio Code használatával, és helyezze üzembe azt a statikus webhely, az Azure Storage-fiókban tárolt.

1. Hozzon létre egy *mywebsite* nevű üres mappát a helyi fájlrendszerben. 
1. Indítsa el a Visual Studio Code-ot, és nyissa meg az imént létrehozott mappát az **Explorer** paneljén.

    ![Mappa megnyitása a Visual Studio Code-ban](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Hozza létre az alapértelmezett index fájlt a *mywebsite* mappában, és nevezze el az *index. html*nevet.

    ![Az alapértelmezett index fájl létrehozása a Visual Studio Code-ban](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Nyissa meg az *index. html* fájlt a szerkesztőben, illessze be a következő szöveget a fájlba, majd mentse:

    ```
    <h1>Hello World!</h1>
    ```

1. Hozza létre az alapértelmezett hibaüzenetet, és nevezze el *404. html*néven.
1. Nyissa meg a *404. html* fájlt a szerkesztőben, illessze be a következő szöveget a fájlba, majd mentse:

    ```
    <h1>404</h1>
    ```

1. Kattintson a jobb gombbal a *mywebsite* mappára az **Explorer** panelen, és válassza a **üzembe helyezés statikus webhelyre** ... lehetőséget a webhely üzembe helyezéséhez. A rendszer bekéri jelentkezzen be Azure-előfizetések listájának lekéréséhez.

1. Válassza ki, amelyhez statikus webhelyüzemeltetésre engedélyezve a storage-fiókot tartalmazó előfizetést. Ezután válassza ki a tárfiókot, amikor a rendszer kéri.

A Visual Studio Code most a fájlok feltöltése a webes végpontra, és a sikeres állapotsor megjelenítése. Indítsa el a webhelyet, megtekintheti azokat az Azure-ban.

Sikeresen elvégezte az oktatóanyagot, és üzembe helyezett egy statikus webhelyet az Azure-ban.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan konfigurálhatja az Azure Storage-fiókját a statikus webhelyek üzemeltetéséhez, és hogyan hozhat létre és helyezhet üzembe statikus webhelyeket egy Azure-végponton.

Következő lépésként megtudhatja, hogyan konfigurálhat egy egyéni tartományt a statikus webhellyel.

> [!div class="nextstepaction"]
> [Egyéni tartomány leképezése egy Azure Blob Storage-végpontra](storage-custom-domain-name.md)
