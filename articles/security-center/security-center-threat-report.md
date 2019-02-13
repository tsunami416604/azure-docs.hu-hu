---
title: Azure Security Center fenyegetésfelderítési jelentés | Microsoft Docs
description: Ebből a dokumentumból megtudhatja hogyan használhatja az Azure Security Center fenyegetésfelderítési jelentést a vizsgálat során, amikor a biztonsági riasztásokkal kapcsolatos információkat keres.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: ba5ab7ce85933545a41f23e2ecd913acbb7e72d1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112733"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Azure Security Center fenyegetésfelderítési jelentés
Ez a dokumentum ismerteti, hogy az Azure Security Center fenyegetésfelderítési jelentés segítségével hogyan tudhat meg többet az olyan fenyegetésekről, amelyek biztonsági riasztást hoztak létre.

## <a name="what-is-a-threat-intelligence-report"></a>Mi a fenyegetésfelderítési jelentés?
A Security Center fenyegetésészlelése úgy működik, hogy figyeli az Azure-erőforrások, a hálózat, és a partnermegoldások biztonsági információit. A fenyegetések azonosításához elemzi ezeket az információkat, és gyakran megvizsgálja a különböző forrásokból származó adatok közötti összefüggéseket. Ez a folyamat része a Security Center [észlelési képességeinek](security-center-detection-capabilities.md).

Ha a Security Center azonosít egy fenyegetést, akkor az [biztonsági riasztást](security-center-managing-and-responding-alerts.md) vált ki, amely részletes információkat tartalmaz az adott eseménnyel kapcsolatban, beleértve az elhárítási javaslatokat is. Segítséget nyújt az incidensmegoldási csapat, vizsgálja meg és az elhárításban, hogy a Security Center fenyegetésfelderítési jelentés észlelt fenyegetésről, beleértve a fenyegetéssel kapcsolatos információkat tartalmazó magában foglalja a:

* A támadó identitása vagy társításai (ha ez az információ elérhető)
* A támadó céljai
* Jelenlegi és korábbi támadássorozatok (ha ez az információ elérhető)
* A támadó taktikája, eszközei és eljárások
* A sérüléssel kapcsolatos mutatók (IoC), például URL-címek és fájlkivonatok
* A viktimológia, azaz az ipari és földrajzi gyakoriság segít annak meghatározásában, hogy az Ön Azure-erőforrásai veszélyeztetettek-e
* Kezelési és elhárítási információk

> [!NOTE]
> Az információ mennyisége minden jelentésben más, a részletesség pedig a kártevő tevékenységén és gyakoriságán alapul.
>
>

A Security Centerben három típusa van a fenyegetési jelentéseknek, amelyek a támadástól függően változnak. Az elérhető jelentések a következők:

* **Tevékenységi csoport jelentés**: mélyreható információkat biztosít a támadókról, a céljaikról és a taktikáikról.
* **Kampányjelentés**: adott támadási kampányok részleteire összpontosít.
* **Fenyegetésösszegző jelentés**: az előző két jelentés összes elemét tartalmazza.

Az ilyen információkat során hasznos a [incidensmegoldási](security-center-incident-response.md) folyamatban, ahol folyamatos vizsgálat forrásának a támadás, a támadó indítékainak megismeréséért, és hogy mit kell tenni a probléma megoldásához a jövőben van .

## <a name="how-to-access-the-threat-intelligence-report"></a>Hogyan értheti el a fenyegetésfelderítési jelentést?
A **Biztonsági riasztások** csempén áttekintheti az aktuális riasztásokat. Nyissa meg az Azure Portalon, és kövesse az egyes riasztásokkal kapcsolatos további részleteket az alábbi lépéseket:

1. A Security Center irányítópultján található a **Biztonsági riasztások** csempe.
2. Kattintson a csempére, hogy megnyissa a **Biztonsági riasztások** panelt, amely további részleteket tartalmaz a riasztásokról, majd kattintson arra a biztonsági riasztásra, amelyről többet szeretne megtudni.

    ![Biztonsági riasztások](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. Ebben az esetben a **gyanús végrehajtott folyamat** panelen jelenik meg a riasztás részletes adatait az alábbi ábrán látható módon:

    ![Biztonsági figyelmeztetés részletei](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. Az egyes biztonsági riasztásokhoz elérthető információ mennyisége a riasztás típusától függően változik. Az a **jelentések** mezőt, egy hivatkozás, a fenyegetésfelderítési jelentést. Kattintson a hivatkozásra, és egy PDF-fájl jelenik meg egy új böngészőablakban.

   ![Tároló kiválasztása](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Innen letöltheti a jelentéshez tartozó PDF-fájlt, ahol további információkat olvashat az észlelt biztonsági problémáról, és a megadott információkat alapul véve intézkedhet.

## <a name="see-also"></a>Lásd még
Ebben a dokumentumban megismerkedhetett azzal, hogyan segítheti Önt az Azure Security Center fenyegetésfelderítési jelentés a biztonsági riasztások vizsgálata során. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security Center használata incidensmegoldásra](security-center-incident-response.md)
* [Az Azure Security Center észlelési képességei](security-center-detection-capabilities.md)
* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md). A tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md). A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Biztonsági incidens kezelése az Azure Security Centerben](security-center-incident.md)
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
