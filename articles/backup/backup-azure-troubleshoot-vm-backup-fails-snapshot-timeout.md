---
title: 'Azure biztonsági mentési hiba elhárítása: Vendég ügynök állapota nem érhető el'
description: A jelenség okok és megoldások Azure biztonsági mentési hibák ügynök, a bővítmény és a lemezek.
services: backup
author: genlin
manager: cshepard
keywords: Az Azure backup; Virtuálisgép-ügynök; Hálózati kapcsolat;
ms.service: backup
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: genli
ms.openlocfilehash: 63cded007af499455e7bb4fc23d26d56caf96678
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606358"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure biztonsági mentési hiba elhárítása: az ügynök vagy a bővítmény problémái

Ez a cikk ismerteti a hibaelhárítási lépéseket, amelyek segítségével javítsa ki a kommunikáció az ügynök és Virtuálisgép-bővítmény a kapcsolódó Azure Backup hibákat.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>Nem lehet kommunikálni az Azure biztonsági mentés Virtuálisgép-ügynök

Hibaüzenet: "Virtuálisgép-ügynök nem lehet kommunikálni az Azure Backup"<br>
Hibakód: "UserErrorGuestAgentStatusUnavailable"

Miután regisztrálja, és ütemezze a biztonsági mentési szolgáltatás virtuális gép, a biztonsági mentés a feladat kezdeményezi úgy, hogy pont időponthoz kötött pillanatképet készít a virtuális gép ügynökkel folytatott kommunikáció. A következő esetekben előfordulhat, hogy a pillanatkép indított folyamatban. Pillanatkép kiváltásakor a nem a biztonsági mentés sikertelen lehet. Fejezze be a következő hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:

**1. ok: [a virtuális gép nem rendelkezik internet-hozzáférés](#the-vm-has-no-internet-access)**  
**2. ok: [az ügynök telepítve legyen a virtuális Géphez, de azok nem válaszoló (a Windows-alapú virtuális gépek)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**3. ok: [a virtuális gépen telepített ügynök elavult (a Linux virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**4. ok: [pillanatkép állapota nem olvasható, vagy a pillanatkép nem végezhető](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**5. ok: [frissítése vagy nem tölthető be nem sikerül a tartalék mellék](#the-backup-extension-fails-to-update-or-load)**  

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Pillanatkép-művelet sikertelen, mert a virtuális gép nincs csatlakoztatva a hálózathoz

Hibaüzenet: "Pillanatkép-művelet sikertelen volt, mert nincs hálózati kapcsolat a virtuális gépen"<br>
Hibakód: "ExtensionSnapshotFailedNoNetwork"

Miután regisztrálja, és egy virtuális Gépet az Azure Backup szolgáltatás ütemezése, biztonsági mentés indít el a feladat által a biztonsági mentés Virtuálisgép-bővítmény időpontban pillanatképének elkészítéséhez kommunikál. A következő esetekben előfordulhat, hogy a pillanatkép indított folyamatban. Ha a pillanatkép nem elindul, a biztonsági mentési hiba léphet fel. Fejezze be a következő hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:    
**1. ok: [a virtuális gép nem rendelkezik internet-hozzáférés](#the-vm-has-no-internet-access)**  
**2. ok: [pillanatkép állapota nem olvasható, vagy a pillanatkép nem végezhető](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**3. ok: [frissítése vagy nem tölthető be nem sikerül a tartalék mellék](#the-backup-extension-fails-to-update-or-load)**  

## <a name="vmsnapshot-extension-operation-failed"></a>VMSnapshot bővítmény művelet sikertelen

Hibaüzenet: "VMSnapshot művelet sikertelen volt"<br>
Hibakód: "ExtentionOperationFailed"

Miután regisztrálja, és egy virtuális Gépet az Azure Backup szolgáltatás ütemezése, biztonsági mentés indít el a feladat által a biztonsági mentés Virtuálisgép-bővítmény időpontban pillanatképének elkészítéséhez kommunikál. A következő esetekben előfordulhat, hogy a pillanatkép indított folyamatban. Ha a pillanatkép nem elindul, a biztonsági mentési hiba léphet fel. Fejezze be a következő hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:  
**1. ok: [pillanatkép állapota nem olvasható, vagy a pillanatkép nem végezhető](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**2. ok: [frissítése vagy nem tölthető be nem sikerül a tartalék mellék](#the-backup-extension-fails-to-update-or-load)**  
**3. ok: [az ügynök telepítve legyen a virtuális Géphez, de azok nem válaszoló (a Windows-alapú virtuális gépek)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**4. ok: [a virtuális gépen telepített ügynök elavult (a Linux virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backup-fails-because-the-vm-agent-is-unresponsive"></a>Biztonsági mentés sikertelen lesz, mivel a Virtuálisgép-ügynök nem válaszol

Hibaüzenet: "Nem tudott kommunikálni a Virtuálisgép-ügynök pillanatkép állapot" <br>
Hibakód: "GuestAgentSnapshotTaskStatusError"

Miután regisztrálja, és egy virtuális Gépet az Azure Backup szolgáltatás ütemezése, biztonsági mentés indít el a feladat által a biztonsági mentés Virtuálisgép-bővítmény időpontban pillanatképének elkészítéséhez kommunikál. A következő esetekben előfordulhat, hogy a pillanatkép indított folyamatban. Ha a pillanatkép nem elindul, a biztonsági mentési hiba léphet fel. Fejezze be a következő hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:  
**1. ok: [az ügynök telepítve legyen a virtuális Géphez, de azok nem válaszoló (a Windows-alapú virtuális gépek)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**2. ok: [a virtuális gépen telepített ügynök elavult (a Linux virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [a virtuális gép nem rendelkezik internet-hozzáférés](#the-vm-has-no-internet-access)**  

## <a name="backup-fails-with-an-internal-error"></a>Belső hiba történt a biztonsági mentés sikertelen

Hibaüzenet: "biztonsági mentés egy belső hiba miatt nem sikerült – próbálja megismételni a műveletet, néhány perc múlva" <br>
Hibakód: "BackUpOperationFailed" / "BackUpOperationFailedV2"

Miután regisztrálja, és egy virtuális Gépet az Azure Backup szolgáltatás ütemezése, biztonsági mentés indít el a feladat által a biztonsági mentés Virtuálisgép-bővítmény időpontban pillanatképének elkészítéséhez kommunikál. A következő esetekben előfordulhat, hogy a pillanatkép indított folyamatban. Ha a pillanatkép nem elindul, a biztonsági mentési hiba léphet fel. Fejezze be a következő hibaelhárítási lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet:  
**1. ok: [a virtuális gép nem rendelkezik internet-hozzáférés](#the-vm-has-no-internet-access)**  
**2. ok: [a virtuális Géphez, de a telepített ügynök nem válaszol a (virtuális gépekhez Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**3. ok: [a virtuális gépen telepített ügynök elavult (a Linux virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**4. ok: [pillanatkép állapota nem olvasható, vagy a pillanatkép nem végezhető](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**5. ok: [frissítése vagy nem tölthető be nem sikerül a tartalék mellék](#the-backup-extension-fails-to-update-or-load)**  
**6. ok: [biztonsági mentési szolgáltatás nem rendelkezik megfelelő jogosultsággal a régi helyreállítási pontok egy erőforrás-csoport zárolás miatt törlése](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

## <a name="causes-and-solutions"></a>Okait és megoldásait

### <a name="the-vm-has-no-internet-access"></a>A virtuális gép nem rendelkezik internet-hozzáférés
/ A üzembe helyezése: követelményeknek a virtuális gép nem rendelkezik internet-hozzáféréssel. Vagy előfordulhat, hogy korlátozásokat, amelyeket tagadni a hozzáférést az Azure-infrastruktúra rendelkezik.

Megfelelő működéséhez a biztonsági mentés bővítmény használatához az Azure nyilvános IP-címek hozzáférés szükséges. A bővítmény parancsokat küld a pillanatképek a virtuális gép kezelése az Azure storage végpont (HTTP URL-cím). Ha a kiterjesztés nem fér hozzá a nyilvános interneten, biztonsági mentés végül sikertelen.

Ez azt a VM forgalmat proxykiszolgáló telepíthető.
##### <a name="create-a-path-for-http-traffic"></a>A HTTP-forgalom elérési utat hoz létre

1. Ha korlátozásait a hálózati hely (például a hálózati biztonsági csoport) található, a forgalmat a HTTP-proxy kiszolgáló telepítése
2. Engedélyezi az internet elérését a HTTP-proxy kiszolgáló, a szabályok hozzáadása a hálózati biztonsági csoporthoz, ha rendelkezik ilyennel.

Megtudhatja, hogyan állíthat be egy HTTP-proxy a virtuális gép biztonsági mentésekhez, lásd: [készítse elő a környezetet a biztonsági mentése Azure virtuális gépek](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

A biztonsági mentésben szereplő virtuális gép vagy a proxykiszolgáló, amelyen keresztül a továbbítódik Azure nyilvános IP-címek hozzáférést igényel.

####  <a name="solution"></a>Megoldás
A probléma megoldása érdekében próbálkozzon a következő módszerek egyikét:

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>Engedélyezi a hozzáférést az Azure storage, amely megfelel a régió

Használhat [címkék szolgáltatás](../virtual-network/security-overview.md#service-tags) az adott régió Storage kapcsolatok lehetővé tételéhez. Győződjön meg arról, hogy a szabály, amely lehetővé teszi a hozzáférést a tárfiókhoz magasabb prioritású, mint a szabály adott blokkok internet-hozzáféréssel rendelkező. 

![Hálózati biztonsági csoport régió tárolási címkékkel](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

Szeretné megtudni, a lépéseit szolgáltatás címkék konfigurálásához, tekintse meg a [Ez a videó](https://youtu.be/1EjLQtbKm1M).

> [!WARNING]
> Tárolási szolgáltatás címkék még csak előzetes verziójúak. Elérhetők csak adott régióban. Régiók listáját lásd: [tárolási címkék szolgáltatás](../virtual-network/security-overview.md#service-tags).

Azure által felügyelt lemezek használatakor szükség lehet egy további port megnyitásával (port 8443) a tűzfalat.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Az ügynök telepítve van a virtuális gép, de nem válaszoló (a Windows-alapú virtuális gépek)

#### <a name="solution"></a>Megoldás
A Virtuálisgép-ügynök előfordulhat, hogy sérült, vagy a szolgáltatás esetleg leállt. A Virtuálisgép-ügynök újratelepítése segít a legújabb verzióra. Emellett segít, indítsa újra a szolgáltatással folytatott kommunikáció.

1. Határozza meg, hogy a Windows Vendégügynök szolgáltatás fut a virtuális gép szolgáltatásainak (services.msc). Próbálja meg újraindítani a Windows Vendégügynök szolgáltatás, és a biztonsági mentése.    
2. Ha a Vendégügynök Windows szolgáltatás nem látható a szolgáltatások Vezérlőpulton, folytassa a **programok és szolgáltatások** annak meghatározásához, hogy telepítve van-e a Windows vendég Agent szolgáltatást.
4. Ha megjelenik a Windows-Vendégügynök **programok és szolgáltatások**, a Windows-Vendégügynök eltávolításához.
5. Töltse le és telepítse a [az ügynök MSI legfrissebb verzióját](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultsággal kell rendelkeznie.
6. Győződjön meg arról, hogy megjelenik-e a Windows Vendégügynök services szolgáltatásokban.
7. Egy igény szerinti biztonsági mentés futtatására: 
    * Válassza a portál **biztonsági mentés most**.

Azt is ellenőrizze, hogy [telepítve van a Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) a virtuális gépen. .NET 4.5-ös verziója szükséges a Virtuálisgép-ügynök a szolgáltatással való kommunikációra.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>A virtuális gépen telepített ügynök elavult (a Linux virtuális gépek)

#### <a name="solution"></a>Megoldás
Legtöbb ügynök vagy bővítmény kapcsolódó hibák Linux virtuális gépek elavult Virtuálisgép-ügynök befolyásoló problémákat okozzák. A probléma megoldásához kövesse az alábbi általános irányelveket:

1. Kövesse az utasításokat [Linux ügynök frissítése](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > A Microsoft *erősen ajánlott* frissíteni az ügynököt csak egy terjesztési-tárházat. Nem ajánlott közvetlenül a Githubból a kód letöltése és frissítése. Ha a legújabb ügynököt, hogy a terjesztési nem érhető el, forduljon terjesztési támogatása a telepítésre vonatkozó útmutatást. A legújabb ügynököt ellenőrzéséhez nyissa meg a [Windows Azure Linux ügynök](https://github.com/Azure/WALinuxAgent/releases) a GitHub-tárházban lapján.

2. Győződjön meg arról, hogy az Azure-ügynök fut a virtuális Gépet a következő parancs futtatásával: `ps -e`

 Ha a folyamat nem fut, indítsa újra a következő parancsokkal:

 * Az Ubuntu: `service walinuxagent start`
 * Az egyéb terjesztéseket: `service waagent start`

3. [Konfigurálja az automatikus újraindítás ügynököt](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Egy új teszt biztonsági mentés futtatására. Ha a hiba továbbra is fennáll, gyűjtése a virtuális Gépet a következő naplók kapcsolódnak:

   * /var/lib/waagent/*.XML
   * /var/log/waagent.log
   * /var/log/azure/*

Ha a részletes naplózást az waagent kérjük, kövesse az alábbi lépéseket:

1. A /etc/waagent.conf fájlban keresse meg a következő sort: **részletes naplózás engedélyezése (y |} n)**
2. Módosítsa a **Logs.Verbose** értéket *n* való *y*.
3. A módosítás mentéséhez, és indítsa újra az ebben a szakaszban bemutatott lépések végrehajtásával waagent.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>A pillanatkép állapota nem lehet beolvasni, vagy a pillanatkép nem végezhető
A virtuális gép biztonsági mentése támaszkodik egy pillanatkép parancs kiadása az alapul szolgáló tárolási fiók. Biztonsági mentés sikertelen lehet, mert nem érhető el a tárfiók, vagy mert a pillanatkép-feladat végrehajtása késik.

#### <a name="solution"></a>Megoldás
A következő feltételek előfordulhat, hogy a pillanatkép-feladat meghiúsul:

| Ok | Megoldás |
| --- | --- |
| A virtuális gép állapotát jelentett nem megfelelő, mert a virtuális gép le van állítva a távoli asztal protokoll (RDP). | Ha a virtuális gép RDP a leállítása, ellenőrizze annak meghatározásához, hogy helyesek-e a virtuális gép állapotát a portál. Ha nem megfelelő, használatával állítsa le a virtuális Gépet a portálon a **leállítási** a virtuális gép irányítópulton lehetőséget. |
| A virtuális gép nem tud a gazdagép vagy a háló címet a DHCP-Kiszolgálótól. | A Vendég típusú infrastruktúra-szolgáltatási virtuális gép biztonsági mentésre működéséhez engedélyezni kell a DHCP. Ha a virtuális gép nem olvasható be a gazdagép vagy a háló címét a DHCP-válasz 245, ez nem töltse le, vagy kiterjesztések futtatása. Ha statikus magánhálózati IP-címe van szüksége, konfigurálja úgy a platformon keresztül. A DHCP-beállítást a virtuális Gépen belül balra engedélyezni kell. További információkért lásd: [egy statikus belső privát IP-cím beállítása](../virtual-network/virtual-networks-reserved-private-ip.md). |

### <a name="the-backup-extension-fails-to-update-or-load"></a>A tartalék mellék nem tudja frissíteni vagy nem tölthető be
Bővítmények nem tölthető be, ha a biztonsági mentés meghiúsul, mert a pillanatkép nem végezhető.

#### <a name="solution"></a>Megoldás

Távolítsa el a bővítmény kényszerítése a VMSnapshot bővítmény töltse be újra. A következő biztonsági mentési kísérlet betölti a bővítményt.

A bővítmény eltávolítása:

1. Az a [Azure-portálon](https://portal.azure.com/), keresse fel a virtuális Gépet, amely a biztonsági mentési hibát tapasztal.
2. Válassza ki **beállítások**.
3. Válassza ki **bővítmények**.
4. Válassza ki **Vmsnapshot bővítmény**.
5. Válassza ki **eltávolítása**.

A Linux virtuális gép, ha a VMSnapshot bővítmény nem jeleníti meg az Azure-portálon a [Azure Linux ügynök frissítése](../virtual-machines/linux/update-agent.md), majd futtassa a biztonsági mentés. 

A lépések végrehajtása azt eredményezi, a bővítmény a következő biztonsági mentés során újra kell telepíteni.

### <a name="backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>A biztonsági mentési szolgáltatás nem rendelkezik a régi helyreállítási pontok egy erőforrás-csoport zárolás miatt törléséhez szükséges engedéllyel
Ez a hiba csak a felügyelt virtuális gépekhez, amelyben a felhasználó zárolja az erőforráscsoport elő. Ebben az esetben a biztonsági mentési szolgáltatás régebbi visszaállítási pont nem törölhető. 18 visszaállítási pontok korlátozva van, mert új biztonsági másolatok elindítása sikertelen.

#### <a name="solution"></a>Megoldás

A probléma megoldásához távolítsa el a zárolást az erőforráscsoportot, és kövesse az alábbi lépéseket a visszaállítási pont gyűjtemény eltávolítása: 
 
1. Távolítsa el a zárolást az erőforráscsoportban, ahol a virtuális gép is található. 
2. Telepítse a ARMClient Chocolatey használatával: <br>
   https://github.com/projectkudu/ARMClient
3. Jelentkezzen be ARMClient: <br>
    `.\armclient.exe login`
4. A visszaállítási pont gyűjteményben, amely megfelel a virtuális gép beolvasása: <br>
    `.\armclient.exe get https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30`

    Példa: `.\armclient.exe get https://management.azure.com/subscriptions/f2edfd5d-5496-4683-b94f-b3588c579006/resourceGroups/winvaultrg/providers/Microsoft.Compute/restorepointcollections/AzureBackup_winmanagedvm?api-version=2017-03-30`
5. A visszaállítási pont gyűjtemény törlése: <br>
    `.\armclient.exe delete https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30` 
6. A következő ütemezett biztonsági mentés automatikusan létrehoz egy helyreállítási pont gyűjtemény és az új visszaállítási pontok.

Ha végzett, újra helyezheti vissza a zárolást a Virtuálisgép-csoport. 
