---
title: 'Azure Backup hiba hibaelhárítása: A vendég ügynök állapota nem érhető el'
description: Az ügynökkel, bővítménnyel és lemezekkel kapcsolatos Azure Backup hibák tünetei, okai és megoldása.
ms.reviewer: saurse
author: dcurwin
manager: carmonm
keywords: Azure Backup; VM-ügynök; Hálózati kapcsolat;
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 7fc288ad9e33088b1b5248c1b61ed439ac95a9c4
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688986"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure Backup hiba hibaelhárítása: Az ügynökkel vagy bővítménnyel kapcsolatos problémák

Ez a cikk hibaelhárítási lépéseket tartalmaz, amelyek segítségével megoldhatja a virtuálisgép-ügynökkel és-bővítménysel folytatott kommunikációhoz kapcsolódó Azure Backup hibákat.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]



## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable – a virtuálisgép-ügynök nem tud kommunikálni Azure Backup

**Hibakód**: UserErrorGuestAgentStatusUnavailable <br>
**Hibaüzenet**: A virtuálisgép-ügynök nem tud kommunikálni Azure Backup<br>

Miután regisztrálta és beütemezte a virtuális gépet a biztonsági mentési szolgáltatáshoz, a biztonsági mentés a virtuálisgép-ügynökkel folytatott kommunikációt kezdeményezi, hogy egy időponthoz tartozó pillanatképet készítsen. A következő feltételek bármelyike megakadályozhatja a pillanatkép aktiválását. Ha egy pillanatkép nincs aktiválva, előfordulhat, hogy a biztonsági mentés sikertelen lesz. Hajtsa végre a következő hibaelhárítási lépéseket a felsorolt sorrendben, majd próbálja megismételni a műveletet:<br>
**1. ok: [Az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**2. ok: [A virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [Nem lehet lekérdezni a pillanatkép állapotát, vagy nem lehet pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**     
**4. ok: [A biztonsági mentési bővítmény nem tud frissíteni vagy betölteni](#the-backup-extension-fails-to-update-or-load)**  
**5. ok: [A virtuális gépnek nincs internet-hozzáférése](#the-vm-has-no-internet-access)**

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError – nem tudott kommunikálni a virtuálisgép-ügynökkel a pillanatkép állapotához

**Hibakód**: GuestAgentSnapshotTaskStatusError<br>
**Hibaüzenet**: A virtuális gép ügynökével való kommunikáció nem volt lehetséges a pillanatfelvétel állapotáról <br>

Miután regisztrálta és beütemezte a virtuális gépet a Azure Backup szolgáltatáshoz, a biztonsági mentés a virtuális gép biztonsági mentési bővítménnyel való kommunikációval kezdeményezi az adott időponthoz tartozó pillanatképet. A következő feltételek bármelyike megakadályozhatja a pillanatkép aktiválását. Ha a pillanatkép nincs aktiválva, a biztonsági mentési hiba merülhet fel. Hajtsa végre a következő hibaelhárítási lépéseket a felsorolt sorrendben, majd próbálja megismételni a műveletet:  
**1. ok: [Az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**2. ok: [A virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [A virtuális gépnek nincs internet-hozzáférése](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached – elérte a visszaállítási pont gyűjtésének maximális korlátját

**Hibakód**: UserErrorRpCollectionLimitReached <br>
**Hibaüzenet**: Elérte a visszaállítási pont gyűjtésének maximális korlátját. <br>
* Ez a probléma akkor fordulhat elő, ha a helyreállítási pont erőforráscsoport zárolása megakadályozza a helyreállítási pont automatikus törlését.
* Ez a probléma akkor is előfordulhat, ha naponta több biztonsági mentést indítanak el. Jelenleg azt javasoljuk, hogy naponta csak egy biztonsági mentést készítsen, mivel az azonnali visszaállítási pontok a beállított pillanatkép-megőrzéssel megőrzik a 1-5 napot, és csak 18 Instant RPs-t lehet egy virtuális géphez társítani egy adott időpontban. <br>

Javasolt művelet:<br>
A probléma megoldásához távolítsa el a virtuális gép erőforráscsoport zárolását, majd próbálja megismételni a műveletet a tisztítás elindításához.
> [!NOTE]
> A Backup szolgáltatás egy külön erőforráscsoportot hoz létre, mint a virtuális gép erőforráscsoport, amely a helyreállítási pontok gyűjtését tárolja. Javasoljuk, hogy ne zárolja a Backup szolgáltatás általi használatra létrehozott erőforráscsoportot. A Backup szolgáltatás által létrehozott erőforráscsoport elnevezési formátuma a következőket eredményezi: AzureBackupRG_`<Geo>`_`<number>` Eg: AzureBackupRG_northeurope_1

**1. lépés: [Zárolás eltávolítása a visszaállítási pont erőforráscsoporthoz](#remove_lock_from_the_recovery_point_resource_group)** <br>
**2. lépés: [Visszaállítási pont gyűjtésének törlése](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured – a biztonsági mentés nem rendelkezik megfelelő engedélyekkel a Key Vault számára a titkosított virtuális gépek biztonsági mentéséhez

**Hibakód**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Hibaüzenet**: A biztonsági mentés nem rendelkezik megfelelő engedélyekkel a Key Vault számára a titkosított virtuális gépek biztonsági mentéséhez. <br>

Ahhoz, hogy a biztonsági mentési művelet sikeres legyen a titkosított virtuális gépeken, rendelkeznie kell engedéllyel a kulcstartó eléréséhez. Ezt a [Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) vagy a [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection)használatával teheti meg.

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork – a pillanatkép-készítési művelet nem sikerült, mert nincs hálózati kapcsolat a virtuális gépen

**Hibakód**: ExtensionSnapshotFailedNoNetwork<br>
**Hibaüzenet**: Nem sikerült elkészíteni a pillanatképet, mert nincs hálózati kapcsolat a virtuális gépen<br>

Miután regisztrálta és beütemezte a virtuális gépet a Azure Backup szolgáltatáshoz, a biztonsági mentés a virtuális gép biztonsági mentési bővítménnyel való kommunikációval kezdeményezi az adott időponthoz tartozó pillanatképet. A következő feltételek bármelyike megakadályozhatja a pillanatkép aktiválását. Ha a pillanatkép nincs aktiválva, a biztonsági mentési hiba merülhet fel. Hajtsa végre a következő hibaelhárítási lépéseket a felsorolt sorrendben, majd próbálja megismételni a műveletet:    
**1. ok: [Nem lehet lekérdezni a pillanatkép állapotát, vagy nem lehet pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**2. ok: [A biztonsági mentési bővítmény nem tud frissíteni vagy betölteni](#the-backup-extension-fails-to-update-or-load)**  
**3. ok: [A virtuális gépnek nincs internet-hozzáférése](#the-vm-has-no-internet-access)**

## <a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks – a VMSnapshot-bővítmény művelete sikertelen volt

**Hibakód**: ExtensionOperationFailedForManagedDisks <br>
**Hibaüzenet**: A VMSnapshot bővítmény művelete sikertelen volt<br>

Miután regisztrálta és beütemezte a virtuális gépet a Azure Backup szolgáltatáshoz, a biztonsági mentés a virtuális gép biztonsági mentési bővítménnyel való kommunikációval kezdeményezi az adott időponthoz tartozó pillanatképet. A következő feltételek bármelyike megakadályozhatja a pillanatkép aktiválását. Ha a pillanatkép nincs aktiválva, a biztonsági mentési hiba merülhet fel. Hajtsa végre a következő hibaelhárítási lépéseket a felsorolt sorrendben, majd próbálja megismételni a műveletet:  
**1. ok: [Nem lehet lekérdezni a pillanatkép állapotát, vagy nem lehet pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**2. ok: [A biztonsági mentési bővítmény nem tud frissíteni vagy betölteni](#the-backup-extension-fails-to-update-or-load)**  
**3. ok: [Az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**4. ok: [A virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 – a biztonsági mentés sikertelen, belső hiba miatt

**Hibakód**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Hibaüzenet**: A biztonsági mentés belső hiba miatt meghiúsult – próbálja megismételni a műveletet néhány perc múlva. <br>

Miután regisztrálta és beütemezte a virtuális gépet a Azure Backup szolgáltatáshoz, a biztonsági mentés a virtuális gép biztonsági mentési bővítménnyel való kommunikációval kezdeményezi az adott időponthoz tartozó pillanatképet. A következő feltételek bármelyike megakadályozhatja a pillanatkép aktiválását. Ha a pillanatkép nincs aktiválva, a biztonsági mentési hiba merülhet fel. Hajtsa végre a következő hibaelhárítási lépéseket a felsorolt sorrendben, majd próbálja megismételni a műveletet:  
**1. ok: [A virtuális gépre telepített ügynök, de nem válaszol (Windows rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**2. ok: [A virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [Nem lehet lekérdezni a pillanatkép állapotát, vagy nem lehet pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**4. ok: [A biztonsági mentési bővítmény nem tud frissíteni vagy betölteni](#the-backup-extension-fails-to-update-or-load)**  
**5. ok: A Backup szolgáltatásnak nincs engedélye a régi visszaállítási pontok törlésére egy erőforráscsoport-zárolás miatt** <br>
**6. ok: [A virtuális gépnek nincs internet-hozzáférése](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-4095gb"></a>UserErrorUnsupportedDiskSize – jelenleg Azure Backup nem támogatja a 4095 GB nál-nál nagyobb méretű lemezeket

**Hibakód**: UserErrorUnsupportedDiskSize <br>
**Hibaüzenet**: Jelenleg Azure Backup nem támogatja a 4095 GB nál-nál nagyobb méretű lemezeket <br>

A biztonsági mentési művelet sikertelen lehet, ha a virtuális gépet a 4095 GB nál-nál nagyobb méretű lemezről készíti. Ha regisztrálni szeretne a Azure Backup nagyméretű lemezes támogatásának privát előzetes verziójára, amely a 4TB-nál nagyobb méretű lemezeket támogat, AskAzureBackupTeam@microsoft.comírjon vissza nekünk a 30TB.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress – nem sikerült elindítani a biztonsági mentést, mert folyamatban van egy másik biztonsági mentési művelet végrehajtása.

**Hibakód**: UserErrorBackupOperationInProgress <br>
**Hibaüzenet**: Nem sikerült elindítani a biztonsági mentést, mert folyamatban van egy másik biztonsági mentési művelet végrehajtása.<br>

A legutóbbi biztonsági mentési feladata sikertelen volt, mert folyamatban van egy meglévő biztonsági mentési művelet. Nem indíthat el új biztonsági mentési feladatot, amíg az aktuális feladatot be nem fejeződik. Győződjön meg arról, hogy a jelenleg folyamatban lévő biztonsági mentési művelet egy másik biztonsági mentési művelet elindítása vagy ütemezése előtt befejeződött. A biztonsági mentési feladatok állapotának ellenőrzéséhez hajtsa végre az alábbi lépéseket:

1. Az Azure Portalra történő bejelentkezés után kattintson a **Minden szolgáltatás** elemre. Írja be a Recovery Services kifejezést, és kattintson a **Recovery Services-tárolók** elemre. A Recovery Services-tárolók listája megjelenik.
2. A Recovery Services-tárolók listájából válassza ki azt a tárat, amelyben a biztonsági mentés konfigurálva van.
3. A tároló irányítópultjának menüjében kattintson a **biztonsági mentési feladatok** lehetőségre az összes biztonsági mentési feladat megjelenítéséhez.

    * Ha folyamatban van egy biztonsági mentési feladat, várja meg, amíg befejeződik, vagy szakítsa meg a biztonsági mentési feladatot.
        * A biztonsági mentési feladat megszakításához kattintson a jobb gombbal a biztonsági mentési feladatokra, majd kattintson a **Mégse** gombra, vagy használja a [PowerShellt](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
    * Ha újrakonfigurálta a biztonsági mentést egy másik tárolóban, akkor győződjön meg arról, hogy a régi tárolóban nem fut biztonsági mentési feladat. Ha már létezik, szakítsa meg a biztonsági mentési feladatot.
        * A biztonsági mentési feladat megszakításához kattintson a jobb gombbal a biztonsági mentési feladatokra, majd kattintson a **Mégse** gombra, vagy használja a [PowerShellt](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0) .
4. Próbálkozzon újra a biztonsági mentési művelettel.

Ha az ütemezett biztonsági mentési művelet több időt vesz igénybe, és a következő biztonsági mentési konfiguráció ütközik, tekintse át az [ajánlott eljárásokat](backup-azure-vms-introduction.md#best-practices), a [biztonsági mentés teljesítményére](backup-azure-vms-introduction.md#backup-performance) és a visszaállításra vonatkozó [szempontokat](backup-azure-vms-introduction.md#backup-and-restore-considerations).


## <a name="causes-and-solutions"></a>Okok és megoldások

### <a name="the-vm-has-no-internet-access"></a>A virtuális gépnek nincs internet-hozzáférése
A központi telepítés követelménye szerint a virtuális gépnek nincs internet-hozzáférése. Vagy előfordulhat, hogy korlátozásokkal rendelkezik, amelyek megakadályozzák az Azure-infrastruktúrához való hozzáférést.

A megfelelő működéshez a biztonsági mentési bővítménynek kapcsolódnia kell az Azure nyilvános IP-címeihez. A bővítmény parancsokat küld egy Azure Storage-végpontnak (HTTPs URL-cím) a virtuális gép pillanatképének kezeléséhez. Ha a bővítmény nem fér hozzá a nyilvános internethez, a biztonsági mentés végül sikertelen lesz.

####  <a name="solution"></a>Megoldás
A hálózati probléma megoldásához tekintse meg a [hálózati kapcsolat létesítése](backup-azure-arm-vms-prepare.md#establish-network-connectivity)című témakört.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)

#### <a name="solution"></a>Megoldás
Lehet, hogy a virtuálisgép-ügynök sérült, vagy a szolgáltatást leállították. A virtuálisgép-ügynök újratelepítése segíti a legújabb verzió beszerzését. A szolgáltatással folytatott kommunikáció újraindítását is lehetővé teszi.

1. Állapítsa meg, hogy a Windows Azure Guest Agent szolgáltatás fut-e a virtuálisgép-szolgáltatásokban (Services. msc). Próbálja meg újraindítani a Windows Azure Guest Agent szolgáltatást, és kezdeményezze a biztonsági mentést.    
2. Ha a Windows Azure Guest Agent ügynök szolgáltatás nem látható a szolgáltatások területen, a Vezérlőpulton lépjen a **programok és szolgáltatások** elemre annak megállapításához, hogy telepítve van-e a Windows Azure Guest Agent szolgáltatás.
4. Ha a Windows Azure Guest Agent ügynök megjelenik a **programok és szolgáltatások szolgáltatásban**, távolítsa el a Windows Azure Guest Agent ügynököt.
5. Töltse le és telepítse az [Agent MSI legújabb verzióját](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságokkal kell rendelkeznie.
6. Ellenőrizze, hogy a Windows Azure Guest Agent szolgáltatás megjelenik-e a szolgáltatások között.
7. Igény szerinti biztonsági mentés futtatása:
    * A portálon válassza a **biztonsági mentés**lehetőséget.

Ellenőrizze azt is, hogy a [Microsoft .NET 4,5 telepítve van](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) -e a virtuális gépen. A .NET 4,5 szükséges ahhoz, hogy a virtuálisgép-ügynök kommunikáljon a szolgáltatással.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>A virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)

#### <a name="solution"></a>Megoldás
A Linux rendszerű virtuális gépek esetében a legtöbb ügynökkel kapcsolatos vagy kiterjesztéssel kapcsolatos hibát az elavult virtuálisgép-ügynököt érintő problémák okozzák. A probléma megoldásához kövesse az alábbi általános irányelveket:

1. Kövesse a Linux rendszerű [virtuális gép ügynökének frissítésével](../virtual-machines/linux/update-agent.md)kapcsolatos utasításokat.

   > [!NOTE]
   > Javasoljuk *, hogy csak* terjesztési tárházon keresztül frissítse az ügynököt. Nem javasoljuk, hogy közvetlenül a GitHubról töltse le az ügynököt, és frissítse azt. Ha a disztribúcióhoz tartozó legújabb ügynök nem érhető el, a telepítésével kapcsolatos útmutatásért forduljon az elosztási támogatáshoz. A legutóbbi ügynök kereséséhez nyissa meg a [Windows Azure Linux Agent](https://github.com/Azure/WALinuxAgent/releases) lapot a GitHub-tárházban.

2. A következő parancs futtatásával győződjön meg arról, hogy az Azure-ügynök fut a virtuális gépen:`ps -e`

   Ha a folyamat nem fut, indítsa újra a következő parancsok használatával:

   * Ubuntu esetén:`service walinuxagent start`
   * Egyéb disztribúciók esetén:`service waagent start`

3. [Konfigurálja az automatikus](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)újraindítási ügynököt.
4. Futtasson egy új teszt biztonsági mentést. Ha a hiba továbbra is fennáll, Gyűjtse össze a következő naplókat a virtuális gépről:

   * /var/lib/waagent/*. XML
   * /var/log/waagent.log
   * /var/log/azure/*

Ha a waagent részletes naplózására van szükség, kövesse az alábbi lépéseket:

1. A/etc/waagent.conf fájlban keresse meg a következő sort: **Részletes naplózás engedélyezése (y | n)**
2. Módosítsa a **naplókat. részletes** érték: *n* – *y*.
3. Mentse a változást, majd indítsa újra a waagent a jelen szakasz fentebb ismertetett lépéseinek végrehajtásával.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Nem lehet lekérdezni a pillanatkép állapotát, vagy nem lehet pillanatképet készíteni
A virtuális gép biztonsági mentése egy pillanatkép-parancsnak a mögöttes Storage-fiókba való kiállítására támaszkodik. A biztonsági mentés sikertelen lehet, mert nem fér hozzá a Storage-fiókhoz, vagy mert a pillanatkép-feladat végrehajtása késleltetve van.

#### <a name="solution"></a>Megoldás
A következő feltételek miatt előfordulhat, hogy a pillanatkép-feladat meghiúsul:

| Ok | Megoldás |
| --- | --- |
| A virtuális gép állapota helytelenül van jelezve, mert a virtuális gép le van állítva RDP protokoll (RDP). | Ha az RDP-ben állítja le a virtuális gépet, a portálon ellenőrizze, hogy helyes-e a virtuális gép állapota. Ha nem megfelelő, állítsa le a virtuális gépet a portálon a virtuális gép irányítópultján lévő **Leállítás** lehetőség használatával. |
| A virtuális gép nem tudja lekérni a gazdagép vagy a háló címe DHCP-ről. | A IaaS virtuális gép biztonsági mentésének működéséhez engedélyezni kell a DHCP-t a vendégen belül. Ha a virtuális gép nem tudja lekérni a gazdagépet vagy a háló címeit a 245-es DHCP-válaszból, nem tölthet le és nem futtathat bővítményeket. Ha statikus magánhálózati IP-címmel kell rendelkeznie, konfigurálja a **Azure Portal** vagy a **PowerShell** használatával, és győződjön meg arról, hogy a virtuális gépen belül a DHCP-beállítás engedélyezve van. [További](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) információ statikus IP-cím beállításáról a PowerShell-lel.

### <a name="the-backup-extension-fails-to-update-or-load"></a>A biztonsági mentési bővítmény nem tud frissíteni vagy betölteni
Ha a bővítmények nem tölthetők be, a biztonsági mentés sikertelen lesz, mert nem lehet pillanatképet készíteni.

#### <a name="solution"></a>Megoldás

Távolítsa el a bővítményt a VMSnapshot-bővítmény újratöltésének kényszerítéséhez. A következő biztonsági mentési kísérlet újratölti a bővítményt.

A bővítmény eltávolítása:

1. A [Azure Portal](https://portal.azure.com/)lépjen a biztonsági mentési hibát észlelő virtuális gépre.
2. Válassza ki **beállítások**.
3. Kattintson az **Extensions** (Bővítmények) gombra.
4. Válassza ki a **Vmsnapshot bővítményt**.
5. Válassza az **Eltávolítás**lehetőséget.

Linux rendszerű virtuális gépek esetén, ha az VMSnapshot bővítmény nem jelenik meg a Azure Portalban, [frissítse az Azure Linux](../virtual-machines/linux/update-agent.md)-ügynököt, majd futtassa a biztonsági mentést.

Ezeknek a lépéseknek a végrehajtásával a bővítmény újratelepíthető a következő biztonsági mentés során.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>A helyreállítási pont erőforráscsoporthoz tartozó zárolás eltávolítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Lépjen a **minden erőforrás lehetőségre**, és válassza a visszaállítási pont gyűjteménye erőforráscsoportot a következő`<Geo>`formátumban`<number>`AzureBackupRG_ _.
3. A zárolások megjelenítéséhez a **Beállítások** szakaszban válassza a **zárolások** lehetőséget.
4. A zárolás eltávolításához válassza a három pontot, majd kattintson a **Törlés**gombra.

    ![Zárolás törlése](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a>Visszaállítási pont gyűjtésének törlése
A zárolás eltávolítását követően a visszaállítási pontokat törölni kell. A visszaállítási pontok tisztításához kövesse az alábbi módszereket:<br>
* [A visszaállítási pontok gyűjtésének törlése alkalmi biztonsági mentés futtatásával](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Visszaállítási pont gyűjtésének törlése Azure Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>A visszaállítási pontok gyűjtésének törlése alkalmi biztonsági mentés futtatásával
A zárolás eltávolítását követően aktiválja az alkalmi/manuális biztonsági mentést. Ezzel biztosítható, hogy a visszaállítási pontok automatikusan törlődnek. Az ad hoc/manuális művelet első alkalommal történő elvégzése várhatóan sikertelen lesz. a visszaállítási pontok manuális törlése helyett azonban gondoskodni fog az automatikus tisztításról. A következő ütemezett biztonsági mentés sikeres törlését követően.

> [!NOTE]
> Az automatikus tisztítás az alkalmi/manuális biztonsági mentés néhány órája után is megtörténik. Ha az ütemezett biztonsági mentés továbbra is sikertelen, próbálja meg manuálisan törölni a visszaállítási pont gyűjteményét az [itt](#clean-up-restore-point-collection-from-azure-portal)felsorolt lépések segítségével.

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Visszaállítási pont gyűjtésének törlése Azure Portal <br>

Ha manuálisan szeretné törölni a visszaállítási pontok gyűjteményét, amely az erőforráscsoport zárolása miatt nem törlődik, próbálkozzon a következő lépésekkel:
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A **központi** menüben kattintson a **minden erőforrás**lehetőségre, majd válassza ki az erőforráscsoportot a következő formátumban`<Geo>`:`<number>` AzureBackupRG_ _, ahol a virtuális gép található.

    ![Zárolás törlése](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Kattintson az erőforráscsoport elemre, és megjelenik az **Áttekintés** panel.
4. Válassza a **rejtett típusok megjelenítése** lehetőséget az összes rejtett erőforrás megjelenítéséhez. Válassza ki a visszaállítási pontok gyűjteményeit a következő`<VMName>`formátumban`<number>`: AzureBackupRG_ _.

    ![Zárolás törlése](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Kattintson a **Törlés**gombra a visszaállítási pont gyűjteményének tisztításához.
6. Próbálja megismételni a biztonsági mentési műveletet.

> [!NOTE]
 >Ha az erőforrás (RP-gyűjtemény) nagy számú visszaállítási ponttal rendelkezik, akkor az azonos a portálról való törlése időtúllépési és sikertelen lehet. Ez egy ismert CRP-probléma, amelyben az összes visszaállítási pont nem törlődik a megadott időn belül, és a művelet túllépi az időkorlátot. a törlési művelet azonban általában 2 vagy 3 újrapróbálkozást követően sikeres lesz.
