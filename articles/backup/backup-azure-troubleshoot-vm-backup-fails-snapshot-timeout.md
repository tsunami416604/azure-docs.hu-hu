---
title: 'Azure Backup hibaelhárítása: Vendég ügynök állapota nem érhető el'
description: Tünetek, okok, és az Azure Backup hibák ügynök, a bővítmény és a lemezek kapcsolatos megoldások.
services: backup
author: genlin
manager: cshepard
keywords: Az Azure backup; A Virtuálisgép-ügynök; Hálózati kapcsolat;
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 5c37e2e3cabb81ed123146f283c7d568cc58816d
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242622"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure Backup hibaelhárítása: az ügynök vagy a bővítmény kapcsolatos problémák

Ez a cikk ismerteti a hibaelhárítási lépések, amelyek segítségével kommunikál a Virtuálisgép-ügynök és a bővítmény az Azure Backup hibáinak megoldásához.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>Nem lehet kommunikálni az Azure Backup Virtuálisgép-ügynök

Hibaüzenet: "Virtuálisgép-ügynök nem lehet kommunikálni az Azure Backup"<br>
Hibakód: "UserErrorGuestAgentStatusUnavailable"

Miután regisztrálta, és a egy virtuális Gépet, a Backup szolgáltatás ütemezése, biztonsági mentése időponthoz pillanatkép készítése a Virtuálisgép-ügynököt kommunikálva indítja el a feladat. Az alábbi feltételek bármelyike előfordulhat, hogy a pillanatkép nem aktiválása. Pillanatkép nem aktiválódik, amikor a biztonsági mentés sikertelen lehet. Kövesse az alábbi hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:

**1. ok: [a virtuális gép nem rendelkezik internet-hozzáférés](#the-vm-has-no-internet-access)**  
**2. ok: [az ügynök telepítve van a virtuális Gépet, de annak nem válaszoló (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**3. ok: [az ügynök telepítve van a virtuális gépen nem naprakész (a Linux rendszerű virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**4. ok: [nem kérhető le a pillanatfelvétel állapotáról, vagy nem lehet egy pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**5. ok: [a biztonsági mentési bővítményt frissítésére vagy betöltése sikertelen](#the-backup-extension-fails-to-update-or-load)**  

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Pillanatkép-készítési művelet sikertelen lesz, mivel a virtuális gép nincs csatlakoztatva a hálózathoz

Hibaüzenet: "Pillanatkép-művelet végrehajtása sikertelen volt, mert nincs hálózati kapcsolat a virtuális gép"<br>
Hibakód: "ExtensionSnapshotFailedNoNetwork"

Miután regisztrálta, és a egy virtuális Gépet az Azure Backup szolgáltatás a ütemezése, biztonsági mentés a Virtuálisgép-biztonsági mentési bővítmény időponthoz pillanatképének kommunikálva indítja el a feladatot. Az alábbi feltételek bármelyike előfordulhat, hogy a pillanatkép nem aktiválása. A pillanatkép nem aktiválódik, ha egy biztonsági mentési hiba fordulhat elő. Kövesse az alábbi hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:    
**1. ok: [a virtuális gép nem rendelkezik internet-hozzáférés](#the-vm-has-no-internet-access)**  
**2. ok: [nem kérhető le a pillanatfelvétel állapotáról, vagy nem lehet egy pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**3. ok: [a biztonsági mentési bővítményt frissítésére vagy betöltése sikertelen](#the-backup-extension-fails-to-update-or-load)**  

## <a name="vmsnapshot-extension-operation-failed"></a>A VMSnapshot bővítmény művelete sikertelen

Hibaüzenet: "a VMSnapshot bővítmény művelete sikertelen"<br>
Hibakód: "ExtentionOperationFailed"

Miután regisztrálta, és a egy virtuális Gépet az Azure Backup szolgáltatás a ütemezése, biztonsági mentés a Virtuálisgép-biztonsági mentési bővítmény időponthoz pillanatképének kommunikálva indítja el a feladatot. Az alábbi feltételek bármelyike előfordulhat, hogy a pillanatkép nem aktiválása. A pillanatkép nem aktiválódik, ha egy biztonsági mentési hiba fordulhat elő. Kövesse az alábbi hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:  
**1. ok: [nem kérhető le a pillanatfelvétel állapotáról, vagy nem lehet egy pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**2. ok: [a biztonsági mentési bővítményt frissítésére vagy betöltése sikertelen](#the-backup-extension-fails-to-update-or-load)**  
**3. ok: [az ügynök telepítve van a virtuális Gépet, de annak nem válaszoló (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**4. ok: [az ügynök telepítve van a virtuális gépen nem naprakész (a Linux rendszerű virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backup-fails-because-the-vm-agent-is-unresponsive"></a>Biztonsági mentés sikertelen, mert a Virtuálisgép-ügynök nem válaszol

Hibaüzenet: "Nem tudott kommunikálni a Virtuálisgép-ügynök a pillanatfelvétel állapotáról" <br>
Hibakód: "GuestAgentSnapshotTaskStatusError"

Miután regisztrálta, és a egy virtuális Gépet az Azure Backup szolgáltatás a ütemezése, biztonsági mentés a Virtuálisgép-biztonsági mentési bővítmény időponthoz pillanatképének kommunikálva indítja el a feladatot. Az alábbi feltételek bármelyike előfordulhat, hogy a pillanatkép nem aktiválása. A pillanatkép nem aktiválódik, ha egy biztonsági mentési hiba fordulhat elő. Kövesse az alábbi hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:  
**1. ok: [az ügynök telepítve van a virtuális Gépet, de annak nem válaszoló (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**2. ok: [az ügynök telepítve van a virtuális gépen nem naprakész (a Linux rendszerű virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [a virtuális gép nem rendelkezik internet-hozzáférés](#the-vm-has-no-internet-access)**  

## <a name="backup-fails-with-an-internal-error"></a>Biztonsági mentés sikertelen, belső hiba

Hibaüzenet: "biztonsági mentés belső hiba miatt meghiúsult – próbálja megismételni a műveletet néhány perc múlva" <br>
Hibakód: "BackUpOperationFailed" / "BackUpOperationFailedV2"

Miután regisztrálta, és a egy virtuális Gépet az Azure Backup szolgáltatás a ütemezése, biztonsági mentés a Virtuálisgép-biztonsági mentési bővítmény időponthoz pillanatképének kommunikálva indítja el a feladatot. Az alábbi feltételek bármelyike előfordulhat, hogy a pillanatkép nem aktiválása. A pillanatkép nem aktiválódik, ha egy biztonsági mentési hiba fordulhat elő. Kövesse az alábbi hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:  
**1. ok: [a virtuális gép nem rendelkezik internet-hozzáférés](#the-vm-has-no-internet-access)**  
**2. ok: [az ügynök telepítve van a virtuális Gépet, de nem válaszol a (Windows virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**3. ok: [az ügynök telepítve van a virtuális gépen nem naprakész (a Linux rendszerű virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**4. ok: [nem kérhető le a pillanatfelvétel állapotáról, vagy nem lehet egy pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**5. ok: [a biztonsági mentési bővítményt frissítésére vagy betöltése sikertelen](#the-backup-extension-fails-to-update-or-load)**  
**6. ok: [Backup szolgáltatásnak nincs engedélye a régi helyreállítási pontok törlése egy erőforrás-csoport zárolás miatt](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

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

1. Határozza meg, hogy a Windows Vendégügynöke szolgáltatás fut-e a virtuális gép szolgáltatások (services.msc). Próbálja meg újraindítani a Windows Vendégügynöke szolgáltatást, és indítsa el a biztonsági mentést.    
2. Ha a Windows Vendégügynöke szolgáltatást nem látható a szolgáltatások, a Vezérlőpulton **programok és szolgáltatások** meghatározni, hogy telepítve van-e a Windows Vendégügynöke szolgáltatást.
4. Ha megjelenik a Windows Vendégügynöke **programok és szolgáltatások**, távolítsa el a Windows Vendégügynöke.
5. Töltse le és telepítse a [az ügynök MSI legfrissebb verzióját](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságokkal kell rendelkeznie.
6. Győződjön meg arról, hogy a Windows Vendégügynöke szolgáltatás szolgáltatások jelennek meg.
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

1. Az /etc/waagent.conf fájlban keresse meg a következő sort: **részletes naplózás engedélyezése (y |} n)**
2. Módosítsa a **Logs.Verbose** értéket *n* való *y*.
3. A módosítás mentéséhez, és indítsa újra waagent korábban ebben a szakaszban ismertetett lépéseket követve.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Nem kérhető le a pillanatfelvétel állapotáról, vagy nem lehet egy pillanatképet készíteni
A virtuális gép biztonsági mentésének támaszkodik egy pillanatkép parancs kiadása az alapul szolgáló tárfiókba. Biztonsági mentés sikertelen lehet, mert azt nem fér hozzá a storage-fiókba, vagy mert a pillanatkép-feladat végrehajtásának késik.

#### <a name="solution"></a>Megoldás
A következő feltételek előfordulhat, hogy a pillanatkép-feladat meghiúsul:

| Ok | Megoldás |
| --- | --- |
| A virtuális gép állapota helytelenül van jelenteni, mert a virtuális gép le van állítva a távoli asztal protokoll (RDP). | Ha leállítja a virtuális Géphez RDP, ellenőrizze a portálra, ellenőrizze, hogy a virtuális gép állapota megfelelő. Ha nem megfelelő, állítsa le a virtuális Gépet a portál használatával a **leállítási** lehetőség a VM-irányítópulton. |
| A virtuális gép nem olvasható be a gazdagép vagy a háló címét a DHCP-Kiszolgálótól. | A Vendég az IaaS virtuális gépek biztonsági mentésének működéséhez engedélyezni kell a DHCP. Ha a virtuális gép nem olvasható be a gazdagép vagy a háló cím 245 DHCP-válaszból, nem töltse le és futtassa a kiterjesztések. Ha statikus magánhálózati IP-cím van szüksége, konfigurálja a platformon keresztül. A virtuális gép DHCP-beállítást engedélyezni kell a bal oldalon. További információkért lásd: [statikus belső magánhálózati IP-cím beállítása](../virtual-network/virtual-networks-reserved-private-ip.md). |

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

### <a name="backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>A Backup szolgáltatás nem rendelkezik engedéllyel a régi visszaállítási pontok törléséhez egy erőforrás-csoport zárolás miatt
A probléma csak a felügyelt virtuális gépek, a felhasználó zárolja magát az erőforráscsoportot. Ebben az esetben a biztonsági mentési szolgáltatás régebbi helyreállítási pontokat nem lehet törölni. 18 visszaállítási pontok korlátozva van, mert új biztonsági másolatok elindítása sikertelen.

#### <a name="solution"></a>Megoldás

A probléma megoldásához távolítsa el a zárolást az erőforráscsoportból, és eltávolítja a visszaállításipont-gyűjtemény a következő lépéseket: 
 
1. Távolítsa el a zárolást, ahol a virtuális gép is található az erőforráscsoportban. 
2. ARMClient telepíti a chocolatey-t: <br>
   https://github.com/projectkudu/ARMClient
3. Jelentkezzen be a ARMClient: <br>
    `.\armclient.exe login`
4. A visszaállításipont-gyűjtemény, amely megfelel a virtuális gép beolvasása: <br>
    `.\armclient.exe get https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30`

    Például: `.\armclient.exe get https://management.azure.com/subscriptions/f2edfd5d-5496-4683-b94f-b3588c579006/resourceGroups/winvaultrg/providers/Microsoft.Compute/restorepointcollections/AzureBackup_winmanagedvm?api-version=2017-03-30`
5. A visszaállításipont-gyűjtemény törlése: <br>
    `.\armclient.exe delete https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30` 
6. A következő ütemezett biztonsági mentés automatikusan létrehoz egy visszaállításipont-gyűjtemény és az új visszaállítási pontok.

Ha végzett, újra helyezheti vissza a zárolást a virtuális gép erőforráscsoportja. 
