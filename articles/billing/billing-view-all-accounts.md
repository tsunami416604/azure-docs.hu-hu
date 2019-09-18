---
title: Számlázási fiókok megtekintése az Azure Portalon | Microsoft Docs
description: Ismerje meg, hogyan tekintheti meg számlázási fiókjait az Azure Portalon.
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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490219"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Számlázási fiókok megtekintése az Azure Portalon  

A számlázási fiók akkor jön létre, amikor regisztrálja magát az Azure használatára. A számlázási fiókban kezelheti számláit, fizetéseit és nyomon követheti kiadásait. Több számlázási fiókhoz is rendelkezhet hozzáféréssel. Előfordulhat például, hogy az egyéni projektjei kezeléséhez regisztrált az Azure-ba. Emellett rendelkezhet hozzáféréssel a szervezete Nagyvállalati Szerződésén vagy egy Microsoft-ügyfélszerződésen keresztül is. Mindegyik regisztrációhoz egy külön számlázási fiók tartozik.

Az Azure Portal jelenleg a következő típusú számlázási fiókokat támogatja:

- **Microsoft Online Services Program**: A Microsoft Online Services Program számlázási fiókjai akkor jönnek létre, amikor az Azure-webhelyen keresztül regisztrál az Azure-ba. Erre példa az [ingyenes Azure-fiók](https://azure.microsoft.com/offers/ms-azr-0044p/), a [használatalapú fizetést használó fiók](https://azure.microsoft.com/offers/ms-azr-0003p/) és a [Visual Studio-előfizetés](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Nagyvállalati Szerződés**: Nagyvállalati Szerződéshez tartozó számlázási fiók akkor jön létre, amikor a szervezet [Nagyvállalati Szerződést (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) köt az Azure használatára.

- **Microsoft-ügyfélszerződés**: A Microsoft-ügyfélszerződéshez tartozó számlázási fiók akkor jön létre, amikor a szervezet a Microsoft képviselőjével együttműködve Microsoft-ügyfélszerződést köt. Egyes régiókban, ha a felhasználó az Azure-webhelyen regisztrál egy [használatalapú fizetést használó fiókot](https://azure.microsoft.com/offers/ms-azr-0003p/), vagy frissíti [ingyenes Azure-fiókját](https://azure.microsoft.com/offers/ms-azr-0044p/), külön számlázási fiókot kaphat a Microsoft-ügyfélszerződéshez. További információkért tekintse meg [a Microsoft-ügyfélszerződés számlázási fiókjával kapcsolatos első lépéseket](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>A számlázási fiókok hatóköre
A hatókör egy csomópont a számlázási fiókon belül. A felhasználók ezt használva tekinthetik meg és kezelhetik a számlázást. A felhasználók itt kezelik a számlázási adatokat, a fizetéseket, a számlákat, és itt történik az általános fiókkezelés. 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

|Hatókör  |Meghatározás  |
|---------|---------|
|Számlázási fiók     | Egy vagy több Azure-előfizetés önálló tulajdonosát (fiókadminisztrátorát) jelöli. A fiókadminisztrátor jogosult elvégezni bizonyos számlázási feladatokat, például az előfizetések létrehozását, a számlák megtekintését vagy az előfizetések számlázásának módosítását.  |
|Előfizetés     |  Adott Azure-erőforrások csoportja. Ebben a hatókörben történik számlakiállítás. Saját fizetési módokkal rendelkezik, amelyek a számlák kiegyenlítésére szolgálnak.|


### <a name="enterprise-agreement"></a>Nagyvállalati Szerződés

|Hatókör  |Meghatározás  |
|---------|---------|
|Számlázási fiók    | Egy Nagyvállalati Szerződésre való regisztrációt jelöl. Ebben a hatókörben történik számlakiállítás. A struktúráját a bevont részlegek és regisztrált fiókok határozzák meg.  |
|Részleg     |  Regisztrált fiókok opcionális csoportja.      |
|Regisztrációs fiók     |  Egyetlen fióktulajdonost jelöl. Ebben a hatókörben létrejönnek Azure-előfizetések.  |


### <a name="microsoft-customer-agreement"></a>Microsoft-ügyfélszerződés

|Hatókör  |Feladatok  |
|---------|---------|
|Számlázási fiók     |   Egy több Microsoft-terméket és -szolgáltatást magában foglaló ügyfélszerződést jelöl. A struktúráját a számlázási fiókok és a számlázási szakaszok határozzák meg.   |
|Számlázási profil     |  Egy számlát és annak fizetési módjait jelöli. Ebben a hatókörben történik számlakiállítás. Több számlázási szakaszt is magában foglalhat.      |
|Számlázási szakasz     |   Egy költségcsoportot jelöl a számlán belül. Az előfizetések és más vásárlások ehhez a hatókörhöz vannak társítva.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>A számlázás hatókörének módosítása az Azure Portalon


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. A bal oldalon válassza ki a **Minden számlázási hatókör** elemet.

   ![Az összes számlázási hatókört megjelenítő képernyőkép](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   **Ha csak egyetlen hatókörhöz van hozzáférése, nem jelenik meg a **Minden számlázási hatókör** lehetőség.

4. Válasszon ki egy hatókört a részletek megtekintéséhez.



## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [elemezheti költségeit](../cost-management/quick-acm-cost-analysis.md).