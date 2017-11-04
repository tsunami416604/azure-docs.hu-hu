Az Azure virtuális gépek (VM) egyes esetekben előfordulhat, hogy újraindítás OK, a hogy a rendszer újraindítása műveletet kezdeményezett a bizonyító adatok nélkül. Ez a cikk műveletek és az eseményeket, amelyek a virtuális gépek újraindítását okozhatja, és váratlan újraindítást problémák elkerülése és az ilyen problémák hatásának csökkentéséhez betekintést nyújt sorolja fel.

## <a name="configure-the-vms-for-high-availability"></a>A virtuális gépeket, a magas rendelkezésre állás konfigurálása
A legjobb módszer az alkalmazás futó Azure virtuális gép elleni védelme újraindul, és állásidő, hogy a virtuális gépek magas rendelkezésre állásra konfigurálja.

Ez a szint a az alkalmazás redundanciájának biztosítása érdekében javasoljuk, hogy két vagy több virtuális gép rendelkezésre állási csoportba. Ez a konfiguráció biztosítja, hogy vagy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép elérhető, és megfelel a 99,95 % [Azure garantált szolgáltatási szintje](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Rendelkezésre állási készletek kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Virtuális gépek rendelkezésre állásának kezelése](../articles/virtual-machines/windows/manage-availability.md)
- [A virtuális gépek rendelkezésre állásának konfigurálása](../articles/virtual-machines/windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Erőforrás-állapot adatai 
Az Azure Resource Health olyan szolgáltatás, amely mutatja az egyes Azure-erőforrások állapotának és végrehajthatóként útmutatást kínál a problémák hibaelhárítása. Egy felhőalapú környezetben, ahol nem lehet közvetlenül elérje a kiszolgálók vagy az infrastruktúra elemeinek az erőforrás állapota célja a hibaelhárításról töltött idő csökkentéséhez. Különösen a célja, hogy csökkentse a meghatározása, hogy a probléma a legfelső szintű esik-e az alkalmazás vagy egy eseményt az Azure platformon belül a töltött időt. További információkért lásd: [megértése és használja erőforráskészlet állapotát](../articles/resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Műveletek és a virtuális gép újraindítására okozó események

### <a name="planned-maintenance"></a>Tervezett karbantartás
A Microsoft Azure rendszeres időközönként frissítéseket javítására megbízhatóságát, teljesítményét és a gazdagép infrastruktúrájának alapjául szolgáló virtuális gépek biztonsági világ körüli. A virtuális gépeken gyakorolt hatás nélkül hajtja végre ezeket a frissítéseket, beleértve a memória-megőrzi a frissítések, számos, vagy a felhőalapú szolgáltatások.

Egyes frissítések azonban a számítógép újraindítása szükséges. A virtuális gépek az ilyen esetekben állnak le azt a infrastruktúra javítás során, majd újraindítja a virtuális gépek.

Szeretné megtudni, milyen Azure tervezett karbantartás, és milyen hatással vannak a Linux virtuális gépek rendelkezésre állását, tekintse meg az itt felsorolt cikkeket. A cikkekben kapcsolatban az Azure tervezett karbantartási folyamata, és további csökkentése érdekében a hatás tervezett karbantartás ütemezése.

- [Az Azure virtuális gépek tervezett karbantartása](../articles/virtual-machines/windows/planned-maintenance.md)
- [Az Azure virtuális gépek tervezett karbantartás ütemezése](../articles/virtual-machines/windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Memóriamegőrző frissítések   
Ez az osztály a frissítések a Microsoft Azure-ban, a felhasználók ne legyen hatással a futó virtuális gépek a felhasználói élmény. A frissítések sok esetben olyan összetevőkhöz vagy szolgáltatásokhoz tartoznak, amelyek frissíthetők a futó példány zavarása nélkül. A gazdagép operációs rendszere, amely a virtuális gépek újraindítás nélkül lehet alkalmazni a platform infrastruktúrát érintő frissítéseket között.

A memória-megőrzi az frissítések technológia, amely lehetővé teszi a helyszíni élő áttelepítés úgy hajthatja végre. Amikor frissül, a virtuális gép bekerül egy *felfüggesztve* állapota. Ebben az állapotban a RAM memória megőrzi, amíg a mögöttes gazdagép operációs rendszere megkapja a szükséges frissítések és javítások. A virtuális Gépet a felfüggesztés 30 másodperce folytatja a működését. A virtuális gép folytatása után a rendszer automatikusan szinkronizálja a rendszeróra.

A rövid késleltetés időszak miatt frissítéseinek telepítéséhez ezzel a mechanizmussal jelentősen csökkenti a virtuális gépek gyakorolt hatás. Nem minden frissítés azonban így is telepíthető. 

Többpéldányos (a virtuális gépek rendelkezésre állási csoportba) frissítései alkalmazott frissítési tartományok egyszerre.

> [!NOTE]
> A kernel pánikot Linux gépeken, amelyek a régi kernel-verziók alatt ez a frissítési mód van hatással. A probléma elkerüléséhez frissítse kernel verzió 3.10.0-327.10.1 vagy újabb verzióra. További információkért lásd: [An Azure Linux virtuális gép egy 3.10-alapú kernel panics egy gazdagép-csomópont frissítése után](https://support.microsoft.com/help/3212236).     
    
### <a name="user-initiated-reboot-or-shutdown-actions"></a>A felhasználó által kezdeményezett újraindítás vagy -leállítás műveletek
 
Az Azure portál, Azure PowerShell, parancssori felület vagy alaphelyzetbe API újraindítást hajt végre, ha az esemény található a [Azure tevékenységnapló](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Ha a művelet végrehajtása a virtuális gép operációs rendszerről, található az eseményt a rendszer naplóit.

Egyéb forgatókönyvek, amelyek általában a virtuális gép újraindítására több konfiguráció-módosítási műveletek közé tartozik. Szokásos megjelenik egy figyelmeztető üzenet, amely jelzi, hogy egy bizonyos művelet végrehajtása a virtuális gép újraindítását eredményezi. Például a virtuális gép átméretezési műveleteket, módosítja a rendszergazdai fiók jelszavát, és egy statikus IP-cím beállítása.

### <a name="azure-security-center-and-windows-update"></a>Az Azure Security Center és a Windows Update
Az Azure Security Center napi Windows és Linux virtuális gépek figyeli a hiányzó operációsrendszer-frissítések. A Security Center lekér egy listát az elérhető biztonsági és kritikus frissítések a Windows Update webhelyről vagy a Windows Server Update Services (WSUS), attól függően, amelyek a szolgáltatás úgy van konfigurálva a Windows virtuális gép. Biztonsági központ is ellenőrzi, hogy a Linux rendszerek legújabb frissítéseit. A virtuális gép hiányzik a rendszer frissítését, ha a Security Center javasolja, hogy a rendszer frissítéseinek alkalmazása. A rendszer frissítéseinek alkalmazása a Security Center az Azure portálon keresztül vezérli. Egyes frissítések telepítése után újraindul a virtuális gép lehet szükség. További információkért lásd: [alkalmazza a rendszer frissítéseket az Azure Security Centerben](../articles/security-center/security-center-apply-system-updates.md).

A helyszíni kiszolgálók, például Azure nem küldi el a frissítéseket a Windows Update a Windows Azure virtuális gépekhez, mert ezek a gépek célja, hogy a felhasználók által kezelhető. Áll, azonban javasolt, hogy hagyja meg az automatikus Windows Update-beállítás engedélyezve van. A Windows Update frissítések automatikus telepítése a is okozhat, megtörténik a frissítések alkalmazása utáni újraindítások. További információkért lásd: [Windows Update GYIK](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Más esetekben a virtuális gép szervizelése
Nincsenek más esetekben, ahol Azure előfordulhat, hogy aktívan felfüggesztése a virtuális gépek használatát. Kap értesítő e-mailek előtt a műveletet, így lesz lehetősége az alapul szolgáló problémák megoldásához. Virtuális gép rendelkezésre állási befolyásoló problémákat például biztonsági problémát és a fizetési módok lejáratát.

### <a name="host-server-faults"></a>Állomás server hibák 
A virtuális Gépet egy Azure-adatközpontban belül futtató fizikai kiszolgálón tárolja. A fizikai kiszolgáló futtatja a gazdagép ügynöke néhány Azure összetevőkön nevű ügynök. Ha ezeket a fizikai kiszolgálón Azure szoftver-összetevő nem válaszol, a felügyeleti rendszer elindítja a helyreállítási kísérletet a kiszolgáló újraindítását. A virtuális gép érhető általában öt percen belül újra, és továbbra is ugyanazon a gazdagépen található, mint korábban live.

Kiszolgáló hibák általában hardverhiba, például a merevlemez vagy SSD-meghajtó hibáját okozza. Azure folyamatosan ezeket az eseményeket figyeli, az alapul szolgáló hibák azonosítja, és bevezeti a frissítés után a megoldás megvalósítása és tesztelt.

Mivel a gazdagép server hibákat adott kiszolgálóval, előfordulhat, hogy egy virtuális gép ismételt újraindítás helyzetben manuálisan újratelepíteni a virtuális Gépet egy másik gazdagép-kiszolgálón kell javítja. Ez a művelet használatával is elindítható a **újratelepíteni** beállítás részleteit megjelenítő oldalon a virtuális gép vagy leállításával és újraindításával a virtuális Gépet az Azure portálon.

### <a name="auto-recovery"></a>Automatikus helyreállítás
A gazdakiszolgáló a bármilyen okból nem újra, ha az Azure platformon indít el az automatikus helyreállítás a hibás gazdagép-kiszolgálón kívül további vizsgálatok Elforgatás végrehajtandó műveletet. 

Minden virtuális gép ugyanazon a gazdagépen a rendszer automatikusan áthelyezését egy másik, a megfelelő kiszolgálóhoz. Befejeződött a folyamat általában 15 percen belül. Az automatikus helyreállítás folyamattal kapcsolatos további tudnivalókért lásd: [virtuális gépek automatikus helyreállítás](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Nem tervezett karbantartás
Ritka esetekben az Azure műveleti csapata módosítania kell végrehajtani a karbantartási tevékenységek általános állapotát, az Azure platformon biztosításához. Ez a viselkedés hatással lehetnek a virtuális gép rendelkezésre állási, és általában eredményez a ugyanazon automatikus helyreállítás művelet az ismertetett módon.  

Nem tervezett maintenances közé tartoznak a következők:

- Sürgős csomópont töredezettségmentesítés
- Sürgős hálózati kapcsoló frissítések

### <a name="vm-crashes"></a>Virtuális gép összeomlik
Virtuális gépek a virtuális gépért belül problémák miatt előfordulhat, hogy újraindul. A munkaterhelés vagy szerepkör, amely a virtuális Gépen futó válthat ki hibakeresés a vendég operációs rendszerben. Segítségre van szüksége a összeomlási okának meghatározásához tekintse meg a rendszer és a Windows virtuális gépek alkalmazás naplókat, és a Linux virtuális gépek soros naplókat.

### <a name="storage-related-forced-shutdowns"></a>A kényszerített leállítások tárolással kapcsolatos
Az Azure virtuális gépek operációs rendszer és az Azure Storage-infrastruktúra a tárolt adatok tárolási virtuális lemezek támaszkodnak. Amikor a rendelkezésre állás vagy a virtuális gép és a társított virtuális lemezekkel közötti kapcsolat több mint 120 másodpercig van hatással, az Azure platformon a virtuális gépek adatainak a sérülés elkerülése érdekében a kényszerített leállítása hajt végre. A virtuális gépeket automatikusan a újra be vannak kapcsolva, után tárolási kapcsolat visszaállítása sikeresen megtörtént. 

A leállítási időtartama a lehető legrövidebb legyen öt perc is lehet, de sokkal hosszabb lehet. A következő egyike a kényszerített leállítások tárolással kapcsolatos társított bizonyos esetekben: 

**IO meghaladó korlátozza.**

Virtuális gépek előfordulhat, hogy ideiglenesen le kell állítani, mert a kötet i/o-műveletek száma másodpercenként (IOPS) meghaladja az i/o-korlátok, a lemez i/o-kérelmek szabályozott következetesen amikor. (A szabványos lemez tároló mérete legfeljebb 500 IOPS.) A probléma orvoslása érdekében lemez csíkozást használja, vagy konfigurálja a tárolóhelyet, a Vendég virtuális gép, attól függően, hogy a munkaterhelés. További információkért lásd: [konfigurálása a Azure virtuális gépek tárolási teljesítményének optimalizálása](http://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

Prémium szintű Azure Storage a akár 80000 IOPS magasabb IOPS-korlátok vonatkoznak érhetők el. További információkért lásd: [prémium szintű Storage nagy teljesítményű](../articles/virtual-machines/windows/premium-storage.md).

### <a name="other-incidents"></a>Egyéb események
Ritka esetekben előfordulhat, hogy a széles körű probléma hatással lehet a több kiszolgálót egy Azure-adatközpontban. Ez a probléma akkor fordul elő, ha az Azure-csapat e-mail értesítéseket küld az érintett előfizetések. Ellenőrizheti a [Azure szolgáltatás állapota irányítópult](https://azure.microsoft.com/status/) és az Azure-portál a folyamatban lévő kimaradások és túli incidensek állapotának.
