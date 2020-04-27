---
title: Azure-infrastruktúra figyelése
description: Ez a cikk az Azure éles hálózatának monitorozását ismerteti.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "68727152"
---
# <a name="azure-infrastructure-monitoring"></a>Azure-infrastruktúra figyelése   

## <a name="configuration-and-change-management"></a>Konfigurálás és módosítás kezelése
Az Azure évente megtekinti és frissíti a hardver-, szoftver-és hálózati eszközök konfigurációs beállításait és alapkonfigurációját. A változások fejlesztése, tesztelése és jóváhagyása szükséges a fejlesztési és/vagy tesztelési környezetből származó éles környezetbe való belépés előtt.

Az Azure-alapú szolgáltatásokhoz szükséges alapkonfigurációkat az Azure biztonsági és megfelelőségi csapata, valamint a szolgáltatási csapatok vizsgálják felül. A Service Team-áttekintés a termelési szolgáltatás üzembe helyezése előtt megjelenő tesztelés részét képezi.

## <a name="vulnerability-management"></a>Sebezhetőségek kezelése
A biztonsági frissítés kezelése segít a rendszerek ismert biztonsági rések elleni védelmében. Az Azure integrált központi telepítési rendszereket használ a Microsoft szoftverhez készült biztonsági frissítések terjesztésének és telepítésének kezeléséhez. Az Azure képes a Microsoft Security Response Center (MSRC) erőforrásaira is felhívni. Az MSRC azonosítja, figyeli, válaszol, és a biztonsági incidenseket és a Felhőbeli biztonsági réseket az év minden napján megoldotta.

## <a name="vulnerability-scanning"></a>Sebezhetőségi vizsgálat
A biztonsági rések vizsgálata kiszolgálói operációs rendszereken, adatbázisokon és hálózati eszközökön történik. A biztonsági rések vizsgálatait negyedévente, minimális szinten hajtjuk végre. Azure-szerződések független értékelők használatával az Azure-határ behatolásának teszteléséhez. A Red-Team gyakorlatokat is rendszeresen hajtják végre, és a rendszer az eredményeket használja a biztonsági funkciók növelésére.

## <a name="protective-monitoring"></a>Védelmi monitorozás
Az Azure Security meghatározott követelményeket támaszt az aktív figyeléshez. A szolgáltatási csapatok az aktív figyelési eszközöket a követelményekkel összhangban konfigurálja. Az aktív figyelési eszközök közé tartozik a Microsoft monitoring Agent (MMA) és a System Center Operations Manager. Ezek az eszközök úgy vannak konfigurálva, hogy azonnali beavatkozást igénylő helyzetekben biztosítsák az Azure biztonsági személyzetének időbeli riasztásait.

## <a name="incident-management"></a>Incidenskezelés
A Microsoft egy biztonsági incidensek kezelési folyamatát valósítja meg, amely lehetővé teszi az incidensek koordinált reagálását.

Ha a Microsoft tudomásul veszi a berendezésén vagy létesítményein tárolt ügyféladatok jogosulatlan elérését, vagy az ilyen berendezésekhez vagy létesítményekhez való jogosulatlan hozzáférést, így az adatok elvesztését, közzétételét vagy megváltoztatását eredményezi, a Microsoft a következő műveleteket végzi el:

- Azonnal értesíti a biztonsági incidens ügyfelét.
- Azonnal megvizsgálja a biztonsági incidenst, és részletes információkat nyújt az ügyfeleknek a biztonsági incidensről.
- Ésszerű és gyors lépéseket tesz a hatások enyhítése és a biztonsági incidensek által okozott károk csökkentése érdekében.

A rendszer létrehoz egy incidens-felügyeleti keretrendszert, amely meghatározza a szerepköröket, és hozzárendeli a feladatokat. Az Azure Security incidensek felügyeleti csapata felelős a biztonsági incidensek kezeléséért, beleértve a eszkalációt, és szükség esetén gondoskodik a szakosodott csapatok bevonásáról. Az Azure Operations managerek feladata a biztonsági és adatvédelmi incidensek vizsgálatának és megoldásának felügyelete.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni arról, hogy a Microsoft Hogyan védi az Azure-infrastruktúrát, olvassa el a következő témakört:

- [Azure-létesítmények,-telephelyek és fizikai biztonság](physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Azure Information System-összetevők és-határok](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure-beli üzemi hálózat](production-network.md)
- [Azure SQL Database biztonsági funkciók](infrastructure-sql.md)
- [Azure-beli üzemi műveletek és felügyelet](infrastructure-operations.md)
- [Azure-infrastruktúra integritása](infrastructure-integrity.md)
- [Azure Customer-adatvédelem](protection-customer-data.md)
