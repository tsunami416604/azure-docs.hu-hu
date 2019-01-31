---
title: Az Azure file storage csatlakoztatása az Azure Windows virtuális |} A Microsoft Docs
description: Store fájl a felhőben az Azure file storage szolgáltatással, és a felhőbeli fájlmegosztás csatlakoztatása egy Azure virtuális gépen (VM).
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.subservice: files
ms.openlocfilehash: d79a2d33157d9e2a6f56eb18d061f71a7dcd635c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462239"
---
# <a name="use-azure-file-shares-with-windows-vms"></a>Használja az Azure-fájlmegosztások Windows-alapú virtuális gépekhez 

Azure-fájlmegosztások használhatja arra, hogy tárolhatja és érheti el fájlokat a virtuális gépről. Például egy szkriptet vagy megosztani az összes virtuális gép kívánt alkalmazáskonfigurációs fájl is tárolhatja. Ebben a cikkben bemutatjuk, hogyan hozhat létre, és a egy Azure-fájlmegosztás csatlakoztatása, és hogyan fájlok feltöltését és letöltését.

## <a name="connect-to-a-file-share-from-a-vm"></a>Csatlakozás egy fájlmegosztást a virtuális gép

Ez a szakasz azt feltételezi, hogy már rendelkezik egy fájlmegosztást, amelyhez csatlakozni kíván. Ha szeretne létrehozni egyet, tekintse meg [fájlmegosztás létrehozása](#create-a-file-share) a cikk későbbi részében.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **tárfiókok**.
3. Válassza ki a tárfiókját.
4. Az a **áttekintése** lap **szolgáltatások**válassza **fájlok**.
5. Válasszon fájlmegosztást, vagy kattintson a **+ fájlmegosztás** , hozzon létre egy új fájlmegosztást használni.
6. Kattintson a **Connect** egy csatlakoztatási a fájlmegosztás Windows és Linux parancssori szintaxist megjelenítő lap megnyitásához.
7. A **meghajtóbetűjel**, válassza ki a kívánt alapján azonosítja a meghajtó betűjelét.
8. Válassza ki a melyik szintaxist használja, és válassza a Másolás gombbal másolja a vágólapra a jobb oldalon. Néhány hely, ahol könnyen hozzáférhet, illessze be. 
8. A virtuális Géphez való csatlakozáshoz, és nyisson meg egy parancssort.
9. Illessze be a szerkesztett kapcsolat szintaxist, és nyomja le **Enter**.
10. Amikor a kapcsolat létrejött, az üzenetet kap **a parancs sikeresen befejeződött.**
11. A kapcsolat ellenőrzéséhez írja be a meghajtóra, majd írja be a meghajtóbetűjelet **dir** fájlmegosztás tartalmának megtekintéséhez.



## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **tárfiókok**.
3. Válassza ki a tárfiókját.
4. Az a **áttekintése** lap **szolgáltatások**válassza **fájlok**.
5. A File Storage-szolgáltatás oldalán kattintson **+ fájlmegosztás**.
6. Adja meg a fájlmegosztás nevét. A fájlmegosztás neve kisbetűket, számokat és kötőjeleket használhatja. A név nem kezdődhet kötőjellel, és nem használhat több egymást követő kötőjelet. 
7. Adja meg egy megszabva, hogy mekkora a fájl lehet, legfeljebb 5120 GB.
8. Kattintson a **OK** a fájlmegosztás létrehozásához.
   
## <a name="upload-files"></a>Fájlok feltöltése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **tárfiókok**.
3. Válassza ki a tárfiókját.
4. Az a **áttekintése** lap **szolgáltatások**válassza **fájlok**.
5. Válasszon fájlmegosztást.
6. Kattintson a **feltöltése** megnyitásához a **fájlok feltöltése** lapot.
7. Kattintson a feltöltendő fájl a helyi fájlrendszerben keresse meg a mappa ikont.   
8. Kattintson a **feltöltése** feltölteni a fájlt a fájlmegosztásra.

## <a name="download-files"></a>Fájlok letöltése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **tárfiókok**.
3. Válassza ki a tárfiókját.
4. Az a **áttekintése** lap **szolgáltatások**válassza **fájlok**.
5. Válasszon fájlmegosztást.
6. Kattintson a jobb gombbal a fájlra, és válassza a **letöltése** , ahonnan letöltheti azt a helyi gépen.
   

## <a name="next-steps"></a>További lépések

Ön is létrehozhatja és kezelheti a fájlmegosztások PowerShell-lel. További információkért lásd: [a Windows Azure File storage használatának első lépései](../../storage/files/storage-dotnet-how-to-use-files.md).
