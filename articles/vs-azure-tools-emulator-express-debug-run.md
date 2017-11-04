---
title: "Emulator Express használatával futtatásához és az Azure-felhőszolgáltatás a helyi számítógépen debug |} Microsoft Docs"
description: "Emulator Express használatával futtatni, és egy felhőalapú szolgáltatás a helyi számítógépen hibakeresési"
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: ghogen
editor: 
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: mikejo
ms.openlocfilehash: 9c258ad7de5e25b4b304f5e56d93abeff1187f71
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="using-emulator-express-to-run-and-debug-an-azure-cloud-service-on-a-local-machine"></a>Emulator Express használatával futtatásához és az Azure-felhőszolgáltatás a helyi számítógépen hibakeresése
Emulator Express használatával tesztelése, és egy felhőalapú szolgáltatás hibakeresése a Visual Studio futtatása rendszergazdaként nélkül. Beállíthatja a projektbeállításokat Emulator Express vagy a felhőalapú szolgáltatás követelményeitől függően a teljes emulátor használatára. A teljes emulátor kapcsolatos további információkért lásd: [egy Azure-alkalmazást futtat a Compute Emulator](storage/common/storage-use-emulator.md).

## <a name="using-emulator-express-in-visual-studio"></a>A Visual Studio Emulator Express használatával
Az Azure SDK 2.3 vagy újabb Azure-projekt létrehozásakor automatikusan Emulator Express szolgál. Létező projektek az Azure SDK korábbi verziójával készült az alábbi lépések segítségével Emulator Express kiválasztása:

1. Hozzon létre, vagy a Visual Studio Azure cloud service projekt megnyitása.

1. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki a helyi menüből **tulajdonságok**.

1. A projektek tulajdonságai lapon válassza ki a **webes** fülre.

    ![Egy Azure-felhőszolgáltatás-projekt tulajdonságai](./media/vs-azure-tools-emulator-express-debug-run/web-properties.png)

1. A **helyi fejlesztési kiszolgáló**, jelölje be **beállítás használatát az IIS Express**.

1. A **emulátor**, jelölje be **használata Emulator Express**.
   
1. Az emulátor Express elindításához futtassa a következő parancsot a parancssorba: 

    ```
    csrun.exe /useemulatorexpress
    ```

## <a name="emulator-express-limitations"></a>Emulator Express korlátozásai
A következő problémák ismert korlátozásai Emulator Express: 

- Emulator Express nem kompatibilis az IIS-webkiszolgáló.
- A felhőalapú szolgáltatás több szerepkört is tartalmazhat, de egyes szerepkörök egy példányra korlátozott.
- Portszámok 1000 alatt nem érhető el. Ha használ olyan hitelesítésszolgáltatója, amely általában az alábbi 1000 portot használ, szükség lehet módosítsa ezt az értéket egy portszám, amely 1000 fent.
- Az Azure Compute Emulator vonatkozó korlátozások is érvényesek Emulator Express. Például nem lehet üzemelő példányonként legfeljebb 50 szerepkörpéldányokat. Az Azure Compute Emulator kapcsolatos további információkért lásd: [egy Azure-alkalmazást futtat a Compute Emulator](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## <a name="next-steps"></a>Következő lépések
[Hibakeresési Azure cloud services csomag](https://msdn.microsoft.com/library/azure/ee405479.aspx)
