---
title: Nagyvállalati Szerződés a feladatokat a Microsoft ügyfél-szerződés – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan végezheti el az új számlázási fiók a nagyvállalati szerződés feladatokat.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2018
ms.author: banders
ms.openlocfilehash: 9b76fa935263904957c87cd062c84d0607771369
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341381"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>A Microsoft vevői szerződés az Ön számlázási fiókjához a teljes nagyvállalati szerződés feladatok

Ha a szervezet egy Microsoft Ügyfélszerződéséhez, hogy újítsa meg a nagyvállalati szerződésre való beléptetésről van bejelentkezve, a szerződés egy új számlázási fiók jön létre. A számlázás pedig az új fiók eltérően a nagyvállalati szerződéshez vannak rendezve. Ez a cikk bemutatja, hogyan használhatja az új számlázási fiók a nagyvállalati szerződés végrehajtott feladatok végrehajtásához.

## <a name="how-billing-is-organized-in-the-new-account"></a>Hogyan vannak rendezve, a számlázás az új fiók

A következő ábra ismerteti, hogyan vannak rendezve, a számlázás az új számlázási fiók.

![Ea-mca-post-váltás-hierarchia képe](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

| Nagyvállalati szerződés   | Microsoft Ügyfélszerződés    |
|------------------------|--------------------------------------------------------|
| Regisztráció            | A számlázási profilja segítségével kezelheti a számlázási szervezetben, a nagyvállalati szerződésre való beléptetésről hasonló. Vállalati rendszergazdák a számlázási profilja tulajdonosai lesznek. Számlázási profilokkal kapcsolatos további tudnivalókért lásd: [számlázási a profilok megismerése](billing-mca-overview.md#understand-billing-profiles).
| Részleg            | Egy számla szakasz használata a költségeket, a nagyvállalati szerződésre való beléptetésről részlegek hasonló rendszerezéséhez. Részleg számla szakaszok válik, és a részleg rendszergazdái válnak a megfelelő számla szakaszok tulajdonosai. Számla szakaszok kapcsolatos további információkért lásd: [Understand számla szakaszok](billing-mca-overview.md#understand-invoice-sections). |
| Fiók               | A fiókok létrejöttek a nagyvállalati szerződésbe, az új számlázási fiók nem támogatottak. A fiók előfizetés tartozik, azok számára a megfelelő számla szakaszra. Fióktulajdonosok is előfizetéseinek létrehozásához és kezeléséhez a számla szakaszokat. |

## <a name="changes-for-enterprise-administrators-in-the-new-billing-account"></a>A vállalati rendszergazdák az új számlázási fiók módosítása

A következő módosításokat vállalati rendszergazdák nagyvállalati szerződéssel, van egy Microsoft-ügyfél megállapodás megújítani a alkalmazni.

- A regisztráció egy számlázási profil jön létre. A számlázási profilja kezelheti a számlázás a szervezete számára, mint a nagyvállalati szerződésre való beléptetésről fogja használni. További információ a számlázási profilok [számlázási a profilok megismerése](billing-mca-overview.md#understand-billing-profiles).
- Egy számla szakasz mindegyik részlege a nagyvállalati szerződésre való beléptetésről jön létre. A számla szakaszok használni a szervezeti egységek kezelése. Létrehozhat új számla szakaszok további szervezeti egységek beállításához. Számla szakaszok kapcsolatos további információkért lásd: [számla szakaszokban megismerheti](billing-mca-overview.md#understand-invoice-sections).
- A számla szakaszok az Azure-előfizetés létrehozója szerepkör segítségével engedélyezze a más Azure-előfizetéssel, például a nagyvállalati szerződésre való beléptetésről létrehozott fiókok létrehozása.
- Szeretné használni a [az Azure portal](https://portal.azure.com) számlázás a szervezet helyett az Azure nagyvállalati szerződések portálján kezelheti.

A következő szerepkörök kapják meg az új számlázási fiók:

**A számlázás profiltulajdonossal** -jött létre, amikor a szerződés aláírásának számlázási profilon számlázási profil tulajdonosi szerepkör van rendelve. A szerepkör lehetővé teszi a szervezet számára a számlázás kezelését. Díjak és számlák megtekintéséhez, rendszerezheti a költségek, a számlán, fizetési módok kezelése és ki férhet hozzá a szervezet számlái.

**Számla szakasz tulajdonosa** – minden számla szakaszt, amely a nagyvállalati szerződésre való beléptetésről részlegei jön létre a számla szakasz tulajdonosi szerepkör van rendelve. A szerepkör lehetővé teszi, hogy Ön szabályozza, ki is az Azure-előfizetések létrehozása, és vásárolja meg más termékekkel.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>A szervezet díjait és a kreditek egyenleg megtekintése

A számlázási profilja segítségével nyomon követheti a költségek és az Azure-kreditek egyenleg a nagyvállalati szerződésre való beléptetésről hasonlóan a szervezet számára.

A számlázási profilja kreditegyenlegét megtekintése kapcsolatban lásd: [nyomon követheti a számlázási profilja Azure kreditegyenlegét](billing-mca-check-azure-credits-balance.md).

Megtudhatja, hogyan kell fizetni egy számlázási profilja megtekintéséhez, lásd: [a költségek, a Microsoft Ügyfélszerződéséhez számlán ismertetése](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Intézet költségek megtekintése

Az egyes részlegek, a nagyvállalati szerződést kellett egy számla szakasz jön létre. Megtekintheti a számla szakasz díjak az Azure Portalon. További információkért lásd: [tranzakciók számla szakaszonként megjelenítéséhez](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Egy olyan fiók költségek megtekintése

A fiókok, a nagyvállalati szerződésre való beléptetésről létrejöttek az új számlázási fiók nem támogatottak. A fiók előfizetés tartozik, azok számára a megfelelő számla szakaszra. Fióktulajdonosok is előfizetéseinek létrehozásához és kezeléséhez a számla szakaszokat.

Összesített költségét fiókhoz tartoztak előfizetések megtekintéséhez be kell egy erőforrás minden egyes előfizetés esetén. Ezt követően használhatja az Azure használat és költségek csv-fájl szűrése a költséghely szerint az előfizetéseket.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Használat és költségek csv letöltése, árlista és adó dokumentumok

A havi számla jön létre minden egyes számlázási profilja az Ön számlázási fiókjához. Minden számlán a letöltheti az Azure használat és költségek csv-fájl, árlista és adó dokumentum (ha van). Az Azure használat és költségek csv-fájl az aktuális hónap díjakat is letölthető.

Az Azure használat és költségek csv-fájl letöltésére megismerkedni, lásd: [használati adatok letöltése a Microsoft vevői szerződés hatálya alá tartozó](billing-download-azure-daily-usage.md#download-usage-for-your-microsoft-customer-agreement).

Töltse le az árlista ismertetését lásd: [töltse le a Microsoft vevői szerződés díjszabását](billing-ea-pricing.md#view-and-download-pricing-for-your-microsoft-customer-agreement).

Adózási dokumentumok letöltése kapcsolatban lásd: [adó dokumentumok megtekintése a Microsoft vevői szerződés hatálya alá tartozó](billing-mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>A vállalati további rendszergazdák hozzáadása

Felhasználók hozzáférést biztosíthat, hogy azok a számlázási profil megtekintése és kezelése a szervezet számára. Használhatja a **hozzáférés-vezérlés (IAM)** oldal az Azure Portalon való hozzáférést.  Profil szerepkörök számlázással kapcsolatos további információkért lásd: [számlázási profil szerepkörök és feladatok](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Adja meg, a számlázási profilja megismeréséhez, tekintse meg a [kezelése az Azure Portal számlázási szerepkörök](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Hozzon létre egy új szervezeti egység

Hozzon létre egy saját igényei szerint, például a nagyvállalati szerződésre való beléptetésről részlegek költségeit rendszerezéséhez számla szakaszt. Az Azure Portalon létrehozhat számla című új szakasszal. További tudnivalókért lásd: [szakaszok létrehozása a számla a költségek rendszerezéséhez](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Új fiók létrehozása

Felhasználók hozzárendelése az Azure-előfizetés létrehozója szerepkör számla szakaszok adni nekik az Azure-előfizetéssel, például a nagyvállalati szerződésre való beléptetésről létrehozott fiókok létrehozásához szükséges engedéllyel. További információkért lásd: [engedélyezze a más Azure-előfizetések létrehozására](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

## <a name="changes-for-department-administrators-in-the-new-billing-account"></a>A részleg rendszergazdái az új számlázási fiók módosítása

A következő módosításokat részleg rendszergazdái nagyvállalati szerződéssel, van egy Microsoft-ügyfél megállapodás megújítani a alkalmazni.

- Egy számla szakasz mindegyik részlege a nagyvállalati szerződésre való beléptetésről jön létre. A számla szakasz a department(s) kezelésére fogja használni. Számla szakaszok kapcsolatos további információkért lásd: [számla szakaszokban megismerheti](billing-mca-overview.md#understand-invoice-sections).
- A számla szakaszban az Azure-előfizetés létrehozója szerepkör segítségével engedélyezze a más Azure-előfizetéssel, például a nagyvállalati szerződésre való beléptetésről létrehozott fiókok létrehozása.
- Az Azure Portalon való kezelése helyett az Azure nagyvállalati szerződések portálján, a szervezet használni kívánt.

A következő szerepkörök kapják meg az új számlázási fiók:

**Számla szakasz tulajdonosa** -jön létre a nagyvállalati szerződés kellett department(s) számla szakasz a számla szakasz tulajdonosi szerepkör van rendelve. A szerepkör lehetővé teszi megtekintése és költségek nyomon követése és vezérlés ki Azure-előfizetések létrehozására és egyéb termékek, a számla szakaszának vásárolni.

### <a name="view-charges-for-your-department"></a>A részleg költségek megtekintése

Megtekintheti a számla szakaszban létrehozott kell fizetni az osztálynak az Azure Portalon [Költségkezelés + számlázás lapján](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).

### <a name="add-an-additional-department-administrator"></a>A további részleg rendszergazda hozzáadása

Az egyes részlegek, a nagyvállalati szerződést kellett egy számla szakasz jön létre. Használhatja a **hozzáférés Control(IAM)** oldal megtekintése és kezelése a számla szakasz hozzáférés biztosítása más az Azure Portalon. Számla szakasz szerepkörök kapcsolatos további információkért lásd: [szakasz szerepkörök és feladatok számla](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Adja meg, a számla a szakasz a kezelésével kapcsolatos információkért lásd: [kezelése az Azure Portal számlázási szerepkörök](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Hozzon létre egy új fiókot a osztály

A számla a szakasz a részleg számára létrehozott Azure-előfizetés létrehozója szerepkört rendelje hozzá a felhasználókat. További információkért lásd: [engedélyezze a más Azure-előfizetések létrehozására](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

### <a name="view-charges-for-accounts-in-your-departments"></a>A szervezeti fiókokat a költségek megtekintése

A fiókok, a nagyvállalati szerződésre való beléptetésről létrejöttek az új számlázási fiók nem támogatottak. A fiók előfizetés tartozik, azok számára a megfelelő számla szakaszra. Fióktulajdonosok is előfizetéseinek létrehozásához és kezeléséhez a számla szakaszokat.

Összesített költségét a részleg egy fiókhoz tartoztak előfizetések megtekintéséhez be kell egy erőforrás minden egyes előfizetés esetén. Ezt követően használhatja azt, hogy a használat és költségek az Azure file szűrése a költséghely szerint az előfizetéseket.

## <a name="changes-for-account-owners-in-the-new-billing-account"></a>A fióktulajdonosok az új számlázási fiók módosítása

A nagyvállalati szerződés a fióktulajdonosok kérjen engedélyt az új számlázási fiók az Azure-előfizetések létrehozására. A meglévő Azure-előfizetések tartoznak a számla a szakasz a részleg számára létrehozott. Ha fiókja egy szervezeti egység nem tartozik, az előfizetés tartozik egy alapértelmezett számla szakasz nevű számla szakaszban.

További Azure-előfizetések létrehozása, a következő szerepkör kapják meg az új számlázási fiók.

**Azure-előfizetés létrehozója** -a a számla szakaszban létrehozott azure-előfizetés létrehozója szerepkört a nagyvállalati szerződés osztály vannak hozzárendelve. Ha a fiókja nem tartozik egy részleg, Azure-előfizetés létrehozója szerepkör kap egy alapértelmezett számla szakasz nevű szakaszban. A szerepkör lehetővé teszi a számla szakaszának Azure-előfizetések létrehozására.

### <a name="create-an-azure-subscription"></a>Azure-előfizetés létrehozása

A számla című rész ismerteti az Azure Portalon az Azure-előfizetéseket hozhat létre. További információkért lásd: [további Azure-előfizetés létrehozása a Microsoft ügyfél-szerződés](billing-mca-create-subscription.md)

### <a name="view-charges-for-your-account"></a>A fiók költségek megtekintése

Egy fiókhoz tartoztak-szolgáltatások díjai megtekintéséhez nyissa meg a [előfizetések lapon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) az Azure Portalon. Az előfizetések lap az összes előfizetéshez tartozó díjakat jeleníti meg.

### <a name="view-charges-for-a-subscription"></a>Egy előfizetéshez tartozó költségek megtekintése

Megtekintheti egy előfizetési díjai vagy a a [előfizetések lapon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) vagy az Azure-elemzés. További információ az Azure cost analysis: [vizsgálata és elemzése a költségeket a költségelemzés](../cost-management/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, kell [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- [A Microsoft vevői szerződés számlázási fiók ismertetése](billing-mca-overview.md)
- [A számla ismertetése](billing-understand-your-bill.md)
- [Számlázással kapcsolatos tudnivalók](billing-understand-your-invoice.md)
- [Első számlázási tulajdonosi jogokat, más felhasználók az Azure-előfizetések](billing-mca-request-billing-ownership.md)
