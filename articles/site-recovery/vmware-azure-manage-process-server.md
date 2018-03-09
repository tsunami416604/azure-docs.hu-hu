---
title: "Az Azure Site Recovery folyamat kiszolgáló kezelése |} Microsoft Docs"
description: "Ez a cikk ismerteti a VMware virtuális és fizikai kiszolgálók replikálása az Azure Site Recovery beállítása folyamat kiszolgáló kezeléséhez."
services: site-recovery
author: AnoopVasudavan
manager: gauravd
editor: 
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 096b2890d41402448809ae87759fcd6b67bee2fe
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="manage-process-servers"></a>Folyamat-kiszolgálók kezelése

A folyamatkiszolgáló használható, ha VMware virtuális gépek vagy fizikai kiszolgálók Azure replikál alapértelmezés szerint telepítve van a helyszíni konfigurációs kiszolgáló gépen. Nincsenek példányok, amelyben állítson be különálló folyamatkiszolgálót kell néhány:

- Nagy telepítések esetén szükség lehet további helyszíni folyamat kiszolgálók méretezése kapacitás.
- A feladat-visszavétel van szüksége egy ideiglenes folyamatkiszolgáló az Azure-ban beállítása. Törölheti a virtuális gép feladat-visszavétel befejezése. 

Ez a cikk tipikus felügyeleti feladatokhoz további folyamat kiszolgálókon foglalja össze.

## <a name="upgrade-a-process-server"></a>Folyamat-kiszolgáló frissítése

A próbaverziót frissítse egy folyamatot futtató kiszolgálót a helyszíni, vagy az Azure-ban (feladat-visszavétel céljából), az alábbiak szerint:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  Általában az Azure-gyűjtemény lemezkép folyamat-kiszolgáló létrehozása az Azure-ban feladatátvételi céljára használatakor fut. az elérhető legújabb verzióra. A Site Recovery kiadás javítások és továbbfejlesztett rendszeresen csapatának, és naprakész állapotban tartása folyamat kiszolgálók ajánlott.



## <a name="reregister-a-process-server"></a>Regisztrálja újra a folyamatkiszolgálót

Ha szeretne regisztrálja újra a folyamatkiszolgálót, helyileg futó, vagy az Azure, a konfigurációs kiszolgálóval, tegye a következőket:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

A beállítások mentése után tegye a következőket:

1. A folyamatkiszolgáló nyisson meg egy rendszergazdai parancssort.
2. Keresse meg a mappa **%PROGRAMDATA%\ASR\Agent**, és futtassa a parancsot:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Egy helyszíni folyamat kiszolgáló proxy beállításainak módosítása

Ha a folyamatkiszolgáló proxyt az Azure Site Recovery elérésére használt, ez az eljárás használható, ha meglévő proxybeállításokat módosítani kell.

1. Jelentkezzen be a folyamat kiszolgálóként működő számítógép. 
2. Nyisson meg egy rendszergazda PowerShell parancsablakot, és futtassa a következő parancsot:
  ```
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


