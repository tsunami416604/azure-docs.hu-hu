---
title: "Egy Windows Azure VM csatlakoztatási Azure file storage |} Microsoft Docs"
description: "Fájl tárolása a felhőben az Azure file storage szolgáltatással, és a felhőalapú fájlmegosztást csatlakoztathatja egy Azure virtuális gép (VM)."
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: tysonn
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 8d537bdc882487784baef9f693e4677c76d3bd8d
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="use-azure-file-shares-with-windows-vms"></a>Azure fájlmegosztásokat Windows virtuális gépek használata 

Azure fájlmegosztásokat is tárolhatja és érheti fájlokat a virtuális Gépet is használhatja. Például egy parancsfájl vagy tárolhatja a virtuális gépeinek megosztani kívánt alkalmazáskonfigurációs fájl. Ebben a cikkben megmutatjuk, hogyan hozhat létre, és az Azure fájlmegosztások csatlakoztatása, és hogyan fájlok feltöltését és letöltését.

## <a name="connect-to-a-file-share-from-a-vm"></a>Csatlakoztatja a fájlmegosztást a virtuális gép

Ez a szakasz azt feltételezi, hogy már rendelkezik egy fájlmegosztást, amelyhez csatlakozni kíván. Ha szeretne létrehozni egyet szüksége, tekintse meg [fájlmegosztás létrehozása](#create-a-file-share) című cikkben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali menü **tárfiókok**.
3. Válassza ki a tárfiókját.
4. Az a **áttekintése** lap **szolgáltatások**, jelölje be **fájlok**.
5. Válasszon egy fájlmegosztást, vagy kattintson a **+ fájlmegosztás** létrehozni új fájlmegosztást használja.
6. Kattintson a **Connect** megnyit egy olyan lapot, hogy a fájlmegosztás Windows vagy Linux csatlakoztatására parancssori szintaxisát jeleníti meg.
7. A **meghajtóbetűjelet**, válassza ki, amelyeket meg szeretne segítségével azonosíthatja a meghajtó betűjelét.
8. Válassza ki a mely szintaxist használja, és válassza ki a jobb oldali másolja a vágólapra Másolás gombra. Illessze be olyan helyen, ahol könnyedén elérheti azt. 
8. Csatlakoztassa a virtuális Gépet, és nyisson meg egy parancssort.
9. Illessze be a szerkesztett kapcsolat szintaxist, majd nyomja le **Enter**.
10. Ha a kapcsolat létrejött, a hibaüzenet a **a parancs sikeresen befejeződött.**
11. Ellenőrizze a kapcsolatot, írja be a meghajtóbetűjelet, amelynek váltson, és írja be **dir** a fájlmegosztás tartalmának megtekintéséhez.



## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali menü **tárfiókok**.
3. Válassza ki a tárfiókját.
4. Az a **áttekintése** lap **szolgáltatások**, jelölje be **fájlok**.
5. A fájl szolgáltatás lapján, kattintson a **+ fájlmegosztás**.
6. Adja meg a fájlmegosztás neve. A fájlmegosztás neve kisbetűket, számokat és kötőjeleket egyetlen használhatja. A név nem kezdődhet kötőjellel, és nem használható több egymást követő kötőjelet. 
7. Töltse ki hogyan nagy a fájl lehet, legfeljebb 5120 GB korlátozást.
8. Kattintson a **OK** a fájlmegosztás létrehozásához.
   
## <a name="upload-files"></a>Fájlok feltöltése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali menü **tárfiókok**.
3. Válassza ki a tárfiókját.
4. Az a **áttekintése** lap **szolgáltatások**, jelölje be **fájlok**.
5. Válasszon egy fájlmegosztást.
6. Kattintson a **feltöltése** megnyitásához a **fájlok feltöltése** lap.
7. Kattintson a feltöltendő fájl a helyi fájlrendszerben tallózással mappa ikonra.   
8. Kattintson a **feltöltése** feltölteni a fájlt a fájlmegosztáshoz.

## <a name="download-files"></a>Fájlok letöltése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali menü **tárfiókok**.
3. Válassza ki a tárfiókját.
4. Az a **áttekintése** lap **szolgáltatások**, jelölje be **fájlok**.
5. Válasszon egy fájlmegosztást.
6. Kattintson a jobb gombbal a fájlra, és válassza a **letöltése** letöltheti a helyi számítógépre.
   

## <a name="next-steps"></a>További lépések

Is létrehozása és kezelése a PowerShell használatával fájlmegosztások. További információkért lásd: [Ismerkedés az Azure File storage on Windows](../../storage/files/storage-dotnet-how-to-use-files.md).
