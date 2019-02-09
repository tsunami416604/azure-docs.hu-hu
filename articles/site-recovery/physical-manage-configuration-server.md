---
title: Az Azure-bA az Azure Site Recovery a helyszíni fizikai kiszolgálók vészhelyreállítása a konfigurációs kiszolgáló kezelése |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan kezelheti az Azure Site Recovery konfigurációs kiszolgálónak a fizikai kiszolgáló vészhelyreállítása az Azure-bA.
services: site-recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: d5ce80e44ee1a3a48443b190ea9259fe2dea0dcb
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55983219"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Fizikai kiszolgáló vészhelyreállításhoz használt konfigurációs kiszolgáló kezelése

Beállította egy helyszíni konfigurációs kiszolgálót használatakor a [Azure Site Recovery](site-recovery-overview.md) szolgáltatást, valamint fizikai kiszolgálók Azure-bA. A konfigurációs kiszolgáló koordinálja a helyszíni gépek és Azure közötti kommunikációt, és felügyeli az adatreplikációt. Ez a cikk a gyakori feladatok kezeléséhez a konfigurációs kiszolgáló üzembe helyezésüket követően foglalja össze.

## <a name="prerequisites"></a>Előfeltételek

A táblázat összefoglalja a helyszíni konfigurációs kiszolgáló gép telepítésének előfeltételeit.

| **Összetevő** | **Követelmény** |
| --- |---|
| Processzormagok| 8 |
| RAM | 16 GB|
| Lemezek száma | 3., beleértve az operációs rendszer lemez, a folyamatkiszolgálói gyorsítótárlemez és az adatmegőrzési meghajtó a feladat-visszavételhez |
| – Lemez szabad területe (Folyamatkiszolgálói gyorsítótár) | 600 GB
| Lemez szabad területe (adatmegőrzési lemez) | 600 GB|
| Operációs rendszer  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Operációs rendszer területi beállítása | Angol (Egyesült Államok)|
| VMware vSphere PowerCLI verziója | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server-szerepkörök | Ezek a szerepkörök nem engedélyezi: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V |
| Csoportházirendek| Ezek a szabályzatok csoport nem engedélyezi: <br> – Való hozzáférés letiltása a parancssor használatával <br> – Való hozzáférés letiltása a beállításjegyzék szerkesztőeszközeihez <br> -Megbízhatósági logika fájlmellékletekhez <br> – Kapcsolja be a parancsfájl végrehajtása <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | – Nincs már meglévő alapértelmezett webhelye <br> -Engedélyezése [a névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás  <br> – Nincs már meglévő webhely vagy alkalmazás 443-as porton<br>|
| Hálózati adapter típusa | VMXNET3 (Ha a VMware virtuális gépként telepített) |
| IP-cím típusa | Statikus |
| Internetelérés | A kiszolgálónak az URL-hozzáférésre van szüksége: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://management.azure.com <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (horizontális felskálázási Folyamatkiszolgáló nem szükséges) <br> - time.nist.gov <br> - time.windows.com |
| Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel)|

## <a name="download-the-latest-installation-file"></a>Töltse le a legújabb telepítési fájlját

A konfigurációs kiszolgáló telepítési fájl legújabb verzióját a Site Recovery portálon érhető el. Továbbá le is tölthetők közvetlenül a [Microsoft Download Center](https://aka.ms/unifiedsetup).

1. Jelentkezzen be az Azure Portalon, és keresse meg a Recovery Services-tárolóba.
2. Keresse meg a **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók** (a VMware és fizikai gépek számára).
3. Kattintson a **+ kiszolgálók** gombra.
4. Az a **-kiszolgáló hozzáadása** lapon, a Letöltés gombra kattintva töltse le a Tárregisztrációs kulcsot. Regisztrálja az Azure Site Recovery szolgáltatás a konfigurációs kiszolgáló telepítése során szüksége lesz rá.
5. Kattintson a **töltse le a Microsoft Azure Site Recovery egyesített telepítőjének** hivatkozást a konfigurációs kiszolgáló legújabb verziójának letöltéséhez.

  ![Letöltési oldala](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Telepítse és regisztrálja a kiszolgálót

1. Futtassa az egyesített telepítő fájlját.
2. A **alapismeretek**válassza **a konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése**.

    ![Előkészületek](./media/physical-manage-configuration-server/combined-wiz1.png)

3. A **Külső szoftver licence** területen kattintson az **Elfogadom** elemre a MySQL letöltéséhez és telepítéséhez.
4. Az **Internetbeállítások** területen adja meg, hogy a konfigurációs kiszolgálón futó Provider hogyan csatlakozzon az Azure Site Recoveryhez az interneten keresztül. Győződjön meg arról, hogy engedélyezte a szükséges URL-címek.

    - Ha szeretne csatlakozni, amely jelenleg be van állítva a számítógépen, válassza a proxy **csatlakozás az Azure Site Recoveryhez proxykiszolgálóval**.
    - Ha azt szeretné, hogy a szolgáltatót, hogy közvetlenül kapcsolódjon, válassza ki a **közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül**.
    - Ha a meglévő proxy hitelesítést igényel, vagy ha egyéni proxyt használ a Provider csatlakoztatásához, válassza ki a kívánt **csatlakozás egyéni proxybeállításokkal**, és adja meg a cím, port és hitelesítő adatokat.
     ![Tűzfal](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Az **Előfeltételek ellenőrzése** területen a telepítő ellenőrzi, hogy a telepítés végrehajtható-e. Ha megjelenik egy figyelmeztetés a **globális időszinkron ellenőrzéséről**, ellenőrizze, hogy a rendszeróra ideje (a **Dátum és idő** beállítások) megegyeznek-e az időzónával.

    ![Előfeltételek](./media/physical-manage-configuration-server/combined-wiz5.png)
7. A **MySQL-konfiguráció** területen hozza létre a telepített MySQL-kiszolgálópéldányra való bejelentkezéshez szükséges hitelesítő adatokat.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. A **Környezet részletei** területen válassza ki, hogy kívánja-e replikálni a VMware virtuális gépeket. Ha Ön, a telepítő ellenőrzi, hogy a PowerCLI 6.0 telepítve van-e.
9. A **Telepítés helye** területen válassza ki, hová szeretné telepíteni a bináris fájlokat, és hol kívánja tárolni a gyorsítótárat. A kiválasztott meghajtón legalább 5 GB szabad lemezterületre van szükség, de javasoljuk, hogy a gyorsítótárazáshoz használt lemezen legyen legalább 600 GB szabad hely.

    ![Telepítés helye](./media/physical-manage-configuration-server/combined-wiz8.png)
10. A **Hálózat kiválasztása** területen adja meg a figyelőt (hálózati adaptert és SSL-portot), amelyen keresztül a konfigurációs kiszolgáló küldi és fogadja a replikált adatokat. A 9443-as port a replikációs forgalom küldésére és fogadására használt alapértelmezett port, ez azonban a környezeti követelményektől függően módosítható. A 9443-as port mellett a 443-as portot is megnyitjuk, amelyen keresztül egy webkiszolgáló a replikálási műveleteket vezényli. Ne használja a 443-as porton küldése vagy fogadása a replikációs forgalom számára.

    ![Hálózat kiválasztása](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Az **Összefoglalás** területen ellenőrizze az adatokat, majd kattintson a **Telepítés** gombra. A telepítés után a rendszer létrehoz egy hozzáférési kódot. Erre szüksége lesz, amikor engedélyezi a replikálást, ezért másolja le, és tárolja biztonságos helyen.


A regisztráció befejezését követően a kiszolgáló megjelenik a tároló **Beállítások** > **Kiszolgálók** paneljén.


## <a name="install-from-the-command-line"></a>Telepítése a parancssorból

Futtassa a telepítőfájlt az alábbiak szerint:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Példa
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Paraméterek

|Paraméter neve| Typo | Leírás| Értékek|
|-|-|-|-|
| /ServerMode|Szükséges|Megadja, hogy a konfigurációs és folyamatkiszolgálót is, vagy csak a folyamatkiszolgálót kell-e telepíteni.|CS<br>PS|
|/InstallLocation|Szükséges|Az összetevők telepítési mappája| A számítógép bármely mappája|
|/MySQLCredsFilePath|Szükséges|A fájl elérési útja, amelyen a MySQL-kiszolgáló hitelesítő adatai tárolva vannak|A fájlnak az alább megadott formátumúnak kell lennie|
|/VaultCredsFilePath|Szükséges|A tároló hitelesítőadat-fájljának elérési útja|Érvényes fájlelérési út|
|/EnvType|Szükséges|Védeni kívánt környezet típusa |VMware<br>NonVMware|
|/PSIP|Szükséges|A replikációs adatátvitelhez használni kívánt hálózati adapter IP-címe| Bármilyen érvényes IP-cím|
|/CSIP|Szükséges|Annak a hálózati adapternek az IP-címe, amelyen a konfigurációs kiszolgáló figyel| Bármilyen érvényes IP-cím|
|/PassphraseFilePath|Szükséges|A jelszófájl teljes elérési útja|Érvényes fájlelérési út|
|/BypassProxy|Optional|Megadja, hogy a konfigurációs kiszolgáló proxy nélkül csatlakozik az Azure-hoz.|Az érték beszerzése innen: Venu|
|/ProxySettingsFilePath|Optional|Proxybeállítások (Az alapértelmezett proxyhoz hitelesítés vagy egyéni proxy szükséges).|A fájlnak az alább megadott formátumúnak kell lennie|
|DataTransferSecurePort|Optional|Az adatreplikációhoz használni kívánt PSIP-port száma| Érvényes portszám (az alapértelmezett érték 9433)|
|/SkipSpaceCheck|Optional|Gyorsítótárlemez terület-ellenőrzésének kihagyása| |
|/AcceptThirdpartyEULA|Szükséges|Ez a jelölő a külső féltől származó végfelhasználói licencszerződés elfogadását jelzi| |
|/ShowThirdpartyEULA|Optional|Külső felektől származó végfelhasználói licenszszerződés megjelenítése. Bemenetként való megadása esetén figyelmen kívül hagyja a többi paramétert.| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Create file input for MYSQLCredsFilePath

A MySQLCredsFilePath paraméter egy fájl fogadja bemeneti adatként. Hozza létre a fájlt a következő formátumban, és adja át azt MySQLCredsFilePath bemeneti paraméterként.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Hozzon létre a ProxySettingsFilePath bemeneti fájllal
ProxySettingsFilePath paraméter egy fájl fogadja bemeneti adatként. Hozza létre a fájlt a következő formátumban, és adja át azt ProxySettingsFilePath bemeneti paraméterként.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Proxybeállítások módosítása

A konfigurációs kiszolgáló gép proxybeállításainak következőképpen módosíthatja:

1. Jelentkezzen be a konfigurációs kiszolgálón.
2. Indítsa el a cspsconfigtool.exe használatával a parancsikont a.
3. Kattintson a **tár regisztrálása** fülre.
4. Töltse le a tároló regisztrációs új fájlt a portálról, és adja meg az eszköz bemeneteként.

  ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Adja meg az új proxy adatait, majd kattintson a **regisztrálása** gombra.
6. Nyisson meg egy rendszergazdai PowerShell-parancsablakot.
7. Futtassa az alábbi parancsot:

  ```PowerShell
  $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Ha további folyamatkiszolgálók a konfigurációs kiszolgáló csatlakoztatva van, akkor [javítsa ki a proxykiszolgáló beállításait a horizontális felskálázási folyamat összes kiszolgálójára](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) a központi telepítésben.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Ugyanahhoz a tárolóhoz a konfigurációs kiszolgáló újraregisztrálása
  1. Jelentkezzen be a konfigurációs kiszolgálóra.
  2. Indítsa el a cspsconfigtool.exe használatával a parancsikont az asztalon.
  3. Kattintson a **tár regisztrálása** fülre.
  4. Töltsön le új regisztrációs fájlt a portálról, és adja meg az eszköz bemeneteként.
        ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
  5. Adja meg a proxykiszolgáló adatait, majd kattintson a **regisztrálása** gombra.  
  6. Nyisson meg egy rendszergazdai PowerShell-parancsablakot.
  7. A következő parancs futtatásával

      ```PowerShell
      $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  Ha több folyamatkiszolgáló rendelkezik, akkor [regisztrálja azokat](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Konfigurációs kiszolgáló regisztrálása másik tárolóval

> [!WARNING]
> A következő lépés disassociates a konfigurációs kiszolgáló, a jelenlegi tárolóból, és az a konfigurációs kiszolgáló területén az összes védett virtuális gép replikációja leáll.

1. Jelentkezzen be a konfigurációs kiszolgáló
2. egy rendszergazdai jogú parancssorban futtassa a parancsot:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Indítsa el a cspsconfigtool.exe használatával a parancsikont az asztalon.
4. Kattintson a **tár regisztrálása** fülre.
5. Töltsön le új regisztrációs fájlt a portálról, és adja meg az eszköz bemeneteként.
6. Adja meg a proxykiszolgáló adatait, majd kattintson a **regisztrálása** gombra.  
7. Nyisson meg egy rendszergazdai PowerShell-parancsablakot.
8. A következő parancs futtatásával
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Konfigurációs kiszolgáló frissítése

Kumulatív frissítés a konfigurációs kiszolgálót frissíteni fogja futtatni. Frissítések akár N-4 verziók esetében is alkalmazható. Példa:

- Ha 9.7, 9.8, 9.9 vagy 9.10 - et futtatja, közvetlenül a 9.11 frissítheti.
- Ha a 9.6 vagy korábbi futtatja, és a 9.11 frissíteni, először frissítenie kell 9.7 verzióra. Mielőtt 9.11.

Kumulatív frissítések a configuration server összes verziójára való frissítéshez mutató hivatkozások találhatók a [frissítések wikioldal](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

A kiszolgáló frissítése a következőképpen:

1. Töltse le a frissítést telepítő a konfigurációs kiszolgálón.
2. A telepítő futtatásához kattintson duplán.
3. A telepítő észleli a gépen futó verziója.
4. Kattintson a **OK** erősítse meg, és a frissítés futtatása. 


## <a name="delete-or-unregister-a-configuration-server"></a>Törölje vagy a konfigurációs kiszolgáló regisztrációjának törlése

> [!WARNING]
> A következő győződjön meg arról, mielőtt elkezdené a konfigurációs kiszolgáló leszerelése.
> 1. [Tiltsa le a védelmet](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) alatt a konfigurációs kiszolgáló összes virtuális gép esetében.
> 2. [Szüntesse meg az](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) és [törlése](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) minden replikációs házirendek a konfigurációs kiszolgálóról.
> 3. [Törlés](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) összes vCenters kiszolgálók/vSphere-gazdagépek a konfigurációs kiszolgáló társítva.


### <a name="delete-the-configuration-server-from-azure-portal"></a>A konfigurációs kiszolgáló törlése az Azure Portal használatával
1. Az Azure-portálon keresse meg a **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók** a tároló menüjében.
2. A konfigurációs kiszolgáló leszerelése kívánt gombra.
3. A konfigurációs kiszolgáló részletei lapon kattintson a **törlése** gombra.
4. Kattintson a **Igen** annak a kiszolgálónak a törlés megerősítéséhez.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Távolítsa el a konfigurációs kiszolgáló és a hozzá tartozó függőségek
  > [!TIP]
  Ha azt tervezi, újra felhasználhatja az Azure Site Recovery a konfigurációs kiszolgálót, majd továbbléphet a 4. lépés közvetlenül

1. Jelentkezzen be a konfigurációs kiszolgálóra rendszergazdaként.
2. Nyisson meg Vezérlőpult > Program > Programok eltávolítása
3. Távolítsa el a program a következő sorrendben:
  * Microsoft Azure Recovery Services Agent
  * A Microsoft Azure Site Recovery mobilitási szolgáltatás vagy fő célkiszolgálóként kiszolgáló
  * A Microsoft Azure Site Recovery Provider
  * A Microsoft Azure Site Recovery Configuration Server/folyamat Servernek
  * A Microsoft Azure Site Recovery konfigurációs függőségei
  * A MySQL-kiszolgáló egy 5.5-ös
4. Futtassa a következő parancsot a és a rendszergazdai jogú parancssorban.
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Vagy a kiszolgáló regisztrációjának konfigurációs kiszolgáló (PowerShell)

1. [Telepítés](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-4.4.0) Azure PowerShell-modul
2. Jelentkezzen be a parancsot az Azure-fiókjába
    
    `Connect-AzureRmAccount`
3. Válassza ki az előfizetést, amelyben a tároló megtalálható

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Most már a tárolási környezet beállítása
    
    ```PowerShell
    $Vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Válassza ki a konfigurációs kiszolgáló beolvasása

    `$Fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. A konfigurációs kiszolgáló törlése

    `Remove-AzureRmSiteRecoveryFabric -Fabric $Fabric [-Force] `

> [!NOTE]
> A **-Force** a konfigurációs kiszolgáló eltávolításának vagy törlésének kényszerítése a Remove-AzureRmSiteRecoveryFabric lehetőség használható.

## <a name="renew-ssl-certificates"></a>SSL-tanúsítványok megújítása
A konfigurációs kiszolgáló rendelkezik egy beépített web server, mely a mobilitási szolgáltatás, a folyamatkiszolgálók és a fő célkiszolgálókat ahhoz kapcsolódó tevékenységeket koordinálja. A webkiszolgáló egy SSL-tanúsítványt használ az ügyfelek hitelesítéséhez. A tanúsítvány három év után lejár, és bármikor meg lehet újítani.

### <a name="check-expiry"></a>Lejáratának ellenőrzése

Konfigurációs kiszolgáló központi telepítése előtt a 2016. május esetén a tanúsítvány lejárati egy év lett beállítva. Ha rendelkezik egy tanúsítvánnyal határidővel lejár, az alábbiak történnek:

- Ha a lejárati dátum két hónapig vagy kevesebb, mint a szolgáltatás elindul, értesítések küldése a portálon, és e-mailben (ha van, amelyre Ön feliratkozott az Azure Site Recovery-értesítések).
- Egy értesítési szalagcím jelenik meg az erőforrás-tároló oldalon. Kattintson a szalagcímre további részletekért.
- Ha megjelenik egy **frissítés most** gomb, akkor ez azt jelenti, hogy nincsenek-e néhány összetevőt a környezetben, amely még nem lett frissítve 9.4.xxxx.x vagy újabb verzió. A tanúsítvány megújításához-összetevő frissítését. Régebbi verziójával nem újíthatók.

### <a name="renew-the-certificate"></a>A tanúsítvány megújítása

1. Nyissa meg a tároló **Site Recovery-infrastruktúra** > **konfigurációs kiszolgáló**, és kattintson a szükséges konfigurációs kiszolgálóra.
2. Megjelenik a lejárati dátum **konfigurációs kiszolgáló állapota**
3. Kattintson a **tanúsítványok megújítása**. 




## <a name="common-issues"></a>Gyakori problémák
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>További lépések

Tekintse át az oktatóanyagok, valamint beállításának [fizikai kiszolgálók](tutorial-physical-to-azure.md) az Azure-bA.

