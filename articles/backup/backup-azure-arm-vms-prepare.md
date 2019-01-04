---
title: Azure-beli virtuális gépek az Azure Backup szolgáltatással visszaállításának előkészítése
description: Ismerteti, hogyan készíti elő az Azure virtuális gépek biztonsági mentése az Azure Backup szolgáltatással a
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: raynew
ms.openlocfilehash: ee7a9c407a26f9334a854c98793db8fc01244e2a
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994674"
---
# <a name="prepare-to-back-up-azure-vms"></a>Felkészülés az Azure virtuális gépek biztonsági mentése

Ez a cikk bemutatja, hogyan készíti elő a biztonsági mentése az Azure virtuális gépet egy [Azure Backup](backup-introduction-to-azure-backup.md) Recovery Services-tároló. Felkészítése biztonsági mentésre tartalmazza:


> [!div class="checklist"]
> * A Kezdés előtt tekintse át a támogatott forgatókönyvekkel és korlátozásokkal.
> * Ellenőrizze az előfeltételeket, beleértve az Azure virtuális gépekre vonatkozó és a hálózati kapcsolatot.
> * Hozzon létre egy tárolót.
> * Válassza ki, hogyan tárolás replikálja.
> * Virtuális gépek felderítéséhez, a biztonsági mentési beállításokat és a szabályzat.
> * A kiválasztott virtuális gépek biztonsági mentésének engedélyezése


> [!NOTE]
   > Ez a cikk ismerteti az Azure virtuális gépek biztonsági mentése egy tároló beállításával, és válassza a virtuális gépek biztonsági mentése. Ez hasznos, ha azt szeretné, hogy több virtuális gépek biztonsági mentése. Is készíthető egy Azure virtuális gép közvetlenül a virtuális gép beállításait. [További információ](backup-azure-vms-first-look-arm.md)

## <a name="before-you-start"></a>Előkészületek

1. [Áttekintés](backup-azure-vms-introduction.md) az Azure Backup szolgáltatás az Azure virtuális gépek.
2. Tekintse át a támogatási részletek és az alábbi korlátozások.

   **Támogatási/korlátozás** | **Részletek**
   --- | ---
   **Windows operációs rendszer** | A Windows Server 2008 R2 64 bites vagy újabb verziója.<br/><br/> Windows-ügyfél 7 64-bit-es vagy újabb verziója.
   **Linux operációs rendszer** | Készíthet biztonsági mentést 64-bit-es Linux-disztribúciók [Azure által támogatott](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), a CoreOS Linux kivételével.<br/><br/> Tekintse át a Linux operációs rendszerek, amelyek [támogatja a fájl visszaállítása](backup-azure-restore-files-from-vm.md#for-linux-os).<br/><br/> Más Linux-disztribúciók működnek, mindaddig, amíg a Virtuálisgép-ügynök nem érhető el a virtuális gépen, és támogatja a pythont. Ezek a disztribúciók azonban nem támogatottak.
   **Régió** | Biztonsági másolatot készíthet az Azure virtuális gépek összes [támogatott régiók](https://azure.microsoft.com/regions/#services). A régió nem támogatott, ha nem tudja választani a tároló létrehozásakor.<br/><br/> Biztonsági és visszaállítása az Azure-régióban. Csak belül egy adott régióban.
   **Adatkorlát lemez** | Több mint 16 adatlemezekkel rendelkező virtuális gépek nem készíthető.
   **Megosztott tároló** | Nem ajánlott virtuális gépek biztonsági mentésének CSV vagy a kibővített fájlkiszolgáló használatával. Fürt megosztott kötetei szolgáltatás író eséllyel lesz sikertelen.
   **Linux-titkosítás** | A Linux egyesített kulcs beállítása (LUKS) titkosított Linux rendszerű virtuális gépek biztonsági mentésének nem támogatott.
   **Virtuális gép konzisztencia** | Az Azure Backup nem támogatja a több virtuális gépre kiterjedő konzisztencia.
   **Hálózat** | Biztonsági másolatba mentett adatok nem tartalmazza a virtuális Géphez csatolt csatlakoztatott hálózati meghajtókat.<br/><br/>
   **A pillanatképek** | Pillanatképek készítése write accelerator-kompatibilis lemezen nem támogatott. Az Azure Backup egy alkalmazáskonzisztens pillanatképet az összes Virtuálisgép-lemezek felvételt blokkol.
   **PowerShell** | Számos elérhető műveleteket, csak a PowerShell-lel:<br/><br/> – Belső és a külső terheléselosztók által, vagy több fenntartott IP-címek vagy adapter virtuális gépek visszaállítása kezeli. [További információ](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)<br/><br/> – Egy több tartományvezérlő konfigurációja a tartományvezérlő virtuális gép visszaállítása. [További információk](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
   **Rendszer pontos ideje** | Az Azure Backup nem támogatja az Azure virtuális gép biztonsági mentéseinek a nyári időszámítás – mentés módosítása a óra automatikus illesztését. Biztonsági mentési szabályzatok manuálisan szükség szerint módosítsa.
   **Tárfiókok** | Ha egy korlátozott hálózati tárfiókot használ, győződjön meg róla, hogy engedélyezze **engedélyezése megbízható Microsoft-szolgáltatások a tárfiók** úgy, hogy az Azure Backup szolgáltatás hozzáfér a fiókot. Az elemszintű helyreállítás nem támogatott, korlátozott hálózati tárfiókok esetében.<br/><br/> A storage-fiókban, ellenőrizze, hogy **tűzfalak és virtuális hálózatok** beállítások engedélyezze a hozzáférést **minden hálózatból elérhető**.


## <a name="prerequisites"></a>Előfeltételek

- A tároló az Azure virtuális gépek biztonsági mentése szeretné ugyanabban a régióban kell létrehoznia.
- Ellenőrizze az Azure-beli Virtuálisgép-régiók, megkezdése előtt.
    - Ha több régióban rendelkezik virtuális gépeket, minden régióban hozzon létre egy tárolót.
    - Nem kell megadnia a storage-fiókok a biztonsági mentési adatok tárolására. A tároló és az Azure Backup szolgáltatás, amely automatikusan kezeli.
- Győződjön meg arról, hogy a Virtuálisgép-ügynök telepítve van-e az Azure virtuális gépekhez, amelyet szeretne készíteni.



### <a name="install-the-vm-agent"></a>Virtuálisgép-ügynök telepítése

Ahhoz, hogy a biztonsági mentés, Azure Backup telepítése (a virtuális gép pillanatképét vagy pillanatkép Linux virtuális gép) egy biztonsági mentési bővítményt a Virtuálisgép-ügynök, amely az Azure virtuális gépen futtatja.
    -  Az Azure Virtuálisgép-ügynök bármely Windows virtuális gépen az Azure Marketplace-lemezképből üzembe helyezett alapértelmezés szerint telepítve van. A portal, PowerShell, CLI vagy az Azure Resource Manager-sablonok az Azure Marketplace-rendszerképpel történő telepítésekor az Azure Virtuálisgép-ügynök is telepítve van.
    - Ha a helyszíni virtuális Gépet telepített át, az ügynök nincs telepítve, és a virtuális gép biztonsági mentés engedélyezése előtt telepíteni kell.

Szükség esetén telepítse az ügynököt a következőképpen.

**VM** | **Részletek**
--- | ---
**Windows rendszerű virtuális gépek** | [Töltse le és telepítse](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) rendszergazdai jogosultságokkal rendelkezik a számítógépen az ügynök.<br/><br/> A telepítés ellenőrzése a *C:\WindowsAzure\Packages* a virtuális gépen, kattintson a jobb gombbal a WaAppAgent.exe > **tulajdonságok**, > **részletei** fülre. **Termékverzió** kell 2.6.1198.718 vagy újabb verziója.
**Linux rendszerű virtuális gépek** | A terjesztési csomag adattárból az RPM- vagy DEB-csomag telepítését a telepítése és frissítése az Azure Linux-ügynök előnyben részesített módszer. Az összes a [terjesztési szolgáltatók által támogatott](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrálhatja az Azure-beli Linuxos ügynök csomag adattárak, valamint a lemezképek. Az ügynök nem érhető el a [GitHub](https://github.com/Azure/WALinuxAgent), de nem ajánlott, hogy ott telepítése.
Ha rendelkezik az Azure virtuális gép biztonsági mentésével kapcsolatos problémák, a következő táblázat segítségével ellenőrizze, hogy az Azure-beli Virtuálisgép-ügynök megfelelően telepítve van a virtuális gépen. A táblázat a Virtuálisgép-ügynök további információt a Windows és Linux rendszerű virtuális gépekhez.

### <a name="establish-network-connectivity"></a>Hálózati kapcsolatok létrehozása

A biztonsági mentési bővítményt a virtuális gépen az Azure nyilvános IP-címek kimenő hozzáféréssel kell rendelkeznie. Engedélyezi a hozzáférést a következőket teheti:


- **Az NSG-szabályok**: Lehetővé teszi a [Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653). Hozzáadhat egy szabályt, amely engedélyezi a hozzáférést az Azure Backup szolgáltatás használata egy [szolgáltatáscímke](../virtual-network/security-overview.md#service-tags), külön-külön lehetővé teszi minden címtartományt, és idővel kezelésükre helyett.
- **Proxy**: A forgalom útválasztási HTTP-proxy kiszolgáló telepítése.
- **Az Azure tűzfal**: Az Azure-tűzfalon keresztül a forgalom engedélyezése a virtuális gép, egy teljesen minősített tartománynév-címke az Azure Backup szolgáltatás használatával.

Beállítások közötti meghatározásakor vegye figyelembe a kompromisszumot.

**Beállítás** | **Előnyök** | **Disadvantages**
--- | --- | ---
**NSG-T** | További költségek nélkül. Egyszerű szolgáltatás címkék kezelése | Az Azure és a tároló nem csak a teljes hozzáférést biztosít. |
**A HTTP-proxy** | Szabályozható a tároló URL-címek használata engedélyezett.<br/><br/> A virtuális gépek internet egyetlen pont hozzáférést.<br/><br/> Proxy további költségekkel.
**Teljes tartománynév-címke** | Könnyen használható, ha a virtuális hálózat alhálózatán beállítása Azure-tűzfal | Nem hozható létre a saját teljesen minősített Tartományneve címkéket, vagy teljes tartománynevek módosíthatja a címke.



Az Azure Managed Disks szolgáltatást, ha szüksége lehet egy további port megnyitása (port 8443) a tűzfalat.



### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Az Azure kimenő hozzáférésének engedélyezéséhez egy NSG-szabály beállítása

Ha az Azure virtuális gép kezeli az NSG-KET hozzáférése van, engedélyezi a kimenő hozzáférést a szükséges tartományok és a portok a biztonságimásolat-tároláshoz.



1. A virtuális gép > **hálózatkezelés**, kattintson a **porton keresztüli kimenő szabály felvétele**.
- Ha egy szabály megtagadja a hozzáférést, az új engedélyezése a szabály magasabbnak kell lennie. Ha például van egy **Deny_All** prioritással 1000, az új szabályra szabálykészlet 1000-nél kisebb értékre kell állítani.
2. A **kimenő biztonsági szabály felvétele**, kattintson a **speciális**.
3. Válassza ki a forrás, **VirtualNetwork**.
4. A **alkalmazásport-tartományok forrás**, írjon be egy csillag (*) bármely porton a kimenő hozzáférésének engedélyezéséhez.
5. A **cél**válassza **Szolgáltatáscímke**. A listából válassza ki a tároló. <region>. A régióban, a régiót, amelyben a tároló és a virtuális gépek biztonsági mentése, találhatók.
6. A **Célporttartomány**, válassza ki azt a portot.

    - Virtuális gép nem felügyelt lemezek és titkosítatlan tárfiókba: 80
    - Virtuális gép a nem felügyelt lemezek titkosított tárfiókban: 443-as (alapértelmezett beállítás)
    - A felügyelt virtuális gép: 8443.
1. A **protokoll**válassza **TCP**.
2. A **prioritású**, adjon meg egy prioritási érték kisebb, mint minden újabb megtagadási szabályoknak.
3. Adjon meg egy nevet és leírást a szabályhoz, és kattintson a **OK**.

Az NSG-szabály engedélyezi a kimenő hozzáférést az Azure-bA az Azure Backup szolgáltatáshoz a több virtuális gép alkalmazhat.

Ez a videó végigvezeti a folyamatot.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]



### <a name="route-backup-traffic-through-a-proxy"></a>Biztonsági mentési forgalomirányítást proxyn keresztül

Irányíthatja a biztonsági mentési forgalmat egy proxyn keresztül, és ezután a proxy hozzáférést biztosít a szükséges Azure-tartományokat.

Konfigurálnia kell a virtuális Gépen a következő proxy:

- Az Azure virtuális gép kell irányítani a proxyn keresztül a nyilvános internetes kötött összes HTTP-forgalmat.
- A proxy kell engedélyezik a bejövő forgalmat a virtuális gépek a megfelelő virtuális hálózaton (VNet).
- Az NSG-t **Elégtelen-zárolási** szüksége van egy szabályt, amely lehetővé teszi, hogy a kimenő internetes forgalmat a virtuális gép érkezett.

Itt látható, hogyan kell beállítani a proxyt. Példaértékek használjuk. Azok a saját kell cserélni.

#### <a name="set-up-a-system-account-proxy"></a>A system fiók proxy beállítása.
Ha nem rendelkezik a system fiók proxy, egyet az alábbiak szerint állíthatja:

1. Töltse le [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Futtatás **PsExec.exe -i -s cmd.exe** a parancssor a rendszerfiók alatt való futtatásához.
3. A böngészőben a rendszerkörnyezetben fut. Példa: **ProgramFiles%\Internet Explorer\iexplore.exe** az Internet Explorerben.  
4. A proxy-beállítások megadása.
    - Linuxos gépeken:
        - Adja hozzá a sort, hogy a **/etc/környezet** fájlt:
            - **http_proxy =http://proxy IP-cím: proxy portja**
        - Adja hozzá ezeket a sorokat a **/etc/waagent.conf** fájlt:
            - **HttpProxy.Host=proxy IP-cím**
            - **HttpProxy.Port=proxy port**
    - Windows gépeken, a böngésző beállításait adja meg, hogy egy proxyt kell használni. Ha proxyt a felhasználói fiók jelenleg használ, ez a szkript használatával alkalmazza a beállítást, hogy rendszer kipróbálását.
        ```
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

        ```

#### <a name="allow-incoming-connections-on-the-proxy"></a>A proxy bejövő kapcsolatok engedélyezése

1. A bejövő kapcsolatok engedélyezése a proxybeállításokat.
2. Nyissa meg például a **fokozott biztonságú Windows tűzfal**.
    - Kattintson a jobb gombbal **bejövő szabályok** > **új szabály**.
    - A **szabálytípus** kiválasztása **egyéni** > **tovább**.
    - A **Program**válassza **minden program** > **tovább**.
    - A **protokollok és portok** a típusa **TCP**, **helyi portok** való **adott**, és **távoli port**való **minden port**.
    - Fejezze be a varázslót, és adja meg a szabály nevét.

#### <a name="add-an-exception-rule-to-the-nsg"></a>Vegyen fel egy kivételt az NSG-hez

Az NSG-t a **Elégtelen-zárolási**, bármely internetes címre a 80-as (HTTP) vagy a 443-as (HTTPS) porton a 10.0.0.5 bármely porton érkező adatforgalom engedélyezéséhez.

- A következő PowerShell-parancsfájl egy példát biztosít forgalom engedélyezéséhez.
- Ne hagyja a nyilvános internet-címeire irányuló kimenő, adjon meg egy IP-címtartomány (-DestinationPortRange), vagy használja a storage.region szolgáltatáscímke.   

    ```
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```
### <a name="allow-firewall-access-with-fqdn-tag"></a>A teljes tartománynév-címke tűzfal hozzáférés engedélyezése

Az Azure-tűzfal engedélyezi a kimenő hozzáférést a hálózati forgalmat az Azure Backup szolgáltatásban is beállítása.

- [Ismerje meg](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) üzembe helyezése az Azure-tűzfal.
- [További információ](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN címkék.


## <a name="create-a-vault"></a>Tároló létrehozása

Biztonsági mentés a Recovery Services-tároló tárolja a biztonsági másolatok és az idők során létrehozott helyreállítási pontokat, és tárolja a biztonsági másolat gépek társított biztonsági mentési szabályzatok. Hozzon létre egy tárolót a következőképpen:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Az a **Hub** menüjében válassza **Tallózás**, és írja be **Recovery Services**. Ahogy elkezd gépelni, a bemeneti szűri az erőforrások listájában. Válassza ki **Recovery Services-tárolók**.

    ![A mezőbe, majd válassza a "Helyreállítási tárak" a eredmények](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    A Recovery Services-tárolók listája megjelenik.
3. Az a **Recovery Services-tárolók** menüjében válassza **Hozzáadás**.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    A **Recovery Services-tárolók** panel nyílik meg. Akkor arra kéri, hogy a **neve**, **előfizetés**, **erőforráscsoport**, és **hely**.

    !["A recovery Services-tárolók" panel](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. A **Név** mezőben adjon meg egy, a tárolót azonosító rövid nevet.
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


## <a name="configure-backup"></a>Biztonsági mentés konfigurálása

Az előfizetésben található virtuális gépek felderítéséhez és a biztonsági mentés konfigurálása.

1. A tárolóban > **áttekintése**, kattintson a **+ biztonsági mentés**

   ![Biztonsági mentés gomb](./media/backup-azure-arm-vms-prepare/backup-button.png)

   A **Backup** és **biztonsági mentés célja** ablaktábla megnyitásához.

2. A **biztonsági mentés célja**> **a számítási feladat futtató?** válassza **Azure**. A **miről szeretne biztonsági másolatot készíteni?** válassza **virtuális gép** >  **OK**. Ez regisztrálja a Virtuálisgép-bővítményt a tárolóban.

   ![Biztonsági mentés és a biztonsági mentés célja panel](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Ebben a lépésben a Virtuálisgép-bővítmény regisztrálja a tárolóban. A **biztonsági mentés célja** panel bezárul, és a **biztonsági mentési szabályzat** panel nyílik meg.

3. A **biztonsági mentési szabályzat**, válassza ki a tárolóval társítani kívánt házirendet. Ezután kattintson az **OK** gombra.
    - Az alapértelmezett házirend részletei megtalálhatók a legördülő menüben.
    - Kattintson a **hozzon létre új** szabályzat létrehozásához. [További](backup-azure-vms-first-look-arm.md#defining-a-backup-policy) kapcsolatos házirend meghatározása.

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

- A biztonsági mentési szabályzat a biztonsági mentés ütemezése szerint fut.
- A Backup szolgáltatás telepíti a biztonsági mentési bővítményt, a virtuális gép fut-e.
    - Egy futó virtuális gép adja a legnagyobb esélyt egy alkalmazással konzisztens helyreállítási pont létrehozásának.
    -  Azonban a virtuális gép biztonsági másolat még akkor is, ha ki van kapcsolva, és a bővítményt nem lehet telepíteni. Ez az úgynevezett *offline virtuális gép*. Ebben az esetben a helyreállítási pont az *összeomláshoz igazodik* lesz.
- Ha szeretne egy igény szerinti biztonsági másolat készítése a virtuális gép, azonnal a **biztonsági másolati elemek**, kattintson a virtuális gép melletti három pontra (...) > **biztonsági mentés**.


## <a name="next-steps"></a>További lépések

- Az esetleges problémák megoldásához a [Azure Virtuálisgép-ügynökökkel](/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) vagy [Azure VM backup](backup-azure-vms-troubleshoot.md).
- [Azure-beli virtuális gépek biztonsági mentése](backup-azure-vms-first-look-arm.md)
