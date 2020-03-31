---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67671205"
---
1. Dapl, rdmacm, ibverbs és mlx4 telepítése

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. Az /etc/waagent.conf fájlban engedélyezze az RDMA-t a következő konfigurációs sorok megjegyzésének kioldásával. A fájl szerkesztéséhez root hozzáférésszükséges.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. Adja hozzá vagy módosítsa a következő memóriabeállításokat a KB-ban az /etc/security/limits.conf fájlban. A fájl szerkesztéséhez root hozzáférésszükséges. Tesztelési célokra beállíthatja a memlock-ot korlátlanra. Például: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Telepítse az Intel MPI könyvtárat. Vagy [vásárolni, és töltse le](https://software.intel.com/intel-mpi-library/) a könyvtárat az Intel, vagy töltse le az ingyenes [próbaverzió](https://registrationcenter.intel.com/en/forms/?productid=1740).

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Csak az Intel MPI 5.x futásidők támogatottak.
 
   A telepítési lépéseket az [Intel MPI Library telepítési útmutatójában talál.](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)

5. Engedélyezze a nem gyökérnem-hibakereső folyamatok nyomon követését (az Intel MPI legújabb verzióihoz szükséges).
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```
