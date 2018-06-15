---
title: A konfigurációs kiszolgáló, az Azure Site Recovery VMware vész-helyreállítási kezelése |} Microsoft Docs
description: Ez a cikk az Azure-bA az Azure Site Recovery VMware vész-helyreállítási meglévő konfigurációs kiszolgáló kezelését ismerteti.
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2018
ms.author: anoopkv
ms.openlocfilehash: 80426b076481aaf2001644498da8cdce6f0311ab
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768507"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>VMware virtuális gépek esetén a kiszolgáló kezelése

Állít be egy helyszíni konfigurációs kiszolgáló használatakor [Azure Site Recovery](site-recovery-overview.md) vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók Azure-bA. A konfigurációs kiszolgáló közötti kommunikáció koordinálja a helyszíni VMware és az Azure és az adatreplikáció kezeli. Ez a cikk a konfigurációs kiszolgáló felügyelete a telepítés után a gyakori feladatokat foglalja össze.


## <a name="modify-vmware-settings"></a>VMware beállításainak módosítása

A VMware-kiszolgáló, amelyhez a kiszolgáló csatlakozik a beállításainak módosítása.

1. Jelentkezzen be a konfigurációs kiszolgálót futtató számítógép.
2. Indítsa el az Azure Site Recovery a Configuration Manager az asztali parancsikonra. Nyissa meg [Ez a hivatkozás](https://configuration-server-name/IP:44315).
3. Válassza ki **kezelése vCenter-kiszolgáló vagy vSPhere ESXi kiszolgáló**, majd tegye a következőket:

    * Válassza ki a konfigurációs kiszolgáló hozzárendelni egy másik VMware-kiszolgáló, **vCenter-kiszolgáló vagy vSphere ESXi kiszolgáló hozzáadása**. Adja meg a kiszolgáló adatait.

    * Válassza ki a VMware virtuális gépek automatikus felderítése a VMware-kiszolgálóhoz való csatlakozáshoz használt hitelesítő adatok frissítéséhez **szerkesztése**. Adja meg az új hitelesítő adatokat, majd válassza ki **OK**.

    ![VMware módosítása](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-vmware-server.png)

## <a name="modify-credentials-for-mobility-service-installation"></a>A mobilitási szolgáltatás telepítési hitelesítő adatok módosítása

Automatikusan telepíteni a mobilitási szolgáltatás a replikációs engedélyezte a VMware virtuális gépeken használt hitelesítő adatok módosítása.

1. Jelentkezzen be a konfigurációs kiszolgálót futtató számítógép.
2. Indítsa el a Site Recovery a Configuration Manager az asztali parancsikonjára. Nyissa meg [Ez a hivatkozás](https://configuration-server-name/IP:44315).
3. Válassza ki **virtuális gép hitelesítő adatok kezelése**, és adja meg az új hitelesítő adatokat. Válassza ki **OK** frissíteni a beállításait.

    ![Módosítsa a mobilitási szolgáltatás hitelesítő adatait](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Proxy beállításainak módosítása

A konfigurációs kiszolgáló gépen internet-hozzáférés az Azure által használt proxy beállítások módosítása. Ha a folyamat kiszolgálógép mellett az alapértelmezett folyamatkiszolgáló a konfigurációs kiszolgáló gépen futó, mindkét gépen beállítások módosítása.

1. Jelentkezzen be a konfigurációs kiszolgálót futtató számítógép.
2. Indítsa el a Site Recovery a Configuration Manager az asztali parancsikonjára. Nyissa meg [Ez a hivatkozás](https://configuration-server-name/IP:44315).
3. Válassza ki **kapcsolat kezelése**, és a proxy értékeinek frissítéséhez. Válassza ki **mentése** frissíteni a beállításait.

## <a name="add-a-network-adapter"></a>Hálózati adapter hozzáadása

A nyitott virtualizációs formátum (OVF) sablont a konfigurációs kiszolgáló virtuális gép egyetlen hálózati adapterrel telepíti. Is [vegyen fel egy további adaptert a virtuális gép)](how-to-deploy-configuration-server.md#add-an-additional-adapter), de a hozzá kell adnia azt, mielőtt regisztrálná a konfigurációs kiszolgálót a tárolóban lévő állapottal.

Adjon hozzá egy adaptert, a kiszolgáló regisztrálása a tárolóban lévő után, vegye fel az adaptert a virtuális gép tulajdonságai. Majd regisztrálja újra a kiszolgálót a tárolóban lévő állapottal.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Regisztrálja újra a konfigurációs kiszolgáló ugyanabban a tárolóban

Ha szeretné újraregisztrálásához a konfigurációs kiszolgáló ugyanabban a tárolóban. Ha rendelkezik egy további folyamat kiszolgálógép mellett az alapértelmezett folyamatkiszolgáló a konfigurációs kiszolgáló gépen futó egyik gépen regisztrálja újra.


  1. Nyissa meg a tároló **kezelése** > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.
  2. A **kiszolgálók**, jelölje be **regisztrációs kulcs letöltése** letölteni a tároló hitelesítési adatait tartalmazó fájlt.
  3. Jelentkezzen be a konfigurációs kiszolgáló számítógépén.
  4. A **%ProgramData%\ASR\home\svagent\bin**, nyissa meg **cspsconfigtool.exe**.
  5. Az a **tároló regisztrációs** lapon jelölje be **Tallózás** , és keresse meg a letöltött tárolói hitelesítő adatok fájlja.
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

1. A telepítő fájl letöltése és a konfigurációs kiszolgáló.
2. A telepítő futtatásához kattintson duplán.
3. A telepítő észleli az aktuális verzió fut a gépen.
4. Válassza ki **OK** erősítse meg, és a frissítés futtatásához. 


## <a name="delete-or-unregister-a-configuration-server"></a>Törölje vagy a konfigurációs kiszolgáló regisztrációját

1. Tiltsa le a [tiltsa le a védelmet](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) alatt a kiszolgáló virtuális gépen.
2. [Szüntesse meg](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) és [törlése](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) összes replikációs házirendet a konfigurációs kiszolgálóról.
3. [Törlés](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery) vCenter kiszolgáló vagy vSphere minden gazdagép társított a konfigurációs kiszolgáló.
4. Nyissa meg a tároló **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.
5. Válassza ki az eltávolítani kívánt konfigurációs kiszolgáló. Ezt követően a **részletek** lapon jelölje be **törlése**.

    ![Delete configuration server](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Törölje a PowerShell használatával

Törölheti a konfigurációs kiszolgáló opcionális PowerShell használatával.

1. [Telepítés](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) az Azure PowerShell modul.
2. Jelentkezzen be az Azure-fiókjával használja a következő parancsot:
    
    `Login-AzureRmAccount`
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

Tekintse át a vész-helyreállítási beállításával kapcsolatos oktatóanyagok [VMware virtuális gépek](tutorial-vmware-to-azure.md) az Azure-bA.
