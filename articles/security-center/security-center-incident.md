---
title: Biztonsági incidensek kezelése az Azure Security Centerben | Microsoft dokumentumok
description: Ez a dokumentum segít az Azure Security Center használatában a biztonsági incidensek kezeléséhez.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415666"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Biztonsági incidensek kezelése az Azure Security Centerben

Triage és vizsgáló biztonsági riasztások időigényes lehet még a legképzettebb biztonsági elemzők, és sok nehéz is tudja, hol kezdjem. A különálló [biztonsági riasztások](security-center-managing-and-responding-alerts.md) adatait összekapcsoló [elemzési szolgáltatások](security-center-detection-capabilities.md) alkalmazásával a Security Center a támadássorozatot és az összes kapcsolódó riasztást egyetlen nézetben jeleníti meg, így gyorsan áttekinthetővé válnak a támadó által végrehajtott műveletek és az érintett erőforrások.

Ez a témakör ismerteti a Security Center ben történt incidenseket, és a riasztások kiújulásának módját.

## <a name="what-is-a-security-incident"></a>Mi az a biztonsági incidens?

A Security Centerben egy biztonsági incidens az adott erőforráshoz tartozó összes olyan riasztás együttese, amelyek egy [támadási folyamatba](alerts-reference.md#intentions) illeszthetők. Az incidensek megjelennek a [Biztonsági riasztások](security-center-managing-and-responding-alerts.md) listában. Kattintson egy incidensre a kapcsolódó riasztások megtekintéséhez, amely lehetővé teszi, hogy további információkat szerezzen az egyes előfordulásokról.

## <a name="managing-security-incidents"></a>Biztonsági incidensek kezelése

1. A Biztonsági központ irányítópultján kattintson a **Biztonsági riasztások csempére.** Az incidensek és riasztások fel vannak sorolva. A biztonsági incidensek leírásának a többi riasztástól eltérő ikonja van.

    ![Biztonsági incidensek megtekintése](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. A részletek megtekintéséhez kattintson egy incidensre. A **biztonsági incidens észlelt** panel további részleteket jelenít meg. Az **Általános információk** szakasz betekintést nyújthat abba, hogy mi váltotta ki a biztonsági riasztást. Olyan információkat jelenít meg, mint a célerőforrás, a forrás IP-címe (ha van), ha a riasztás még aktív, és javaslatokat tesz a javításra.  

    ![Reagálás biztonsági incidensekre az Azure Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Ha további információt szeretne kapni az egyes riasztásokról, kattintson egy riasztásra. A Security Center által javasolt elhárítási műveletek a biztonsági riasztástól függően eltérők lehetnek.

   > [!NOTE]
   > Ugyanaz a riasztás egy incidens részeként is létezhet, valamint önálló riasztásként is látható lehet.

    ![Riasztás részletei](./media/security-center-incident/security-center-incident-alert.png)

1. Kövesse az egyes riasztásokhoz megadott javítási lépéseket.


## <a name="see-also"></a>Lásd még
Ebben a dokumentumban megismerkedhetett a Security Center biztonságiincidens-kezelési képességeinek használatával. A kapcsolódó információkat lásd az alábbi témakörben:

* [Veszélyforrások elleni védelem az Azure Security Centerben](threat-protection.md)
* [Biztonsági riasztások az Azure Security Centerben](security-center-alerts-overview.md)
* [Biztonsági riasztások kezelése](security-center-managing-and-responding-alerts.md)