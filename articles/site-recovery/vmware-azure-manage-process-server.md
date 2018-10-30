---
title: Kezelheti a folyamatkiszolgáló vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók az Azure-bA az Azure Site Recoveryvel |} A Microsoft Docs
description: Ez a cikk azt ismerteti, felügyelhetők a folyamatkiszolgálók állítható be a vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálóról az Azure Site Recovery használatával.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: 8a9342a2354cd4c92fa0230965b4eef6284ee826
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209122"
---
# <a name="manage-process-servers"></a>Folyamatkiszolgálók kezelése

Alapértelmezés szerint a folyamatkiszolgáló, amikor replikál a VMware virtuális gépek vagy fizikai kiszolgálók Azure-bA a helyszíni konfigurációs kiszolgáló gépen telepítve van. Van néhány kell állíthat be különálló folyamatkiszolgálót példányok:

- Nagyméretű környezetekben szükség lehet további helyszíni folyamatkiszolgálók kapacitást.
- Feladat-visszavételt, szüksége lesz egy ideiglenes folyamatkiszolgáló állítsa be az Azure-ban. Ez a virtuális gép feladat-visszavétel végeztével törölhetők. 

Ez a cikk összegzi az alábbi további folyamatkiszolgálók a tipikus felügyeleti feladatokhoz.

## <a name="upgrade-a-process-server"></a>Frissítse a folyamatkiszolgálót

Frissítse a folyamatkiszolgálót, a helyszínen vagy az Azure-ban (feladat-visszavétel célokra) fut a következő:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  Általában az Azure Gallery-Image használatával folyamatkiszolgáló létrehozása az Azure-ban feladat-visszavétel az alkalmazásában, ha fut a legújabb elérhető verzió. A Site Recovery csapatok kiadás javításokat és rendszeres időközönként fejlesztések, és azt javasoljuk, tartsa naprakészen a folyamatkiszolgálók.



## <a name="reregister-a-process-server"></a>A folyamatkiszolgáló regisztrálása

Ha szeretné a helyszínen futó folyamatkiszolgáló regisztrálása, vagy az Azure-ban, a konfigurációs kiszolgálóval, tegye a következőket:

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

Ha a folyamatkiszolgáló csatlakozni az Azure Site Recovery egy proxyt használ, ez az eljárás használható, ha a meglévő proxybeállításokkal módosítani kell.

1. Jelentkezzen be a folyamat kiszolgáló gép. 
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


