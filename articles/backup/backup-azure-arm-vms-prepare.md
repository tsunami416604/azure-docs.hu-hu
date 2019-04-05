---
title: Azure virtuális gépek biztonsági mentése Recovery Services-tárolót az Azure Backup használatával
description: Ismerteti, hogyan lehet Azure virtuális gépek biztonsági mentése Recovery Services-tárolót az Azure Backup használatával
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: 3342b15511305ab337d9b5032080e205e36150d3
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049813"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Azure virtuális gépek biztonsági mentése Recovery Services-tárolóban

Ez a cikk bemutatja, hogyan lehet Azure virtuális gépek biztonsági mentése Recovery Services-tárolót a használatával a [Azure Backup](backup-overview.md) szolgáltatás. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Ellenőrizze a támogatást és a egy biztonsági mentés előfeltételei.
> * Az Azure virtuális gépek előkészítése. Az Azure-beli Virtuálisgép-ügynök szükség esetén telepítse, és a virtuális gépek kimenő hozzáférésének ellenőrzése.
> * Hozzon létre egy tárolót.
> * Virtuális gépek felderítéséhez és a egy biztonsági mentési szabályzat konfigurálása.
> * Az Azure virtuális gépek biztonsági mentésének engedélyezése.


> [!NOTE]
> Ez a cikk azt ismerteti, hogyan állítsa be a tárolót, és válassza ki a virtuális gépek biztonsági mentése. Ez hasznos, ha azt szeretné, hogy több virtuális gépek biztonsági mentése. Lehetőségként [egyetlen Azure virtuális gép biztonsági mentése](backup-azure-vms-first-look-arm.md) közvetlenül a a virtuális gép beállításait.

## <a name="before-you-start"></a>Előkészületek


- [Felülvizsgálat](backup-architecture.md#architecture-direct-backup-of-azure-vms) az Azure virtuális gép biztonsági mentési architektúra.
- [Ismerje meg](backup-azure-vms-introduction.md) Azure virtuális gépek biztonsági mentését, és a biztonsági mentési bővítményt.
- [Tekintse át a támogatási mátrix](backup-support-matrix-iaas.md) Azure VM backup esetében.


## <a name="prepare-azure-vms"></a>Az Azure virtuális gépek előkészítése

Bizonyos esetekben szüksége lehet beállítani az Azure virtuális gépeken az Azure Virtuálisgép-ügynök, vagy explicit módon engedélyezi a kimenő hozzáférést a virtuális gép.

### <a name="install-the-vm-agent"></a>Virtuálisgép-ügynök telepítése 

Az Azure Backup biztonsági másolatot készít az Azure virtuális gépek a gépen futó Azure-beli Virtuálisgép-ügynök bővítmény telepítésével. Ha a virtuális gép létrejött, az Azure Marketplace-rendszerképpel, az ügynök telepítve és fut. Ha egy egyéni virtuális Gépet hoz létre, vagy egy helyszíni gép áttelepítése, szüksége lehet az ügynök telepítése manuálisan, a táblázat foglalja össze.

**VM** | **Részletek**
--- | ---
**Windows** | 1. [Töltse le és telepítse](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) az ügynök MSI-fájlt.<br/><br/> 2. Telepítse a rendszergazdai engedélyekkel a gépen.<br/><br/> 3. Ellenőrizze a telepítést. A *C:\WindowsAzure\Packages* a virtuális gépen, kattintson a jobb gombbal **WaAppAgent.exe** > **tulajdonságok**. A a **részletei** lapon **termékverzió** kell 2.6.1198.718 vagy újabb.<br/><br/> Amikor az ügynököt frissít, győződjön meg arról, hogy nincs biztonsági mentési műveletek futnak, és [telepítse újra az ügynököt](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Telepítse az RPM- vagy DEB-csomag a terjesztési csomag adattárból. Ez a telepítése és frissítése az Azure Linux-ügynök az előnyben részesített módszere. Az összes a [terjesztési szolgáltatók által támogatott](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrálhatja az Azure-beli Linuxos ügynök csomag adattárak, valamint a lemezképek. Az ügynök nem érhető el a [GitHub](https://github.com/Azure/WALinuxAgent), de nem ajánlott, hogy ott telepítése.<br/><br/> Amikor az ügynököt frissít, ellenőrizze, nincs biztonsági mentési műveletek futnak, és a bináris fájlok frissítése.


### <a name="establish-network-connectivity"></a>Hálózati kapcsolatok létrehozása

A biztonsági mentési bővítményt a virtuális gépen az Azure nyilvános IP-címek kimenő hozzá kell férnie.

Általában nem kell explicit módon engedélyezi kimenő hálózati hozzáférés egy Azure virtuális gép, így az képes legyen kommunikálni az Azure Backup.
Ha a virtuális gépek nem tud kapcsolódni, és a következő hibát látja **ExtensionSnapshotFailedNoNetwork**, explicit módon kell engedélyezi a hozzáférést. A biztonsági mentési bővítményt és kommunikálhat az Azure nyilvános IP-címeket, a biztonsági mentések forgalmától.


#### <a name="explicitly-allow-outbound-access"></a>Explicit módon engedélyezi a kimenő hozzáférést

Ha egy virtuális gép nem tud csatlakozni a Backup szolgáltatás, explicit módon engedélyezi a kimenő hozzáférést a táblázatban összefoglalt módszerek egyikének használatával.

**Beállítás** | **Műveletek** | **Részletek** 
--- | --- | --- 
**Az NSG-szabályok beállítása** | Lehetővé teszi a [Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653). | Lehetővé teszi, és minden címtartományt kezelése, helyett egy hálózati biztonsági csoport (NSG) szabályt, amely lehetővé teszi a hozzáférést az Azure Backup szolgáltatás használatával adhat hozzá egy [szolgáltatáscímke](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). [További információk](../virtual-network/security-overview.md#service-tags).<br/><br/> Nincsenek további költségek nélkül.<br/><br/> Szabályok egyszerűek szolgáltatáscímkék való kezeléséhez.
**A proxy üzembe helyezése** | A forgalom útválasztási HTTP-proxy kiszolgáló telepítése. | Ez a módszer az Azure és a tároló nem csak a teljes hozzáférést biztosít.<br/><br/> Szabályozható a tároló URL-címek használata engedélyezett.<br/><br/> Nincs a virtuális gépek hozzáférést egy internet egyetlen pont.<br/><br/> Nincsenek további költségek proxy.
**Az Azure-tűzfal beállítása** | Lehetővé teszi a forgalom Azure-tűzfalon keresztül a virtuális gépen, egy teljesen minősített tartománynév-címke az Azure Backup szolgáltatás használatával. |  Ez a módszer könnyen használható, ha Azure tűzfal állítsa be, egy virtuális hálózat alhálózatán.<br/><br/> Nem a saját FQDN-címkék létrehozása, és teljes tartománynevek módosíthatja a címke.<br/><br/> Az Azure Managed Disks szolgáltatást, ha szüksége lehet egy további port megnyitása (port 8443) a tűzfalat.

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

### <a name="allow-firewall-access-by-using-an-fqdn-tag"></a>Tűzfal hozzáférés engedélyezése egy teljesen minősített tartománynév-címke használatával

Azure-tűzfal engedélyezi a kimenő hozzáférést a hálózati forgalmat az Azure Backup szolgáltatásban is beállítása.

- [Ismerje meg](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) üzembe helyezése az Azure-tűzfal.
- [További információ](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN címkék.

## <a name="modify-storage-replication-settings"></a>A tárolóreplikálási beállítások módosítása

Alapértelmezés szerint a tároló rendelkezik [georedundáns tárolás (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). Az elsődleges biztonsági mentési GRS ajánlott. Használhat [helyileg redundáns tárolás (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) egy olcsóbb számára.

Módosítsa a következőképpen tárolóreplikáció típusa:

1. A portálon válassza ki az új tároló. A **beállítások**válassza **tulajdonságok**.
2. A **tulajdonságok**alatt **biztonsági mentés konfigurációja**válassza **frissítés**.
3. Válassza ki a tárolóreplikáció típusa, és válassza ki **mentése**.

![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/full-blade.png)


## <a name="configure-a-backup-policy"></a>Biztonsági mentési szabályzat konfigurálása

Az előfizetésben található virtuális gépek felderítéséhez és a biztonsági mentés konfigurálása.

1. A tárolóban > **áttekintése**válassza **+ biztonsági mentés**.

   ![Biztonsági mentés gomb](./media/backup-azure-arm-vms-prepare/backup-button.png)

   A **Backup** és **biztonsági mentés célja** ablaktábla megnyitásához.

2. A **biztonsági mentés célja** > **a számítási feladat futtató?** válassza **Azure**. A **miről szeretne biztonsági másolatot készíteni?** válassza **virtuális gép** >  **OK**.

   ![Biztonsági mentés és a biztonsági mentés célja panel](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Ebben a lépésben a Virtuálisgép-bővítmény regisztrálja a tárolóban. A **biztonsági mentés célja** panel bezárul, és a **biztonsági mentési szabályzat** panel nyílik meg.

3. A **biztonsági mentési szabályzat**, válassza ki a tárolóval társítani kívánt házirendet. Ezután kattintson az **OK** gombra.
    - Az alapértelmezett házirend részletei megtalálhatók a legördülő menüben.
    - Válassza ki **hozzon létre új** szabályzat létrehozásához. [További](backup-azure-arm-vms-prepare.md#configure-a-backup-policy) kapcsolatos házirend meghatározása.

    !["Mentés" és "Biztonsági mentési szabályzat" panel](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. Az a **válassza ki a virtuális gépek** ablaktáblán válassza ki a virtuális gépeket, amelyet használni fog a megadott biztonsági mentési szabályzat > **OK**.

   A kiválasztott virtuális gép érvényesítve lesz. Választhat a virtuális gépek és a tárolónak ugyanabban a régióban. Virtuális gépek csak biztonsági mentésre alkalmas Ezenkívül egyetlen tárban.

   !["A virtuális gépek kiválasztása" panelen](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. A **biztonsági mentési**válassza **biztonsági mentés engedélyezése**.

   Ez a lépés telepíti a szabályzatot a tárolóban, és a virtuális gépeket. A biztonsági mentési bővítményt a Virtuálisgép-ügynök az Azure virtuális gépen futó a is telepíti.
   
   Ebben a lépésben a virtuális gép nem hozza létre a kezdeti helyreállítási pontot.

   !["A biztonsági mentés engedélyezése" gombra](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Miután engedélyezte a biztonsági mentés:

- Egy kezdeti biztonsági mentés a biztonsági mentés ütemezése szerint fut.
- A Backup szolgáltatás telepíti a biztonsági mentési bővítményt, a virtuális gép fut-e.

Egy futó virtuális gép adja a legnagyobb esélyt egy alkalmazással konzisztens helyreállítási pont létrehozásának. Azonban a virtuális gép biztonsági másolat még akkor is, ha ki van kapcsolva, és a bővítményt nem lehet telepíteni. A kapcsolat nélküli virtuális gépként ismert. Ebben az esetben a helyreállítási pont összeomlás-konzisztens lesz.
    
> [!NOTE]
> Az Azure Backup nem támogatja az Azure virtuális gép biztonsági mentéseinek a nyári időszámítás – mentés módosítása a óra automatikus illesztését. Biztonsági mentési szabályzatok manuálisan szükség szerint módosítsa.

## <a name="run-the-initial-backup"></a>A kezdeti biztonsági mentés futtatása

A kezdeti biztonsági mentés beállított ütemezés fog futni, kivéve, ha manuálisan futtatja, azonnal. Futtassa manuálisan az alábbiak szerint:

1. Válassza ki a tároló menüjében **biztonsági mentési elemek**.
2. A **biztonsági másolati elemek**válassza **Azure virtuális gép**.
3. Az a **biztonsági másolati elemek** listájához, válassza ki a három pontra (**...** ).
4. Válassza ki **biztonsági mentés**.
5. A **biztonsági mentés**, használja a naptárvezérlőt annak, hogy megőrződjön-e a helyreállítási pont kiválasztására > **OK**.
6. A portál feladatértesítések figyelésére. Figyelheti a feladat előrehaladását a tároló irányítópultján > **biztonsági mentési feladatok** > **folyamatban**. A virtuális gép méretétől függően a kezdeti biztonsági mentés létrehozása eltarthat egy ideig.



## <a name="next-steps"></a>További lépések

- Az esetleges problémák megoldásához [Azure Virtuálisgép-ügynökökkel](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) vagy [Azure VM backup](backup-azure-vms-troubleshoot.md).
- [Visszaállítás](backup-azure-arm-restore-vms.md) Azure virtuális gépek.

