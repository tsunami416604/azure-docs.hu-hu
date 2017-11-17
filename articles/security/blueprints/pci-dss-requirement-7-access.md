---
title: "Az Azure támogatási feldolgozási tervezetének - hozzáférési követelmények"
description: "PCI DSS követelmény 7"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: ac3afee9-0471-465d-a115-67488a1635a6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5a3c9eac552fb96309cfa791a2e72a7102662e60
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="access-requirements-for-pci-dss-compliant-environments"></a>Hozzáférési követelmények PCI DSS-kompatibilis környezetben 
## <a name="pci-dss-requirement-7"></a>PCI DSS követelmény 7

**Kártya tulajdonosát adatok által az üzleti szükséges mértékű ismeretek hozzáférés korlátozása**

> [!NOTE]
> Ezeket a követelményeket határozzák meg a [Payment Card Industry (PCI) biztonsági szabványok Tanács](https://www.pcisecuritystandards.org/pci_security/) részeként a [PCI adatok biztonsági szabvány (DSS) 3.2-es verziójú](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Tekintse meg a PCI DSS tesztelési eljárások és az egyes követelményekhez útmutatót olvashat.

Annak érdekében, hogy kritikus fontosságú adatokat csak arra jogosult személyek által érhető el, rendszerek és folyamatok a szükséges mértékű ismeretek alapján, és megfelelően felelősségi hozzáférés korlátozásához rendelkezése kell állnia.

"Tudniuk kell, hogy" akkor, ha a hozzáférési jogok a csak a lehető legkevesebb adat és a feladat végrehajtásához szükséges jogosultságokkal.

## <a name="pci-dss-requirement-71"></a>PCI DSS követelmény 7.1

**7.1** korlátozza a hozzáférés-összetevőkkel és azok számára, amelynek feladat ilyen hozzáférést igényel kártya tulajdonosát az adatokat.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Azure Azure személyzet hozzáférés az Azure-összetevőkkel, az access control hatékonyságának ellenőrzése tekintetében, csak az idő biztosítása mellett a meglévő ISMS házirendek érvénybe lépteti rendszergazdai hozzáférést, ha már nem szükséges, és biztosítani kell személyzet hozzáférés visszavonása üzleti elérése az Azure platformon környezetben van szükség. Felhasználói környezetben Azure hozzáférést erősen korlátozott, és csak az ügyfél jóváhagyásának engedélyezett.<br /><br />Eljárások az Adatközpont engedéllyel rendelkező alkalmazottak, szállítók, alvállalkozói és látogatók való fizikai hozzáférés korlátozása bevezetett. A belső data center létesítményt a személyes ideiglenes hozzáférést igénylő személyzet biztonsági ellenőrzést, és be szükségesek. Belépési naplók negyedévente felülvizsgálata Azure csoportok. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | Felhasználók korlátozása a hozzáférés-összetevőkkel és kártya tulajdonosát az adatokat csak feladatokkal ilyen hozzáférést igényel a személyek felelősek. Korlátozza és a hozzáférés korlátozása az Azure felügyeleti portálon fiókok vagy szerepkörök megadása a hozhat létre, valamint módosítana vagy törölne PaaS szolgáltatások.|



### <a name="pci-dss-requirement-711"></a>PCI DSS követelmény 7.1.1

**7.1.1** Define hozzáférést kell az egyes szerepkörökhöz, beleértve:
- Összetevők és az adatok rendszererőforrások egyes szerepkörök a feladat függvény hozzáférésre van szüksége.
- Jogosultság szükséges (például felhasználó, rendszergazda, stb.) az erőforrások elérése

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | Ügyfelek felelőssége meghatározása és a felhasználói azonosító jóváhagyási folyamat dokumentálása, legalacsonyabb jogosultságok meghatározása, kártya tulajdonosát elérésének korlátozásával, egyedi azonosítók használata, feladataik elválasztását igénylik biztosítása és visszavonni a hozzáférést, ha már nem szükséges.|



### <a name="pci-dss-requirement-712"></a>PCI DSS követelmény 7.1.2

**7.1.2** elérésének korlátozása a rendszerjogosultságú felhasználói azonosítók a feladatok végrehajtásához szükséges legalacsonyabb jogosultságok.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure elfogadta a megfelelő vállalati és szervezeti biztonsági házirendek, beleértve az információk biztonsági házirend. A házirendek rendelkezik lett jóváhagyott, közzétett és a Windows Azure közli. A Microsoft Azure információk biztonsági házirend szükséges, hogy biztosított legyen-e alapján üzleti indoklásának, az eszköz tulajdonosa engedélyt a Microsoft Azure eszközökhöz való hozzáférést, és "szükség alapú tudás" és "legalacsonyabb jogosultsági" alapelvek alapján. A házirend is címek hozzáférés-kezelés életciklusának beleértve a kiépítés, hozzáférés-engedélyezési, hitelesítési hozzáférési jogok eltávolítása követelményei, és ellenőrzi, hogy időszakos hozzáférés. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló üzembe helyezése során hoz létre három fiók: rendszergazda, sqladmin, és edna (az alapértelmezett felhasználó bejelentkezik a web app, bemutató végrehajtása során). Felhasználói szerepkörök feladataik dokumentált bemutató forgatókönyv alapján korlátozódnak.|



### <a name="pci-dss-requirement-713"></a>PCI DSS követelmény 7.1.3

**7.1.3** egyedi személyzet feladat besorolási és funkció alapján hozzáférés hozzárendelése.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló üzembe helyezése során hoz létre három fiók: rendszergazda, sqladmin, és edna (az alapértelmezett felhasználó bejelentkezik a web app, bemutató végrehajtása során). Felhasználói szerepkörök feladataik dokumentált bemutató forgatókönyv alapján korlátozódnak.|



### <a name="pci-dss-requirement-714"></a>PCI DSS követelmény 7.1.4

**7.1.4** szükséges megadó jogosult felek dokumentált jóváhagyás szükséges jogosultságokkal.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | Felhasználók korlátozása a hozzáférés-összetevőkkel és kártya tulajdonosát az adatokat csak feladatokkal ilyen hozzáférést igényel a személyek felelősek. Korlátozza és a hozzáférés korlátozása az Azure felügyeleti portálon fiókok vagy szerepkörök megadása a hozhat létre, valamint módosítana vagy törölne PaaS szolgáltatások.|



## <a name="pci-dss-requirement-72"></a>7.2 PCI DSS követelmény

**7.2** létesíteni egy hozzáférés-vezérlő rendszer számára, amely korlátozza a hozzáférést egy felhasználó alapján rendszerek összetevők tudniuk kell, hogy, és az "összes Elutasítás" értékre van állítva, kifejezetten engedélyezett.
A hozzáférés-vezérlő rendszerében a következőket kell tartalmazniuk:
- 7.2.1 összes rendszer összetevőit körét.
- 7.2.2 hozzárendelése egyéni felhasználók számára, feladat besorolási és funkció alapján szükséges engedélyekkel.
- 7.2.3 alapértelmezett "megtagadása – minden" beállítás.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló csak a kijelölt felhasználók hozzáférését korlátozni az Azure Active Directory. További információkért lásd: [PCI útmutatást - Identity Management](payment-processing-blueprint.md#identity-management).|



## <a name="pci-dss-requirement-73"></a>7.3 a PCI DSS követelmény

**7.3** győződjön meg arról, hogy a biztonsági házirendek és üzemeltetési eljárások kártya tulajdonosát adatokhoz való hozzáférés korlátozására dokumentálva, valamint használatban van, és ismert, hogy az összes érintett fél.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Szolgáltató<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI &#8209; DSS&nbsp;tervezetének)** | A Contoso webes tároló dokumentációja tartalmaz a használati esetek és egy leírást, aki CHD használja, és hogyan használja fel CHD kapcsolatban.|




