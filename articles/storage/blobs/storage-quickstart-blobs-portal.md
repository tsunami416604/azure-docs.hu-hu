---
title: Rövid útmutató – blob létrehozása a Azure Portal
titleSuffix: Azure Storage
description: Ebben a rövid útmutatóban az Azure Portal használatával kezelheti az objektumtárat (blobtárat). Majd az Azure Portal segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 04/16/2020
ms.author: tamram
ms.openlocfilehash: a9a048801c6bea74e6b5318eeedf76f92709bee6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81535169"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Rövid útmutató: blobok feltöltése, letöltése és listázása az Azure Portal használatával

Ez a rövid útmutató azt ismerteti, hogyan használható az [Azure Portal](https://portal.azure.com/) egy Azure Storage-tároló létrehozására, valamint blokkblobok fel- és letöltésére a tárolóban.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Tároló létrehozása

Az Azure Portalon a következő lépések végrehajtásával hozhat létre egy tárolót:

1. Az Azure Portalon lépjen az új tárfiókjára.
2. A Storage-fiók bal oldali menüjében görgessen a **blob Service** szakaszra, majd válassza a **tárolók**lehetőséget.
3. Válassza a **+ Tároló** gombot.
4. Adja meg az új tároló nevét. A tároló neve csak kisbetűket tartalmazhat, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat. A tárolók és a Blobok neveivel kapcsolatos további információkért lásd: [tárolók, blobok és metaadatok elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).
5. Adja meg a tároló nyilvános hozzáférési szintjét. Az alapértelmezett szint: **Privát (nincs névtelen hozzáférés)**.
6. A tároló létrehozásához válassza az **OK** gombot.

    ![A tárolók Azure Portalon történő létrehozását bemutató képernyőkép](media/storage-quickstart-blobs-portal/create-container.png)

## <a name="upload-a-block-blob"></a>Blokkblob feltöltése

A blokkblobok blobokká összefűzött adatblokkokból állnak. A Blob Storage legtöbb használati forgatókönyvében blokkblobokat kell használni. A blokkblobok ideális megoldást kínálnak szöveges és bináris adatok (például fájlok, képek és videók) felhőben történő tárolására. Ez a rövid útmutató a blokkblobok használatát mutatja be.

Az Azure Portalon a következő lépések végrehajtásával tölthet fel egy blokkblobot az Azure Portalon lévő új tárolóba:

1. Az Azure Portalon lépjen az előző szakaszban létrehozott tárolóhoz.
1. Válassza ki a tárolót a benne lévő blobok listájának megtekintéséhez. Ez a tároló új, így még nem tartalmaz blobokat.
1. Kattintson a **feltöltés** gombra a feltöltés panel megnyitásához, és tallózással keresse meg a helyi fájlrendszert, és keresse meg a blokkoló blobként feltölteni kívánt fájlt. A speciális szakaszt kibontva a feltöltési művelet egyéb beállításait is konfigurálhatja.

    ![A blobok helyi meghajtóról történő feltöltését bemutató képernyőkép](media/storage-quickstart-blobs-portal/upload-blob.png)

1. A blob feltöltéséhez kattintson a **feltöltés** gombra.
1. Ezzel a módszerrel tetszőleges számú blobot tölthet fel. Látni fogja, hogy az új blobok már fel vannak sorolva a tárolóban.

## <a name="download-a-block-blob"></a>Blokkblob letöltése

A blokkblobok letölthetők a böngészőben történő megjelenítéshez vagy a helyi fájlrendszerben történő mentéshez. A blokkblobok letöltéséhez kövesse az alábbi lépéseket:

1. Lépjen az előző szakaszban feltöltött blobok listájához.
1. Kattintson a jobb gombbal a letölteni kívánt blobra, majd válassza a **Letöltés** elemet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen rövid útmutatóban létrehozott erőforrások eltávolításához törölje a tárolót. Ekkor a rendszer törli az adott tárolóban lévő összes blobot is.

A tároló törlése:

1. Az Azure Portalon lépjen a tárfiókban tárolóinak listájához.
1. Válassza ki a törölni kívánt tárolót.
1. Válassza a **Továbbiak** gombot (**...**), majd a **Törlés** elemet.
1. Erősítse meg, hogy törölni kívánja a tárolót.

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között az Azure Portal használatával. Ha bővebb információra van szüksége a Blob Storage használatával kapcsolatban, lépjen tovább a Blob Storage használati útmutatójára.

> [!div class="nextstepaction"]
> [Blob Storage-műveletek használati útmutatója](storage-dotnet-how-to-use-blobs.md)
