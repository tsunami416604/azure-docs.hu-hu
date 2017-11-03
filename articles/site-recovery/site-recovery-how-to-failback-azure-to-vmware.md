---
title: "Hogyan VMware tudni az Azure-ból |} Microsoft Docs"
description: "Az Azure virtuális gépek feladatátvétel után a feladat-visszavétel a virtuális gépeket kapcsolja vissza a helyszíni is kezdeményezhető. Ismerje meg a szükséges lépéseket a feladat-visszavételt."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: ruturajd
ms.openlocfilehash: 1ca34b262a51b694cb9541750588bbea139eeae1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Az Azure-ból a helyszíni helyhez sikertelen

Ez a cikk ismerteti, hogyan vissza a virtuális gépek az Azure virtuális gépek számára a helyszíni hely sikertelen lesz. Ez a cikk utasításait követve sikertelen biztonsági a VMware virtuális gépeket vagy windowsos/Linuxos fizikai kiszolgálók után azok feladatátvételt is a helyszíni hely Azure használatával hajtsa végre a [replikálása VMware virtuális gépek és fizikai kiszolgálók Azure-bA az Azure Site Recovery](site-recovery-vmware-to-azure-classic.md) oktatóanyag.

> [!WARNING]
> Feladat-visszavétel nem, miután vagy [áttelepítésével kész](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), a virtuális gép áthelyezése a másik erőforráscsoportba vagy törölni az Azure virtuális géphez. Ha letiltja a virtuális gép védelme, feladat-visszavétel nem.

> [!NOTE]
> Ha VMware virtuális gépek átvette majd nem lehet a feladat-visszavétel egy Hyper-v gazdagépen.

## <a name="overview-of-failback"></a>Feladat-visszavétel áttekintése
Ez feladat-visszavétel működése. Miután az Azure-bA rendelkezik nem sikerült keresztül, akkor visszaadják feladataikat a helyszíni hely néhány lépésben:

1. [Lássa el újból védelemmel](site-recovery-how-to-reprotect.md) az Azure virtuális gépeket, hogy elindítja a helyszíni hely VMware virtuális gépeket replikálni. Ez a folyamat részeként szükség:
    1. Egy helyszíni fő célkiszolgáló beállítása: Windows fő célkiszolgáló Windows virtuális gépek és [Linuxos fő célkiszolgáló](site-recovery-how-to-install-linux-master-target.md) Linux virtuális gépekhez.
    2. Állítson be egy [folyamatkiszolgáló](site-recovery-vmware-setup-azure-ps-resource-manager.md).
    3. Kezdeményezzen [lássa el újból védelemmel](site-recovery-how-to-reprotect.md). Ez a helyszíni virtuális gép kikapcsolása, és az Azure virtuális gépek adatainak szinkronizálása a helyszíni lemezeket.
5. Miután a virtuális gépek Azure a helyszíni helyre replikál, elindította egy sikertelen keresztül az Azure-ból a helyszíni hely.

Után az adatok visszaállítása sikertelen volt, akkor állítsa a helyszíni virtuális gépek vissza a megbukott, hogy elindítja az Azure-bA replikálni.

Gyors áttekintését tekintse meg a következő videó bemutatja, hogyan hogy áthelyezze a feladatokat az Azure-ból a helyszíni-helyhez.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="fail-back-to-the-original-or-alternate-location"></a>Visszaadják feladataikat az eredeti vagy egy másik helyre

Ha VMware virtuális gépek feladatátvételt, akkor sikertelen lehet az azonos forrás helyszíni virtuális gépre Ha továbbra is létezik. Ebben a forgatókönyvben csak a módosítások vissza replikálódnak. Ebben a forgatókönyvben az eredeti helyre történő helyreállítást nevezik. Ha a helyszíni virtuális gép nem létezik, akkor a forgatókönyv, egy másodlagos hely helyreállítását.

> [!NOTE]
> Csak a feladat-visszavétel az eredeti vCenter és a konfigurációs kiszolgáló is. Nem telepíthet egy új konfigurációs kiszolgáló és a feladat-visszavétel használja azt. Is akkor nem adható hozzá egy új vCenter a meglévő konfigurációs kiszolgáló és a feladat-visszavétel az új vCenter.

#### <a name="original-location-recovery"></a>Eredeti helyre történő helyreállítás

Ha nem az eredeti virtuális gépre, az alábbi feltételek szükségesek:
* Ha a virtuális gép vCenter-kiszolgáló felügyeli, a fő célkiszolgáló ESX-gazdagépet a virtuális gép hozzáférése kell rendelkeznie.
* Ha a virtuális gépet az ESX-gazdagépen, de nem felügyeli a vCenter, majd a merevlemezt a virtuális gép található a fő célkiszolgáló gazdagép el tudja érni adattárolót kell lennie.
* Ha a virtuális gép ESX-gazdagépen, és nem használja a vCenter, majd el kell végeznie az ESX-gazdagép, a fő célkiszolgáló felderítése ahhoz, hogy lássa el újból védelemmel. Ez igaz, ha a feladat-visszavétele vissza fizikai kiszolgálók túl.
* Sikertelen lehet virtuális tárolóhálózat (vSAN) vagy egy lemezt, amely alapján a mapping (RDM), ha a lemezek már létezik, és a helyszíni virtuális gép csatlakozik nyers eszköz.

#### <a name="alternate-location-recovery"></a>Helyreállítás másik helyre
Ha a helyszíni virtuális gép nem létezik a virtuális gép újbóli védelméhez előtt, a forgatókönyv neve egy másik helyre történő helyreállítást. A védelem-újrabeállítási munkafolyamat újra létrehozza a helyszíni virtuális gépet. Ennek hatására a teljes adatok letöltése.

* Ha nem vissza egy másik helyre, a virtuális gép a azonos ESX-gazdagépet, amelyre telepítve van a fő célkiszolgáló fogja visszaállítani. Az adattároló lemez létrehozásához használt lesz az azonos adattárral választotta, amikor a virtuális gép újbóli védelméhez.
* Sikertelen lehet a vissza csak egy virtuális gép fájlrendszer (virtuálisgép-Fájlrendszereinek) vagy a vSAN-adattároló. Ha egy RDM, védelem-újrabeállítási és feladat-visszavétel fog működni.
* Védelem-újrabeállítási magában foglalja egy nagy kezdeti adatátvitelt, amely a változtatások követi. Ez a folyamat létezik, mert a virtuális gép nem létezik a helyszínen. A teljes adatokat kell annak vissza. A védelem-újrabeállítási veszik az eredeti helyre történő helyreállítás több időt is.
* Nem utasíthat el vissza az RDM-alapú lemezek. Új virtuálisgép-lemezeket (VMDKs) csak a virtuálisgép-Fájlrendszereinek/vSAN adattárolót hozhatók létre.

A fizikai gép, ha az Azure-ba, a feladatátvételt is sikertelen biztonsági csak VMware virtuális gépként (más néven P2A2V). Ez a folyamat a másodlagos hely helyreállítását hatálya alá tartozik.

* Windows Server 2008 R2 SP1 fizikai kiszolgálók, ha a védett, és az Azure-ba, a feladatátvételt nem sikerült vissza.
* Győződjön meg arról, hogy legalább egy fő célkiszolgáló és a szükséges ESX/ESXi-gazdagépek, amelyhez a feladat-visszavételt kell felderíteni.

## <a name="have-you-completed-reprotection"></a>Elvégezte a ismételt védelem?
Mielőtt továbblépne, hajtsa végre a védelem-újrabeállítási, hogy a virtuális gépek replikált állapotban van, és megkezdheti a feladatátvételt egy helyszíni hely. További információkért lásd: [hogyan lássa el újból védelemmel az Azure-ból a helyszíni](site-recovery-how-to-reprotect.md).

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Az Azure-bA a feladatátvétel során a helyszíni hely nem lehet elérni, és ezért a konfigurációs kiszolgáló lehet vagy nem érhető el vagy -leállítás. Védelem-újrabeállítási és a feladat-visszavétel során a helyszíni konfigurációs kiszolgáló fut, és egy csatlakoztatott OK állapotúnak kell lennie.

* Ha így tesz, a feladat-visszavétel a konfigurációs kiszolgáló helyi szükséges. A kiszolgálón futó állapotban kell lennie, és kapcsolódik a szolgáltatáshoz, például úgy, hogy az állapotfigyelő OK gombra. A feladat-visszavétel során a virtuális gép léteznie kell a konfigurációs kiszolgáló adatbázisában, vagy nem sikerült a feladat-visszavétel. Így győződjön meg arról, hogy a rendszeresen ütemezett biztonsági mentések a kiszolgáló is. Hiba történt egy olyan vészhelyzet esetén, ha szüksége lesz a feladat-visszavétel működéséhez azonos IP-címével a kiszolgáló helyreállítása.
* A fő célkiszolgáló nem kell meglévő pillanatképeket védelem-újrabeállítási/feladat-visszavétel elindítása előtt.

## <a name="steps-to-fail-back"></a>A feladat-visszavételt lépései

> [!IMPORTANT]
> Feladat-visszavétel elindítása előtt győződjön meg arról, hogy végrehajtotta-e a virtuális gépek ismételt védelem. A virtuális gépek védett állapotban kell lennie, és meg kell az állapotukat **OK**. Adja meg újra a virtuális gépek védelmét, olvassa el [hogyan állítsa](site-recovery-how-to-reprotect.md).

1. A replikált elemek oldalon válassza ki a virtuális gépet, és kattintson a jobb gombbal, válassza ki, hogy **nem tervezett feladatátvétel**.
2. A **megerősítéséhez feladatátvétel**, ellenőrizze a feladatátvételi irányát (az Azure-ból), és válassza ki a helyreállítási pontot (legújabb vagy a legutóbbi alkalmazáskonzisztens), amely a feladatátvételre használni kívánt. Az alkalmazás konzisztens pont mögött a legutóbbi pontnak időben, ami adatvesztést.
3. A feladatátvétel során a Site Recovery Azure virtuális gépek leáll. Után ellenőrizheti, hogy feladat-visszavétel a várt módon befejeződött, ellenőrizheti, hogy a virtuális gépek Azure-be lett zárva.

### <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Milyen helyreállítási pontra is szeretnék a feladat-visszavételt a virtuális gépek?

A feladat-visszavétel során történő feladat-visszavételt a virtuális gép vagy helyreállítási terv két választási lehetősége van.

Ha a legújabb feldolgozott pont időben, az összes virtuális gép átvétele fog megtörténni a legutóbbi pontnak időben. Ha a helyreállítási terv belül replikációs csoport van, majd a replikációs csoport minden egyes virtuális gép hajt végre feladatátvételt a független legújabb pontjára időben.

Nem utasíthat el újra a virtuális gépek amíg rá nem kényszerül legalább egy helyreállítási pontot. Nem utasíthat el újra a helyreállítási terv mindaddig, amíg az összes virtuális gép rendelkezik legalább egy helyreállítási pontot.

> [!NOTE]
> A legutóbbi helyreállítási pontot az összeomlás-konzisztens helyreállítási pontot.

Ha a alkalmazáskonzisztens helyreállítási pontnak, egyetlen virtuális gép feladat-visszavétel a legutóbbi elérhető alkalmazáskonzisztens helyreállítási pontnak állítja helyre. A replikációs csoport helyreállítási tervet, ha minden replikációs csoport a közös elérhető helyreállítási pont állítja helyre.
Ne feledje, hogy időben alkalmazáskonzisztens helyreállítási pontokra lehet mögött, előfordulhat adatvesztés adatokat.

### <a name="what-happens-to-vmware-tools-post-failback"></a>Mi történik a VMware-eszközök feladás egy vagy több feladat-visszavétel?

Az Azure-bA a feladatátvétel során a VMware-eszközök nem működik az Azure virtuális gépen. Esetén a Windows rendszerű virtuális gép automatikus rendszer-Helyreállítás letiltja a VMware-eszközök a feladatátvétel során. Linux virtuális gép esetén az ASR eltávolítása a VMware-eszközök a feladatátvétel során.

A Windows virtuális gép feladat-visszavétel során a VMware-eszközök újbóli engedélyezéséig a feladat-visszavétel. Ehhez hasonlóan a linux virtuális gép, a VMware-eszközök újratelepíti a gépen feladat-visszavétel során.

## <a name="next-steps"></a>Következő lépések

Feladat-visszavétel befejezése után kell véglegesíti a virtuális gépet, hogy ellenőrizze, hogy törölték-e a helyreállított virtuális gépek Azure-ban.

### <a name="commit"></a>Véglegesítés
Távolítsa el a feladatokat átadó virtuális gép az Azure-ból véglegesítési szükséges.
Kattintson a jobb gombbal a védett elem, és kattintson **véglegesítése**. Egy feladat fog távolítsa el a sikertelen virtuális gépeket az Azure-ban.

### <a name="reprotect-from-on-premises-to-azure"></a>Állítsa a helyszíni Azure-bA

Véglegesítési befejezése után a virtuális gép vissza a helyszíni helyen, de azt nem lehet védetté tenni. Indítsa el újra az Azure-bA replikálni, tegye a következőket:

1. A **tároló** > **beállítás** > **replikált elemek**, válassza ki a visszaállítása sikertelen volt, és kattintson a virtuális gépek **védelmének újbóli beállításához**.
2. Az érték a folyamatkiszolgáló, amelyet a adatokat küldhet vissza az Azure ad.
3. Kattintson a **OK** a védelem-újrabeállítási feladat megkezdéséhez.

> [!NOTE]
> Miután egy helyszíni virtuális gép elindul, csak bizonyos idő az ügynök regisztrálása a konfigurációs kiszolgálóra (legfeljebb 15 perc). Ebben az időszakban a védelem-újrabeállítási sikertelen lesz, és egy hibaüzenetet jelenít meg arról, hogy az ügynök nincs telepítve. Várjon néhány percet, és próbálkozzon újra az védelem-újrabeállítási.

A védelem-újrabeállítási feladat befejezése után a virtuális gép replikálódik vissza az Azure-ba, és elvégezhető a feladatátvétel.

## <a name="common-issues"></a>Gyakori problémák
Győződjön meg arról, hogy a vCenter csatlakoztatott állapotban van, a feladat-visszavétel előtt. Lemez leválasztása és csatolva a virtuális gépre ellenkező esetben sikertelen lesz.
