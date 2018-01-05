

## <a name="what-is-happening"></a>mi történik?

Az iparágban használt, a hardveres biztonsági rést lett [közzétenni a január 3](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html). Az ügyfelek biztonságos mindig van kapcsolva a legnagyobb prioritással és az aktív lépések végrehajtásával győződjön meg arról, hogy nincs Azure vevő kapcsolódik a biztonsági rések jegyében.

A biztonsági rés nyilvánosságra azt [tervezett karbantartás ütemezése az elérését gyorsítja fel](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) és automatikusan újraindul a virtuális gépek továbbra is szükséges a frissítés megkezdése.
 
## <a name="how-can-i-see-which-of-my-vms-are-already-updated"></a>Hogyan tekinthető meg a virtuális gépek közül melyik már frissülnek? 

Láthatja, hogy a virtuális gépek állapotát, és ha az újraindítás befejeződött belül a a a [VM lista az Azure portálon](https://aka.ms/T08tdc). A virtuális gépek megjelenik, vagyis "Már frissítés" Ha a frissítés rendelkezik lett telepítve, vagy "Ütemezett" Ha a frissítés továbbra is szükséges. Ha meg szeretné-e tekinteni a csupán a virtuális gépek "Ütemezett" tekintse meg a [Azure szolgáltatás állapota](https://portal.azure.com/).

## <a name="can-i-find-out-exactly-when-my-vms-will-be-rebooted"></a>Található meg pontosan amikor a virtuális gépek újraindításáról?

A legjobb módszer az újraindítás kapcsolatos figyelmeztetést kap, hogy konfigurálja [ütemezett események](https://docs.microsoft.com/azure/virtual-machines/windows/scheduled-events). Ez biztosítja a virtuális gép 15 perces értesítést karbantartás miatt fog – teszi a virtuális gép egy API-hívás és, továbbá a tevékenység naplóbejegyzés segítségével eseményindítót figyelő Azure küldjön e-mailek, SMS vagy webhookok. Nincs lehetőség arra, hogy bármely korábbi értesítés.

## <a name="can-i-manually-redeploy-now-to-perform-the-required-maintenance"></a>I manuálisan telepítheti a szükséges karbantartás végrehajtásához most? 

De nem tudjuk garantálni, hogy egy újratelepített virtuális Géphez rendelt egy frissített gazdagépet. Ahol lehetséges, az Azure-hálót megpróbálja virtuális gépek kiosztásához a gazdagépekhez, amelyek már frissülnek. Akkor lehet, hogy a virtuális gép újbóli sikerült ártó nem frissített gazdagépen ebben az esetben, előfordulhat, hogy második újraindítás, ütemezett karbantartás részeként kényszerített vonatkoznak. Ennek eredményeképpen manuális redeploys használata nem ajánlott megoldás.

## <a name="how-long-will-the-reboot-take"></a>Mennyi az újraindítás érvénybe? 

A legtöbb újraindítások körülbelül végzése **30 perc**.

## <a name="does-the-guest-os-need-to-be-updated"></a>A vendég operációs rendszer frissítenie kell használ? 

Az Azure-infrastruktúra frissítése a biztonsági rést került a hipervizor szintjén, és nem igényel egy frissítést adunk ki a Windows vagy Linux rendszerű virtuális lemezképet. Azonban mindig, mint kell továbbra is ajánlott biztonsági eljárások a Virtuálisgép-lemezképek alkalmazni. Lépjen kapcsolatba a forgalmazójával annak az operációs rendszerek a frissítéseket és az utasításokat, igény szerint. A Windows Server virtuális gép ügyfelek útmutatás most is közzé lett téve, és érhető el itt.

## <a name="will-there-be-a-performance-impact-as-a-result-of-resolving-this-update"></a>Lesz-e a teljesítményre gyakorolt hatás feloldása a frissítés eredményeképpen?

Az Azure-ügyfelek többsége nem láthatta észlelhető teljesítménybeli hatása a frissítés. A Microsoft korábban már használt optimalizálja a CPU és a lemez i/o-elérési út, és nem látható észlelhető teljesítménybeli hatása a javítás után. A felhasználók egy kis készletét bizonyos hálózati teljesítményre gyakorolt hatás tapasztalhat. Ez használatával Azure hálózatkezelés az elérését gyorsítja fel, figyelembe venni [Windows](https://docs.microsoft.com/en-us/azure/virtual-network/create-vm-accelerated-networking-powershell) vagy [Linux](https://docs.microsoft.com/en-us/azure/virtual-network/create-vm-accelerated-networking-cli), ez az összes Azure-ügyfél számára érhető el egy szabad képesség.

## <a name="i-follow-your-recommendations-for-high-availability-will-my-environment-remain-highly-available-during-the-reboot"></a>I kövesse a magas rendelkezésre állásra, a környezet marad magas rendelkezésre állású az újraindítás során?

Igen, a rendelkezésre állási telepített virtuális gépek vagy a virtuálisgép-méretezési csoportok rendelkezik a frissítési tartományok (UD) szerkezetet. Ha karbantartást végez, Azure eleget tegyen a UD megkötés, és nem újraindul a virtuális gépek különböző UD (belül az azonos rendelkezésre állási csoport). Magas rendelkezésre állású kapcsolatos további információkért tekintse meg [a Windows Azure virtuális gépek rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) vagy [Linux virtuális gépek Azure-ban rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

## <a name="i-have-architected-my-business-continuitydisaster-recovery-plan-using-region-pairs-will-reboots-to-my-vms-occur-in-region-pairs-at-the-same-time"></a>Szükségem van tervezett saját üzleti folytonossági/vész-helyreállítási terv régió párok használatával. A virtuális gépek újraindítások történik régió párok egyszerre?

Általában Azure tervezett karbantartási események előkészítése már megkezdődött egy párosított régiók mindkét régiókban megszűnésének kockázatának minimalizálása érdekében egyszerre. Azonban a biztonsági frissítés sürgős jellege miatt azt vannak végrehajtaná a frissítést minden egyes egyidejűleg.

## <a name="what-about-paas-services-on-azure"></a>Mi a helyzet Azure PaaS szolgáltatások?  

A webes és mobil, adatszolgáltatások, IoT, beleértve az Azure platform szolgáltatásaiból kiszolgáló nélküli, stb. kell venni a biztonsági rés. Nincs szükség a ezeket a szolgáltatásokat használó ügyfelek számára.

## <a name="next-steps"></a>További lépések

További tudnivalókért lásd: [CPU biztonsági rés védelmét biztosító Azure-ügyfelek](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).