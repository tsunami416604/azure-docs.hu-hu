---
title: A fenyegetés-észlelési-beli virtuális gépek és kiszolgálók az Azure Security Centerben |} A Microsoft Docs
description: Ez a témakör bemutatja a virtuális gép és a kiszolgáló elérhető riasztások az Azure Security Centerben.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 5487b4f49f5dbf7b968cd45d40555c69b54c329a
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571581"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>Fenyegetésészlelés virtuális gépek és kiszolgálók az Azure Security Centerben

Ez a témakör a különböző típusú észlelési módszerek és elérhető riasztások be az virtuális gépek és kiszolgálók esetében a következő operációs rendszereken. Támogatott verziók listáját lásd: [platformok és az Azure Security Center által támogatott funkciók](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

A Security Center integrálható az Azure-szolgáltatások figyelése és a Windows-alapú gépek védelméhez.  A Security Center egy könnyen használható formátumban mutat be a riasztásokat és szervizelési javaslatokat az ezen szolgáltatások mindegyikéhez.

### Microsoft Server Defender ATP <a nanme="windows-atp"></a>

Az Azure Security Center a számítási feladatok védelmét platform kiterjeszti a Windows Defender komplex veszélyforrások elleni védelem (ATP) integrálásával. Ez a végpont észlelés és válasz (EDR) funkciókészletet biztosít.

> [!NOTE]
> Windows-kiszolgálók, amelyek előkészítve az Azure Security Center automatikusan engedélyezve van a Windows Server Defender ATP-érzékelő.

Ha a Windows Server Defender ATP veszélyforrást észlel, riasztást. A riasztás a Security Center irányítópultján jelenik meg. Az irányítópultról is kimutatás az a Windows Defender ATP konzol végrehajtásához a részletes vizsgálat elvégzésével nyújt betekintést a támadások hatókörét. További információ a Windows Server Defender ATP: [előkészítése kiszolgálók a Windows Defender ATP szolgáltatásnak](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Összeomlási memóriakép elemzése <a nanme="windows-dump"></a>

Amikor a szoftver összeomlik, egy összeomlási memóriakép rögzíti a memória egy részét az összeomlás időpontjában.

Összeomlás megakadályozhatta kártevő vagy kártevő. Biztonsági termékek által érzékelt elkerüléséhez különféle formában előforduló kártevők használja, írnak a lemezre vagy titkosított írt szoftverösszetevőket elkerülhető fileless támadás lemezre. Ilyen típusú támadások nehéz a hagyományos lemezalapú megoldásokkal észlelheti.

Azonban az ilyen típusú támadás is észlelhető memóriaelemzéssel felismerhetők. Az összeomlási memóriaképben található memóriarész elemzésével a Security Center észlelni tudja a szoftver biztonsági réseit, hozzáférnek a bizalmas adatokhoz és elrejtőzve a feltört gépen használja a támadási módszerek. Teljesítmény minimális hatással van a gazdagépek a Security Center háttérrendszere végzi.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Kódinjektálás észlelhető**|A kódinjektálás olyan művelet, amely végrehajtható modulokat szúr be a futó folyamatokba vagy szálakba. Ezt a módszert használja adatok elérését, kártevő szoftverek magát, hogy sikeresen elrejtése alatt található és eltávolítása. <br/>Ez a riasztás jelzi, hogy injektált modul szerepel az összeomlási memóriaképben. A kártékony és a nem kártékony injektált modulok megkülönböztetéséhez, a Security Center ellenőrzi, hogy az injektált modulra illik-e a gyanús működés profilja.|
|**Gyanús kódszegmens észlelhető**|Azt jelzi, hogy egy kódszegmens lett lefoglalva nem szabványos módszerrel, például reflektív injektálással vagy a folyamat hollowing. A riasztás környezetét a képességeket biztosít dolgozott a kódszegmens további jellemzőit és a jelentett kódszegmens viselkedéseit biztosít.|
|**Héjkód észlelhető**|A héjkód az a kártékony kód, amely azután fut le, hogy a kártevő a szoftver biztonsági rését kihasználva bejut a rendszerbe.<br/>Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése olyan-e, amely a kártékony kódokra viselkedés érvényes végrehajtható kódot talált. Bár a nem rosszindulatú szoftverhez is hajthatják végre ezt a viselkedést, akkor sem jellemzően a szokásos szoftverfejlesztési gyakorlatban.|

### Fileless támadás észlelése <a nanme="windows-fileless"></a>

Az Azure-ban ügyfeleinknél végpontok célzó fileless támadások rendszeresen látható.

Észlelési elkerülése érdekében fileless támadások kártékony kódokra betöltése a memóriába. Támadó is észleltünk adattartalmakat. a memória, a feltört folyamatok belül maradnak, és végezze el számos különféle szándékú tevékenységeket.

Fileless támadások észlelése, a automatizált memória Törvényszéki technikák fileless támadási eszközkészleteket technikák és viselkedések azonosításához. Ez a megoldás rendszeres időközönként megvizsgálja a gépen futásidőben, és elemzéseket közvetlenül kigyűjti a memória, a biztonsági szempontból kritikus fontosságú folyamatok.

Bizonyítékok kiaknázása, a kódinjektálás és a kártékony kódokra végrehajtásának találja meg. Fileless támadások észlelése a riasztási osztályozási, korrelációvizsgáló és alsóbb rétegbeli válaszidő felgyorsítása részletes biztonsági riasztásokat állít elő. Ez a megközelítés eseményalapú EDR megoldások biztosítása nagyobb észlelési lefedettség egészíti ki.

> [!NOTE]
> Letöltés szimulálhatja a Windows-riasztások [Azure Security Center-forgatókönyv](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef): Biztonsági riasztások, és kövesse a megadott

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Észlelt fileless támadási módszer**|A memória, a folyamat az alább megadott egy fileless támadási eszközkészlet tartalmazza: Meterpreter. Fileless támadási eszközkészleteket általában nem rendelkezik jelenlét a hagyományos víruskereső észlelési megnehezítve a fájlrendszerben.|

### <a name="further-reading"></a>További olvasnivalók

Példák és további információ a Security Center észlelési:

* [Hogyan automatizálja az Azure Security Center a kibertámadás felismerése](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Hogyan észleli az Azure Security Center a biztonsági rések felügyeleti eszközök használatával](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

A Security Center összegyűjti a Linux rendszerű gépek naplórekordok **auditd**egy, a leggyakrabban használt Linux-naplózási keretrendszer. auditd kellene már hosszú ideje és a által kernel élő előnye van. 

### Linux auditd riasztásokat és a Microsoft Monitoring Agent (MMA) integrációja <a name="linux-auditd"></a>

A auditd rendszer rendszermag-szintű alrendszer, amelynek feladata a rendszerhívásokat figyelését, egy adott szabálykészleten szerinti szűrés és azokat az üzeneteket írna a szoftvercsatorna áll. A Security Center integrálható a auditd csomagból belül a Microsoft Monitoring Agent (MMA) funkcióit. Ez az integráció lehetővé teszi, hogy auditd események gyűjtése az összes támogatott Linuxos kiadásokról előfeltételeket nélkül.  

auditd rekordokat gyűjti, bővített, és események Linux MMA-ügynök használatával összesíti. A Security Center folyamatosan dolgozik hozzáadása új analytics, a Linux jelzi a felhő rosszindulatú viselkedésének észlelésére és a helyszíni Linuxos gépek használják. Hasonló Windows-funkciókat, ezek az analitikák ívelhet át több gyanús folyamatok, kétséges bejelentkezési kísérlet, kernel modul betöltése és más tevékenységek, amely egy gép támadás alatt áll, vagy nem tettek jelzi.  

Az alábbiakban néhány példa az elemzés, amelyek bemutatják, hogyan tudjuk ívelhet át több a támadás életciklus különböző szakaszaiban.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Látható, szokatlan módon fér hozzá az SSH hitelesített kulcsfájlhoz folyamat**|Hitelesített kulcsfájl SSH elérésére az ismert kártevő kampányok hasonló módszer került sor. Ez a hozzáférés jelezheti, hogy egy támadó megpróbálja állandó hozzáférést a gép|
|**Észlelt adatmegőrzés kísérlet**|Gazdagép adatelemzés azt észlelte, hogy telepítették-e egy indítási szkriptet a egyfelhasználós üzemmódra. <br/>Mivel ez ritka, hogy bármilyen jogos folyamat ebben a módban végrehajtásához szükségesek, ez arra utalhat, hogy egy támadó hozzá van adva egy rosszindulatú folyamat minden Futtatás szintű garantálja az adatmegőrzés.|
|**Észlelt az ütemezett feladatok kezelése**|Gazdagép-adatok elemzése az ütemezett feladatok lehetséges értelmezésének észlelte. A támadók gyakran adja hozzá az ütemezett feladatok gépek azok biztonsága próbál a jeggyel megőrzése sérült.|
|**Gyanús fájlt időbélyeg módosítása**|Gazdagép-adatok elemzése észlelt gyanús időbélyeg módosítása. A támadók gyakran másolása időbélyegeket meglévő kockázatmentes fájlok újonnan kihagyott fájlok észlelésének elkerülésére új eszközök|
|**Új felhasználó lett felvéve a sudoers csoporthoz**|Gazdagép adatelemzés azt észlelte, hogy egy felhasználó lett felvéve a sudoers csoporthoz, amelynek tagjai kiemelt jogosultságokkal futtathatnak parancsokat.|
|**Dhcp-ügyfél biztonsági DynoRoot valószínűleg biztonsági rés kiaknázása elleni**|Gazdagép-adatok elemzése a szülő folyamattal dhclient parancsfájl egy szokatlan parancs végrehajtása észlelhető.|
|**Gyanús kernel-modul észlelhető**|Gazdagép-adatok elemzése egy megosztott objektum fájlt, mint egy kernelmodul betöltése észlelt. Ez lehet jogos tevékenységet, vagy azt jelzi, hogy egyik gépet feltörték.|
|**Digitális pénznem adatbányászati észlelt kapcsolódó folyamatok**|Gazdagép-adatok elemzése digitális pénznem adatbányászati leveszi a folyamat végrehajtása észlelhető|
|**A lehetséges porttovábbítást a külső IP-cím**|Gazdagép adatelemzés azt észlelte, hogy a továbbítás külső IP-cím, port kezdeményezéséről.|

> [!NOTE]
> Windows-riasztások szimulálhatja a Letöltés [Azure Security Center-forgatókönyv: Biztonsági riasztások](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef) , és kövesse a megadott.


További információval a következő cikkek szolgálnak:  

* [Használja ki az Azure Security Center a feltört Linux rendszerű számítógépek, a támadás észlelése](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure Security Center észlelni tudja a felmerülő biztonsági rések Linux rendszeren](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 