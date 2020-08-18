---
title: Biztonsági kapcsolattartási adatok megadása a Azure Security Centerban | Microsoft Docs
description: Ebből a dokumentumból megtudhatja, hogyan biztosíthat biztonsági kapcsolattartási adatokat a Azure Security Centerban.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: dda61b81ee2c357ddac29701832fe4780ea06859
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516299"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>E-mail-értesítések beállítása biztonsági riasztásokhoz 

Annak biztosítása érdekében, hogy a szervezetben a megfelelő személyek értesüljenek a biztonsági riasztásokról a környezetben, adja meg az e-mail-címüket az **e-mail értesítések** beállításai lapon.

Az értesítések beállításakor beállíthatja, hogy az e-mailek az adott személyeknek vagy egy adott Azure-szerepkörrel rendelkező felhasználóknak legyenek elküldve egy előfizetéshez. 

A riasztások fáradtságának elkerülése érdekében Security Center korlátozza a kimenő levelek mennyiségét. Az egyes előfizetésekhez Security Center a következőt küldi el:

- legfeljebb **négy** e-mail-cím naponta a **nagy súlyosságú** riasztásokhoz
- a **közepes súlyosságú** riasztások esetében naponta legfeljebb **két** e-mail-üzenet
- legfeljebb **egy** e-mail-cím naponta az **alacsony súlyosságú** riasztásokhoz


:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="A biztonsági riasztásokkal kapcsolatos e-maileket fogadó partner adatainak konfigurálása." :::

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető|
|Árképzési|Ingyenes szint|
|Szükséges szerepkörök és engedélyek:|**Biztonsági rendszergazda**<br>**Előfizetés tulajdonosa** |
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Yes](./media/icons/yes-icon.png) US Gov (részleges)<br>![No](./media/icons/no-icon.png) Kínai gov, egyéb gov|
|||


## <a name="set-up-email-notifications-for-alerts"></a>Értesítő e-mail-értesítések beállítása <a name="email"></a>

E-mailes értesítéseket küldhet magánszemélyeknek vagy az adott Azure-szerepkörökkel rendelkező felhasználóknak is.

1. A Security Center **díjszabása & beállítások** területen válassza a megfelelő előfizetést, majd válassza az **e-mail értesítések**lehetőséget.

1. Adja meg az értesítések címzettjeit:

    - A legördülő listából válassza ki az elérhető szerepkörök közül.
    - És/vagy adja meg a megadott e-mail-címeket vesszővel elválasztva. A megadható e-mail-címek száma nincs korlátozva.

1. A biztonsági kapcsolattartási adatok előfizetésre való alkalmazásához válassza a **Mentés**lehetőséget.


## <a name="see-also"></a>Lásd még
A biztonsági riasztásokkal kapcsolatos további tudnivalókért tekintse meg a következőket:

* [Biztonsági riasztások – útmutató](alerts-reference.md) a Azure Security Center veszélyforrások elleni védelmi moduljában esetlegesen megjelenő biztonsági riasztások megismeréséhez
* [Biztonsági riasztások kezelése és válaszadás a Azure Security Center-ben](security-center-managing-and-responding-alerts.md) – útmutató a biztonsági riasztások kezeléséhez és megválaszolásához
* [Munkafolyamat-automatizálás](workflow-automation.md) – a riasztásokra adott válaszok automatizálása egyéni értesítési logikával