---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/04/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 169e25aeb8503a11f768a2a3062022eef51a76b8
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659820"
---
**Utolsó dokumentum-frissítés**: 4. június 2019 3:00 PM PST.

Közzétételét egy [új osztályt a CPU biztonsági rések](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) néven ismert spekulatív végrehajtás kockázatának csökkentése ügyféloldali-csatorna támadások eredményezte az egyértelműség érdekében további kérő ügyfelek a kérdéseket.  

A Microsoft minden felhőszolgáltatásában megoldások telepítve van. Az infrastruktúra, amely Azure fut, és elkülöníti egymástól ügyfél munkaterheléseinek védelme alatt áll. Ez azt jelenti, hogy a lehetséges támadóknak ugyanazon az infrastruktúrán használatával nem támadásokkal szemben az alkalmazás a biztonsági rések használatával.

Az Azure által használt [karbantartás megőrzése memória](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) , amikor csak lehetséges, az ügyfél gyakorolt hatás minimalizálása érdekében, és szükségtelenné teszi az újraindítások. Azure továbbra is élvezhetik ezen módszerek előnyeit, amikor telepítenek frissítéseket a gazdagépre, és a elkövettünk ügyfeleink védelme.

További információ a security hogyan integrált az Azure platformba érhető el a [Azure Security dokumentációja](https://docs.microsoft.com/azure/security/) hely. 

> [!NOTE] 
> Ez a dokumentum először lett közzétéve, mivel ez az osztály biztonsági rések több változatának közzétették. A Microsoft erősen az ügyfeleink védelme, és útmutatást nyújtunk a befektettek továbbra is. Ezen a lapon frissül, továbbra is, hogy további javításokat kiadás. 
> 
> A 2019. május 14. [nyilvánosságra Intel](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html) spekulatív végrehajtás kockázatának csökkentése ügyféloldali csatorna biztonsági rés ismert mikroarchitekturális adat-mintavételezés új készletét (MDS, tekintse meg a Microsoft biztonsági útmutatója [ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)), amely hozzá lett rendelve több CVEs: 
> - CVE – a 2019-11091 - mikroarchitekturális adatok mintavételezésének Uncacheable memória (MDSUM)
> - CVE – 2018-12126 - mikroarchitekturális Store puffer adatok mintavételezésének (MSBDS) 
> - CVE – 2018-12127 - mikroarchitekturális terhelés Port adatok mintavételezésének (MLPDS)
> - CVE – 2018-12130 - mikroarchitekturális kitöltés puffer adatok mintavételezésének (MFBDS)
>
> Ez a hiba érinti az Intel® Core® processzorok és az Intel® Xeon® processzorok.  Microsoft Azure közzétette az operációs rendszer frissítéseit, és üzembe helyezése új mikrokód módon elérhetővé Intel, a felhasználók új biztonsági rések elleni védelme érdekében a flotta során.   Azure szorosan dolgoznak Intel tesztelése és ellenőrzése az új mikrokód a platform a hivatalos kiadás előtt. 
>
> **Nem megbízható futtató ügyfelek code belül a virtuális gép** kell venni a biztonsági rések elleni védelmét összes spekulatív végrehajtás kockázatának csökkentése ügyféloldali-csatorna biztonsági rés (Microsoft tanácsadók ADV további útmutatást alább olvasási művelet [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018), és [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Más ügyfelek kell a mélység perspektíva biztonsági rések értékelése, és fontolja meg a választott konfigurációs biztonsági és teljesítménybeli következményekkel járhat.



## <a name="keeping-your-operating-systems-up-to-date"></a>Az operációs rendszerek frissítése

Operációsrendszer-frissítés nem szükséges az Azure-ügyfeleink az Azure-ban futtatott alkalmazások elkülönítése, miközben, mindig célszerű a szoftver naprakészen tartásához. A legújabb biztonsági frissítések a Windows több spekulatív végrehajtás kockázatának csökkentése ügyféloldali csatorna biztonsági rés megoldások tartalmaznak. Hasonlóképpen Linux-disztribúciók kiadott több frissítéseket, a biztonsági rések. Íme a javasolt műveleteket frissíteni az operációs rendszer:

| Ajánlat | Javasolt művelet  |
|----------|---------------------|
| Azure Cloud Services  | Engedélyezése [automatikus frissítést](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) vagy ellenőrizze, hogy a legújabb vendég operációs rendszer futtatja. |
| Azure-beli Linuxos virtuális gépek | Frissítések telepítése az operációs rendszer-szolgáltatótól. További információkért lásd: [Linux](#linux) dokumentum későbbi részében. |
| Az Azure Windows virtuális gépek  | Telepítse a legújabb biztonsági kumulatív frissítést.
| Más Azure PaaS-szolgáltatások | Nem tartoznak ezeket a szolgáltatásokat használó ügyfelek számára szükséges műveletek. Az Azure automatikusan biztosítja, hogy az operációs rendszer verziója naprakész. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>További útmutatást a nem megbízható kód futtatásakor 

Ügyfelek, akik nem megbízható felhasználók tetszőleges kódot lehet, hogy szeretne valósít meg bizonyos további biztonsági funkciókat az Azure Virtual Machines vagy Cloud Services belül. Ezek a funkciók számos spekulatív végrehajtás kockázatának csökkentése biztonsági leíró on belüli folyamat nyilvánosságra vektorok ellen.

Ha további biztonsági funkciók használata ajánlott példaforgatókönyvek:

- Engedélyezi a kódot, amely nem megbízható futtatásához a virtuális Gépen belül.  
    - *Például lehetővé teszi az ügyfeleknek a bináris feltöltése vagy parancsfájlt, amikor, majd hajtsa végre az alkalmazáson belül egyik*. 
- Lehetővé teszi a felhasználóknak, amely nem bízik meg bejelentkezni a virtuális Gépre való alacsony jogosultságú fiókot lát.   
    - *Például engedélyezi egy alacsony jogosultsági szintű felhasználó bejelentkezni a virtuális gépek közül távoli asztali vagy SSH használatával*.  
- Engedélyezi a nem megbízható felhasználóknak hozzáférést a beágyazott virtualizálás implementálása virtuális gépekhez.  
    - *Például a Hyper-V-gazdagép irányítása, de a virtuális gépek lefoglalása nem megbízható felhasználók*. 

Ügyfelek, akik nem valósítják meg a nem megbízható kód használata esetén ez a forgatókönyv nem kell a további biztonsági funkciók engedélyezéséhez. 

## <a name="enabling-additional-security"></a>További biztonság engedélyezése 

Nem megbízható kód futtatásakor engedélyezheti a további biztonsági szolgáltatások a virtuális gép vagy Felhőszolgáltatás belül. Párhuzamosan győződjön meg arról az operációs rendszer naprakészen belül a virtuális gép vagy Felhőszolgáltatás biztonsági szolgáltatások engedélyezése

### <a name="windows"></a>Windows 

A cél operációs rendszer a további biztonsági funkciók engedélyezéséhez naprakész állapotban kell lennie. Számos spekulatív végrehajtás kockázatának csökkentése ügyféloldali csatorna megoldások alapértelmezés szerint engedélyezve vannak, amíg az itt leírt további funkciókat engedélyezni kell a manuális és teljesítménycsökkenést okozhat. 


**1. lépés: Tiltsa le a hyper-Threading technológiát a virtuális gép** -ügyfeleknek a többszálú virtuális gépeken futó nem megbízható kód kell tiltsa le a hyper-Threading technológiát, vagy helyezze át a virtuális gép nem – a hyper-threaded méretét. Hivatkozás [feljegyzett](https://docs.microsoft.com/azure/virtual-machines/windows/acu) a hyper-threaded Virtuálisgép-méretek (ahol vcpu-t mag arány, 2:1). Ellenőrizze, hogy a virtuális gép rendelkezik-e a hyper-threading engedélyezve van, tekintse meg az alábbi szkriptet a parancssorból Windows, a virtuális gépen.

Típus `wmic` , adja meg az interaktív felületét. Írja be az alábbi fizikai mennyisége megtekintéséhez és a logikai processzorok, a virtuális gépen.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Ha a logikai processzorok száma nagyobb, mint a fizikai processzor (magok), majd a hyper-threading engedélyezve van.  Ha egy hyper-threaded virtuális Gépet futtat, kérjük [Azure támogatási](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) lekérése hyper-threading le van tiltva.  A hyper-threading le van tiltva, miután **támogatási lesz szükség a teljes virtuális gép újraindítását**. Tekintse meg [magos száma](#core-count) tudni, miért érdemes a Virtuálisgép-magok száma csökkent.


**2. lépés**: 1. lépés párhuzamosan, kövesse a [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) védelmet engedélyezve vannak a használatával ellenőrizheti a [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell-modult.

> [!NOTE]
> Ha ez a modul korábban letöltötte, szüksége lesz a legújabb verzió telepítéséhez.
>


A PowerShell-parancsprogram kimenetéből rendelkeznie kell az alábbi értékek ellenőrzése engedélyezve van a biztonsági rések elleni védelmet:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

A kimenet látható `MDS mitigation is enabled: False`, kérjük [Azure támogatási](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) elérhető kockázatcsökkentési lehetőségek.



**3. lépés**: Kernel virtuális cím árnyékozása (KVAS) és a fiókiroda cél injektálási (BTI) operációs rendszer támogatás engedélyezéséhez kövesse a [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) ahhoz, hogy védelmet használ a `Session Manager` beállításkulcsok. Egy újraindításra szükség.


**4. lépés**: Az üzemelő példányok által használt [beágyazott virtualizálás](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 és E3 csak): Ezek az utasítások érvényesek, a virtuális gép a Hyper-V-gazdagépként használja.

1.  Kövesse a [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) ahhoz, hogy védelmet használ a `MinVmVersionForCpuBasedMitigations` beállításkulcsok.
2.  A hipervizor scheduler típusa `Core` utasításokat követve [Itt](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types).


### <a name="linux"></a>Linux

<a name="linux"></a>További biztonsági funkciók belül engedélyezése megköveteli, hogy a cél operációs rendszer teljes mértékben naprakész. Bizonyos megoldások alapértelmezés szerint engedélyezve lesz. Az alábbi szakasz az a Funkciók, amelyek vannak kapcsolva, alapértelmezés szerint, illetve tartománybeli hardvertámogatásra (mikrokód). Ezek a szolgáltatások engedélyezését okozhat teljesítménycsökkenést. Az operációs rendszer szolgáltató referenciadokumentációt további útmutatás


**1. lépés: Tiltsa le a hyper-Threading technológiát a virtuális gép** -ügyfeleknek a többszálú virtuális gépeken futó nem megbízható kód kell letiltja a hyper-Threading technológiát, vagy áthelyezés nem – a többszálú virtuális Gépre.  Hivatkozás [feljegyzett](https://docs.microsoft.com/azure/virtual-machines/linux/acu) a hyper-threaded Virtuálisgép-méretek (ahol vcpu-t mag arány, 2:1). Ellenőrizze, hogy a hyper-threaded virtuális gép fut-e, futtassa a `lscpu` parancsot a Linux rendszerű virtuális gépen. 

Ha `Thread(s) per core = 2`, majd a hyper-threading engedélyezve van. 

Ha `Thread(s) per core = 1`, majd a hyper-threading le van tiltva. 

 
Egy virtuális Gépet a hyper-threading engedélyezve van a példa a kimenetre: 

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

Ha egy hyper-threaded virtuális Gépet futtat, kérjük [Azure támogatási](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) lekérése hyper-threading le van tiltva.  A hyper-threading le van tiltva, miután **támogatási lesz szükség a teljes virtuális gép újraindítását**. Tekintse meg [magos száma](#core-count) tudni, miért érdemes a Virtuálisgép-magok száma csökkent.



**2. lépés**: Bármely csökkentése érdekében az alábbi spekulatív végrehajtás kockázatának csökkentése ügyféloldali-csatorna biztonsági résekről, tekintse meg az operációs rendszer konfigurációszolgáltató dokumentációját:   
 
- [Redhat and CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Magok száma

A többszálú virtuális gép létrehozásakor a Azure lefoglal magonként 2 hozzászólásláncot adott találatul – ezek az úgynevezett vcpu-k. Ha a hyper-threading le van tiltva, az Azure eltávolítja a szálak és a felszínre hozza be egyetlen szálon futó mag (fizikai mag). A Processzor vcpu-t aránya 2:1, így egyszer a hyper-threading le van tiltva, a Processzor száma a virtuális gép megjelenik a felére csökkent. Ha például egy D8_v3 virtuális gép 8 Vcpu (2 core x 4 magok szálak) futó hyper-threaded VM.  Ha a hyper-threading le van tiltva, a rendszer eldobja a processzorokat, 4 fizikai maggal és magonként 1 szálat. 

## <a name="next-steps"></a>További lépések

Ez a cikk útmutatást nyújt a spekulatív végrehajtás kockázatának csökkentése ügyféloldali-csatorna támadások számos modern processzorok befolyásoló alábbi:

[A spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE – 2017-5715 - ág cél injektálási (BTI)  
- CVE – 2017-5754 - Kernel lap tábla elkülönítési (KPTI)
- CVE-2018-3639 – spekulatív Store Mellőzés (KPTI) 
 
[L1 A Terminálszolgáltatások tartalék (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE – 2018-3615 - Intel Guard Szoftverbővítmények (Intel SGX)
- CVE – 2018-3620 – operációs rendszer (OS) és a rendszer felügyeleti mód (SMM)
- CVE – 2018-3646 – hatással van a Virtual Machine Manager (VMM)

[Mikroarchitekturális adat-mintavételezés](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE – a 2019-11091 - mikroarchitekturális adatok mintavételezésének Uncacheable memória (MDSUM)
- CVE – 2018-12126 - mikroarchitekturális Store puffer adatok mintavételezésének (MSBDS)
- CVE – 2018-12127 - mikroarchitekturális terhelés Port adatok mintavételezésének (MLPDS)
- CVE – 2018-12130 - mikroarchitekturális kitöltés puffer adatok mintavételezésének (MFBDS)








