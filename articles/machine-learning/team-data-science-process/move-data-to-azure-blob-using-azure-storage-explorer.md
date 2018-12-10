---
title: A Blob storage adatok áthelyezése az Azure Storage Explorer – csoportos adatelemzési folyamat
description: Adatok áthelyezése Azure Blob Storage-tárolóba vagy onnan máshová az Azure Storage Explorer használatával
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 99c9a86d130989e8c62a948e440b35c928b42299
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53134098"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Adatok importálására és az Azure Blob Storage Azure Storage Explorer használatával
Az Azure Storage Explorer egy olyan ingyenes eszköz, a Microsoft, amely lehetővé teszi, hogy az Azure Storage-adatokkal Windows, macOS és Linux rendszereken. Ez a témakör ismerteti, hogyan használható a fel- és letöltése az adatok Azure blob storage-ból. Az eszköz letölthető a [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Ha használja a virtuális Gépet, amely be lett állítva a parancsfájlok által biztosított [Data Science virtuális gépek Azure-ban](virtual-machines.md), majd az Azure Storage Explorer már telepítve van a virtuális gépen.
> 
> [!NOTE]
> Teljes körű Bevezetés az Azure blob storage, tekintse meg a [Azure Blob alapjai](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) és [Azure Blob Service-ben](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Jelen dokumentum céljából feltételezzük, hogy rendelkezik Azure-előfizetéssel, egy storage-fiókot és a kapcsolódó tárfiók-kulcsot az adott fiók. Adatok feltöltése/letöltése, előtt ismernie kell az Azure storage-fiók tárfióknév és fiókkulcs. 

* Azure-előfizetés beállításával kapcsolatban lásd: [ingyenes egy hónapos próbaidőszak](https://azure.microsoft.com/pricing/free-trial/).
* Storage-fiók létrehozásával és az első fiók és a kulcsadatokat: [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md). Jegyezze fel a tárfiók elérési kulcsának ezt a kulcsot az Azure Storage Explorer eszközzel a fiókhoz való csatlakozáshoz szükség szerint.
* Az Azure Storage Explorer eszköz letölthető a [Microsoft Azure Storage Explorer](http://storageexplorer.com/). Elfogadhatja az alapértelmezett beállításokat a telepítés során.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Az Azure Storage Explorer használata
A dokumentum feltöltése/letöltése Azure Storage Explorer használata az adatok az alábbi lépéseket. 

1. Indítsa el a Microsoft Azure Storage Explorerben.
2. Csatlakozva a **bejelentkezni a fiókjába...**  varázslóban válassza **Azure-fiók beállításai** ikonra, majd **vegyen fel egy fiókot** adja meg hitelesítő adatait. ![Az Azure storage-fiók hozzáadása](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Csatlakozva a **csatlakozás az Azure Storage** varázslóban válassza a **csatlakozás az Azure storage** ikonra. ![Kattintson a "Csatlakozás az Azure storage"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Írja be a hozzáférési kulcsot az Azure storage-fiókból a **csatlakozás az Azure Storage** varázslót, majd **tovább**. ![Adja meg a hozzáférési kulcsot az Azure storage-fiókból](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Adja meg a tárfiók nevét a **fióknév** mezőbe, majd **tovább**. ![Külső tárterület csatolása](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. A storage-fiókot hozzá kell láthatók. Hozzon létre egy blobtárolót a tárfiókban lévő, kattintson a jobb gombbal a **Blobtárolók** a fiókját, válassza a csomópontot **Blobtároló létrehozása**, és adjon meg egy nevet.
7. Adatok feltöltése a tárolóba, válassza ki a céltárolót, és kattintson a **feltöltése** gomb.![ Storage-fiókok](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Kattintson a **...**  jobb oldalán a **fájlok** jelölje ki egy vagy több fájlokat a fájlrendszerből, és kattintson a **feltöltése** a fájlok feltöltése a kezdéshez.![ Fájlok feltöltése](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Töltse le az adatokon, és válassza a blob letöltéséhez, és kattintson a megfelelő tárolóban való **letöltése**. ![Fájlok letöltése](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

