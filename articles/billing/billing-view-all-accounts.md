---
title: A számlázási fiókok megtekintése az Azure Portalon |} A Microsoft Docs
description: Ismerje meg a számlázási fiókok megtekintése az Azure Portalon.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: 36430e9b0a4554761d53b537d3c32fa57068eabb
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490219"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Az Azure portal számlázási fiókok megtekintése  

Feliratkozás használható az Azure számlázási fiók jön létre. A számlázási fiók használata kezelheti a számlákon, a fizetés, és nyomon követni a költségeket. Több számlázási fiók rendelkezhet. Például előfordulhat, hogy feliratkozott az Azure-hoz a személyes projektekhez. Hozzáférés a szervezet nagyvállalati szerződéssel vagy Microsoft Ügyfélszerződéséhez keresztül is lehet. Az egyes forgatókönyvek esetében, a számlázási fiókra kell.

Az Azure portal jelenleg a következő típusú számlázási fiókok használatát támogatja:

- **A Microsoft Online Services Program**: Egy számlázási fiók a Microsoft Online Services Program jön létre, amikor regisztrál az Azure-hoz az Azure-webhelyen keresztül. Például, ha bejelentkezik az egy [ingyenes Azure-fiók](https://azure.microsoft.com/offers/ms-azr-0044p/), [fiók az utólagos elszámolású díjszabás szerint](https://azure.microsoft.com/offers/ms-azr-0003p/) vagy mint egy [Visual studio-előfizetők](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Nagyvállalati szerződés**: A nagyvállalati szerződés számlázási fiók jön létre, amikor egy szervezet előfizet egy [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) Azure használatához.

- **A Microsoft Ügyfélszerződéséhez**: Ha a szervezet együttműködik a Microsoft képviselője a Microsoft vevői szerződést kötnek, egy számlázási fiók a Microsoft vevői szerződés jön létre. Néhány keresztül az Azure-webhelyen a regisztráló ügyfelek minden régióban, egy [fiók az utólagos elszámolású díjszabás szerint](https://azure.microsoft.com/offers/ms-azr-0003p/) , vagy frissítse a [ingyenes Azure-fiók](https://azure.microsoft.com/offers/ms-azr-0044p/) egy Microsoft Customer előfordulhat, hogy rendelkezik számlázási fiók Szerződés is. További információkért lásd: [Microsoft Ügyfélszerződéséhez Ismerkedés az Ön számlázási fiókjához](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>A számlázási fiókok hatókörök
A hatókör egy csomópontot egy számlázási fiókot használó felhasználók megtekintése és kezelése a számlázási belül. Ahol a felhasználók kezelése a számlázási adatokat, kifizetések, számlák, és viselkedési szabályzattal az általános felhasználóifiók-kezelés. 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

|Scope  |Meghatározás  |
|---------|---------|
|Számlázási fiók     | Egy vagy több Azure-előfizetések egyetlen tulajdonosnak (fiókadminisztrátori) jelöli. Egy fiók rendszergazdája, előfizetések létrehozása, számlák megtekintése vagy módosítása a számlázás az előfizetések különböző számlázási feladatok végrehajtására jogosult.  |
|Előfizetés     |  Azure-erőforrások egy csoportját jelöli. Számla jön létre a hatókörben. A saját a számlás fizetési használt fizetési módok rendelkezik.|


### <a name="enterprise-agreement"></a>Nagyvállalati Szerződés

|Scope  |Meghatározás  |
|---------|---------|
|Számlázási fiók    | A nagyvállalati szerződésre való beléptetésről jelöli. Számla jön létre a hatókörben. Van felépítve, részlegek és regisztrációs fiókok használatával.  |
|Részleg     |  Regisztrációs fiókok csoportja nem kötelező.      |
|Regisztrációs fiók     |  Egy egyetlen fiók tulajdonosa jelöli. Az Azure-előfizetések a hatókör alatt jönnek létre.  |


### <a name="microsoft-customer-agreement"></a>Microsoft Ügyfélszerződés

|Scope  |Feladatok  |
|---------|---------|
|Számlázási fiók     |   Egy több Microsoft-termékek és szolgáltatások ügyfélszerződéséhez jelöli. Számlázási profilok és a számla szakaszok felépítése.   |
|Számlázási profilja     |  Számla és a hozzá tartozó fizetési módok jelöli. Számla jön létre a hatókörben. Lehet, hogy a szakaszokat számla.      |
|Számla szakasz     |   Költségek a számla egy csoportját jelöli. Előfizetések és az egyéb vásárlási hatókör társítva.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Váltás az Azure Portal számlázási hatókör


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg **Cost Management és számlázás**.

   ![Az Azure portál keresési bemutató képernyőkép](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Válassza ki **számlázási mindegyikének** bal oldalán.

   ![Minden elszámolási hatókör bemutató képernyőkép](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** Nem jelenik meg **számlázási mindegyikének** Ha csak rendelkezik hozzáféréssel egy hatókört.

4. Válassza ki a hatókör részleteinek megtekintéséhez.



## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [a költségek elemzésével](../cost-management/quick-acm-cost-analysis.md).