---
title: Az Azure fizetési feldolgozási tervezetének - figyelés követelményei
description: PCI DSS követelmény 10
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 293a1673-54bc-478c-9400-231074004eee
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 708c57c1d7b79d3fd3c129de9a7ce4099ab6ac36
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="monitoring-requirements-for-pci-dss-compliant-environments"></a>PCI DSS-kompatibilis környezetben felügyeleti követelmények 
## <a name="pci-dss-requirement-10"></a>PCI DSS követelmény 10

**Követheti és figyelheti az összes hálózati erőforrásokhoz és adatokhoz történő hozzáférés kártya tulajdonosát**

> [!NOTE]
> Ezeket a követelményeket határozzák meg a [Payment Card Industry (PCI) biztonsági szabványok Tanács](https://www.pcisecuritystandards.org/pci_security/) részeként a [PCI adatok biztonsági szabvány (DSS) 3.2-es verziójú](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Tekintse meg a PCI DSS tesztelési eljárások és az egyes követelményekhez útmutatót olvashat.

Naplózási mechanizmusok és felhasználói tevékenységek követését kritikusak megelőzésében, észlelése vagy minimalizálja az adatok biztonsági sérülése hatását. Minden környezetben naplók jelenléte alapos követési, a riasztás és az elemzés lehetővé teszi, ha valamilyen hiba lép fel. A biztonság sérülését okának megállapítása nagyon nehéz feladat, ha a rendszer tevékenységi naplóit nélkül nem lehetséges.

## <a name="pci-dss-requirement-101"></a>PCI DSS követelmény 10.1

**10.1** megvalósítása elkérése rendszerösszetevők egyes felhasználók hozzáférésének csatolása.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure korlátozza a hozzáférést az arra jogosult személyek felügyeleti és diagnosztikai eszközök megfelelő feladat ezzel. A Microsoft Azure a legalacsonyabb jogosultsági alapelvek alapján éles környezetben használt eszközök korlátozza a privilegizált hozzáférési jogosultsága. A Microsoft Azure rögzíti, és tart fenn a Microsoft Azure-összetevőkkel a platform környezetben minden egyes felhasználói hozzáférés naplózása.<br /><br />A Microsoft Azure platform összetevők (például az operációs rendszer, CloudNet, háló és így tovább) vannak konfigurálva a naplózása, és a biztonsági események gyűjtése. A Microsoft Azure platform rendszergazdai tevékenység naplózása. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló kiterjedt naplózás minden rendszer, és felhasználói tevékenység (beleértve a CHD naplózás) rendelkezik. További információkért lásd: [útmutatást PCI - naplózás](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-102"></a>PCI DSS követelmény 10.2

**10.2** megvalósítása automatikus elkérése rendszer összetevők hozza létre újból a következő események:
- **10.2.1** minden egyes felhasználói hozzáfér kártya tulajdonosát adatokhoz
- **10.2.2** minden, a legfelső szintű vagy rendszergazdai jogosultságokkal rendelkező bármely személy által végrehajtott műveletek
- **10.2.3** összes elkérése eléréséhez
- **10.2.4** érvénytelen logikai hozzáférések
- **10.2.5** használata, és az azonosítási és hitelesítési mechanizmusok módosítása – többek között a következőket, de nem kizárólagosan a új fiókok létrehozását és a jogok kiterjesztésének – és a változások, hozzáadásokat, és törléseket a gyökér vagy a rendszergazdai fiókok jogosultságok
- **10.2.6** inicializálási, leállítása, és a biztonsági naplók felfüggesztése
- **10.2.7** a rendszerszintű objektumok létrehozását és törlését

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure korlátozza a hozzáférést az arra jogosult személyek felügyeleti és diagnosztikai eszközök megfelelő feladat ezzel. A Microsoft Azure a legalacsonyabb jogosultsági alapelvek alapján éles környezetben használt eszközök korlátozza a privilegizált hozzáférési jogosultsága. A Microsoft Azure rögzíti, és tart fenn a Microsoft Azure-összetevőkkel a platform környezetben minden egyes felhasználói hozzáférés naplózása.<br /><br />A Microsoft Azure platform összetevők (például az operációs rendszer, CloudNet, háló és így tovább) vannak konfigurálva a naplózása, és a biztonsági események gyűjtése. A Microsoft Azure platform rendszergazdai tevékenység naplózása. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló rendelkezik az összes rendszer és felhasználói tevékenységek, beleértve a CHD naplózási kiterjedt naplózását. További információkért lásd: [útmutatást PCI - naplózás](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-103"></a>PCI DSS követelmény 10.3

**10.3** legalább jegyezze fel a következő eljárást a naplóbejegyzések minden rendszer összetevőire vonatkozó minden esemény:
- **10.3.1** felhasználói azonosító
- **10.3.2** esemény típusa
- **10.3.3** dátuma és időpontja
- **10.3.4** sikerességét vagy sikertelenségét jelzi
- **10.3.5** eredetének esemény
- **10.3.6** identitás vagy az érintett adatok, a rendszer az összetevő vagy az erőforrás nevét

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure létesített eljárások szinkronizálni a kiszolgálók és hálózati eszközök a Microsoft Azure környezetben NTP hierarchiabeli 1 időkiszolgálók szinkronizálva a globális helymeghatározási (GPS) szatellit. Szinkronizálás automatikusan 5 percenként történik. Microsoft Azure a megfelelő szinkronizálási idő üzemeltetett szolgáltatások azzal biztosításáért felelős. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló rögzíti a felhasználói azonosító, eseménytípus, dátum-időbélyeg, sikeres, sikertelen események, esemény forrását, és a 10,3 vezérlő által megkövetelt erőforrás nevét.|



## <a name="pci-dss-requirement-104"></a>PCI DSS követelmény 10.4

**10.4** időszinkronizálást technológiát használ, szinkronizálja a kritikus rendszerideje és az időpontokat, és biztosítania, hogy a következő beszerzése, terjesztése és idő tárolására. 
> [!NOTE]
> Egy példa idő szinkronizálási technológia, a Network Time Protocol (NTP).

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure létesített eljárások szinkronizálni a kiszolgálók és hálózati eszközök a Microsoft Azure környezetben NTP hierarchiabeli 1 időkiszolgálók szinkronizálva a globális helymeghatározási (GPS) szatellit. Szinkronizálás automatikusan 5 percenként történik. Microsoft Azure a megfelelő szinkronizálási idő üzemeltetett szolgáltatások azzal biztosításáért felelős. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A PaaS szolgáltatás időszinkronizálást Azure végzi el.|



### <a name="pci-dss-requirement-1041"></a>PCI DSS követelmény 10.4.1

**10.4.1** kritikus rendszerek rendelkezik a megfelelő és egységes idő.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 10.4](#pci-dss-requirement-10-4). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A PaaS szolgáltatás időszinkronizálást Azure végzi el.|



### <a name="pci-dss-requirement-1042"></a>PCI DSS követelmény 10.4.2

**10.4.2** idő adatok védelmét.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 10.4](#pci-dss-requirement-10-4). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A PaaS szolgáltatás időszinkronizálást Azure végzi el.|



### <a name="pci-dss-requirement-1043"></a>PCI DSS követelmény 10.4.3

**10.4.3** időbeállítást iparágban elfogadott idő forrásból érkezik.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 10.4](#pci-dss-requirement-10-4). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A PaaS szolgáltatás időszinkronizálást Azure végzi el.|



## <a name="pci-dss-requirement-105"></a>PCI DSS követelmény 10.5

**10.5** biztonságos elkérése, ezért nem módosítható.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | FIM és Azonosítók eszközök a Microsoft Azure-környezeten belül valósíthatók meg. A Microsoft Azure EWS események a működési környezetben valós idejű elemzési támogatásához használja. MAs és célok készítése közel valós idejű riasztásokat az eseményeket, amelyek vélhetően okoz a rendszerben. <br /><br />(Webkiszolgáló naplóinak, FTP-kiszolgáló naplóit, és így tovább) szolgáltatás, a felhasználó- és a biztonsági események naplózása engedélyezve van, és központilag maradnak. Azure korlátozza a hozzáférést a naplók az arra jogosult személyek felelősségi alapján. Eseménynaplók az Azure biztonságos archiválási infrastruktúra archivált, és 180 napig megőrződnek. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló biztosít az összes elem Azure naplózását. Biztonsági mentés külső forrásból is elvégezheti [Azure biztonsági mentés](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1051"></a>PCI DSS követelmény 10.5.1

**10.5.1** korlát megjelenítése naplózás ellenőrzését és az érintett feladathoz kapcsolódó van szükség.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 10.5](#pci-dss-requirement-10-5). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló biztosít az összes elem Azure naplózását. Biztonsági mentés külső forrásból is elvégezheti [Azure biztonsági mentés](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1052"></a>PCI DSS követelmény 10.5.2

**10.5.2** eljárást fájlok védelme naplózási, az illetéktelen módosítások.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 10.5](#pci-dss-requirement-10-5). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló biztosít az összes elem Azure naplózását. Biztonsági mentés külső forrásból is elvégezheti [Azure biztonsági mentés](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1053"></a>PCI DSS követelmény 10.5.3

**10.5.3** azonnal készítsen biztonsági másolatot ellenőrzési útvonalat nyújt fájlok egy központi naplózási kiszolgáló vagy a tároló, amelyet nehéz módosítására.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 10.5](#pci-dss-requirement-10-5). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló biztosít az összes elem Azure naplózását. Biztonsági mentés külső forrásból is elvégezheti [Azure biztonsági mentés](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1054"></a>PCI DSS követelmény 10.5.4

**10.5.4** írni egy biztonságos, központosított, belső napló kiszolgáló vagy adathordozó eszköz alakzatot kívülre irányuló technológiák naplókat.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 10.5](#pci-dss-requirement-10-5). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló biztosít az összes elem Azure naplózását. Biztonsági mentés külső forrásból is elvégezheti [Azure biztonsági mentés](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1055"></a>PCI DSS követelmény 10.5.5

**10.5.5** naplók-sértetlenségét figyelés vagy észlelésének szoftver segítségével győződjön meg arról, hogy a meglévő naplóadatokat riasztások kiváltásának (bár a hozzáadni kívánt új adatok nem okozhat riasztást) nélkül nem módosítható.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 10.5](#pci-dss-requirement-10-5). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló biztosít az összes elem Azure naplózását. Biztonsági mentés külső forrásból is elvégezheti [Azure biztonsági mentés](https://azure.microsoft.com/services/backup/).|



## <a name="pci-dss-requirement-106"></a>PCI DSS követelmény 10.6

**10.6** tekintse át a naplókat, valamint a biztonsági események rendszer összetevők rendellenességeket vagy gyanús tevékenységek azonosítására.
 
> [!NOTE]
> Napló lehetőségét, elemzés és riasztási eszközök megfeleljen ennek a követelménynek is használható.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | FIM és Azonosítók eszközök a Microsoft Azure-környezeten belül valósíthatók meg. A Microsoft Azure EWS események a működési környezetben valós idejű elemzési támogatásához használja. MAs és célok készítése közel valós idejű riasztásokat az eseményeket, amelyek vélhetően okoz a rendszerben. <br /><br />(Webkiszolgáló naplóinak, FTP-kiszolgáló naplóit, és így tovább) szolgáltatás, a felhasználó- és a biztonsági események naplózása engedélyezve van, és központilag maradnak. Azure korlátozza a hozzáférést a naplók az arra jogosult személyek felelősségi alapján. Eseménynaplók az Azure biztonságos archiválási infrastruktúra archivált, és 180 napig megőrződnek. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló használ [az Azure Security Center](https://azure.microsoft.com/services/security-center/) figyeléséhez, jelentést, és megakadályozza a rendellenességeket. [Az Azure Advisor](/azure/advisor/advisor-security-recommendations) javaslatok az Azure erőforrások egységes, összevont nézetét jeleníti meg.|



### <a name="pci-dss-requirement-1061"></a>PCI DSS követelmény 10.6.1

**10.6.1** tekintse át a következő legalább naponta:
- Minden biztonsági esemény
- Minden rendszerösszetevő tárolására, feldolgozni vagy CHD és/vagy SAD továbbítja a naplókat
- Az összes kritikus rendszer összetevő naplók
- Kiszolgálók és a biztonsági funkciók (például tűzfalak behatolásérzékelési rendszerek/behatolás-megelőzési rendszerek (Azonosítók/IP-CÍMEK), hitelesítési kiszolgálók, elektronikus kereskedelmi átirányítási kiszolgálók és stb) végrehajtására rendszerösszetevő naplókat.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 10.6](#pci-dss-requirement-10-6). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló használ [az Azure Security Center](https://azure.microsoft.com/services/security-center/) figyeléséhez, jelentést, és megakadályozza a rendellenességeket. [Az Azure Advisor](/azure/advisor/advisor-security-recommendations) javaslatok az Azure erőforrások egységes, összevont nézetét jeleníti meg.|



### <a name="pci-dss-requirement-1062"></a>PCI DSS követelmény 10.6.2

**10.6.2** tekintse át az összes egyéb rendszerösszetevők, rendszeres időközönként a szervezet házirendek és kockázatkezelési stratégiáját, alapján a szervezet éves kockázatbecslés alapján.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 10.6](#pci-dss-requirement-10-6). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló használ [az Azure Security Center](https://azure.microsoft.com/services/security-center/) figyeléséhez, jelentést, és megakadályozza a rendellenességeket. [Az Azure Advisor](/azure/advisor/advisor-security-recommendations) javaslatok az Azure erőforrások egységes, összevont nézetét jeleníti meg.|



### <a name="pci-dss-requirement-1063"></a>PCI DSS követelmény 10.6.3

**10.6.3** kivételeket és a felülvizsgálati folyamat során azonosított rendellenességeket nyomon követéséhez.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | További részletekért lásd a "Microsoft Azure" című rész [követelmény 10.6](#pci-dss-requirement-10-6). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló használ [az Azure Security Center](https://azure.microsoft.com/services/security-center/) figyeléséhez, jelentést, és megakadályozza a rendellenességeket. [Az Azure Advisor](/azure/advisor/advisor-security-recommendations) javaslatok az Azure erőforrások egységes, összevont nézetét jeleníti meg.|



## <a name="pci-dss-requirement-107"></a>PCI DSS követelmény 10.7

**10.7** ellenőrzési útvonalat nyújt előzményeinek legalább egy évre legalább három hónapos azonnal elemzéshez (például online, az archivált vagy biztonsági másolatból visszaállítható).

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure saját belső portálon keresztül azonnal elérhető legfrissebb 3 hónapos egy évig naplók megőrzi. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló kiterjedt naplózás minden rendszer, és felhasználói tevékenység (beleértve a CHD naplózás) rendelkezik. További információkért lásd: [útmutatást PCI - naplózás](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-108"></a>PCI DSS követelmény 10.8

**10.8** **szolgáltatók csak további követelményei:** alkalmazzon olyan folyamatot, a időben történő észlelése és kritikus fontosságú biztonsági ellenőrzési rendszerek, beleértve többek között a sikertelen volt-e hibák jelentése:
- Tűzfalak
- AZONOSÍTÓK/IP-CÍMEK
- FIM
- Anti-virus
- Fizikai hozzáférés-vezérlést
- Logikai hozzáférés-vezérlést
- Naplózási naplózási mechanizmusok
- Szegmentálás vezérlők (használat esetén) 

> [!NOTE]
> Ez a követelmény a legjobb 2018 január 31-ig elteltével követelmény válik.



**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure EWS események a működési környezetben valós idejű elemzési támogatásához használja. MAs és célok készítése közel valós idejű riasztásokat az eseményeket, amelyek vélhetően okoz a rendszerben. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló kiterjedt naplózás minden rendszer, és felhasználói tevékenység (beleértve a CHD naplózás) rendelkezik. További információkért lásd: [útmutatást PCI - naplózás](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-1081"></a>PCI DSS követelmény 10.8.1

**10.8.1** **szolgáltatók csak további követelményei:** kritikus fontosságú biztonsági vezérlőket hibák válaszol időben. Sikertelen biztonsági vezérlők válaszol-folyamatok tartalmaznia kell:
- Biztonsági funkciók visszaállítása
- Azonosítja és dokumentálásához az időtartam (dátum és idő befejezéséhez indítása) a biztonsági hiba
- Azonosítja és dokumentálásához levonását hiba, beleértve az alapvető ok és dokumentálásához szükséges cím alapvető ok szervizelése
- Azonosítja és biztonsági problémák merültek fel a meghibásodás során címzés
- A kockázatbecslés annak meghatározásához, hogy a biztonsági hiba miatt szükség-e további műveletek végrehajtása
- Végrehajtási hiba okát megakadályozásához ismétlődés - biztonsági vezérlők figyelésének újrakezdése 

> [!NOTE]
> Ez a követelmény a legjobb 2018 január 31-ig elteltével követelmény válik.


**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure EWS események a működési környezetben valós idejű elemzési támogatásához használja. MAs és célok készítése közel valós idejű riasztásokat az eseményeket, amelyek vélhetően okoz a rendszerben. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló kiterjedt naplózás minden rendszer, és felhasználói tevékenység (beleértve a CHD naplózás) rendelkezik. További információkért lásd: [útmutatást PCI - naplózás](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-109"></a>PCI DSS követelmény 10.9

**10.9** győződjön meg arról, hogy a biztonsági házirendek és a hálózati erőforrásokhoz és kártya tulajdonosát adatokhoz való hozzáférés teljes figyelésre műveleti eljárásokká dokumentálva, valamint használatban van, és ismert, hogy az összes érintett fél.


**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló biztosít a használati esetek és egy leírást arról, hogyan a CHD felügyelete és védelme.|




