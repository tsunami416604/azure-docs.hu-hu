---
title: A mobilitási ügynök kezelése a kiszolgálókon a VMware virtuális gépek és a fizikai kiszolgálók vész-helyreállításához Azure Site Recoverykal | Microsoft Docs
description: A mobilitási szolgáltatás ügynökének kezelése a VMware virtuális gépek és fizikai kiszolgálók az Azure-ba való vész-helyreállításához a Azure Site Recovery szolgáltatás használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 0a8b3a8bcfc2aa8270d7be140a94e5b83973f3e5
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972129"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>A mobilitási ügynök kezelése a védett gépeken

Ha Azure Site Recoveryt használ a VMware virtuális gépek és a fizikai kiszolgálók Azure-ba történő helyreállításához, akkor a mobilitási ügynököt a kiszolgálón kell beállítania. A mobilitási ügynök koordinálja a védett számítógép, a konfigurációs kiszolgáló/kibővíthető folyamat kiszolgálója közötti kommunikációt, és kezeli az adatreplikációt. Ez a cikk összefoglalja a mobilitási ügynök telepítés utáni általános feladatait.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Mobilitási szolgáltatás frissítése Azure Portal

1. Mielőtt elkezdené, győződjön meg arról, hogy a konfigurációs kiszolgáló, a kibővíthető folyamat-kiszolgálók és a telepítés részét képező fő célkiszolgáló frissítése megtörtént, mielőtt frissíti a mobilitási szolgáltatást a védett gépeken.
2. A portálon nyissa meg a tároló > **replikált elemeket**.
3. Ha a konfigurációs kiszolgáló a legújabb verzió, megjelenik egy értesítés, amely beolvassa az "új site Recovery replikációs ügynök frissítése" című témakört. Kattintson ide a telepítéshez. "

     ![Replikált elemek ablak](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Kattintson az értesítésre, majd az **ügynök frissítése**területen válassza ki azokat a gépeket, amelyeken frissíteni kívánja a mobilitási szolgáltatást. Ezután kattintson az **OK** gombra.

     ![Replikált elemek virtuálisgép-listája](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. A mobilitási szolgáltatás frissítése feladata minden kiválasztott gépen elindul.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Mobilitási szolgáltatás frissítése PowerShell-parancsfájllal a Windows Serveren

A következő szkripttel frissítheti a mobilitási szolgáltatást a kiszolgálón a Power Shell parancsmag használatával

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>A mobilitási szolgáltatás leküldéses telepítéséhez használt fiók frissítése

Site Recovery üzembe helyezése esetén a mobilitási szolgáltatás leküldéses telepítésének engedélyezéséhez olyan fiókot adott meg, amelyet a Site Recovery Process Server a gépek eléréséhez használ, és telepíti a szolgáltatást, amikor a replikáció engedélyezve van a gépen. Ha frissíteni szeretné a fiók hitelesítő adatait, kövesse az [alábbi utasításokat](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Mobilitási szolgáltatás eltávolítása

### <a name="on-a-windows-machine"></a>Windows rendszerű gépen

Távolítsa el a felhasználói felületet vagy a parancssorból.

- **A felhasználói felületen**: A gép Vezérlőpultján válassza a **programok**lehetőséget. Válassza a **Microsoft Azure site Recovery mobilitási szolgáltatás/fő célkiszolgáló** > **eltávolítása**lehetőséget.
- **Parancssorból**: Nyisson meg egy parancssori ablakot rendszergazdaként a gépen. Futtassa a következő parancsot: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Linux rendszerű gépen
1. A Linux rendszerű gépen jelentkezzen be **root** felhasználóként.
2. A terminálban nyissa meg a/usr/local/ASR
3. Futtassa a következő parancsot:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Site Recovery VSS-szolgáltató telepítése a forrásoldali gépen

Azure Site Recovery VSS-szolgáltatóra van szükség a forrásoldali gépen az alkalmazás konzisztencia-pontjainak létrehozásához. Ha a szolgáltató telepítése nem sikerült a leküldéses telepítéssel, az alábbi útmutatást követve manuálisan telepítheti azt.

1. Nyissa meg a rendszergazda cmd ablakot.
2. Navigáljon a mobilitási szolgáltatás telepítési helyére. (Például – C:\Program Files (x86) \Microsoft Azure site Recovery\agent)
3. Futtassa a InMageVSSProvider_Uninstall. cmd parancsfájlt. Ez a művelet eltávolítja a szolgáltatást, ha az már létezik.
4. A VSS-szolgáltató manuális telepítéséhez futtassa a InMageVSSProvider_Install. cmd parancsfájlt.

## <a name="next-steps"></a>További lépések

- [Vész-helyreállítás beállítása VMware virtuális gépekhez](vmware-azure-tutorial.md)
- [A fizikai kiszolgálók vész-helyreállításának beállítása](physical-azure-disaster-recovery.md)
