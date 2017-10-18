---
title: "Az Azure Files felügyelete az Azure Portalról | Microsoft Docs"
description: "Itt megismerheti, hogyan felügyelheti az Azure Files szolgáltatást az Azure Portal használatával."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: e56f8bf1057a8bc2cfcde841f69022104bafff27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-files-from-the-azure-portal"></a>Az Azure Files használata az Azure Portalról
Az [Azure Portal](https://portal.azure.com) kezelőfelületet nyújt a felhasználók számára az Azure Files kezelésére. Az alábbi műveleteket hajthatja végre a webböngészőjéből:

* Fájlmegosztás létrehozása
* Fájlok fel- és letöltése a fájlmegosztásból.
* Az egyes fájlmegosztások valós használatának figyelése.
* A fájlmegosztás méretkvótájának módosítása.
* A használandó csatlakoztatási parancsok másolása a fájlmegosztás Windows- vagy Linux-ügyfélből történő csatlakoztatásához.

## <a name="create-file-share"></a>Fájlmegosztás létrehozása
1. Jelentkezzen be az Azure portálra.
2. A navigációs menüben kattintson a **Tárfiókok** vagy a **Tárfiókok (klasszikus)** elemre.
    
    ![Képernyőkép a fájlmegosztás létrehozásáról a portálon](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share1.png)

3. Válassza ki a tárfiókját.

    ![Képernyőkép a fájlmegosztás létrehozásáról a portálon](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share2.png)

4. Válassza a „Fájlok” szolgáltatást.

    ![Képernyőkép a fájlmegosztás létrehozásáról a portálon](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share3.png)

5. Kattintson a „Fájlmegosztások” elemre, és a hivatkozás segítségével hozza létre az első fájlmegosztását.

    ![Képernyőkép a fájlmegosztás létrehozásáról a portálon](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share4.png)

6. Adja meg a fájlmegosztás nevét és méretét (legfeljebb 5120 GB), és hozza létre első fájlmegosztását. Ha a fájlmegosztás létrejött, bármely, SMB 2.1 vagy SMB 3.0 protokollt támogató fájlrendszerről csatlakoztathatja azt. A fájlmegosztás **Kvóta** elemére kattintva módosíthatja a fájl méretét legfeljebb 5120 GB-ra. Az Azure Files használatával járó tárolási költségek becsléséhez tekintse meg az [Azure díjkalkulátort](https://azure.microsoft.com/pricing/calculator/).

    ![Képernyőkép a fájlmegosztás létrehozásáról a portálon](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share5.png)

## <a name="upload-and-download-files"></a>Fájlok fel- és letöltése
1. Válasszon egy már létrehozott fájlmegosztást.

    ![Képernyőkép a fájlok feltöltéséről és letöltéséről a portálon](./media/storage-how-to-use-files-portal/use-files-portal-upload-file1.png)

2. Kattintson a **Feltöltés** gombra a fájlok feltöltésére szolgáló felület megnyitásához.

    ![Képernyőkép a fájlok feltöltéséről a portálon](./media/storage-how-to-use-files-portal/use-files-portal-upload-file2.png)

## <a name="connect-to-file-share"></a>Kapcsolódás a fájlmegosztáshoz
-  Kattintson a **Csatlakoztatás** gombra a fájlmegosztás Windows és Linux rendszeren való csatlakoztatásához szükséges parancssor lekéréséhez. Az egyéb Linux-disztribúciókra vonatkozó csatlakoztatási utasításokért lásd: [az Azure Files Linuxszal való használatát](../storage-how-to-use-files-linux.md) ismertető cikket.

    ![Képernyőkép a fájlmegosztás csatlakoztatásáról](./media/storage-how-to-use-files-portal/use-files-portal-connect.png)
-  A Windows és Linux rendszeren való fájlmegosztás-csatlakoztatáshoz szükséges parancsokat kimásolhatja, és futtathatja az Azure-beli virtuális gépen vagy a helyszíni számítógépen.

    ![A Windows és Linux rendszeren való csatlakoztatáshoz szükséges parancsokat bemutató képernyőkép](./media/storage-how-to-use-files-portal/use-files-portal-show-mount-commands.png)

**Tipp:**  
A csatlakoztatáshoz szükséges tárelérési kulcsok megkereséséhez kattintson a csatlakoztatási oldal alján található **A tárfiók elérési kulcsainak megtekintése** lehetőségre.

## <a name="see-also"></a>Lásd még:
Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról.

* [Gyakori kérdések](../storage-files-faq.md)
* [Hibaelhárítás a Windows rendszerben](storage-troubleshoot-windows-file-connection-problems.md)      
* [Hibaelhárítás a Linux rendszerben](storage-troubleshoot-linux-file-connection-problems.md)    
