---
title: A Blob storage adatok áthelyezése az Azure Storage Explorer – csoportos adatelemzési folyamat
description: Ismerje meg, hogyan töltheti fel és töltheti le az Azure Blob Storage-ból az Azure Storage Explorer-t.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720911"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Adatok importálására és az Azure Blob Storage Azure Storage Explorer használatával
Az Azure Storage Explorer egy olyan ingyenes eszköz, a Microsoft, amely lehetővé teszi, hogy az Azure Storage-adatokkal Windows, macOS és Linux rendszereken. Ez a témakör ismerteti, hogyan használható a fel- és letöltése az adatok Azure blob storage-ból. Az eszköz letölthető [Microsoft Azure Storage Explorerról](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Ha olyan virtuális gépet használ, amely az Azure-beli [adatelemzési virtuális gépek](virtual-machines.md)által biztosított parancsfájlokkal lett beállítva, akkor Azure Storage Explorer már telepítve van a virtuális gépen.
> 
> [!NOTE]
> Az Azure Blob Storage teljes körű bevezetéséhez tekintse meg az [Azure Blob alapjai](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) és az [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)című témakört.   
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Jelen dokumentum céljából feltételezzük, hogy rendelkezik Azure-előfizetéssel, egy storage-fiókot és a kapcsolódó tárfiók-kulcsot az adott fiók. Az adatfeltöltés/-letöltés előtt ismernie kell az Azure Storage-fiók nevét és a fiók kulcsát. 

* Azure-előfizetés beállításához tekintse meg az [ingyenes egy hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/).
* A Storage-fiók létrehozásával, valamint a fiók-és a kulcsfontosságú információk beszerzésével kapcsolatos útmutatásért lásd: [Tudnivalók az Azure Storage-fiókokról](../../storage/common/storage-create-storage-account.md). Jegyezze fel a tárfiók elérési kulcsának ezt a kulcsot az Azure Storage Explorer eszközzel a fiókhoz való csatlakozáshoz szükség szerint.
* A Azure Storage Explorer eszköz letölthető [Microsoft Azure Storage Explorerról](https://storageexplorer.com/). Elfogadhatja az alapértelmezett beállításokat a telepítés során.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Az Azure Storage Explorer használata
A dokumentum feltöltése/letöltése Azure Storage Explorer használata az adatok az alábbi lépéseket. 

1. Indítsa el a Microsoft Azure Storage Explorerben.
2. Ha be szeretné állítani a **bejelentkezést a fiókjába...** varázsló, válassza az **Azure-Fiókbeállítások** ikont, majd **vegyen fel egy fiókot** , és adja meg a hitelesítő adatokat. 
![Azure Storage-fiók hozzáadása](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. A **Kapcsolódás az Azure Storage** -hoz varázsló létrehozásához válassza a **Kapcsolódás az Azure Storage-hoz** ikont. ![kattintson a "kapcsolódás az Azure Storage-hoz" lehetőségre](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Adja meg az Azure Storage-fiók elérési kulcsát a **Kapcsolódás az Azure Storage-hoz** varázsló, majd a **tovább**gombra. ![adja meg az Azure Storage-fiók elérési kulcsát](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Adja meg a Storage-fiók nevét a **fióknév** mezőben, majd kattintson a **tovább**gombra. ![külső tároló csatolása](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Ekkor megjelenik a hozzáadott Storage-fiók. Ha BLOB-tárolót szeretne létrehozni egy Storage-fiókban, kattintson a jobb gombbal a **blob** -tárolók csomópontra a fiókban, válassza a **blob-tároló létrehozása**elemet, és adjon meg egy nevet.
7. Az adatok tárolóba való feltöltéséhez válassza ki a tárolót, és kattintson a **feltöltés** gombra.
![Tárfiókok](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. A **fájlok** mező jobb oldalán kattintson a **...** elemre, válasszon ki egy vagy több fájlt a fájlrendszerből való feltöltéshez, majd kattintson a **feltöltés** gombra a fájlok feltöltésének megkezdéséhez. fájlok feltöltése![](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Az adatletöltéshez válassza ki a blobot a megfelelő tárolóban a letöltéshez, majd kattintson a **Letöltés**gombra. ![fájlok letöltése](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

