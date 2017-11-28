---
title: " Az Azure Site Recovery konfigurációs kiszolgáló kezelése |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan kezelheti a konfigurációs kiszolgáló."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 11/23/2017
ms.author: anoopkv
ms.openlocfilehash: 4f23750ff1ba261ea3cf782f7c85858e46632cfa
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="manage-a-configuration-server"></a>A konfigurációs kiszolgáló kezelése

Konfigurációs kiszolgáló között a Site Recovery services és a helyszíni infrastruktúra koordinátora működik. Ez a cikk ismerteti, hogyan állítsa be, konfigurálhatja és kezelheti a konfigurációs kiszolgáló.

> [!NOTE]
> [Kapacitástervezés](site-recovery-capacity-planner.md) egy fontos lépés annak érdekében, hogy telepít a konfigurációs kiszolgáló konfigurálása, hogy a csomagok a terhelési követelményeknek. Tudjon meg többet az [méretezése a konfigurációs kiszolgáló követelményei](#sizing-requirements-for-a-configuration-server).


## <a name="prerequisites"></a>Előfeltételek
A minimális hardver-, szoftver, és a konfigurációs kiszolgáló beállításához szükséges hálózati konfiguráció az alábbiakban.
> [!IMPORTANT]
> VMware virtuális gépek védelme a konfigurációs kiszolgáló telepítésekor ajánlott telepíteni azt egy **magas rendelkezésre ÁLLÁS** virtuális gépet.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-configuration-server-software"></a>A konfigurációs kiszolgáló szoftver letöltése

1. Jelentkezzen be az Azure-portálon, és keresse meg a Recovery Services-tároló.
2. Keresse meg a **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók** (a VMware és fizikai gépek).

  ![Kiszolgálók lap hozzáadása](./media/site-recovery-vmware-to-azure-manage-configuration-server/AddServers.png)
3. Kattintson a **+ kiszolgálók** gombra.
4. Az a **kiszolgáló hozzáadása** lapon, a Letöltés gombra kattintva töltse le a regisztrációs kulcsot. A konfigurációs kiszolgáló telepítése az Azure Site Recovery szolgáltatásban való regisztrálása során kell ezt a kulcsot.
5. Kattintson a **töltse le a Microsoft Azure Site Recovery az egységes telepítő** hivatkozást a konfigurációs kiszolgáló legújabb verziójának letöltéséhez.

  ![Letöltési oldala](./media/site-recovery-vmware-to-azure-manage-configuration-server/downloadcs.png)

  > [!TIP]
  A konfigurációs kiszolgáló legújabb verziója letölthető közvetlenül [Microsoft Download Center letöltési oldala](http://aka.ms/unifiedsetup)

## <a name="installing-and-registering-a-configuration-server-from-gui"></a>Telepítése és regisztrálása a konfigurációs kiszolgáló grafikus felhasználói felület
[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

## <a name="installing-and-registering-a-configuration-server-using-command-line"></a>Telepítése és regisztrálása a konfigurációs kiszolgáló parancssor használatával

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Példa
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="configuration-server-installer-command-line-arguments"></a>Konfigurációs kiszolgáló telepítő parancssori argumentumokat.
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-mysql-credentials-file"></a>Egy MySql hitelesítőadat-fájlt létrehozni
MySQLCredsFilePath paraméter egy fájl fogadja bemeneti adatként. Hozza létre a fájlt a következő formátumban, és adja át bemeneti MySQLCredsFilePath paraméterként.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-a-proxy-settings-configuration-file"></a>Proxykiszolgáló-beállítások konfigurációs fájl létrehozása
ProxySettingsFilePath paraméter egy fájl fogadja bemeneti adatként. Hozza létre a fájlt a következő formátumban, és adja át bemeneti ProxySettingsFilePath paraméterként.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-configuration-server"></a>Konfigurációs kiszolgáló proxy beállításainak módosítása
1. Jelentkezzen be a konfigurációs kiszolgáló.
2. Indítsa el a használatával a helyi cspsconfigtool.exe a.
3. Kattintson a **tároló regisztrációs** fülre.
4. Töltsön le egy új tároló regisztrációs fájlt a portálról, és adja meg az eszköz számára.

  ![register-konfiguráció-kiszolgáló](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
5. Adja meg az új proxykiszolgáló adatait, és kattintson a **regisztrálása** gombra.
6. Nyisson meg egy rendszergazda PowerShell parancsablakot.
7. A következő parancsot
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Ha a konfigurációs kiszolgálóhoz kapcsolódó kibővített folyamat kiszolgálóval rendelkezik, akkor [hárítsa el a proxykiszolgáló beállításait a kibővített folyamat összes kiszolgálójára](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#modifying-proxy-settings-for-scale-out-process-server) a környezetben.

## <a name="modify-user-accounts-and-passwords"></a>Felhasználói fiókok és jelszavak módosítása

A felhasználói fiókok kezeléséhez használt a CSPSConfigTool.exe **VMware virtuális gépek automatikus felderítését** és végrehajtásához ** leküldéses telepítési a mobilitási szolgáltatást a védett gépek. 

1. Jelentkezzen be a konfigurációs kiszolgáló.
2. A CSPSConfigtool.exe érhető el az asztali parancsikonra kattintva indíthatja el.
3. Kattintson a **fiókok kezelése** fülre.
4. Válassza ki a fiókot, amelyhez a jelszót kell módosítani, majd kattintson a a **szerkesztése** gombra.
5. Adja meg az új jelszót, és kattintson a **OK**


## <a name="re-register-a-configuration-server-with-the-same-recovery-services-vault"></a>Regisztrálja újra a konfigurációs kiszolgáló a azonos Recovery Services-tároló
  1. Jelentkezzen be a konfigurációs kiszolgáló.
  2. Indítsa el a cspsconfigtool.exe használatával a parancsikont az asztalon.
  3. Kattintson a **tároló regisztrációs** fülre.
  4. Töltsön le új regisztrációs fájlt a portálról, és adja meg az eszköz számára.
        ![register-konfiguráció-kiszolgáló](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
  5. Adja meg a proxykiszolgáló adatait, és kattintson a **regisztrálása** gombra.  
  6. Nyisson meg egy rendszergazda PowerShell parancsablakot.
  7. A következő parancsot

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  Ha a konfigurációs kiszolgálóhoz kapcsolódó kibővített folyamat kiszolgálóval rendelkezik, akkor [regisztrálja újra a kibővített folyamat kiszolgálók](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#re-registering-a-scale-out-process-server) a környezetben.

## <a name="registering-a-configuration-server-with-a-different-recovery-services-vault"></a>A konfigurációs kiszolgáló regisztrálása egy másik Recovery Services-tároló.

> [!WARNING]
> A következő lépés megszünteti a konfiguráció az aktuális tárolóból, és a replikáció az összes védett virtuális gépet, a konfigurációs kiszolgáló.

1. Jelentkezzen be a konfigurációs kiszolgáló.
2. Futtassa a parancsot egy rendszergazdai parancssorból

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Indítsa el a használatával a helyi cspsconfigtool.exe a.
4. Kattintson a **tároló regisztrációs** fülre.
5. Töltsön le új regisztrációs fájlt a portálról, és adja meg az eszköz számára.

    ![register-konfiguráció-kiszolgáló](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
6. Adja meg a proxykiszolgáló adatait, és kattintson a **regisztrálása** gombra.  
7. Nyisson meg egy rendszergazda PowerShell parancsablakot.
8. A következő parancsot
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrading-a-configuration-server"></a>A konfigurációs kiszolgáló frissítése

> [!WARNING]
> Frissítése akkor támogatott, csak a N-4th verzióra. Például ha a legújabb verzióra a piacon 9.11, majd frissítheti 9.10, 9.9, 9.8 vagy 9.7 verziójából származó közvetlenül a 9.11. De ha bármelyik verzión kisebb vagy egyenlő 9.6, majd frissítenie kell a legalább előtt 9.7 be a konfigurációs kiszolgálóra a legújabb frissítésekkel is alkalmazhat. Töltse le a hivatkozások a korábbi verziót alatt találhatók [Azure Site Recovery szolgáltatás frissítések](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)

1. Töltse le a frissítéstelepítő a konfigurációs kiszolgálón.
2. A telepítő duplán kattintva indítsa el a telepítő.
3. A telepítő észleli a Site Recovery összetevők a jóváhagyást a számítógépen, valamint kérdés jelen verziója. 
4. Kattintson az OK gombra kattintva a erősítse meg, és folytassa a frissítést.


## <a name="delete-or-unregister-a-configuration-server"></a>Törölje vagy a konfigurációs kiszolgáló regisztrációját

> [!WARNING]
> Ellenőrizze a konfigurációs kiszolgáló leszerelése megkezdése előtt.
> 1. [Tiltsa le a védelmet](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) az összes virtuális gép ezen a konfigurációs kiszolgálón.
> 2. [Szüntesse meg](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) és [törlése](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) összes replikációs házirendet a konfigurációs kiszolgálóról.
> 3. [Törlés](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery) Vcenter kiszolgáló vagy vSphere minden gazdagép tartozó és a konfigurációs kiszolgáló.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Törölje a konfigurációs kiszolgáló Azure-portálon
1. Azure-portálon keresse meg a **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók** a tároló menüből.
2. Kattintson a kiszolgáló leszerelése kívánt.
3. A konfigurációs kiszolgáló adatai lapon kattintson a Törlés gomb.

  ![törlés-konfiguráció-kiszolgáló](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.PNG)
4. Kattintson a **Igen** annak a kiszolgálónak a törlés megerősítéséhez.

### <a name="uninstall-the-configuration-server-software-and-its-dependencies"></a>Távolítsa el a konfigurációs kiszolgáló szoftver és annak függőségeit
  > [!TIP]
  Ha szeretne újra felhasználhatja a konfigurációs kiszolgáló Azure Site Recovery szolgáltatással, majd továbbléphet a 4. lépés közvetlenül

1. Jelentkezzen be rendszergazdaként a konfigurációs kiszolgáló.
2. Nyissa meg Vezérlőpult > Program > Programok eltávolítása
3. Távolítsa el a programokat, az alábbi sorrendben:
  * Microsoft Azure Recovery Services Agent
  * Microsoft Azure Site helyreállítási mobilitási szolgáltatás/fő célkiszolgáló megadásával
  * A Microsoft Azure Site Recovery Providert
  * Microsoft Azure Recovery konfigurációs kiszolgáló/folyamat helykiszolgáló
  * A Microsoft Azure Site helyreállítási konfigurációs kiszolgáló függőségek
  * MySQL Server 5.5
4. Futtassa a következő parancsot a és a rendszergazdai parancssorba.
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Törölje vagy szüntesse meg a konfigurációs kiszolgáló (PowerShell)

1. [Telepítés](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) Azure PowerShell modul
2. Bejelentkezés az Azure-fiókjába a paranccsal a
    
    `Login-AzureRmAccount`
3. Válassza ki az előfizetést, amely alatt a tárolóban jelen

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Most már a tároló környezet beállítása
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Válassza ki a konfigurációs kiszolgáló beolvasása

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. A konfigurációs kiszolgáló törlése

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> A **-Force** a Remove-AzureRmSiteRecoveryFabric beállítás segítségével kényszerítheti a konfigurációs kiszolgáló eltávolítása vagy törlése.

## <a name="renew-configuration-server-secure-socket-layerssl-certificates"></a>Konfigurációs kiszolgáló biztonságos szoftvercsatorna Layer(SSL) tanúsítványainak megújítása
A kiszolgáló rendelkezik egy beépített webkiszolgálót, amely a mobilitási szolgáltatás, a folyamat kiszolgálók és a fő célkiszolgáló kiszolgálók, a konfigurációs kiszolgálóhoz kapcsolódó tevékenységeket koordinálja. A konfigurációs kiszolgáló webkiszolgáló SSL-tanúsítványt használ az ügyfelek hitelesítéséhez. Ez a tanúsítvány rendelkezik egy három év lejárta, és az alábbi módszerrel bármikor újítható:

> [!WARNING]
Tanúsítványlejárat csak verziót lehetne végrehajtani 9.4.XXXX. X vagy újabb verzióját. Az Azure Site Recovery minden összetevőjét (konfigurációs kiszolgáló, a Folyamatkiszolgáló, fő célkiszolgáló, mobilitási szolgáltatás) frissítése a tanúsítványok megújítása munkafolyamat megkezdése előtt.

1. Az Azure-portálon keresse meg a tároló > Site Recovery-infrastruktúra > konfigurációs kiszolgáló.
2. Kattintson a konfigurációs kiszolgáló, amelyhez újítsa meg az SSL-tanúsítványt kell.
3. A konfigurációs kiszolgáló állapota tekintse meg a lejárat dátumát az SSL-tanúsítvány.
4. A tanúsítvány megújításához kattintson a **tanúsítványok megújítása** művelet a következő ábrán látható módon:

  ![törlés-konfiguráció-kiszolgáló](./media/site-recovery-vmware-to-azure-manage-configuration-server/renew-cert-page.png)

### <a name="secure-socket-layer-certificate-expiry-warning"></a>Secure Socket Layer tanúsítvány lejáratával kapcsolatos figyelmeztetés

> [!NOTE]
Az összes telepítés előtt 2016. május történt az SSL-tanúsítvány érvényességi egy évre lett beállítva. már elindította az Azure-portálon jelenik meg a tanúsítványok lejárati értesítéseinek jelent.

1. Ha a konfigurációs kiszolgáló SSL-tanúsítvány a következő két hónapon belül lejár, a szolgáltatás elindul, értesíti a felhasználókat az Azure-portálon & e-mailek (kell lehet feliratkozni az Azure Site Recovery értesítésekre). A tároló-erőforrás oldalon értesítésszalagról kezdenek el azt.

  ![tanúsítvány-értesítés](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-renew-notification.png)
2. Kattintson a szalagcím, és kérjen további részleteket a tanúsítvány lejárta.

  ![tanúsítvány-részletek](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-expiry-details.png)

  >[!TIP]
  Ahelyett, hogy ha egy **megújítása most** gomb megjelenik egy **frissítés most** gombra. A frissítés most gombra azt jelzi, hogy vannak-e néhány összetevőt a környezetben, amelyek még nincsenek frissítve az 9.4.xxxx.x vagy újabb verzió.

## <a name="revive-a-configuration-server-if-the-secure-socket-layer-ssl-certificate-expired"></a>A konfigurációs kiszolgáló Reaktiválja, ha a Secure Socket Layer (SSL) tanúsítvány lejárt

1. A konfigurációs kiszolgálón, hogy frissítse a [legújabb verziója](http://aka.ms/unifiedinstaller)
2. Ha kibővített folyamat kiszolgálókat, feladat-visszavétel a fő célkiszolgálón kiszolgálók, a feladatátvételi folyamat kiszolgálók frissítse azokat a legújabb verzióra
3. Frissítés a mobilitási szolgáltatást a védett virtuális gépet a legújabb verzióra.
4. Jelentkezzen be a konfigurációs kiszolgáló, és nyisson meg egy parancssort rendszergazdai jogosultságokkal.
5. Keresse meg a mappa %ProgramData%\ASR\home\svsystems\bin
6. Futtassa a konfigurációs kiszolgálón az SSL-tanúsítvány megújítása RenewCerts.exe.
7. Ha a folyamat sikeres, megjelenik a üzenet "tanúsítvány megújítási időtartama sikeres"


## <a name="sizing-requirements-for-a-configuration-server"></a>A konfigurációs kiszolgáló méretezése követelményei

| **PROCESSZOR** | **Memória** | **Gyorsítótár-lemez mérete** | **Adatváltozási sebesség** | **Védett gépek** |
| --- | --- | --- | --- | --- |
| 8 Vcpu (2 sockets * @ 2,5 GHz, 4 mag) |16 GB |300 GB |500 GB vagy kevesebb |100-nál kevesebb gépek replikálása. |
| 12 Vcpu (2 sockets * @ 2,5 GHz-es 6 mag) |18 GB |600 GB |1 TB 500 GB |100-150 gépek közti replikálásához. |
| 16 Vcpu (2 sockets * @ 2,5 GHz-es 8 mag) |32 GB |1 TB |1 TB-os és 2 TB |150-200 gépek közti replikálásához. |

  >[!TIP]
  Ha a napi adatforgalommal meghaladja a 2 TB, vagy több mint 200 olyan virtuális gépet replikálni tervezi, javasoljuk, hogy a replikációs forgalom további folyamat-kiszolgálót telepíthet. Ismerje meg, további kibővített folyamat telepítésével kapcsolatos kiszolgálója.


## <a name="common-issues"></a>Gyakori problémák
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]
