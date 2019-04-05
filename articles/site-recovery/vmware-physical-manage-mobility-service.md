---
title: A vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók az Azure Site Recovery mobilitási ügynök felügyelete |} A Microsoft Docs
description: A mobilitási szolgáltatás ügynökének vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók Azure-bA az Azure Site Recovery szolgáltatás használatával kezelheti.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 69b8e1c533747d1bade69949911ea43f299f49e9
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59043808"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>Mobilitási szolgáltatást a védett számítógépek kezelése

Beállíthatja a mobilitási ügynököt a kiszolgálón vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók Azure-bA az Azure Site Recovery használatakor. A mobilitási ügynök a védett számítógépen, a konfigurációs kiszolgáló vagy kibővíthető folyamatkiszolgáló közötti kommunikáció koordinálását, és felügyeli az adatreplikációt. Ez a cikk összefoglalja a gyakori feladatok kezeléséhez a mobilitási ügynök telepítése után.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Mobilitási szolgáltatás frissítése az Azure Portalról

1. Megkezdése előtt győződjön meg arról, hogy a konfigurációs kiszolgáló, horizontális felskálázási folyamatkiszolgáló, és bármely fő célkiszolgálókat, amelyek a központi telepítés részei frissítve lett, mielőtt frissítené a mobilitási szolgáltatás a védett gépek.
2. A portálon nyissa meg a tároló > **replikált elemek**.
3. Ha a konfigurációs kiszolgálót a legújabb verzióra, megjelenik egy értesítés, olvasó, "új Site recovery replikációs ügynökének frissítése érhető el. Telepítéséhez kattintson ide."

     ![Replikált elemek ablak](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Kattintson az értesítésre, és a **ügynök frissítése**, válassza ki a gépeket, amelyeken szeretné frissíteni a mobilitási szolgáltatást. Ezután kattintson az **OK** gombra.

     ![Replikált elemek Virtuálisgép-lista](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. A mobilitási szolgáltatás frissítése feladat elindul, a kiválasztott gépek mindegyikéhez.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Mobilitási szolgáltatás frissítése a Windows server powershell-parancsfájl segítségével

Az alábbi parancsfájl power shell parancsmag segítségével a kiszolgálón a mobilitási szolgáltatás frissítése

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Frissítse a mobilitási szolgáltatás leküldéses telepítéséhez használt fiók

A mobilitási szolgáltatás leküldéses telepítésének engedélyezése a Site Recovery üzembe helyezésekor megadott egy fiókot, amely a Site Recovery folyamatkiszolgáló használja a gépek eléréséhez, és telepítse a szolgáltatást, amikor a replikáció engedélyezve van a számítógépen. Ha szeretné frissíteni a fiók hitelesítő adatait, hajtsa végre a [ezek az utasítások](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Távolítsa el a mobilitási szolgáltatás

### <a name="on-a-windows-machine"></a>Egy Windows-gépen

Távolítsa el a felhasználói felületen vagy a parancssorból.

- **A felhasználói felületről**: A Vezérlőpult a gép, jelölje be a **programok**. Válassza ki **a Microsoft Azure Site Recovery mobilitási szolgáltatás vagy fő célkiszolgálóként kiszolgáló** > **Eltávolítás**.
- **Egy parancssorból**: Nyisson meg egy parancssori ablakot rendszergazdaként azon a gépen. Futtassa az alábbi parancsot: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Linux rendszerű gépen
1. A Linux rendszerű gépen jelentkezzen be egy **legfelső szintű** felhasználói.
2. A terminálban keresse /user/local/ASR.
3. Futtassa az alábbi parancsot:
    ```
    uninstall.sh -Y

## Install Site Recovery VSS provider on source machine

Azure Site Recovery VSS provider is required on the source machine to generate application consistency points. If the installation of the provider didn't succeed through push installation, follow the below given guidelines to install it manually.

1. Open admin cmd window.
2. Navigate to the mobility service installation location. (Eg - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Run the script InMageVSSProvider_Uninstall.cmd . This will uninstall the service if it already exists.
4. Run the script InMageVSSProvider_Install.cmd to install the VSS provider manually.

## Next steps

- [Set up disaster recovery for VMware VMs](vmware-azure-tutorial.md)
- [Set up disaster recovery for physical servers](physical-azure-disaster-recovery.md)
