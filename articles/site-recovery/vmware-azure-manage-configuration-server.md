---
title: Az Azure Site Recovery VMware-vészhelyreállításhoz használt konfigurációs kiszolgáló kezelése |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan lehet egy meglévő konfigurációs kiszolgáló VMware-vészhelyreállításhoz az Azure-bA az Azure Site RecoveryS kezelése.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 29fa232e328ec0b16cb4e00fb16e3be458936cd7
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857248"
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
      net stop obengine
      net start obengine
      ```

## <a name="upgrade-the-configuration-server"></a>A konfigurációs kiszolgáló frissítése

Kumulatív frissítés a konfigurációs kiszolgálót frissíteni fogja futtatni. Frissítések akár N-4 verziók esetében is alkalmazható. Példa:

- Ha 9.7, 9.8, 9.9 vagy 9.10 futtatja, frissítheti, közvetlenül a 9.11.
- Ha 9.6 vagy korábbi, és a 9.11 frissíteni, először frissítenie kell 9.7 verzióra. Mielőtt 9.11.

Kumulatív frissítések a configuration server összes verziójára való frissítéshez mutató hivatkozások találhatók a [frissítések wikioldal](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

A kiszolgáló frissítése a következőképpen:

1. A tárolót, lépjen a **kezelés** > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.
2. Frissítés érhető el, ha egy hivatkozás megjelenik a **Ügynökverzió** > oszlop.
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Töltse le a frissítést telepítő a konfigurációs kiszolgálón.

    ![Frissítés](./media/vmware-azure-manage-configuration-server/update1.png)

4. A telepítő futtatásához kattintson duplán.
5. A telepítő észleli a gépen futó verziója. Kattintson a **Igen** a frissítés elindításához.
6. A frissítés befejezése után ellenőrzi a kiszolgáló konfigurációját.

    ![Frissítés](./media/vmware-azure-manage-configuration-server/update3.png)
    
7. Kattintson a **Befejezés** gombra kattintva zárja be a telepítőt.

## <a name="delete-or-unregister-a-configuration-server"></a>Törölje vagy a konfigurációs kiszolgáló regisztrációjának törlése

1. [Tiltsa le a védelmet](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) a konfigurációs kiszolgáló területén az összes virtuális gép.
2. [Szüntesse meg az](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) és [törlése](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) minden replikációs házirendek a konfigurációs kiszolgálóról.
3. [Törlés](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) minden vCenter kiszolgálók/vSphere-gazdagépek a konfigurációs kiszolgáló társítva.
4. Nyissa meg a tároló **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.
5. Válassza ki a konfigurációs kiszolgáló, amely a el kívánja távolítani. Ezután a a **részletek** lapon jelölje be **törlése**.

    ![Konfigurációs kiszolgáló törlése](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Törölje a PowerShell-lel

Szükség esetén törölheti a konfigurációs kiszolgáló PowerShell-lel.

1. [Telepítés](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) az Azure PowerShell modult.
2. Jelentkezzen be az Azure-fiókjába a következő paranccsal:
    
    `Connect-AzureRmAccount`
3. Válassza ki a tároló előfizetését.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Állítsa be a tárolási környezet.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. A konfigurációs kiszolgáló lekéréséhez.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. A konfigurációs kiszolgáló törlése.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Használhatja a **-Force** kényszerített törlése a konfigurációs kiszolgáló, a Remove-AzureRmSiteRecoveryFabric lehetőséget.
 
## <a name="generate-configuration-server-passphrase"></a>Konfigurációs kiszolgáló hozzáférési kód létrehozása

1. Jelentkezzen be a konfigurációs kiszolgáló, és nyissa meg egy parancssori ablakot rendszergazdaként.
2. Módosítsa a könyvtárat a bin mappát, hajtsa végre a parancsot **cd %ProgramData%\ASR\home\svsystems\bin**
3. A hozzáférési fájl kódjának létrehozásához hajtsa végre a **genpassphrase.exe - v > MobSvc.passphrase**.
4. A megadott jelszó lesz tárolva helyen található fájl **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="renew-ssl-certificates"></a>SSL-tanúsítványok megújítása

A konfigurációs kiszolgáló rendelkezik egy beépített web server, mely a mobilitási szolgáltatást, folyamatkiszolgálók és fő célkiszolgálók ahhoz kapcsolódó tevékenységeket koordinálja. A webkiszolgáló egy SSL-tanúsítványt használ az ügyfelek hitelesítéséhez. A tanúsítvány három év után lejár, és bármikor meg lehet újítani.

### <a name="check-expiry"></a>Lejáratának ellenőrzése

Konfigurációs kiszolgáló központi telepítése előtt a 2016. május esetén a tanúsítvány lejárati egy év lett beállítva. Ha rendelkezik egy tanúsítvánnyal, amelyet szeretne érvényessége lejár, az alábbiak történnek:

- Ha a lejárati dátum két hónapig vagy kevesebb, mint a szolgáltatás elindul, értesítések küldése a portálon, és e-mailben (ha van, amelyre Ön feliratkozott a Site Recovery-értesítések).
- Egy értesítési szalagcím jelenik meg az erőforrás-tároló oldalon. További információkért válassza ki a szalagcímet.
- Ha megjelenik egy **frissítés most** gombra, az azt jelzi, hogy néhány összetevőt a környezetben még nem lett frissítve 9.4.xxxx.x vagy újabb verzió. Frissítse az összetevőket, a tanúsítvány megújításához. Régebbi verziójával nem újíthatók.

### <a name="renew-the-certificate"></a>A tanúsítvány megújítása

1. Nyissa meg a tároló **Site Recovery-infrastruktúra** > **konfigurációs kiszolgáló**. Válassza ki a szükséges konfigurációs kiszolgálót.
2. Megjelenik a lejárati dátum **konfigurációs kiszolgáló állapota**.
3. Válassza ki **tanúsítványok megújítása**. 


## <a name="next-steps"></a>További lépések

Tekintse át az oktatóanyagok, valamint beállításának [VMware virtuális gépek](vmware-azure-tutorial.md) az Azure-bA.
