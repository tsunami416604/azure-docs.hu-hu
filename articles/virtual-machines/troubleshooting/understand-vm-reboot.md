---
title: Egy Azure virtuális Géphez tartozó rendszer-újraindítás ismertetése |} A Microsoft Docs
description: Felsorolja azokat az eseményeket, amelyek miatt egy virtuális gép újraindítása
services: virtual-machines
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: fa316ee47e6fdabacf22e1e419bfd501620dd83d
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429150"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Azure virtuális Géphez tartozó rendszer-újraindítás ismertetése

Azure-beli virtuális gépek (VM) néha előfordulhat, hogy újraindítás OK, a kellene az újraindítási műveletet kezdeményezett bizonyítékokat nélkül. Ez a cikk felsorolja a műveletek és az eseményeket, amelyek virtuális gépek újraindítását okozhatja, és hogyan váratlan újraindítás problémák elkerülése érdekében, vagy csökkentse az ilyen problémák hatását betekintést nyújt.

## <a name="configure-the-vms-for-high-availability"></a>A magas rendelkezésre állású virtuális gép konfigurálása
A legjobb módszer egy alkalmazás, amelyen fut az Azure-beli virtuális gépek elleni védelme érdekében újraindul, és állásidő a magas rendelkezésre állású virtuális gépeket konfigurálhatja.

Ez a szint az alkalmazás redundanciájának biztosítása érdekében javasoljuk, hogy két vagy több virtuális gépet egy rendelkezésre állási csoportban. Ez a konfiguráció biztosítja, hogy vagy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép elérhető, és megfelel-e a 99,95 %-os [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

A rendelkezésre állási csoportok kapcsolatos további információkért lásd a következő cikkeket:

- [Virtuális gépek rendelkezésre állásának kezelése](../windows/manage-availability.md)
- [Virtuális gépek rendelkezésre állásának konfigurálása](../windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Erőforrás-állapotára vonatkozó 
Az Azure Resource Health szolgáltatás, amely az egyes Azure-erőforrások állapotáról, és hasznos útmutatást problémák elhárításához nyújt. Egy felhőalapú környezetben, ahol nem lehetséges közvetlenül hozzáférni a kiszolgálók vagy infrastruktúra-elemeket a Resource Health célja, hogy a hibaelhárítási töltött idő csökkentése érdekében. Különösen célja-e a probléma okát esik-e az alkalmazás vagy egy esemény belül az Azure platform meghatározása töltött idő csökkentése érdekében. További információkért lásd: [megismerése és használata a Resource Health](../../resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Műveletek és események, amelyek miatt a virtuális gép újraindítása

### <a name="planned-maintenance"></a>Tervezett karbantartás
A Microsoft Azure a Microsoft rendszeresen végez frissítéseket szerte a világon a megbízhatóságának, teljesítményének és a gazda-infrastruktúrát alapjául szolgáló virtuális gépek biztonságának javítása érdekében. Ezeket a frissítéseket, beleértve a memóriamegőrző frissítésekkel számos működésére hatással az a virtuális gépek vagy cloud services.

Bizonyos frissítések azonban újraindítás szükséges. Ezekben az esetekben a virtuális gépek leállnak, ilyenkor az infrastruktúra javításának során, majd a virtuális gépeket újra lesz indítva.

Ismerje meg, milyen tervezett karbantartás az Azure-t, és milyen hatással vannak a Linux rendszerű virtuális gépek rendelkezésre állásának, tekintse meg az itt felsorolt cikkeket. A cikkekben az Azure-ral kapcsolatos háttér-karbantartási folyamata és ütemezése további hatást a tervezett karbantartás tervezett.

- [Az Azure-beli virtuális gépek tervezett karbantartása](../windows/planned-maintenance.md)
- [Az Azure virtuális gépek tervezett karbantartás ütemezése](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Memóriamegőrző frissítések   
Ez az osztály a frissítések a Microsoft Azure-ban, a felhasználói élmény nincs hatással a futó virtuális gépek a. A frissítések sok esetben olyan összetevőkhöz vagy szolgáltatásokhoz tartoznak, amelyek frissíthetők a futó példány zavarása nélkül. Néhány platforminfrastruktúra-frissítések a gazdagép operációs rendszere, amely a virtuális gépek újraindítása nélkül alkalmazhatók.

Ezeket a memóriamegőrző frissítéseket olyan technológia biztosítja, amely lehetővé teszi a helyszíni élő áttelepítést. Frissül, amikor a virtuális gép bekerül egy *szüneteltetve* állapota. Ez az állapot megőrzi a memóriát a RAM-ban, amíg az alapjául szolgáló gazda operációs rendszer megkapja a szükséges frissítéseket és javításokat. A virtuális gép a szüneteltetés után 30 másodpercen belül folytatja a működését. A szüneteltetés után a virtuális gép órája automatikusan szinkronizálódik.

A rövid felfüggesztési időszak miatt frissítések telepítése ezzel a mechanizmussal jelentősen csökkenti a virtuális gépeken. Nem minden frissítés azonban így is telepíthető. 

A többpéldányos frissítések végrehajtása (a rendelkezésre állási csoportokban lévő virtuális gépeken) frissítési tartományonként történik.

> [!NOTE]
> Linux rendszerű gépek, amelyeken a régi kernel-verzióknál kernelpánikot során ez a frissítési mód van hatással. A probléma elkerülése érdekében frissítse a kernel verziója 3.10.0-327.10.1 vagy újabb verzióra. További információkért lásd: [egy Azure Linux virtuális gép 3.10-alapú kernel panics egy gazdagép-csomópont frissítése után](https://support.microsoft.com/help/3212236).     
    
### <a name="user-initiated-reboot-or-shutdown-actions"></a>Felhasználó által kezdeményezett újraindítás vagy leállítás műveletek
 
Ha az Azure Portalon, az Azure PowerShell, a parancssori felület vagy a REST API az újraindítást hajt végre, Észreveheti, hogy az esemény a [Azure-tevékenységnapló](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

A művelet végrehajtása a virtuális gép operációs rendszerről, megkeresni az eseményt a rendszer naplókban.

Egyéb forgatókönyvek, amelyek általában a virtuális gép újraindításához több konfiguráció-módosítási műveletek közé tartozik. Általában megjelenik egy figyelmeztető üzenet, amely jelzi, hogy egy adott művelet végrehajtása a virtuális gép újraindítását eredményezi. Ilyenek például a virtuális gép átméretezése műveleteket, az a rendszergazdai fiók jelszavának módosítása, valamint a statikus IP-címet.

### <a name="azure-security-center-and-windows-update"></a>Az Azure Security Center és a Windows Update
Az Azure Security Center figyeli a napi Windows és Linux rendszerű virtuális gépek operációsrendszer-frissítések hiányoznak. A Security Center lekéri az elérhető biztonsági és kritikus frissítések listáját a Windows Update webhelyről vagy a Windows Server Update Services (WSUS), attól függően, amelyek a szolgáltatás úgy van konfigurálva a Windows virtuális gép. A Security Center is ellenőrzi a Linux rendszerek legújabb frissítéseit. Ha a virtuális gép nincs telepítve a rendszer frissítés, a Security Center javasolja, hogy a rendszerfrissítések alkalmazása. Ezeket a rendszer frissítéseinek alkalmazása a Security Center az Azure Portalon keresztül vezérli. Egyes frissítések telepítését, miután a virtuális gépek újraindításának lehet szükség. További információkért lásd: [az Azure Security Center rendszer frissítéseinek alkalmazása](../../security-center/security-center-apply-system-updates.md).

A helyszíni kiszolgálók, például Azure nem küldi el a frissítések a Windows Update Windows virtuális gépekhez, mivel ezek a gépek célja, hogy a felhasználók által felügyelt lehet. Ön azonban javasolt, hogy hagyja meg a Windows Update automatikus beállítás engedélyezve van. A Windows Update frissítések automatikus telepítése a okozhat is előfordul, a frissítések alkalmazása utáni újraindítások. További információkért lásd: [Windows Update – GYIK](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Más helyzetekben a virtuális gépek rendelkezésre állásának
Nincsenek más esetekben, amelyben Azure aktívan előfordulhat, hogy felfüggeszti a virtuális gépek használatát. E-mail-értesítések a műveletet, mielőtt, jelenik meg, így lesz arra, hogy az alapul szolgáló kapcsolatos hibák elhárítása. Virtuális gép rendelkezésre állást befolyásoló problémákat közé biztonsági problémákat és a fizetési módok lejárta.

### <a name="host-server-faults"></a>Gazdagép-kiszolgálói hibák 
A virtuális gép üzemel az Azure-adatközpont belül futó fizikai kiszolgálón. A fizikai kiszolgálón fut néhány más Azure-összetevők mellett a gazdagép-ügynök nevű ügynök. Ezek a fizikai kiszolgálón Azure szoftver-összetevő nem válaszol, amikor a monitorozási rendszer elindítja a helyreállítási kísérletek a gazdakiszolgáló újraindítása. A virtuális gép újra öt percen belül általában érhető el, és továbbra is ugyanazon a gazdagépen, mint korábban live.

Kiszolgáló-hibák általában hardverhiba, például a merevlemez vagy SSD-meghajtó hibáját okozza. Az Azure folyamatosan figyeli az ilyen események, az alapul szolgáló hibák azonosítja, és bevezeti a frissítés után a megoldás megvalósítása és tesztelt.

Mivel a gazdagép server hibákat adott erre a kiszolgálóra, olyan ismétlődő virtuális gép újraindítási helyzet előfordulhat, hogy továbbfejlesztett által manuálisan újbóli üzembe helyezés virtuális Gépre, és a egy másik gazdakiszolgálón. Ez a művelet használatával is elindítható a **ismételt üzembe helyezése** beállítás részleteit megjelenítő oldalon a virtuális gép vagy leállításával és újraindításával a virtuális gép az Azure Portalon.

### <a name="auto-recovery"></a>Automatikus helyreállítása
Ha a kiszolgáló valamilyen okból nem újraindítás, az Azure platform egy automatikus helyreállítási műveletet a hibás gazdakiszolgáló további teendőkért rotációból indítja el. 

Az adott gazdagép összes virtuális gép automatikusan áthelyezéséről van egy másik, megfelelő állapotú gazdakiszolgálón. A folyamat befejeződése általában 15 percen belül. Automatikus helyreállítási folyamatával kapcsolatos további tudnivalókért lásd: [virtuális gépek automatikus helyreállítása](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Nem tervezett karbantartás
Ritka esetekben az Azure üzemeltetési csapat kell végrehajtani a karbantartási tevékenységek annak biztosítása érdekében az Azure platform általános állapotát. Ez a viselkedés hatással lehet a virtuális gépek rendelkezésre állása, és általában eredményez a ugyanaz a automatikus helyreállítási művelet az korábban leírtaknak megfelelően.  

Nem tervezett maintenances a következők:

- Sürgős csomópont töredezettségmentesítése
- Sürgős hálózati kapcsoló frissítések

### <a name="vm-crashes"></a>Virtuális gép összeomlik
Virtuális gépek előfordulhat, hogy indítsa újra a virtuális gépre kapcsolatos problémák miatt. A számítási feladat vagy szerepkör, amely a virtuális gép fut-e hibakeresés a vendég operációs rendszerben is kiválthatják. Az összeomlás okának meghatározásához segítségre van szüksége tekintse meg a rendszer és az alkalmazásnaplókat a Windows virtuális gépek és a Linux rendszerű virtuális gépek soros naplókat.

### <a name="storage-related-forced-shutdowns"></a>A kényszerített leállások Storage szolgáltatással kapcsolatos
Az Azure-beli virtuális gépek operációs rendszer és az adattárolás, az Azure Storage-infrastruktúrában üzemeltetett virtuális lemezek támaszkodnak. Minden alkalommal, amikor a rendelkezésre állás vagy a virtuális gép és a társított virtuális lemezekkel közötti kapcsolat több mint 120 másodpercen van hatással, az Azure platform adatsérüléseinek elkerülése érdekében a virtuális gépek Kényszerített leállítás hajt végre. A virtuális gépek automatikusan bekapcsolt biztonsági után tárolási kapcsolat helyreállt. 

A leállítási időtartama mindössze öt perc is lehet, de sokkal hosszabb lehet. A következő egy társított kényszerített leállások storage szolgáltatással kapcsolatos adott esetek valamelyike: 

**IO meghaladó korlátozza.**

Előfordulhat, hogy ideiglenesen leállítása virtuális gépeket, ha az i/o-kérések következetesen szabályozva, mivel az i/o-műveletek száma másodpercenként (IOPS) mennyisége meghaladja a a lemez i/o-korlátok között. (A standard szintű lemezes tárolás az legfeljebb 500 IOPS.) A probléma megoldásához használja a lemez szétosztottsága befolyásolhatja, vagy konfigurálja a Vendég virtuális gép, a terheléstől függően a ténylegesen felhasznált tárterület. További információkért lásd: [Azure virtuális gép konfigurálása optimális tároló-teljesítményre](http://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

Magasabb IOPS-korlátok Azure Premium Storage akár 80 000 iops-n keresztül érhetők el. További információkért lásd: [nagy teljesítményű Premium Storage](../windows/premium-storage.md).

### <a name="other-incidents"></a>Egyéb események
Ritka esetekben a széles körű probléma hatással lehet a több kiszolgálót az Azure-adatközpontban. Ez a probléma akkor fordul elő, ha az Azure-csapat e-mail-értesítéseket küld az érintett előfizetések. Ellenőrizheti a [Azure Service Health dashboard](https://azure.microsoft.com/status/) és az Azure portal, az állapot folyamatos kimaradások és korábbi eseményeit.
