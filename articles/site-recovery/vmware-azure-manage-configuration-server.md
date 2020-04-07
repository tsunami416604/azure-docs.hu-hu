---
title: A vészhelyreállítás konfigurációs kiszolgálójának kezelése az Azure Site Recovery szolgáltatással
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: ramamill
ms.openlocfilehash: 56c53b9e2388cc0594076a5ef35b072216aec20d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672738"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>A VMware VM/physical server vész-helyreállítási konfigurációs kiszolgálójának kezelése

A helyszíni konfigurációs kiszolgálót akkor állítja be, amikor [az Azure Site Recovery](site-recovery-overview.md) szolgáltatást használja a VMware virtuális gépek és a fizikai kiszolgálók azure-beli vészhelyreállítása érdekében. A konfigurációs kiszolgáló koordinálja a helyszíni VMware és az Azure közötti kommunikációt, és kezeli az adatreplikációt. Ez a cikk összefoglalja a konfigurációs kiszolgáló üzembe helyezését követő en végzett gyakori feladatokat.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>Windows-licenc frissítése

Az OVF sablonhoz mellékelt licenc 180 napig érvényes értékelési licenc. A megszakítás nélküli használat érdekében aktiválnia kell a Windows rendszert beszerzett licenccel. A licencfrissítés önálló kulccsal vagy KMS-szabványkulccsal végezhető el. Útmutató a [DISM Windows parancssorból](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options)érhető el az operációs rendszer futtatásához. Kulcsok beszerzéséhez olvassa el a [KMS-ügyfél beállítása](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys).

## <a name="access-configuration-server"></a>Hozzáférés konfigurációs kiszolgálója

A konfigurációs kiszolgáló taszerint érheti el:

* Jelentkezzen be a virtuális gép, amelyen telepítve van, és indítsa el az **Azure Site Recovery Configuration Manager** az asztali parancsikont.
* Másik lehetőségként távolról is elérheti a konfigurációs kiszolgálót https://*ConfigurationServerName*/:44315/. Jelentkezzen be rendszergazdai hitelesítő adatokkal.

## <a name="modify-vmware-server-settings"></a>VMware-kiszolgáló beállításainak módosítása

1. Ha egy másik VMware-kiszolgálót szeretne társítani a konfigurációs kiszolgálóhoz, [a bejelentkezés](#access-configuration-server)után válassza **a VCenter Server/vSphere ESXi kiszolgáló hozzáadása**lehetőséget.
2. Írja be a részleteket, majd kattintson **az OK gombra.**

## <a name="modify-credentials-for-automatic-discovery"></a>Hitelesítő adatok módosítása az automatikus felderítéshez

1. A VMware virtuális gépek automatikus felderítéséhez a VMware-kiszolgálóhoz való csatlakozáshoz használt hitelesítő adatok frissítéséhez a [bejelentkezés](#access-configuration-server)után válassza ki a fiókot, és kattintson a **Szerkesztés**gombra.
2. Írja be az új hitelesítő adatokat, majd kattintson **az OK gombra.**

    ![VMware módosítása](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

A hitelesítő adatokat a CSPSConfigtool.exe fájlon keresztül is módosíthatja.

1. Jelentkezzen be a konfigurációs kiszolgálóra, és indítsa el a CSPSConfigtool.exe programot
2. Válassza ki a módosítani kívánt fiókot, majd kattintson a **Szerkesztés gombra.**
3. Adja meg a módosított hitelesítő adatokat, és kattintson **az Ok** gombra.

## <a name="modify-credentials-for-mobility-service-installation"></a>Hitelesítő adatok módosítása a Mobilszolgáltatás telepítéséhez

Módosítsa a Mobility Service automatikus telepítéséhez használt hitelesítő adatokat a replikációra lehetővé tesz VMware virtuális gépeken.

1. A [bejelentkezés](#access-configuration-server)után válassza **a Virtuálisgép-hitelesítő adatok kezelése** lehetőséget.
2. Válassza ki a módosítani kívánt fiókot, és kattintson a **Szerkesztés gombra.**
3. Írja be az új hitelesítő adatokat, majd kattintson **az OK gombra.**

    ![A mobilitási szolgáltatás hitelesítő adatainak módosítása](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

A hitelesítő adatokat a CSPSConfigtool.exe fájlon keresztül is módosíthatja.

1. Jelentkezzen be a konfigurációs kiszolgálóra, és indítsa el a CSPSConfigtool.exe programot
2. Válassza ki a módosítani kívánt fiókot, és kattintson a **Szerkesztés gombra.**
3. Írja be az új hitelesítő adatokat, és kattintson **az Ok gombra.**

## <a name="add-credentials-for-mobility-service-installation"></a>Hitelesítő adatok hozzáadása a Mobilitásszolgáltatás telepítéséhez

Ha nem adott hozzá hitelesítő adatokat a konfigurációs kiszolgáló OVF-telepítése során,

1. A [bejelentkezés](#access-configuration-server)után válassza **a Virtuálisgép-hitelesítő adatok kezelése**lehetőséget.
2. Kattintson a **Virtuálisgép-hitelesítő adatok hozzáadása**elemre.
    ![mobilitási hitelesítő adatok hozzáadása](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Adja meg az új hitelesítő adatokat, és kattintson a **Hozzáadás gombra.**

A kriptücéseszköz-konfigurációs eszköz.konként is hozzáadhat hitelesítő adatokat.

1. Jelentkezzen be a konfigurációs kiszolgálóra, és indítsa el a CSPSConfigtool.exe programot
2. Kattintson a **Hozzáadás**gombra, adja meg az új hitelesítő adatokat, majd kattintson az **Ok gombra.**

## <a name="modify-proxy-settings"></a>Proxybeállítások módosítása

Módosítsa a konfigurációs kiszolgáló gép által az Azure-hoz való internet-hozzáféréshez használt proxybeállításokat. Ha a konfigurációs kiszolgálógépen futó alapértelmezett folyamatkiszolgálómellett van folyamatkiszolgáló-gépe, módosítsa mindkét gépen a beállításokat.

1. A konfigurációs kiszolgálóra [való bejelentkezés](#access-configuration-server) után válassza a **Kapcsolat kezelése**lehetőséget.
2. Frissítse a proxyértékeket. Ezután a Beállítások frissítéséhez válassza a **Mentés** gombot.

## <a name="add-a-network-adapter"></a>Hálózati adapter hozzáadása

Az Open Virtualization Format (OVF) sablon egyetlen hálózati adapterrel telepíti a konfigurációs kiszolgáló virtuális gépét.

- Hozzáadhat [egy további adaptert a virtuális géphez,](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)de hozzá kell adnia, mielőtt regisztrálna a konfigurációs kiszolgálót a tárolóban.
- Adapter hozzáadása után a konfigurációs kiszolgáló a tárolóban, adja hozzá az adaptert a virtuális gép tulajdonságai. Ezután [újra regisztrálnia](#reregister-a-configuration-server-in-the-same-vault) kell a kiszolgálót a tárolóban.

## <a name="how-to-renew-ssl-certificates"></a>Az SSL-tanúsítványok megújítása

A konfigurációs kiszolgáló beépített webkiszolgálóval rendelkezik, amely a mobilitási ügynökök tevékenységeit összes védett gépen, beépített/kibővített folyamatkiszolgálókon és fő célkiszolgálókon vezényli. A webkiszolgáló SSL-tanúsítványt használ az ügyfelek hitelesítéséhez. A tanúsítvány három év után lejár, és bármikor megújítható.

### <a name="check-expiry"></a>Lejárat ellenőrzése

A lejárati dátum a **Konfigurációs kiszolgáló állapota**alatt jelenik meg. A konfigurációs kiszolgáló 2016 májusa előtti telepítéseesetén a tanúsítvány lejárata egy évre volt állítva. Ha lejárt a tanúsítványa, a következő jelenség következik be:

- Ha a lejárati idő legkét hónap vagy annál kevesebb, a szolgáltatás megkezdi az értesítések küldését a portálon, és e-mailben (ha feliratkozott a Site Recovery értesítésekre).
- Egy értesítési szalagcím jelenik meg a tároló erőforráslapján. További információért válassza ki a szalagcímet.
- Ha megjelenik a **Frissítés most** gomb, az azt jelzi, hogy a környezet egyes összetevői nem lettek frissítve a 9.4.xxxx.x vagy újabb verzióra. Frissítse az összetevőket a tanúsítvány megújítása előtt. Régebbi verziókon nem újítható meg.

### <a name="if-certificates-are-yet-to-expire"></a>Ha a tanúsítványok még nem jártak le

1. A megújításhoz nyissa meg a **Site Recovery Infrastructure** > **Configuration Server kiszolgálót**a tárolóban. Válassza ki a szükséges konfigurációs kiszolgálót.
2. Győződjön meg arról, hogy az összes összetevő kibővített folyamatkiszolgálói, fő célkiszolgálói és mobilitási ügynökei az összes védett gépen a legújabb verziókon vannak, és csatlakoztatott állapotban vannak.
3. Most válassza **a Tanúsítványok megújítása**lehetőséget.
4. Gondosan kövesse az ezen a lapon található utasításokat, és kattintson az ok gombra a tanúsítványok megújításához a kiválasztott konfigurációs kiszolgálón, és ez a hozzá juk tartozó összetevők.

### <a name="if-certificates-have-already-expired"></a>Ha a tanúsítványok már lejártak

1. A lejárat után a tanúsítványok **nem újíthatók meg az Azure Portalról.** A folytatás előtt győződjön meg arról, hogy az összes összetevő kibővített folyamatkiszolgálója, fő célkiszolgálója és mobilitási ügynöke ia legújabb verziókon vannak, és csatlakoztatott állapotban vannak.
2. **Csak akkor kövesse ezt az eljárást, ha a tanúsítványok már lejártak.** Jelentkezzen be a konfigurációs kiszolgálóra, keresse meg a C meghajtót, > a Program Data > Site Recovery > otthoni > svsystems > bin és a "RenewCerts" végrehajtó eszköz rendszergazdaként való végrehajtása.
3. Megjelenik egy PowerShell-végrehajtási ablak, és elindítja a tanúsítványok megújítását. A művelet akár 15 percet is igénybe vehet. Ne zárja be az ablakot a megújítás befejezéséig.

:::image type="content" source="media/vmware-azure-manage-configuration-server/renew-certificates.png" alt-text="RenewCertificates (Tanúsítványok megújítása)":::

## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Konfigurációs kiszolgáló újraregisztrálása ugyanabban a tárolóban

Szükség esetén újra regisztrálhatja újra a konfigurációs kiszolgálót ugyanabban a tárolóban. Ha rendelkezik egy további folyamatkiszolgáló-géppel, a konfigurációs kiszolgálógépen futó alapértelmezett folyamatkiszolgálón kívül mindkét gépet újra regisztrálja újra.


1. A tárolóban nyissa meg a**Hely-helyreállítási infrastruktúra** > **konfigurációs kiszolgálóinak** **kezelése** > lehetőséget.
2. A **Kiszolgálók alkalmazásban**válassza **a Regisztrációs kulcs letöltése** lehetőséget a tároló hitelesítő fájljának letöltéséhez.
3. Jelentkezzen be a konfigurációs kiszolgálógépre.
4. A **%ProgramData%\ASR\home\svsystems\bin mappában**nyissa meg **a cspsconfigtool.exe programot.**
5. A **Vault regisztráció jalapján** válassza a **Tallózás**gombot, és keresse meg a letöltött tároló hitelesítő adatait.
6. Szükség esetén adja meg a proxykiszolgáló adatait. Ezután kattintson a **Regisztrálás** elemre.
7. Nyisson meg egy rendszergazdai PowerShell parancsablakot, és futtassa a következő parancsot:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >Annak érdekében, hogy **a legújabb tanúsítványokat a konfigurációs** kiszolgálóról a horizontális felskálázási folyamatkiszolgálóra szeretné lehallgatni, hajtsa végre a " *\<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe>"-registermt*

8. Végül indítsa újra az obengine-t a következő parancs végrehajtásával.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Konfigurációs kiszolgáló regisztrálása másik tárolóval

> [!WARNING]
> A következő lépés elválasztja a konfigurációs kiszolgálót az aktuális tárolótól, és a konfigurációs kiszolgáló alatt leállított összes védett virtuális gép replikációja leáll.

1. Jelentkezzen be a konfigurációs kiszolgálóra.
2. Nyisson meg egy rendszergazdai PowerShell parancsablakot, és futtassa a következő parancsot:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Indítsa el a konfigurációs kiszolgáló készülék böngészőportálját az asztalon található parancsikon használatával.
4. Hajtsa végre az új konfigurációs kiszolgáló [regisztrációjához](vmware-azure-tutorial.md#register-the-configuration-server)hasonló regisztrációs lépéseket.

## <a name="upgrade-the-configuration-server"></a>A konfigurációs kiszolgáló frissítése

A konfigurációs kiszolgáló frissítéséhez összegző frissítők futtatásával. A frissítések legfeljebb N-4 verziókhoz jelentkezhetnek. Példa:

- Ha a 9.7, 9.8, 9.9 vagy 9.10 rendszert futtatja, közvetlenül frissíthet a 9.11-re.
- Ha 9.6-os vagy korábbi verziót futtat, és 9.11-re szeretne frissíteni, először frissítenie kell a 9.7-es verzióra. 9.11.

Az Azure Site Recovery összetevőinek támogatási nyilatkozatával kapcsolatos részletes útmutatásért tekintse meg [itt.](https://aka.ms/asr_support_statement)
A konfigurációs kiszolgáló összes verziójára való frissítésre mutató frissítő frissítőfrissítő kondukkulyákra mutató hivatkozások [itt](https://aka.ms/asr_update_rollups)érhetők el.

> [!IMPORTANT]
> Az Azure Site Recovery összetevő minden új verziója "N" kiadásával az "N-4" alatti összes verzió támogatáson kívül áll. Mindig tanácsos frissíteni a legújabb verzióra.</br>
> Az Azure Site Recovery összetevőinek támogatási nyilatkozatával kapcsolatos részletes útmutatásért tekintse meg [itt.](https://aka.ms/asr_support_statement)

Frissítse a kiszolgálót az alábbiak szerint:

1. A tárolóban nyissa meg a**Hely-helyreállítási infrastruktúra** > **konfigurációs kiszolgálóinak** **kezelése** > című területet.
2. Ha rendelkezésre áll frissítés, egy hivatkozás jelenik meg az **Ügynök >** oszlopban.
    ![Frissítés](./media/vmware-azure-manage-configuration-server/update2.png)
3. Töltse le a frissítéstelepítő fájlját a konfigurációs kiszolgálóra.

    ![Frissítés](./media/vmware-azure-manage-configuration-server/update1.png)

4. A telepítő futtatásához kattintson duplán.
5. A telepítő észleli a számítógépen futó aktuális verziót. A frissítés elindításához kattintson az **Igen** gombra.
6. Amikor a frissítés befejeződik, a kiszolgáló konfigurációja érvényesíti.

    ![Frissítés](./media/vmware-azure-manage-configuration-server/update3.png)

7. A telepítő bezárásához kattintson a **Befejezés** gombra.
8. A Site Recovery többi összetevőjének frissítéséhez olvassa el a [frissítési útmutatót.](https://aka.ms/asr_vmware_upgrades)

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Konfigurációs kiszolgáló/folyamatkiszolgáló frissítése a parancssorból

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
MySQLRootPassword = "Password>"
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

## <a name="delete-or-unregister-a-configuration-server"></a>Konfigurációs kiszolgáló törlése vagy regisztrációjának megszüntetése

1. [Tiltsa le](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) a védelmet a konfigurációs kiszolgáló n lévő összes virtuális gépe számára.
2. Az összes replikációs házirend társításának és [törlésének leválasztaniésa](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) a konfigurációs kiszolgálóról. [Disassociate](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)
3. [Törölje](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) a konfigurációs kiszolgálóhoz társított összes vCenter-kiszolgálót/vSphere-állomást.
4. A tárolóban nyissa meg a Site Recovery Infrastructure Configuration Servers **(Hely-helyreállítási infrastruktúra** > **konfigurációs kiszolgálói) webhely-konfigurációs kiszolgálóit.**
5. Jelölje ki az eltávolítani kívánt konfigurációs kiszolgálót. Ezután a **Részletek** lapon válassza a **Törlés**lehetőséget.

    ![Konfigurációs kiszolgáló törlése](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Törlés a PowerShell használatával

A konfigurációs kiszolgálót a PowerShell használatával is törölheti.

1. [Telepítse](https://docs.microsoft.com/powershell/azure/install-Az-ps) az Azure PowerShell-modult.
2. Jelentkezzen be Azure-fiókjába ezzel a paranccsal:

    `Connect-AzAccount`
3. Válassza ki a tároló-előfizetést.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Állítsa be a tároló környezetben.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. A konfigurációs kiszolgáló beolvasása.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Törölje a konfigurációs kiszolgálót.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> Használhatja a **-Force** opciót az Remove-AzSiteRecoveryFabric programban a konfigurációs kiszolgáló kényszerített törléséhez.

## <a name="generate-configuration-server-passphrase"></a>Konfigurációs kiszolgáló jelmondatának létrehozása

1. Jelentkezzen be a konfigurációs kiszolgálóra, majd nyisson meg egy parancssori ablakot rendszergazdaként.
2. A könyvtár bin mappára való módosításához hajtsa végre a **%ProgramData%\ASR\home\svsystems\bin parancscd-t**
3. A jelszófájl létrehozásához hajtsa végre a **genpassphrase.exe -v > MobSvc.passphrase fájlt.**
4. A jelszó a **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase mappában**található fájlban lesz tárolva.

## <a name="refresh-configuration-server"></a>Konfigurációs kiszolgáló frissítése

1. Az Azure Portalon keresse meg a **Helyreállítási szolgáltatások tárolóját** > **a VMware & fizikai gépek** > **konfigurációs kiszolgálóinak** **hely-helyreállítási infrastruktúrájának** > **kezelése** > 
2. Kattintson a frissíteni kívánt konfigurációs kiszolgálóra.
3. A panelen a kiválasztott konfigurációs kiszolgáló részletei közül kattintson a **További** > **kiszolgáló frissítése gombra.**
4. A feladat előrehaladásának figyelése a **Helyreállítási szolgáltatások tárolója** > **helyreállítási** > **helyhelyreállítási feladatok alatt.**

## <a name="failback-requirements"></a>Feladat-visszavételre vonatkozó követelmények

Az újbóli védelem és a feladat-visszavétel során a helyszíni konfigurációs kiszolgálónak futnia kell, és csatlakoztatott állapotban kell lennie. A sikeres feladat-visszavétel hez a rendszernek léteznie kell a konfigurációs kiszolgáló adatbázisában a sikertelensé tett virtuális gépnek.

Győződjön meg arról, hogy rendszeresen ütemezett biztonsági mentést készít a konfigurációs kiszolgálóról. Ha katasztrófa történik, és a konfigurációs kiszolgáló elvész, először vissza kell állítania a konfigurációs kiszolgálót egy biztonsági másolatból, és meg kell győződnie arról, hogy a visszaállított konfigurációs kiszolgáló ugyanazt az IP-címet, amellyel a tárolóba regisztrálták. A feladat-visszavétel nem fog működni, ha a visszaállított konfigurációs kiszolgálóhoz más IP-címet használ.

## <a name="next-steps"></a>További lépések

Tekintse át a [VMware virtuális gépek](vmware-azure-tutorial.md) azure-ba való vészhelyreállításának beállításával szolgáló oktatóanyagokat.
