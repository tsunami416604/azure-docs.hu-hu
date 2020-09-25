---
title: Azure Security teljesítményteszt v2 – incidens válasza
description: Azure Security benchmark v2 – incidens válasza
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 172607a7f8f036bbfb68e8d15e77b2a3e3fb5377
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326388"
---
# <a name="security-control-v2-incident-response"></a>Security Control v2: incidens válasza

Az incidensekre adott válasz az incidensek életciklusának előkészítését, észlelését és elemzését, a tárolást és az incidens utáni tevékenységeket fedi le. Ez magában foglalja az Azure-szolgáltatások, például a Azure Security Center és a Sentinel használatát az incidensek reagálási folyamatának automatizálásához.

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: előkészítés – az incidensek frissítési folyamatának frissítése az Azure-ban

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| IR-1 | 19 | IR-4, IR-8 |

Győződjön meg arról, hogy a szervezetnek vannak folyamatai a biztonsági incidensekre való reagálásra, frissítette ezeket a folyamatokat az Azure-ban, és rendszeresen gyakorolja őket a készültség biztosítására.

- [A biztonság megvalósítása a nagyvállalati környezetben](https://aka.ms/AzSec4)

- [Incidens-válasz referenciájának útmutatója](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Fenyegetésészlelési intelligencia](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: előkészítés – telepítési incidens értesítése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| IR-2 | 19,5 | IR-4, IR-5, IR-6, IR-8 |

Biztonsági incidensek elérhetőségi adatainak beállítása a Azure Security Centerban. A Microsoft a kapcsolattartási adatokat arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. Lehetőség van az incidensek riasztásának és az értesítések különböző Azure-szolgáltatásokban való testreszabására is az incidensek igényei alapján. 

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: észlelés és elemzés – incidensek létrehozása magas minőségi riasztások alapján

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| IR-3 | 19,6 | IR-4, IR-5 |

Ellenőrizze, hogy van-e olyan folyamat, amely kiváló minőségű riasztásokat hoz létre, és mérje fel a riasztások minőségét. Ez lehetővé teszi, hogy megismerje a múltbeli incidensek tanulságait, és rangsorolja a riasztásokat az elemzők számára, hogy ne pazarolja az időt a hamis pozitív értékekre. 

A kiváló minőségű riasztások a múltbeli incidensek, a hitelesített közösségi források és a riasztások létrehozásához és tisztításához tervezett eszközök alapján hozhatók létre a különböző jelforrások elutasításával és korrelációs megoldásával. 

Azure Security Center kiváló minőségű riasztásokat biztosít számos Azure-eszközön. Az ASC adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba. Az Azure Sentinel lehetővé teszi, hogy speciális riasztási szabályokat hozzon létre az incidensek automatikus létrehozásához a vizsgálathoz. 

A Azure Security Center-riasztások és-javaslatok exportálásával az Exportálás funkcióval azonosíthatók az Azure-erőforrásokkal kapcsolatos kockázatok. Riasztásokat és javaslatokat manuálisan vagy folyamatos, folyamatos módon exportálhat.

- [Az Exportálás konfigurálása](../../security-center/continuous-export.md)

- [Riasztások továbbítása az Azure Sentinelbe](../../sentinel/connect-azure-security-center.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Fenyegetésészlelési intelligencia](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: észlelés és elemzés – incidens vizsgálata

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

Győződjön meg arról, hogy az elemzők különböző adatforrásokat tudnak lekérdezni és használni a lehetséges incidensek kivizsgálásakor, így teljes körű képet kaphat arról, hogy mi történt. Különböző naplókat kell gyűjteni a lehetséges támadók tevékenységének nyomon követéséhez a támadási láncon belül a vak foltok elkerülése érdekében.  Gondoskodjon arról is, hogy az elemzések és a megszerzett ismeretek rögzítése más elemzők és a későbbi korábbi referenciák esetében is megtörténjen.  

A vizsgálathoz használt adatforrások közé tartoznak azok a központi naplózási források, amelyeket a rendszer már gyűjt a hatókörbeli szolgáltatásokból és a futó rendszerekről, de a következőket is tartalmazhatja:

- Hálózati adatok – hálózati biztonsági csoportok folyamatábrája, Azure Network Watcher és Azure Monitor a hálózati folyamat naplói és egyéb elemzési információk rögzítésére. 

- Futó rendszerek pillanatképei: 

    - Az Azure-beli virtuális gép pillanatkép-funkciója segítségével pillanatkép készíthető a futó rendszer lemezéről. 

    - A futó rendszer memóriájának pillanatképének létrehozásához használja az operációs rendszer natív memóriaképének képességét.

    - Használja az Azure-szolgáltatások pillanatkép-szolgáltatását, vagy az Ön szoftverének saját funkciója a futó rendszerek pillanatképének létrehozásához.

Az Azure Sentinel széles körű adatelemzést tesz lehetővé gyakorlatilag bármely naplózási forrás és egy eseti felügyeleti portál között az incidensek teljes életciklusának kezeléséhez. A nyomozás során felmerülő hírszerzési információk követési és jelentéskészítési célokra is társíthatók. 

- [Windows-gép lemezének pillanatképe](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [A Linux rendszerű gép lemezének pillanatképe](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure a diagnosztikai információk és a memóriakép-gyűjtés támogatása](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidensek vizsgálata az Azure Sentineltel](../../sentinel/tutorial-investigate-cases.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Fenyegetésészlelési intelligencia](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: észlelés és elemzés – az incidensek rangsorolása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| IR-5 | 19,8 | CA-2, IR-4 |

Adja meg a kontextust az elemzők számára, amelyeken a riasztás súlyossága és az eszközök érzékenysége alapján az elsőre kell összpontosítani. 

A Azure Security Center az egyes riasztásokhoz súlyosságot rendel, így rangsorolhatja, hogy a rendszer mely riasztásokat vizsgálja meg először. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett a címkék használatával megjelölheti az erőforrásokat, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosítására és kategorizálására, különösen a bizalmas adatok feldolgozására.  Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../../security-center/security-center-alerts-overview.md)

- [Címkék használata az erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Fenyegetésészlelési intelligencia](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: tárolás, mentesítés és helyreállítás – az incidensek kezelésének automatizálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| IR-6 | 19 | IR-4, IR-5, IR-6 |

Automatizálja a manuális ismétlődő feladatokat a válaszidő felgyorsításához és az elemzők terhének csökkentéséhez. A manuális feladatok végrehajtása hosszabb időt vehet igénybe, és lelassítja az egyes incidenseket, és csökkenti az elemző által kezelhető incidensek számát. A manuális feladatok növelik az elemzői fáradtságot is, ami növeli a késést okozó emberi hibák kockázatát, és csökkenti az elemzők azon képességét, hogy az összetett feladatokon hatékonyan összpontosítsanak. A Azure Security Center és az Azure Sentinel munkafolyamat-automatizálási funkcióinak használatával automatikusan aktiválhatja a műveleteket, vagy futtathat egy forgatókönyvet a bejövő biztonsági riasztásokra való reagáláshoz. A forgatókönyv végrehajtja a műveleteket, például értesítések küldését, fiókok letiltását és a problémás hálózatok elkülönítését. 

- [Munkafolyamat-automatizálás konfigurálása Security Centerban](../../security-center/workflow-automation.md)

- [Automatizált veszélyforrásokkal kapcsolatos válaszok beállítása Azure Security Center](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Automatizált veszélyforrásokkal kapcsolatos válaszok beállítása az Azure Sentinelben](../../sentinel/tutorial-respond-threats-playbook.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Fenyegetésészlelési intelligencia](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

