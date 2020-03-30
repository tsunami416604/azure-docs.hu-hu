---
title: Fizikai kiszolgálók konfigurációs kiszolgálójának kezelése az Azure Site Recovery szolgáltatásban
description: Ez a cikk ismerteti, hogyan kezelheti az Azure Site Recovery konfigurációs kiszolgáló fizikai kiszolgáló vész-helyreállítási azure-ba.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: 25be48e9caed446be3a86a11143ce3040808065a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294298"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>A fizikai kiszolgáló vész-helyreállítási konfigurációs kiszolgálójának kezelése

A helyszíni konfigurációs kiszolgálóbeállítása, amikor az [Azure Site Recovery](site-recovery-overview.md) szolgáltatást használja a fizikai kiszolgálók Azure-ba történő vész-helyreállításához. A konfigurációs kiszolgáló koordinálja a helyszíni gépek és az Azure közötti kommunikációt, és kezeli az adatreplikációt. Ez a cikk összefoglalja a konfigurációs kiszolgáló üzembe helyezését követő en végzett gyakori feladatokat.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

A tábla összefoglalja a helyszíni konfigurációs kiszolgálószámítógép üzembe helyezésének előfeltételeit.

| **Összetevő** | **Követelmény** |
| --- |---|
| Processzormagok| 8 |
| RAM | 16 GB|
| Lemezek száma | 3, beleértve az operációs rendszer lemezét, a kiszolgáló gyorsítótárának feldolgozását és a feladat-visszavétel megőrzési meghajtóját |
| – Lemez szabad területe (Folyamatkiszolgálói gyorsítótár) | 600 GB
| Lemez szabad területe (adatmegőrzési lemez) | 600 GB|
| Operációs rendszer  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Operációs rendszer területi beállítása | Angol (US)|
| VMware vSphere PowerCLI verziója | Nem kötelező|
| Windows Server-szerepkörök | Ne engedélyezze ezeket a szerepköröket: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V |
| Csoportházirendek| Ne engedélyezze ezeket a csoportházirendeket: <br> - Hozzáférés megakadályozása a parancssorba <br> - Hozzáférés megakadályozása a rendszerleíró adatbázis szerkesztő eszközök <br> - Megbízhatósági logika fájlmellékletek <br> - Kapcsolja be a Script végrehajtás <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | - Nincs már létező alapértelmezett webhely <br> - Névtelen [hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) engedélyezése <br> - Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás  <br> - Nincs már létező weboldal / alkalmazás hallgatása a 443-as porton<br>|
| Hálózati adapter típusa | VMXNET3 (VMware vm-ként telepítve) |
| IP-cím típusa | Statikus |
| Internet-hozzáférés | A kiszolgálónak hozzá kell férnie ezekhez az URL-címekhez: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi(nem szükséges a kibővített folyamatkiszolgálók esetében) <br> - time.nist.gov <br> - time.windows.com |
| Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel)|

## <a name="download-the-latest-installation-file"></a>A legújabb telepítési fájl letöltése

A konfigurációs kiszolgáló telepítési fájljának legújabb verziója elérhető a Webhely-helyreállítási portálon. Ezenkívül közvetlenül a Microsoft [letöltőközpontból](https://aka.ms/unifiedsetup)is letölthető.

1. Jelentkezzen be az Azure Portalra, és keresse meg a Recovery Services-tallózóját.
2. Tallózással keresse meg **a Hely-helyreállítási infrastruktúra** > **konfigurációs kiszolgálóit** (a VMware & fizikai gépek esetén) területen.
3. Kattintson a **+Servers** gombra.
4. A **Kiszolgáló hozzáadása** lapon kattintson a Letöltés gombra a Regisztrációs kulcs letöltéséhez. Erre a kulcsra a konfigurációs kiszolgáló telepítése során regisztrálnia kell az Azure Site Recovery szolgáltatással.
5. Kattintson **a Microsoft Azure Site Recovery egyesített telepítési** hivatkozásra a konfigurációs kiszolgáló legújabb verziójának letöltéséhez.

   ![Oldal letöltése](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>A kiszolgáló telepítése és regisztrálása

1. Futtassa az egyesített telepítő fájlját.
2. A **Kezdés előtt**jelölje be a **Konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése**lehetőséget.

    ![Előkészületek](./media/physical-manage-configuration-server/combined-wiz1.png)

3. A **Külső szoftver licence** területen kattintson az **Elfogadom** elemre a MySQL letöltéséhez és telepítéséhez.
4. Az **Internetbeállítások** területen adja meg, hogy a konfigurációs kiszolgálón futó Provider hogyan csatlakozzon az Azure Site Recoveryhez az interneten keresztül. Győződjön meg arról, hogy engedélyezte a szükséges URL-eket.

    - Ha csatlakozni szeretne a számítógépen jelenleg beállított proxyhoz, válassza a Csatlakozás az **Azure Site Recovery szolgáltatáshoz proxykiszolgálóhasználatával lehetőséget.**
    - Ha azt szeretné, hogy a szolgáltató közvetlenül csatlakozzon, válassza **a Csatlakozás közvetlenül az Azure Site Recovery szolgáltatáshoz proxykiszolgáló nélkül**lehetőséget.
    - Ha a meglévő proxy hitelesítést igényel, vagy ha egyéni proxyt szeretne használni a szolgáltatói kapcsolathoz, válassza a **Csatlakozás egyéni proxybeállításokkal**lehetőséget, és adja meg a címet, a portot és a hitelesítő adatokat.
     ![Tűzfal](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Az **Előfeltételek ellenőrzése** területen a telepítő ellenőrzi, hogy a telepítés végrehajtható-e. Ha megjelenik egy figyelmeztetés a **globális időszinkron ellenőrzéséről**, ellenőrizze, hogy a rendszeróra ideje (a **Dátum és idő** beállítások) megegyeznek-e az időzónával.

    ![Előfeltételek](./media/physical-manage-configuration-server/combined-wiz5.png)
7. A **MySQL-konfiguráció** területen hozza létre a telepített MySQL-kiszolgálópéldányra való bejelentkezéshez szükséges hitelesítő adatokat.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. A **Környezet részletei** területen válassza ki, hogy kívánja-e replikálni a VMware virtuális gépeket. Ha így van, akkor a telepítő ellenőrzi, hogy a PowerCLI 6.0 telepítve van-e.
9. A **Telepítés helye** területen válassza ki, hová szeretné telepíteni a bináris fájlokat, és hol kívánja tárolni a gyorsítótárat. A kiválasztott meghajtón legalább 5 GB szabad lemezterületre van szükség, de javasoljuk, hogy a gyorsítótárazáshoz használt lemezen legyen legalább 600 GB szabad hely.

    ![Telepítés helye](./media/physical-manage-configuration-server/combined-wiz8.png)
10. A **Hálózatkiválasztása területen**válassza ki a hálózati adaptert, amelyet a beépített folyamatkiszolgáló használ a mobilitási szolgáltatás felderítéséhez és leküldéses telepítéséhez a forrásgépeken, majd válassza ki azt a hálózati adaptert, amelyet a Configuration Server használ az Azure-ral való kapcsolathoz. A 9443-as port a replikációs forgalom küldésére és fogadására használt alapértelmezett port, ez azonban a környezeti követelményektől függően módosítható. A 9443-as port mellett a 443-as portot is megnyitjuk, amelyen keresztül egy webkiszolgáló a replikálási műveleteket vezényli. Ne használja a 443-as portot replikációs forgalom küldésére vagy fogadására.

    ![Hálózat kiválasztása](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Az **Összefoglalás** területen ellenőrizze az adatokat, majd kattintson a **Telepítés** gombra. A telepítés után a rendszer létrehoz egy hozzáférési kódot. Erre szüksége lesz, amikor engedélyezi a replikálást, ezért másolja le, és tárolja biztonságos helyen.


A regisztráció befejezése után a kiszolgáló megjelenik a **beállítások** > **kiszolgálói** panelen a tárolóban.


## <a name="install-from-the-command-line"></a>Telepítés a parancssorból

Futtassa a telepítési fájlt az alábbiak szerint:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Mintahasználat
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Paraméterek

|Paraméter neve| Típus | Leírás| Értékek|
|-|-|-|-|
| /ServerMode|Kötelező|Megadja, hogy a konfigurációs és folyamatkiszolgálót is, vagy csak a folyamatkiszolgálót kell-e telepíteni.|CS<br>PS|
|/InstallLocation|Kötelező|Az összetevők telepítési mappája| A számítógép bármely mappája|
|/MySQLCredsFilePath|Kötelező|A fájl elérési útja, amelyen a MySQL-kiszolgáló hitelesítő adatai tárolva vannak|A fájlnak az alább megadott formátumúnak kell lennie|
|/VaultCredsFilePath|Kötelező|A tároló hitelesítőadat-fájljának elérési útja|Érvényes fájlelérési út|
|/EnvType|Kötelező|A védeni kívánt környezet típusa |VMware<br>NonVMware|
|/PSIP|Kötelező|A replikációs adatátvitelhez használni kívánt hálózati adapter IP-címe| Bármilyen érvényes IP-cím|
|/CSIP|Kötelező|Annak a hálózati adapternek az IP-címe, amelyen a konfigurációs kiszolgáló figyel| Bármilyen érvényes IP-cím|
|/PassphraseFilePath|Kötelező|A jelszófájl teljes elérési útja|Érvényes fájlelérési út|
|/BypassProxy|Optional|Megadja, hogy a konfigurációs kiszolgáló proxy nélkül csatlakozik az Azure-hoz.|Az érték beszerzése innen: Venu|
|/ProxySettingsFilePath|Optional|Proxybeállítások (Az alapértelmezett proxyhoz hitelesítés vagy egyéni proxy szükséges).|A fájlnak az alább megadott formátumúnak kell lennie|
|DataTransferSecurePort|Optional|Az adatreplikációhoz használni kívánt PSIP-port száma| Érvényes portszám (az alapértelmezett érték 9433)|
|/SkipSpaceCheck|Optional|Gyorsítótárlemez terület-ellenőrzésének kihagyása| |
|/AcceptThirdpartyEULA|Kötelező|Ez a jelölő a külső féltől származó végfelhasználói licencszerződés elfogadását jelzi| |
|/ShowThirdpartyEULA|Optional|Külső felektől származó végfelhasználói licenszszerződés megjelenítése. Bemenetként való megadása esetén figyelmen kívül hagyja a többi paramétert.| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Fájlbevitel létrehozása a MYSQLCredsFilePath számára

A MySQLCredsFilePath paraméter egy fájlt vesz fel bemenetként. Hozza létre a fájlt a következő formátumban, és adja át bemeneti MySQLCredsFilePath paraméterként.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Fájlbemenet létrehozása a ProxySettingsFilePath alkalmazáshoz
A ProxySettingsFilePath paraméter egy fájlt vesz fel bemenetként. Hozza létre a fájlt a következő formátumban, és adja át bemeneti ProxySettingsFilePath paraméterként.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Proxybeállítások módosítása

A konfigurációs kiszolgáló számítógépének proxybeállításai a következőképpen módosulhatnak:

1. Jelentkezzen be a konfigurációs kiszolgálóra.
2. Indítsa el a cspsconfigtool.exe programot az asztalon található parancsikon segítségével.
3. Kattintson a **Tároló regisztráció fülére.**
4. Töltse le az új trezor regisztrációs fájlt a portálról, és adja meg az eszköz bemeneteként.

   ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Adja meg az új proxyrészleteket, és kattintson a **Regisztráció** gombra.
6. Nyisson meg egy RendszergazdaI PowerShell parancsablakot.
7. Futtassa az alábbi parancsot:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Ha további folyamatkiszolgálók vannak csatlakoztatva a konfigurációs kiszolgálóhoz, a központi telepítés [összes kibővített folyamatkiszolgálóján ki kell javítania a proxybeállításokat.](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server)

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Konfigurációs kiszolgáló újraregisztrálása ugyanazzal a tárolóval
1. Jelentkezzen be a konfigurációs kiszolgálóra.
2. Indítsa el a cspsconfigtool.exe programot az asztalon található parancsikon segítségével.
3. Kattintson a **Tároló regisztráció fülére.**
4. Töltsön le egy új regisztrációs fájlt a portálról, és adja meg az eszköz bemeneteként.
      ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Adja meg a Proxykiszolgáló adatait, és kattintson a **Regisztráció** gombra.  
6. Nyisson meg egy RendszergazdaI PowerShell parancsablakot.
7. Futtassa a következő parancsot:

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Ha több folyamatkiszolgálóval rendelkezik, újra kell [regisztrálnia őket.](vmware-azure-manage-process-server.md#reregister-a-process-server)

## <a name="register-a-configuration-server-with-a-different-vault"></a>Konfigurációs kiszolgáló regisztrálása másik tárolóval

> [!WARNING]
> A következő lépés elválasztja a konfigurációs kiszolgálót az aktuális tárolótól, és a konfigurációs kiszolgáló alatt leállított összes védett virtuális gép replikációja leáll.

1. Bejelentkezés a konfigurációs kiszolgálóra
2. egy rendszergazdai parancssorból futtassa a parancsot:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Indítsa el a cspsconfigtool.exe programot az asztalon található parancsikon segítségével.
4. Kattintson a **Tároló regisztráció fülére.**
5. Töltsön le egy új regisztrációs fájlt a portálról, és adja meg az eszköz bemeneteként.
6. Adja meg a Proxykiszolgáló adatait, és kattintson a **Regisztráció** gombra.  
7. Nyisson meg egy RendszergazdaI PowerShell parancsablakot.
8. Futtassa a következő parancsot:
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Konfigurációs kiszolgáló frissítése

A konfigurációs kiszolgáló frissítéséhez összegző frissítők futtatásával. A frissítések legfeljebb N-4 verziókhoz jelentkezhetnek. Példa:

- Ha 9.7, 9.8, 9.9 vagy 9.10 -- közvetlenül frissíthet 9.11-re.
- Ha 9.6-os vagy korábbi verziót futtat, és 9.11-re szeretne frissíteni, először frissítenie kell a 9.7-es verzióra. 9.11.

A konfigurációs kiszolgáló összes verziójára való frissítésre mutató frissítő frissítőlapokra mutató hivatkozások a [wikifrissítések lapon](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)érhetők el.

Frissítse a kiszolgálót az alábbiak szerint:

1. Töltse le a frissítéstelepítő fájlját a konfigurációs kiszolgálóra.
2. A telepítő futtatásához kattintson duplán.
3. A telepítő észleli a számítógépen futó aktuális verziót.
4. Kattintson **az OK** gombra a megerősítéshez és a frissítés futtatásához. 


## <a name="delete-or-unregister-a-configuration-server"></a>Konfigurációs kiszolgáló törlése vagy regisztrációjának megszüntetése

> [!WARNING]
> A konfigurációs kiszolgáló leszerelésének megkezdése előtt győződjön meg a következőkről.
> 1. [Tiltsa le](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) a védelmet a konfigurációs kiszolgáló alatt található összes virtuális gép számára.
> 2. Az összes replikációs házirend [társításának](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) és [törlésének leválasztani](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) és törölni a konfigurációs kiszolgálótól.
> 3. [Törölje](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) a konfigurációs kiszolgálóhoz társított összes vCenters-kiszolgálót/vSphere-állomást.


### <a name="delete-the-configuration-server-from-azure-portal"></a>A konfigurációs kiszolgáló törlése az Azure Portalról
1. Az Azure Portalon keresse meg a Vault menü **webhely-helyreállítási infrastruktúra** > **konfigurációs kiszolgálói** között.
2. Kattintson a leszerelni kívánt konfigurációs kiszolgálóra.
3. A Konfigurációs kiszolgáló részletes lapján kattintson a **Törlés** gombra.
4. A kiszolgáló törlésének megerősítéséhez kattintson az **Igen** gombra.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>A konfigurációs kiszolgáló és függőségei eltávolítása
> [!TIP]
>   Ha azt tervezi, hogy újra felhasználja a konfigurációs kiszolgálót az Azure Site Recovery szolgáltatással, akkor közvetlenül a 4.

1. Jelentkezzen be rendszergazdaként a konfigurációs kiszolgálóra.
2. A Vezérlőpult > program > az Eltávolítási programok megnyitása
3. Távolítsa el a programokat a következő sorrendben:
   * Microsoft Azure helyreállítási szolgáltatások ügynöke
   * Microsoft Azure webhely-helyreállítási mobilitási szolgáltatás/fő célkiszolgáló
   * Microsoft Azure webhely-helyreállítási szolgáltató
   * Microsoft Azure webhely-helyreállítási konfigurációs kiszolgáló/folyamatkiszolgáló
   * Microsoft Azure webhely-helyreállítási konfigurációs kiszolgáló függőségei
   * MySQL Server 5.5
4. Futtassa a következő parancsot a rendszergazdai parancssorból.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Konfigurációs kiszolgáló törlése vagy regisztrációjának megszüntetése (PowerShell)

1. [Telepítés](https://docs.microsoft.com/powershell/azure/install-Az-ps) Azure PowerShell-modul
2. Bejelentkezés az Azure-fiókjába a paranccsal
    
    `Connect-AzAccount`
3. Válassza ki azt az előfizetést, amely alatt a tároló jelen van

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Most állítsa be a trezor környezetben
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. A konfigurációs kiszolgáló kiválasztása

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. A konfigurációs kiszolgáló törlése

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> Az Remove-AzSiteRecoveryFabric **-Force** kapcsolója a konfigurációs kiszolgáló eltávolításának/törlésének kényszerítésére használható.

## <a name="renew-ssl-certificates"></a>SSL-tanúsítványok megújítása
A konfigurációs kiszolgáló beépített webkiszolgálóval rendelkezik, amely a mobilitási szolgáltatás, a folyamatkiszolgálók és a hozzá csatlakoztatott fő célkiszolgálók tevékenységeit vezényli. A webkiszolgáló SSL-tanúsítványt használ az ügyfelek hitelesítéséhez. A tanúsítvány három év után lejár, és bármikor megújítható.

### <a name="check-expiry"></a>Lejárat ellenőrzése

A konfigurációs kiszolgáló 2016 májusa előtti telepítéseesetén a tanúsítvány lejárata egy évre volt állítva. Ha a tanúsítvány le fog járni, a következő történik:

- Ha a lejárati dátum két hónap vagy annál kevesebb, a szolgáltatás megkezdi az értesítések küldését a portálon, és e-mailben (ha előfizetett az Azure Site Recovery értesítésekre).
- Egy értesítési szalagcím jelenik meg a tároló erőforráslapján. További részletekért kattintson a szalagcímre.
- Ha megjelenik a **Frissítés most** gomb, az azt jelzi, hogy a környezetben vannak olyan összetevők, amelyek nem lettek frissítve a 9.4.xxxx.x vagy újabb verziókra. Frissítse az összetevőket a tanúsítvány megújítása előtt. Régebbi verziókon nem újítható meg.

### <a name="renew-the-certificate"></a>A tanúsítvány megújítása

1. A tárolóban nyissa meg a **Site Recovery Infrastructure** > **Configuration Server**kiszolgálót, és kattintson a szükséges konfigurációs kiszolgálóra.
2. A lejárati dátum a **Konfigurációs kiszolgáló állapota** alatt jelenik meg
3. Kattintson **a Tanúsítványok megújítása gombra.** 




## <a name="common-issues"></a>Gyakori problémák
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>További lépések

Tekintse át az azure-beli [fizikai kiszolgálók](tutorial-physical-to-azure.md) vészhelyreállításának beállítására szolgáló oktatóanyagokat.

