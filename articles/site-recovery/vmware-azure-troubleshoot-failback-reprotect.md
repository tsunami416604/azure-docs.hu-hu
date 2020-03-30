---
title: A vmware-i virtuális gép vész-helyreállítási szolgáltatásának hibaelhárítása az Azure Site Recovery szolgáltatással
description: Ez a cikk ismerteti, hogyan hárítsa el a feladat-visszavételi és újravédelmi problémák során VMware virtuális gép vész-helyreállítási azure-beli Azure-beli Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b577b82585ffad0547818b4f19554a2f39cb830c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498106"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Azure-ból helyszíni rendszerbe történő feladat-visszavétel hibaelhárítása

Ez a cikk ismerteti, hogyan háríthatja el azokat a problémákat, amelyek akkor fordulhatnak elő, amikor az Azure-beli virtuális gépeket visszaadja a helyszíni VMware-infrastruktúrának, miután az Azure Site Recovery használatával feladatátvételt hajtott végre az [Azure-ba.](site-recovery-overview.md)

A feladat-visszavétel lényegében két fő lépést foglal magában. Az első lépés, miután feladatátvétel, újra kell védenie az Azure-beli virtuális gépek a helyszíni, hogy azok replikálása. A második lépés az Azure-ból a helyszíni helyre való feladatátvétel futtatása.

## <a name="common-issues"></a>Gyakori problémák

- Ha csak olvasható felhasználói vCenter-felderítést hajt végre, és védi a virtuális gépeket, a védelem sikeres, és a feladatátvétel működik. Az újravédelem során a feladatátvétel sikertelen, mert az adattárak nem fedezhetők fel. A tünet az, hogy az adattárak nem szerepelnek az újravédelem során. A probléma megoldásához frissítheti a vCenter hitelesítő adatait egy megfelelő, engedélyekkel rendelkező fiókkal, majd próbálkozzon újra a feladattal.
- Amikor visszaad egy Linux-virtuális gépet, és a helyszínen futtatja, láthatja, hogy a Hálózatkezelő csomagot eltávolították a gépről. Ez az eltávolítás oka az, hogy a Hálózatkezelő csomag eltávolításra kerül, amikor a virtuális gép helyreáll az Azure-ban.
- Ha egy Linux virtuális gép statikus IP-címmel van konfigurálva, és az Azure-ba kerül a feladat, az IP-cím a DHCP-től lesz beszerezve. Amikor átmegy a helyszíni feladatátvételre, a virtuális gép továbbra is dhcp-t használ az IP-cím megszerzéséhez. Manuálisan jelentkezzen be a számítógépre, majd szükség esetén állítsa vissza az IP-címet statikus címre. A Windows virtuális gépek ismét beszerezhetik a statikus IP-címet.
- Ha az ESXi 5.5 ingyenes kiadását vagy a vSphere 6 Hypervisor ingyenes kiadást használja, a feladatátvétel sikeres, de a feladat-visszavétel nem sikerül. A feladat-visszavétel engedélyezéséhez frissítsen bármelyik program kiértékelési licencre.
- Ha nem tudja elérni a konfigurációs kiszolgálót a folyamatkiszolgálóról, a Telnet segítségével ellenőrizze a kapcsolatot a 443-as porton lévő konfigurációs kiszolgálóval. Megpróbálhatja pingelni a konfigurációs kiszolgálót a folyamatkiszolgálóról is. A folyamatkiszolgálónak szívveréssel is rendelkeznie kell, amikor az a konfigurációs kiszolgálóhoz csatlakozik.
- A fizikai helyszíni kiszolgálóként védett Windows Server 2008 R2 SP1 kiszolgáló nem lehet visszakerülni az Azure-ból egy helyszíni helyre.
- A következő körülmények között nem lehet visszakapni:
    - Gépeket telepített át az Azure-ba. [További információ](migrate-overview.md#what-do-we-mean-by-migration).
    - Virtuális gép áthelyezése egy másik erőforráscsoportba.
    - Törölte az Azure virtuális gép.
    - Letiltotta a virtuális gép védelmét.
    - A virtuális gép manuálisan az Azure-ban. A gép kellett volna kezdetben védett a helyszínen, és feladatátvételt az Azure-ban, mielőtt újravédelem.
    - Nem lehet csak egy ESXi host. A Virtuálisgép-gyártók virtuális gépek et vagy fizikai kiszolgálókat nem lehet feladat-visszavételre használni a Hyper-V gazdagépekre, fizikai gépekre vagy VMware-munkaállomásokra.


## <a name="troubleshoot-reprotection-errors"></a>Újravédelmi hibák elhárítása

Ez a szakasz részletezi a gyakori újravédelmi hibákat, és hogyan lehet kijavítani őket.

### <a name="error-code-95226"></a>95226-os hibakód

**Az újravédelem nem sikerült, mert az Azure virtuális gép nem tudta elérni a helyszíni konfigurációs kiszolgálót.**

Ez a hiba akkor fordul elő, ha:

* Az Azure virtuális gép nem tudja elérni a helyszíni konfigurációs kiszolgáló. A virtuális gép nem fedezhető fel és regisztrálható a konfigurációs kiszolgálóra.
* Az InMage Scout alkalmazásszolgáltatás nem fut az Azure virtuális gép feladatátvétel után. A szolgáltatás szükséges a helyszíni konfigurációs kiszolgálóval való kommunikációhoz.

A probléma megoldása:

* Ellenőrizze, hogy az Azure virtuális gép hálózatlehetővé teszi-e az Azure virtuális gép kommunikálni a helyszíni konfigurációs kiszolgálóval. Beállíthat egy helyek közötti VPN-t a helyszíni adatközpontba, vagy konfigurálhat egy Azure ExpressRoute-kapcsolatot az Azure virtuális gép virtuális hálózatának virtuális hálózatán a privát társviszony-létesítéssel.
* Ha a virtuális gép képes kommunikálni a helyszíni konfigurációs kiszolgálóval, jelentkezzen be a virtuális gépbe. Ezután ellenőrizze az InMage Scout alkalmazásszolgáltatást. Ha úgy látja, hogy nem fut, indítsa el a szolgáltatást manuálisan. Ellenőrizze, hogy a szolgáltatás indítási típusa **Automatikus.**

### <a name="error-code-78052"></a>78052-es hibakód

**A virtuális gép elleni védelem nem fejezhető be.**

Ez a probléma akkor fordulhat elő, ha már van egy virtuális gép ugyanazzal a névvel a fő célkiszolgálón, amelynek a visszaadott.

A probléma megoldása:

* Válasszon másik fő célkiszolgálót egy másik állomáson, hogy az újravédelem egy másik állomáson hozza létre a gépet, ahol a nevek nem ütköznek.
* A vMotion segítségével a fő célt egy másik állomásra is áthelyezheti, ahol a névütközés nem fog bekövetkezni. Ha a meglévő virtuális gép egy kóbor gép, nevezze át úgy, hogy az új virtuális gép ugyanazon az ESXi-gazdagépen hozható létre.


### <a name="error-code-78093"></a>78093-as hibakód

**A virtuális gép nem fut, nem lógott állapotban, vagy nem érhető el.**

A probléma megoldása:

A sikertelen virtuális gép újbóli védelméhez az Azure virtuális gépnek futnia kell, hogy a Mobility Service regisztráljon a helyszíni konfigurációs kiszolgálóval, és a folyamatkiszolgálóval való kommunikációval megkezdheti a replikálást. Ha a számítógép nem megfelelő hálózaton van, vagy nem fut (nem válaszol vagy leáll), a konfigurációs kiszolgáló nem tudja elérni a mobilszolgáltatás a virtuális gépen az újravédelem megkezdéséhez.

* Indítsa újra a virtuális gép, hogy meg tudja kezdeni a kommunikációt a helyszíni kommunikáció.
* Indítsa újra az újravédelmi feladatot az Azure virtuális gép indítása után.

### <a name="error-code-8061"></a>8061-es hibakód

**Az adattár nem érhető el az ESXi host-tól.**

Ellenőrizze a fő cél előfeltételeit és a [támogatott adattárak](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) a feladat-visszavétel.


## <a name="troubleshoot-failback-errors"></a>Feladat-visszavételi hibák elhárítása

Ez a szakasz a feladat-visszavétel során előforduló gyakori hibákat ismerteti.

### <a name="error-code-8038"></a>8038-as hibakód

**A hiba miatt nem sikerült a helyszíni virtuális gép.**

Ez a probléma akkor fordul elő, ha a helyszíni virtuális gép egy olyan gazdagépen jelenik meg, amely nem rendelkezik elegendő memóriával. 

A probléma megoldása:

* Több memória kiépítése az ESXi gazdagépen.
* Emellett a vMotion segítségével áthelyezheti a virtuális gép egy másik ESXi-gazdagépbe, amely elegendő memóriával rendelkezik a virtuális gép indításához.
