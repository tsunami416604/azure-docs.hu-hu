---
title: "Azure virtuális gépek és az Azure Site Recovery a helyszíni VMware közötti feladat-visszavétel során hibák elhárítása |} Microsoft Docs"
description: "Ez a cikk ismerteti azokat a módszereket hibaelhárítás általános feladat-visszavétel és ismételt védelem feladat-visszavétel során VMware az Azure-ból Azure Site Recovery segítségével."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: rajanaki
ms.openlocfilehash: 480c3524ad4fb8a8c6ea02f09b8d27f254da9b08
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>A feladat-visszavétel az Azure-ból VMware hibaelhárítása

Ez a cikk ismerteti, ha nem sikerül vissza Azure virtuális gépek számára a helyszíni VMware-infrastruktúra az Azure-bA a feladatátvételt követően előforduló hibák elhárítása [Azure Site Recovery](site-recovery-overview.md).

Feladat-visszavétel lényegében két fő lépést foglal magában. Az első lépés a feladatátvétel után kell állítsa a helyszíni Azure virtuális gépeken, így replikálást indítani. A második lépése, hogy feladatátvételt az a visszaadják feladataikat a helyszíni hely Azure-ból.

## <a name="troubleshoot-reprotection-errors"></a>Ismételt védelem hibák elhárítása

Ez a szakasz részletesen gyakran ismételt védelem előforduló hiba és azok megoldására.

### <a name="error-code-95226"></a>95226. Hibakód:

**Védelem-újrabeállítási sikertelen volt, mert a virtuális gépet az Azure nem tudta elérni a helyi konfigurációs kiszolgálót.**

Ez a hiba akkor fordul elő, ha:

* Az Azure virtuális gép nem érhető el a helyszíni konfigurációs kiszolgáló. A virtuális gép nem felderítése és a konfigurációs kiszolgáló regisztrálva.
* Az InMage Scout alkalmazásszolgáltatás nem fut az Azure virtuális gép a feladatátvételt követően. A szolgáltatás a helyi kiszolgáló kommunikációjához szükséges.

A probléma megoldásához:

* Ellenőrizze, hogy az Azure Virtuálisgép-hálózat lehetővé teszi, hogy az Azure virtuális Gépen a helyi konfigurációs kiszolgálóval való kommunikációhoz. A helyszíni adatközpontját a telephelyek közötti VPN beállítása, vagy Azure ExpressRoute kapcsolat konfigurálása a magánhálózati társviszony-létesítés az Azure virtuális gép a virtuális hálózaton.
* Ha a virtuális gép kommunikálhat a helyszíni konfigurációs kiszolgáló, jelentkezzen be a virtuális Gépet. Ellenőrizze az InMage Scout alkalmazásszolgáltatás. Ha azt látja, hogy nem fut, indítsa el kézzel a szolgáltatást. Ellenőrizze, hogy a szolgáltatás indítási típus értéke **automatikus**.

### <a name="error-code-78052"></a>78052. Hibakód:

**Nem sikerült engedélyezni a virtuális gép védelmét.**

A probléma akkor fordulhat elő, ha már van egy virtuális Gépet, a fő célkiszolgálón, amelyhez visszavétele vissza ugyanazzal a névvel.

A probléma megoldásához:

* Válassza ki egy másik fő célkiszolgáló egy másik gazdagépen, így ismételt védelemmel ellátni azt egy másik gazdagépet, ahol a nevek nem ütköznek a gép hoz létre.
* Is használhatja vMotion a fő célkiszolgáló áthelyezése egy másik gazdagépen, ahol nem történik meg az ütközést. Ha a meglévő virtuális gép szétszórt, nevezze át azt, hogy az új virtuális gép ugyanazon az ESXi-állomáson is létrehozható.


### <a name="error-code-78093"></a>78093. Hibakód:

**A virtuális gép nem fut, lefagyott állapotú, vagy nem érhető el.**

A probléma megoldásához:

Állítsa a virtuális gép feladatátadása, az Azure virtuális Gépen kell futnia, hogy a mobilitási szolgáltatás regisztrál a konfigurációs kiszolgáló a helyszíni és a részleg által a folyamat kiszolgálóval való kommunikáció replikálást indítani. Ha a gépet egy megfelelő hálózati, vagy nem futnak (lefagyott állapot vagy a leállítási le), a kiszolgáló nem érhető el a virtuális gépek ismételt védelem megkezdéséhez a mobilitási szolgáltatást.

* Indítsa újra a virtuális gép, így hátsó helyszíni kommunikáció elindíthatja.
* Indítsa újra a védelem-újrabeállítási feladat, az Azure virtuális gép elindítása után.

### <a name="error-code-8061"></a>8061. Hibakód:

**Az adattároló nem érhető el az ESXi-állomáson.**

Ellenőrizze a [fő cél Előfeltételek és a támogatott adattárolókhoz](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) feladat-visszavételre.


## <a name="troubleshoot-failback-errors"></a>Feladat-visszavétel hibák elhárítása

Ez a szakasz ismerteti a feladat-visszavétel során esetleg előforduló gyakori hibák.

### <a name="error-code-8038"></a>8038. Hibakód:

**Nem sikerült elindítani a helyszíni virtuális gépet a hiba miatt.**

A probléma történik, ha a helyszíni virtuális Gépet olyan gazdagépre, amely nem rendelkezik elegendő memóriával kiépítése nem válik. 

A probléma megoldásához:

* Több memóriát az ESXi-állomáson kiépítéséhez.
* Ezenkívül vMotion használatával a virtuális gép áthelyezése egy másik ESXi-állomáson indítsa el a virtuális gép elegendő memóriával rendelkezik.
