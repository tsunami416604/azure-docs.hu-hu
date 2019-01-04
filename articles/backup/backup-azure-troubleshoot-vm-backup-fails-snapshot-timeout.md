---
title: 'Azure Backup hibaelhárítása: Vendég ügynök állapota nem érhető el'
description: Tünetek, okok, és az Azure Backup hibák ügynök, a bővítmény és a lemezek kapcsolatos megoldások.
services: backup
author: genlin
manager: cshepard
keywords: Az Azure backup; A Virtuálisgép-ügynök; Hálózati kapcsolat;
ms.service: backup
ms.topic: troubleshooting
ms.date: 12/03/2018
ms.author: genli
ms.openlocfilehash: a0f002266764ace07482023a0412366b90acec63
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789857"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure Backup hibaelhárítása: Az ügynök vagy a bővítmény kapcsolatos problémák

Ez a cikk ismerteti a hibaelhárítási lépések, amelyek segítségével kommunikál a Virtuálisgép-ügynök és a bővítmény az Azure Backup hibáinak megoldásához.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable – Virtuálisgép-ügynök nem lehet kommunikálni az Azure Backup szolgáltatással

**Hibakód:**: UserErrorGuestAgentStatusUnavailable <br>
**Chybová zpráva**: Nem lehet kommunikálni az Azure Backup Virtuálisgép-ügynök<br>

Miután regisztrálta, és a egy virtuális Gépet, a Backup szolgáltatás ütemezése, biztonsági mentése időponthoz pillanatkép készítése a Virtuálisgép-ügynököt kommunikálva indítja el a feladat. Az alábbi feltételek bármelyike előfordulhat, hogy a pillanatkép nem aktiválása. Pillanatkép nem aktiválódik, amikor a biztonsági mentés sikertelen lehet. Kövesse az alábbi hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:<br>
**1. ok: [Az ügynök telepítve van a virtuális gépen, de nem válaszoló (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**2. ok: [Az ügynök telepítve van a virtuális gépen nem naprakész (a Linux rendszerű virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [Nem kérhető le a pillanatfelvétel állapotáról, vagy nem lehet egy pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**4. ok: [A biztonsági mentési bővítményt frissítésére vagy betöltése sikertelen](#the-backup-extension-fails-to-update-or-load)**  
**5. ok: [A virtuális gép nem rendelkezik internet-hozzáférés](#the-vm-has-no-internet-access)**

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - kommunikáció nem volt lehetséges a pillanatfelvétel állapotáról Virtuálisgép-ügynök

**Hibakód:**: GuestAgentSnapshotTaskStatusError<br>
**Chybová zpráva**: A virtuális gép ügynökével való kommunikáció nem volt lehetséges a pillanatfelvétel állapotáról <br>

Miután regisztrálta, és a egy virtuális Gépet az Azure Backup szolgáltatás a ütemezése, biztonsági mentés a Virtuálisgép-biztonsági mentési bővítmény időponthoz pillanatképének kommunikálva indítja el a feladatot. Az alábbi feltételek bármelyike előfordulhat, hogy a pillanatkép nem aktiválása. A pillanatkép nem aktiválódik, ha egy biztonsági mentési hiba fordulhat elő. Kövesse az alábbi hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:  
**1. ok: [Az ügynök telepítve van a virtuális gépen, de nem válaszoló (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**2. ok: [Az ügynök telepítve van a virtuális gépen nem naprakész (a Linux rendszerű virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [A virtuális gép nem rendelkezik internet-hozzáférés](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - elérte a Visszaállításipont-gyűjtemény maximális korlátját

**Hibakód:**: UserErrorRpCollectionLimitReached <br>
**Chybová zpráva**: Elérte a Visszaállításipont-gyűjtemény maximális korlátját. <br>
* A probléma akkor fordulhat elő, ha a helyreállítási pont erőforráscsoport megakadályozza az automatikus tisztítás helyreállítási pont található egy zárolás.
* A probléma is előfordulhat, ha naponta több biztonsági mentése esetén. Jelenleg javasoljuk, hogy csak egy biztonsági mentés azonnali RPs 7 napig maradnak, naponta, és csak 18 azonnali RPs társítható egy virtuális Gépet egy adott időpontban. <br>

Javasolt művelet:<br>
A probléma megoldásához távolítsa el a zárolást a az erőforráscsoport, a virtuális gép, és próbálja megismételni a műveletet kiváltó karbantartási. 
> [!NOTE]
    > A biztonsági mentési szolgáltatás létrehoz egy külön erőforráscsoportot, mint az erőforráscsoport, a virtuális gép visszaállításipont-gyűjtemény tárolásához. Ügyfelek, a Backup szolgáltatás használni létrehozott erőforráscsoportot zárolása nem végigvitelével. A Backup szolgáltatás által létrehozott erőforráscsoport elnevezési formátuma: AzureBackupRG_`<Geo>`_`<number>` működtek az adatbázisok: AzureBackupRG_northeurope_1

**1. lépés: [Távolítsa el a zárolást a visszaállítási pont erőforráscsoportból](#remove_lock_from_the_recovery_point_resource_group)** <br>
**2. lépés: [Visszaállításipont-gyűjtemény törlése](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured – a biztonsági mentés nem rendelkezik megfelelő engedélyekkel a titkosított virtuális gépek biztonsági mentése a key vault.

**Hibakód:**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Chybová zpráva**: Biztonsági mentés nem rendelkezik megfelelő engedélyekkel a key vault a biztonsági mentéshez, a titkosított virtuális gépekhez. <br>

A titkosított virtuális gépek biztonsági mentési művelethez azt a kulcstartó hozzáférési engedéllyel kell rendelkeznie. Ehhez használja a [az Azure portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption#provide-permissions-to-backup) vagy a [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection)

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - pillanatkép-készítési művelet sikertelen volt, mert nincs hálózati kapcsolat a virtuális gépen

**Hibakód:**: ExtensionSnapshotFailedNoNetwork<br>
**Chybová zpráva**: Nem sikerült elkészíteni a pillanatképet, mert nincs hálózati kapcsolat a virtuális gépen<br>

Miután regisztrálta, és a egy virtuális Gépet az Azure Backup szolgáltatás a ütemezése, biztonsági mentés a Virtuálisgép-biztonsági mentési bővítmény időponthoz pillanatképének kommunikálva indítja el a feladatot. Az alábbi feltételek bármelyike előfordulhat, hogy a pillanatkép nem aktiválása. A pillanatkép nem aktiválódik, ha egy biztonsági mentési hiba fordulhat elő. Kövesse az alábbi hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:    
**1. ok: [Nem kérhető le a pillanatfelvétel állapotáról, vagy nem lehet egy pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**2. ok: [A biztonsági mentési bővítményt frissítésére vagy betöltése sikertelen](#the-backup-extension-fails-to-update-or-load)**  
**3. ok: [A virtuális gép nem rendelkezik internet-hozzáférés](#the-vm-has-no-internet-access)**

## <a name="ExtentionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtentionOperationFailedForManagedDisks - VMSnapshot bővítmény művelete sikertelen volt

**Hibakód:**: ExtentionOperationFailedForManagedDisks <br>
**Chybová zpráva**: A VMSnapshot bővítmény művelete sikertelen volt<br>

Miután regisztrálta, és a egy virtuális Gépet az Azure Backup szolgáltatás a ütemezése, biztonsági mentés a Virtuálisgép-biztonsági mentési bővítmény időponthoz pillanatképének kommunikálva indítja el a feladatot. Az alábbi feltételek bármelyike előfordulhat, hogy a pillanatkép nem aktiválása. A pillanatkép nem aktiválódik, ha egy biztonsági mentési hiba fordulhat elő. Kövesse az alábbi hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:  
**1. ok: [Nem kérhető le a pillanatfelvétel állapotáról, vagy nem lehet egy pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**2. ok: [A biztonsági mentési bővítményt frissítésére vagy betöltése sikertelen](#the-backup-extension-fails-to-update-or-load)**  
**3. ok: [Az ügynök telepítve van a virtuális gépen, de nem válaszoló (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**4. ok: [Az ügynök telepítve van a virtuális gépen nem naprakész (a Linux rendszerű virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 – biztonsági mentés sikertelen, belső hiba

**Hibakód:**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Chybová zpráva**: Biztonsági mentés belső hiba miatt meghiúsult – próbálja megismételni a műveletet néhány perc múlva <br>

Miután regisztrálta, és a egy virtuális Gépet az Azure Backup szolgáltatás a ütemezése, biztonsági mentés a Virtuálisgép-biztonsági mentési bővítmény időponthoz pillanatképének kommunikálva indítja el a feladatot. Az alábbi feltételek bármelyike előfordulhat, hogy a pillanatkép nem aktiválása. A pillanatkép nem aktiválódik, ha egy biztonsági mentési hiba fordulhat elő. Kövesse az alábbi hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:  
**1. ok: [Az ügynök telepítve van a virtuális Gépet, de nem válaszol a (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**2. ok: [Az ügynök telepítve van a virtuális gépen nem naprakész (a Linux rendszerű virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [Nem kérhető le a pillanatfelvétel állapotáról, vagy nem lehet egy pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**4. ok: [A biztonsági mentési bővítményt frissítésére vagy betöltése sikertelen](#the-backup-extension-fails-to-update-or-load)**  
**5. ok: [Biztonsági mentési szolgáltatás nem rendelkezik engedéllyel a régi visszaállítási pontok törléséhez egy erőforrás-csoport zárolás miatt](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)** <br>
**6. ok: [A virtuális gép nem rendelkezik internet-hozzáférés](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-1023gb"></a>UserErrorUnsupportedDiskSize – a jelenleg az Azure Backup nem támogatja a 1023GB-nál nagyobb lemezméretek

**Hibakód:**: UserErrorUnsupportedDiskSize <br>
**Chybová zpráva**: Az Azure Backup jelenleg nem támogatja az 1023 GB-nál nagyobb adatlemezeket <br>

A biztonsági mentési művelet sikertelen lehet, mivel a tároló nem frissül az Azure virtuális gép biztonsági mentési vermének v2 verziójára az 1023GB-nál nagyobb méretű virtuális Gépet is biztonsági. Azure VM Backup frissítését stack V2 biztosít akár 4 TB-os támogatja. Tekintse át a [előnyöket](backup-upgrade-to-vm-backup-stack-v2.md), [szempontok](backup-upgrade-to-vm-backup-stack-v2.md#considerations-before-upgrade), majd folytassa a következő frissítési [utasításokat](backup-upgrade-to-vm-backup-stack-v2.md#upgrade).  

## <a name="usererrorstandardssdnotsupported---currently-azure-backup-does-not-support-standard-ssd-disks"></a>UserErrorStandardSSDNotSupported – a jelenleg az Azure Backup nem támogatja a Standard SSD-lemez

**Hibakód:**: UserErrorStandardSSDNotSupported <br>
**Chybová zpráva**: Jelenleg az Azure Backup nem támogatja a Standard SSD-lemez <br>

Az Azure Backup jelenleg csak a tárolók az Azure virtuális gép biztonsági mentési vermének v2 verziójára frissített támogatja a Standard SSD-lemezeket. Tekintse át a [előnyöket](backup-upgrade-to-vm-backup-stack-v2.md), [szempontok](backup-upgrade-to-vm-backup-stack-v2.md#considerations-before-upgrade), majd folytassa a következő frissítési [utasításokat](backup-upgrade-to-vm-backup-stack-v2.md#upgrade).


## <a name="causes-and-solutions"></a>Okait és megoldásait

### <a name="the-vm-has-no-internet-access"></a>A virtuális gép nem rendelkezik internet-hozzáférés
A deployment követelmény száma a virtuális gép nincs internetkapcsolata. Vagy előfordulhat, hogy korlátozásokat, amelyeket az Azure-infrastruktúra megakadályozza a hozzáférést.

A megfelelő működéshez, a Backup bővítmény kapcsolódnia kell az Azure nyilvános IP-címeket. A bővítmény parancsokat küld a pillanatképek a virtuális gép kezelése az Azure storage végpont (HTTPs URL-cím). Ha a bővítmény nem fér hozzá a nyilvános interneten, biztonsági mentés idővel sikertelen.

A virtuális gép forgalom irányítására egy proxykiszolgáló üzembe helyezéséhez lehetőség.
##### <a name="create-a-path-for-https-traffic"></a>A HTTPs-forgalmat elérési utat hoz létre

1. Ha hálózati korlátozások (például egy hálózati biztonsági csoportot) helyben, üzembe helyezése egy HTTPs-proxy kiszolgáló irányíthatja a forgalmat.
2. Engedélyezi a hozzáférést az internethez, a HTTPs-proxy kiszolgáló, a szabályok hozzáadása a hálózati biztonsági csoporthoz, ha rendelkezik ilyennel.

Ismerje meg, hogyan állítható be a virtuális gép biztonsági mentéseinek egy HTTPs-proxyk, lásd: [Azure virtuális gépek biztonsági mentése a környezet előkészítése](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

Az Azure nyilvános IP-címek hozzáférésre van szüksége a biztonsági másolat a virtuális gép vagy a proxykiszolgálóhoz, amelyen keresztül az adatforgalmat

####  <a name="solution"></a>Megoldás
A probléma megoldásához próbálja meg a következő módszerek egyikét:

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>Az Azure storage, amely megfelel a régióban való hozzáférés engedélyezése

Használhat [szolgáltatáscímkéket](../virtual-network/security-overview.md#service-tags) , hogy az adott régió storage kapcsolatokat. Győződjön meg arról, hogy a szabály, amely lehetővé teszi a hozzáférést a tárfiókhoz magasabb prioritású, mint a szabály a blokkok internet-hozzáféréssel rendelkező.

![Hálózati biztonsági csoport tárolási címkékkel régió](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

Szeretné megtudni, a részletes eljárást szolgáltatáscímkék konfigurálásához, tekintse meg a [ebben a videóban](https://youtu.be/1EjLQtbKm1M).

> [!WARNING]
> Tárolási szolgáltatáscímkék olyan előzetes verzióban érhető el. Elérhetők csak bizonyos régiókban. Régiók listáját lásd: [Szolgáltatáscímkéket Storage](../virtual-network/security-overview.md#service-tags).

Az Azure Managed Disks szolgáltatást, ha szüksége lehet egy további port megnyitása (port 8443) a tűzfalat.

Továbbá ha az alhálózat nem rendelkezik a kimenő internetes forgalmat útvonal, hozzá kell egy szolgáltatásvégpontot szolgáltatáscímke "Microsoft.Storage" és az alhálózathoz.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Az ügynök telepítve van a virtuális gépen, de nem válaszoló (Windows virtuális gépek esetén)

#### <a name="solution"></a>Megoldás
A Virtuálisgép-ügynök sérült, vagy a szolgáltatás előfordulhat, hogy leállt. A Virtuálisgép-ügynök újratelepítése révén a legújabb verzióra. Emellett segít, indítsa újra a szolgáltatással való kommunikáció.

1. Határozza meg, a Windows Azure Vendég ügynök szolgáltatás fut-e a virtuális gép szolgáltatások (services.msc). Próbálja meg újraindítani a Windows Azure Guest Agent szolgáltatást, és indítsa el a biztonsági mentést.    
2. Ha a Windows Azure Guest Agent szolgáltatás nem látható a szolgáltatások, a Vezérlőpulton **programok és szolgáltatások** meghatározni, hogy telepítve van-e a Windows Azure Guest Agent szolgáltatást.
4. Ha megjelenik a Windows Azure-Vendégügynök **programok és szolgáltatások**, a Windows Azure Vendég ügynök eltávolítása.
5. Töltse le és telepítse a [az ügynök MSI legfrissebb verzióját](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságokkal kell rendelkeznie.
6. Győződjön meg arról, hogy a Windows Azure-Vendégügynök services szolgáltatások jelennek meg.
7. Egy igény szerinti biztonsági mentés futtatása:
    * Válassza a portál **biztonsági mentés**.

Emellett ellenőrizze, hogy [telepítve van a Microsoft .NET 4.5-ös](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) a virtuális gépen. .NET 4.5-ös szükség a Virtuálisgép-ügynök a szolgáltatással való kommunikációra.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Az ügynök telepítve van a virtuális gépen nem naprakész (a Linux rendszerű virtuális gépek)

#### <a name="solution"></a>Megoldás
Legtöbb ügynök vagy bővítmény kapcsolatos hibák Linux rendszerű virtuális gépek egy elavult Virtuálisgép-ügynök érintő problémák okozzák. A probléma elhárításához kövesse az alábbi általános irányelveket:

1. Kövesse az utasításokat [Linux rendszerű virtuális gép-ügynök frissítése](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > Hogy *erősen ajánlott* frissíteni az ügynök csak egy terjesztési tárház keresztül. A kód letöltése a közvetlenül a githubból, és frissítéskor nem ajánlott. Ha a legújabb ügynököt a disztribúció nem érhető el, forduljon terjesztési támogatása útmutatást a telepítéshez. A legújabb ügynök ellenőrzéséhez nyissa meg a [Windows Azure Linux-ügynök](https://github.com/Azure/WALinuxAgent/releases) lap a GitHub-adattárában.

2. Győződjön meg arról, hogy az Azure-ügynököt a virtuális gépen fut a következő parancs futtatásával: `ps -e`

 Ha a folyamat nem fut, indítsa újra a következő parancsokat:

 * Az ubuntu rendszeren: `service walinuxagent start`
 * Más disztribúciók: `service waagent start`

3. [Az automatikus újraindítás ügynök konfigurálása](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Egy új tesztelési biztonsági mentés futtatása. Ha a hiba továbbra is fennáll, gyűjtése a virtuális Gépet a következő naplók kapcsolódnak:

   * /var/lib/waagent/*.XML
   * /var/log/waagent.log
   * /var/log/azure/*

Ha a waagent szükséges részletes naplózást, kövesse az alábbi lépéseket:

1. Az /etc/waagent.conf fájlban keresse meg a következő sort: **Részletes naplózás engedélyezése (y |} n)**
2. Módosítsa a **Logs.Verbose** értéket *n* való *y*.
3. A módosítás mentéséhez, és indítsa újra waagent korábban ebben a szakaszban ismertetett lépéseket követve.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Nem kérhető le a pillanatfelvétel állapotáról, vagy nem lehet egy pillanatképet készíteni
A virtuális gép biztonsági mentésének támaszkodik egy pillanatkép parancs kiadása az alapul szolgáló tárfiókba. Biztonsági mentés sikertelen lehet, mert azt nem fér hozzá a storage-fiókba, vagy mert a pillanatkép-feladat végrehajtásának késik.

#### <a name="solution"></a>Megoldás
A következő feltételek előfordulhat, hogy a pillanatkép-feladat meghiúsul:

| Ok | Megoldás |
| --- | --- |
| A virtuális gép állapota helytelenül van jelenteni, mert a virtuális gép le van állítva a távoli asztal protokoll (RDP). | Ha leállítja a virtuális Géphez RDP, ellenőrizze a portálra, ellenőrizze, hogy a virtuális gép állapota megfelelő. Ha nem megfelelő, állítsa le a virtuális Gépet a portál használatával a **leállítási** lehetőség a VM-irányítópulton. |
| A virtuális gép nem olvasható be a gazdagép vagy a háló címét a DHCP-Kiszolgálótól. | A Vendég az IaaS virtuális gépek biztonsági mentésének működéséhez engedélyezni kell a DHCP. Ha a virtuális gép nem olvasható be a gazdagép vagy a háló cím 245 DHCP-válaszból, nem töltse le és futtassa a kiterjesztések. Statikus magánhálózati IP-cím van szüksége, ha úgy konfigurálja, keresztül a **az Azure Portal** vagy **PowerShell** , és győződjön meg arról, hogy a virtuális gép a DHCP-beállítás engedélyezve van. További információ a Powershellen keresztül statikus IP-cím telepítés: [klasszikus virtuális gép](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm) és [Resource Manager-alapú](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface).

### <a name="the-backup-extension-fails-to-update-or-load"></a>A biztonsági mentési bővítményt frissítésére vagy betöltése sikertelen
Bővítmények nem tölthető be, ha a biztonsági mentés sikertelen, mert nem lehet egy pillanatképet készíteni.

#### <a name="solution"></a>Megoldás

Távolítsa el a bővítményt, kényszerítheti a VMSnapshot-bővítményt, ide az újrabetöltéshez. A következő biztonsági mentési kísérletet újra betölti a bővítményt.

A bővítmény eltávolítása:

1. Az a [az Azure portal](https://portal.azure.com/), nyissa meg a virtuális gép, amelyen a biztonsági mentési hiba jelentkezik.
2. Válassza ki **beállítások**.
3. Kattintson az **Extensions** (Bővítmények) gombra.
4. Válassza ki **Vmsnapshot-bővítmény**.
5. Válassza ki **eltávolítása**.

A Linux rendszerű virtuális gép, ha a VMSnapshot-bővítményt nem jeleníti meg az Azure Portalon [az Azure Linux-ügynök frissítése](../virtual-machines/linux/update-agent.md), majd futtassa a biztonsági mentés.

Ezen lépések elvégzése hatására a következő biztonsági mentés során újra kell telepíteni a bővítményt.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Távolítsa el a zárolást a helyreállítási pont erőforráscsoportból
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com/).
2. Lépjen a **összes erőforrás lehetőséget**, válassza ki a visszaállítási pont gyűjtemény erőforráscsoportot a következő formátumban AzureBackupRG_`<Geo>`_`<number>`.
3. Az a **beállítások** szakaszban jelölje be **zárolások** a zárolások megjelenítéséhez.
4. Távolítsa el a zárolást, kattintson a három pontra, és kattintson a **törlése**.

    ![Törlési zárolást ](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a> Visszaállításipont-gyűjtemény törlése
A zár feloldása után a visszaállítási pontok törölni kell. A visszaállítási pontok törléséhez kövesse a módszerekkel:<br>
* [A futó ad hoc biztonsági másolat visszaállításipont-gyűjtemény törlése](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Távolítsa el a visszaállítási pont gyűjtemény Azure Portalról](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>A futó ad hoc biztonsági másolat visszaállításipont-gyűjtemény törlése
Zárolás eltávolítása után egy ad-hoc vagy manuális biztonsági mentés indítása. Ez biztosítja, a visszaállítási pontok automatikusan törlődnek. Sikertelen első alkalommal; az ad-hoc vagy manuális művelet várható az automatikus tisztítás manuális visszaállítási pontok törlése helyett azonban biztosítja. A következő ütemezett biztonsági mentés utáni tisztítás sikeres legyen.

> [!NOTE]
    > Automatikus karbantartás, az ad-hoc vagy manuális biztonsági mentésének elindítása néhány óra múlva történik meg. Ha az ütemezett biztonsági mentés továbbra is sikertelen, akkor próbálja meg manuálisan törölni a visszaállításipont-gyűjtemény lépésekkel felsorolt [Itt](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Távolítsa el a visszaállítási pont gyűjtemény Azure Portalról <br>

Törölje manuálisan a visszaállítási pontok gyűjteményt, amely miatt a zárolást a az erőforráscsoport nem törlődnek, az alábbi lépéseket:
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com/).
2. Az a **Hub** menüben kattintson a **összes erőforrás**, válassza ki az erőforráscsoportot és a következő formátumú AzureBackupRG_`<Geo>`_`<number>` ahol a virtuális gép megtalálható.

    ![Törlési zárolást ](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Kattintson az erőforráscsoport, a **áttekintése** panel jelenik meg.
4. Válassza ki **rejtett típusok megjelenítése** lehetőséget a rejtett erőforrások megjelenítéséhez. Jelölje ki a következő formátumú AzureBackupRG_ visszaállítási pont gyűjteményeket`<VMName>`_`<number>`.

    ![Törlési zárolást ](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Kattintson a **törlése**, megtisztítani a visszaállításipont-gyűjtemény.
6. Próbálja megismételni a biztonsági mentési műveletet.
