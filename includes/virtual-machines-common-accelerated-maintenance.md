

## <a name="what-is-happening"></a>mi történik?

[Január 3-án bejelentettek](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html) egy, az egész iparágra kiterjedő, hardveralapú biztonsági rést. Az ügyfelek biztonságos mindig van kapcsolva a legnagyobb prioritással és az aktív lépések végrehajtásával győződjön meg arról, hogy nincs Azure vevő kapcsolódik a biztonsági rések jegyében.

A biztonsági rés nyilvánosságra azt [tervezett karbantartás ütemezése az elérését gyorsítja fel](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) és automatikusan újraindul a virtuális gépek továbbra is szükséges a frissítés megkezdése.


## <a name="how-can-i-see-which-of-my-vms-are-already-updated"></a>Hogyan tekinthető meg a virtuális gépek közül melyik már frissülnek? 

[Az Azure Portalon a virtuális gépek listájában](https://aka.ms/T08tdc) láthatja a virtuális gépek állapotát, és hogy az újraindítás befejeződött-e. A virtuális gépek „Már frissítve” állapotúak, ha frissítés alkalmazása megtörtént, és „Ütemezett” állapotúak, ha még szükség van a frissítésre. Ha csak az „Ütemezett” állapotú virtuális gépeket szeretné látni, tekintse meg az [Azure Service Health](https://portal.azure.com/) eszközt.

## <a name="can-i-find-out-exactly-when-my-vms-will-be-rebooted"></a>Található meg pontosan amikor a virtuális gépek újraindításáról?

A legjobb módszer az újraindítás kapcsolatos figyelmeztetést kap, hogy konfigurálja [ütemezett események](https://docs.microsoft.com/azure/virtual-machines/windows/scheduled-events). Ez lehetővé teszi egy 15 perces értesítés, amely a virtuális Gépet karbantartás miatt lesz.

## <a name="can-i-manually-redeploy-now-to-perform-the-required-maintenance"></a>I manuálisan telepítheti a szükséges karbantartás végrehajtásához most? 

De nem tudjuk garantálni, hogy egy újratelepített virtuális Géphez rendelt egy frissített gazdagépet. Ahol lehetséges, az Azure-hálót megpróbálja virtuális gépek kiosztásához a gazdagépekhez, amelyek már frissülnek. Akkor lehet, hogy a virtuális gép újbóli sikerült ártó nem frissített gazdagépen ebben az esetben, előfordulhat, hogy második újraindítás, ütemezett karbantartás részeként kényszerített vonatkoznak. Ennek eredményeképpen manuális redeploys használata nem ajánlott megoldás.

## <a name="how-long-will-the-reboot-take"></a>Mennyi az újraindítás érvénybe? 

A legtöbb újraindítások körülbelül végzése **30 perc**.

## <a name="does-the-guest-os-need-to-be-updated"></a>A vendég operációs rendszer frissítenie kell használ? 

Az Azure-infrastruktúra frissítése a biztonsági rést került a hipervizor szintjén, és nem igényel egy frissítést adunk ki a Windows vagy Linux rendszerű virtuális lemezképet. Azonban mindig, mint kell továbbra is ajánlott biztonsági eljárások a Virtuálisgép-lemezképek alkalmazni. Lépjen kapcsolatba a forgalmazójával annak az operációs rendszerek a frissítéseket és az utasításokat, igény szerint. A Windows Server-alapú virtuális gépet használó ügyfeleknek [itt](../articles/virtual-machines/windows/mitigate-se.md) érhető el útmutatás.

## <a name="will-there-be-a-performance-impact-as-a-result-of-resolving-this-update"></a>Lesz-e a teljesítményre gyakorolt hatás feloldása a frissítés eredményeképpen?

Az Azure-ügyfelek többsége nem láthatta észlelhető teljesítménybeli hatása a frissítés. Arra törekedtünk, hogy optimalizáljuk a CPU és a lemez I/O-útvonalát, és nem érzékeltünk észrevehető teljesítményváltozást a javítás alkalmazása után. Az ügyfelek kis hányada érezhet valamilyen hatást a hálózati teljesítményben. Ez használatával Azure hálózatkezelés az elérését gyorsítja fel, figyelembe venni [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) vagy [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli), ez az összes Azure-ügyfél számára érhető el egy szabad képesség.

## <a name="i-follow-your-recommendations-for-high-availability-will-my-environment-remain-highly-available-during-the-reboot"></a>I kövesse a magas rendelkezésre állásra, a környezet marad magas rendelkezésre állású az újraindítás során?

Igen, a rendelkezésre állási telepített virtuális gépek vagy a virtuálisgép-méretezési csoportok rendelkezik a frissítési tartományok (UD) szerkezetet. Ha karbantartást végez, Azure eleget tegyen a UD megkötés, és nem újraindul a virtuális gépek különböző UD (belül az azonos rendelkezésre állási csoport). Magas rendelkezésre állású kapcsolatos további információkért tekintse meg [a Windows Azure virtuális gépek rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) vagy [Linux virtuális gépek Azure-ban rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

## <a name="i-have-architected-my-business-continuitydisaster-recovery-plan-using-region-pairs-will-reboots-to-my-vms-occur-in-region-pairs-at-the-same-time"></a>Szükségem van tervezett saját üzleti folytonossági/vész-helyreállítási terv régió párok használatával. A virtuális gépek újraindítások történik régió párok egyszerre?

Az Azure tervezett karbantartási eseményei jellemzően egyszerre csak a régiópár egyik tagján történnek, ami mindkét régióban csökkenti a kimaradás kockázatát. Azonban a biztonsági frissítés sürgős jellege miatt azt vannak végrehajtaná a frissítést minden egyes egyidejűleg.

## <a name="what-about-paas-services-on-azure"></a>Mi a helyzet Azure PaaS szolgáltatások?  

A webes és mobil, adatszolgáltatások, IoT, beleértve az Azure platform szolgáltatásaiból kiszolgáló nélküli, stb. kell venni a biztonsági rés. Nincs szükség a ezeket a szolgáltatásokat használó ügyfelek számára.

## <a name="intel-released-additional-guidance-on-january-22-2018-related-to-the-security-vulnerabilities--will-this-guidance-cause-any-additional-maintenance-activities-by-azure"></a>Intel. január 22, 2018 biztonsági rések kapcsolatos további útmutatást ki.  Ez az útmutató, akkor további karbantartási tevékenységeket az Azure-ban?  

2018. január 3. a korábban közzétett Azure megoldást nem érinti a [útmutatójának frissítése](https://newsroom.intel.com/news/root-cause-of-reboot-issue-identified-updated-guidance-for-customers-and-partners/) az Intel. A felhasználói virtuális gépeket az új információk Intel által a nincs további karbantartási tevékenység fognak futni.
 

## <a name="next-steps"></a>További lépések

További tudnivalókért lásd: [CPU biztonsági rés védelmét biztosító Azure-ügyfelek](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).
