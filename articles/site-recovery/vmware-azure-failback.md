---
title: VMware virtuális gépek és fizikai kiszolgálók visszavétele az Azure-ból az Azure Site Recovery szolgáltatással
description: Ismerje meg, hogyan lehet feladatátvételt a helyszíni hely után feladatátvétel az Azure-ba, a VMware virtuális gépek és a fizikai kiszolgálók azure-ba vészhelyreállítása során.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495327"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>VMware virtuális gépek visszavétele a helyszíni helyre

Ez a cikk ismerteti, hogyan lehet feladat-vissza az Azure-beli virtuális gépek egy helyszíni helyen, miután a helyszíni virtuális gépek az Azure-ba az [Azure Site Recovery](site-recovery-overview.md) [feladatátvételt](site-recovery-failover.md) követően. A helyszíni feladat-visszavétel után engedélyezi a replikációt, hogy a helyszíni virtuális gépek replikálni kezdjék az Azure-ba.

## <a name="before-you-start"></a>Előkészületek

1. További információ a [VMware-feladat-visszavételről.](failover-failback-overview.md#vmwarephysical-reprotectionfailback) 
2. Győződjön meg arról, hogy áttekintette és elvégezte a [feladat-visszavételre való felkészülés](vmware-azure-prepare-failback.md)lépéseit, és hogy az összes szükséges összetevő telepítve van. Az összetevők közé tartozik egy folyamatkiszolgáló az Azure-ban, egy helyszíni fő célkiszolgáló és egy VPN-hely közötti kapcsolat (vagy ExpressRoute privát társviszony-létesítés) a feladat-visszavételhez.
3. Győződjön meg arról, hogy teljesítette az újravédelem és a feladat-visszavétel [követelményeit,](vmware-azure-reprotect.md#before-you-begin) és hogy engedélyezte az Azure virtuális gépek [újravédelmét,](vmware-azure-reprotect.md#enable-reprotection) hogy azok replikálódjanak az Azure-ból a helyszíni webhelyre. A virtuális gépeknek replikált állapotban kell lenniük, hogy visszavethessék.




## <a name="run-a-failover-to-fail-back"></a>Feladatátvétel futtatása a feladat-visszavételhez

1. Győződjön meg arról, hogy az Azure virtuális gépek újra védett, és replikálása a helyszíni helyen. 
    - A virtuális gépnek legalább egy helyreállítási pontra van szüksége a visszavételhez.
    - Ha visszaad egy helyreállítási tervet, akkor a tervben lévő összes gépnek rendelkeznie kell legalább egy helyreállítási ponttal.
2. A tárolóban > **replikált elemeket,** válassza ki a virtuális gép. Kattintson a jobb gombbal a **nem tervezett feladatátvétel**> virtuális gépre.
3. A **Feladatátvétel megerősítése**területen ellenőrizze a feladatátvétel irányát (az Azure-ból).
4. Válassza ki a feladatátvételhez használni kívánt helyreállítási pontot.
    - Azt javasoljuk, hogy használja a **legújabb** helyreállítási pontot. Az alkalmazáskonzisztens pont a legutóbbi időpont mögött van, és némi adatvesztést okoz.
    - **A Legújabb** egy összeomlás-konzisztens helyreállítási pont.
    - A **Legújabb**, a virtuális gép átadja a legújabb rendelkezésre álló időpontban. Ha rendelkezik egy replikációs csoport több virtuális gép konzisztenciájához egy helyreállítási terven belül, a csoport minden virtuális gépe átadja a független legutóbbi időpontban.
    - Ha egy alkalmazás-konzisztens helyreállítási pontot használ, minden virtuális gép nem felel meg a legújabb elérhető pont. Ha egy helyreállítási terv rendelkezik replikációs csoporttal, minden csoport helyreáll a közös rendelkezésre álló helyreállítási pontra.
5. Megkezdődik a feladatátvétel. A Site Recovery leállítja az Azure virtuális gépeket.
6. A feladatátvétel befejezése után ellenőrizze, hogy minden a várt módon működik-e. Ellenőrizze, hogy az Azure-beli virtuális gépek le vannak-e állítva. 
7. Minden ellenőrzött, kattintson a jobb gombbal a virtuális gép > **commit**, a feladatátvételi folyamat befejezéséhez. Commit eltávolítja a sikertelen Azure virtuális gép. 

> [!NOTE]
> Windows virtuális gépek esetén a Site Recovery letiltja a VMware-eszközöket feladatátvétel közben. A Windows virtuális gép feladat-visszavétele során a VMware-eszközök újra engedélyeznek. 




## <a name="reprotect-from-on-premises-to-azure"></a>A helyszíni és az Azure-beli védelem újbóli védelme

A feladat-visszavétel véglegesítése után az Azure virtuális gépek törlődnek. A virtuális gép visszatért a helyszíni helyen, de nem védett. A virtuális gépek azure-ba történő replikálásának újratörténő replikálása az alábbiak szerint:

1. A tárolóban > **Replikált elemeket,** jelölje ki a sikertelen virtuális gépeket, majd kattintson **az Újravédelem gombra.**
2. Adja meg a folyamatkiszolgálót, amely az adatok visszaküldéséhez szolgál az Azure-ba.
3. Az **ÚJRAVÉDELMI** feladat megkezdéséhez válassza az OK gombot.

> [!NOTE]
> A helyszíni virtuális gép indítása után akár 15 percet is igénybe vesz, amíg az ügynök regisztráljon vissza a konfigurációs kiszolgálóra. Ez alatt az idő alatt az újravédelmi sikertelen lesz, és hibaüzenetet ad arról, hogy az ügynök nincs telepítve. Ebben az esetben várjon néhány percet, majd védje újra.

## <a name="next-steps"></a>További lépések

Miután az újravédelmi feladat befejeződik, a helyszíni virtuális gép replikálja az Azure-ba. Szükség esetén [futtathat egy másik feladatátvételt](site-recovery-failover.md) az Azure-ba.

