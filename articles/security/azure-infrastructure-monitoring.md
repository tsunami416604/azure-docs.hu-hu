---
title: Azure-infrastruktúra megfigyelése
description: A cikk ismerteti az Azure éles hálózati környezetben a figyelését.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 17e7183ff56725462dc43cba21db418a86d86b51
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102341"
---
# <a name="monitoring-of-azure-infrastructure"></a>Azure-infrastruktúra megfigyelése   

## <a name="configuration-and-change-management"></a>Konfigurációs és a változáskezeléshez
Microsoft Azure ellenőrzi, és frissíti a konfigurációs beállításokat és az eredeti hardveres, szoftveres és hálózati eszközök beállításait évente. Módosítások fejlesztett, tesztelt, és az éles környezetben érkező fejlesztési vagy tesztelési környezetben előtt jóvá.

Az Azure-alapú szolgáltatások számára szükséges alapterv konfigurációkat van felül az Azure biztonsági és megfelelőségi csoport és a csoportokat. A service csapatának felülvizsgálati tesztelése előtt az éles szolgáltatás részét képezi.

## <a name="vulnerability-management"></a>A biztonsági rés kezelése
Biztonsági frissítés management megvédi rendszerek a ismert biztonsági rések. Az Azure által használt integrált telepítési rendszerek kezeléséhez terjesztési és biztonsági frissítések Microsoft-szoftverek telepítését. Azure egyben a Microsoft biztonsági válasz Center (MSRC) erőforrásait igénybe. MSRC azonosítja, figyeli, válaszol, és oldja fel a biztonsági események és a felhő biztonsági rések éjjel, minden év napja.

## <a name="vulnerability-scanning"></a>Biztonsági rések keresése
Kiszolgálói operációs rendszerek, adatbázisok és a hálózati eszközökön történik a biztonsági rések keresése. A biztonsági vizsgálatok legalább negyedévente történik. A Microsoft Azure szerződéseket független értékelő behatolást vagy a biztonság teszteléséhez a Microsoft Azure-határ. Piros-csapat gyakorlatokat is rendszeresen készül, és eredmények használ a biztonsági javítások.

## <a name="protective-monitoring"></a>Védelmi figyelése
A Microsoft Azure biztonsági követelmények aktív figyelés definiálva van. Csoportok konfigurálása aktív Hálózatfigyelő eszközök a követelményeknek megfelelően. Aktív felügyeleti eszközök közé tartozik, a figyelési ügynök (MA) és a System Center Operations Manager. Ezek az eszközök a Microsoft Azure biztonsági csoporthoz azonnali beavatkozást igénylő esetekben idő riasztások megadására vannak konfigurálva.

## <a name="incident-management"></a>Incidenskezelés
Microsoft valósítja meg a biztonsági incidenskezelési folyamat megkönnyítésére koordinált választ adhasson az eseményekre, megtörténik, egyet.

Ha Microsoft tudomást berendezései vagy létesítményekben, vagy ilyen berendezések vagy adatvesztés, nyilvánosságra vagy megváltoztatása ügyféladatok létesítményekben jogosulatlan elérésére tárolt ügyféladatok jogosulatlan hozzáférést, a Microsoft a következő lép műveletek:

- Azonnal értesíti az ügyfél a biztonsági incidens
- Azonnal pedig megvizsgálja a biztonsági incidens, és az ügyfél biztosít a biztonsági incidens kapcsolatos részletes információk
- Végrehajtja által okozott hatások mérsékléséhez és minimalizálása érdekében a biztonsági incidens egy károk ésszerű és azonnali műveleteket.

Az incidenskezelés keretrendszer definiált szerepkörök és felelősségek lefoglalt létrejött. A Windows Azure biztonsági incidens (WASIM) felügyeleti csoport biztonsági incidensek kezelése, beleértve eszkalációs és specialistája csapatok szükség esetén bevonásának biztosítása felelős. Az Azure üzemeltetési vezetők is vizsgálati és a biztonság és adatvédelem az incidensek megoldási felügyeletéért felelős.

## <a name="next-steps"></a>További lépések
Microsoft funkciója az Azure-infrastruktúra védelméhez kapcsolatos további információkért lásd:

- [Az Azure létesítményekben, a helyszíni és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure információk rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [A Microsoft Azure SQL Database biztonsági funkciói](azure-infrastructure-sql.md)
- [Az Azure éles műveletek és kezelése](azure-infrastructure-operations.md)
- [Azure-infrastruktúra integritása](azure-infrastructure-integrity.md)
- [Az Azure-ban felhasználói adatok védelme](azure-protection-of-customer-data.md)
