---
title: " A konfigurációs kiszolgáló, az Azure Site Recovery VMware vész-helyreállítási kezelése |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan kezelheti a meglévő konfigurációs kiszolgáló VMware vész-helyreállítási az Azure-ba, az Azure Site Recovery szolgáltatással."
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2018
ms.author: anoopkv
ms.openlocfilehash: 9cdabfb4e24423d76e4f247f184ac4156c3b257b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="manage-the-configuration-server"></a>A konfigurációs kiszolgáló kezelése

Állít be egy helyszíni konfigurációs kiszolgáló használatakor az [Azure Site Recovery](site-recovery-overview.md) VMware virtuális gépek és fizikai kiszolgálók Azure-bA vész-helyreállítási szolgáltatás. A konfigurációs kiszolgáló közötti kommunikáció koordinálja a helyszíni VMware és az Azure és adatreplikáció kezeli. Ez a cikk a gyakori feladatok a konfigurációs kiszolgáló kezeléséhez, miután üzembe helyezésüket követően foglalja össze.

## <a name="modify-vmware-settings"></a>VMware beállításainak módosítása

A VMware-kiszolgáló, amelyhez a kiszolgáló csatlakozik a beállításainak módosítása.

1. Jelentkezzen be a konfigurációs kiszolgálót futtató számítógép.
2. Indítsa el az Azure Site Recovery a Configuration Manager az asztali parancsikonjára. Nyissa meg **https://configuration-server-name/IP:44315**.
3. Kattintson a **kezelése vCenter-kiszolgáló vagy vSPhere ESXi kiszolgáló**:
    - A konfigurációs kiszolgáló társítható egy másik VMware-kiszolgáló, kattintson a **vCenter-kiszolgáló vagy vSphere ESXi kiszolgáló hozzáadása**, és adja meg a kiszolgáló adatait.
    - A VMware virtuális gépek automatikus felderítése a VMware-kiszolgálóhoz való csatlakozáshoz használt hitelesítő adatok frissítéséhez kattintson **szerkesztése**. Adja meg az új hitelesítő adatokkal, és kattintson **OK**.

        ![VMware módosítása](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-vmware-server.png)

## <a name="modify-credentials-for-mobility-service-installation"></a>A mobilitási szolgáltatás telepítési hitelesítő adatok módosítása

Automatikusan telepítse a mobilitási szolgáltatást a replikáció engedélyezése VMware virtuális gépeken használt hitelesítő adatok módosítása.

1. Jelentkezzen be a konfigurációs kiszolgálót futtató számítógép.
2. Indítsa el az Azure Site Recovery a Configuration Manager az asztali parancsikonjára. Nyissa meg **https://configuration-server-name/IP:44315**.
3. Kattintson a **virtuális gép hitelesítő adatok kezelése**, és adja meg az új hitelesítő adatokkal. Kattintson a **OK** frissíteni a beállításait.

    ![Módosítsa a mobilitási szolgáltatás hitelesítő adatait](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Proxy beállításainak módosítása

A konfigurációs kiszolgáló gépen internet-hozzáférés az Azure által használt proxy beállítások módosítása. Ha egy további folyamat kiszolgáló mellett az alapértelmezett folyamatkiszolgáló a konfigurációs kiszolgáló gépen futó gép mindkét gépen beállítások módosítása.

1. Jelentkezzen be a konfigurációs kiszolgálót futtató számítógép.
2. Indítsa el az Azure Site Recovery a Configuration Manager az asztali parancsikonjára. Nyissa meg **https://configuration-server-name/IP:44315**.
3. Kattintson a **kapcsolat kezelése**, és a proxy értékeinek frissítéséhez. Kattintson a **mentése** frissíteni a beállításait.

## <a name="add-a-network-adapter"></a>Hálózati adapter hozzáadása

Az OVF-sablon a konfigurációs kiszolgáló virtuális gép egyetlen hálózati adapterrel telepíti. Is [vegyen fel egy további adaptert a virtuális gép)](how-to-deploy-configuration-server.md#add-an-additional-adapter), de ehhez, mielőtt regisztrálná a konfigurációs kiszolgálót a tárolóban kell.

Ha az adapter hozzáadása után a konfigurációs kiszolgálót a tárolóban már regisztrált van szüksége, szeretné hozzáadni az adaptert a virtuális gép tulajdonságai, majd regisztrálja újra a kiszolgálót a tárolóban lévő állapottal.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Regisztrálja újra a konfigurációs kiszolgáló ugyanabban a tárolóban

Ha szeretné újraregisztrálásához a konfigurációs kiszolgáló ugyanabban a tárolóban. egy további folyamat kiszolgáló mellett az alapértelmezett folyamatkiszolgáló a konfigurációs kiszolgáló gépen futó gép f egyik gépen regisztrálja újra.

  1. Nyissa meg a tároló **kezelése** > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.
  2. A **kiszolgálók**, kattintson a **regisztrációs kulcs letöltése**. Ezzel letölti a tároló hitelesítési adatait tartalmazó fájlt.
  3. Jelentkezzen be a konfigurációs kiszolgáló számítógépén.
  4. A **%ProgramData%\ASR\home\svagent\bin**, nyissa meg **cspsconfigtool.exe**.
  5. Az a **tároló regisztrációs** lapon kattintson a Tallózás gombra, és a letöltött tárolói hitelesítő adatok fájlja található.
  6. Ha szükséges, adja meg a proxykiszolgáló kiszolgáló adatait. Kattintson a **regisztrálása**.
  7. Nyisson meg egy rendszergazda PowerShell parancsablakot, és futtassa a következő parancsot:

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```
## <a name="upgrade-the-configuration-server"></a>A konfigurációs kiszolgáló verziófrissítése

Kumulatív frissítése a konfigurációs kiszolgáló futtatja. Frissítések is alkalmazhatók a legfeljebb N-4 verziók. Példa:

- Ha 9.7, 9.8, 9.9 vagy 9.10 - frissíthet közvetlenül 9.11.
- Ha 9.6 vagy korábbi futtatja, és frissíti a 9.11, először frissítenie kell 9.7 verzióra. Mielőtt 9.11.

A konfigurációs kiszolgáló az összes verziójára történő frissítés összegző mutató hivatkozások találhatók a [wiki frissítések lap](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

A kiszolgáló frissítése az alábbiak szerint:

1. A telepítő fájl letöltése és a konfigurációs kiszolgáló.
2. A telepítő futtatásához kattintson duplán.
3. A telepítő észleli az aktuális verzió fut a gépen.
4. Kattintson a **OK** erősítse meg, és a frissítés futtatásához. 


## <a name="delete-or-unregister-a-configuration-server"></a>Törölje vagy a konfigurációs kiszolgáló regisztrációját

1. Tiltsa le a [tiltsa le a védelmet](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) alatt a kiszolgáló virtuális gépen.
2. [Szüntesse meg](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) és [törlése](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) összes replikációs házirendet a konfigurációs kiszolgálóról.
3. [Törlés](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery) Vcenter kiszolgáló vagy vSphere minden gazdagép társított a konfigurációs kiszolgáló.
4. Nyissa meg a tárolóban, **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**
5. Kattintson a konfigurációs kiszolgáló, hogy el szeretné távolítani. Ezt követően a **részletek** kattintson **törlése**.

    ![Delete configuration server](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Törölje a PowerShell használatával

Opcionálisan törlése a PowerShell használatával konfigurációs kiszolgáló:

1. [Telepítés](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) Azure PowerShell modul
2. Jelentkezzen be az Azure-fiókjával a parancs segítségével:
    
    `Login-AzureRmAccount`
3. Válassza ki a tároló előfizetést:

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  A tároló környezet beállítása:
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. A konfigurációs kiszolgáló beolvasása:

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. A konfigurációs kiszolgáló törlése:

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Használhatja a **-Force** a konfigurációs kiszolgáló kényszerített törlésének a Remove-AzureRmSiteRecoveryFabric beállítást.
 


## <a name="renew-ssl-certificates"></a>SSL-tanúsítványainak megújítása

A kiszolgáló rendelkezik egy beépített webkiszolgálón, amely koordinálja a tevékenységeit a mobilitási szolgáltatás, a folyamat kiszolgálók és a hozzá kapcsolódó fő célkiszolgálóra. A webkiszolgáló az SSL-tanúsítvány használatával hitelesíti az ügyfeleket. A tanúsítvány három év után lejár, és bármikor lehet megújítani.

### <a name="check-expiry"></a>A lejárati ellenőrzése

Konfigurációs kiszolgáló központi telepítése előtt a 2016. május esetén a tanúsítvány lejárati egyéves lett beállítva. Ha egy tanúsítvány előzetes verziója, az alábbiak történnek:

- Ha a lejárati két hónapig vagy kevesebb, mint a szolgáltatás indításakor értesítések küldése a portálon, és e-mailben (ha az Azure Site Recovery értesítések előfizetni).
- Értesítésszalagról lapján jelenik meg a tároló erőforrás. Kattintson a szalagcím további részleteket.
- Ha megjelenik egy **frissítés most** gomb, ez azt jelzi, hogy vannak-e néhány összetevőt a környezetben, amelyek még nem frissültek a 9.4.xxxx.x vagy újabb verzió. A tanúsítvány megújításához összetevő frissítését. A régebbi verziói nem újíthatók meg.

### <a name="renew-the-certificate"></a>A tanúsítvány megújítása

1. Nyissa meg a tároló **Site Recovery-infrastruktúra** > **konfigurációs kiszolgáló**, és kattintson a szükséges konfigurációs kiszolgáló.
2. A lejárati jelenik meg az **konfigurációs kiszolgáló állapota**
3. Kattintson a **megújítani a tanúsítványokat**. 


## <a name="next-steps"></a>További lépések

Tekintse át a vész-helyreállítási beállításával kapcsolatos oktatóanyagok [VMware virtuális gépek](tutorial-vmware-to-azure.md) és fizikai servers(tutorial-physical-to-azure.md) az Azure-bA.
