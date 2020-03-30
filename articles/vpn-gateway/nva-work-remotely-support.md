---
title: 'Távolról végzett munka: A hálózati virtuális berendezés (NVA) szempontjai a távoli munkához | Azure VPN-átjáró'
description: Ez a cikk segít megérteni, hogy mit kell figyelembe venni a hálózati virtuális készülékek (NVA-k) az Azure-ban a COVID-19 világjárvány során.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/21/2020
ms.author: scottnap
ms.openlocfilehash: 8a22e8f0bde2d13b4055566d96680fadc2db6e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337096"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Távolról végzett munka: A hálózati virtuális berendezés (NVA) szempontjai a távoli munkához

>[!NOTE]
>Ez a cikk bemutatja, hogyan használhatja ki a hálózati virtuális készülékeket, az Azure-t, a Microsoft-hálózatot és az Azure-partneri ökoszisztémát a távoli munkavégzéshez és a COVID-19-es válság miatt felmerülő hálózati problémák csökkentéséhez.
>

Egyes Azure-ügyfelek az Azure Piactér külső hálózati virtuális készülékeit (NVA-kat) használják, hogy kritikus szolgáltatásokat nyújtsanak, például pontról-helyekre VPN-t a COVID-19-járvány idején otthonról dolgozó alkalmazottaik számára. Ez a cikk néhány magas szintű útmutatást vázol fel, amelyeket figyelembe kell venni az Azure-beli nva-k távoli elérési megoldások biztosításához történő kihasználásához.

## <a name="nva-performance-considerations"></a>NVA teljesítményre vonatkozó szempontok

Az Azure Marketplace-en minden nagyobb NVA-szállítónak javaslatokat kell tennie a virtuális gép méretére és a megoldásaik üzembe helyezésekor használandó példányok számára vonatkozójavaslatokra vonatkozóan.  Bár szinte az összes NVA-szállító lehetővé teszi, hogy bármilyen méretet válasszon, amely elérhető az Ön számára egy adott régióban, nagyon fontos, hogy kövesse a szállítók nak az Azure VM-példány méretekre vonatkozó javaslatait, mivel ezek a javaslatok a szállító által végzett teljesítmény virtuális gép méretei tesztelést az Azure-ban.  

### <a name="consider-the-following"></a>Fontolja meg a következő

- **Kapacitás és az egyidejű felhasználók száma** – Ez a szám különösen fontos a pont-hely VPN felhasználók számára, mivel minden csatlakoztatott felhasználó létrehoz egy titkosított (IPSec vagy SSL VPN) alagutat.  
- **Összesített átviteli -** Mi az összesített sávszélesség lesz szüksége, hogy alkalmazkodjon a felhasználók száma van szüksége, amelyre szüksége lesz, hogy a távoli hozzáférés.
- **A virtuális gép mérete lesz szüksége** – mindig használja az NVA szállító által ajánlott virtuálisgép-méretek.  A pont-hely VPN, ha lesz egy csomó egyidejű felhasználói kapcsolatok, nagyobb virtuálisgép-méretek, például [a Dv2 és a DSv2 sorozatú](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Dv2 és Dsv2 sorozat") virtuális gépek. Ezek a virtuális gépek általában több vCPU-val rendelkeznek, és több egyidejű VPN-munkamenetet képesek kezelni.  Amellett, hogy több virtuális mag, az Azure-ban a nagyobb virtuálisgép-méretek nagyobb összesített sávszélesség-kapacitással rendelkeznek, mint a kisebb virtuálisgép-méretek.
    > **Fontos:** Minden szállító másképp használja az erőforrásokat.  Ha nem egyértelmű, hogy milyen példányméreteket kell használnia a becsült felhasználói terhelés hez, forduljon közvetlenül a szoftver forgalmazójához, és kérjen tőlük egy ajánlást.
- **Példányok száma** – Ha azt várja, hogy nagy számú felhasználó és a kapcsolatok, vannak korlátok, hogy milyen felskálázás a NVA-példány mérete elérheti.  Fontolja meg több virtuálisgép-példány üzembe helyezését.
- **IPSec VPN vs SSL VPN** - Az IPSec VPN-implementációk általában jobban teljesítenek, mint az SSL VPN-implementációk.  
- **Licencelés** - Győződjön meg arról, hogy az NVA megoldáshoz vásárolt szoftverlicencek fedezik a COVID-19 járvány során tapasztalt hirtelen növekedést.  Számos NVA licencelési program korlátozza a megoldás által képes kapcsolatok vagy sávszélességek számát.
- **Gyorsított hálózatkezelés** – Fontolja meg egy NVA-megoldás, amely támogatja a gyorsított hálózatkezelés.  A gyorsított hálózatkezelés lehetővé teszi az egygyökérű I/O-virtualizációt (SR-IOV) a virtuális géphez, jelentősen javítva a hálózati teljesítményt. Ez a nagy teljesítményű elérési út megkerüli az állomást az adatelérési útról, csökkentve a késést, a vibráló és a PROCESSZOR-kihasználtságot a támogatott virtuálisgép-típusok on a legigényesebb hálózati munkaterhelésekkel való használatra. A gyorsított hálózatkezelés a legtöbb általános célú, és a számítási optimalizált példányméretek két vagy több vCPU-val támogatottak.

## <a name="monitoring-resources"></a>Monitoring források

Minden NVA-megoldás nak megvannak a saját eszközei és erőforrásai az NVA teljesítményének figyelésére.  Tekintse meg a gyártók dokumentációját, hogy biztosan tisztában legyen a teljesítménykorlátaival, és észlelje, ha az NVA közel van vagy eléri a kapacitást.  Ezenkívül megtekintheti az Azure Monitor Network Insights webhelyét, és megtekintheti a hálózati virtuális készülékekalapvető teljesítményadatait, például a következőket:

- PROCESSZOR-kihasználtság
- Bejövő hálózat
- Kimenő hálózat
- Bejövő forgalomfolyamok
- Kimenő folyamatok

## <a name="next-steps"></a>Következő lépések

A legtöbb nagy NVA partnerek kifüggesztett útmutatást körül méretezés a hirtelen, váratlan növekedés során COVID-19. Íme néhány hasznos hivatkozás a partner erőforrásaihoz.

[Barracuda Engedélyezése munka otthonról, miközben biztosítja az adatokat során COVID-19](https://www.barracuda.com/covid-19/work-from-home "Engedélyezze a munka otthoni munkát az adatok védelme közben a COVID-19 során")

[Cisco AnyConnect implementáció és teljesítmény/méretezési referencia a COVID-19 előkészítéséhez](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Cisco AnyConnect implementáció és teljesítmény/méretezési referencia a COVID-19 előkészítéséhez")

[Citrix COVID-19 Válasz támogató központ](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID-19 Válasz támogató központ")

[F5 Útmutató a távoli munkavállalók drámai növekedésének kezeléséhez](https://www.f5.com/business-continuity "F5 Útmutató a távoli munkavállalók drámai növekedésének kezeléséhez")

[Fortinet COVID-19 frissítések az ügyfelek és partnerek számára](https://www.fortinet.com/covid-19.html "COVID-19 frissítések az ügyfelek és partnerek számára")

[Palo Alto Hálózatok COVID-19 Válasz Központ](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Hálózatok COVID-19 Válasz Központ")
