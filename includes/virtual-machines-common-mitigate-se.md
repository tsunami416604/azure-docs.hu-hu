---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 130cc66831b25621cb022eb19005c624fcd71b9e
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2018
ms.locfileid: "40105506"
---
**Utolsó dokumentum-frissítés**: 14 2018 augusztus 10:00 Csendes-ÓCEÁNI.

Közzétételét egy [új osztályt a CPU biztonsági rések](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) néven ismert spekulatív végrehajtás kockázatának csökkentése ügyféloldali-csatorna támadások eredményezte az egyértelműség érdekében további kérő ügyfelek a kérdéseket.  

A Microsoft minden felhőszolgáltatásában megoldások telepítve van. Az infrastruktúra, amely Azure fut, és elkülöníti egymástól ügyfél munkaterheléseinek védelme alatt áll. Ez azt jelenti, hogy a lehetséges támadóknak ugyanazon az infrastruktúrán használatával nem támadásokkal szemben az alkalmazás a biztonsági rések használatával.

Az Azure által használt [karbantartás megőrzése memória](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#memory-preserving-maintenance) , amikor csak lehetséges, az ügyfél gyakorolt hatás minimalizálása érdekében, és szükségtelenné teszi az újraindítások. Azure továbbra is élvezhetik ezen módszerek előnyeit, amikor telepítenek frissítéseket a gazdagépre, és a elkövettünk ügyfeleink védelme.

További információ a security hogyan integrált az Azure platformba érhető el a [Azure Security dokumentációja](https://docs.microsoft.com/azure/security/) hely. 

> [!NOTE] 
> Ez a dokumentum először lett közzétéve, mivel ez az osztály biztonsági rések több változatának közzétették. A Microsoft erősen az ügyfeleink védelme, és útmutatást nyújtunk a befektettek továbbra is. Ezen a lapon frissül, továbbra is, hogy további javításokat kiadás. 
> 
> 2018. augusztus 14., az iparágban közzé egy új spekulatív végrehajtás kockázatának csökkentése ügyféloldali csatorna biztonsági rés ismert [L1 terminálon tartalék](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018) (L1TF) amely hozzá lett rendelve több CVEs ([CVE – 2018-3615, CVE – 2018-3620 és CVE-2018-3646](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00161.html)). Ez a hiba érinti az Intel® Core® processzorok és az Intel® Xeon® processzorok. A Microsoft felhőszolgáltatásában, amelyek az ügyfelek közötti elkülönítés megerősítése megoldások telepítve van. Olvassa el alább L1TF és a korábbi biztonsági rések elleni védelem érdekében további útmutatást ([Spectre Variant 2 CVE – 2017-5715 és a Meltdown Variant 3 CVE – 2017-5754](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)).
>  






## <a name="keeping-your-operating-systems-up-to-date"></a>Az operációs rendszerek frissítése

Operációsrendszer-frissítés nem szükséges az Azure-ügyfeleink az Azure-ban futtatott alkalmazások elkülönítése, miközben, mindig célszerű a szoftver naprakészen tartásához. A legújabb biztonsági frissítések a Windows több spekulatív végrehajtás kockázatának csökkentése ügyféloldali csatorna biztonsági rés megoldások tartalmaznak. Hasonlóképpen Linux-disztribúciók kiadott több frissítéseket, a biztonsági rések. Íme a javasolt műveleteket frissíteni az operációs rendszer:

| Ajánlat | Ajánlott művelet  |
|----------|---------------------|
| Azure Cloud Services  | Engedélyezése [automatikus frissítést](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) vagy ellenőrizze, hogy a legújabb vendég operációs rendszer futtatja. |
| Azure Linux Virtual Machines | Frissítések telepítése az operációs rendszer-szolgáltatótól. További információkért lásd: [Linux](#linux) dokumentum későbbi részében. |
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

A virtuális gép vagy Felhőszolgáltatás belül további biztonsági funkciókat is engedélyezheti.

### <a name="windows"></a>Windows 

A cél operációs rendszer a további biztonsági funkciók engedélyezéséhez naprakész állapotban kell lennie. Számos spekulatív végrehajtás kockázatának csökkentése ügyféloldali csatorna megoldások alapértelmezés szerint engedélyezve vannak, amíg az itt leírt további funkciókat engedélyezni kell a manuális és teljesítménycsökkenést okozhat. 

**1. lépés**: [forduljon az Azure-támogatási](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) frissített fiókszinten elérhető a belső vezérlőprogram (mikrokód), a virtuális gépeken. 

**2. lépés**: engedélyezése Kernel virtuális cím árnyékozása (KVAS) és a fiókiroda cél injektálási (BTI) operációs rendszer támogatása. Kövesse a [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) keresztül védelem engedélyezése a `Session Manager` beállításkulcsok. Egy újraindításra szükség. 

**3. lépés**: használó környezetekben [beágyazott virtualizálás](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 és E3 csak): ezek az utasítások érvényesek, a virtuális gép a Hyper-V-gazdagépként használja. 

1. Kövesse a [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) keresztül védelem engedélyezése a `MinVmVersionForCpuBasedMitigations` beállításkulcsok.  
 
1. A hipervizor scheduler típusa **Core** utasításokat követve [Itt](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types). 

**4. lépés**: kövesse a [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) védelmet engedélyezve vannak a használatával ellenőrizheti a [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell-modult. 

> [!NOTE]
> Ha ez a modul korábban letöltötte, szüksége lesz a legújabb verzió telepítéséhez.
>

Minden virtuális gépnek kell megjelennie:

```
branch target injection mitigation is enabled: True

kernel VA shadow is enabled: True  

L1TFWindowsSupportEnabled: True
```


### <a name="linux"></a>Linux

<a name="linux"></a>További biztonsági funkciók belül engedélyezése megköveteli, hogy a cél operációs rendszer teljes mértékben naprakész. Bizonyos megoldások alapértelmezés szerint engedélyezve lesz. Az alábbi szakasz az a Funkciók, amelyek vannak kapcsolva, alapértelmezés szerint, illetve tartománybeli hardvertámogatásra (mikrokód). Ezek a szolgáltatások engedélyezését okozhat teljesítménycsökkenést. Az operációs rendszer szolgáltató referenciadokumentációt további útmutatás
 
**1. lépés**: [forduljon az Azure-támogatási](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) frissített fiókszinten elérhető a belső vezérlőprogram (mikrokód), a virtuális gépeken.
 
**2. lépés**: engedélyezése ág cél injektálási (BTI) operációs rendszer támogatási csökkentése érdekében CVE – 2017-5715 (Spectre Variant 2) a következő, az operációs rendszer konfigurációszolgáltató dokumentációját. 
 
**3. lépés**: engedélyezése Kernel lap tábla elkülönítési (KPTI) csökkentése érdekében CVE – 2017-5754 (Meltdown Variant 3) a következő, az operációs rendszer konfigurációszolgáltató dokumentációját. 
 
További információ az operációs rendszer szolgáltatói érhető el:  
 
- [Redhat és a CentOS](https://access.redhat.com/security/vulnerabilities/speculativeexecution) 
- [SUSE](https://www.suse.com/support/kb/doc/?id=7022512) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/SpectreAndMeltdown) 


## <a name="next-steps"></a>További lépések

További tudnivalókért lásd: [Azure-ügyfelek védelme a CPU biztonsági rései ellen](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).
