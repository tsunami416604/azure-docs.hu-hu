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
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a5ed91cef6e49fcb71c35f2262479be45a018651
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754306"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Veszélyforrások észlelése virtuális gépek és kiszolgálók esetében Azure Security Center

Ez a témakör bemutatja a különböző típusú észlelési módszereket és riasztásokat a virtuális gépek és a kiszolgálók számára a következő operációs rendszerekkel. A támogatott verziók listáját lásd: [Azure Security Center által támogatott platformok és szolgáltatások](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows<a name="windows-machines"></a>

A Azure Security Center együttműködik az Azure-szolgáltatásokkal a Windows-alapú gépek monitorozásához és védeleméhez. Security Center az összes szolgáltatás riasztásait és szervizelési javaslatait egyszerűen használható formátumban jeleníti meg.

* A **Microsoft Defender ATP** <a name="windows-atp"></a> -Security Center a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) integrálásával kiterjeszti a felhőalapú munkaterhelés-védelmi platformokat. Ez átfogó végpont-észlelési és-reagálási (EDR) képességeket biztosít.

    > [!NOTE]
    > A Microsoft Defender ATP-érzékelő automatikusan engedélyezve van a Security Center-t használó Windows-kiszolgálókon.

    Ha a Microsoft Defender ATP fenyegetést észlel, riasztást indít el. A riasztás a Security Center irányítópulton jelenik meg. Az irányítópulton megtekintheti a Microsoft Defender ATP-konzolt, és részletes vizsgálatot végezhet a támadás hatókörének felderítése érdekében. További információ a Microsoft Defender ATP szolgáltatással kapcsolatban: kiszolgálók beléptetése [a Microsoft DEFENDER ATP szolgáltatásba](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Összeomlási memóriakép elemzése** <a name="windows-dump"></a> – a szoftverek összeomlásakor egy összeomlási memóriakép rögzíti a memória egy részét az összeomlás időpontjában.

    Előfordulhat, hogy az összeomlást kártevő okozta, vagy kártevőt tartalmaz. A biztonsági termékek észlelésének elkerülése érdekében a kártevő szoftverek különböző formái egy fájl nélküli támadást használnak, amely elkerüli a lemezre írást, vagy titkosítja a lemezre írt összetevőket. Az ilyen típusú támadásokat nehéz felderíteni a hagyományos lemezes megközelítések használatával.

    A memória-elemzés használatával azonban az ilyen típusú támadásokat is kiderítheti. Az összeomlási memóriakép memóriájának elemzésével a Security Center képes azonosítani a támadás által használt technikákat. Előfordulhat például, hogy a támadás megkísérli a szoftver biztonsági réseit kihasználni, hozzáférni a bizalmas adatokhoz, és titokban megmarad egy feltört gépen belül. Security Center ez a művelet a gazdagépek minimális teljesítményére gyakorolt hatásával működik.

    Az összeomlási memóriakép elemzési riasztásával kapcsolatos részletekért tekintse meg a [riasztások hivatkozási táblázatát](alerts-reference.md#alerts-windows).

* A **fájlok közötti támadás észlelése** <a name="windows-fileless"></a> – a végpontokat célzó, nem gyakori támadások. Az észlelés elkerülése érdekében a fájlokkal nem rendelkező támadások rosszindulatú hasznos adatokat szúrnak be a memóriába. A támadó adattartalmai a feltört folyamatok memóriáján belül maradnak, és számos kártékony tevékenységet hajtanak végre.

    A fájlok közötti támadás észlelése, az automatizált memória kriminalisztikai módszerei azonosítják a fájlokra vonatkozó támadási segédanyagokat, technikákat és viselkedéseket. Ez a megoldás rendszeres időközönként ellenőrzi a gépet, és kinyeri az eredményeket közvetlenül a biztonsági szempontból kritikus folyamatok memóriájában.

    Megtalálhatja a kihasználat, a kódok befecskendezését és a kártékony hasznos adatok végrehajtását. A fájlok közötti támadás észlelése részletes biztonsági riasztásokat generál a riasztások osztályozásának, korrelációjának és az alsóbb szintű válaszidő felgyorsításához. Ez a megközelítés kiegészíti az eseményvezérelt EDR megoldásokat, és nagyobb észlelési lefedettséget biztosít.

    A fájlokba nem kerülő támadás észlelésével kapcsolatos riasztások részleteiért tekintse meg a [riasztások hivatkozási táblázatát](alerts-reference.md#alerts-windows).

> [!NOTE]
> A Windows-riasztásokat a [biztonsági riasztások Azure Security Center](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)forgatókönyvek letöltésével szimulálhatja.

## Linux<a name="linux-machines"></a>

A Security Center a Linux rendszerű gépekről naplózza a naplózási rekordokat **, az**egyik leggyakrabban használt Linux-naplózási keretrendszerben. a naplózott élet a fővonali kernelben. 

* **Linux auditált riasztások és Microsoft monitoring Agent (MMA) integráció** <a name="linux-auditd"></a> – az auditált rendszer egy kernel szintű alrendszerből áll, amelynek feladata a rendszerhívások figyelése. Egy megadott szabálykészlet alapján szűri őket, és üzeneteket küld nekik egy szoftvercsatorna számára. A Security Center a Microsoft monitoring Agent (MMA) szolgáltatáson belül a naplózott csomag funkcióit integrálja. Ez az integráció lehetővé teszi az auditált események gyűjtését az összes támogatott Linux-disztribúcióban, előfeltételek nélkül.  

    a naplózott rekordok gyűjtése, bővítése és összesítése az eseményekre a Linux MMA-ügynök használatával történik. Security Center folyamatosan bővíti a Linux-jeleket használó új elemzéseket a Felhőbeli és a helyszíni linuxos gépek kártékony viselkedésének észlelése érdekében. A Windows-képességekhez hasonlóan ezek az elemzések a gyanús folyamatokon, a kétes bejelentkezési kísérleteken, a kernel modul betöltésén és más tevékenységeken is kiterjedhetnek. Ezek a tevékenységek jelezhetik, hogy a gép támadás alatt áll, vagy megsértették.  

    A következő példák a támadási életciklus különböző szakaszaira kiterjedő elemzésekre mutatnak.

    A Linux-riasztások listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-linux)tartalmazza.

> [!NOTE]
> A Linux-riasztásokat a Linux- [észlelések Azure Security Center](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)forgatókönyv letöltésével szimulálhatja.

 
 ## <a name="next-steps"></a>Következő lépések

Példákat és további információt a Security Center észleléséről a következő témakörben talál:

* [Hogyan Azure Security Center automatizálja a számítógépes támadás észlelését](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Biztonsági rések észlelése a felügyeleti eszközök használatával Azure Security Center](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)
* [Azure Security Center kihasználása a feltört Linux-gépek támadásának észlelése érdekében](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [A Azure Security Center felismeri a Linuxon elérhető új biztonsági réseket](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)