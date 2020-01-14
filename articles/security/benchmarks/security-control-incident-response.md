---
title: Azure Security Control – incidens válasza
description: Biztonsági vezérlő Incidensének válasza
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934422"
---
# <a name="security-control-incident-response"></a>Biztonsági ellenőrzés: incidens válasza

A szervezet információinak és hírnevének biztosításához az incidensek válaszának infrastruktúráját (például a csomagokat, a meghatározott szerepköröket, a képzést, a kommunikációt, a felügyeletet és a felügyeletet) a támadás gyors felderítése érdekében, majd hatékonyan tartalmazza a kárt, felszámolja a támadó jelenlétét, és helyreállítja a hálózat és a rendszerek integritását.

## <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 10.1 | 19,1, 19,2, 19,3 | Ügyfél |

Hozzon létre egy incidensre vonatkozó választ a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az észleléstől az incidens utáni felülvizsgálatig.

Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

A Microsoft Security Response Center egy Incidensének anatómiája:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásához:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 10.2 | 19,8 | Ügyfél |

A Security Center az egyes riasztásokhoz súlyosságot rendel, így rangsorolhatja, hogy a rendszer mely riasztásokat vizsgálja meg először. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben nem termelt), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

## <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 10,3 | 19 | Ügyfél |

Gyakorlatok végrehajtása a rendszerek incidensekre adott válaszait rendszeres ütemben tesztelheti. Azonosítsa a gyenge pontokat és a réseket, és szükség szerint módosítsa a tervet.

Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatának megtervezéséhez:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 10,4 | 19,5 | Ügyfél |

A Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte.  A problémák megoldása érdekében tekintse át az incidenseket a tény után.

A Azure Security Center biztonsági kapcsolattartó beállítása:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 10,5 | 19,6 | Ügyfél |

Exportálja Azure Security Center riasztásait és javaslatait a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Használhatja a Azure Security Center adatösszekötőt a riasztások Sentinel továbbításához.

Folyamatos exportálás konfigurálása:

https://docs.microsoft.com/azure/security-center/continuous-export

Riasztások továbbítása az Azure Sentinelbe:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 10,6 | 19 | Ügyfél |

A Azure Security Center munkafolyamat-automatizálási szolgáltatásával automatikusan elindíthatja a válaszokat a biztonsági riasztások és javaslatok &quot;Logic Apps&quot; használatával.

A Munkafolyamat-automatizálás és a Logic Apps konfigurálása:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő biztonsági vezérlőt: [behatolási tesztek és Red Team Exercises](security-control-penetration-tests-red-team-exercises.md)