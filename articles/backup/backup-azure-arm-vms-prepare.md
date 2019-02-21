---
title: Azure virtuális gépek biztonsági mentése a Recovery Services-tárolót az Azure Backup szolgáltatással
description: Ismerteti, hogyan lehet Azure virtuális gépek biztonsági mentése a Recovery Services-tárolót az Azure Backup szolgáltatással
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: e782afb971f95a654119d9817edeef02642bee9e
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447565"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Azure virtuális gépek biztonsági mentése Recovery Services-tárolóban

Ez a cikk bemutatja, hogyan készíthet biztonsági másolatot az Azure-beli Virtuálisgép- [Azure Backup](backup-overview.md) helyezheti üzembe, és amely lehetővé teszi a biztonsági mentés a Recovery Services-tároló. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Ellenőrizze a támogatott forgatókönyveket és előfeltételek.
> * Az Azure virtuális gépek előkészítése. Az Azure-beli Virtuálisgép-ügynök szükség esetén telepítse, és a virtuális gépek kimenő hozzáférésének ellenőrzése.
> * Hozzon létre egy tárolót.
> * A tároló tárolás beállítása
> * Virtuális gépek felderítéséhez, a biztonsági mentési beállításokat és a szabályzat.
> * Az Azure virtuális gépek biztonsági mentésének engedélyezése


> [!NOTE]
   > Ez a cikk ismerteti az Azure virtuális gépek biztonsági mentése egy tároló beállításával, és válassza a virtuális gépek biztonsági mentése. Ez hasznos, ha azt szeretné, hogy több virtuális gépek biztonsági mentése. Emellett [biztonsági mentése Azure virtuális gép](backup-azure-vms-first-look-arm.md) közvetlenül a a virtuális gép beállításait.

## <a name="before-you-start"></a>Előkészületek

Az Azure Backup biztonsági másolatot készít az Azure virtuális gépek a gépen futó Azure-beli Virtuálisgép-ügynök bővítmény telepítésével.

1. [Felülvizsgálat](backup-architecture.md#architecture-direct-backup-of-azure-vms) Azure virtuális gép biztonsági mentési architektúra.
[Ismerje meg](backup-azure-vms-introduction.md) Azure virtuális gépek biztonsági mentését, és a biztonsági mentési bővítményt.
2. [Tekintse át a támogatási mátrix](backup-support-matrix-iaas.md) Azure VM backup esetében.
3. Az Azure virtuális gépek előkészítése. Virtuálisgép-ügynök telepítése, ha nincs telepítve, és a készíteni kívánt virtuális gépek kimenő hozzáférésének ellenőrzése.


## <a name="prepare-azure-vms"></a>Az Azure virtuális gépek előkészítése

Szükség esetén Virtuálisgép-ügynök telepítése, és a virtuális gépek kimenő hozzáférésének ellenőrzése.

### <a name="install-the-vm-agent"></a>Virtuálisgép-ügynök telepítése 
Szükség esetén telepítse az ügynököt a következőképpen.

**VM** | **Részletek**
--- | ---
**Windows rendszerű virtuális gépek** | [Töltse le és telepítse](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) az ügynök MSI-fájlt. Telepítse a rendszergazdai engedélyekkel a gépen.<br/><br/> A telepítés ellenőrzése a *C:\WindowsAzure\Packages* a virtuális gépen, kattintson a jobb gombbal a WaAppAgent.exe > **tulajdonságok**, > **részletei** fülre. **Termékverzió** kell 2.6.1198.718 vagy újabb verziója.<br/><br/> Amikor az ügynököt frissít, ellenőrizze, hogy nincs biztonsági mentési műveletek futnak, és [telepítse újra az ügynököt](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux rendszerű virtuális gépek** | A terjesztési csomag adattárból az RPM- vagy DEB-csomag telepítését a telepítése és frissítése az Azure Linux-ügynök előnyben részesített módszer. Az összes a [terjesztési szolgáltatók által támogatott](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrálhatja az Azure-beli Linuxos ügynök csomag adattárak, valamint a lemezképek. Az ügynök nem érhető el a [GitHub](https://github.com/Azure/WALinuxAgent), de nem ajánlott, hogy ott telepítése.<br/><br/> Amikor az ügynököt frissít, ellenőrizze, nincs biztonsági mentési művelet van folyamatban, és a bináris fájlok frissítése. 


### <a name="establish-network-connectivity"></a>Hálózati kapcsolatok létrehozása

A biztonsági mentési bővítményt a virtuális gépen az Azure nyilvános IP-címek kimenő hozzáféréssel kell rendelkeznie.

- Nincs explicit kimenő hálózati hozzáférés nem Azure-beli virtuális gép Azure Backup szolgáltatással folytatott kommunikációhoz szükséges.
- Azonban egyes régebbi virtuális gépek között problémák és a hiba miatt sikertelen **ExtensionSnapshotFailedNoNetwork** csatlakozásra tett kísérlet közben. Ebben az esetben használja az alábbi lehetőségek közül, hogy a biztonsági mentési bővítményt kommunikálhassanak az Azure nyilvános IP-címeket, a biztonsági mentések forgalmától.

   **Beállítás** | **a művelet** ** | **Előnyök** | **Disadvantages**
   --- | --- | --- | ---
   **Az NSG-szabályok beállítása** | Lehetővé teszi a [Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/>  Hozzáadhat egy szabályt, amely engedélyezi a hozzáférést az Azure Backup szolgáltatás használata egy [szolgáltatáscímke](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure), külön-külön lehetővé téve, és minden címtartományt kezelése helyett. [További](../virtual-network/security-overview.md#service-tags) szolgáltatáscímkék kapcsolatban. | További költségek nélkül. Egyszerű szolgáltatás címkék kezelése
   **A proxy üzembe helyezése** | A forgalom útválasztási HTTP-proxy kiszolgáló telepítése. | Az Azure és a tároló nem csak a teljes hozzáférést biztosít. Szabályozható a tároló URL-címek használata engedélyezett.<br/><br/> A virtuális gépek internet egyetlen pont hozzáférést.<br/><br/> Proxy további költségekkel.<br/><br/> 
   **Az Azure-tűzfal beállítása** | Az Azure-tűzfalon keresztül a forgalom engedélyezése a virtuális gép, egy teljesen minősített tartománynév-címke az Azure Backup szolgáltatás használatával.|  Könnyen használható, ha a virtuális hálózat alhálózatán beállítása Azure-tűzfal | Nem hozható létre a saját teljesen minősített Tartományneve címkéket, vagy teljes tartománynevek módosíthatja a címke.<br/><br/> Az Azure Managed Disks szolgáltatást, ha szüksége lehet egy további port megnyitása (port 8443) a tűzfalat.

#### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Az Azure kimenő hozzáférésének engedélyezéséhez egy NSG-szabály beállítása

Ha az Azure virtuális gép kezeli az NSG-KET hozzáférése van, engedélyezi a kimenő hozzáférést a szükséges tartományok és a portok a biztonságimásolat-tároláshoz.

1. A virtuális gép > **hálózatkezelés**, kattintson a **porton keresztüli kimenő szabály felvétele**.
2. A **kimenő biztonsági szabály felvétele**, kattintson a **speciális**.
3. A **forrás**válassza **VirtualNetwork**.
4. A **alkalmazásport-tartományok forrás**, írjon be egy csillag (*) bármely porton a kimenő hozzáférésének engedélyezéséhez.
5. A **cél**válassza **Szolgáltatáscímke**. Válassza ki a listából **Storage.region**. A régióban, a régiót, amelyben a tároló és a virtuális gépek biztonsági mentése, találhatók.
6. A **Célporttartomány**, válassza ki azt a portot.
    - Nem felügyelt virtuális gép nem titkosított storage-fiók: 80
    - Nem felügyelt virtuális gép titkosított storage-fiók: 443-as (alapértelmezett beállítás)
    - A felügyelt virtuális gép: 8443.
7. A **protokoll**válassza **TCP**.
8. A **prioritású**, adja meg a prioritás értéke kisebb, mint minden újabb megtagadási szabályoknak. Ha egy szabály megtagadja a hozzáférést, az új engedélyezése a szabály magasabbnak kell lennie. Ha például van egy **Deny_All** prioritással 1000, az új szabályra szabálykészlet 1000-nél kisebb értékre kell állítani.
9. Adjon meg egy nevet és leírást a szabályhoz, és kattintson a **OK**.

Az NSG-szabály több virtuális gép kimenő hozzáférésének engedélyezéséhez alkalmazhat.

Ez a videó végigvezeti a folyamatot.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


#### <a name="route-backup-traffic-through-a-proxy"></a>Biztonsági mentési forgalomirányítást proxyn keresztül

Irányíthatja a biztonsági mentési forgalmat egy proxyn keresztül, és ezután a proxy hozzáférést biztosít a szükséges Azure-tartományokat.
Konfigurálnia kell a virtuális Gépen a következő proxy:

- Az Azure virtuális gép kell irányítani a proxyn keresztül a nyilvános internetes kötött összes HTTP-forgalmat.
- A proxy kell engedélyezik a bejövő forgalmat a virtuális gépek a megfelelő virtuális hálózaton (VNet).
- Az NSG-t **Elégtelen-zárolási** szüksége van egy szabályt, amely lehetővé teszi, hogy a kimenő internetes forgalmat a virtuális gép érkezett.

##### <a name="set-up-the-proxy"></a>A proxy beállítása.
Ha nem rendelkezik a system fiók proxy, egyet az alábbiak szerint állíthatja:

1. Töltse le [PsExec](https://technet.microsoft.com/sysinternals/bb897553).

2. Futtatás **PsExec.exe -i -s cmd.exe** a parancssor a rendszerfiók alatt való futtatásához.
3. A böngészőben a rendszerkörnyezetben fut. Például: **%PROGRAMFILES%\Internet Explorer\iexplore.exe** az Internet Explorerben.  
4. A proxy-beállítások megadása.
    - Linuxos gépeken:
        - Adja hozzá a sort, hogy a **/etc/környezet** fájlt:
            - **http_proxy =http://proxy IP-cím: proxy portja**
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

##### <a name="allow-incoming-connections-on-the-proxy"></a>A proxy bejövő kapcsolatok engedélyezése

A bejövő kapcsolatok engedélyezése a proxybeállításokat.

- Nyissa meg például a **fokozott biztonságú Windows tűzfal**.
    - Kattintson a jobb gombbal **bejövő szabályok** > **új szabály**.
    - A **szabálytípus** kiválasztása **egyéni** > **tovább**.
    - A **Program**válassza **minden program** > **tovább**.
    - A **protokollok és portok** a típusa **TCP**, **helyi portok** való **adott**, és **távoli port**való **minden port**.
    - Fejezze be a varázslót, és adja meg a szabály nevét.

##### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Vegyen fel egy kivételt az NSG-hez a proxy

Az NSG-t a **Elégtelen-zárolási**, bármely internetes címre a 80-as (HTTP) vagy a 443-as (HTTPS) porton a 10.0.0.5 bármely porton érkező adatforgalom engedélyezéséhez.

- A következő PowerShell-parancsfájl egy példát biztosít forgalom engedélyezéséhez.
- Ne hagyja a nyilvános internet-címeire irányuló kimenő, adjon meg egy IP-címtartomány (-DestinationPortRange), vagy használja a storage.region szolgáltatáscímke.   

    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```

### <a name="allow-firewall-access-with-fqdn-tag"></a>A teljes tartománynév-címke tűzfal hozzáférés engedélyezése

Az Azure-tűzfal engedélyezi a kimenő hozzáférést a hálózati forgalmat az Azure Backup szolgáltatásban is beállítása.

- [Ismerje meg](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) üzembe helyezése az Azure-tűzfal.
- [További információ](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN címkék.

## <a name="create-a-vault"></a>Tároló létrehozása

A tároló tárolja a biztonsági másolatok és az idők során létrehozott helyreállítási pontokat, és tárolja a biztonsági másolat gépek társított biztonsági mentési szabályzatok. Hozzon létre egy tárolót a következőképpen:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Az a **Hub** menüjében válassza **Tallózás**, és írja be **Recovery Services**. Válassza ki **Recovery Services-tárolók**.

    ![A mezőbe, majd válassza a "Helyreállítási tárak" a eredmények](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. Az a **Recovery Services-tárolók** menüjében válassza **Hozzáadás**.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    !["A recovery Services-tárolók" panel](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. A **Recovery Services-tárolók** >  **neve**, adjon meg egy, a tárolót azonosító rövid nevet.
    - A névnek egyedinek kell lennie az Azure-előfizetéshez.
    - 2 – 50 karaktert tartalmazhat.
    - Betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.
5. Válassza ki **előfizetés** az elérhető előfizetések listájának megtekintéséhez. Ha nem biztos abban, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (vagy javasolt) előfizetést. Nincs több lehetőség csak akkor, ha a munkahelyi vagy iskolai fiók több Azure-előfizetéssel társítva.
6. Válassza ki **erőforráscsoport** az elérhető erőforráscsoportok listájának megtekintéséhez, vagy válasszon **új** egy új erőforráscsoport létrehozásához. [További](../azure-resource-manager/resource-group-overview.md) erőforráscsoportokkal kapcsolatos.
7. Válassza ki **hely** a tárolóhoz tartozó földrajzi régió kiválasztásához. A tároló *kell* kell a virtuális gépek biztonsági mentéséhez használni kívánt ugyanabban a régióban.
8. Kattintson a **Létrehozás** gombra.
    - A tároló létrehozása egy ideig is eltarthat.
    - Figyelje a jobb felső területén, a portál.
    ![A Backup-tárolók listája](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listája. Ha nem látja a tárolót, válassza ki a **frissítése**.

## <a name="set-up-storage-replication"></a>Tárreplikáció beállítása

Alapértelmezés szerint a tároló rendelkezik [georedundáns tárolás (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). Az elsődleges biztonsági mentési GRS ajánlott, de használhatja[helyileg redundáns tárolás](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) egy olcsóbb számára.

Tárreplikáció módosítsa a következőképpen:

1. A tárolóban > **biztonsági mentési infrastruktúra**, kattintson a **biztonsági mentési konfiguráció**

   ![A Backup-tárolók listája](./media/backup-azure-arm-vms-prepare/full-blade.png)

2. A **biztonsági mentés konfigurációja**, kötelező, és válassza ki a storage redundancia módját módosítani **mentése**.


## <a name="configure-a-backup-policy"></a>Biztonsági mentési szabályzat konfigurálása

Az előfizetésben található virtuális gépek felderítéséhez és a biztonsági mentés konfigurálása.

1. A tárolóban > **áttekintése**, kattintson a **+ biztonsági mentés**

   ![Biztonsági mentés gomb](./media/backup-azure-arm-vms-prepare/backup-button.png)

   A **Backup** és **biztonsági mentés célja** ablaktábla megnyitásához.

2. A **biztonsági mentés célja**> **a számítási feladat futtató?** válassza **Azure**. A **miről szeretne biztonsági másolatot készíteni?** válassza **virtuális gép** >  **OK**. Ez regisztrálja a Virtuálisgép-bővítményt a tárolóban.

   ![Biztonsági mentés és a biztonsági mentés célja panel](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Ebben a lépésben a Virtuálisgép-bővítmény regisztrálja a tárolóban. A **biztonsági mentés célja** panel bezárul, és a **biztonsági mentési szabályzat** panel nyílik meg.

3. A **biztonsági mentési szabályzat**, válassza ki a tárolóval társítani kívánt házirendet. Ezután kattintson az **OK** gombra.
    - Az alapértelmezett házirend részletei megtalálhatók a legördülő menüben.
    - Kattintson a **hozzon létre új** szabályzat létrehozásához. [További](backup-azure-arm-vms-prepare.md#configure-a-backup-policy) kapcsolatos házirend meghatározása.
    

    !["Mentés" és "Biztonsági mentési szabályzat" panel](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. A **válassza ki a virtuális gépek** ablaktáblán válassza ki a virtuális gépeket, amelyet használni fog a megadott biztonsági mentési szabályzat > **OK**.

    - A kiválasztott virtuális gép érvényesítve lesz.
    - Választhat a virtuális gépek és a tárolónak ugyanabban a régióban. Virtuális gépek csak biztonsági mentésre alkalmas Ezenkívül egyetlen tárban.

   !["A virtuális gépek kiválasztása" panelen](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. A **biztonsági mentési**válassza **biztonsági mentés engedélyezése**.

   - Telepítheti a szabályzatot a tárolóban, és a virtuális gépeket, és telepíti a biztonsági mentési bővítményt a Virtuálisgép-ügynök az Azure virtuális gépen.
   - Ebben a lépésben a virtuális gép nem hozza létre a kezdeti helyreállítási pontot.

   !["A biztonsági mentés engedélyezése" gombra](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Biztonsági mentés engedélyezése után:

- Egy kezdeti biztonsági mentés a biztonsági mentés ütemezése szerint fut.
- A Backup szolgáltatás telepíti a biztonsági mentési bővítményt, a virtuális gép fut-e.
    - Egy futó virtuális gép adja a legnagyobb esélyt egy alkalmazással konzisztens helyreállítási pont létrehozásának.
    -  Azonban a virtuális gép biztonsági másolat még akkor is, ha ki van kapcsolva, és a bővítményt nem lehet telepíteni. Ez az úgynevezett *offline virtuális gép*. Ebben az esetben a helyreállítási pont az *összeomláshoz igazodik* lesz.
    Vegye figyelembe, hogy az Azure Backup nem támogatja az óra automatikus illesztését nyári időszámítás – mentés módosításait az Azure virtuális gép biztonsági mentéseinek. Biztonsági mentési szabályzatok manuálisan szükség szerint módosítsa.
  
 ## <a name="run-the-initial-backup"></a>A kezdeti biztonsági mentés futtatása

A kezdeti biztonsági mentés beállított ütemezés fog futni, kivéve, ha manuálisan futtatja, azonnal. Futtassa manuálisan az alábbiak szerint:

1. A tároló menüjében kattintson **biztonsági mentési elemek**.
2. A **biztonsági másolati elemek** kattintson **Azure virtuális gép**.
3. Az a **biztonsági másolati elemek** listában, kattintson a három pontra **...** .
4. Kattintson a **biztonsági mentés**.
5. A **biztonsági mentés**, használja a naptárvezérlőt annak, hogy megőrződjön-e a helyreállítási pont kiválasztására > **OK**.
6. A portál feladatértesítések figyelésére. Figyelheti a feladat előrehaladását a tároló irányítópultján > **biztonsági mentési feladatok** > **folyamatban**. A virtuális gép méretétől függően a kezdeti biztonsági mentés létrehozása hosszabb időt vehet igénybe.



## <a name="next-steps"></a>További lépések

- Az esetleges problémák megoldásához a [Azure Virtuálisgép-ügynökökkel](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) vagy [Azure VM backup](backup-azure-vms-troubleshoot.md).
- [Azure-beli virtuális gépek biztonsági mentése](backup-azure-vms-first-look-arm.md)
