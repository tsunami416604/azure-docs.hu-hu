---
title: Azure-infrastruktúra figyelése
description: Ez a cikk ismerteti az Azure éles hálózati környezetben figyelését.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 88bc76116392140c533f67402642c68d714227c0
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108212"
---
# <a name="azure-infrastructure-monitoring"></a>Azure-infrastruktúra figyelése   

## <a name="configuration-and-change-management"></a>Konfiguráció és a változáskezeléshez
Az Azure ellenőrzi, és frissíti a konfigurációs beállításokat és a hardveres, szoftveres és hálózati eszközök alapkonfigurációkhoz évente. Módosítások vannak fejlesztett, tesztelését és jóváhagyott fejlesztési és/vagy tesztelési környezetből éles környezetben megadása előtt.

Az Azure-alapú szolgáltatások számára szükséges alapkonfigurációkhoz nyilvánosan lektorálhatók csoportokat és az Azure biztonsági és megfelelőségi csapata által. A szolgáltatások csapat vizsgálata részét képezi a tesztelés, amely a termelési service telepítése előtt következik be.

## <a name="vulnerability-management"></a>Biztonsági rések kezelése
Biztonsági frissítés management megvédi rendszereket az ismert biztonsági kockázatokkal. Azure az integrált központi telepítési rendszerek kezelése a terjesztési és a Microsoft-szoftverek biztonsági frissítések telepítését. Az Azure is el tudja forrásaiból a Microsoft Security Response Center (MSRC). A MSRC azonosítja, figyeli, válaszol, és oldja fel a biztonsági incidensek és a felhő biztonsági rések éjjel, az év minden napján.

## <a name="vulnerability-scanning"></a>Biztonsági rés vizsgálata
Biztonsági rések keresése a kiszolgálói operációs rendszerek, adatbázisok és a hálózati eszközök történik. A vizsgálatot a biztonsági rések legalább negyedévente történik. Az Azure szerződéseket független értékelők behatolásvizsgálat, az Azure határain végrehajtásához. Red team gyakorlatokban is rendszeresen történik, és az eredményeket, hogy a biztonsági fejlesztésekkel szolgálnak.

## <a name="protective-monitoring"></a>Védelmi figyelése
Az Azure security definiálva van aktív monitorozására vonatkozó követelmények. Szolgáltatás csapatok aktív figyelési eszközök ezeknek a követelményeknek megfelelően konfigurálja. Aktív figyelési eszközök közé tartozik, a Microsoft Monitoring Agent (MMA) és a System Center Operations Manager. Ezek az eszközök értesítések nyújtson az azonnali intézkedést igénylő esetekben az Azure biztonsági csoporthoz vannak konfigurálva.

## <a name="incident-management"></a>incidenskezelés
A Microsoft valósít meg egy biztonsági incidens folyamat megkönnyítése érdekében koordinált választ adhasson az eseményekre, csak az egyik fordulhat elő.

Ha a Microsoft tudomást az eszközökön, vagy a létesítményekben tárolt vásárlói adatokhoz való jogosulatlan hozzáféréssel, vagy ilyen berendezések vagy az eszközök elvesztése, közzétételi vagy az megváltoztatására ügyféladatok, ami a jogosulatlan hozzáférést tudomást A Microsoft a következő műveleteket hajtja végre:

- Azonnal értesíti az ügyfél a biztonsági incidens.
- Azonnal folytat a biztonsági incidensek és ügyfelek részletes információit a biztonsági incidensek.
- Ésszerű és azonnali lépéseket által okozott hatások mérsékléséhez és a biztonsági incidensek bármely károk minimalizálása vesz igénybe.

Az incidenskezelés keretrendszer létrejött, amely határozza meg a szerepkörök és feladatkörök foglalja le. Az Azure biztonsági incidens csapat felelős biztonsági incidensek kezelése, ideértve a eszkalációs, és szükség esetén szakértő csapatok bevonása biztosítása. Az Azure üzemeltetési vezetők a vizsgálati és a biztonság és adatvédelem problémamegoldást felügyeletéért felelős.

## <a name="next-steps"></a>További lépések
A Microsoft használ az Azure-infrastruktúra secure kapcsolatos további információkért lásd:

- [Azure létesítményekben, a helyi és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure information rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [Az Azure SQL Database biztonsági funkciók](azure-infrastructure-sql.md)
- [Azure éles környezetben való üzemeltetés és a felügyelet](azure-infrastructure-operations.md)
- [Az Azure infrastruktúra-integritás](azure-infrastructure-integrity.md)
- [Az Azure vásárlói adatok védelmére](azure-protection-of-customer-data.md)
