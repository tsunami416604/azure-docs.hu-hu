---
title: Az Azure Site Recovery VMware-vészhelyreállításhoz használt konfigurációs kiszolgáló kezelése |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan lehet egy meglévő konfigurációs kiszolgáló VMware-vészhelyreállításhoz az Azure-bA az Azure Site RecoveryS kezelése.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: df5b2ecce2a5c9d7c263ee0acc3a49b859b93f7f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346120"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>A konfigurációs kiszolgáló VMware virtuális gépek kezelése

Beállította egy helyszíni konfigurációs kiszolgálót használatakor [Azure Site Recovery](site-recovery-overview.md) vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók Azure-bA. A konfigurációs kiszolgáló közötti kommunikáció koordinálja a helyszíni VMware és az Azure és felügyeli az adatreplikációt. Ez a cikk összefoglalja a gyakori feladatok kezeléséhez a konfigurációs kiszolgáló telepítése után.



## <a name="modify-vmware-settings"></a>VMware-beállításainak módosítása

A konfigurációs kiszolgáló módon érheti el:
    - Jelentkezzen be a virtuális gép, amelyre telepítve van, és indítsa el az Azure Site Recovery Konfigurációkezelő, az asztali parancsikonjára.
    - Azt is megteheti, érheti el a konfigurációs kiszolgáló **https://*ConfigurationServerName*/:44315 /**. Jelentkezzen be rendszergazdai hitelesítő adataival.
   
### <a name="modify-vmware-server-settings"></a>VMware-kiszolgáló beállításainak módosítása

1. Szeretne társítani a konfigurációs kiszolgáló, egy másik VMware-kiszolgáló után jelentkezzen be, válassza ki a **Hozzáadás vCenter-kiszolgáló vagy vSphere ESXi kiszolgáló**.
2. Adja meg a részleteket, és válassza ki **OK**.


### <a name="modify-credentials-for-automatic-discovery"></a>Automatikus felderítés hitelesítő adatok módosítása

1. A bejelentkezést követően, a VMware virtuális gépek automatikus felderítését a VMware-kiszolgálóhoz való kapcsolódáshoz használt hitelesítő adatok frissítéséhez válassza **szerkesztése**.
2. Adja meg az új hitelesítő adatait, és válassza ki **OK**.

    ![VMware módosítása](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>A mobilitási szolgáltatás telepítési hitelesítő adatok módosítása

Automatikusan engedélyezi a replikációt a VMware virtuális gépeken a mobilitási szolgáltatás telepítéséhez használt hitelesítő adatok módosítása

1. Válassza ki a bejelentkezést követően, **virtuális gép hitelesítő adatainak kezelése**
2. Adja meg az új hitelesítő adatait, és válassza ki **OK**.

    ![A mobilitási szolgáltatás hitelesítő adatainak módosítása](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Proxybeállítások módosítása

Módosítsa az internet-hozzáférés az Azure-bA a konfigurációs kiszolgáló gép által használt proxy beállításait. Ha egy folyamat kiszolgáló gép mellett a alapértelmezett folyamatkiszolgáló a konfigurációs kiszolgáló gépen futó, módosítsa a beállításokat mindkét gépen.

1. Bejelentkezés után a konfigurációs kiszolgálóhoz, válassza ki a **kapcsolat kezelése**.
2. Frissítse a proxy. Válassza ki **mentése** a beállítások frissítéséhez.

## <a name="add-a-network-adapter"></a>Hálózati adapter hozzáadása

A nyílt virtualizációs formátum (OVF) sablont a konfigurációs kiszolgáló virtuális gép egyetlen hálózati adapterrel telepíti.

- Is [további adapter hozzáadása a virtuális géphez](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), de Ön hozzá kell adnia a konfigurációs kiszolgálót regisztrálja a tárolóban.
- Adjon hozzá egy adaptert, miután a konfigurációs kiszolgálót regisztrálja a tárolóban, a virtuális gép tulajdonságainak az adapter hozzáadásához. Ezután meg kell regisztrálja újra a kiszolgálót a tárolóban.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Ugyanahhoz a tárolóhoz a konfigurációs kiszolgáló újraregisztrálása

Ha szeretne újraregisztrálásához a konfigurációs kiszolgáló az ugyanahhoz a tárolóhoz. Ha az alapértelmezett folyamatkiszolgáló a konfigurációs kiszolgáló gépen futó mellett egy további kiszolgáló gép regisztrálja újra a gépeket is.


  1. Nyissa meg a tároló **kezelés** > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.
  2. A **kiszolgálók**válassza **regisztrációs kulcs letöltése** töltheti le a tároló hitelesítőadat-fájlja.
  3. Jelentkezzen be a konfigurációs kiszolgáló gép.
  4. A **%ProgramData%\ASR\home\svsystems\bin**, nyissa meg **cspsconfigtool.exe**.
  5. Az a **tár regisztrálása** lapon jelölje be **Tallózás**, és keresse meg a tároló hitelesítőadat-fájlja letöltött.
  6. Ha szükséges, adja meg a proxykiszolgáló adatai. Ezután kattintson a **Regisztrálás** elemre.
  7. Nyisson meg egy rendszergazdai PowerShell-parancsablakot, és futtassa a következő parancsot:
   ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

      >[!NOTE] 
      >Annak érdekében, hogy **kérje le a legújabb tanúsítványokat** horizontális felskálázási folyamatkiszolgáló a konfigurációs kiszolgálóról végrehajtani a parancsot *"< telepítési Drive\Microsoft Azure webhely Recovery\agent\cdpcli.exe >"--registermt parancsot*

  8. Végezetül indítsa újra az obengine futtassa az alábbi parancsot.
  ```
          net stop obengine
          net start obengine

## Upgrade the configuration server

You run update rollups to update the configuration server. Updates can be applied for up to N-4 versions. For example:

- If you run 9.7, 9.8, 9.9, or 9.10, you can upgrade directly to 9.11.
- If you run 9.6 or earlier and you want to upgrade to 9.11, you must first upgrade to version 9.7. before 9.11.

Links to update rollups for upgrading to all versions of the configuration server are available in the [wiki updates page](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade the server as follows:

1. In the vault, go to **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.
2. If an update is available, a link appears in the **Agent Version** > column.
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Download the update installer file to the configuration server.

    ![Update](./media/vmware-azure-manage-configuration-server/update1.png)

4. Double-click to run the installer.
5. The installer detects the current version running on the machine. Click **Yes** to start the upgrade.
6. When the upgrade completes the server configuration validates.

    ![Update](./media/vmware-azure-manage-configuration-server/update3.png)
    
7. Click **Finish** to close the installer.

## Delete or unregister a configuration server

1. [Disable protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) for all VMs under the configuration server.
2. [Disassociate](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) and [delete](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) all replication policies from the configuration server.
3. [Delete](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) all vCenter servers/vSphere hosts that are associated with the configuration server.
4. In the vault, open **Site Recovery Infrastructure** > **Configuration Servers**.
5. Select the configuration server that you want to remove. Then, on the **Details** page, select **Delete**.

    ![Delete configuration server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### Delete with PowerShell

You can optionally delete the configuration server by using PowerShell.

1. [Install](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) the Azure PowerShell module.
2. Sign in to your Azure account by using this command:
    
    `Connect-AzureRmAccount`
3. Select the vault subscription.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Set the vault context.
    
    ```
    $vault = get-AzureRmRecoveryServicesVault-név <name of your vault> Set-AzureRmSiteRecoveryVaultSettings - ARSVault $vault
    ```
4. Retrieve the configuration server.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Delete the configuration server.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> You can use the **-Force** option in Remove-AzureRmSiteRecoveryFabric for forced deletion of the configuration server.
 
## Generate configuration server Passphrase

1. Sign in to your configuration server, and then open a command prompt window as an administrator.
2. To change the directory to the bin folder, execute the command **cd %ProgramData%\ASR\home\svsystems\bin**
3. To generate the passphrase file, execute **genpassphrase.exe -v > MobSvc.passphrase**.
4. Your passphrase will be stored in the file located at **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## Renew SSL certificates

The configuration server has an inbuilt web server, which orchestrates activities of the Mobility Service, process servers, and master target servers connected to it. The web server uses an SSL certificate to authenticate clients. The certificate expires after three years and can be renewed at any time.

### Check expiry

For configuration server deployments before May 2016, certificate expiry was set to one year. If you have a certificate that is going to expire, the following occurs:

- When the expiry date is two months or less, the service starts sending notifications in the portal, and by email (if you subscribed to Site Recovery notifications).
- A notification banner appears on the vault resource page. For more information, select the banner.
- If you see an **Upgrade Now** button, it indicates that some components in your environment haven't been upgraded to 9.4.xxxx.x or higher versions. Upgrade the components before you renew the certificate. You can't renew on older versions.

### Renew the certificate

1. In the vault, open **Site Recovery Infrastructure** > **Configuration Server**. Select the required configuration server.
2. The expiry date appears under **Configuration Server health**.
3. Select **Renew Certificates**. 


## Next steps

Review the tutorials for setting up disaster recovery of [VMware VMs](vmware-azure-tutorial.md) to Azure.
