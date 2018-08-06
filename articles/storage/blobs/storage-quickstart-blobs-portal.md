---
title: Azure rövid útmutató – Blob létrehozása objektumtárban az Azure Portal használatával | Microsoft Docs
description: Ebben a rövid útmutatóban az Azure Portal használatával kezelheti az objektumtárat (blobtárat). Majd az Azure Portal segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: tamram
ms.openlocfilehash: c3f281532260cfc895ecea56666260a1df1fb0c0
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39400501"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-portal"></a>Rövid útmutató: blobok feltöltése, letöltése és listázása az Azure Portal használatával

Ez a rövid útmutató azt ismerteti, hogyan használható az [Azure Portal](https://portal.azure.com/) egy Azure Storage-tároló létrehozására, valamint blokkblobok fel- és letöltésére a tárolóban.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="create-a-container"></a>Tároló létrehozása

Az Azure Portalon a következő lépések végrehajtásával hozhat létre egy tárolót:

1. Az Azure Portalon lépjen az új tárfiókjára.
2. A tárfiók bal oldali menüjében görgessen a **Blob service** szakaszhoz, majd válassza a **Blobok tallózása** lehetőséget.
3. Kattintson a **Tároló hozzáadása** gombra.
4. Adja meg az új tároló nevét. A tároló neve csak kisbetűket tartalmazhat, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat. A tárolók és blobok elnevezésével kapcsolatos részletekért lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.
5. Adja meg a tároló nyilvános hozzáférési szintjét. Az alapértelmezett szint: **Privát (nincs névtelen hozzáférés)**.
6. A tároló létrehozásához kattintson az **OK** gombra.

    ![A tárolók Azure Portalon történő létrehozását bemutató képernyőkép](media/storage-quickstart-blobs-portal/create-container.png)

## <a name="upload-a-block-blob"></a>Blokkblob feltöltése

A blokkblobok blobokká összefűzött adatblokkokból állnak. A Blob Storage legtöbb használati forgatókönyvében blokkblobokat kell használni. A blokkblobok ideális megoldást kínálnak szöveges és bináris adatok (például fájlok, képek és videók) felhőben történő tárolására. Ez a rövid útmutató a blokkblobok használatát mutatja be. 

Az Azure Portalon a következő lépések végrehajtásával tölthet fel egy blokkblobot az Azure Portalon lévő új tárolóba:

1. Az Azure Portalon lépjen az előző szakaszban létrehozott tárolóhoz.
2. Válassza ki a tárolót a benne lévő blobok listájának megtekintéséhez. Mivel ebben az esetben egy új tárolót hozott létre, még egy blob sem található benne.
3. A **Feltöltés** gombra kattintva tölthet fel egy blobot a tárolóba.
4. A helyi fájlrendszerben keresse meg tallózással a blokkblobként feltölteni kívánt fájlt, és kattintson a **Feltöltés** elemre.
     
    ![A blobok helyi meghajtóról történő feltöltését bemutató képernyőkép](media/storage-quickstart-blobs-portal/upload-blob.png)

5. Ezzel a módszerrel tetszőleges számú blobot tölthet fel. Látni fogja, hogy az új blobok már fel vannak sorolva a tárolóban.

    ![Képernyőkép a tárolóban található blobok listájáról](media/storage-quickstart-blobs-portal/list-blobs.png)

## <a name="download-a-block-blob"></a>Blokkblob letöltése

A blokkblobok letölthetők a böngészőben történő megjelenítéshez vagy a helyi fájlrendszerben történő mentéshez. A blokkblobok letöltéséhez kövesse az alábbi lépéseket:

1. Lépjen az előző szakaszban feltöltött blobok listájához. 
2. Válassza ki a letölteni kívánt blobot.
3. Kattintson a jobb gombbal a **Továbbiak** gombra (**...**), és válassza a **Letöltés** lehetőséget. 

![A blobok Azure Portalon való letöltését bemutató képernyőkép](media/storage-quickstart-blobs-portal/download-blob.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen rövid útmutatóban létrehozott erőforrások eltávolításához egyszerűen törölje a tárolót. Ekkor a rendszer törli az adott tárolóban lévő összes blobot is.

A tároló törlése:

1. Az Azure Portalon lépjen a tárfiókban tárolóinak listájához.
2. Válassza ki a törölni kívánt tárolót.
3. Kattintson a jobb gombbal a **Továbbiak** gombra (**...**), és válassza a **Törlés** lehetőséget.
4. Erősítse meg, hogy törölni kívánja a tárolót.

    ![A tárolók Azure Portalról történő törlését bemutató képernyőkép](media/storage-quickstart-blobs-portal/delete-container.png)   

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között a .NET használatával. Ha bővebb információra van szüksége a Blob Storage használatával kapcsolatban, lépjen tovább a Blob Storage használati útmutatójára.

> [!div class="nextstepaction"]
> [Blob Storage-műveletek használati útmutatója](storage-dotnet-how-to-use-blobs.md)

