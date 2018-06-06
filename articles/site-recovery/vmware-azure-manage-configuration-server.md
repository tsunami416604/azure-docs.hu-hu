---
title: A konfigurációs kiszolgáló, az Azure Site Recovery VMware vész-helyreállítási kezelése |} Microsoft Docs
description: Ez a cikk az Azure-bA az Azure Site RecoveryS VMware vész-helyreállítási meglévő konfigurációs kiszolgáló kezelését ismerteti.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 06/04/2018
ms.author: raynew
ms.openlocfilehash: 64f5f2105a9048d649503b0790231676182a4c4f
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737503"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>VMware virtuális gépek esetén a kiszolgáló kezelése

Állít be egy helyszíni konfigurációs kiszolgáló használatakor [Azure Site Recovery](site-recovery-overview.md) vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók Azure-bA. A konfigurációs kiszolgáló közötti kommunikáció koordinálja a helyszíni VMware és az Azure és az adatreplikáció kezeli. Ez a cikk a konfigurációs kiszolgáló felügyelete a telepítés után a gyakori feladatokat foglalja össze.



## <a name="modify-vmware-settings"></a>VMware beállításainak módosítása

A konfigurációs kiszolgáló az alábbiak szerint végezheti el:
    - Jelentkezzen be a virtuális gép, amelyen telepítve van, és Azure Site Recovery a Configuration Manager indítsa el a az asztali parancsikonra.
    - Másik lehetőségként elérheti a kiszolgáló távolról a **https://*ConfigurationServerName*/:44315 /**. Jelentkezzen be rendszergazdai hitelesítő adataival.
   
### <a name="modify-vmware-server-settings"></a>VMware-kiszolgáló beállításainak módosítása

1. A konfigurációs kiszolgáló, a bejelentkezés után egy másik VMware-kiszolgáló társítani, válassza ki a **vCenter-kiszolgáló vagy vSphere ESXi kiszolgáló hozzáadása**.
2. Adja meg a részleteket, és válassza **OK**.


### <a name="modify-credentials-for-automatic-discovery"></a>Automatikus felderítés hitelesítő adatok módosítása

1. Frissítse a hitelesítő adatokat, csatlakozhat a VMware-kiszolgáló, a VMware virtuális gépek automatikus észlelése után bejelentkezhet, jelölje be **szerkesztése**.
2. Adja meg az új hitelesítő adatokat, majd válassza ki **OK**.

    ![VMware módosítása](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>A mobilitási szolgáltatás telepítési hitelesítő adatok módosítása

Automatikusan telepíteni a mobilitási szolgáltatás a replikációs engedélyezte a VMware virtuális gépeken használt hitelesítő adatok módosítása.

1. Után bejelentkezhet, válassza ki a **virtuális gép hitelesítő adatok kezelése**
2. Adja meg az új hitelesítő adatokat, majd válassza ki **OK**.

    ![Módosítsa a mobilitási szolgáltatás hitelesítő adatait](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Proxy beállításainak módosítása

A konfigurációs kiszolgáló gépen internet-hozzáférés az Azure által használt proxy beállítások módosítása. Ha a folyamat kiszolgálógép mellett az alapértelmezett folyamatkiszolgáló a konfigurációs kiszolgáló gépen futó, mindkét gépen beállítások módosítása.

1. Után jelentkezzen be a konfigurációs kiszolgáló, válassza ki a **kapcsolat kezelése**.
2. A proxy értékeinek frissítéséhez. Válassza ki **mentése** frissíteni a beállításait.

## <a name="add-a-network-adapter"></a>Hálózati adapter hozzáadása

A nyitott virtualizációs formátum (OVF) sablont a konfigurációs kiszolgáló virtuális gép egyetlen hálózati adapterrel telepíti.

- Is [vegyen fel egy további adaptert a virtuális gép)](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), de Ön hozzá kell adnia a konfigurációs kiszolgáló regisztrálja a tárolóban.
- Adjon hozzá egy adaptert, a kiszolgáló regisztrálása a tárolóban lévő után, vegye fel az adaptert a virtuális gép tulajdonságai. Majd kell regisztrálja újra a kiszolgálót a tárolóban lévő állapottal.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Regisztrálja újra a konfigurációs kiszolgáló ugyanabban a tárolóban

Ha szeretné újraregisztrálásához a konfigurációs kiszolgáló ugyanabban a tárolóban. Ha rendelkezik egy további folyamat kiszolgálógép mellett az alapértelmezett folyamatkiszolgáló a konfigurációs kiszolgáló gépen futó egyik gépen regisztrálja újra.


  1. Nyissa meg a tároló **kezelése** > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.
  2. A **kiszolgálók**, jelölje be **regisztrációs kulcs letöltése** letölteni a tároló hitelesítési adatait tartalmazó fájlt.
  3. Jelentkezzen be a konfigurációs kiszolgáló számítógépén.
  4. A **%ProgramData%\ASR\home\svsystems\bin**, nyissa meg **cspsconfigtool.exe**.
  5. Az a **tároló regisztrációs** lapon jelölje be **Tallózás**, és keresse meg a letöltött tárolói hitelesítő adatok fájlját.
  6. Ha szükséges, adja meg a proxykiszolgáló kiszolgáló adatait. Ezután kattintson a **Regisztrálás** elemre.
  7. Nyisson meg egy rendszergazda PowerShell parancsablakot, és futtassa a következő parancsot:

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

## <a name="upgrade-the-configuration-server"></a>A konfigurációs kiszolgáló verziófrissítése

Kumulatív frissítése a konfigurációs kiszolgáló futtatja. Frissítések is alkalmazhatók a legfeljebb N-4 verziók. Példa:

- Ha futtatja a 9.7, 9.8, 9.9 vagy 9.10, frissíthet közvetlenül 9.11.
- Ha 9.6 vagy korábbi futtatja, és frissíti a 9.11, először frissítenie kell 9.7 verzióra. Mielőtt 9.11.

A konfigurációs kiszolgáló az összes verziójára történő frissítés összegző mutató hivatkozások találhatók a [wiki frissítések lap](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

A kiszolgáló frissítése az alábbiak szerint:

1. A tárolóban, Ugrás **kezelése** > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.
2. Frissítés érhető el, ha egy hivatkozás megjelenik a **Ügynökverzió** > oszlop.

    ![Frissítés](./media/vmware-azure-manage-configuration-server/update2.png)

1. A telepítő fájl letöltése és a konfigurációs kiszolgáló.

    ![Frissítés](./media/vmware-azure-manage-configuration-server/update1.png)

4. A telepítő futtatásához kattintson duplán.
2. A telepítő észleli az aktuális verzió fut a gépen. Kattintson a **Igen** a frissítés elindításához. 
3. Ha a frissítés befejezése a kiszolgálókonfiguráció ellenőrzi.

    ![Frissítés](./media/vmware-azure-manage-configuration-server/update3.png)

4. Kattintson a **Befejezés** a telepítő bezárásához.


## <a name="delete-or-unregister-a-configuration-server"></a>Törölje vagy a konfigurációs kiszolgáló regisztrációját

1. [Tiltsa le a védelmet](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) alatt a kiszolgáló virtuális gépen.
2. [Szüntesse meg](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) és [törlése](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) összes replikációs házirendet a konfigurációs kiszolgálóról.
3. [Törlés](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) vCenter kiszolgáló vagy vSphere minden gazdagép társított a konfigurációs kiszolgáló.
4. Nyissa meg a tároló **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.
5. Válassza ki az eltávolítani kívánt konfigurációs kiszolgáló. Ezt követően a **részletek** lapon jelölje be **törlése**.

    ![Törli a konfigurációs kiszolgálót](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Törölje a PowerShell használatával

Törölheti a konfigurációs kiszolgáló opcionális PowerShell használatával.

1. [Telepítés](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) az Azure PowerShell modul.
2. Jelentkezzen be az Azure-fiókjával használja a következő parancsot:
    
    `Connect-AzureRmAccount`
3. Válassza ki a tároló-előfizetést.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  A tároló környezet beállítása.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. A konfigurációs kiszolgáló beolvasása.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. A konfigurációs kiszolgáló törlése.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Használhatja a **-Force** a konfigurációs kiszolgáló kényszerített törlésének a Remove-AzureRmSiteRecoveryFabric beállítást.
 


## <a name="renew-ssl-certificates"></a>SSL-tanúsítványainak megújítása

A kiszolgáló rendelkezik egy beépített webkiszolgálón, amely vezénylő tevékenységek a mobilitási szolgáltatás, a folyamat kiszolgálók és a hozzá kapcsolódó fő célkiszolgálóra. A webkiszolgáló az SSL-tanúsítvány használatával hitelesíti az ügyfeleket. A tanúsítvány három év után lejár, és bármikor lehet megújítani.

### <a name="check-expiry"></a>A lejárati ellenőrzése

Konfigurációs kiszolgáló központi telepítése előtt a 2016. május esetén a tanúsítvány lejárati egyéves lett beállítva. Ha olyan tanúsítvány, amely előzetes verziója, az alábbiak történnek:

- Ha a lejárati két hónapig vagy kevesebb, mint a szolgáltatás indításakor értesítések küldése a portálon, és e-mailben (ha előfizetni a Site Recovery-értesítések).
- Értesítésszalagról lapján jelenik meg a tároló erőforrás. További információkért válassza ki a szalagcím.
- Ha megjelenik egy **frissítés most** gomb, azt jelzi, hogy néhány összetevőt a környezetben még nem lett frissítve 9.4.xxxx.x vagy újabb verzió. A tanúsítvány megújításához, frissítse az összetevőket. A régebbi verziói nem újíthatók meg.

### <a name="renew-the-certificate"></a>A tanúsítvány megújítása

1. Nyissa meg a tároló **Site Recovery-infrastruktúra** > **konfigurációs kiszolgáló**. Válassza ki a szükséges konfigurációs kiszolgálót.
2. A lejárati jelenik meg az **konfigurációs kiszolgáló állapotának**.
3. Válassza ki **megújítani a tanúsítványokat**. 


## <a name="next-steps"></a>További lépések

Tekintse át a vész-helyreállítási beállításával kapcsolatos oktatóanyagok [VMware virtuális gépek](vmware-azure-tutorial.md) az Azure-bA.
