---
title: Hálózati biztonsági csoportokhoz az Azure Site Recovery szolgáltatással |} Microsoft Docs
description: Hálózati biztonsági csoportok használata az Azure Site Recovery vész-helyreállítási és áttelepítés
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/21/2018
ms.author: manayar
ms.openlocfilehash: 2b35578e2dc49cd47689f62fc47c5341ea8767a4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655252"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Hálózati biztonsági csoportok az Azure Site Recovery szolgáltatással

Hálózati biztonsági csoportok segítségével korlátozhatja a hálózati forgalom egy virtuális hálózatán lévő erőforrásokat. A [hálózati biztonsági csoport (NSG)](../virtual-network/security-overview.md#network-security-groups) felsorolja azokat a szabályokat, amelyek alapján a forrás vagy a cél IP-cím, port és protokoll bejövő vagy kimenő hálózati adatforgalom engedélyezéséhez vagy letiltásához.

A Resource Manager-alapú üzemi modellben NSG-ket társíthatók alhálózatok vagy az egyes hálózati adapterek. Ha az NSG-t hozzárendelik egy alhálózathoz, a szabályok érvényesek lesznek az alhálózathoz csatlakozó összes erőforrásra. Forgalom tovább korlátozza is NGS társítása egyes hálózati adapterek, amely már rendelkezik egy társított NSG egy alhálózaton belül.

Ez a cikk ismerteti, hogyan használja a hálózati biztonsági csoportok az Azure Site Recovery szolgáltatással.

## <a name="using-network-security-groups"></a>Hálózati biztonsági csoportok használata

Egy egyedi alhálózatot állhat nulla vagy egy, a társított NSG-t. Az egyes hálózati adapter is beállíthatja, hogy nulla vagy egy, a társított NSG-t. Igen hatékonyan lehet kettős forgalom korlátozása egy virtuális gépet egy NSG először egy alhálózatot, és a virtuális gép hálózati illesztőhöz majd egy másik NSG társításával. NSG-szabályok alkalmazása ebben az esetben a forgalom iránya a és a használt biztonsági szabályok prioritásának függ.

Érdemes lehet egy egyszerű példa egy virtuális géphez az alábbiak szerint:
-   A virtuális gép el van helyezve belül a **Contoso alhálózati**.
-   **Contoso alhálózati** kapcsolódó **alhálózati NSG**.
-   A virtuális gép hálózati adapter kapcsolódik továbbá **VM NSG**.

![A Site Recovery NSG](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

Az ebben a példában a bejövő forgalmat az alhálózati NSG kiértékelésének először. Minden alhálózati NSG engedélyezett forgalmat majd kiértékeli a virtuális gép NSG. A névkeresési jelenti, először értékelt VM NSG kimenő forgalom esetén alkalmazható. Minden virtuális gép NSG engedélyezett forgalmat Ezután kiértékeli a alhálózati NSG.

Ez lehetővé teszi a részletes biztonsági szabály az alkalmazáshoz. Például előfordulhat, hogy kívánt engedélyezése néhány alkalmazás a virtuális gépek (például az előtér virtuális gépek) alhálózat bejövő internet-hozzáférést, de a bejövő internet-hozzáférés korlátozása más virtuális gépek (például az adatbázisok és más háttér virtuális gépeken). Ebben az esetben az alhálózati NSG, lehetővé téve az internetes forgalmat, alkalmaznia szabály rendelkezik, és korlátozhatja a hozzáférést az adott virtuális gépen megtagadja a hozzáférést a virtuális gép NSG. Ugyanez a kimenő forgalom lehet alkalmazni.

Ilyen NSG konfigurációk beállításához, gondoskodjon arról, hogy a helyes prioritások alkalmazva legyenek a [biztonsági szabályok](../virtual-network/security-overview.md#security-rules). A szabályok feldolgozása prioritási sorrendben történik. Az alacsonyabb sorszámúak feldolgozása a magasabb sorszámúak előtt történik, mivel az alacsonyabb sorszámok magasabb prioritást jelölnek. Ha az adatforgalom megfelel valamelyik szabálynak, a feldolgozás leáll. Ennek eredményeképp az olyan alacsonyabb prioritású (magasabb számú) szabályokat, amelyek attribútumai megegyeznek a magasabb prioritású szabályokéival, a rendszer nem dolgozza fel.

Nem biztos, hogy minden esetben tisztában lesz vele, ha a hálózati adapterrel és az azt tartalmazó alhálózattal is társítva van hálózati biztonsági csoport. Az összesített szabálya egy adott hálózati csatoló megtekintésével ellenőrizheti a [hatékony biztonsági szabályok](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) egy adott hálózati csatoló. Is használhatja a [IP folyamata ellenőrizze](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) képesség [Azure hálózati figyelőt](../network-watcher/network-watcher-monitoring-overview.md) annak meghatározásához, hogy a kommunikáció engedélyezve van vagy a hálózati illesztő. Az eszköz megmutatja, hogy a kommunikáció engedélyezett-e, valamint azt is, hogy melyik hálózati biztonsági szabály engedélyezi vagy blokkolja a forgalmat.

## <a name="on-premises-to-azure-replication-with-nsg"></a>A helyszíni Azure replikációt NSG

Az Azure Site Recovery lehetővé teszi, hogy katasztrófa utáni helyreállítás és a helyszíni Azure-ba történő áttelepítés [Hyper-V virtuális gépek](hyper-v-azure-architecture.md), [VMware virtuális gépek](vmware-azure-architecture.md), és [fizikai kiszolgálók](physical-azure-architecture.md). Az összes helyszíni Azure forgatókönyvekre replikációs adatokat küldött, és egy Azure Storage-fiókban tárolt. Replikálás során a virtuális gép díjakat nem kell fizetnie. Ha a feladatátvételt az Azure-ba, a Site Recovery automatikusan létrehozza a Azure infrastruktúra-szolgáltatási virtuális gépeket.

Virtuális gépek létrehozása után a feladatátvétel az Azure-ba, az NSG-k segítségével korlátozni a virtuális hálózat és a virtuális gépek hálózati forgalmat. A Site Recovery nem hoz létre NSG-ket a feladatátvételi művelet részeként. Azt javasoljuk, hogy a szükséges Azure NGSs létrehozása a feladatátvétel kezdeményezése előtt. Majd társíthat NSG-ket a feladatátvételt virtuális gépeket automatikusan a feladatátvétel során a Site Recovery automatizálási parancsfájlokat hatékony használata [helyreállítási tervek](site-recovery-create-recovery-plans.md).

Például, ha a feladatátvételt követően Virtuálisgép-konfiguráció hasonlít a [példa](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) részletes fent:
-   Létrehozhat **Contoso virtuális hálózat** és **Contoso alhálózati** tervezésekor a cél Azure-régiót DR részeként.
-   Is létrehozása és konfigurálása mindkét **alhálózati NSG** , valamint **VM NSG** része a azonos DR tervezési.
-   **Alhálózati NSG** is majd azonnal társítható **Contoso alhálózati**, mind az NSG-t, és az alhálózat már rendelkezésre állnak.
-   **Virtuális gép NSG** lehet társítani a virtuális gépek a helyreállítási tervek segítségével feladatátvétel során.

Az NSG-k létrehozása és konfigurálása után ajánlott fut egy [feladatátvételi teszt](site-recovery-test-failover-to-azure.md) ellenőrzése parancsprogrammal létrehozva az NSG-társítások és a feladatátvételt követően VM-kapcsolatokat.

## <a name="azure-to-azure-replication-with-nsg"></a>NSG-replikációt Azure az Azure-bA

Az Azure Site Recovery lehetővé teszi, hogy a vész-helyreállítási [Azure virtuális gépek](azure-to-azure-architecture.md). Azure virtuális gépek replikációjának engedélyezése, a Site Recovery hozza létre a replikát virtuális hálózatok (például alhálózatok és átjáró alhálózatok) a cél régió, és létrehozhat a leképezések között a forrás és cél virtuális hálózatok. Előzetes létrehozása a cél ügyféloldali hálózatok és alhálózatok, és azonos használja a replikáció során is. A Site Recovery nem hoz létre a virtuális gépek a cél Azure-régió, előtt a [feladatátvételi](azure-to-azure-tutorial-failover-failback.md).

Azure virtuális gép replikációs, győződjön meg arról, hogy a forrás az Azure-régió az NSG-szabályok engedélyezik-e [kimenő kapcsolat](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) a replikációs forgalmat. Is tesztelni, és ellenőrizze ezen szükséges szabályok [példa NSG konfigurációs](azure-to-azure-about-networking.md#example-nsg-configuration).

A Site Recovery hozzon létre vagy nem replikálja az NSG-ket a feladatátvételi művelet részeként. Azt javasoljuk, hogy a szükséges NGSs létrehozása a cél Azure-régió, a feladatátvétel kezdeményezése előtt. Majd társíthat NSG-ket a feladatátvételt virtuális gépeket automatikusan a feladatátvétel során a Site Recovery automatizálási parancsfájlokat hatékony használata [helyreállítási tervek](site-recovery-create-recovery-plans.md).

Annak eldöntéséhez, hogy a [példa](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) korábban leírt:
-   A Site Recovery hozhat létre a replikákat **Contoso virtuális hálózat** és **Contoso alhálózati** a cél Azure-régió, amikor a virtuális gép számára engedélyezve van a replikáció.
-   A kívánt replikái hozhat létre **alhálózati NSG** és **VM NSG** (nevű, például **cél alhálózat NSG** és **cél virtuális gép NSG**, illetve) a cél Azure-régió, lehetővé teszi a bármely további szabályok, a tároló terület szükséges.
-   **Cél alhálózat NSG** ezután azonnal a cél régió alhálózathoz társított mind az NSG-t, és az alhálózat már rendelkezésre állnak.
-   **A cél virtuális gép NSG** lehet társítani a virtuális gépek a helyreállítási tervek segítségével feladatátvétel során.

Az NSG-k létrehozása és konfigurálása után ajánlott fut egy [feladatátvételi teszt](azure-to-azure-tutorial-dr-drill.md) ellenőrzése parancsprogrammal létrehozva az NSG-társítások és a feladatátvételt követően VM-kapcsolatokat.

## <a name="next-steps"></a>További lépések
-   További információ [hálózati biztonsági csoportok](../virtual-network/security-overview.md#network-security-groups).
-   További tudnivalók az NSG [biztonsági szabályok](../virtual-network/security-overview.md#security-rules).
-   További információ [hatékony biztonsági szabályok](../virtual-network/diagnose-network-traffic-filter-problem.md) a egy NSG.
-   További információ [helyreállítási tervek](site-recovery-create-recovery-plans.md) alkalmazás a feladatátvétel automatizálásához.
