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
ms.author: v-mohabe
ms.openlocfilehash: 7ab42fdb03209173c61b70fdf498aa77b39a1c23
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036385"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Azure Security Center fenyegetésfelderítési jelentés
Ez a dokumentum ismerteti, hogy az Azure Security Center fenyegetésfelderítési jelentés segítségével hogyan tudhat meg többet az olyan fenyegetésekről, amelyek biztonsági riasztást hoztak létre.

## <a name="what-is-a-threat-intelligence-report"></a>Mi a fenyegetésfelderítési jelentés?
A Security Center fenyegetésészlelése úgy működik, hogy figyeli az Azure-erőforrások, a hálózat, és a partnermegoldások biztonsági információit. A fenyegetések azonosításához elemzi ezeket az információkat, és gyakran megvizsgálja a különböző forrásokból származó adatok közötti összefüggéseket. További információ: [How Azure Security Center észleli és reagál a fenyegetésekre](security-center-alerts-overview.md#detect-threats).

Ha a Security Center azonosít egy fenyegetést, akkor az [biztonsági riasztást](security-center-managing-and-responding-alerts.md) vált ki, amely részletes információkat tartalmaz az adott eseménnyel kapcsolatban, beleértve az elhárítási javaslatokat is. Az incidensek megválaszolásával foglalkozó csapatok támogatásához, a fenyegetések kivizsgálásához és elhárításához Security Center tartalmaz egy fenyegetési intelligencia-jelentést, amely az észlelt fenyegetéssel kapcsolatos információkat tartalmaz, beleértve a következő információkat:

* A támadó identitása vagy társításai (ha ez az információ elérhető)
* A támadó céljai
* Jelenlegi és korábbi támadássorozatok (ha ez az információ elérhető)
* Támadók taktikája, eszközei és eljárásai
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

Az ilyen típusú információk az incidensek válaszának folyamata során hasznosak, ahol a támadás forrását, a támadó motivációit és a probléma megoldásának megelőzését szolgáló folyamatos vizsgálatot végeznek.

## <a name="how-to-access-the-threat-intelligence-report"></a>Hogyan értheti el a fenyegetésfelderítési jelentést?
A **Biztonsági riasztások** csempén áttekintheti az aktuális riasztásokat. Nyissa meg a Azure Portal, és kövesse az alábbi lépéseket az egyes riasztások részleteinek megtekintéséhez:

1. A Security Center irányítópultján található a **Biztonsági riasztások** csempe.
2. Kattintson a csempére, hogy megnyissa a **Biztonsági riasztások** panelt, amely további részleteket tartalmaz a riasztásokról, majd kattintson arra a biztonsági riasztásra, amelyről többet szeretne megtudni.

    ![Biztonsági riasztások](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. Ebben az esetben a **gyanús folyamat végrehajtotta** a riasztás részleteit az alábbi ábrán látható módon:

    ![Biztonsági figyelmeztetés részletei](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. Az egyes biztonsági riasztásokhoz elérthető információ mennyisége a riasztás típusától függően változik. A **jelentések** mezőben a fenyegetési intelligencia jelentésre mutató hivatkozás található. Kattintson a hivatkozásra, és egy PDF-fájl jelenik meg egy új böngészőablakban.

   ![Tároló kiválasztása](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Innen letöltheti a jelentéshez tartozó PDF-fájlt, ahol további információkat olvashat az észlelt biztonsági problémáról, és a megadott információkat alapul véve intézkedhet.

## <a name="see-also"></a>Lásd még
Ebben a dokumentumban megismerkedhetett azzal, hogyan segítheti Önt az Azure Security Center fenyegetésfelderítési jelentés a biztonsági riasztások vizsgálata során. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md). A tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md). A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Biztonsági incidens kezelése az Azure Security Centerben](security-center-incident.md)
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
