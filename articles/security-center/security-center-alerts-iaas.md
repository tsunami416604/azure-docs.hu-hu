---
title: Veszélyforrások észlelése virtuális gépek &-kiszolgálókon Azure Security Centerban | Microsoft Docs
description: Ez a témakör bemutatja a virtuális gépek és a kiszolgálók Azure Security Centerban elérhető riasztásait.
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
ms.author: v-mohabe
ms.openlocfilehash: f23865fc0a1943a5157e4ff8eb8de10a71ef0883
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295788"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>Veszélyforrások észlelése virtuális gépek &-kiszolgálókon Azure Security Center

Ez a témakör bemutatja a különböző típusú észlelési módszereket és riasztásokat a virtuális gépek és a kiszolgálók számára a következő operációs rendszerekkel. A támogatott verziók listáját lásd: [Azure Security Center által támogatott platformok és szolgáltatások](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows<a name="windows-machines"></a>

A Security Center együttműködik az Azure-szolgáltatásokkal a Windows-alapú gépek monitorozásához és védeleméhez.  Security Center az összes szolgáltatás riasztásait és szervizelési javaslatait egyszerűen használható formátumban jeleníti meg.

### Microsoft Server Defender ATP<a nanme="windows-atp"></a>

A Azure Security Center a felhőalapú munkaterhelés-védelmi platformokat a Windows Defender komplex veszélyforrások elleni védelem (ATP) integrálásával bővíti. Ez átfogó végpont-észlelési és-reagálási (EDR) képességeket biztosít.

> [!NOTE]
> A Windows Server Defender ATP-érzékelő automatikusan engedélyezve van a Azure Security Centerba beépített Windows-kiszolgálókon.

Ha a Windows Server Defender ATP fenyegetést észlel, riasztást indít el. A riasztás a Security Center irányítópulton jelenik meg. Az irányítópulton eldöntheti, hogy a Windows Defender ATP-konzolon részletes vizsgálatot végezzen a támadás hatókörének felderítése érdekében. A Windows Server Defender ATP szolgáltatással kapcsolatos további információkért lásd: [a Windows DEFENDER ATP szolgáltatást futtató kiszolgálók](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Összeomlási memóriakép elemzése<a nanme="windows-dump"></a>

Amikor a szoftver összeomlik, egy összeomlási memóriakép rögzíti a memória egy részét az összeomlás időpontjában.

Előfordulhat, hogy az összeomlást kártevő okozta, vagy kártevőt tartalmaz. A biztonsági termékek észlelésének elkerülése érdekében a kártevő szoftverek különböző formái egy fájl nélküli támadást használnak, amely elkerüli a lemezre írást, vagy titkosítja a lemezre írt összetevőket. Az ilyen típusú támadásokat nehéz felderíteni a hagyományos lemezes megközelítések használatával.

Ez a fajta támadás azonban a memória elemzése alapján észlelhető. Az összeomlási memóriakép memóriájának elemzésével a Security Center felismeri azokat a technikákat, amelyeket a támadás használ a szoftverek biztonsági réseinak kiaknázására, a bizalmas adatok elérésére és a titokban való megőrzésre egy sérült gépen belül. Ezt a Security Center háttérrendszer a gazdagépek minimális teljesítményére gyakorolt hatásával hajtja végre.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Kód befecskendezése felderítve**|A kódinjektálás olyan művelet, amely végrehajtható modulokat szúr be a futó folyamatokba vagy szálakba. Ezt a technikát a kártevők használják az adateléréshez, miközben a rendszer sikeresen elrejti magát, hogy megakadályozza a keresését és eltávolítását. <br/>Ez a riasztás jelzi, hogy injektált modul szerepel az összeomlási memóriaképben. A kártékony és a nem rosszindulatú befecskendezett modulok megkülönböztetéséhez Security Center ellenőrzi, hogy a befecskendezett modul megfelel-e a gyanús viselkedés profiljának.|
|**Gyanús kódrészlet felderítve**|Azt jelzi, hogy egy kódrészletet nem szabványos metódusokkal, például a tükröző injektálással és a folyamat-bemélyedéssel foglalt le. A riasztás a jelentett kódrészlet további jellemzőit tartalmazza, amelyeket a rendszer a jelentett kódrészlet képességeinek és viselkedésének biztosításához dolgoz fel.|
|**Héjkód felderítve**|A héjkód az a kártékony kód, amely azután fut le, hogy a kártevő a szoftver biztonsági rését kihasználva bejut a rendszerbe.<br/>Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése olyan végrehajtható kódot talált, amely a kártékony hasznos adatok által gyakran végrehajtott viselkedést mutatja. Bár a nem kártevő szoftverek is elvégezhetik ezt a viselkedést, nem jellemző a normál szoftverfejlesztés gyakorlatára.|

### Fájlok közötti támadás észlelése<a nanme="windows-fileless"></a>

Az Azure-ban rendszeresen jelennek meg az ügyfelek végpontjának célzására szolgáló, fájlokkal nem rendelkező támadások.

Az észlelés elkerülése érdekében a fájlokkal nem rendelkező támadások rosszindulatú hasznos adatokat szúrnak be a memóriába. A támadó adattartalmai a feltört folyamatok memóriáján belül maradnak, és számos kártékony tevékenységet hajtanak végre.

A fájlok közötti támadás észlelése, az automatizált memória kriminalisztikai módszerei azonosítják a fájlokra vonatkozó támadási segédanyagokat, technikákat és viselkedéseket. Ez a megoldás rendszeresen ellenőrzi a gépet futásidőben, és kinyeri az eredményeket közvetlenül a biztonság szempontjából kritikus folyamatok memóriájában.

Megtalálhatja a kihasználat, a kódok befecskendezését és a kártékony hasznos adatok végrehajtását. A fájlok közötti támadás észlelése részletes biztonsági riasztásokat generál a riasztások osztályozásának, korrelációjának és az alsóbb szintű válaszidő felgyorsításához. Ez a megközelítés a nagyobb észlelési lefedettséget biztosító eseményvezérelt EDR-megoldásokat egészíti ki.

> [!NOTE]
> A Windows-riasztásokat a [Azure Security Center](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)forgatókönyv letöltésével szimulálhatja: Biztonsági riasztások és a megadott irányelvek követése

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**A rendszer a fájlokkal nem rendelkező támadási technikákat észlelt**|Az alább megadott folyamat memóriája egy file un-Attack Toolkit-eszközkészletet tartalmaz: Meterpreter. A fájl nélkül álló támadási eszközkészletek általában nem rendelkeznek jelenléttel a fájlrendszerben, így a hagyományos víruskeresők nehezen észlelhetők.|

### <a name="further-reading"></a>További olvasnivalók

Példák és további információk a Security Center észleléséről:

* [Hogyan Azure Security Center automatizálja a számítógépes támadás észlelését](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Biztonsági rések észlelése a felügyeleti eszközök használatával Azure Security Center](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux<a name="linux-machines"></a>

A Security Center naplózza a Linux rendszerű gépek naplózási rekordjait az auditált használatával, az egyik leggyakrabban használt Linux-naplózási keretrendszerben. a naplózott előnye, hogy hosszú ideje és a fővonali kernelben él. 

### Linux auditált riasztások és a Microsoft monitoring Agent (MMA) integrációja<a name="linux-auditd"></a>

A naplózott rendszer egy kernel szintű alrendszerből áll, amely a rendszerhívások figyelésére, az adott szabálykészlet szerinti szűrésre, valamint az üzenetek egy szoftvercsatornára való írására szolgál. A Security Center a Microsoft monitoring Agent (MMA) szolgáltatáson belül a naplózott csomag funkcióit integrálja. Ez az integráció lehetővé teszi a naplózott események gyűjtését az összes támogatott Linux-disztribúcióban az előfeltételek nélkül.  

a naplózott rekordok gyűjtése, bővítése és összesítése a Linux MMA-ügynök használatával történik. A Security Center folyamatosan dolgozik az új elemzések hozzáadásán, amelyek a Linux-jeleket használják a Felhőbeli és a helyszíni linuxos gépek rosszindulatú viselkedésének észlelésére. A Windows-funkciókhoz hasonlóan ezek az elemzések a gyanús folyamatokon, a kétes bejelentkezési kísérleteken, a kernel modul betöltésén és más tevékenységeken is láthatók, amelyek jelzik, hogy a gép támadás alatt áll, vagy megsértették.  

Az alábbiakban néhány példa látható az elemzésre, amely bemutatja, hogyan terjed ki a támadási életciklus különböző szakaszai között.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Az SSH-t használó kulcsokhoz tartozó fájl szokatlan módon való elérése folyamatban van**|Az SSH-t használó hitelesítő kulcsok fájlja az ismert kártevő-kampányokhoz hasonló módon lett hozzáférve. Ez a hozzáférés azt jelezheti, hogy egy támadó állandó hozzáférést próbál megnyerni a géphez|
|**Észlelt adatmegőrzési kísérlet**|A gazdagép adatelemzése azt észlelte, hogy az egyfelhasználós üzemmód indítási parancsfájlja telepítve van. <br/>Mivel ritkán fordul elő, hogy az adott módban bármilyen legitim folyamatra lenne szükség, ez arra utalhat, hogy egy támadó rosszindulatú folyamatot adott hozzá minden egyes futtatási szinthez, hogy biztosítsa az adatmegőrzést.|
|**Az ütemezett feladatok kezelése észlelve**|A gazdagép adatok elemzése az ütemezett feladatok lehetséges kezelését észlelte. A támadók gyakran ütemezett feladatokat vesznek fel azokhoz a gépekhez, amelyeket az adatmegőrzés érdekében feltörtek.|
|**Gyanús fájl időbélyegének módosítása**|A gazdagép adatelemzése gyanús időbélyeg-módosítást észlelt. A támadók gyakran másolnak időbélyegeket a meglévő legitim fájlokból az új eszközökre az újonnan eldobott fájlok észlelésének elkerülése érdekében|
|**Új felhasználó lett hozzáadva a sudoers-csoporthoz**|A gazdagép adatelemzése azt észlelte, hogy a felhasználó hozzá lett adva a sudoers csoportjához, amely lehetővé teszi a tagjai számára, hogy magas szintű jogosultságokkal futtassák a parancsokat.|
|**A DHCP-ügyfél DynoRoot biztonsági rése valószínűleg kihasználható**|A Host adatelemzési szolgáltatás egy szokatlan parancs végrehajtását észlelte a dhclient parancsfájl szülő folyamatával.|
|**A rendszer gyanús kernel modult észlelt**|A gazdagép adatai elemzése során a rendszer kernel-modulként betöltött megosztott objektumot észlelt. Ez lehet legitim tevékenység, vagy jelezhető, hogy az egyik gépet feltörték.|
|**A rendszer a digitális valuta kitermeléséhez kapcsolódó folyamatot észlelt**|A gazdagép adatelemzési szolgáltatásának során a rendszer a digitális pénzbányászathoz általában társított folyamat végrehajtását észlelte|
|**Lehetséges port továbbítása külső IP-címnek**|A Host adatelemzési szolgáltatás a port továbbításának kezdeményezését észlelte egy külső IP-címhez.|

> [!NOTE]
> A Windows-riasztásokat a Azure Security Center forgatókönyv [letöltésével szimulálhatja: Biztonsági riasztások](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef) és a megadott irányelvek követése.


További információval a következő cikkek szolgálnak:  

* [Azure Security Center kihasználása a feltört Linux-gépek támadásának észlelése érdekében](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [A Azure Security Center felismeri a Linuxon elérhető új biztonsági réseket](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 