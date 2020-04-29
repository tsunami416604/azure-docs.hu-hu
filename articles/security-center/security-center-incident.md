---
title: Biztonsági incidensek kezelése a Azure Security Centerban | Microsoft Docs
description: Ez a dokumentum segít a biztonsági incidensek kezeléséhez Azure Security Center használatával.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415666"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Biztonsági incidensek kezelése Azure Security Center

A biztonsági riasztások osztályozása és kivizsgálása időigényes lehet a legképzettebb biztonsági elemzők számára is, és sokak számára nehéz megismerni, hogy hol kell kezdeni. A különálló [biztonsági riasztások](security-center-managing-and-responding-alerts.md) adatait összekapcsoló [elemzési szolgáltatások](security-center-detection-capabilities.md) alkalmazásával a Security Center a támadássorozatot és az összes kapcsolódó riasztást egyetlen nézetben jeleníti meg, így gyorsan áttekinthetővé válnak a támadó által végrehajtott műveletek és az érintett erőforrások.

Ez a témakör a Security Center incidenseit, valamint a riasztások szervizelését ismerteti.

## <a name="what-is-a-security-incident"></a>Mi az a biztonsági incidens?

A Security Centerben egy biztonsági incidens az adott erőforráshoz tartozó összes olyan riasztás együttese, amelyek egy [támadási folyamatba](alerts-reference.md#intentions) illeszthetők. Az incidensek megjelennek a [biztonsági riasztások](security-center-managing-and-responding-alerts.md) listájában. A kapcsolódó riasztások megtekintéséhez kattintson egy incidensre, amely lehetővé teszi, hogy az egyes előfordulásokról további információt kapjon.

## <a name="managing-security-incidents"></a>Biztonsági incidensek kezelése

1. A Security Center irányítópulton kattintson a **biztonsági riasztások** csempére. Az incidensek és a riasztások szerepelnek a felsorolásban. A biztonsági incidensek leírásának a többi riasztástól eltérő ikonja van.

    ![Biztonsági incidensek megtekintése](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. A részletek megtekintéséhez kattintson egy incidensre. A **biztonsági incidens észlelve** panel további részleteket jelenít meg. Az **általános információ** szakasz betekintést nyújt a biztonsági riasztás kiváltott részébe. Olyan információkat jelenít meg, mint például a cél erőforrás, a forrás IP-címe (ha van ilyen), ha a riasztás még aktív, és javaslatokat tesz a megoldására.  

    ![Válaszadás a Azure Security Center biztonsági incidensekre](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Ha további információt szeretne kapni az egyes riasztásokról, kattintson a riasztásra. A Security Center által javasolt elhárítási műveletek a biztonsági riasztástól függően eltérők lehetnek.

   > [!NOTE]
   > Ugyanez a riasztás egy incidens részeként is létezhet, valamint önálló riasztásként is látható.

    ![Riasztás részletei](./media/security-center-incident/security-center-incident-alert.png)

1. Kövesse az egyes riasztásokhoz megadott szervizelési lépéseket.


## <a name="see-also"></a>Lásd még
Ebben a dokumentumban megismerkedhetett a Security Center biztonságiincidens-kezelési képességeinek használatával. A kapcsolódó információkat a következő témakörben tekintheti meg:

* [Fenyegetésvédelem az Azure Security Centerben](threat-protection.md)
* [Biztonsági riasztások az Azure Security Centerben](security-center-alerts-overview.md)
* [Biztonsági riasztások kezelése](security-center-managing-and-responding-alerts.md)