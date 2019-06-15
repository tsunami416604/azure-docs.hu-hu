---
title: VMware virtuális gépek és fizikai kiszolgálók az Azure-bA az Azure Site Recovery vészhelyreállításhoz használt folyamatkiszolgáló kezelése |} A Microsoft Docs
description: Ez a cikk azt ismerteti, felügyelhetők a folyamatkiszolgálók állítható be a vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók Azure-bA az Azure Site Recovery használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 2c27779719c73adf4d7fc1a61a0c77d03df71815
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64925622"
---
# <a name="manage-process-servers"></a>Folyamatkiszolgálók kezelése

Ez a cikk azt ismerteti, hogy a Site Recovery folyamatkiszolgáló kezeléséhez kapcsolódó gyakori feladatok.

A folyamatkiszolgáló kapni, optimalizálása és a replikációs adatok küldése az Azure-bA szolgál. VMware virtuális gépeken és fizikai kiszolgálókat szeretne replikálni, is végez leküldéses telepítését a mobilitási szolgáltatást, és elvégzi a helyszíni gépek automatikus felderítését. A helyszíni VMware virtuális gépek vagy fizikai kiszolgálók replikálása az Azure-ba, a folyamatkiszolgáló alapértelmezés szerint telepítve van a konfigurációs kiszolgáló gépen. 

- Nagyméretű környezetekben szükség lehet további helyszíni folyamatkiszolgálók kapacitást.
- A feladat-visszavételhez az Azure-ból a helyszíni be kell állítania egy ideiglenes folyamatkiszolgáló az Azure-ban. Ez a virtuális gép feladat-visszavétel végeztével törölhetők. 

További információ a folyamatkiszolgáló.


## <a name="upgrade-a-process-server"></a>Frissítse a folyamatkiszolgálót

Amikor folyamatkiszolgáló üzembe helyezése a helyszínen, vagy Azure virtuális gépként a feladat-visszavételhez, az a folyamatkiszolgáló legújabb verziója van telepítve. A Site Recovery csapatok kiadás javításokat és rendszeres időközönként fejlesztések, és azt javasoljuk, tartsa naprakészen a folyamatkiszolgálók. Folyamatkiszolgáló következőképpen frissítheti:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Helyezze át a virtuális gépek, a folyamat kiszolgáló terhelés kiegyenlítése érdekében

A terhelés kiegyenlítése a következő két folyamatot kiszolgálók között, helyezze át virtuális gépeket:

1. A tároló alatt **kezelés** kattintson **Site Recovery-infrastruktúra**. A **a VMware és fizikai gépek**, kattintson a **konfigurációs kiszolgálók**.
2. Kattintson a konfigurációs kiszolgálón, amelyhez a folyamatkiszolgálók van regisztrálva.
3. Kattintson a folyamat, amelynek meg szeretné betölteni a forgalom terheléselosztása kiszolgálón.

    ![LoadBalance](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Kattintson a **terheléselosztása**, válassza ki a cél folyamatkiszolgáló, amelyre át szeretné helyezni a gépek. Kattintson a **OK**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Kattintson a **gépek kijelölése**, és válassza ki a gépeket szeretne áthelyezni a folyamat-célkiszolgáló az aktuális. Átlagos változás részletei jelennek meg minden egyes virtuális gépek ellen. Ezután kattintson az **OK** gombra. 
3. A tárolóban, a feladat előrehaladásának figyeléséhez **figyelés** > **Site Recovery-feladatok**.

A módosítások megjelennek a portálon hozzávetőlegesen 15 percet vesz igénybe. A gyorsabb hatás [frissítse a konfigurációs kiszolgáló](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Egy teljes terhelést váltson egy másik folyamatkiszolgálóra

Helyezze át a teljes terhelés kezelése egy másik folyamatkiszolgálóra a folyamatkiszolgáló a következő:

1. A tároló alatt **kezelés** kattintson **Site Recovery-infrastruktúra**. A **a VMware és fizikai gépek**, kattintson a **konfigurációs kiszolgálók**.
2. Kattintson a konfigurációs kiszolgálón, amelyhez a folyamatkiszolgálók van regisztrálva.
3. Kattintson a folyamatkiszolgáló, amelyről szeretne váltani a számítási feladatok.
4. Kattintson a **kapcsoló**, válassza ki a cél folyamatkiszolgáló, amelyre át szeretné helyezni a a számítási feladatok. Kattintson a **OK**

    ![Kapcsoló](media/vmware-azure-manage-process-server/Switch.PNG)

5. A tárolóban, a feladat előrehaladásának figyeléséhez **figyelés** > **Site Recovery-feladatok**.

A módosítások megjelennek a portálon hozzávetőlegesen 15 percet vesz igénybe. A gyorsabb hatás [frissítse a konfigurációs kiszolgáló](vmware-azure-manage-configuration-server.md#refresh-configuration-server).



## <a name="reregister-a-process-server"></a>A folyamatkiszolgáló regisztrálása

A helyszínen futó folyamatkiszolgáló regisztrálása vagy egy Azure virtuális gépen a konfigurációs kiszolgálóval, az alábbiak szerint:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

A beállítások mentését követően tegye a következőket:

1. A folyamatkiszolgáló nyisson meg egy rendszergazdai parancssort.
2. Keresse meg a mappa **%PROGRAMDATA%\ASR\Agent**, és futtassa a parancsot:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>A helyszíni folyamatkiszolgálót proxybeállításainak módosítása

A helyszíni folyamatkiszolgálót egy proxy segítségével csatlakozzon az Azure-ba, ha a proxykiszolgáló beállításai a következőképpen módosíthatja:

1. Jelentkezzen be a folyamat server gépre. 
2. Nyisson meg egy rendszergazdai PowerShell-parancsablakot, és futtassa a következő parancsot:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Keresse meg a mappa **%PROGRAMDATA%\ASR\Agent**, és futtassa a következő parancsot:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Távolítsa el a folyamatkiszolgáló

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Kizárhat bizonyos mappákat a víruskereső szoftver

Ha víruskereső fut egy horizontális felskálázási folyamatkiszolgáló (vagy a fő célkiszolgáló), a következő mappák kizárása víruskereső műveletek:


- C:\Program Files\Microsoft Azure Recovery Services-ügynök
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Folyamat kiszolgáló telepítési könyvtárában. Példa: C:\Program Files (x86)\Microsoft Azure Site Recovery