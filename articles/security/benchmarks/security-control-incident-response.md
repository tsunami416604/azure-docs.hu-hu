---
title: Azure Security Control – Incidensre adott válasz
description: Az Azure security control incidensre adott válasza
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 993793d21e6253188dfc199d8701cbe117503517
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408421"
---
# <a name="security-control-incident-response"></a>Biztonsági ellenőrzés: Incidensre adott válasz

A szervezet információinak és hírnevének védelme az incidenselhárítási infrastruktúra (pl. tervek, meghatározott szerepek, képzés, kommunikáció, felügyeleti felügyelet) kifejlesztésével és megvalósításával a támadások gyors felfedezéséhez, majd a károk hatékony megfékezéséhez, a támadó jelenlétének felszámolásához, valamint a hálózat és a rendszerek integritásának helyreállításához.

## <a name="101-create-an-incident-response-guide"></a>10.1: Incidenselhárítási útmutató létrehozása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Ügyfél |

Hozzon létre egy incidens-elhárítási útmutatót a szervezet számára. Győződjön meg arról, hogy vannak olyan írásos eseményreagálási tervek, amelyek meghatározzák a személyzet összes szerepkörét, valamint az incidenskezelés/-kezelés fázisait az észleléstől az incidens utáni felülvizsgálatig.  

- [Útmutató a saját biztonsági incidensek reválaszolási folyamatának felépítéséhez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [A Microsoft Security Response Center egy incidens anatómia](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Használja ki a NIST számítógépes biztonsági incidenskezelési útmutatóját, hogy segítse saját eseményreagálási tervének létrehozását](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és rangsorolási eljárás létrehozása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 10.2 | 19.8 | Ügyfél |

A Security Center minden riasztáshoz súlyossági szintet rendel, hogy segítsen rangsorolni, mely riasztásokat kell először kivizsgálni. A súlyosság attól függ, hogy a Security Center mennyire magabiztos a keresésben, vagy a riasztás kiadásához használt analitikus, valamint a riasztáshoz vezető tevékenység mögött rosszindulatú szándék megbízhatósági szintje. 

Ezenkívül egyértelműen jelölje meg az előfizetéseket (pl. termelés, nem prod) címkék használatával, és hozzon létre egy elnevezési rendszer egyértelműen azonosítani és kategorizálni az Azure-erőforrások, különösen a bizalmas adatok feldolgozása.  Az Ön felelőssége, hogy rangsorolja a riasztások szervizelése alapján az Azure-erőforrások és a környezet, ahol az esemény történt.

- [Biztonsági riasztások az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="103-test-security-response-procedures"></a>10.3: A tesztelési biztonsági reagálási eljárások

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 10.3 | 19 | Ügyfél |

Végezzen gyakorlatokat a rendszerek incidenselhárítási képességeinek rendszeres ütemben való teszteléséhez az Azure-erőforrások védelme érdekében. Azonosítsa a gyenge pontokat és hiányosságokat, és szükség szerint vizsgálja felül a tervet.

- [A NIST kiadványa - Útmutató az informatikai tervek és képességek teszteléséhez, képzéséhez és edzéséhez](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidensek elérhetőségét, és állítsa be a biztonsági incidensek riasztási értesítéseit

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 10.4 | 19.5 | Ügyfél |

A Biztonsági incidensek kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy az Ön adataihoz jogosulatlan vagy jogosulatlan fél fért hozzá. Tekintse át az incidenseket a problémák megoldásának biztosítása érdekében.

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidenselhárítási rendszerbe

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 10,5 | 19.6 | Ügyfél |

Exportálja az Azure Security Center-riasztásokat és javaslatokat a Folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosításához. A Folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális exportálását, vagy folyamatos, folyamatos módon. Használhatja az Azure Security Center adatösszekötőt a riasztások streameléséhez az Azure Sentinel.

- [A folyamatos exportálás konfigurálása](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Értesítések streamelése az Azure Sentinelbe](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztásokra adott válasz automatizálása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 10.6 | 19 | Ügyfél |

Az Azure Security Center Munkafolyamat-automatizálási szolgáltatásával automatikusan elindíthatja a "Logic Apps" szolgáltatáson keresztül a biztonsági riasztásokra és az Azure-erőforrások védelmére vonatkozó javaslatokra adott válaszokat.

- [Munkafolyamat-automatizálás és logikai alkalmazások konfigurálása](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>További lépések

- Lásd a következő biztonsági ellenőrzés: [Penetrációs tesztek és a Vörös Csapat gyakorlatok](security-control-penetration-tests-red-team-exercises.md)