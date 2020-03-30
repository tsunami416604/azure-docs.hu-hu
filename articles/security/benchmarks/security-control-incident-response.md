---
title: Azure Security Control – Incidensre adott válasz
description: Biztonsági ellenőrzési incidensre adott válasz
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934422"
---
# <a name="security-control-incident-response"></a>Biztonsági ellenőrzés: Incidensre adott válasz

A szervezet információinak és hírnevének védelme egy incidenselhárítási infrastruktúra (pl. tervek, meghatározott szerepek, képzés, kommunikáció, irányítási felügyelet) kifejlesztésével és megvalósításával a támadások gyors felfedezéséhez, majd hatékonyan tartalmazza a kárt, felszámolja a támadó jelenlétét, és helyreállítja a hálózat és a rendszerek integritását.

## <a name="101-create-an-incident-response-guide"></a>10.1: Incidenselhárítási útmutató létrehozása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Ügyfél |

Hozzon létre egy incidens-elhárítási útmutatót a szervezet számára. Győződjön meg arról, hogy vannak olyan írásos eseményreagálási tervek, amelyek meghatározzák a személyzet összes szerepkörét, valamint az incidenskezelés/-kezelés fázisait az észleléstől az incidens utáni felülvizsgálatig.

Munkafolyamat-automatizálások konfigurálása az Azure Security Centerben:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Útmutató a saját biztonsági incidensek reválaszolási folyamatának felépítéséhez:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

A Microsoft Security Response Center egy incidens anatómia:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Az Ügyfél a NIST számítógépes biztonsági incidenskezelési útmutatóját is felhasználhatja, hogy segítse saját eseményreagálási tervének létrehozását:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és rangsorolási eljárás létrehozása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 10.2 | 19.8 | Ügyfél |

A Security Center minden riasztáshoz súlyossági szintet rendel, hogy segítsen rangsorolni, mely riasztásokat kell először kivizsgálni. A súlyosság attól függ, hogy a Security Center mennyire magabiztos a keresésben, vagy a riasztás kiadásához használt analitikus, valamint a riasztáshoz vezető tevékenység mögött rosszindulatú szándék megbízhatósági szintje.

Ezenkívül egyértelműen jelölje meg az előfizetéseket (pl. termelés, nem prod), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosítására és kategorizálására.

## <a name="103-test-security-response-procedures"></a>10.3: A tesztelési biztonsági reagálási eljárások

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 10.3 | 19 | Ügyfél |

Végezzen gyakorlatokat, hogy rendszeres enkeresztül tesztelje a rendszerek incidenselhárítási képességeit. Azonosítsa a gyenge pontokat és hiányosságokat, és szükség szerint vizsgálja felül a tervet.

Tekintse meg a NIST kiadványát: Útmutató az informatikai tervekés képességek tesztelési, képzési és edzésprogramjaihoz:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidensek elérhetőségét, és állítsa be a biztonsági incidensek riasztási értesítéseit

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 10.4 | 19.5 | Ügyfél |

A Biztonsági incidensek kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) felfedezi, hogy az ügyfél adataihoz jogellenes vagy jogosulatlan fél fért hozzá.  Tekintse át az incidenseket a problémák megoldásának biztosítása érdekében.

Az Azure Security Center biztonsági kapcsolattartójának beállítása:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidenselhárítási rendszerbe

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 10,5 | 19.6 | Ügyfél |

Exportálja az Azure Security Center-riasztásokat és javaslatokat a Folyamatos exportálás funkcióval. A Folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális exportálását, vagy folyamatos, folyamatos módon. Használhatja az Azure Security Center adatösszekötő a Sentinel riasztások streameléséhez.

A folyamatos exportálás konfigurálása:

https://docs.microsoft.com/azure/security-center/continuous-export

Értesítések streamelése az Azure Sentinelbe:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztásokra adott válasz automatizálása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 10.6 | 19 | Ügyfél |

Az Azure Security Center Munkafolyamat-automatizálási szolgáltatásával &quot;automatikusan elindíthatja a biztonsági riasztásokra és javaslatokra vonatkozó válaszokat a Logic Apps alkalmazásokkal.&quot;

A munkafolyamat-automatizálás és a logikai alkalmazások konfigurálása:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>További lépések

Lásd a következő biztonsági ellenőrzés: [Behatolási tesztek és vörös csapat gyakorlatok](security-control-penetration-tests-red-team-exercises.md)