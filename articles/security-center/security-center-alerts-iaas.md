---
title: Veszélyforrások észlelése a Azure Security Center virtuális gépek és kiszolgálók esetében | Microsoft Docs
description: Ez a témakör bemutatja a virtuális gépek és a kiszolgálók Azure Security Centerban elérhető riasztásait.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 0d6f05524be41693ba280b0f0162f65b517ec281
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219621"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Veszélyforrások észlelése virtuális gépek és kiszolgálók esetében Azure Security Center

Ez a témakör bemutatja a különböző típusú észlelési módszereket és riasztásokat a virtuális gépek és a kiszolgálók számára a következő operációs rendszerekkel. A támogatott verziók listáját lásd: [Azure Security Center által támogatott platformok és szolgáltatások](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows<a name="windows-machines"></a>

A Azure Security Center együttműködik az Azure-szolgáltatásokkal a Windows-alapú gépek monitorozásához és védeleméhez. Security Center az összes szolgáltatás riasztásait és szervizelési javaslatait egyszerűen használható formátumban jeleníti meg.

### Windows Server Defender ATP<a nanme="windows-atp"></a>

A Security Center a felhőalapú munkaterhelés-védelmi platformokat a Windows Server Defender komplex veszélyforrások elleni védelem (ATP) integrálásával bővíti. Ez átfogó végpont-észlelési és-reagálási (EDR) képességeket biztosít.

> [!NOTE]
> A Windows Server Defender ATP-érzékelő automatikusan engedélyezve van a Security Center-t használó Windows-kiszolgálókon.

Ha a Windows Server Defender ATP fenyegetést észlel, riasztást indít el. A riasztás a Security Center irányítópulton jelenik meg. Az irányítópulton megtekintheti a Microsoft Defender ATP-konzolt, és részletes vizsgálatot végezhet a támadás hatókörének felderítése érdekében. További információ a Windows Server Defender ATP szolgáltatással kapcsolatban: kiszolgálók beléptetése [a Microsoft DEFENDER ATP szolgáltatásba](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Összeomlási memóriakép elemzése<a nanme="windows-dump"></a>

Amikor a szoftver összeomlik, egy összeomlási memóriakép rögzíti a memória egy részét az összeomlás időpontjában.

Előfordulhat, hogy az összeomlást kártevő okozta, vagy kártevőt tartalmaz. A biztonsági termékek észlelésének elkerülése érdekében a kártevő szoftverek különböző formái egy fájl nélküli támadást használnak, amely elkerüli a lemezre írást, vagy titkosítja a lemezre írt összetevőket. Az ilyen típusú támadásokat nehéz felderíteni a hagyományos lemezes megközelítések használatával.

A memória-elemzés használatával azonban az ilyen típusú támadásokat is kiderítheti. Az összeomlási memóriakép memóriájának elemzésével a Security Center képes azonosítani a támadás által használt technikákat. Előfordulhat például, hogy a támadás megkísérli a szoftver biztonsági réseit kihasználni, hozzáférni a bizalmas adatokhoz, és titokban megmarad egy feltört gépen belül. Security Center ez a művelet a gazdagépek minimális teljesítményére gyakorolt hatásával működik.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Kód befecskendezése felderítve**|A kódinjektálás olyan művelet, amely végrehajtható modulokat szúr be a futó folyamatokba vagy szálakba. Ezt a technikát a kártevők használják az adateléréshez, miközben a rendszer sikeresen elrejti magát, hogy megakadályozza a keresését és eltávolítását. <br/>Ez a riasztás jelzi, hogy injektált modul szerepel az összeomlási memóriaképben. A kártékony és a nem rosszindulatú befecskendezett modulok megkülönböztetéséhez Security Center ellenőrzi, hogy a befecskendezett modul megfelel-e a gyanús viselkedés profiljának.|
|**Gyanús kódrészlet felderítve**|Azt jelzi, hogy a kód szegmensét nem szabványos metódusok, például a tükröző injektálás és a folyamat-elszámolási módszer használatával osztották le. A riasztás a jelentett kódrészlet további jellemzőit tartalmazza, amelyeket a rendszer a jelentett kódrészlet képességeinek és viselkedésének biztosításához dolgoz fel.|
|**Héjkód felderítve**|A héjkód az a kártékony kód, amely azután fut le, hogy a kártevő a szoftver biztonsági rését kihasználva bejut a rendszerbe.<br/>Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése olyan végrehajtható kódot talált, amely a kártékony hasznos adatok által gyakran végrehajtott viselkedést mutatja. Bár a nem kártevő szoftverek is elvégezhetik ezt a viselkedést, nem jellemző a normál szoftverfejlesztői gyakorlatokra.|

### Fájlok közötti támadás észlelése<a nanme="windows-fileless"></a>

Gyakoriak a végpontokat célzó, fájlokkal nem rendelkező támadások. Az észlelés elkerülése érdekében a fájlokkal nem rendelkező támadások rosszindulatú hasznos adatokat szúrnak be a memóriába. A támadó adattartalmai a feltört folyamatok memóriáján belül maradnak, és számos kártékony tevékenységet hajtanak végre.

A fájlok közötti támadás észlelése, az automatizált memória kriminalisztikai módszerei azonosítják a fájlokra vonatkozó támadási segédanyagokat, technikákat és viselkedéseket. Ez a megoldás rendszeres időközönként ellenőrzi a gépet, és kinyeri az eredményeket közvetlenül a biztonsági szempontból kritikus folyamatok memóriájában.

Megtalálhatja a kihasználat, a kódok befecskendezését és a kártékony hasznos adatok végrehajtását. A fájlok közötti támadás észlelése részletes biztonsági riasztásokat generál a riasztások osztályozásának, korrelációjának és az alsóbb szintű válaszidő felgyorsításához. Ez a megközelítés kiegészíti az eseményvezérelt EDR megoldásokat, és nagyobb észlelési lefedettséget biztosít.

> [!NOTE]
> A Windows-riasztásokat a Azure Security Center forgatókönyv [letöltésével szimulálhatja: Biztonsági riasztások](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**A rendszer a fájlokkal nem rendelkező támadási technikákat észlelt**|A megadott folyamat memóriája tartalmaz egy file UNA támadási eszközkészletet: Meterpreter. A fájl nélkül támadási eszközkészletek általában nem rendelkeznek jelenléttel a fájlrendszerben, így a hagyományos víruskereső szoftverek észlelése bonyolult.|

### <a name="further-reading"></a>További olvasnivalók

Példákat és további információt a Security Center észleléséről a következő témakörben talál:

* [Hogyan Azure Security Center automatizálja a számítógépes támadás észlelését](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Biztonsági rések észlelése a felügyeleti eszközök használatával Azure Security Center](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux<a name="linux-machines"></a>

A Security Center a Linux rendszerű gépekről naplózza a naplózási rekordokat **, az**egyik leggyakrabban használt Linux-naplózási keretrendszerben. a naplózott élet a fővonali kernelben. 

### Linux auditált riasztások és a Microsoft monitoring Agent (MMA) integrációja<a name="linux-auditd"></a>

A naplózott rendszer egy kernel szintű alrendszerből áll, amely a rendszerhívások figyelésének felelőse. Egy megadott szabálykészlet alapján szűri őket, és üzeneteket küld nekik egy szoftvercsatorna számára. A Security Center a Microsoft monitoring Agent (MMA) szolgáltatáson belül a naplózott csomag funkcióit integrálja. Ez az integráció lehetővé teszi az auditált események gyűjtését az összes támogatott Linux-disztribúcióban, előfeltételek nélkül.  

a naplózott rekordok gyűjtése, bővítése és összesítése az eseményekre a Linux MMA-ügynök használatával történik. Security Center folyamatosan bővíti a Linux-jeleket használó új elemzéseket a Felhőbeli és a helyszíni linuxos gépek kártékony viselkedésének észlelése érdekében. A Windows-képességekhez hasonlóan ezek az elemzések a gyanús folyamatokon, a kétes bejelentkezési kísérleteken, a kernel modul betöltésén és más tevékenységeken is kiterjedhetnek. Ezek a tevékenységek jelezhetik, hogy a gép támadás alatt áll, vagy megsértették.  

A következő példák a támadási életciklus különböző szakaszaira kiterjedő elemzésekre mutatnak.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Az SSH-t használó kulcsokhoz tartozó fájl szokatlan módon való elérése folyamatban van**|Az SSH-t használó hitelesítő kulcsok fájlja az ismert kártevő-kampányokhoz hasonló módon lett hozzáférve. Ez a hozzáférés arra utalhat, hogy egy támadó állandó hozzáférést próbál a géphez.|
|**Észlelt adatmegőrzési kísérlet**|A gazdagép adatelemzése azt észlelte, hogy az egyfelhasználós üzemmód indítási parancsfájlja telepítve van. <br/>Mivel ritkán fordul elő, hogy az adott módban bármilyen legitim folyamatra lenne szükség, ez arra utalhat, hogy egy támadó rosszindulatú folyamatot adott hozzá minden egyes futtatási szinthez, hogy biztosítsa az adatmegőrzést.|
|**Az ütemezett feladatok kezelése észlelve**|A gazdagép adatok elemzése az ütemezett feladatok lehetséges kezelését észlelte. A támadók gyakran ütemezett feladatokat vesznek fel azokhoz a gépekhez, amelyeket az adatmegőrzés érdekében feltörtek.|
|**Gyanús fájl időbélyegének módosítása**|A gazdagép adatelemzése gyanús időbélyeg-módosítást észlelt. A támadók gyakran az új eszközökre másolják a meglévő, legitim fájlokból származó időbélyegeket, hogy elkerülje az újonnan eldobott fájlok észlelését.|
|**Új felhasználó lett hozzáadva a sudoers-csoporthoz**|A gazdagép adatelemzése azt észlelte, hogy a felhasználó hozzá lett adva a sudoers csoportjához, amely lehetővé teszi a tagjai számára, hogy magas szintű jogosultságokkal futtassák a parancsokat.|
|**A DHCP-ügyfél DynoRoot biztonsági rése valószínűleg kihasználható**|A Host adatelemzési szolgáltatás egy szokatlan parancs végrehajtását észlelte a dhclient-parancsfájl fölérendelt folyamatával.|
|**A rendszer gyanús kernel modult észlelt**|A gazdagép adatai elemzése során a rendszer kernel-modulként betöltött megosztott objektumot észlelt. Ez lehet jogos tevékenység, vagy jelzi, hogy az egyik gép sérült.|
|**A rendszer a digitális valuta kitermeléséhez kapcsolódó folyamatot észlelt**|A Host adatelemzési szolgáltatás egy olyan folyamat végrehajtását észlelte, amely normál esetben a digitális valuta kitermeléséhez van társítva.|
|**Lehetséges port továbbítása külső IP-címnek**|A Host adatelemzési szolgáltatás a port továbbításának kezdeményezését észlelte egy külső IP-címhez.|

> [!NOTE]
> A Windows-riasztásokat a Azure Security Center forgatókönyv [letöltésével szimulálhatja: Linux-észlelések](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).


További információkért lásd:  

* [Azure Security Center kihasználása a feltört Linux-gépek támadásának észlelése érdekében](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [A Azure Security Center felismeri a Linuxon elérhető új biztonsági réseket](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 