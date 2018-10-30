---
title: Hibaelhárítás a feladat-visszavételhez helyszíni VMware virtuális gép vészhelyreállítás az Azure-bA az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk ismerteti azokat a módszereket, feladat-visszavétel és ismételt védelem hibáinak elhárítása az Azure-bA az Azure Site Recovery VMware virtuális gép vészhelyreállítás során.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: 89484e14d69493c74ed1ff983cf88ec146e0313d
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210363"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>A helyszíni feladat-visszavétel az Azure-ból – hibaelhárítás

Ez a cikk ismerteti az Azure virtuális gépek átadja az Azure-bA a feladatátvételt követően a helyszíni VMware-infrastruktúra előforduló hibáinak elhárítása [Azure Site Recovery](site-recovery-overview.md).

Feladat-visszavétel lényegében két fő lépésből áll. Az első lépés a feladatátvételt követően kell ismételt védelem – Azure virtuális gépek a helyszíni, hogy megkezdődhessen a replikálás. A második lépés, hogy futtasson egy feladatátvételi feladat-visszavételhez a helyszíni hely Azure-ból.

## <a name="troubleshoot-reprotection-errors"></a>Ismételt védelem kapcsolatos hibák elhárítása

Ez a szakasz részletesen az ismételt védelem előforduló gyakori hibák és a rendszernaplóban őket.

### <a name="error-code-95226"></a>95226. Hibakód:

**Az ismételt védelem sikertelen volt, mert az Azure virtuális gép nem tudta elérni a helyszíni konfigurációs kiszolgálót.**

Ez a hiba akkor fordul elő, ha:

* Az Azure virtuális gép nem tudja elérni a helyszíni konfigurációs kiszolgálót. A virtuális gép nem felderíthető és a konfigurációs kiszolgáló regisztrálva.
* Az InMage Scout alkalmazásszolgáltatás nem fut az Azure virtuális gép a feladatátvételt követően. A szolgáltatás a helyszíni konfigurációs kiszolgálóval való kommunikációhoz szükséges.

A probléma megoldásához:

* Ellenőrizze, hogy az Azure Virtuálisgép-hálózat lehetővé teszi, hogy az Azure virtuális Gépen a helyszíni konfigurációs kiszolgálóval való kommunikációhoz. A site-to-site VPN beállítása a helyszíni adatközponthoz, vagy az Azure ExpressRoute-kapcsolatok konfigurálása a privát társviszony-létesítést az Azure virtuális gépek a virtuális hálózaton.
* Ha a virtuális gép kommunikálhat a helyszíni konfigurációs kiszolgálót, jelentkezzen be a virtuális géphez. Ezután ellenőrizze az InMage Scout application szolgáltatást. Ha látja, hogy nem fut, indítsa el manuálisan a szolgáltatást. Ellenőrizze, hogy a szolgáltatás indítási típus beállítása **automatikus**.

### <a name="error-code-78052"></a>78052. Hibakód:

**A virtuális gép védelme nem sikerült befejezni.**

A probléma akkor fordulhat elő, ha már van egy virtuális Gépet, amelyhez Ön éppen visszavétel a fő célkiszolgáló ugyanazzal a névvel.

A probléma megoldásához:

* Válassza ki egy másik fő célkiszolgálót egy másik gazdagépen, így ismételt védelem hoz létre a gépen egy másik gazdagépre, ahol a nevek nem ütköznek.
* Emellett használhatja vMotion a fő célkiszolgáló áthelyezése egy másik gazdagépre, ahol a névütközést nem történik meg. Ha a meglévő virtuális gép egy szétszórt gép, nevezze át, hogy az új virtuális gép ugyanazon az ESXi-gazdagépen hozható létre.


### <a name="error-code-78093"></a>78093. Hibakód:

**A virtuális gép nem fut, lefagyott állapotban, vagy nem érhető el.**

A probléma megoldásához:

Egy átvevő virtuális gép ismételt védelme, hogy az Azure virtuális Gépen kell futnia, hogy a mobilitási szolgáltatás regisztrál a konfigurációs kiszolgálót a helyszínen és a is replikáljon által a folyamat-kiszolgálóval való kommunikációhoz. Ha a gép egy hibás hálózati vagy nem fut (lefagyott állapotban vagy a Leállítás le), a konfigurációs kiszolgáló nem érhető el a mobilitási szolgáltatás a virtuális gépen az ismételt védelem megkezdéséhez.

* Indítsa újra a virtuális Gépet, így kommunikál a helyszíni elindul.
* Indítsa újra az ismételt védelmi feladat, az Azure virtuális gép elindítása után.

### <a name="error-code-8061"></a>8061. Hibakód:

**Az adattárhoz az ESXi-gazdagép nem érhető el.**

Ellenőrizze a [fő cél Előfeltételek és a támogatott adattárak](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) a feladat-visszavételhez.


## <a name="troubleshoot-failback-errors"></a>Feladat-visszavétel kapcsolatos hibák elhárítása

Ez a szakasz ismerteti a feladat-visszavétel során esetleg felmerülő gyakori hibák.

### <a name="error-code-8038"></a>8038. Hibakód:

**Csatlakozva a helyszíni virtuális gép a hiba miatt nem sikerült.**

A hiba akkor fordul elő, amikor a helyszíni virtuális gép egy gazdagépen, amely nem rendelkezik elegendő kiosztott memória van állapotba. 

A probléma megoldásához:

* Építhet ki több memóriát az ESXi-gazdagépen.
* Emellett a vMotion használatával a virtuális gép áthelyezése egy másik ESXi-gazdagép, amely a virtuális gép elegendő memóriával rendelkezik.
