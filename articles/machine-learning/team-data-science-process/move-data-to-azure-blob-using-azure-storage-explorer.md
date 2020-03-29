---
title: Blob-tárolási adatok áthelyezése az Azure Storage Explorer rel – Csapatadat-elemzési folyamat
description: Ismerje meg, hogyan tölthet fel és tölthet le adatokat az Azure blobstorage-ból az Azure Storage használatával.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bfc63c6f5aca92fb7fda9e3ecf63ce4c332b12ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720911"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Adatok áthelyezése az Azure Blob Storage-ba és onnan az Azure Storage Explorer használatával
Az Azure Storage Explorer a Microsoft ingyenes eszköze, amely lehetővé teszi az Azure Storage-adatok windowsos, macOS és Linux rendszeren történő használatát. Ez a témakör azt ismerteti, hogyan tölthet fel és tölthet le adatokat az Azure blob storage-ból. Az eszköz letölthető a [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Ha olyan virtuális gépet használ, amely et az [Azure-ban az Azure-ban az Adatelemzési virtuális gépek](virtual-machines.md)által biztosított parancsfájlokkal állították be, akkor az Azure Storage Explorer már telepítve van a virtuális gépen.
> 
> [!NOTE]
> Az Azure blobstorage teljes bemutatását az [Azure Blob Alapjai](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) és az [Azure Blob Service című dokumentumban tájékot tudni.](https://msdn.microsoft.com/library/azure/dd179376.aspx)   
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez a dokumentum feltételezi, hogy rendelkezik egy Azure-előfizetéssel, egy tárfiókkal és a fiók megfelelő tárolási kulcsával. Az adatok feltöltése/letöltése előtt ismernie kell az Azure Storage-fiók nevét és a fiókkulcsot. 

* Azure-előfizetés beállításáról az [Ingyenes egyhónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* A tárfiók létrehozásával, valamint a fiók- és kulcsinformációk beszerzésével kapcsolatos tudnivalókat [az Azure Storage-fiókok – tudnivalók.](../../storage/common/storage-create-storage-account.md) Jegyezze fel a tárfiók hozzáférési kulcsát, mivel szüksége van erre a kulcsra az Azure Storage Explorer eszközzel való csatlakozáshoz.
* Az Azure Storage Explorer eszköz letölthető a [Microsoft Azure Storage Explorer](https://storageexplorer.com/)ből. A telepítés során fogadja el az alapértelmezett értékeket.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Az Azure Storage Explorer használata
A következő lépések bemutatják, hogyan tölthet nek fel/tölthetnek le adatokat az Azure Storage Explorer használatával. 

1. Indítsa el a Microsoft Azure Storage Explorer t.
2. A Bejelentkezés a **fiókjába varázsló** varázsló hoz, válassza az **Azure-fiók beállításai** ikont, majd **adjon hozzá egy fiókot,** és adja meg a hitelesítő adatokat. 
![Azure Storage-fiók hozzáadása](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. A Csatlakozás az **Azure Storage-hoz** varázsló hoz, válassza a Csatlakozás az **Azure Storage-hoz** ikont. ![Kattintson a "Csatlakozás az Azure Storage-hoz" lehetőségre](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Adja meg a hozzáférési kulcsot az Azure Storage-fiókjából a Csatlakozás az **Azure Storage-hoz** varázslóban, majd a **Tovább**. ![Hozzáférési kulcs megadása az Azure Storage-fiókból](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Írja be a tárfiók nevét a **Fióknév mezőbe,** majd válassza a **Tovább**gombot. ![Külső tároló csatlakoztatása](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. A hozzáadott tárfiók most meg kell jelennie. Blob-tároló létrehozásához egy tárfiókban, kattintson a jobb gombbal a **Blob-tárolók** csomópontra a fiókban, válassza **a Blob-tároló létrehozása parancsot,** és adjon meg egy nevet.
7. Adatok tárolóba való feltöltéséhez jelölje ki a céltárolót, és kattintson a **Feltöltés gombra.**
![Tárfiókok](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Kattintson a fájlok **mappájának** jobb oldalán található **...** elemre, jelöljön ki egy vagy több fájlt a fájlrendszerből, és kattintson a **Feltöltés gombra** a fájlok feltöltésének megkezdéséhez. ![Fájlok feltöltése](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Adatok letöltéséhez jelölje ki a blobot a megfelelő tárolóban a letöltéshez, és kattintson a **Letöltés gombra.** ![Fájlok letöltése](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

