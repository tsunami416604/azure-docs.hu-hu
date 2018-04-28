---
title: " A konfigurációs kiszolgáló, az Azure Site Recovery vész-helyreállítási fizikai kiszolgáló kezelése |} Microsoft Docs"
description: A cikkből megtudhatja, hogyan felügyelheti a fizikai kiszolgáló vész-helyreállítási az Azure-ba, az Azure Site Recovery szolgáltatásban meglévő konfigurációs kiszolgáló.
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 04/11/2018
ms.author: anoopkv
ms.openlocfilehash: 580d32a51f6b38916ddccd46784b80b1179c29c4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>A fizikai kiszolgáló vész-helyreállítási kiszolgáló kezelése

Állít be egy helyszíni konfigurációs kiszolgáló használatakor az [Azure Site Recovery](site-recovery-overview.md) fizikai kiszolgálók Azure vész-helyreállítási szolgáltatás. A konfigurációs kiszolgáló koordinálja a helyszíni gépeket és az Azure közötti kommunikáció és kezeli a replikációs adatokat. Ez a cikk a gyakori feladatok a konfigurációs kiszolgáló kezeléséhez, miután üzembe helyezésüket követően foglalja össze.

## <a name="prerequisites"></a>Előfeltételek

A táblázat összefoglalja a prerequistes üzembe helyezéséhez a konfigurációs kiszolgáló a helyi számítógépen.

| **Összetevő** | **Követelmény** |
| --- |---|
| Processzormagok| 8 |
| RAM | 16 GB|
| Lemezek száma | 3., beleértve az operációs rendszer lemez, a folyamat kiszolgáló gyorsítótár lemez és a feladat-visszavételi adatmegőrzési meghajtó |
| – Lemez szabad területe (Folyamatkiszolgálói gyorsítótár) | 600 GB
| Lemez szabad területe (adatmegőrzési lemez) | 600 GB|
| Operációs rendszer  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Operációs rendszer területi beállítása | Angol (Egyesült Államok)|
| VMware vSphere PowerCLI verziója | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server-szerepkörök | Ezek a szerepkörök nem engedélyezi: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V |
| Csoportházirendek| A csoportházirendek nem engedélyezi: <br> -Hozzáférés megakadályozása a parancssorból <br> -Hozzáférés megakadályozása a beállításjegyzék szerkesztésével eszközök <br> -Megbízhatósági fájlmellékletek logika <br> -Kapcsolja be a parancsfájl végrehajtása <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | -Nincs már meglévő alapértelmezett webhely <br> -Engedélyezése [névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás  <br> -Egyetlen már meglévő webhely vagy alkalmazás figyeli a 443-as port<br>|
| A hálózati adapter típusa | VMXNET3 (ha VMware virtuális gépként telepített) |
| IP-cím típusa | Statikus |
| Internetelérés | A kiszolgáló URL-hozzáférésre van szüksége: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - dc.services.visualstudio.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (kibővített folyamat kiszolgálók nem kötelező) <br> - time.nist.gov <br> - time.windows.com |
| Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel)|

## <a name="download-the-latest-installation-file"></a>A legfrissebb telepítőfájljának letöltése

A konfigurációs kiszolgáló telepítési fájl legújabb verzióját a következő helyen érhető el a Site Recovery portálon. Emellett az letölthetők közvetlenül a [Microsoft Download Center](http://aka.ms/unifiedsetup).

1. Jelentkezzen be az Azure-portálon, és keresse meg a Recovery Services-tároló.
2. Keresse meg a **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók** (a VMware és fizikai gépek).
3. Kattintson a **+ kiszolgálók** gombra.
4. Az a **kiszolgáló hozzáadása** lapon, a Letöltés gombra kattintva töltse le a regisztrációs kulcsot. A konfigurációs kiszolgáló telepítése az Azure Site Recovery szolgáltatásban való regisztrálása során kell ezt a kulcsot.
5. Kattintson a **töltse le a Microsoft Azure Site Recovery az egységes telepítő** hivatkozást a konfigurációs kiszolgáló legújabb verziójának letöltéséhez.

  ![Letöltési oldala](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Telepítse és regisztrálja a kiszolgálót

1. Futtassa az egyesített telepítő fájlját.
2. A **előkészületek**, jelölje be **a konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése**.

    ![Előkészületek](./media/physical-manage-configuration-server/combined-wiz1.png)

3. A **Külső szoftver licence** területen kattintson az **Elfogadom** elemre a MySQL letöltéséhez és telepítéséhez.
4. Az **Internetbeállítások** területen adja meg, hogy a konfigurációs kiszolgálón futó Provider hogyan csatlakozzon az Azure Site Recoveryhez az interneten keresztül. Győződjön meg arról, hogy engedélyezte a szükséges URL-címek.

    - Ha szeretné csatlakoztatni a jelenleg be van állítva a számítógépen, válassza a proxy **csatlakozás az Azure Site Recovery proxykiszolgálóval**.
    - Ha azt szeretné, hogy a Provider közvetlenül kapcsolódjon, válassza ki a **Csatlakozás közvetlenül az Azure Site Recovery proxykiszolgáló nélkül**.
    - Ha a meglévő proxy hitelesítést igényel, vagy ha egyéni proxyt a szolgáltatói kapcsolat, válassza ki a használni kívánt **kapcsolódás egyéni proxybeállításokkal**, és adja meg a címet, a port és a hitelesítő adatokat.
     ![Tűzfal](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Az **Előfeltételek ellenőrzése** területen a telepítő ellenőrzi, hogy a telepítés végrehajtható-e. Ha megjelenik egy figyelmeztetés a **globális időszinkron ellenőrzéséről**, ellenőrizze, hogy a rendszeróra ideje (a **Dátum és idő** beállítások) megegyeznek-e az időzónával.

    ![Előfeltételek](./media/physical-manage-configuration-server/combined-wiz5.png)
7. A **MySQL-konfiguráció** területen hozza létre a telepített MySQL-kiszolgálópéldányra való bejelentkezéshez szükséges hitelesítő adatokat.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. A **Környezet részletei** területen válassza ki, hogy kívánja-e replikálni a VMware virtuális gépeket. Ha, a telepítő ellenőrzi, hogy telepítve van-e a PowerCLI 6.0.
9. A **Telepítés helye** területen válassza ki, hová szeretné telepíteni a bináris fájlokat, és hol kívánja tárolni a gyorsítótárat. A kiválasztott meghajtón legalább 5 GB szabad lemezterületre van szükség, de javasoljuk, hogy a gyorsítótárazáshoz használt lemezen legyen legalább 600 GB szabad hely.

    ![Telepítés helye](./media/physical-manage-configuration-server/combined-wiz8.png)
10. A **Hálózat kiválasztása** területen adja meg a figyelőt (hálózati adaptert és SSL-portot), amelyen keresztül a konfigurációs kiszolgáló küldi és fogadja a replikált adatokat. A 9443-as port a replikációs forgalom küldésére és fogadására használt alapértelmezett port, ez azonban a környezeti követelményektől függően módosítható. A 9443-as port mellett a 443-as portot is megnyitjuk, amelyen keresztül egy webkiszolgáló a replikálási műveleteket vezényli. Ne használja a 443-as porton és-replikációs forgalom fogadására.

    ![Hálózat kiválasztása](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Az **Összefoglalás** területen ellenőrizze az adatokat, majd kattintson a **Telepítés** gombra. A telepítés után a rendszer létrehoz egy hozzáférési kódot. Erre szüksége lesz, amikor engedélyezi a replikálást, ezért másolja le, és tárolja biztonságos helyen.


A regisztráció befejezését követően a kiszolgáló megjelenik a tároló **Beállítások** > **Kiszolgálók** paneljén.


## <a name="install-from-the-command-line"></a>Telepítse a parancssorból

Futtassa a telepítőfájlt a következőképpen:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Példa
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Paraméterek

|Paraméter neve| Típus | Leírás| Értékek|
|-|-|-|-|
| /ServerMode|Szükséges|Megadja, hogy a konfigurációs és folyamatkiszolgálót is, vagy csak a folyamatkiszolgálót kell-e telepíteni.|CS<br>PS|
|/InstallLocation|Szükséges|Az összetevők telepítési mappája| A számítógép bármely mappája|
|/MySQLCredsFilePath|Szükséges|A fájl elérési útja, amelyen a MySQL-kiszolgáló hitelesítő adatai tárolva vannak|A fájlnak az alább megadott formátumúnak kell lennie|
|/VaultCredsFilePath|Szükséges|A tároló hitelesítőadat-fájljának elérési útja|Érvényes fájlelérési út|
|/EnvType|Szükséges|A védeni kívánt környezet típusa |VMware<br>NonVMware|
|/PSIP|Szükséges|A replikációs adatátvitelhez használni kívánt hálózati adapter IP-címe| Bármilyen érvényes IP-cím|
|/CSIP|Szükséges|Annak a hálózati adapternek az IP-címe, amelyen a konfigurációs kiszolgáló figyel| Bármilyen érvényes IP-cím|
|/PassphraseFilePath|Szükséges|A jelszófájl teljes elérési útja|Érvényes fájlelérési út|
|/BypassProxy|Optional|Megadja, hogy a konfigurációs kiszolgáló proxy nélkül csatlakozik az Azure-hoz.|Az érték beszerzése innen: Venu|
|/ProxySettingsFilePath|Optional|Proxybeállítások (Az alapértelmezett proxyhoz hitelesítés vagy egyéni proxy szükséges).|A fájlnak az alább megadott formátumúnak kell lennie|
|DataTransferSecurePort|Optional|Az adatreplikációhoz használni kívánt PSIP-port száma| Érvényes portszám (az alapértelmezett érték 9433)|
|/SkipSpaceCheck|Optional|Gyorsítótárlemez terület-ellenőrzésének kihagyása| |
|/AcceptThirdpartyEULA|Szükséges|Ez a jelölő a külső féltől származó végfelhasználói licencszerződés elfogadását jelzi| |
|/ShowThirdpartyEULA|Optional|Külső felektől származó végfelhasználói licenszszerződés megjelenítése. Bemenetként való megadása esetén figyelmen kívül hagyja a többi paramétert.| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>A megadott fájl MYSQLCredsFilePath létrehozása

A MySQLCredsFilePath paraméter egy fájl fogadja bemeneti adatként. Hozza létre a fájlt a következő formátumban, és adja át bemeneti MySQLCredsFilePath paraméterként.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>A megadott fájl ProxySettingsFilePath létrehozása
ProxySettingsFilePath paraméter egy fájl fogadja bemeneti adatként. Hozza létre a fájlt a következő formátumban, és adja át bemeneti ProxySettingsFilePath paraméterként.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Proxy beállításainak módosítása

A konfigurációs kiszolgáló gép proxy beállításait az alábbiak szerint módosíthatja:

1. Jelentkezzen be a konfigurációs kiszolgáló.
2. Indítsa el a használatával a helyi cspsconfigtool.exe a.
3. Kattintson a **tároló regisztrációs** fülre.
4. Töltsön le új tárolói regisztrációs fájlt a portálról, és adja meg az eszköz számára.

  ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Adja meg az új proxy adatait, és kattintson a **regisztrálása** gombra.
6. Nyisson meg egy rendszergazda PowerShell parancsablakot.
7. Futtassa az alábbi parancsot:
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Ha a konfigurációs kiszolgálóhoz kapcsolódó további folyamat kiszolgálóval rendelkezik, akkor [hárítsa el a proxykiszolgáló beállításait a kibővített folyamat összes kiszolgálójára](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) a környezetben.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Regisztrálja újra a konfigurációs kiszolgáló ugyanabban a tárolóban
  1. Jelentkezzen be a konfigurációs kiszolgáló.
  2. Indítsa el a cspsconfigtool.exe használatával a parancsikont az asztalon.
  3. Kattintson a **tároló regisztrációs** fülre.
  4. Töltsön le új regisztrációs fájlt a portálról, és adja meg az eszköz számára.
        ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
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
  Ha több folyamat kiszolgálóval rendelkezik, akkor [regisztrálja azokat](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>A konfigurációs kiszolgáló regisztrálása egy másik tárolóban.

> [!WARNING]
> A következő lépés megszünteti a konfigurációs kiszolgáló, az aktuális tárolóból, és a replikáció az összes védett virtuális gépet, a konfigurációs kiszolgáló leállt.

1. Jelentkezzen be a konfigurációs kiszolgálón
2. egy rendszergazdai parancssorból futtassa a parancsot:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Indítsa el a cspsconfigtool.exe használatával a parancsikont az asztalon.
4. Kattintson a **tároló regisztrációs** fülre.
5. Töltsön le új regisztrációs fájlt a portálról, és adja meg az eszköz számára.
6. Adja meg a proxykiszolgáló adatait, és kattintson a **regisztrálása** gombra.  
7. Nyisson meg egy rendszergazda PowerShell parancsablakot.
8. A következő parancsot
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Konfigurációs kiszolgáló verziófrissítése

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

> [!WARNING]
> Ellenőrizze a konfigurációs kiszolgáló leszerelése megkezdése előtt.
> 1. [Tiltsa le a védelmet](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) az összes virtuális gép ezen a konfigurációs kiszolgálón.
> 2. [Szüntesse meg](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) és [törlése](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) összes replikációs házirendet a konfigurációs kiszolgálóról.
> 3. [Törlés](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) Vcenter kiszolgáló vagy vSphere minden gazdagép tartozó és a konfigurációs kiszolgáló.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Törölje a konfigurációs kiszolgáló Azure-portálon
1. Azure-portálon keresse meg a **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók** a tároló menüből.
2. Kattintson a kiszolgáló leszerelése kívánt.
3. A konfigurációs kiszolgáló Részletek lapján kattintson a **törlése** gombra.
4. Kattintson a **Igen** annak a kiszolgálónak a törlés megerősítéséhez.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Távolítsa el a konfigurációs kiszolgáló és a függőségek
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
    
    `Connect-AzureRmAccount`
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




## <a name="common-issues"></a>Gyakori problémák
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>További lépések

Tekintse át a vész-helyreállítási beállításával kapcsolatos oktatóanyagok [fizikai kiszolgálók](tutorial-physical-to-azure.md) az Azure-bA.

