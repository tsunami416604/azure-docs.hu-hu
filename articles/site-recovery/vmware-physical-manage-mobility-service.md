---
title: A VMware-kiszolgálók mobilitási ügynökének kezelése az Azure Site Recovery szolgáltatással
description: A Mobility Service-ügynök kezelése a VMware virtuális gépek és a fizikai kiszolgálók Azure-ba történő vészhelyreállítása érdekében az Azure Site Recovery szolgáltatás használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 9be758c286e072b0fbefc5f8b20b7accc4e6741b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256964"
---
# <a name="manage-the-mobility-agent"></a>A Mobility-ügynök kezelése 

Mobilitási ügynököt akkor állít be a kiszolgálón, amikor az Azure Site Recovery szolgáltatást használja a VMware virtuális gépek és a fizikai kiszolgálók azure-beli vészhelyreállítása érdekében. A mobilügynök koordinálja a védett gép, a konfigurációs kiszolgáló/kibővített folyamatkiszolgáló közötti kommunikációt, és kezeli az adatreplikációt. Ez a cikk összefoglalja a mobilitási ügynök üzembe helyezését követő gyakori feladatokat.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Mobilitási szolgáltatás frissítése az Azure Portalról

1. Mielőtt elkezdené, győződjön meg arról, hogy a konfigurációs kiszolgáló, a kibővített folyamatkiszolgálók és a fő célkiszolgálók, amelyek a központi telepítés részét képezik, frissülnek, mielőtt a mobilitási szolgáltatást védett gépeken frissítené.
2. A portálon nyissa meg a tárolót > **replikált elemeket.**
3. Ha a konfigurációs kiszolgáló a legújabb verzió, megjelenik egy értesítés, amely így szól: "Új hely helyreállítási replikációs ügynök frissítése érhető el. Kattintson a telepítéshez."

     ![Replikált cikkek ablaka](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Kattintson az értesítésre, és az **Ügynök frissítésében**válassza ki azokat a gépeket, amelyeken frissíteni szeretné a Mobilitás szolgáltatást. Ezt követően kattintson az **OK** gombra.

     ![Replikált elemek virtuális gépének listája](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. A Mobility Service frissítése feladat minden egyes kiválasztott géphez elindul.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Mobilitási szolgáltatás frissítése powershell parancsfájlon keresztül Windows serveren

Mielőtt elkezdené, győződjön meg arról, hogy a konfigurációs kiszolgáló, a kibővített folyamatkiszolgálók és a fő célkiszolgálók, amelyek a központi telepítés részét képezik, frissülnek, mielőtt a mobilitási szolgáltatást védett gépeken frissítené.

A következő parancsfájl használata a mobilitási szolgáltatás frissítése kiszolgálón a tápellátás rendszerhéj-parancsmagon keresztül

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Mobilitási szolgáltatás manuális frissítése minden védett kiszolgálón

1. Mielőtt elkezdené, győződjön meg arról, hogy a konfigurációs kiszolgáló, a kibővített folyamatkiszolgálók és a fő célkiszolgálók, amelyek a központi telepítés részét képezik, frissülnek, mielőtt a mobilitási szolgáltatást védett gépeken frissítené.

2. [Keresse meg az ügynöktelepítőt](vmware-physical-mobility-service-overview.md#locate-installer-files) a kiszolgáló operációs rendszere alapján.

>[!IMPORTANT]
> Ha az Azure IaaS virtuális gép replikálása egyik Azure-régióból a másikba, ne használja ezt a módszert. Az összes rendelkezésre álló lehetőségről [tájékozódhat.](azure-to-azure-autoupdate.md)

3. Másolja a telepítési fájlt a védett számítógépre, és futtassa azt a mobilitási ügynök frissítéséhez.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>A Mobilitási szolgáltatás leküldéses telepítéséhez használt fiók frissítése

A Site Recovery telepítésekor a Mobilrendszer-leküldéses szolgáltatás leküldéses telepítésének engedélyezéséhez megadott egy fiókot, amelyet a Site Recovery folyamatkiszolgáló használ a gépek eléréséhez és a szolgáltatás telepítéséhez, ha a replikáció engedélyezve van a számítógépen. Ha frissíteni szeretné a fiók hitelesítő adatait, kövesse [az alábbi utasításokat.](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)

## <a name="uninstall-mobility-service"></a>Mobilitási szolgáltatás eltávolítása

### <a name="on-a-windows-machine"></a>Windows rendszerű számítógépen

Eltávolítás a felhasználói felületről vagy a parancssorból.

- **A felhasználói felületen:** A vezérlőpulton válassza a **Programok**lehetőséget . Válassza a **Microsoft Azure webhely-helyreállítási mobilitási szolgáltatás/fő célkiszolgáló** > **eltávolítása lehetőséget.**
- **A parancssorból**: Nyisson meg egy parancssorablakot rendszergazdaként a számítógépen. Futtassa az alábbi parancsot: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Linux gépen
1. A Linux-gépen jelentkezzen be **gyökérfelhasználóként.**
2. A terminálon nyissa meg a /usr/local/ASR.In a terminal, go to /usr/local/ASR.
3. Futtassa az alábbi parancsot:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Site Recovery VSS-szolgáltató telepítése a forrásszámítógépre

Az Azure Site Recovery VSS-szolgáltató szükséges a forrásgépen alkalmazás konzisztenciapontok létrehozásához. Ha a szolgáltató telepítése nem sikerült a leküldéses telepítéssorán, kövesse az alábbi irányelveket a manuális telepítéshez.

1. Nyissa meg a rendszergazdai cmd ablakot.
2. Nyissa meg a mobilitási szolgáltatás telepítési helyét. (Pl. - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Futtassa a parancsfájlt InMageVSSProvider_Uninstall.cmd . Ezzel eltávolítja a szolgáltatást, ha már létezik.
4. Futtassa a parancsfájlt InMageVSSProvider_Install.cmd-vel a VSS-szolgáltató manuális telepítéséhez.

## <a name="next-steps"></a>További lépések

- [Vészhelyreállítás beállítása VMware virtuális gépekhez](vmware-azure-tutorial.md)
- [Vészhelyreállítás beállítása fizikai kiszolgálókhoz](physical-azure-disaster-recovery.md)
