---
title: Az Azure-infrastruktúra figyelése
description: Ez a cikk ismerteti az Azure éles hálózat figyelése.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: af9c157b4644156edc6dcdb1b53c141263576500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727152"
---
# <a name="azure-infrastructure-monitoring"></a>Az Azure-infrastruktúra figyelése   

## <a name="configuration-and-change-management"></a>Konfiguráció és változáskezelés
Az Azure évente áttekinti és frissíti a hardver-, szoftver- és hálózati eszközök konfigurációs beállításait és alapkonfigurációit. A módosításokat a fejlesztési és/vagy vizsgálati környezetből a termelési környezetbe való belépés előtt fejlesztik, tesztelik és hagyják jóvá.

Az Azure-alapú szolgáltatásokhoz szükséges alapkonfigurációkat az Azure biztonsági és megfelelőségi csapata és a szolgáltatási csapatok ellenőrzik. A szolgáltatáscsapat felülvizsgálata része a tesztelés, amely az éles szolgáltatás üzembe helyezése előtt történik.

## <a name="vulnerability-management"></a>A biztonsági rés kezelése
A biztonsági frissítések kezelése segít megvédeni a rendszereket az ismert biztonsági résektől. Az Azure integrált központi telepítési rendszereket használ a Microsoft-szoftverek biztonsági frissítéseinek terjesztésének és telepítésének kezelésére. Az Azure a Microsoft Security Response Center (MSRC) erőforrásaiból is támaszkodhat. Az MSRC az év minden napján éjjel-nappal azonosítja, figyeli, válaszol és megoldja a biztonsági incidenseket és a felhőbeli biztonsági réseket.

## <a name="vulnerability-scanning"></a>Biztonsági rés vizsgálata
A biztonsági rés vizsgálata kiszolgálói operációs rendszereken, adatbázisokon és hálózati eszközökön történik. A biztonsági rés vizsgálatai legalább negyedévente történnek. Az Azure-szerződések független értékelők az Azure-határ behatolási tesztelése. A vöröscsapat-gyakorlatokat is rutinszerűen végzik, és az eredményeket a biztonsági fejlesztésekhez használják.

## <a name="protective-monitoring"></a>Védőellenőrzés
Az Azure security az aktív figyelés követelményeit határozta meg. A szervizcsapatok az aktív figyelési eszközöket ezeknek a követelményeknek megfelelően konfigurálják. Az aktív figyelési eszközök közé tartozik a Microsoft Monitoring Agent (MMA) és a System Center Operations Manager. Ezek az eszközök úgy vannak konfigurálva, hogy azonnali beavatkozást igénylő helyzetekben időriasztásokat biztosítsanak az Azure biztonsági személyzetének.

## <a name="incident-management"></a>Incidenskezelés
A Microsoft egy biztonsági incidenskezelési folyamatot valósít meg, hogy megkönnyítse az incidensekre adott összehangolt választ, ha bekövetkezne.

Ha a Microsoft tudomást szerez a berendezésein vagy létesítményeiben tárolt ügyféladatokhoz való jogosulatlan hozzáférésről, vagy tudomást szerez az ilyen berendezésekhez vagy létesítményekhez való jogosulatlan hozzáférésről, amely az ügyféladatok elvesztését, nyilvánosságra hozatalát vagy módosítását eredményezi, A Microsoft a következő műveleteket követi:

- Azonnal értesíti az ügyfelet a biztonsági incidensről.
- Azonnal kivizsgálja a biztonsági incidenst, és részletes információkat nyújt az ügyfeleknek a biztonsági incidensről.
- Ésszerű és gyors lépéseket tesz a hatások csökkentése és a biztonsági incidensből eredő károk minimalizálása érdekében.

Egy incidenskezelési keretrendszert hoztak létre, amely meghatározza a szerepeket és kiosztja a felelősségeket. Az Azure biztonsági incidensek kezelésére csapat felelős a biztonsági incidensek kezeléséért, beleértve az eszkalációt, és biztosítja a speciális csapatok bevonását, ha szükséges. Az Azure műveleti vezetői felelősek a biztonsági és adatvédelmi incidensek kivizsgálásának és megoldásának felügyeletéért.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni arról, hogy a Microsoft mit tesz az Azure-infrastruktúra védelme érdekében, olvassa el a következő témakört:

- [Az Azure létesítményei, helyiségei és fizikai biztonsága](physical-security.md)
- [Az Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Az Azure információs rendszer összetevői és határai](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure éles hálózat](production-network.md)
- [Az Azure SQL Database biztonsági szolgáltatásai](infrastructure-sql.md)
- [Az Azure éles üzemei és kezelése](infrastructure-operations.md)
- [Az Azure infrastruktúra integritása](infrastructure-integrity.md)
- [Az Azure-beli ügyfelek adatainak védelme](protection-customer-data.md)
