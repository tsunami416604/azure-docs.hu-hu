---
title: Az Azure-beli virtuális gépek rendszerújraindításának ismertetése | Microsoft dokumentumok
description: Felsorolja azokat az eseményeket, amelyek a virtuális gép újraindítását okozhatják
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e94ffb3d34082745c3d7ca86cfda2b93c0ed08da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919413"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Az Azure VM rendszerújraindításának megismerése

Az Azure virtuális gépek (VM-ek) néha újraindul minden látható ok nélkül, anélkül, hogy a bizonyíték arra, hogy az újraindítási műveletet kezdeményezett. Ez a cikk felsorolja azokat a műveleteket és eseményeket, amelyek a virtuális gépek újraindítását okozhatják, és betekintést nyújt a váratlan újraindítási problémák elkerülésébe vagy az ilyen problémák hatásának csökkentésébe.

## <a name="configure-the-vms-for-high-availability"></a>A virtuális gépek konfigurálása magas rendelkezésre állásra

Az Azure-on futó alkalmazások virtuális gépek újraindítása és állásidő elleni védelmének legjobb módja a virtuális gépek magas rendelkezésre állásra való konfigurálása.

Ha ilyen szintű redundanciát szeretne biztosítani az alkalmazás számára, azt javasoljuk, hogy két vagy több virtuális gépet csoportosítson egy rendelkezésre állási csoportban. Ez a konfiguráció biztosítja, hogy egy tervezett vagy nem tervezett karbantartási esemény során legalább egy virtuális gép elérhető legyen, és megfeleljen a 99,95 százalékos [Azure SLA-nak.](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/)

A rendelkezésre állási csoportokról a [Virtuális gépek elérhetőségének kezelése című](../windows/manage-availability.md) témakörben talál további információt.

## <a name="resource-health-information"></a>Erőforrás-egészségügyi információk

Az Azure Resource Health egy olyan szolgáltatás, amely az egyes Azure-erőforrások állapotát teszi elérhetővé, és hasznos útmutatást nyújt a problémák elhárításához. Olyan felhőalapú környezetben, ahol nem lehet közvetlenül hozzáférni a kiszolgálókhoz vagy az infrastruktúra-elemekhez, az Erőforrás-állapot célja, hogy csökkentse a hibaelhárításra fordított időt. Különösen a cél az, hogy csökkentse az időt, hogy a probléma gyökerét az alkalmazás, vagy egy esemény az Azure platformon belül. További információt az Erőforrás állapotának megértése és használata című [témakörben talál.](../../resource-health/resource-health-overview.md)

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Olyan műveletek és események, amelyek a virtuális gép újraindítását okozhatják

### <a name="planned-maintenance"></a>Tervezett karbantartás

A Microsoft Azure rendszeres időközönként frissítéseket hajt végre világszerte a virtuális gépek alapjául szolgáló gazdainfrastruktúra megbízhatóságának, teljesítményének és biztonságának javítása érdekében. A frissítések nagy része, beleértve a memória-megőrzési frissítéseket is, a virtuális gépekre vagy a felhőszolgáltatásokra gyakorolt hatás nélkül történik.

Egyes frissítések azonban újraindítást igényelnek. Ilyen esetekben a virtuális gépek leállnak, amíg az infrastruktúra javítása, majd a virtuális gépek újraindulnak.

Ha meg szeretné tudni, hogy mi az Azure tervezett karbantartása, és hogyan befolyásolhatja a Linux virtuális gépek rendelkezésre állását, tekintse meg az itt felsorolt cikkeket. A cikkek háttértudást biztosítanak az Azure tervezett karbantartási folyamatáról, illetve arról, hogy miként ütemezheti úgy a tervezett karbantartást, hogy az a lehető legkisebb hatást gyakorolja.

- [A virtuális gépek tervezett karbantartása az Azure-ban](../windows/planned-maintenance.md)
- [Azure-beli virtuális gépek tervezett karbantartásának ütemezése](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Memóriamegőrző frissítések

A Microsoft Azure-ban a frissítések ezen osztálya esetén a felhasználók nem tapasztalnak hatást a futó virtuális gépekre. A frissítések sok esetben olyan összetevőkhöz vagy szolgáltatásokhoz tartoznak, amelyek frissíthetők a futó példány zavarása nélkül. Néhány platform infrastruktúra-frissítések a gazdaoperációs rendszer, amely a virtuális gépek újraindítása nélkül is alkalmazható.

Ezeket a memóriamegőrző frissítéseket olyan technológia biztosítja, amely lehetővé teszi a helyszíni élő áttelepítést. A frissítés során a virtuális gép *szüneteltetésre* kerül. Ez az állapot megőrzi a memóriát a RAM-ban, amíg az alapjául szolgáló gazda operációs rendszer megkapja a szükséges frissítéseket és javításokat. A virtuális gép a szüneteltetés után 30 másodpercen belül folytatja a működését. A szüneteltetés után a virtuális gép órája automatikusan szinkronizálódik.

A rövid szüneteltetési időszak miatt a frissítések üzembe helyezése ezzel a mechanizmussal jelentősen csökkenti a virtuális gépekre gyakorolt hatást. Azonban nem minden frissítés telepíthető ily módon. 

A többpéldányos frissítések végrehajtása (a rendelkezésre állási csoportokban lévő virtuális gépeken) frissítési tartományonként történik.

> [!NOTE]
> A régi kernelverzióval rendelkező Linux-gépeket kernelpánik befolyásolja a frissítési módszer során. A probléma elkerülése érdekében frissítsen a 3.10.0-327.10.1-es vagy újabb kernelverzióra. További információ: [Egy Azure Linux virtuális gép egy 3.10-alapú kernel pánik után a gazdagép csomópont frissítése.](https://support.microsoft.com/help/3212236)

### <a name="user-initiated-reboot-or-shutdown-actions"></a>A felhasználó által kezdeményezett újraindítási vagy leállítási műveletek

Ha újraindítja az Azure Portalon, az Azure PowerShellben, a parancssori felületen vagy a REST API-ban, megtalálhatja az eseményt az [Azure-tevékenységnaplóban.](../../azure-monitor/platform/platform-logs-overview.md)

Ha a virtuális gép operációs rendszeréről hajtja végre a műveletet, az eseményt a rendszernaplókban találja.

Más forgatókönyvek, amelyek általában a virtuális gép újraindítása több konfiguráció-módosítási műveletek et tartalmaznak. Általában megjelenik egy figyelmeztető üzenet, amely jelzi, hogy egy adott művelet végrehajtása a virtuális gép újraindítását eredményezi. Ilyenek például a virtuális gép átméretezési műveletek, a rendszergazdai fiók jelszavának módosítása és a statikus IP-cím beállítása.

### <a name="azure-security-center-and-windows-update"></a>Az Azure Security Center és a Windows Update

Az Azure Security Center figyeli a napi Windows és Linux virtuális gépeket a hiányzó operációsrendszer-frissítések miatt. A Security Center lekéri a Windows Update vagy a Windows Server Update Services (WSUS) elérhető biztonsági és kritikus frissítéseinek listáját attól függően, hogy melyik szolgáltatás van konfigurálva a Windows virtuális gépen. A Security Center a Linux rendszerek legújabb frissítéseit is ellenőrzi. Ha a virtuális gép hiányzik egy rendszerfrissítés, a Security Center azt javasolja, hogy a rendszerfrissítések alkalmazása. Ezek a rendszerfrissítések alkalmazása az Azure Portalon található Security Centeren keresztül történik. Bizonyos frissítések alkalmazása után szükség lehet a virtuális gép újraindítására. További információt a [Rendszerfrissítések alkalmazása az Azure Security Centerben című témakörben talál.](../../security-center/security-center-apply-system-updates.md)

A helyszíni kiszolgálókhoz hasonlóan az Azure sem engedélyezi a Frissítések et a Windows-frissítésekről a Windows virtuális gépekre, mivel ezeket a gépeket a felhasználók nak kell kezelniük. Javasoljuk azonban, hogy hagyja el az automatikus Windows Update beállítást. A frissítések automatikus telepítése a Windows Update szolgáltatásból a frissítések telepítése után is újraindítást okozhat. További információt a Windows Update gyakori kérdések című témakörben [talál.](https://support.microsoft.com/help/12373/windows-update-faq)

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>A virtuális gép rendelkezésre állását befolyásoló egyéb helyzetek

Vannak más esetek, amelyekben az Azure aktívan felfüggesztheti a virtuális gép használatát. A művelet meglépése előtt e-mailben értesítést fog kapni, így lehetősége lesz a mögöttes problémák megoldására. Példák a virtuális gépek elérhetőségét befolyásoló problémákra: a biztonsági szabályok megsértése és a fizetési módok lejárata.

### <a name="host-server-faults"></a>A gazdakiszolgáló hibái

A virtuális gép egy fizikai kiszolgálón található, amely egy Azure-adatközpontban fut. A fizikai kiszolgáló néhány más Azure-összetevő mellett a gazdaügynök nevű ügynököt is futtat. Ha ezek az Azure-szoftverösszetevők a fizikai kiszolgálón nem válaszolnak, a figyelőrendszer elindítja a gazdakiszolgáló újraindítását a helyreállítás megkísérlése érdekében. A virtuális gép általában öt percen belül újra elérhető, és továbbra is ugyanazon az állomáson él, mint korábban.

A kiszolgáló hibáit általában hardverhiba okozza, például a merevlemez vagy az SSD-meghajtó hibája. Az Azure folyamatosan figyeli ezeket az eseményeket, azonosítja az alapul szolgáló hibákat, és a megoldás megvalósítása és tesztelése után frissítéseket vezet be.

Mivel egyes gazdakiszolgálói hibák az adott kiszolgálóra jellemzőek lehetnek, a virtuális gép ismételt újraindítási helyzete javítható a virtuális gép egy másik gazdakiszolgálóra történő manuális újratelepítésével. Ez a művelet a virtuális gép részletes lapján az **újraüzembe helyezési** lehetőség használatával indítható el, vagy a virtuális gép leállítása és újraindítása az Azure Portalon.

### <a name="auto-recovery"></a>Automatikus helyreállítás

Ha a gazdakiszolgáló bármilyen okból nem tudja újraindítani, az Azure platform automatikus helyreállítási műveletet kezdeményez a hibás gazdakiszolgáló rotációból való kiforgatása érdekében további vizsgálat céljából. 

Az adott állomáson lévő összes virtuális gép automatikusan áthelyeződik egy másik, kifogástalan állapotú gazdakiszolgálóra. Ez a folyamat általában 15 percen belül befejeződik. Az automatikus helyreállítási folyamatról a virtuális gépek automatikus helyreállítása című témakörben olvashat [bővebben.](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines)

### <a name="unplanned-maintenance"></a>Nem tervezett karbantartás

Ritkán előfordulhat, hogy az Azure-üzemeltetési csapatnak karbantartási tevékenységeket kell végeznie az Azure platform általános állapotának biztosítása érdekében. Ez a viselkedés hatással lehet a virtuális gép rendelkezésre állására, és általában ugyanazt az automatikus helyreállítási műveletet eredményezi, mint korábban leírt.  

A nem tervezett karbantartás a következőket tartalmazza:

- Sürgős csomóponttöredezettség-mentesítés
- Sürgős hálózati kapcsolófrissítések

### <a name="vm-crashes"></a>A virtuális gép összeomlik

A virtuális gépek a virtuális gépen belüli problémák miatt újraindulhatnak. A virtuális gépen futó számítási feladatok vagy szerepkör hibaellenőrzést válthat ki a vendég operációs rendszeren belül. Az összeomlás okának meghatározásához tekintse meg a Windows virtuális gépek rendszer- és alkalmazásnaplóit, valamint a Linux-virtuális gépek soros naplóit.

### <a name="storage-related-forced-shutdowns"></a>A Storage szolgáltatással kapcsolatos kényszerített leállások

Az Azure-beli virtuális gépek az Azure Storage-infrastruktúrában üzemeltetett operációs rendszer és adattárolás virtuális lemezeire támaszkodnak. Ha a virtuális gép és a kapcsolódó virtuális lemezek közötti rendelkezésre állást vagy kapcsolatot több mint 120 másodpercig érinti, az Azure platform a virtuális gépek kényszerített leállítását hajtja végre az adatsérülés elkerülése érdekében. A virtuális gépek automatikusan újra bekapcsolva lesznek a tárolási kapcsolat visszaállítása után. 

A leállítás időtartama akár öt perc is lehet, de jelentősen hosszabb is lehet. A következőkben a tárolással kapcsolatos kényszerített leállításokhoz kapcsolódó konkrét esetek egyike látható: 

**Az I/O-határértékek túllépése**

Előfordulhat, hogy a virtuális gépek ideiglenesen leállnak, ha az I/O-kérelmek et következetesen szabályozzák, mert az I/O-műveletek másodpercenkénti (IOPS) mennyisége meghaladja a lemez I/O-korlátját. (A normál lemezes tárhely 500 IOPS-ra korlátozódik.) A probléma enyhítése érdekében használja a lemezcsíkozást, vagy konfigurálja a vendég virtuális gépen belüli tárhelyet, a munkaterheléstől függően. További információt az [Azure virtuális gépek konfigurálása az optimális tárolási teljesítményhez](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)című témakörben talál.

### <a name="other-incidents"></a>Egyéb események

Ritka esetekben egy széles körben elterjedt probléma hatással lehet több kiszolgálóegy Azure-adatközpontban. Ha ez a probléma jelentkezik, az Azure-csapat e-mail értesítéseket küld az érintett előfizetések. Az Azure [Service Health irányítópultján](https://azure.microsoft.com/status/) és az Azure Portalon ellenőrizheti a folyamatban lévő kimaradások és a korábbi incidensek állapotát.
