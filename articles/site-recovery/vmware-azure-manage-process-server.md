---
title: VMware virtuális gépek és fizikai kiszolgálóvész-helyreállítási folyamatkiszolgálójának kezelése az Azure Site Recovery szolgáltatásban
description: Ez a cikk a VMware virtuális gépek/fizikai kiszolgálók vész-helyreállítási folyamatkiszolgálójának kezelését ismerteti az Azure Site Recovery használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: ef16e3b75ca8e051b1b7abb1a92843279884c697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257250"
---
# <a name="manage-process-servers"></a>Folyamatkiszolgálók kezelése

Ez a cikk a Webhely-helyreállítási folyamatkiszolgáló kezelésével kapcsolatos gyakori feladatokat ismerteti.

A folyamatkiszolgáló segítségével fogadhat, optimalizálhat és küldhet replikációs adatokat az Azure-ba. Emellett leküldéses telepítést hajt végre a Mobility szolgáltatás vmware virtuális gépeken és a replikálni kívánt fizikai kiszolgálókon, és elvégzi a helyszíni gépek automatikus felderítését. A helyszíni VMware virtuális gépek vagy fizikai kiszolgálók Azure-ba replikálása esetén a folyamatkiszolgáló alapértelmezés szerint telepítve van a konfigurációs kiszolgálón. 

- Nagy telepítések esetén szükség lehet további helyszíni folyamatkiszolgálók kapacitás méretezéséhez.
- Az Azure-ból a helyszíni feladat-visszavételhez be kell állítania egy ideiglenes folyamatkiszolgálót az Azure-ban. Törölheti ezt a virtuális gép, ha feladat-visszavétel befejeződött. 

További információ a folyamatkiszolgálóról.


## <a name="upgrade-a-process-server"></a>Folyamatkiszolgáló frissítése

Amikor egy folyamatkiszolgálót telepít a helyszínen, vagy egy Azure virtuális gép feladat-visszavétel, a folyamatkiszolgáló legújabb verziója települ. A Site Recovery csapatok rendszeresen kiadják a javításokat és fejlesztéseket, és javasoljuk, hogy tartsa naprakészen a folyamatkiszolgálókat. A folyamatkiszolgálót a következőképpen frissítheti:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Virtuális gépek áthelyezése a folyamatkiszolgáló terhelésének kiegyenlítésére

A terhelés elosztása a virtuális gépek két folyamatkiszolgáló közötti áthelyezésével, az alábbiak szerint:

1. A tárolóban a **Kezelés csoportban** kattintson a **Hely-helyreállítási infrastruktúra elemre.** A **VMware & fizikai gépek esetében**kattintson a **Konfigurációs kiszolgálók**elemre.
2. Kattintson arra a konfigurációs kiszolgálóra, amellyel a folyamatkiszolgálók regisztrálva vannak.
3. Kattintson arra a folyamatkiszolgálóra, amelynek terheléselosztási forgalmat szeretne betölteni.

    ![Terheléselosztás](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Kattintson a **Terheléselosztás gombra,** válassza ki azt a célfolyamat-kiszolgálót, amelybe gépeket szeretne áthelyezni. Ezután kattintson **az OK gombra**

    ![BetöltőPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Kattintson **a Gépek kiválasztása**gombra, és válassza ki azokat a gépeket, amelyeket az aktuális ról a célfolyamat-kiszolgálóra szeretne áthelyezni. Az átlagos adatváltozás részletei minden virtuális gépen megjelennek. Ezt követően kattintson az **OK** gombra. 
3. A tárolóban figyelje a feladat előrehaladását a Site Recovery feladatok **figyelése** > **Site Recovery jobs**területen.

Körülbelül 15 percet vesz igénybe, amíg a változások megjelennek a portálon. A gyorsabb hatás érdekében [frissítse a konfigurációs kiszolgálót.](vmware-azure-manage-configuration-server.md#refresh-configuration-server)

## <a name="switch-an-entire-workload-to-another-process-server"></a>Teljes munkaterhelés váltása másik folyamatkiszolgálóra

A folyamatkiszolgáló által kezelt teljes munkaterhelés áthelyezése egy másik folyamatkiszolgálóra az alábbiak szerint:

1. A tárolóban a **Kezelés csoportban** kattintson a **Hely-helyreállítási infrastruktúra elemre.** A **VMware & fizikai gépek esetében**kattintson a **Konfigurációs kiszolgálók**elemre.
2. Kattintson arra a konfigurációs kiszolgálóra, amellyel a folyamatkiszolgálók regisztrálva vannak.
3. Kattintson arra a folyamatkiszolgálóra, amelyről át szeretné váltani a munkaterhelést.
4. Kattintson a **Switch**gombra, válassza ki azt a célfolyamat-kiszolgálót, amelyre át szeretné helyezni a munkaterhelést. Ezután kattintson **az OK gombra**

    ![Kapcsoló](media/vmware-azure-manage-process-server/Switch.PNG)

5. A tárolóban figyelje a feladat előrehaladását a Site Recovery feladatok **figyelése** > **Site Recovery jobs**területen.

Körülbelül 15 percet vesz igénybe, amíg a változások megjelennek a portálon. A gyorsabb hatás érdekében [frissítse a konfigurációs kiszolgálót.](vmware-azure-manage-configuration-server.md#refresh-configuration-server)

## <a name="register-a-master-target-server"></a>Fő célkiszolgáló regisztrálása

A fő célkiszolgáló a konfigurációs kiszolgálón és a kibővített folyamatkiszolgálókon található. A konfigurációs kiszolgálón regisztrálni kell. Abban az esetben, ha a regisztráció hiba, hatással lehet a védett elemek állapotát. A fő célkiszolgáló konfigurációs kiszolgálóval történő regisztrálásához jelentkezzen be arra a konfigurációs kiszolgálóra/kibővített folyamatkiszolgálóra, amelyen a regisztráció szükséges. Nyissa meg a **%PROGRAMDATA%\ASR\Agent**mappát, és futtassa a következőt a rendszergazda parancssorában.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Folyamatkiszolgáló újraregisztrálása

Regisztráljon újra egy helyszíni vagy egy Azure-beli virtuális gépet futtató folyamatkiszolgálót a konfigurációs kiszolgálóval az alábbiak szerint:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

A beállítások mentése után tegye a következőket:

1. A folyamatkiszolgálón nyisson meg egy rendszergazdai parancssort.
2. Tallózással keresse meg a **%PROGRAMDATA%\ASR\Agent**mappát, és futtassa a következő parancsot:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Helyszíni folyamatkiszolgáló proxybeállításainak módosítása

Ha egy helyszíni folyamatkiszolgáló proxyt használ az Azure-hoz való csatlakozáshoz, a proxybeállításokat az alábbiak szerint módosíthatja:

1. Jelentkezzen be a folyamatkiszolgáló gépébe. 
2. Nyisson meg egy Rendszergazda PowerShell parancsablakot, és futtassa a következő parancsot:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Tallózással keresse meg a **%PROGRAMDATA%\ASR\Agent**mappát, és futtassa a következő parancsot:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Folyamatkiszolgáló eltávolítása

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Mappák kizárása a víruskereső szoftverből

Ha a víruskereső szoftver kibővített folyamatkiszolgálón (vagy fő célkiszolgálón) fut, zárja ki a következő mappákat a vírusvédelmi műveletekből:


- C:\Program Files\Microsoft Azure helyreállítási szolgáltatások ügynöke
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure webhely helyreállítása
- Folyamat kiszolgáló telepítési könyvtár. Például: C:\Program Files (x86)\Microsoft Azure Site Recovery