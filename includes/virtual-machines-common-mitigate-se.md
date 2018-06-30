---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: b31e5cc3f99bdbb45aae6f9d71efdabdcc60f9c8
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138177"
---
**Utolsó dokumentálja a frissítés**: 21 előfordulhat, hogy 2018 3:00 ESTE.

A legutóbbi közzétételének egy [CPU biztonsági rések új osztály](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) néven ismert spekulatív végrehajtási ügyféloldali csatorna támadások, így további egyértelműség kérő ügyfelek kérdéseket.  

A Microsoft a felhőszolgáltatás megoldást alkalmazva van. Az infrastruktúra, amely Azure elkülöníti egymástól ügyfél munkaterheléseinek védett.  Ez azt jelenti, hogy más Azure-on futó ügyfelek nem támadható meg az alkalmazás biztonsági rések használatával.

Emellett Azure használatát bővíti [megőrzi az karbantartási memória](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#memory-preserving-maintenance) amikor csak lehetséges, a virtuális Gépet, miközben a rendszer frissíti a gazdagép legfeljebb 30 másodpercig vagy a virtuális gép felfüggesztése kerül egy már frissített gazdagépet.  Megőrzi az karbantartási további memória minimalizálja ügyfél, és nem kell az újraindítások.  Azure felhasznál ezeket a módszereket, rendszerszintű frissítések a gazdagép meghozásakor.

> [!NOTE] 
2018. május 21. Google projekt nulla és a Microsoft bejelentette spekulatív ügyféloldali csatorna biztonsági spekulatív tároló áthidaló néven új alosztálya. A mélység azok mérséklési további védelmet a Microsoft felhőalapú infrastruktúra, amely közvetlenül a spekulatív végrehajtási réseinek között van telepítve. További információ érhető el itt: https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180012 
>
> Késedelmes. február 2018 Intel Corporation közzétett frissített [mikrokód változat útmutatást](https://newsroom.intel.com/wp-content/uploads/sites/11/2018/03/microcode-update-guidance.pdf) azok mikrokód kiadások, amely stabilitást és nyilvánosságralegutóbbibiztonságirésekelleniállapotának[Google projekt nulla](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html). Az Azure-beli függeszthetők a megoldást [2018. január 3](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) Intel mikrokód frissítés nem érinti. Microsoft erős megoldást már vezetnek be Azure-ügyfél más Azure virtuális gépek elleni védelméhez.  
>
> Intel mikrokód címek variant 2 színkép - [CVE-2017-5715](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-5715) vagy fiókirodai cél injektálási - elleni támadások ellen, amelyek csak akkor alkalmazható futtató megosztott vagy nem megbízható munkaterhelések belül a virtuális gépek Azure-on. A mérnökök teszteli a stabilitás minimalizálása érdekében a mikrokód, mielőtt elérhetővé tétele az Azure-ügyfél számára a részeinek teljesítményére.  Kevés ügyfelet a virtuális gépeken nem megbízható alkalmazásokat és szolgáltatásokat futtathatnak, mivel a legtöbb ügyfél nem kell ahhoz, hogy ez a funkció egyszer kiadott. 
>
> További információ áll rendelkezésre a ezen a lapon frissülni fog.  






## <a name="keeping-your-operating-systems-up-to-date"></a>Az operációs rendszer frissítése

Bár egy operációsrendszer-frissítést nem szükséges a más felhasználóktól Azure-on futó Azure-on futó alkalmazások elkülönítése, ez a beállítás mindig ajánlott eljárás az operációsrendszer-verziók naprakész állapotban tartása érdekében. 2018 és újabb verziók. január [biztonsági frissítésekben](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) azok mérséklési lehetőségeit a biztonsági rések tartalmaz.

Az a következő ajánlatok az alábbiakban az operációs rendszer frissítésére a javasolt művelet: 

<table>
<tr>
<th>Az ajánlat</th> <th>Ajánlott művelet </th>
</tr>
<tr>
<td>Azure Cloud Services </td>  <td>Automatikus frissítés engedélyezéséhez, vagy ellenőrizni kell, hogy a legújabb vendég operációs rendszer.</td>
</tr>
<tr>
<td>Azure Linux Virtual Machines</td> <td>Frissítések telepítése az operációs rendszer szolgáltatótól, ha elérhető. </td>
</tr>
<tr>
<td>A Windows Azure virtuális gépek </td> <td>Győződjön meg arról, hogy egy támogatott víruskereső alkalmazást futtatja, az operációs rendszer frissítéseinek telepítése előtt. Lépjen kapcsolatba a víruskereső szoftver gyártójával kompatibilitási információt.<p> Telepítse a [január a biztonság összegzése](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002). </p></td>
</tr>
<tr>
<td>Egyéb Azure PaaS szolgáltatások</td> <td>Nincs szükség a ezeket a szolgáltatásokat használó ügyfelek számára. Azure szolgáltatás automatikusan az operációsrendszer-verziók naprakész. </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>További útmutatás, ha a nem megbízható kód futtatja 

Nincsenek további felhasználói beavatkozásra van szükség, kivéve, ha futtatja a nem megbízható kód. Ha engedélyezi a kódot, amely nem bízik meg (például engedélyezi egy bináris vagy kódrészletet, amely, majd hajtsa végre a felhőben az alkalmazáson belül feltölteni az ügyfelek), akkor a következő további lépéseket kell tenni.  


### <a name="windows"></a>Windows 
Ha Windows használ, és nem megbízható kód üzemeltet, engedélyeznie kell a is nevezett Kernel virtuális cím (KVA) Shadowing, így további védelmet biztosít a spekulatív végrehajtási ügyféloldali csatorna biztonsági rés (kifejezetten a Windows-szolgáltatás Variant 3 Meltdown, [CVE-2017-5754](https://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5754), vagy rosszindulatú gyorsítótár adatbetöltés). Ez a funkció alapértelmezés szerint ki van kapcsolva, és befolyásolhatja a teljesítményt, ha engedélyezve van. Hajtsa végre a [Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) utasításokat a védelem engedélyezése a kiszolgálón. Ha Azure Felhőszolgáltatások futtat, győződjön meg arról, hogy futnak-e WA-VENDÉG-operációsrendszer-5.15_201801-01 vagy WA-VENDÉG-OS-4.50_201801-01 (rendszertől érhető el a 2018 január 10) vagy a engedélyezése a beállításjegyzék-kulcs egy indítási feladattal.


### <a name="linux"></a>Linux
Ha Linux használunk, és nem megbízható kód üzemeltető, frissítse is Linux egy újabb verzióra, amely rendszermag lap-tábla elkülönítési (KPTI), amely elválasztja a felhasználói felület tartozó a rendszermag által használt lap táblák. Ezek azok mérséklési Linux operációs rendszert futtató frissíteni, és ha elérhető terjesztési szolgáltatótól származó. Az operációs rendszer szolgáltató segítségével megállapíthatja, hogy e védelmet engedélyezett vagy letiltott alapértelmezés szerint.



## <a name="next-steps"></a>További lépések

További tudnivalókért lásd: [CPU biztonsági rés védelmét biztosító Azure-ügyfelek](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).
