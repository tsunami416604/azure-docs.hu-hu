---
title: Hálózati biztonsági csoportok az Azure Site Recoveryvel |} A Microsoft Docs
description: Hálózati biztonsági csoportok használata az Azure Site Recovery vész-helyreállítási és áttelepítés
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 4885be7547d404505b50c563036f260166cbc2cc
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52833822"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Hálózati biztonsági csoportok az Azure Site Recoveryvel

Hálózati biztonsági csoportok segítségével egy virtuális hálózatban lévő erőforrásokra irányuló hálózati forgalom korlátozásáról. A [hálózati biztonsági csoport (NSG)](../virtual-network/security-overview.md#network-security-groups) biztonsági szabályok, amelyek engedélyezik vagy megtagadják a bejövő vagy kimenő hálózati forgalmat a forrás vagy cél IP-cím, port és protokoll alapján listáját tartalmazza.

A Resource Manager-alapú üzemi modellben az NSG-k társíthatók alhálózatokhoz vagy egyedi hálózati adapterekhez. Ha az NSG-t hozzárendelik egy alhálózathoz, a szabályok érvényesek lesznek az alhálózathoz csatlakozó összes erőforrásra. Forgalom tovább korlátozható is társít egy NSG-t egyes hálózati adapterekhez, amely már rendelkezik társított NSG-t egy alhálózaton belül.

Ez a cikk bemutatja, hogyan használhatja a hálózati biztonsági csoportok az Azure Site Recoveryvel.

## <a name="using-network-security-groups"></a>Hálózati biztonsági csoportok használata

Az egyes alhálózattal nulla vagy egy, a társított NSG-t. Az egyes hálózati adapter is rendelkezhet nulla vagy egy, a társított NSG-t. Így hatékonyan rendelkezhet kettős forgalom korlátozása egy virtuális gépet társít egy NSG-t először egy alhálózatot, és a egy másik NSG-t a virtuális gép hálózati adapteréhez. NSG-szabályok az alkalmazás ebben az esetben a forgalom irányát és alkalmazott biztonsági szabályok prioritásának függ.

Vegye figyelembe a következő egy egyszerű példa egy virtuális géppel:
-   A virtuális gép helyezik a **Contoso alhálózati**.
-   **Contoso alhálózati** társítva van **alhálózati NSG**.
-   A virtuális hálózati adapter továbbá társítva **virtuális gép NSG**.

![NSG-t, a Site Recoveryvel](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

Ebben a példában a bejövő forgalmat az alhálózati NSG-t abban az esetben először. Alhálózati NSG átengedett forgalmat Ezután kiértékeli a virtuális gép NSG-t. A fordított először kiértékelése megtörténik a virtuális gép NSG-vel a kimenő adatforgalomra vonatkozik. Virtuális gép NSG átengedett forgalmat ezután alhálózati NSG is kiértékeli.

Ez lehetővé teszi a részletes biztonsági szabály alkalmazás. Például érdemes néhány alkalmazás virtuális gépekhez (például virtuális gépek előtér) egy alhálózatot a bejövő internet-hozzáférés engedélyezése, de a bejövő internet-hozzáférés korlátozása a többi virtuális géphez (például adatbázisok és egyéb háttérbeli virtuális gépek). Ebben az esetben enyhébb szabály rendelkezik az alhálózati NSG-hez, lehetővé téve az internetes forgalmat, és letiltja a hozzáférést a virtuális gép NSG alapján korlátozzák a hozzáférést az adott virtuális gépek. Ugyanaz a kimenő forgalmat is alkalmazható.

Ilyen NSG konfigurációk beállításához, győződjön meg arról, hogy a megfelelő prioritások érvényesek a [biztonsági szabályok](../virtual-network/security-overview.md#security-rules). A szabályok feldolgozása prioritási sorrendben történik. Az alacsonyabb sorszámúak feldolgozása a magasabb sorszámúak előtt történik, mivel az alacsonyabb sorszámok magasabb prioritást jelölnek. Ha az adatforgalom megfelel valamelyik szabálynak, a feldolgozás leáll. Ennek eredményeképp az olyan alacsonyabb prioritású (magasabb számú) szabályokat, amelyek attribútumai megegyeznek a magasabb prioritású szabályokéival, a rendszer nem dolgozza fel.

Nem biztos, hogy minden esetben tisztában lesz vele, ha a hálózati adapterrel és az azt tartalmazó alhálózattal is társítva van hálózati biztonsági csoport. Ellenőrizheti, hogy megtekinti egy hálózati adapterhez rendelt összesített szabályokat a [érvényes biztonsági szabályok](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) egy hálózati adapter. Is használhatja a [IP-folyamat ellenőrzésével](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) képességről [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) meghatározni, hogy a kommunikáció engedélyezve van, vagy a hálózati adapterre. Az eszköz megmutatja, hogy a kommunikáció engedélyezett-e, valamint azt is, hogy melyik hálózati biztonsági szabály engedélyezi vagy blokkolja a forgalmat.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Az NSG-t az Azure-bA helyszíni

Az Azure Site Recovery lehetővé teszi a vészhelyreállítást és migrálás az Azure-bA a helyszíni [Hyper-V virtuális gépek](hyper-v-azure-architecture.md), [VMware virtuális gépek](vmware-azure-architecture.md), és [fizikai kiszolgálók](physical-azure-architecture.md). Az összes helyszíni – Azure forgatókönyvek esetén, a replikációs adatokat fogadja és egy Azure Storage-fiókban tárolt. A replikáció során a virtuális gép díjakat nem kell fizetnie. Ha feladatátvételt végez az Azure-ba, a Site Recovery automatikusan létrehozza az Azure IaaS virtuális gépeket.

Az Azure-bA a feladatátvételt követően létrehozása után a virtuális gépek, az NSG-ket a virtuális hálózat és a virtuális gépek hálózati forgalom korlátozásáról használható. A Site Recovery nem hoz létre NSG-ket a feladatátvételi művelet részeként. Azt javasoljuk, hogy a szükséges Azure NGSs létrehozását, mielőtt elindítaná a feladatátvételt. Ezután NSG-ket társíthat a virtuális gépek automatikus feladatátvétele a feladatátvétel során használatával automatizálási szkriptek a Site Recovery hatékony [helyreállítási tervek](site-recovery-create-recovery-plans.md).

Például, ha a feladatátvételen átesett virtuális gép konfigurációjának hasonlít a [példaforgatókönyv](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) fenti Útmutató:
-   Létrehozhat **Contoso virtuális hálózat** és **Contoso alhálózati** részeként a Vészhelyreállítás megtervezése a cél Azure-régióban.
-   Emellett hozhat létre, és mindkét **alhálózati NSG** , valamint **virtuális gép NSG** rész az azonos Vészhelyreállítás-tervezési.
-   **Alhálózati NSG** majd azonnal társítható **Contoso alhálózati**, mind az NSG-t, és az alhálózat már rendelkezésre állnak.
-   **Virtuális gép NSG** társítható virtuális gépek helyreállítási tervek használatával feladatátvétel során.

Az NSG-k létrehozása és konfigurálása után futtatását javasoljuk egy [feladatátvételi teszt](site-recovery-test-failover-to-azure.md) ellenőrzése parancsfájlalapú NSG társítások és a feladatátvétel utáni virtuális gépek kapcsolatai.

## <a name="azure-to-azure-replication-with-nsg"></a>Azure-bA replikációs NSG-vel

Az Azure Site Recovery lehetővé teszi, hogy a vész-helyreállítási [Azure-beli virtuális gépek](azure-to-azure-architecture.md). Ha engedélyezve van a replikáció az Azure virtuális gépek, a Site Recovery a célként megadott régióban lévő virtuális hálózatok (beleértve az alhálózatok és átjáró-alhálózatokkal) a replika létrehozásához, és létrehozhat a szükséges, a forrás közötti leképezéseket és cél virtuális hálózatok. Előre hozzon létre a cél ügyféloldali hálózatok és alhálózatok, és használja azonos a replikáció engedélyezése során is. A Site Recovery nem hozható létre olyan virtuális gépek a cél Azure-régió előtt [feladatátvételi](azure-to-azure-tutorial-failover-failback.md).

Azure virtuális gép replikációja, győződjön meg arról, hogy a forrás az Azure-régióban az NSG-szabályok engedélyezik-e [kimenő kapcsolat](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) a replikációs forgalmat. Is tesztelheti, és ellenőrizze a szükséges szabályok ezen [példa az NSG konfigurációs](azure-to-azure-about-networking.md#example-nsg-configuration).

A Site Recovery nem hoz létre, vagy hálózati biztonsági csoportok replikálása a feladatátvételi művelet részeként. Azt javasoljuk, hogy a szükséges NGSs feladatátvétel kezdeményezése előtt a cél Azure-régióban hozza létre. Ezután NSG-ket társíthat a virtuális gépek automatikus feladatátvétele a feladatátvétel során használatával automatizálási szkriptek a Site Recovery hatékony [helyreállítási tervek](site-recovery-create-recovery-plans.md).

Figyelembe véve a [példaforgatókönyv](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) fentebb leírt:
-   A Site Recovery létre tudja hozni a replikákat a **Contoso virtuális hálózat** és **Contoso alhálózati** a cél Azure-régió, amikor a replikáció engedélyezve van a virtuális gép számára.
-   Hozhat létre a kívánt replikáin **alhálózati NSG** és **virtuális gép NSG** (nevű, például **cél alhálózat NSG** és **cél virtuális gép NSG**, jelölik) a cél Azure-régióba, lehetővé téve a célrégióban a szükséges további szabályokat.
-   **Cél alhálózat NSG** majd lehet azonnal régió célalhálózat társított mind az NSG-t, és az alhálózat már rendelkezésre állnak.
-   **Cél virtuális gép NSG** társítható virtuális gépek helyreállítási tervek használatával feladatátvétel során.

Az NSG-k létrehozása és konfigurálása után futtatását javasoljuk egy [feladatátvételi teszt](azure-to-azure-tutorial-dr-drill.md) ellenőrzése parancsfájlalapú NSG társítások és a feladatátvétel utáni virtuális gépek kapcsolatai.

## <a name="next-steps"></a>További lépések
-   Tudjon meg többet [hálózati biztonsági csoportok](../virtual-network/security-overview.md#network-security-groups).
-   További információ az NSG [biztonsági szabályok](../virtual-network/security-overview.md#security-rules).
-   Tudjon meg többet [érvényben lévő biztonsági szabályokat](../virtual-network/diagnose-network-traffic-filter-problem.md) az NSG-t.
-   Tudjon meg többet [helyreállítási tervek](site-recovery-create-recovery-plans.md) alkalmazás a feladatátvétel automatizálásához.
