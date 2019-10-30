---
title: BLOB Storage-adatAzure Storage Explorer-csoport adatelemzési folyamatának áthelyezése
description: Ismerje meg, hogyan töltheti fel és töltheti le az Azure Blob Storage-ból az Azure Storage Explorer-t.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 880b81c07aeed7359dfe35e1361a20ecb11e27dd
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053956"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Adatok áthelyezése Azure-Blob Storageba és onnan az Azure Storage Explorer használatával
A Azure Storage Explorer a Microsoft ingyenes eszköze, amely lehetővé teszi az Azure Storage-adatok használatát Windows, macOS és Linux rendszeren. Ez a témakör azt ismerteti, hogyan használható az Azure Blob Storage-ból származó adatok feltöltése és letöltése. Az eszköz letölthető [Microsoft Azure Storage Explorerról](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Ha olyan virtuális gépet használ, amely az Azure-beli [adatelemzési virtuális gépek](virtual-machines.md)által biztosított parancsfájlokkal lett beállítva, akkor Azure Storage Explorer már telepítve van a virtuális gépen.
> 
> [!NOTE]
> Az Azure Blob Storage teljes körű bevezetéséhez tekintse meg az [Azure Blob alapjai](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) és az [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)című témakört.   
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez a dokumentum feltételezi, hogy rendelkezik Azure-előfizetéssel, egy Storage-fiókkal és az adott fiókhoz tartozó tárolási kulccsal. Az adatfeltöltés/-letöltés előtt ismernie kell az Azure Storage-fiók nevét és a fiók kulcsát. 

* Azure-előfizetés beállításához tekintse meg az [ingyenes egy hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/).
* A Storage-fiók létrehozásával, valamint a fiók-és a kulcsfontosságú információk beszerzésével kapcsolatos útmutatásért lásd: [Tudnivalók az Azure Storage-fiókokról](../../storage/common/storage-create-storage-account.md). Jegyezze fel a Storage-fiók elérési kulcsát, mivel szüksége van erre a kulcsra ahhoz, hogy a Azure Storage Explorer eszközzel csatlakozhasson a fiókhoz.
* A Azure Storage Explorer eszköz letölthető [Microsoft Azure Storage Explorerról](https://storageexplorer.com/). A telepítés során fogadja el az alapértelmezett értékeket.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Azure Storage Explorer használata
A következő lépések bemutatják, hogyan tölthetők fel/tölthetők le az adatok a Azure Storage Explorer használatával. 

1. Microsoft Azure Storage Explorer elindítása.
2. Ha be szeretné állítani a **bejelentkezést a fiókjába...** varázsló, válassza az **Azure-Fiókbeállítások** ikont, majd **vegyen fel egy fiókot** , és adja meg a hitelesítő adatokat. ![Azure Storage-fiók hozzáadása](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. A **Kapcsolódás az Azure Storage** -hoz varázsló létrehozásához válassza a **Kapcsolódás az Azure Storage-hoz** ikont. ![kattintson a "kapcsolódás az Azure Storage-hoz" lehetőségre](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Adja meg az Azure Storage-fiók elérési kulcsát a **Kapcsolódás az Azure Storage-hoz** varázsló, majd a **tovább**gombra. ![adja meg az Azure Storage-fiók elérési kulcsát](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Adja meg a Storage-fiók nevét a **fióknév** mezőben, majd kattintson a **tovább**gombra. ![külső tároló csatolása](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. A hozzáadott Storage-fióknak mostantól szerepelnie kell a listáján. Ha BLOB-tárolót szeretne létrehozni egy Storage-fiókban, kattintson a jobb gombbal a **blob** -tárolók csomópontra a fiókban, válassza a **blob-tároló létrehozása**elemet, és adjon meg egy nevet.
7. Az adatok tárolóba való feltöltéséhez válassza ki a tárolót, és kattintson a **feltöltés** gombra.![Storage-fiókok](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. A **fájlok** mező jobb oldalán kattintson a **...** elemre, válasszon ki egy vagy több fájlt a fájlrendszerből való feltöltéshez, majd kattintson a **feltöltés** gombra a fájlok feltöltésének megkezdéséhez. fájlok feltöltése![](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Az adatletöltéshez válassza ki a blobot a megfelelő tárolóban a letöltéshez, majd kattintson a **Letöltés**gombra. ![fájlok letöltése](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

