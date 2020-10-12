---
title: E-mail-értesítések konfigurálása Azure Security Center riasztásokhoz
description: Ismerje meg, hogyan végezheti el a biztonsági riasztások Azure Security Center által küldött e-mailek típusának finomhangolását.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: memildin
ms.openlocfilehash: ac50b17826f9f3d06d01d3cce9704094fddf1b1d
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945617"
---
# <a name="configure-email-notifications-for-security-alerts"></a>E-mail-értesítések konfigurálása biztonsági riasztásokhoz 

A biztonsági riasztásoknak el kell érniük a szervezete megfelelő személyeit. Alapértelmezés szerint Security Center e-mail-előfizetések tulajdonosai, amikor magas súlyosságú riasztást váltanak ki az előfizetéséhez. Ezen az oldalon megtudhatja, hogyan szabhatja testre ezeket az értesítéseket.

Az értesítő e-mailek saját beállításainak megadásához Azure Security Center **e-mail-értesítéseinek** beállításai oldalon a következő lehetőségek közül választhat:

- ** *kinek* kell értesítést** küldeni – az e-maileket elküldheti az egyéni felhasználóknak vagy egy adott Azure-szerepkörrel rendelkező felhasználók számára az előfizetéshez. 
- ** *mit* kell** kapniuk a szolgáltatásról – módosítsa azokat a súlyossági szinteket, amelyekre Security Center küldi az értesítéseket.

A riasztások fáradtságának elkerülése érdekében Security Center korlátozza a kimenő levelek mennyiségét. Az egyes előfizetésekhez Security Center a következőt küldi el:

- legfeljebb **négy** e-mail-cím naponta a **nagy súlyosságú** riasztásokhoz
- a **közepes súlyosságú** riasztások esetében naponta legfeljebb **két** e-mail-üzenet
- legfeljebb **egy** e-mail-cím naponta az **alacsony súlyosságú** riasztásokhoz

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="A biztonsági riasztásokkal kapcsolatos e-maileket fogadó partner adatainak konfigurálása." :::
 
## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|Ingyenes|
|Szükséges szerepkörök és engedélyek:|**Biztonsági rendszergazda**<br>**Előfizetés tulajdonosa** |
|Felhők|![Igen](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Igen](./media/icons/yes-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||


## <a name="customize-the-security-alerts-email-notifications"></a>A biztonsági riasztások e-mail-értesítéseinek testreszabása<a name="email"></a>

E-mailes értesítéseket küldhet magánszemélyeknek vagy az adott Azure-szerepkörökkel rendelkező felhasználóknak is.

1. A Security Center **díjszabása & beállítások** területen válassza ki a megfelelő előfizetést, és válassza az **e-mail értesítések**lehetőséget.

1. Adja meg az értesítések címzettjeit az alábbi lehetőségek egyikével vagy mindkettővel:

    - A legördülő listából válassza ki az elérhető szerepkörök közül.
    - Adja meg a megadott e-mail-címeket vesszővel elválasztva. A megadható e-mail-címek száma nincs korlátozva.

1. A biztonsági kapcsolattartási adatok előfizetésre való alkalmazásához válassza a **Mentés**lehetőséget.


## <a name="see-also"></a>Lásd még
A biztonsági riasztásokkal kapcsolatos további tudnivalókért tekintse meg a következő lapokat:

- [Biztonsági riasztások – útmutató](alerts-reference.md)a Azure Security Center veszélyforrások elleni védelmi moduljában esetlegesen megjelenő biztonsági riasztások megismeréséhez
- [Biztonsági riasztások kezelése és válaszadás a Azure Security Center-ben](security-center-managing-and-responding-alerts.md)– útmutató a biztonsági riasztások kezeléséhez és megválaszolásához
- [Munkafolyamat-automatizálás](workflow-automation.md)– a riasztásokra adott válaszok automatizálása egyéni értesítési logikával