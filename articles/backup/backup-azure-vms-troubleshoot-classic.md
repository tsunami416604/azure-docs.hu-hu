---
title: "A klasszikus portálon az Azure biztonsági mentési hibák elhárítása |} Microsoft Docs"
description: "Végezzen hibaelhárítást az Azure biztonsági mentés és visszaállítás az Azure virtuális gépek a klasszikus portálon."
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 117201fb-c0cd-4be4-b47f-abd88fe914cf
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: trinadhk;markgal;
ms.openlocfilehash: 284a1b64fbb15d0aa800182c6671d447e191b76a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Azure-beli virtuális gépek biztonsági mentésének hibaelhárítása
> [!div class="op_single_selector"]
> * [Recovery services-tároló](backup-azure-vms-troubleshoot.md)
> * [Mentési tároló](backup-azure-vms-troubleshoot-classic.md)
>
>

Észlelt, miközben az Azure Backup segítségével információkat az alábbi táblázatban szereplő hibák is elháríthatók.

## <a name="discovery"></a>Felderítés
| Biztonsági mentési művelet | Hiba legutolsó részletes adatai | Megkerülő megoldás |
| --- | --- | --- |
| Felderítés |Nem sikerült felderíteni az új elemek - észlelt a Microsoft Azure biztonsági mentés és a belső hiba. Várjon néhány percet, és próbálkozzon újra a művelettel. |15 perc múlva próbálkozzon újra a felderítési folyamat. |
| Felderítés |Nem sikerült felderíteni az új cikkek – egy másik felderítési művelet már folyamatban van. Várjon, amíg a jelenlegi felderítési művelet befejeződik. |None |

## <a name="register"></a>Regisztráljon
| Biztonsági mentési művelet | Hiba legutolsó részletes adatai | Megkerülő megoldás |
| --- | --- | --- |
| Regisztráljon |A virtuális géphez csatolt adatlemezek száma meghaladta a megengedett értéket – adja válassza le a virtuális gépen néhány adatlemezek, és próbálja megismételni a műveletet. Azure biztonsági mentés támogatja a biztonsági mentéshez egy Azure virtuális géphez legfeljebb 16 adatlemez |None |
| Regisztráljon |A Microsoft Azure Backup szolgáltatás belső hibába ütközött - Várjon, amíg az néhány percet, majd próbálja megismételni a műveletet. Ha nem szűnik meg a probléma, forduljon a Microsoft támogatási szolgálatához. |Ez a hiba miatt a virtuális gépet a következő nem támogatott konfiguráció egyikét a prémium szintű LRS érheti el. <br> Prémium szintű storage virtuális gépek biztonsági másolat készíthető a recovery services-tároló segítségével. [További információ](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup) |
| Regisztráljon |Nem sikerült regisztrálni az ügynök telepítése a művelet időkorlátja |Ellenőrizze, hogy ha a virtuális gép operációsrendszer-verziója támogatott. |
| Regisztráljon |Parancs végrehajtása nem sikerült – a elem folyamatban van egy másik művelet. Várjon, amíg az előző művelet befejezése |None |
| Regisztráljon |Prémium szintű storage a tárolt virtuális merevlemezek rendelkező virtuális gépek nem támogatottak a biztonsági mentéshez |None |
| Regisztráljon |Virtuálisgép-ügynök nincs jelen a virtuális gépen – telepítse a szükséges előfeltételt, az ügynököt, és indítsa újra a műveletet. |[További](#vm-agent) Virtuálisgép-ügynök telepítése, és a Virtuálisgép-ügynök telepítésének ellenőrzése. |

## <a name="backup"></a>Biztonsági mentés
| Biztonsági mentési művelet | Hiba legutolsó részletes adatai | Megkerülő megoldás |
| --- | --- | --- |
| Biztonsági mentés |Nem sikerült a pillanatkép állapotával kapcsolatos kommunikáció a virtuális gépeket kezelő ügynökkel. Pillanatkép virtuális gép sub feladat túllépte az időkorlátot. – Tekintse meg a hibaelhárítási útmutató a probléma megoldásához hogyan. |Ez a hiba fordul elő, ha a probléma oka a Virtuálisgép-ügynök vagy az Azure-infrastruktúra hálózati hozzáférését blokkolja a valamilyen módon. További információ [virtuális gépet hibakeresés pillanatkép-problémák](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md). <br> Ha a Virtuálisgép-ügynök nem okoz probléma merül fel, majd indítsa újra a virtuális Gépet. Esetenként a virtuális gép állapota nem megfelelő problémákat okozhatnak, és a virtuális gép újraindítása alaphelyzetbe állítja a "hibás állapot" |
| Biztonsági mentés |Biztonsági mentés egy belső hiba miatt nem sikerült – a művelet néhány perc múlva próbálkozzon újra. Ha a probléma továbbra is fennáll, forduljon a Microsoft Support |Győződjön meg arról, ha van egy átmeneti hiba Virtuálisgép-tároló elérése közben. Győződjön meg róla [Azure állapot](https://azure.microsoft.com/en-us/status/) van-e bármilyen folyamatos probléma compute/tárolási vagy hálózati régióban kapcsolódik. Próbálja megismételni a biztonsági mentés utáni probléma elhárítására. |
| Biztonsági mentés |Nem sikerült elvégezni a műveletet, mert a virtuális gép már nem létezik. |Biztonsági mentés nem végezhető el, mert a biztonsági mentéshez konfigurált virtuális gép törölve lett. Állítsa le a további biztonsági mentések Ha védett elemek nézetében, válassza ki a védett elem, és kattintson a védelem kikapcsolása. Adatok megőrzése biztonsági mentési adatok lehetőség kiválasztásával őrizheti meg. Később folytathatja a védelmet a virtuális gép kattintva a konfigurálás regisztrált elemek nézetében elleni védelem |
| Biztonsági mentés |Nem sikerült telepíteni az Azure Recovery Services bővítmény a kijelölt elem - Virtuálisgép-ügynök Azure Recovery Services-bővítmény előzetesen szükséges. Telepítse az Azure Virtuálisgép-ügynök, majd indítsa újra a regisztrációs műveletet |<ol> <li>Ellenőrizze, hogy ha a Virtuálisgép-ügynök megfelelően van telepítve. <li>Győződjön meg arról, hogy a virtuális gép konfigurációs jelzőt megfelelően van-e állítva.</ol> [További](#validating-vm-agent-installation) Virtuálisgép-ügynök telepítése, és a Virtuálisgép-ügynök telepítésének ellenőrzése. |
| Biztonsági mentés |Parancs végrehajtása nem sikerült – egy másik művelet jelenleg folyamatban van erre az elemre. Várjon, amíg az előző művelet befejeződik, majd próbálkozzon újra |Egy meglévő biztonsági mentési vagy visszaállítási feladat a virtuális gép fut, és egy új feladat nem indítható el a meglévő feladat futása közben. |
| Biztonsági mentés |Bővítmény telepítése nem sikerült a "COM + nem tudta kérdezze meg a Microsoft Distributed Transaction Coordinator |Ez általában azt jelenti, hogy nem fut a COM + szolgáltatást. A probléma megoldásán segítségért forduljon a Microsoft támogatási szolgálatához. |
| Biztonsági mentés |Pillanatkép művelet nem sikerült a VSS művelet "ezen a meghajtón a BitLocker meghajtótitkosítás zárolta. Ezen a meghajtón, a Vezérlőpult kell zárolásának feloldásához. |Kapcsolja ki a BitLocker összes merevlemezén a virtuális Gépre, és figyelje meg, ha a VSS probléma megoldódott-e. |
| Biztonsági mentés |Prémium szintű storage a tárolt virtuális merevlemezek rendelkező virtuális gépek nem támogatottak a biztonsági mentéshez |None |
| Biztonsági mentés |Az Azure virtuális gép nem található. |Ez akkor fordul elő, ha az elsődleges virtuális gép törlődik, de a biztonsági mentési házirend továbbra is fennáll, készítsen biztonsági mentést a virtuális gépek kereséséhez. Ez a hiba elhárításához: <ol><li>Hozza létre újra a virtuális gépet az azonos nevű és ugyanazon erőforráscsoport neve [cloud service name] <br>(VAGY) <li> Tiltsa le a virtuális gép védelmét, így további biztonsági mentések nem indul beolvasása. </ol> |
| Biztonsági mentés |Virtuálisgép-ügynök nincs jelen a virtuális gépen – telepítse a szükséges előfeltételt, az ügynököt, és indítsa újra a műveletet. |[További](#vm-agent) Virtuálisgép-ügynök telepítése, és a Virtuálisgép-ügynök telepítésének ellenőrzése. |

## <a name="jobs"></a>Feladatok
| Művelet | Hiba legutolsó részletes adatai | Megkerülő megoldás |
| --- | --- | --- |
| Megszakítása |Megszakítása nem támogatott ilyen típusú feladatokat - Várjon, amíg a feladat befejeződik. |None |
| Megszakítása |A feladat nem törölhető állapotban van,-várjon, amíg a feladat befejeződik. <br>VAGY<br> A kiválasztott feladat nem törölhető állapotban van,-várjon a feladat végrehajtásához. |A feladat minden valószínűség szerint, majdnem kész; Várjon, amíg a feladat befejezése után |
| Megszakítása |A feladatot nem lehet megszakítani, mert nincs folyamatban – törlését csak a támogatott feladatok, amely jelenleg folyamatban vannak. Adjon kísérlet megszakítása az egy folyamatban lévő feladat. |Ez akkor fordul elő egy átmeneti állapota miatt. Várjon egy percet, majd próbálja megismételni a visszavonási művelet |
| Megszakítása |Nem sikerült visszavonni a feladatot - Várjon, amíg a feladat befejeződik. |None |

## <a name="restore"></a>Visszaállítás
| Művelet | Hiba legutolsó részletes adatai | Megkerülő megoldás |
| --- | --- | --- |
| Visszaállítás |Visszaállítás felhő belső hiba miatt sikertelen volt |<ol><li>A felhőalapú szolgáltatás, amelyhez visszaállítani kívánt DNS-beállításokkal van konfigurálva. Ellenőrizheti a <br>$deployment get-AzureDeployment - szolgáltatásnév "ServiceName" =-tárolóhely "Éles" Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Ha nincs konfigurált címet, ez azt jelenti, hogy a DNS-beállítások vannak konfigurálva.<br> <li>Felhőalapú szolgáltatás, amelyre szeretné visszaállítani kívánt foglalt IP-cím van konfigurálva, és a meglévő virtuális gépek által a felhőalapú szolgáltatás leállított állapotban van.<br>Ellenőrizheti a felhőszolgáltatás van foglalt IP-cím a következő powershell-parancsmagok használatával:<br>$deployment = get-AzureDeployment - szolgáltatásnév "servicename"-"Éles" $tárolóhely DEP ReservedIPName <br><li>Állítsa vissza a következő speciális hálózati beállításokat a virtuális gép ugyanazon a felhőalapú szolgáltatás kívánt. <br>– Virtuális gépek a terheléselosztó-konfigurációja (külső és belső)<br>-A virtuális gépek a több foglalt IP-cím<br>-A virtuális gépek több hálózati adapterrel rendelkező<br>Válasszon ki egy új felhőalapú szolgáltatást a felhasználói felületen, vagy tekintse meg [szempontok visszaállítása](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations) speciális hálózati konfigurációk rendelkező virtuális gépek</ol> |
| Visszaállítás |A kijelölt DNS-név már használatban van – adjon meg egy másik DNS-nevet, és próbálkozzon újra. |A DNS-neve itt hivatkozik a felhőszolgáltatás neve (általában végződő. cloudapp.net). Ennek egyedinek kell lennie. Ha ezt a hibát észlel, akkor válasszon egy másik Virtuálisgép-nevet a visszaállítás során. <br><br> Ez a hiba csak az Azure-portálon a felhasználók számára látható. A visszaállítási műveletet az PowerShell sikeres lesz, mert csak visszaállítja a lemezeket, és nem hoz létre a virtuális Gépet. A hiba akkor lesz tapasztalt, a virtuális gép létrehozásakor explicit módon Ön által a lemezre visszaállítási művelet után. |
| Visszaállítás |Érvénytelen a megadott virtuális hálózati konfiguráció – adjon meg egy másik virtuális hálózati konfiguráció, és próbálkozzon újra. |None |
| Visszaállítás |A megadott felhőszolgáltatás egy fenntartott IP-cím, amelyek nem felelnek meg a visszaállítandó virtuális gép konfigurációs – adja meg egy másik felhőalapú szolgáltatást, amely nem használja a foglalt IP-cím, vagy válasszon másik helyreállítási pontot a visszaállítandó használ. |None |
| Visszaállítás |A felhőalapú szolgáltatás elérte a bemeneti végpontok számára vonatkozó korlátozást, mert a másik felhőalapú szolgáltatást, vagy egy meglévő végpont használatával, próbálja megismételni a műveletet. |None |
| Visszaállítás |Biztonsági mentési tároló és a cél tárfiók két különböző régiókban – győződjön meg arról, hogy a visszaállítási művelet a megadott tárfiók ugyanabban a régióban Azure a biztonsági mentési tárolóval. |None |
| Visszaállítás |Tárfiók megadott a visszaállítási művelet nem támogatott – a storage-fiókok csak Basic vagy Standard helyileg redundáns vagy földrajzi redundancia replikációs beállítások támogatottak. Válasszon egy támogatott tárfiókot |None |
| Visszaállítás |A visszaállítási művelet a megadott Tárfiók típus nem online – győződjön meg arról, hogy online állapotban-e a visszaállítási művelet a megadott tárfiók |Ez azért fordulhat elő, az Azure Storage, illetve a nem tervezett kimaradás átmeneti hiba miatt. Válasszon másik tárolási fiókot. |
| Visszaállítás |Erőforráscsoport kvóta elérve - töröljön egyes erőforráscsoportok Azure-portálon, vagy lépjen kapcsolatba az Azure támogatási szolgálatától a korlátok növelését. |None |
| Visszaállítás |Nem létezik a kijelölt alhálózat – válasszon ki egy alhálózatot, amely létezik |None |

## <a name="policy"></a>Szabályzat
| Művelet | Hiba legutolsó részletes adatai | Megkerülő megoldás |
| --- | --- | --- |
| Házirend létrehozása |Nem sikerült létrehozni a házirend - csökkentse a megőrzési lehetőségek folytatja a házirend-konfigurációt. |None |

## <a name="vm-agent"></a>Virtuálisgép-ügynök
### <a name="setting-up-the-vm-agent"></a>A Virtuálisgép-ügynök beállítása
A Virtuálisgép-ügynök általában már szerepel az Azure katalógusában a létrehozott virtuális gépeket. A helyszíni adatközpontját a áttelepített virtuális gépek azonban nem áll a Virtuálisgép-ügynök van telepítve. Ilyen virtuális gépek a Virtuálisgép-ügynök telepítve kell lennie explicit módon. Tudjon meg többet az [a Virtuálisgép-ügynök telepítését meglévő virtuális](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Windows virtuális gépek:

* Töltse le és telepítse az [ügynök MSI-t](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek.
* [Frissítse a virtuális gép tulajdonságát](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), hogy megjelenjen a telepített ügynök.

Linux virtuális gépek:

* Telepítse a legújabb [Linux-ügynök](https://github.com/Azure/WALinuxAgent) a githubról.
* [Frissítse a virtuális gép tulajdonságát](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), hogy megjelenjen a telepített ügynök.

### <a name="updating-the-vm-agent"></a>A virtuálisgép-ügynök frissítése
Windows virtuális gépek:

* A virtuálisgép-ügynök frissítése a [virtuálisgép-ügynök bináris fájljainak](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) újratelepítéséből áll. Azonban meg kell győződjön meg arról, hogy nincs biztonsági mentési művelet fut. a Virtuálisgép-ügynök frissítése közben.

Linux virtuális gépek:

* Kövesse az utasításokat [frissítése Linux Virtuálisgép-ügynök](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="validating-vm-agent-installation"></a>Virtuálisgép-ügynök telepítésének ellenőrzése
A Virtuálisgép-ügynök verziója, a Windows virtuális gépek ellenőrzésének módja:

1. Jelentkezzen be az Azure virtuális géphez, és lépjen abba a mappába *C:\WindowsAzure\Packages*. Itt találja a WaAppAgent.exe fájlt.
2. Kattintson jobb gombbal a fájlra, válassza a **Tulajdonságok** parancsot, majd nyissa meg a **Részletek** lapot. A termék verziószáma mező lehet 2.6.1198.718 vagy újabb
