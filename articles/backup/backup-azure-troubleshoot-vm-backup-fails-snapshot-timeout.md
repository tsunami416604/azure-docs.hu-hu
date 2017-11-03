---
title: "Azure biztonsági mentési hiba elhárítása: Vendég ügynök állapota nem érhető el |} Microsoft Docs"
description: "A jelenség, okait és Azure biztonsági mentési hibák ügynök, a bővítmény, a lemezek kapcsolódó megoldások"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
keywords: "Az Azure backup; Virtuálisgép-ügynök; Hálózati kapcsolat;"
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: genli;markgal;
ms.openlocfilehash: f3195fa83479986a3e605abce618c78bcdb64dac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-backup-failure-issues-with-agent-andor-extension"></a>Azure biztonsági mentési hiba elhárítása: ügynök és/vagy kiterjesztés problémái

A cikkben az ügynök és Virtuálisgép-bővítmény kommunikációs a problémával kapcsolatos biztonsági mentési hibák elhárításához hibaelhárítási lépéseket.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>Nem lehet kommunikálni az Azure biztonsági mentés Virtuálisgép-ügynök
Miután regisztrálja, és egy virtuális Gépet az Azure Backup szolgáltatás a ütemezése, a biztonsági mentés kezdeményezi a a feladat úgy pont időponthoz kötött pillanatképet készít a virtuális gép ügynökkel folytatott kommunikáció. A következő feltételek megakadályozhatja, hogy a pillanatfelvételt a elindul, ami viszont biztonsági mentés sikertelen. Kövesse az alábbi lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>1. ok: [a virtuális gép nincs Internet-hozzáféréssel rendelkező](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>2. ok: [az ügynök telepítve van-e a virtuális Géphez, de nem válaszol a (virtuális gépekhez Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>3. ok: [a virtuális gépen telepített ügynök elavult (a Linux virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>4. ok: [pillanatkép állapotát nem sikerült beolvasni vagy pillanatkép nem végezhető.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>5. ok: [frissítése vagy nem tölthető be nem sikerül a tartalék mellék](#the-backup-extension-fails-to-update-or-load)

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Pillanatkép-művelet meghiúsult, mert nincs hálózati kapcsolat a virtuális gépen
Miután regisztrálja, és egy virtuális Gépet az Azure Backup szolgáltatás ütemezése, biztonsági mentés indít el a feladat által a biztonsági mentés Virtuálisgép-bővítmény időpontban pillanatképének elkészítéséhez kommunikál. A következő feltételek megakadályozhatja, hogy a pillanatfelvételt a elindul, ami viszont biztonsági mentés sikertelen. Kövesse az alábbi lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>1. ok: [a virtuális gép nincs Internet-hozzáféréssel rendelkező](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>2. ok: [pillanatkép állapotát nem sikerült beolvasni vagy pillanatkép nem végezhető.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-3-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>3. ok: [frissítése vagy nem tölthető be nem sikerül a tartalék mellék](#the-backup-extension-fails-to-update-or-load)

## <a name="vmsnapshot-extension-operation-failed"></a>VMSnapshot bővítmény művelete sikertelen volt

Miután regisztrálja, és egy virtuális Gépet az Azure Backup szolgáltatás ütemezése, biztonsági mentés indít el a feladat által a biztonsági mentés Virtuálisgép-bővítmény időpontban pillanatképének elkészítéséhez kommunikál. A következő feltételek megakadályozhatja, hogy a pillanatfelvételt a elindul, ami viszont biztonsági mentés sikertelen. Kövesse az alábbi lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet.
##### <a name="cause-1-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>1. ok: [pillanatkép állapotát nem sikerült beolvasni vagy pillanatkép nem végezhető.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-2-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>2. ok: [frissítése vagy nem tölthető be nem sikerül a tartalék mellék](#the-backup-extension-fails-to-update-or-load)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>3. ok: [a virtuális gép nincs Internet-hozzáféréssel rendelkező](#the-vm-has-no-internet-access)
##### <a name="cause-4-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>4. ok: [az ügynök telepítve van-e a virtuális Géphez, de nem válaszol a (virtuális gépekhez Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-5-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>5. ok: [a virtuális gépen telepített ügynök elavult (a Linux virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)

## <a name="unable-to-perform-the-operation-as-the-vm-agent-is-not-responsive"></a>Nem sikerült végrehajtani a műveletet, a Virtuálisgép-ügynök nem válaszol

Miután regisztrálja, és egy virtuális Gépet az Azure Backup szolgáltatás ütemezése, biztonsági mentés indít el a feladat által a biztonsági mentés Virtuálisgép-bővítmény időpontban pillanatképének elkészítéséhez kommunikál. A következő feltételek megakadályozhatja, hogy a pillanatfelvételt a elindul, ami viszont biztonsági mentés sikertelen. Kövesse az alábbi lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet.
##### <a name="cause-1-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>1. ok: [az ügynök telepítve van-e a virtuális Géphez, de nem válaszol a (virtuális gépekhez Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-2-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>2. ok: [a virtuális gépen telepített ügynök elavult (a Linux virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>3. ok: [a virtuális gép nincs Internet-hozzáféréssel rendelkező](#the-vm-has-no-internet-access)

## <a name="backup-failed-with-an-internal-error---please-retry-the-operation-in-a-few-minutes"></a>Biztonsági mentés egy belső hiba miatt nem sikerült – próbálja megismételni a műveletet, néhány perc múlva

Miután regisztrálja, és egy virtuális Gépet az Azure Backup szolgáltatás ütemezése, biztonsági mentés indít el a feladat által a biztonsági mentés Virtuálisgép-bővítmény időpontban pillanatképének elkészítéséhez kommunikál. A következő feltételek megakadályozhatja, hogy a pillanatfelvételt a elindul, ami viszont biztonsági mentés sikertelen. Kövesse az alábbi lépéseket a megadott sorrendben, majd próbálja megismételni a műveletet.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>1. ok: [a virtuális gép nincs Internet-hozzáféréssel rendelkező](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-installed-in-the-vm-but-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>2. ok: [az ügynök telepítve azonban nem válaszol a virtuális gép (Windows virtuális gépek)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>3. ok: [a virtuális gépen telepített ügynök elavult (a Linux virtuális gépek)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>4. ok: [pillanatkép állapotát nem sikerült beolvasni vagy pillanatkép nem végezhető.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>5. ok: [frissítése vagy nem tölthető be nem sikerül a tartalék mellék](#the-backup-extension-fails-to-update-or-load)

## <a name="the-specified-disk-configuration-is-not-supported"></a>A megadott lemezkonfiguráció nem támogatott.

Azure Backup szolgáltatás jelenleg nem támogatja lemezméret [1023GB-nál nagyobb](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm). 
- Ha a lemez 1 TB-nál nagyobb [új lemez csatolása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) 1 Terabájtnál kisebb ezek <br>
- Ezután másolja újonnan létrehozott lemez(ek) 1 TB-nál kisebb méretű lemez 1TB-nál nagyobb az adatokat. <br>
- Győződjön meg arról, hogy minden adat másolta, és távolítsa el a nagyobb, mint 1 TB-os lemezeken
- A biztonsági mentése

## <a name="causes-and-solutions"></a>Okait és megoldásait

### <a name="the-vm-has-no-internet-access"></a>A virtuális gép nincs Internet-hozzáféréssel rendelkező
Az üzembe helyezése: követelményeknek a virtuális gép nincs Internet-hozzáféréssel rendelkező, vagy korlátozások miatt helyen tagadni a hozzáférést az Azure-infrastruktúra rendelkezik.

A megfelelő működéshez, a tartalék mellék használatához az Azure nyilvános IP-címek hozzáférés szükséges. A bővítmény parancsokat küld egy Azure Storage végpontot (HTTP URL-cím) a pillanatképek a virtuális gépek kezeléséhez. Ha a kiterjesztés nem érhető el a nyilvános interneten, biztonsági mentés végül sikertelen.

####  <a name="solution"></a>Megoldás
A probléma megoldása érdekében próbálkozzon az alábbi módszerek valamelyikét.
##### <a name="allow-access-to-the-azure-datacenter-ip-ranges"></a>Az Azure datacenter IP-címtartományok hozzáférés engedélyezése

1. Szerezze be a [Azure datacenter IP-címek listája](https://www.microsoft.com/download/details.aspx?id=41653) eléréséhez.
2. Az IP-címek feloldása futtatásával a **New-NetRoute** parancsmagot egy emelt szintű PowerShell-ablakban az Azure virtuális gépen. A parancsmag futtatása rendszergazdaként.
3. Az IP-címek való hozzáférés engedélyezéséhez szabályok hozzáadása a hálózati biztonsági csoporthoz, ha rendelkezik ilyennel.

##### <a name="create-a-path-for-http-traffic-to-flow"></a>A HTTP-forgalom áramlását az elérési utat hoz létre

1. Ha korlátozásait a hálózati hely (például a hálózati biztonsági csoport) található, a forgalmat a HTTP-proxy kiszolgáló telepítése
2. Engedélyezi az Internet elérését a HTTP-proxy kiszolgáló, a szabályok hozzáadása a hálózati biztonsági csoporthoz, ha rendelkezik ilyennel.

Megtudhatja, hogyan állíthat be egy HTTP-proxy a virtuális gép biztonsági mentésekhez, lásd: [készítse elő a környezetet a biztonsági mentése Azure virtuális gépek](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

Abban az esetben felügyelt lemezt használ, szükség lehet egy további port (8443) nyitása a tűzfalon a.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Az ügynök telepítve azonban nem válaszol a virtuális gép (Windows virtuális gépek)

#### <a name="solution"></a>Megoldás
A Virtuálisgép-ügynök előfordulhat, hogy sérült, vagy a szolgáltatás esetleg leállt. A Virtuálisgép-ügynök újbóli telepítése segít az a legújabb verzióra, és indítsa újra a kommunikációt.

1. Ellenőrizze, hogy a virtuális gép (services.msc) szolgáltatásban futó Windows Vendégügynök szolgáltatás. Próbálja meg, indítsa újra a Vendégügynök Windows-szolgáltatást, és a biztonsági mentése<br>
2. Ha nem látható a szolgáltatások, ellenőrizze a programok és szolgáltatások, hogy Windows vendég ügynökszolgáltatás telepítve van.
4. Ha Ön megtekintheti a Programok telepítése és szolgáltatások a Windows-Vendégügynök eltávolításához.
5. Töltse le és telepítse a [ügynök MSI legfrissebb verzióját](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek.
6. Ezután meg kell Windows Vendégügynök services szolgáltatások megtekintése
7. Próbálja meg futtatni egy a-igény szerinti/ad hoc biztonsági mentés "Biztonsági mentés most" gombra kattintva a portálon.

Emellett ellenőrizze a virtuális gép rendelkezik  **[a rendszeren telepítve a .NET 4.5](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)**. Szükséges, hogy a Virtuálisgép-ügynök a szolgáltatással való kommunikációra az

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>A virtuális gépen telepített ügynök elavult (a Linux virtuális gépek)

#### <a name="solution"></a>Megoldás
Legtöbb ügynök vagy bővítmény kapcsolódó hibák Linux virtuális gépek elavult Virtuálisgép-ügynök befolyásoló problémákat okozzák. A probléma megoldásához kövesse az alábbi általános irányelveket:

1. Kövesse az utasításokat [Linux ügynök frissítése](../virtual-machines/linux/update-agent.md).

 >[!NOTE]
 >A Microsoft *erősen ajánlott* frissíteni az ügynököt csak egy terjesztési-tárházat. Nem ajánlott közvetlenül a Githubból a kód letöltése és frissítése. Ha a legújabb-ügynök nem érhető el a terjesztéshez, ügyfélszolgálatához terjesztési kapcsolatos utasításokat a telepítéshez. A legújabb ügynököt ellenőrzéséhez nyissa meg a [Windows Azure Linux ügynök](https://github.com/Azure/WALinuxAgent/releases) a GitHub-tárházban lapján.

2. Győződjön meg arról, hogy az Azure-ügynök fut a virtuális Gépet a következő parancs futtatásával:`ps -e`

 Ha a folyamat nem fut, indítsa újra a következő parancsokkal:

 * Az Ubuntu:`service walinuxagent start`
 * Az egyéb terjesztéseket:`service waagent start`

3. [Konfigurálja az automatikus újraindítás ügynököt](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Egy új teszt biztonsági mentés futtatására. Ha a hiba továbbra is fennáll, gyűjtse össze a következő naplók kapcsolódnak az ügyfél virtuális gépről:

   * /var/lib/waagent/*.XML
   * /var/log/waagent.log
   * / var/jelentkezzen/azure / *

Ha a részletes naplózást az waagent kérjük, kövesse az alábbi lépéseket:

1. A /etc/waagent.conf fájlban keresse meg a következő sort: **részletes naplózás engedélyezése (y |} n)**
2. Módosítsa a **Logs.Verbose** értéket  *n*  való *y*.
3. A módosítás mentéséhez, és indítsa újra waagent ebben a szakaszban az előző lépéseket követve.

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>A pillanatkép állapotát nem sikerült beolvasni vagy a pillanatkép nem végezhető
A virtuális gép biztonsági mentése támaszkodik egy pillanatkép parancs kiadása az alapul szolgáló tárolási fiók. Biztonsági mentés sikertelen lehet, mert nem érhető el a tárfiók, vagy a pillanatkép-feladat végrehajtása késik.

#### <a name="solution"></a>Megoldás
A következő okok miatt jöhet létre pillanatkép-feladat sikertelensége:

| Ok | Megoldás |
| --- | --- |
| A virtuális Gépnek legyen beállítva SQL Server biztonsági másolat. | Alapértelmezés szerint a virtuális gép biztonsági mentése fut a VSS teljes biztonsági mentés a Windows virtuális gépeken. A virtuális gépeken futó SQL Server-alapú kiszolgálók és az SQL Servert mentés konfigurálva van, pillanatkép végrehajtási késések fordulhat elő.<br><br>Ha a biztonsági mentés hibát tapasztal egy pillanatkép probléma miatt, állítsa be a következő beállításkulcsot:<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE"** |
| A virtuális gép állapotát jelentett nem megfelelő, mert a virtuális gép RDP a leállítása. | Ha leállítja a virtuális gép távoli asztal protokoll (RDP), ellenőrizze, helyes-e a virtuális gép állapotának meghatározásához a portálon. Ha nem megfelelő, használatával állítsa le a virtuális Gépet a portálon a **leállítási** a virtuális gép irányítópulton lehetőséget. |
| Azonos felhőalapú szolgáltatásából sok virtuális gép biztonsági mentése egy időben vannak konfigurálva. | Akkor célszerű felülbírálásokkal a mentési ütemezések virtuális gépek azonos felhőalapú szolgáltatásából. |
| A virtuális gép fut, magas CPU és memória-használatot. | Ha a virtuális gép magas CPU-használat (több mint 90 %-) vagy a magas memóriahasználatban fut, a pillanatkép-feladat várólistára, és késleltetett, és végül időtúllépéssel leáll. Ilyen esetben próbálkozzon egy igény szerinti biztonsági mentést. |
| A virtuális gép nem tud a gazdagép/háló címet a DHCP-Kiszolgálótól. | A Vendég típusú infrastruktúra-szolgáltatási virtuális gép biztonsági mentésre működéséhez engedélyezni kell a DHCP.  Ha a virtuális gép nem olvasható be a gazdagép/háló címet a DHCP-válasz 245, ez nem töltse le, vagy kiterjesztések futtatása. Ha statikus magánhálózati IP-címe van szüksége, úgy kell beállítania, a platform keresztül. A DHCP-beállítást a virtuális Gépen belül balra engedélyezni kell. További információkért lásd: [egy statikus belső privát IP-cím beállítása](../virtual-network/virtual-networks-reserved-private-ip.md). |

### <a name="the-backup-extension-fails-to-update-or-load"></a>A tartalék mellék nem tudja frissíteni vagy nem tölthető be
Nem tölthetők be bővítmények, ha a biztonsági mentés meghiúsul, mert a pillanatkép nem végezhető.

#### <a name="solution"></a>Megoldás

**A Windows-vendégek:** győződjön meg arról, hogy a iaasvmprovider szolgáltatás engedélyezve van, és indítási típust *automatikus*. Ha a szolgáltatás nem így van konfigurálva, engedélyezheti annak meghatározásához, hogy a következő biztonsági mentés sikeres.

**A Linux-vendégek:** VMSnapshot legújabb verziójának ellenőrzése, Linux (a biztonsági mentés által használt kiterjesztés) érték 1.0.91.0.<br>


Ha a tartalék mellék még mindig nem tudja frissíteni vagy nem tölthető be, kényszerítheti az VMSnapshot bővítményt, hogy a bővítmény eltávolításával töltve. A következő biztonsági mentési kísérlet újrabetöltést végez a bővítményt.

Távolítsa el a bővítményt, tegye a következőket:

1. Nyissa meg az [Azure Portal](https://portal.azure.com/).
2. Keresse meg a virtuális Gépet, amely a biztonsági mentési problémái vannak.
3. Kattintson a **beállítások**.
4. Kattintson a **bővítmények**.
5. Kattintson a **Vmsnapshot bővítmény**.
6. Kattintson a **eltávolítása**.

Ez az eljárás azt eredményezi, a bővítmény a következő biztonsági mentés során újra kell telepíteni.

