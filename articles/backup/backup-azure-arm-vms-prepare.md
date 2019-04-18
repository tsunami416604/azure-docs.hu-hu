---
title: Azure virtuális gépek biztonsági mentése az Azure Backup segítségével a Recovery Services-tároló
description: Ismerteti, hogyan lehet Azure virtuális gépek biztonsági mentése a Recovery Services-tároló az Azure Backup használatával
service: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: raynew
ms.openlocfilehash: 142ffdadf4adb1ee07f3592624cbdddfb310b580
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264556"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Azure virtuális gépek biztonsági mentése Recovery Services-tárolóban

Ez a cikk bemutatja, hogyan lehet Azure virtuális gépek biztonsági mentése Recovery Services-tárolót, a használatával a [Azure Backup](backup-overview.md) szolgáltatás. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az Azure virtuális gépek előkészítése.
> * Hozzon létre egy tárolót.
> * Virtuális gépek felderítéséhez és a egy biztonsági mentési szabályzat konfigurálása.
> * Az Azure virtuális gépek biztonsági mentésének engedélyezése.
> * Futtatja a kezdeti biztonsági mentést.


> [!NOTE]
> Ez a cikk azt ismerteti, hogyan állítsa be a tárolót, és válassza ki a virtuális gépek biztonsági mentése. Ez hasznos, ha azt szeretné, hogy több virtuális gépek biztonsági mentése. Lehetőségként [egyetlen Azure virtuális gép biztonsági mentése](backup-azure-vms-first-look-arm.md) közvetlenül a a virtuális gép beállításait.

## <a name="before-you-start"></a>Előkészületek


- [Felülvizsgálat](backup-architecture.md#architecture-direct-backup-of-azure-vms) az Azure virtuális gép biztonsági mentési architektúra.
- [Ismerje meg](backup-azure-vms-introduction.md) Azure virtuális gépek biztonsági mentését, és a biztonsági mentési bővítményt.
- [Tekintse át a támogatási mátrix](backup-support-matrix-iaas.md) biztonsági mentés konfigurálása előtt.

Ezenkívül van néhány dolgot, amely a bizonyos körülmények között szüksége lehet:

- **A Virtuálisgép-ügynök telepítése a virtuális gépen**: Az Azure Backup biztonsági másolatot készít az Azure virtuális gépek a gépen futó Azure-beli Virtuálisgép-ügynök bővítmény telepítésével. Ha a virtuális gép az Azure marketplace-lemezképből lett létrehozva, az ügynök telepítve és fut. Ha egy egyéni virtuális Gépet hoz létre, vagy egy helyszíni gép áttelepítése, szüksége lehet [telepítse kézzel az ügynököt](#install-the-vm-agent).
- **Explicit módon engedélyezi a kimenő hozzáférést**: Általában nem kell explicit módon engedélyezi a kimenő hálózati hozzáférés egy Azure virtuális gép ahhoz, hogy azt az Azure Backup folytatott kommunikációhoz. Azonban néhány virtuális gépet is szembesülhet kapcsolódási problémák, megjelenítése a **ExtensionSnapshotFailedNoNetwork** hiba csatlakozásra tett kísérlet közben. Ha ez történik, akkor [explicit módon engedélyezi a kimenő hozzáférést](#explicitly-allow-outbound-access), így az Azure Backup bővítmény képes kommunikálni az Azure nyilvános IP-címeket, a biztonsági mentések forgalmától.


## <a name="create-a-vault"></a>Tároló létrehozása

 A tároló tárolja a biztonsági másolatok és az idők során létrehozott helyreállítási pontokat, és tárolja a biztonsági másolat gépek társított biztonsági mentési szabályzatok. Hozzon létre egy tárolót a következőképpen:    

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).    
2. Írja be a kifejezést a keresőmezőbe, **Recovery Services**. A **szolgáltatások**, kattintson a **Recovery Services-tárolók**.   

     ![Keresse meg a Recovery Services-tárolók](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/> 

3. A **Recovery Services-tárolók** menüben kattintson a **+ Hozzáadás**.    

     ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)   

4. A **Recovery Services-tároló**, adjon meg egy rövid nevet, a tárolót azonosító.   
    - A névnek egyedinek kell lennie az Azure-előfizetéshez.   
    - 2 – 50 karaktert tartalmazhat.    
    - Betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.   
5. Válassza ki az Azure-előfizetést, erőforráscsoportot és a földrajzi régióban, amelyben a tároló kell létrehozni. Ezt követően kattintson a **Create** (Létrehozás) gombra.    
    - A tároló létrehozása egy ideig is eltarthat.  
    - Figyelje a jobb felső területén, a portál.   


 Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listája. Ha nem látja a tárolót, válassza ki a **frissítése**.
 
![A Backup-tárolók listája](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)    

### <a name="modify-storage-replication"></a>Tárreplikáció módosítása

Alapértelmezés szerint a tárolók használata [georedundáns tárolás (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

- Ha a tároló az elsődleges biztonsági mentési mechanizmus, javasoljuk, GRS-t használ.
- Használhat [helyileg redundáns tárolás (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) egy olcsóbb számára.

Módosítsa a következőképpen tárolóreplikáció típusa:

1. Kattintson az új tároló **tulajdonságok** a a **beállítások** szakaszban.
2. A **tulajdonságok**alatt **biztonsági mentés konfigurációja**, kattintson a **frissítés**.
3. Válassza ki a tárolóreplikáció típusa, és kattintson a **mentése**.

      ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/full-blade.png)
> [!NOTE]
   > A tárolóreplikáció típusa nem módosítható, miután a tároló be van állítva, és tartalmazza a biztonsági másolati elemek. Ha azt szeretné, ehhez létre kell hoznia a tárolóban. 

## <a name="apply-a-backup-policy"></a>Biztonsági mentési szabályzat alkalmazása

A tároló biztonsági mentési szabályzat konfigurálása.

1. Kattintson a tárolóban **+ biztonsági mentés** a a **áttekintése** szakaszban.

   ![Biztonsági mentés gomb](./media/backup-azure-arm-vms-prepare/backup-button.png)


2. A **biztonsági mentés célja** > **a számítási feladat futtató?** kiválasztása **Azure**. A **miről szeretne biztonsági másolatot készíteni?** kiválasztása **virtuális gép** >  **OK**. Ez regisztrálja a Virtuálisgép-bővítményt a tárolóban.

   ![Biztonsági mentés és a biztonsági mentés célja panel](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. A **biztonsági mentési szabályzat**, válassza ki a tárolóval társítani kívánt házirendet. 
    - A virtuális gép naponta egyszer készít az alapértelmezett szabályzat. A napi biztonsági mentések 30 napig megőrződnek. Az azonnali helyreállítás pillanatfelvételek megmaradnak a két napig.
    - Ha nem szeretné használni az alapértelmezett házirendet, válassza ki a **hozzon létre új**, és a egy egyéni szabályzat létrehozása a következő eljárásban leírtak szerint.

      ![Alapértelmezett biztonsági mentési szabályzat](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. A **válassza ki a virtuális gépek**, válassza ki a virtuális gépek biztonsági mentését a szabályzattal szeretné. Ezután kattintson az **OK** gombra.

   - A kijelölt virtuális gépek ellenőrzése megtörtént.
   - Választhat a virtuális gépek és a tárolónak ugyanabban a régióban.
   - Virtuális gépek csak biztonsági mentésre alkalmas Ezenkívül egyetlen tárban.

     !["A virtuális gépek kiválasztása" panelen](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. A **biztonsági mentési**, kattintson a **biztonsági mentés engedélyezése**. Telepítheti a szabályzatot a tárolóban, és a virtuális gépeket, és telepíti a biztonsági mentési bővítményt a Virtuálisgép-ügynök az Azure virtuális gépen.
     
     !["A biztonsági mentés engedélyezése" gombra](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Biztonsági mentés engedélyezése után:

- A Backup szolgáltatás telepíti a biztonsági mentési bővítményt, a virtuális gép fut-e.
- Egy kezdeti biztonsági mentés a biztonsági mentési ütemterv szerint fog futni.
- Ha futtatja a biztonsági mentések, vegye figyelembe, hogy:
    - Egy futó virtuális Gépre van a legnagyobb valószínűséggel egy alkalmazáskonzisztens helyreállítási pont rögzítésére.
    - Azonban akkor is, ha a virtuális gép ki van kapcsolva biztonsági mentését. Az ilyen virtuális Gépet egy offline állapotú virtuális gép néven ismert. Ebben az esetben a helyreállítási pont összeomlás-konzisztens lesz.
    

### <a name="create-a-custom-policy"></a>Egyéni szabályzat létrehozása

Ha egy új biztonsági mentési szabályzat létrehozása lehetőséget választotta, adja meg a házirend-beállításokat.

1. A **házirendnév**, adjon meg egy kifejező nevet.
2. A **biztonsági mentés ütemezése** adja meg, amikor a biztonsági mentéseket kell tenni. Az Azure virtuális gépek napi vagy heti biztonsági mentéseket is igénybe vehet.
2. A **azonnali visszaállítása**, adja meg, hogy mennyi ideig szeretné megőrizni a pillanatképek helyi az azonnali helyreállítás.
    - Visszaállításkor, biztonsági másolatot a virtuális gép lemezek vannak storage-tárhelyéből, helyreállítási tárolási helyét a hálózaton keresztül. Az azonnali helyreállítás biztonsági mentési adatok átvitele a tárolóba való várakozás nélkül egy biztonsági mentési feladat során vett helyileg tárolt pillanatképek használhatja.
    - Öt nap közötti, az azonnali helyreállítás pillanatképeinek őrizheti meg. Két napot az alapértelmezett beállítása.
3. A **megőrzési**, adja meg, hogy mennyi ideig szeretné megőrizni a a napi vagy heti biztonsági mentési pontok.
4. A **havi biztonsági mentési pontok megőrzése**, adja meg, hogy szeretné-e egy-egy havi biztonsági másolat a napi vagy heti biztonsági mentések megtartása. 
5. Kattintson a **OK** mentse a szabályzatot.

    ![Új biztonsági mentési szabályzat](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Az Azure Backup nem támogatja az Azure virtuális gép biztonsági mentéseinek a nyári időszámítás – mentés módosítása a óra automatikus illesztését. Idő változás esetén módosítsa a biztonsági mentési szabályzatok manuálisan szükség szerint.

## <a name="trigger-the-initial-backup"></a>Az eseményindító a kezdeti biztonsági mentés

A kezdeti biztonsági mentés a ütemterv szerint fog futni, de is futtatható legyen azonnal az alábbiak szerint:

1. A tároló menüjében kattintson **biztonsági mentési elemek**.
2. A **biztonsági másolati elemek** kattintson **Azure virtuális gép**.
3. Az a **biztonsági másolati elemek** listában, kattintson a három pontra (...).
4. Kattintson a **biztonsági mentés**.
5. A **biztonsági mentés**, használja a naptárvezérlőt annak, hogy megőrződjön-e a helyreállítási pont kiválasztására. Ezután kattintson az **OK** gombra.
6. A portál feladatértesítések figyelésére. Figyelheti a feladat előrehaladását a tároló irányítópultján > **biztonsági mentési feladatok** > **folyamatban**. A virtuális gép méretétől függően a kezdeti biztonsági mentés létrehozása hosszabb időt vehet igénybe.

## <a name="optional-steps-install-agentallow-outbound"></a>Nem kötelező lépések (telepíthetik az ügynök/kimenő forgalom engedélyezése)
### <a name="install-the-vm-agent"></a>Virtuálisgép-ügynök telepítése

Az Azure Backup biztonsági másolatot készít az Azure virtuális gépek a gépen futó Azure-beli Virtuálisgép-ügynök bővítmény telepítésével. Ha a virtuális gép létrejött, az Azure Marketplace-rendszerképpel, az ügynök telepítve és fut. Ha egy egyéni virtuális Gépet hoz létre, vagy egy helyszíni gép áttelepítése, szüksége lehet az ügynök telepítése manuálisan, a táblázat foglalja össze.

**VM** | **Részletek**
--- | ---
**Windows** | 1. [Töltse le és telepítse](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) az ügynök MSI-fájlt.<br/><br/> 2. Telepítse a rendszergazdai engedélyekkel a gépen.<br/><br/> 3. Ellenőrizze a telepítést. A *C:\WindowsAzure\Packages* a virtuális gépen, kattintson a jobb gombbal **WaAppAgent.exe** > **tulajdonságok**. A a **részletei** lapon **termékverzió** kell 2.6.1198.718 vagy újabb.<br/><br/> Amikor az ügynököt frissít, győződjön meg arról, hogy nincs biztonsági mentési műveletek futnak, és [telepítse újra az ügynököt](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Telepítse az RPM- vagy DEB-csomag a terjesztési csomag adattárból. Ez a telepítése és frissítése az Azure Linux-ügynök az előnyben részesített módszere. Az összes a [terjesztési szolgáltatók által támogatott](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrálhatja az Azure-beli Linuxos ügynök csomag adattárak, valamint a lemezképek. Az ügynök nem érhető el a [GitHub](https://github.com/Azure/WALinuxAgent), de nem ajánlott, hogy ott telepítése.<br/><br/> Amikor az ügynököt frissít, ellenőrizze, nincs biztonsági mentési műveletek futnak, és a bináris fájlok frissítése.

### <a name="explicitly-allow-outbound-access"></a>Explicit módon engedélyezi a kimenő hozzáférést

A biztonsági mentési bővítményt a virtuális gépen az Azure nyilvános IP-címek kimenő hozzá kell férnie.

- Általában nem kell explicit módon engedélyezi a kimenő hálózati hozzáférés egy Azure virtuális gép ahhoz, hogy azt az Azure Backup folytatott kommunikációhoz.
- Ha nehézségek rendelkező virtuális gépek csatlakoztatása, vagy ha a hibát látja **ExtensionSnapshotFailedNoNetwork** próbál csatlakozni, amikor érdemes explicit módon engedélyezi a hozzáférést, a biztonsági mentési bővítményt kommunikálhatnak az Azure nyilvános IP-címhez biztonsági mentési forgalom címeit. Hozzáférési módszer a következő táblázat foglalja össze.


**Beállítás** | **Művelet** | **Részletek** 
--- | --- | --- 
**Az NSG-szabályok beállítása** | Lehetővé teszi a [Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/> Lehetővé teszi, és minden címtartományt kezelése, helyett hozzáadhat egy szabályt, amely engedélyezi a hozzáférést az Azure Backup szolgáltatás használata egy [szolgáltatáscímke](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). | [További](../virtual-network/security-overview.md#service-tags) szolgáltatáscímkék kapcsolatban.<br/><br/> Szolgáltatások címkék hozzáférés-kezelés egyszerűsítése és többletköltséggel nem.
**A proxy üzembe helyezése** | A forgalom útválasztási HTTP-proxy kiszolgáló telepítése. | Az Azure és a tároló nem csak a teljes hozzáférést biztosít.<br/><br/> Szabályozható a tároló URL-címek használata engedélyezett.<br/><br/> A virtuális gépek internet egyetlen pont hozzáférést.<br/><br/> Proxy további költségekkel.
**Az Azure-tűzfal beállítása** | A virtuális gép, egy teljesen minősített tartománynév-címke az Azure Backup szolgáltatás a forgalom az Azure-tűzfalon keresztül | Könnyen használható, ha rendelkezik Azure-tűzfal beállítása egy virtuális hálózat alhálózatához.<br/><br/> Nem a saját FQDN-címkék létrehozása, és teljes tartománynevek módosíthatja a címke.<br/><br/> Ha az Azure-beli virtuális gépek felügyelt lemezekkel rendelkezik, szükség lehet további megnyitásához a tűzfalas (8443) portot.

#### <a name="establish-network-connectivity"></a>Hálózati kapcsolatok létrehozása

NSG-t, csatlakozni proxy, illetve a tűzfalon keresztül

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Az Azure kimenő hozzáférésének engedélyezéséhez egy NSG-szabály beállítása

Ha az NSG-KET a virtuális gép hozzáférés kezel, engedélyezi a kimenő hozzáférést a szükséges tartományok és a portok a biztonságimásolat-tároláshoz.

1. A virtuális gép tulajdonságainak > **hálózatkezelés**válassza **porton keresztüli kimenő szabály felvétele**.
2. A **kimenő biztonsági szabály felvétele**válassza **speciális**.
3. A **forrás**válassza **VirtualNetwork**.
4. A **alkalmazásport-tartományok forrás**, írjon be egy csillagot (*) bármely porton a kimenő hozzáférésének engedélyezéséhez.
5. A **cél**válassza **Szolgáltatáscímke**. Válassza ki a listából **Storage.region**. A régióban, amelyben a tároló és a virtuális gépek biztonsági mentése, kívánt találhatók.
6. A **Célporttartomány**, válassza ki azt a portot.
    - Nem felügyelt virtuális gép nem titkosított storage-fiók: 80
    - Nem felügyelt virtuális gép titkosított storage-fiók: 443-as (alapértelmezett beállítás)
    - A felügyelt virtuális gép: 8443.
7. A **protokoll**válassza **TCP**.
8. A **prioritású**, adja meg a prioritás értéke kisebb, mint minden újabb megtagadási szabályoknak.
   
   Ha egy szabályt, amely megtagadja a hozzáférést, az új engedélyezése szabály magasabbnak kell lennie. Ha például van egy **Deny_All** prioritással 1000, az új szabályra szabálykészlet 1000-nél kisebb értékre kell állítani.
9. Adjon meg egy nevet és leírást a szabályhoz, és válassza ki **OK**.

Az NSG-szabály több virtuális gép kimenő hozzáférésének engedélyezéséhez alkalmazhat. Ez a videó végigvezeti a folyamatot.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>Biztonsági mentési forgalomirányítást proxyn keresztül

Irányíthatja a biztonsági mentési forgalmat egy proxyn keresztül, és ezután a proxy hozzáférést biztosít a szükséges Azure-tartományokat. Konfigurálja a proxyt, hogy a következő virtuális Géphez:

- Az Azure virtuális gép kell irányítani a proxyn keresztül a nyilvános internetes kötött összes HTTP-forgalmat.
- A proxy kell engedélyezik a bejövő forgalmat a virtuális gépek a megfelelő virtuális hálózatban.
- Az NSG-t **Elégtelen-zárolási** szüksége van egy szabályt, amely lehetővé teszi, hogy a kimenő internetes forgalmat a virtuális gép érkezett.

###### <a name="set-up-the-proxy"></a>A proxy beállítása.

Ha nem rendelkezik a system fiók proxy, egyet az alábbiak szerint állíthatja:

1. Töltse le [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Futtatás **PsExec.exe -i -s cmd.exe** a parancssor a rendszerfiók alatt való futtatásához.
3. A böngészőben a rendszerkörnyezetben fut. Például **%PROGRAMFILES%\Internet Explorer\iexplore.exe** az Internet Explorerben.  
4. A proxy-beállítások megadása.
   - Linuxos gépeken:
     - Adja hozzá a sort, hogy a **/etc/környezet** fájlt:
       - **http_proxy = http:\//proxy IP-cím: proxy portja**
     - Adja hozzá ezeket a sorokat a **/etc/waagent.conf** fájlt:
         - **HttpProxy.Host=proxy IP address**
         - **HttpProxy.Port=proxy port**
   - Windows gépeken, a böngésző beállításait adja meg, hogy egy proxyt kell használni. Ha proxyt a felhasználói fiók jelenleg használ, ez a szkript használatával alkalmazza a beállítást, a fiók szintjén.
       ```powershell
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>A proxy bejövő kapcsolatok engedélyezése

A bejövő kapcsolatok engedélyezése a proxybeállításokat.

1. A Windows tűzfal, nyissa meg a **fokozott biztonságú Windows tűzfal**.
2. Kattintson a jobb gombbal **bejövő szabályok** > **új szabály**.
3. A **szabálytípus**válassza **egyéni** > **tovább**.
4. A **Program**válassza **minden program** > **tovább**.
5. A **protokollok és portok**:
   - A típusa **TCP**.
   - Állítsa be **helyi portok** való **adott**.
   - Állítsa be **távoli port** való **minden port**.
  
6. Fejezze be a varázslót, és adja meg a szabály nevét.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Vegyen fel egy kivételt az NSG-hez a proxy

Az NSG-t a **Elégtelen-zárolási**, bármely internetes címre a 80-as (HTTP) vagy a 443-as (HTTPS) porton a 10.0.0.5 bármely porton érkező adatforgalom engedélyezéséhez.

A következő PowerShell-parancsfájl egy példát biztosít forgalom engedélyezéséhez.
Ne hagyja a nyilvános internet-címeire irányuló kimenő, adjon meg egy IP-címtartomány (`-DestinationPortRange`), vagy használja a storage.region szolgáltatáscímke.   

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>Egy teljesen minősített tartománynév-címke a tűzfal hozzáférés engedélyezése

Azure-tűzfal engedélyezi a kimenő hozzáférést a hálózati forgalmat az Azure Backup szolgáltatásban is beállítása.

- [Ismerje meg](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) üzembe helyezése az Azure-tűzfal.
- [További információ](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN címkék.



## <a name="next-steps"></a>További lépések

- Az esetleges problémák megoldásához [Azure Virtuálisgép-ügynökökkel](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) vagy [Azure VM backup](backup-azure-vms-troubleshoot.md).
- [Visszaállítás](backup-azure-arm-restore-vms.md) Azure virtuális gépek.

