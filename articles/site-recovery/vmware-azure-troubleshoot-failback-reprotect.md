---
title: "Azure virtuális gépek és az Azure Site Recovery a helyszíni VMware közötti feladat-visszavétel során hibák elhárítása |} Microsoft Docs"
description: "Ez a cikk ismerteti azokat a módszereket hibaelhárítás általános feladat-visszavétel és ismételt védelem feladat-visszavétel során VMware Azure Site Recovery segítségével, az Azure-ból."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2017
ms.author: rajanaki
ms.openlocfilehash: 1c54ae96273880caede1f50f3a0705c41f15f26e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>A feladat-visszavétel az Azure-ból VMware hibaelhárítása

Ez a cikk ismerteti, ha nem sikerül vissza Azure virtuális gépek a helyszíni VMware-infrastruktúra az Azure használatával feladatátvételt követően előforduló hibák elhárítása [Azure Site Recovery](site-recovery-overview.md).

Feladat-visszavétel lényegében két fő lépést foglal magában. Feladatátvétel után kell Azure virtuális gépek állítsa a helyszínen, így a replikálást indítani. A második lépése, hogy feladatátvételt visszaadják feladataikat a helyszíni hely számára az Azure-ból. 

## <a name="troubleshoot-reprotection-errors"></a>Ismételt védelem hibák elhárítása

Ez a szakasz részletesen gyakran ismételt védelem hibákat, és hogyan javítsa ki azokat.

### <a name="error-code-95226"></a>95226. Hibakód:

**Védelem-újrabeállítási sikertelen volt, mert a virtuális gépet az Azure nem tudta elérni a helyi konfigurációs kiszolgálót.**

Ez a hiba akkor fordul elő, ha:

1. Az Azure virtuális gép nem érhető el a helyszíni konfigurációs kiszolgáló. A virtuális gép nem felderítése, és a konfigurációs kiszolgáló regisztrálva. 
2. Az InMage Scout alkalmazásszolgáltatás nem fut az Azure virtuális gép a feladatátvételt követően. A szolgáltatás a helyi kiszolgáló kommunikációjához szükséges.

A probléma megoldásához:

1. Ellenőrizze, hogy az Azure Virtuálisgép-hálózat lehetővé teszi, hogy az Azure virtuális Gépen a helyi konfigurációs kiszolgálóval való kommunikációhoz. Ehhez az szükséges, állítsa be a helyszíni adatközpontját a pont-pont VPN, vagy ExpressRoute kapcsolat konfigurálása a magánhálózati társviszony-létesítés az Azure virtuális gép a virtuális hálózaton. 
2. Ha a virtuális gép kommunikálhat a helyszíni konfigurációs kiszolgáló, majd jelentkezzen be a virtuális Gépet, és ellenőrizze a "InMage Scout alkalmazásszolgáltatás". Ha azt látja, hogy nem fut, indítsa el kézzel a szolgáltatást, és ellenőrizze, hogy a szolgáltatás indítási típusa automatikusra van beállítva.

### <a name="error-code-78052"></a>78052. Hibakód:

***Nem sikerült engedélyezni a virtuális gép védelmét.**

Ez akkor fordulhat elő, ha már van egy virtuális Gépet, a fő célkiszolgálón, amelyhez visszavétele vissza ugyanazzal a névvel.

Ez a probléma tegye a következőket megoldása:
1. Válassza ki egy másik fő célkiszolgáló egy másik gazdagépen, így ismételt védelem hoz létre a gépet egy másik gazdagépet, ahol a nevek nem ütköznek. 
2. A fő célkiszolgáló egy másik gazdagépet, amelyen a névütközés nem történik meg a vMotion is. Ha a meglévő virtuális gép szétszórt, nevezze át azt, hogy az új virtuális gép ugyanazon az ESXi-állomáson is létrehozható.

### <a name="error-code-78093"></a>78093. Hibakód:

**A virtuális gép nem fut, lefagyott állapotú, vagy nem érhető el.**

A állítsa át a virtuális gép egy sikertelen, az Azure virtuális gép futnia kell. Ez azért, hogy a mobilitási szolgáltatás regisztrálja a konfigurációs kiszolgáló a helyszínen, és megkezdheti a replikáló által a folyamat kiszolgálóval való kommunikáció során. Ha a gép helytelen hálózatra, vagy (lefagyott állam vagy -leállítás) nem fut, a a konfigurációs kiszolgáló nem tudja elérni a mobilitási szolgáltatást a virtuális gép ismételt védelem megkezdéséhez a. 

1. Indítsa újra a virtuális gép, így hátsó helyszíni kommunikáció elindíthatja.
2. Az Azure virtuális gép elindítása után a védelem-újrabeállítási feladat újraindításához

### <a name="error-code-8061"></a>8061. Hibakód:

**Az adattároló nem érhető el az ESXi-állomáson.**
 
Ellenőrizze a [fő cél Előfeltételek és a támogatott adattárolókhoz](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) feladat-visszavételre.


## <a name="troubleshoot-failback-errors"></a>Feladat-visszavétel hibák elhárítása

Ez a szakasz ismerteti a feladat-visszavétel során esetleg előforduló gyakori hibák.

### <a name="error-code-8038"></a>8038. Hibakód:

**Nem sikerült elindítani a helyszíni virtuális gép a hiba miatt**

Ez akkor fordul elő, ha a helyszíni virtuális Gépet olyan gazdagépre, amely nem rendelkezik elegendő memóriával kiépítése nem válik. A probléma megoldásához:

1. Több memóriát az ESXi-állomáson kiépítéséhez.
2. Emellett a virtuális Gépet egy másik ESXi-állomáson, amelyen elegendő memóriával a virtuális gép rendszerindító vMotion is.
