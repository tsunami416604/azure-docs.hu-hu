---
title: Emulator Express használatával futtassa, és a egy helyi gépen az Azure cloud service hibakeresése |} A Microsoft Docs
description: Emulator Express használatával futtassa, és a egy helyi gépen cloud service hibakeresése
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: mikejo
ms.openlocfilehash: 3432e8778b2c5b5fd4db53a82ca23b55d75bcac5
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969537"
---
# <a name="using-emulator-express-to-run-and-debug-an-azure-cloud-service-on-a-local-machine"></a>Emulator Express használatával futtassa, és a egy helyi gépen az Azure cloud service hibakeresése
Emulator Express használatával tesztelése, és a egy cloud service hibakeresése a Visual Studio futtatása rendszergazdaként nélkül. A Projektbeállítások között Emulator Express vagy a teljes emulátor, a felhőalapú szolgáltatás követelményeitől függően használatával állíthatja be. A teljes emulátor kapcsolatos további információkért lásd: [egy Azure-alkalmazások futtatása a Compute emulatorban](storage/common/storage-use-emulator.md).

## <a name="using-emulator-express-in-visual-studio"></a>A Visual Studio Emulator Express használatával
Azure-projekt az Azure SDK 2.3-as vagy újabb létrehozásakor automatikusan Emulator Express szolgál. Az Azure SDK korábbi verziójával létrehozott meglévő projektek esetén a következő lépések segítségével válassza ki az Emulator Express:

1. Hozzon létre vagy nyisson meg egy Azure-felhőszolgáltatás-projekt a Visual Studióban.

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a projektre, és a helyi menüből válassza ki a **tulajdonságok**.

1. A projektek tulajdonságai lapon válassza ki a **webes** fülre.

    ![Az Azure felhőszolgáltatás-projekt tulajdonságai](./media/vs-azure-tools-emulator-express-debug-run/web-properties.png)

1. Alatt **helyi fejlesztési kiszolgáló**válassza **beállítást használja az IIS Express**.

1. Alatt **emulátor**válassza **használata Emulator Express**.
   
1. Az Emulator Express indításához futtassa a következő parancsot a parancssorba: 

    ```
    csrun.exe /useemulatorexpress
    ```

## <a name="emulator-express-limitations"></a>Emulator Express korlátozások
A következő problémák korlátozásokkal az Emulator Express ismert: 

- Emulator Express, ezért nem kompatibilis az IIS-webkiszolgálón.
- A cloud Services több szerepkört is tartalmazhat, de minden szerepkör egyetlen példányra korlátozott.
- Portszámok 1000 alatt nem érhető el. Ha használ egy hitelesítésszolgáltató, amely általában az alábbi 1000 portot használ, szüksége lehet ezt az értéket módosítsa a port számát, hogy 1000 fölött van.
- Az Azure Compute Emulator vonatkozó korlátozások Emulator Express is vonatkoznak. Ha például nem lehet több mint 50 szerepkörpéldányok száma üzemelő példányonként. Az Azure Compute Emulator kapcsolatos további információkért lásd: [egy Azure-alkalmazások futtatása a Compute emulatorban](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## <a name="next-steps"></a>További lépések
[Hibakeresés az Azure cloud services](https://msdn.microsoft.com/library/azure/ee405479.aspx)
