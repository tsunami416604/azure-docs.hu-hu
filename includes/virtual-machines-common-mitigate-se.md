---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 6668d9753d0b93ab907d37cdeff8315f488cff7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73935887"
---
**Utolsó dokumentum frissítés**: 12 November 2019 10:00 PST.

A [CPU-sebezhetőségek](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) spekulatív végrehajtási oldalcsatornás támadásoknéven ismert új osztályának nyilvánosságra hozatala olyan kérdéseket eredményezett, amelyeket az ügyfelek egyértelműbbé szeretnének tenni.  

A Microsoft minden felhőszolgáltatásunkban alkalmazta a megoldásokat. Az Azure-t futtató és egymástól elkülönítő infrastruktúra védett. Ez azt jelenti, hogy az azonos infrastruktúrát használó potenciális támadó nem támadhatja meg az alkalmazást a biztonsági rések használatával.

Az Azure a [memória megőrzését használja a karbantartást,](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) amikor csak lehetséges, hogy minimálisra csökkentse az ügyfelek hatását, és ne kelljen újraindítania. Az Azure továbbra is ezeket a módszereket fogja kidolgozni, amikor rendszerszintű frissítéseket készít a gazdagépnek, és megvédi ügyfeleinket.

Az [Azure biztonsági dokumentációs](https://docs.microsoft.com/azure/security/) webhelyén további információ arról, hogy a biztonság hogyan integrálódik az Azure minden aspektusába. 

> [!NOTE] 
> A dokumentum első közzététele óta a biztonsági résosztály több változatát is nyilvánosságra hozták. A Microsoft továbbra is jelentős összegeket fektet ügyfeleink védelmébe és útmutatás nyújtásába. Ez az oldal frissülni fog, ahogy továbbra is kiadja a további javításokat. 
> 
> 2019. november 12-én az [Intel technikai tanácsadót tett közzé](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort) az Intel® Transactional Synchronization Extensions (Intel® TSX) Transaction Asynchronous Abort (TAA) [cve-2019-11135 (TAA)](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135)biztonsági réséről. A biztonsági rés az Intel® Core® processzorokat és az Intel® Xeon® processzorokat érinti.  A Microsoft Azure kiadta az operációs rendszer frissítéseit, és új mikrokódot telepít, ahogy azt az Intel elérhetővé tette a flottánkban, hogy megvédje ügyfeleinket az új biztonsági résektől.   Az Azure szorosan együttműködik az Intelsegítségével az új mikrokód tesztelésében és érvényesítésében a platformon való hivatalos kiadása előtt. 
>
> **Azoknak az ügyfeleknek, akik nem megbízható kódot futtatnak a virtuális gépükön belül,** az alábbi, spekulatív végrehajtási oldalcsatorna-biztonsági résekre vonatkozó további útmutatásokkal (Microsoft Advisors ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)és [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)) az alábbi útmutatóban kell intézkedniük.
>
> Más ügyfelek nek ki kell értékelniük ezeket a biztonsági réseket a védelem mélységszempontjából, és figyelembe kell venniük a választott konfiguráció biztonsági és teljesítménybeli következményeit.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>Az operációs rendszerek naprakészen tartása

Bár az Azure-ban futó alkalmazások elkülönítéséhez nincs szükség operációs rendszer-frissítésre más Azure-ügyfelektől, a szoftver naprakészen tartása mindig ajánlott. A Windows legújabb biztonsági összesítései számos spekulatív végrehajtási oldalcsatorna-biztonsági rés megoldását tartalmazzák. Hasonlóképpen a Linux disztribúciók több frissítést is közzétettek a biztonsági rések megszüntetésére. Az alábbiakban az operációs rendszer frissítéséhez ajánlott műveleteket javasoljuk:

| Ajánlat | Javasolt művelet  |
|----------|---------------------|
| Azure Cloud Services  | Engedélyezze [az automatikus frissítést,](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) vagy győződjön meg arról, hogy a legújabb vendég operációs rendszert futtatja. |
| Azure Linux virtuális gépek | Telepítse a frissítéseket az operációs rendszer szolgáltatójától. További információ: [Linux](#linux) a jelen dokumentum későbbi részében. |
| Azure Windows virtuális gépek  | Telepítse a legújabb biztonsági összesítést.
| Egyéb Azure PaaS-szolgáltatások | Nincs szükség műveletre az ezeket a szolgáltatásokat használó ügyfelek számára. Az Azure automatikusan naprakészen tartja az operációs rendszer verzióit. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>További útmutatás, ha nem megbízható kódot futtat 

Azok az ügyfelek, akik lehetővé teszik a nem megbízható felhasználók számára, hogy tetszőleges kódot hajtsanak végre, további biztonsági funkciókat valósíthatnak meg az Azure virtuális gépeiken vagy a felhőszolgáltatásokon belül. Ezek a funkciók védelmet nyújtanak a folyamaton belüli közzétételi vektorok ellen, amelyeket számos spekulatív végrehajtási biztonsági rés leír.

Példa esetekre, ahol további biztonsági szolgáltatások ajánlottak:

- Engedélyezi, hogy a nem megbízható kód a virtuális gépen belül fusson.  
    - *Például engedélyezi az egyik ügyfélnek, hogy feltöltsön egy bináris vagy parancsfájlt, amelyet ezután az alkalmazáson belül hajt végre.* 
- Engedélyezheti, hogy a nem megbízható felhasználók alacsony kiemelt jogosultságú fiókok használatával jelentkezzenek be a virtuális gépre.   
    - *Például lehetővé teszi, hogy egy alacsony jogosultsági szintű felhasználó jelentkezzen be az egyik virtuális gépre távoli asztal vagy SSH használatával.*  
- Lehetővé teszi a nem megbízható felhasználók számára a beágyazott virtualizáción keresztül megvalósított virtuális gépek elérését.  
    - *Például ön vezérelheti a Hyper-V gazdagépet, de lefoglalja a virtuális gépeket a nem megbízható felhasználóknak.* 

Azoknak az ügyfeleknek, akik nem valósítanak meg nem megbízható kódot tartalmazó forgatókönyvet, nem kell engedélyeznie ezeket a további biztonsági szolgáltatásokat. 

## <a name="enabling-additional-security"></a>További biztonság engedélyezése 

Ha nem megbízható kódot futtat, engedélyezheti a virtuális gépen vagy a felhőszolgáltatáson belül további biztonsági funkciókat. Ezzel párhuzamosan győződjön meg arról, hogy az operációs rendszer naprakész a virtuális gépen vagy a felhőszolgáltatáson belüli biztonsági funkciók engedélyezéséhez

### <a name="windows"></a>Windows 

A további biztonsági szolgáltatások engedélyezéséhez a cél operációs rendszernek naprakésznek kell lennie. Bár az itt leírt további funkciókat alapértelmezés szerint számos spekulatív végrehajtási oldali csatornakockázat-csökkentés engedélyezve van, manuálisan kell engedélyezni, és teljesítménybeli hatást gyakorolhatnak. 


**1. lépés: Tiltsa le a hyper-threading a virtuális gép** – a nem megbízható kódot futtató ügyfelek egy hiper-szálas virtuális gép le kell tiltania a hyper-threading, vagy átkell helyeznie egy nem hiperszálas virtuális gép mérete. Hivatkozzon erre a [dokumentumra](https://docs.microsoft.com/azure/virtual-machines/windows/acu) a hiperszálas virtuális gép méreteinek listájához (ahol a vCPU és a Core aránya 2:1). Annak ellenőrzéséhez, hogy a virtuális gép rendelkezik-e hyper-threading engedélyezve, tekintse meg az alábbi parancsfájlt a Windows parancssorból a virtuális gépből.

Írja `wmic` be az interaktív felületbe való belépéshez. Ezután írja be az alábbi a virtuális gép fizikai és logikai processzorainak megtekintéséhez.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Ha a logikai processzorok száma nagyobb, mint a fizikai processzorok (magok), akkor a hyper-threading engedélyezve van.  Ha hiperszálas virtuális gép fut, kérjük, [forduljon az Azure-támogatáshoz](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) a hyper-threading letiltásához.  Ha a hyper-threading le van tiltva, **a támogatás teljes virtuális gép újraindítását igényli.** Kérjük, olvassa el a [Core count](#core-count) annak megértéséhez, hogy miért a virtuális gép magszáma csökkent.


**2. lépés:** Az 1. [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) [SpeculationControl](https://aka.ms/SpeculationControlPS)

> [!NOTE]
> Ha korábban már letöltötte ezt a modult, telepítenie kell a legújabb verziót.
>


A PowerShell-parancsfájl kimenetének az alábbi értékekkel kell rendelkeznie a biztonsági rések elleni engedélyezett védelem érvényesítéséhez:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

Ha a `MDS mitigation is enabled: False`kimenet i dinamnézisben látható, kérjük, lépjen kapcsolatba az [Azure-támogatással](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) a rendelkezésre álló kockázatcsökkentési lehetőségekért.



**3. lépés:** A kernel virtuális cím árnyékolásának (KVAS) és a branch target injection (BTI) operációs rendszer támogatásának engedélyezéséhez kövesse a [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) biztonsági rés utasításait a `Session Manager` beállításkulcsok használatával történő védelem engedélyezéséhez. Újraindításra van szükség.


**4. lépés:** Beágyazott [virtualizációt](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) használó központi telepítések esetén (csak D3 és E3): Ezek az utasítások a Hyper-V gazdagépként használt virtuális gépen belül érvényesek.

1.  A beállításkulcsok használatával történő védelem engedélyezéséhez kövesse a `MinVmVersionForCpuBasedMitigations` [KB4072698.](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)
2.  Állítsa be a hipervizor `Core` ütemező típusát az [itt](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)található utasításokat követve.


### <a name="linux"></a>Linux

<a name="linux"></a>A további biztonsági szolgáltatások készletének engedélyezése megköveteli, hogy a cél operációs rendszer teljesen naprakész legyen. Néhány megoldás alapértelmezés szerint engedélyezve lesz. A következő szakasz azokat a funkciókat ismerteti, amelyek alapértelmezés szerint ki vannak kapcsolva és/vagy hardvertámogatásra (mikrokód) támaszkodnak. Ezeknek a szolgáltatásoknak az engedélyezése teljesítménybeli hatást gyakorolhat. További utasításokért tájékodhat az operációs rendszer szolgáltatójának dokumentációjában


**1. lépés: Tiltsa le a hyper-threading a virtuális gép** – a nem megbízható kódot futtató ügyfelek egy hiper-szálas virtuális gép le kell tiltania a hyper-threading, vagy átkell helyeznie egy nem hiperszálas virtuális gép.  Hivatkozzon erre a [dokumentumra](https://docs.microsoft.com/azure/virtual-machines/linux/acu) a hiperszálas virtuális gép méreteinek listájához (ahol a vCPU és a Core aránya 2:1). Annak ellenőrzéséhez, hogy egy hiperszálas virtuális `lscpu` gép fut-e, futtassa a parancsot a Linux virtuális gépben. 

Ha `Thread(s) per core = 2`a , akkor a hyper-threading engedélyezve van. 

Ha `Thread(s) per core = 1`a , akkor a hyper-threading le van tiltva. 

 
Mintakimenet olyan virtuális géphez, amelyen engedélyezve van a hyper-threading: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Ha hiperszálas virtuális gép fut, kérjük, [forduljon az Azure-támogatáshoz](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) a hyper-threading letiltásához.  Ha a hyper-threading le van tiltva, **a támogatás teljes virtuális gép újraindítását igényli.** Kérjük, olvassa el a [Core count](#core-count) annak megértéséhez, hogy miért a virtuális gép magszáma csökkent.



**2. lépés:** Az alábbi spekulatív végrehajtási oldalcsatorna-biztonsági rések csökkentése érdekében olvassa el az operációs rendszer szolgáltatójának dokumentációját:   
 
- [Redhat és CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Alapvető számláló

Egy hiperszálas virtuális gép létrehozásakor az Azure magonként 2 szálat foglal le – ezeket vCPU-knak nevezzük. Ha a hyper-threading le van tiltva, az Azure eltávolítja a szálat, és egyetlen szálas magokat (fizikai magokat) hoz létre. A vCPU és a CPU aránya 2:1, így ha a hyper-threading le van tiltva, a virtuális gép processzorszáma a felére csökken. Például egy D8_v3 virtuális gép egy 8 vCPU-n futó hiperszálas virtuális gép (2 szál magonként x 4 mag).  Ha a hyper-threading le van tiltva, a processzorok magonként 4 fizikai magra csökkennek, magonként 1 menettel. 

## <a name="next-steps"></a>További lépések

Ez a cikk útmutatást nyújt az alábbi spekulatív végrehajtási oldalcsatornás támadásokhoz, amelyek számos modern processzort érintenek:

[Kísértet meltdown:](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002)
- CVE-2017-5715 - Ág célinjekció (BTI)  
- CVE-2017-5754 - Kernel laptábla elkülönítése (KPTI)
- CVE-2018-3639 – Spekulatív áruházi bypass (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Windows kernel információ – a Spectre Variant 1 változata
 
[L1 terminálhiba (L1TF):](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018)
- CVE-2018-3615 - Intel Software Guard Extensions (Intel SGX)
- CVE-2018-3620 - Operációs rendszerek (OPERÁCIÓS RENDSZER) és rendszerkezelési mód (SMM)
- CVE-2018-3646 – hatással van a Virtuálisgép-kezelőre (VMM)

[Mikroarchitekturális adatok mintavételezése:](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013) 
- CVE-2019-11091 - Mikroarchitokturális adatok mintavételezése nem gyorsítótárazható memória (MDSUM)
- CVE-2018-12126 - Mikroarchitekturális tárolópuffer-adatok mintavételezése (MSBDS)
- CVE-2018-12127 - Mikroarchitekturális terhelési portadat-mintavétel (MLPDS)
- CVE-2018-12130 - Mikroarchitekturális töltéspuffer-adatok mintavételezése (MFBDS)

Tranzakciós szinkronizálási bővítmények (Intel® TSX) tranzakció megszakítása:  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – TSX tranzakció aszinkron megszakítás (TAA)








