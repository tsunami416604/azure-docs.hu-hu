
 
A legutóbbi közzétételének egy [CPU biztonsági rések új osztály](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) néven ismert spekulatív végrehajtási ügyféloldali csatorna támadások, így további egyértelműség kérő ügyfelek kérdéseket. 

 
## <a name="azure-infrastructure"></a>Azure-infrastruktúra

A hipervizor határ kihagyásával két közös futtatott virtuális gépek közötti memória közzétételének engedélyezése a biztonsági rések felfedése ismertetett problémák használható. Egy korábbi jelentett [blogbejegyzés](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/), Azure alkalmazva van-e azok mérséklési felhasználók biztonsági rés elleni védelme érdekében.  A Microsoft ajánlott biztonsági eljárások az összes biztonsági frissítések telepíthet az operációs rendszer gyártójától származó virtuális gép képeit telepítését mindig javasolja.

## <a name="paas-services-on-azure"></a>A PaaS szolgáltatások az Azure-on
Azure PaaS ajánlatok azok mérséklési alapértelmezés szerint telepítve van. Nincs szükség felhasználói műveletek ügyfelek. Lásd az alábbi Azure Felhőszolgáltatások kivétel.  


## <a name="azure-cloud-services"></a>Azure Cloud Services

[Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) automatikus frissítés engedélyezése automatikusan történik, a verzió a vendég operációs rendszer, amely tartalmazza a biztonsági rések megoldást jusson. 

A következő vendég operációs rendszer kiadásai spekulatív végrehajtási ügyféloldali csatorna biztonsági rések elleni védelem frissítések:

* WA-VENDÉG-OPERÁCIÓSRENDSZER-5.15_201801-01
* WA-VENDÉG-OPERÁCIÓSRENDSZER-4.50_201801-01


Azure Cloud Services használata a gazdagép nem megbízható kód ügyfelek kis számú is engedélyezze a [Kernel virtuális cím árnyékozása](#enabling-kernel-virtual-address-shadowing-on-windows-server) mellett a vendég operációs rendszer frissítése. Ügyféloldali csatorna biztonsági rések ez nyújt további védelmet biztosít a spekulatív végrehajtása. Ehhez egy indítási feladattal. További információ arról, hogy mely ügyfelek és a használati forgatókönyvek igényel a szolgáltatásról és az engedélyezéshez, lejjebb tekinthetők meg.


## <a name="azure-virtual-machines-windows--linux"></a>Az Azure virtuális gépek (a Windows és Linux)

A Microsoft javasolja mindig, minden biztonsági frissítés telepítését. 

A január 2018 a biztonság összegzése és a biztonsági rések javításait tartalmazzák. Győződjön meg arról, hogy egy támogatott víruskereső alkalmazást futtatja, az operációs rendszer frissítéseinek telepítése előtt. Lépjen kapcsolatba a víruskereső szoftver gyártójával kompatibilitási információt. 

A cím spekulatív végrehajtási biztonsági réseinek, a Linux kernel lesz szükség, és ha elérhető terjesztési szolgáltatótól származó frissítések. 

A gazdagép nem megbízható kód Azure virtuális gép (Windows) használó ügyfelek kis számú is engedélyezze a [Kernel virtuális cím árnyékozása](#enabling-kernel-virtual-address-shadowing-on-windows-server) biztosító további védelmet biztosít a spekulatív végrehajtási ügyféloldali csatorna biztonsági rések.  Mely ügyfelek, valamint a használati esetekben szükséges a szolgáltatásról és az engedélyezéshez további információk lejjebb tekinthetők meg.


## <a name="enabling-kernel-virtual-address-shadowing-on-windows-server"></a>A Windows Server árnyékozása Kernel virtuáliscím engedélyezése

Az ügyfelek, akik a Windows Servert használják a nem megbízható kód végrehajtása engedélyezze nevezett, Kernel virtuális cím árnyékozása rendszerek védelmét biztosító ahol nem megbízható kód végrehajtja az alacsony felhasználói jogosultságokkal szolgáltatással.

A további védelem kihathat a teljesítményre, és alapértelmezés szerint ki van kapcsolva. Kernel virtuális cím árnyékozása folyamat-folyamat és a kernel-folyamat adatokhoz való illetéktelen hozzáférés elleni védelmet nyújt.

A virtuális gépek, a Cloud Services vagy a helyszíni kiszolgálók van kitéve, ha az az alábbi kategóriák valamelyikébe tartoznak:

* Azure beágyazott virtualizálási Hyper-V-gazdagépek
* A távoli asztali szolgáltatások Hosts (RDSH)
* Virtuális gépek vagy a felhőben futó szolgáltatások például tárolók vagy nem megbízható bővítmények adatbázis, a nem megbízható webes tartalom vagy a munkaterhelések nem megbízható kód, amely a kódra külső forrásból biztosított.

Példa egy olyan forgatókönyvet, ahol Kernel virtuális cím árnyékozása szükség: 

|     |
|-----|
|Egy Azure virtuális gépet egy szolgáltatást futtat ahol elküldheti a nem megbízható felhasználók JavaScript-kód, amely végrehajtja a rendszer korlátozott jogosultságokkal rendelkező. Az azonos virtuális gépen van egy magas szintű jogosultsággal rendelkező folyamattal, amely tartalmazza a titkos adatok, amelyek nem legyen elérhető a felhasználók számára. Ebben a helyzetben Kernel virtuális cím árnyékozása szükség, a két közzététele elleni védelem biztosításához.|
|     | 

A Kernel virtuális cím árnyékozása funkció engedélyezésével konkrét utasításokat keresztül érhetők el [Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution).


> [!NOTE]
> A kiadvány időpontjában Kernel virtuális cím árnyékozása csak érhető el a Windows Server 2016-os, Windows Server 2012 R2 és Windows Server 2008 R2.  
>
>

Ha a Linux-kiszolgálón futtatja, tekintse meg a gyártó által az operációs rendszerek frissítések és az utasításokat.

## <a name="branch-target-injection-mitigation-support-microcode"></a>Fiókirodai cél beszúrására szolgáló megoldás támogatási (mikrokód)

Az ügyfelek eszközökkel, melyek észlelik mikrokód alapú azok mérséklési megléte jelentést, hogy az Azure a nem javított. Az adatok helytelenek. A kiadvány időpontjában fiókirodai cél beszúrására szolgáló megoldás támogatás nem lesz közzétéve az Azure-hipervizorról Azure virtuális gépek vagy Azure Cloud Services. Ez azt jelenti, hogy a virtuális gépek nem tudnak a mikrokód meglétét, és nem használható a kibővített utasítás set. Ez nem jelenti azt, hogy Azure a kereszt-VM spekulatív végrehajtási ügyféloldali csatorna támadásokkal szemben sebezhető.
 
Mivel dolgozunk az iparági partnerekkel további frissítések rendelkezésre állásúvá válhat.

## <a name="next-steps"></a>További lépések

További tudnivalókért lásd: [CPU biztonsági rés védelmét biztosító Azure-ügyfelek](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).