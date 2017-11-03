---
title: "Adatok áthelyezése, illetve onnan a Blob Storage-ban az Azure Storage-kezelővel |} Microsoft Docs"
description: "Adatok áthelyezése Azure Blob Storage-tárolóba vagy onnan máshová az Azure Storage Explorer használatával"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 10bd283f-0875-4c67-af63-6492270b7656
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: 12caf933b04258065039a51f45d531a30865f8d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Adatok áthelyezése, és az Azure Blob Storage Azure Storage Explorerrel
Az Azure Tártallózó egy olyan szabad eszköz a Microsoft, amely lehetővé teszi a Windows, a macOS és a Linux Azure Storage-adatokkal dolgozni. Ez a témakör ismerteti, és töltse le az adatokat az Azure blob storage használatával. Az eszköz letölthető a [Microsoft Azure Tártallózó](http://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Ha virtuális Gépet, amely a parancsfájlok által biztosított be lett állítva az [adatok tudományos virtuális gépek Azure-ban](virtual-machines.md), majd Azure Tártallózó már telepítve van a virtuális Gépen.
> 
> [!NOTE]
> Az Azure blob storage teljes bevezetéséhez hivatkoznak [Azure Blob alapjai](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) és [Azure Blob szolgáltatás](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Jelen dokumentum céljából feltételezzük, hogy az Azure-előfizetéssel, a tárfiók és a megfelelő kulcsot adott fiók rendelkezik. Adatok feltöltése/letöltése, előtt ismernie kell az Azure storage-fiók nevét és a fiók kulcs. 

* Állítsa be Azure-előfizetéssel, lásd: [ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* A storage-fiók létrehozásával és az első fiók és a fontos információkat lásd: [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md). Jegyezze fel a tárfiók elérési kulcsának ezt a kulcsot, a fiók az Azure Tártallózó eszközzel való csatlakozáshoz szükség szerint.
* Az Azure Tártallózó eszköz letölthető a [Microsoft Azure Tártallózó](http://storageexplorer.com/). Fogadja el az alapértelmezett telepítés során.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Az Azure Storage-kezelővel
Az alábbi lépéseket a dokumentum feltöltése/letöltése Azure Tártallózó adatokat. 

1. Indítsa el a Microsoft Azure Tártallózó.
2. Elindítani a **jelentkezzen be a fiókba...**  varázslóban válassza **Azure-fiók beállításai** ikonra, majd **vegyen fel egy fiókot** és adja meg hitelesítő adatokat. ![Egy Azure storage-fiók hozzáadása](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Elindítani a **csatlakozás az Azure Storage** lapján jelölje be a **csatlakozás az Azure storage** ikonra. ![Csatlakozás az Azure storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Adja meg az elérési kulcsot az Azure-tárfiókot a a **csatlakozás az Azure Storage** varázsló, majd **következő**. ![Csatlakozás az Azure storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. A tárfiók nevét adja meg a **fióknév** mezőbe, majd válassza ki **következő**. ![Külső tárterület csatolása](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. A hozzáadott tárfiókot most kell felsorolni. A tárfiók a blob-tároló létrehozásához kattintson a jobb gombbal a **Blobtárolók** fiók, jelölje be a csomópont **Blob-tároló létrehozása**, és adjon meg egy nevet.
7. Adatok feltöltése a tárolóhoz, válassza ki a cél-tároló, majd kattintson a **feltöltése** gomb.![ Storage-fiókok](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Kattintson a **...**  jobb oldalán a **fájlok** mezőben adja meg egy vagy több fájl feltöltése a fájlrendszerből, és kattintson a **feltöltése** a fájlok feltöltése a kezdéshez.![ Fájlok feltöltése](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Adatok, jelölje ki a megfelelő tárolót, hogy töltse le, és kattintson a blob letöltése **letöltése**. ![Fájlok letöltése](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

