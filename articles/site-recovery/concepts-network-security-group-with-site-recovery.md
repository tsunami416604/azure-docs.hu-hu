---
title: Hálózati biztonsági csoportok az Azure Site Recovery szolgáltatással | Microsoft dokumentumok
description: A hálózati biztonsági csoportok használata az Azure Site Recovery szolgáltatással a vészhelyreállításhoz és az áttelepítéshez
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: eb5ba99133f5726c44164b0ba45b7ab5d94e292f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292364"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Hálózati biztonsági csoportok az Azure site recovery szolgáltatással

A hálózati biztonsági csoportok a hálózati forgalmat a virtuális hálózat erőforrásaira korlátozzák. A [hálózati biztonsági csoport (NSG)](../virtual-network/security-overview.md#network-security-groups) tartalmazza azon biztonsági szabályok listáját, amelyek engedélyezik vagy megtagadják a bejövő vagy kimenő hálózati forgalmat a forrás- vagy célIP-cím, -port és -protokoll alapján.

Az Erőforrás-kezelő telepítési modellje szerint az NSG-k alhálózatokhoz vagy egyes hálózati adapterekhez társíthatók. Ha az NSG-t hozzárendelik egy alhálózathoz, a szabályok érvényesek lesznek az alhálózathoz csatlakozó összes erőforrásra. A forgalmat tovább korlátozhatja, ha az NSG-t egy olyan alhálózaton belül lévő egyes hálózati adapterekhez társítják, amelyekhez már tartozik az NSG.

Ez a cikk ismerteti, hogyan használhatja a hálózati biztonsági csoportok az Azure Site Recovery.

## <a name="using-network-security-groups"></a>Hálózati biztonsági csoportok használata

Egy adott alhálózat rendelkezhet nulla, vagy egy társított NSG. Az egyes hálózati adapterek hez nulla vagy egy társított NSG is tartozhat. Így hatékonyan kettős forgalomkorlátozást hozhat létre egy virtuális géphez, ha először egy NSG-t társít egy alhálózathoz, majd egy másik NSG-t a virtuális gép hálózati felületéhez. Az NSG-szabályok alkalmazása ebben az esetben a forgalom irányáttól és az alkalmazott biztonsági szabályok elsőbbségétől függ.

Vegyünk egy egyszerű példát egy virtuális géppel az alábbiak szerint:
-    A virtuális gép a **Contoso alhálózatba**kerül.
-    **A Contoso alhálózat** az **Alhálózati NSG-hez**van társítva.
-    A virtuális gép hálózati adaptere emellett a **VM NSG-hez**is társítva van.

![NSG a site recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

Ebben a példában a bejövő forgalom esetében először az alhálózati NSG kerül kiértékelésre. Az Alhálózati NSG-n keresztül engedélyezett forgalmat ezután a vm NSG értékeli ki. A fordított a kimenő forgalom, a virtuális gép NSG kiértékelése először. A virtuális gép NSG-n keresztül engedélyezett forgalmat ezután az Alhálózati NSG értékeli ki.

Ez lehetővé teszi a részletes biztonsági szabály alkalmazását. Például előfordulhat, hogy engedélyezni szeretné a bejövő internet-hozzáférést néhány alkalmazás virtuális gépei (például előtér-virtuális gépek) egy alhálózat alatt, de korlátozza a bejövő internet-hozzáférést más virtuális gépek (például adatbázis és egyéb háttér virtuális gépek). Ebben az esetben lehet egy enyhébb szabályt az alhálózati NSG, amely lehetővé teszi az internetes forgalmat, és korlátozza a hozzáférést az adott virtuális gépek a virtuális gép NSG-hozzáférés megtagadásával. Ugyanez vonatkozik a kimenő forgalomra is.

Az ilyen NSG-konfigurációk beállításakor győződjön meg arról, hogy a [biztonsági szabályok](../virtual-network/security-overview.md#security-rules)a megfelelő prioritásokat alkalmazzák. A szabályok feldolgozása prioritási sorrendben történik. Az alacsonyabb sorszámúak feldolgozása a magasabb sorszámúak előtt történik, mivel az alacsonyabb sorszámok magasabb prioritást jelölnek. Ha az adatforgalom megfelel valamelyik szabálynak, a feldolgozás leáll. Ennek eredményeképp az olyan alacsonyabb prioritású (magasabb számú) szabályokat, amelyek attribútumai megegyeznek a magasabb prioritású szabályokéival, a rendszer nem dolgozza fel.

Nem biztos, hogy minden esetben tisztában lesz vele, ha a hálózati adapterrel és az azt tartalmazó alhálózattal is társítva van hálózati biztonsági csoport. A hálózati adapterre alkalmazott összesített szabályokat a hálózati adapter [hatályos biztonsági szabályainak](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) megtekintésével ellenőrizheti. Az [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) [IP-folyamat-ellenőrzési](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) funkcióval is meghatározhatja, hogy a kommunikáció engedélyezett-e a hálózati adapterhez vagy a hálózati adapterről. Az eszköz megmutatja, hogy a kommunikáció engedélyezett-e, valamint azt is, hogy melyik hálózati biztonsági szabály engedélyezi vagy blokkolja a forgalmat.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Helyszíni azure-replikáció az NSG-vel

Az Azure Site Recovery lehetővé teszi a vészhelyreállítást és az Azure-ba való áttelepítést a helyszíni [Hyper-V virtuális gépek](hyper-v-azure-architecture.md), [VMware virtuális gépek](vmware-azure-architecture.md)és fizikai [kiszolgálók](physical-azure-architecture.md)számára. Az Azure-forgatókönyvek minden helyszíni azure-forgatókönyvek replikációs adatok at küldi el, és egy Azure Storage-fiókban tárolja. A replikáció során nem kell fizetnie a virtuális gépek nek. Amikor feladatátvételt futtat az Azure-ba, a Site Recovery automatikusan létrehozza az Azure IaaS virtuális gépeket.

Miután virtuális gépek et hoztak létre az Azure-ba való feladatátvétel után, NSG-k segítségével korlátozhatja a hálózati forgalmat a virtuális hálózat és a virtuális gépek. A Site Recovery nem hoz létre NSG-ket a feladatátvételi művelet részeként. Azt javasoljuk, hogy a feladatátvétel megkezdése előtt hozza létre a szükséges Azure NSG-ket. Ezután az NSG-ket automatikusan társíthatja a virtuális gépekfeladat-feladáshoz, automatizálási parancsfájlok használatával a Site Recovery hatékony [helyreállítási terveivel.](site-recovery-create-recovery-plans.md)

Ha például a feladatátvétel utáni virtuális gép konfigurációja hasonló a fent részletezett [példaforgatókönyvhöz:](concepts-network-security-group-with-site-recovery.md#using-network-security-groups)
-    A cél Azure-régió DR-tervezésének részeként **contoso virtuális hálózatot** és **contoso-alhálózatot** hozhat létre.
-    Az **alhálózati NSG-t** és a **virtuális gép NSG-jét** is létrehozhatja és konfigurálhatja ugyanazzal a VÉSZ-tervezéssel.
-    **Az alhálózati NSG** ezután azonnal társítható **a Contoso alhálózathoz,** mivel az NSG és az alhálózat már elérhető.
-    **Virtuális gép NSG** lehet társítható virtuális gépek feladatátvétel helyreállítási tervek használatával.

Az NSG-k létrehozása és konfigurálása után azt javasoljuk, hogy futtasson egy [teszt feladatátvételt](site-recovery-test-failover-to-azure.md) a parancsfájlalapú NSG-társítások és a feladatátvétel utáni virtuálisgép-kapcsolat ellenőrzéséhez.

## <a name="azure-to-azure-replication-with-nsg"></a>Azure-replikáció az NSG-vel

Az Azure Site Recovery lehetővé teszi az [Azure virtuális gépek](azure-to-azure-architecture.md)vészhelyreállítását. Az Azure virtuális gépek replikációjának engedélyezésekor a Site Recovery létrehozhatja a replika virtuális hálózatait (beleértve az alhálózatokat és az átjáróalhálózatokat) a célrégióban, és létrehozhatja a szükséges leképezéseket a forrás és a cél virtuális hálózatok között. A céloldali hálózatokat és alhálózatokat előre is létrehozhatja, és ugyanazt használhatja a replikáció engedélyezése közben. A Site Recovery nem hoz létre virtuális gépeket a cél Azure-régióban a [feladatátvétel](azure-to-azure-tutorial-failover-failback.md)előtt.

Az Azure VM-replikáció, győződjön meg arról, hogy az NSG-szabályok a forrás Azure-régióban lehetővé teszi [a kimenő kapcsolat](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) a replikációs forgalom. Tesztelheti és ellenőrizheti ezeket a szükséges szabályokat ebben a [példában az NSG konfigurációjának](azure-to-azure-about-networking.md#example-nsg-configuration)segítségével.

A Site Recovery nem hoz létre és replikál nsg-eket a feladatátvételi művelet részeként. Azt javasoljuk, hogy a feladatátvétel megkezdése előtt hozza létre a szükséges NSG-k a cél Azure-régióban. Ezután az NSG-ket automatikusan társíthatja a virtuális gépekfeladat-feladáshoz, automatizálási parancsfájlok használatával a Site Recovery hatékony [helyreállítási terveivel.](site-recovery-create-recovery-plans.md)

Figyelembe véve a korábban ismertetett [példaforgatókönyvet:](concepts-network-security-group-with-site-recovery.md#using-network-security-groups)
-    A Site Recovery a **Contoso virtuális hálózat** és a **Contoso-alhálózat** replikáit hozhatja létre a cél Azure-régióban, ha a virtuális gép replikációja engedélyezve van.
-    Létrehozhatja a kívánt replikák **alhálózati NSG** és **vm NSG** (neve, például a **cél alhálózati NSG** és a **cél virtuális gép NSG,** illetve) a cél-Azure régióban, amely lehetővé teszi a további szabályok szükségesek a célrégióban.
-    **A célalhálózat nsg-je** ezután azonnal társítható a célrégió alhálózatához, mivel az NSG és az alhálózat már elérhető.
-    **A célvirtuális gép NSG-je** társítható virtuális gépekhez a helyreállítási tervek használatával történő feladatátvétel során.

Az NSG-k létrehozása és konfigurálása után azt javasoljuk, hogy futtasson egy [teszt feladatátvételt](azure-to-azure-tutorial-dr-drill.md) a parancsfájlalapú NSG-társítások és a feladatátvétel utáni virtuálisgép-kapcsolat ellenőrzéséhez.

## <a name="next-steps"></a>További lépések
-    További információ a [hálózati biztonsági csoportokról.](../virtual-network/security-overview.md#network-security-groups)
-    További információ az NSG [biztonsági szabályairól.](../virtual-network/security-overview.md#security-rules)
-    További információ az [NSG-k hatékony biztonsági szabályairól.](../virtual-network/diagnose-network-traffic-filter-problem.md)
-    További információ az alkalmazásfeladat-átvétel automatizálását célzó [helyreállítási tervekről.](site-recovery-create-recovery-plans.md)
