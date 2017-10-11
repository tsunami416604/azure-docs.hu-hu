---
title: "Biztonsági riasztások kezelése az Azure Security Centerben | Microsoft Docs"
description: "Ebből a dokumentumból elsajátíthatja az Azure Security Center a biztonsági incidensek kezeléséhez szükséges képességeinek alkalmazását."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: e8feb669-8f30-49eb-ba38-046edf3f9656
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: yurid
ms.openlocfilehash: a302f8cb2555eef469a24da2523fdd9b97cc5730
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="handling-security-incidents-in-azure-security-center"></a>Biztonsági incidensek kezelése az Azure Security Centerben
A biztonsági riasztások osztályba sorolása és kivizsgálása még a legképzettebb biztonsági elemzők számára is időigényes feladat lehet, sokak számára pedig már annak megtalálása is nehézséget okoz, hogy hol kezdjenek hozzá. A különálló [biztonsági riasztások](security-center-managing-and-responding-alerts.md) adatait összekapcsoló [elemzési szolgáltatások](security-center-detection-capabilities.md) alkalmazásával a Security Center a támadássorozatot és az összes kapcsolódó riasztást egyetlen nézetben jeleníti meg, így gyorsan áttekinthetővé válnak a támadó által végrehajtott műveletek és az érintett erőforrások.

Ebben a dokumentumban megismerkedhet a Security Center biztonságiriasztás-kezelési funkcióinak használatával.

## <a name="what-is-a-security-incident"></a>Mi az a biztonsági incidens?
A Security Centerben egy biztonsági incidens az adott erőforráshoz tartozó összes olyan riasztás együttese, amelyek egy [támadási folyamatba](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) illeszthetők. Ezek az incidensek megjelennek a [Security Alerts](security-center-managing-and-responding-alerts.md) (Biztonsági riasztások) csempén és panelen is. Az incidensek megnyitásakor megjelenik a kapcsolódó riasztások listája, amelyből további információkat kaphat az egyes riasztásokról.

## <a name="managing-security-incidents"></a>Biztonsági incidensek kezelése
A Biztonsági riasztások csempén áttekintheti az aktuális biztonsági incidenseket. Nyissa meg az Azure Portalt, és kövesse az alábbi lépéseket az egyes biztonsági incidensek részleteinek megtekintéséhez:

1. A Security Center irányítópultján található a **Biztonsági riasztások** csempe.

    ![Biztonsági riasztások csempe a Security Centerben](./media/security-center-incident/security-center-incident-fig1.png)

2. A csempére kattintva bontsa ki azt, ha pedig a rendszer biztonsági incidenst észlel, az a biztonsági riasztások diagram alatt jelenik meg, ahogyan az az alábbi ábrán látható:

    ![Biztonsági incidens](./media/security-center-incident/security-center-incident-fig2.png)

3. A biztonsági incidensek leírásának a többi riasztástól eltérő ikonja van. Kattintson erre az ikonra az incidens részleteinek megtekintéséhez.

    ![Biztonsági incidens](./media/security-center-incident/security-center-incident-fig3.png)

4. Az **incidens** panelen további részleteket tekinthet meg az adott biztonsági incidensről, többek között annak teljes leírását, súlyosságát (ez esetben magas), aktuális állapotát (ez esetben továbbra is *aktív*, ami azt jelenti, hogy a felhasználó még nem hajtott végre rajta műveletet – ezt úgy teheti meg, ha a jobb gombbal a **Biztonsági riasztások** panelen látható incidensre kattint), a megtámadott erőforrást (amely ez esetben a *VM1*), az incidens elhárításához szükséges lépéseket, az alsó panelen pedig az incidens során előfordult riasztásokat. Ha további információkat kíván megtudni az egyes riasztásokról, egyszerűen kattintson rájuk. Ekkor egy újabb panel nyílik meg, ahogy az az alábbi képen is látható:

    ![Biztonsági incidens](./media/security-center-incident/security-center-incident-fig4.png)

A panelen megjelenő információk a riasztástól függően változnak. Ezen riasztások kezelésével kapcsolatos további információkért olvassa el a [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) című cikket. Ezzel a képességgel kapcsolatos fontos szempontok:

* Egy új szűrő alkalmazásával testre szabhatja a nézetet csak incidensek, csak riasztások vagy mindkettő megjelenítésére.
* Egy adott riasztás kezelhető egy incidens (ha van) részeként, valamint önálló riasztásként is.

## <a name="see-also"></a>Lásd még:
Ebben a dokumentumban megismerkedhetett a Security Center biztonságiincidens-kezelési képességeinek használatával. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)
* [Az Azure Security Center észlelési funkciói](security-center-detection-capabilities.md)
* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
