---
title: Nagyvállalati szerződéses feladatok elvégzése a Microsoft-ügyfélszerződésben – Azure
description: Megtudhatja, hogyan végezheti el a nagyvállalati szerződéses feladatokat az új számlázási fiókjában.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2019
ms.author: banders
ms.openlocfilehash: 87fe00066989a0b037fc093c8ddf181b8453d5d9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849870"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Nagyvállalati szerződéses feladatok elvégzése a Microsoft-ügyfélszerződés számlázási fiókjában

Ha a szervezete aláírta a Microsoft-ügyfélszerződést a nagyvállalati szerződéses regisztráció megújítása érdekében, akkor létrejön egy új számlázási fiók a szerződéshez. Az új fiók számlázási rendszere eltér a Nagyvállalati Szerződésétől. Ez a cikk azt ismerteti, hogyan hajthatja végre a Nagyvállalati Szerződésben végrehajtott feladatokat az új számlázási fiók használatával.

## <a name="billing-organization-in-the-new-account"></a>Az új fiók számlázási rendszere

Az alábbi ábra az új számlázási fiók számlázási rendszerét mutatja be.

![Az ea-mca-post-transition-hierarchy képe](./media/mca-setup-account/mca-post-transition-hierarchy.png)

| Nagyvállalati Szerződés   | Microsoft-ügyfélszerződés    |
|------------------------|--------------------------------------------------------|
| Regisztráció            | A számlázási profillal a nagyvállalati szerződéses regisztrációhoz hasonlóan kezelheti a szervezet számlázását. A vállalati rendszergazdák a számlázási profil tulajdonosai lesznek. A számlázási profilokkal kapcsolatos további információkért lásd [a számlázási profilok ismertetését](billing-mca-overview.md#billing-profiles).
| Részleg            | A költségek rendszerezését a nagyvállalati szerződéses regisztráció részlegeihez hasonlóan a számlázási szakaszokkal teheti meg. A részlegekből számlázási szakaszok lesznek, a részlegszintű rendszergazdákból pedig a megfelelő számlázási szakaszok tulajdonosai. A számlázási szakaszokkal kapcsolatos további információkért lásd [a számlázási szakaszok ismertetését](billing-mca-overview.md#invoice-sections). |
| Fiók               | A Nagyvállalati Szerződésben létrehozott fiókok nem támogatottak az új számlázási fiókban. A fiók előfizetései a részlegek megfelelő számlázási szakaszához tartoznak. A fióktulajdonosok létrehozhatják és kezelhetik a számlázási szakaszaikhoz tartozó előfizetéseket. |

## <a name="changes-for-enterprise-administrators"></a>Módosítások a vállalati rendszergazdák esetében

Az alábbi módosítások azon nagyvállalati szerződéses vállalati rendszergazdákra érvényesek, akik Microsoft-ügyfélszerződésre lettek újítva.

- A rendszer létrehoz egy számlázási profilt a regisztrációjához. A számlázási profillal a nagyvállalati szerződéses regisztrációhoz hasonlóan kezelheti a szervezet számlázását. A számlázási profilokkal kapcsolatos további információkért lásd [a számlázási profilok ismertetését](billing-mca-overview.md#billing-profiles).
- A rendszer létrehoz egy számlázási szakaszt a nagyvállalati szerződéses regisztráció egyes részlegei számára. A részlegeket a számlázási szakaszokkal kezelheti. Új számlázási szakaszok létrehozásával további részlegeket állíthat be. A számlázási szakaszokkal kapcsolatos további információkért lásd [a számlázási szakaszok ismertetését](billing-mca-overview.md#invoice-sections).
- Az Azure-előfizetés létrehozói szerepkörének számlázási szakaszokban történő használatával a nagyvállalati szerződéses regisztrációban létrehozott fiókokhoz hasonlóan engedélyt adhat másoknak Azure-előfizetések létrehozására.
- A nagyvállalati szerződéses Azure Portal helyett az [Azure Portallal](https://portal.azure.com) kezelheti a szervezet számlázását.

Az új számlázási fiókban az alábbi szerepkörökkel rendelkezik:

**Számlázási profil tulajdonosa** – A rendszer hozzárendeli Önhöz a szerződés aláírásakor létrehozott számlázási profil tulajdonosi szerepkörét a számlázási profilban. Ezzel a szerepkörrel kezelheti a szervezete számlázását. Megtekintheti a díjakat és a számlákat, rendszerezheti a számla költségeit, kezelheti a fizetési módokat és szabályozhatja a szervezete számlázásához való hozzáférést.

**Számlázási szakasz tulajdonosa** – A rendszer hozzárendeli Önhöz a számlázási szakasz tulajdonosi szerepkörét a nagyvállalati szerződéses regisztráció részlegei számára létrehozott összes számlázási szakasz esetében. Ezzel a szerepkörrel megszabhatja, hogy ki hozhat létre Azure-előfizetéseket és vásárolhat egyéb termékeket.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>A szervezete költségeinek és kreditegyenlegének megtekintése

A számlázási profillal a nagyvállalati szerződéses regisztrációhoz hasonlóan nyomon követheti a szervezete költségeinek és Azure-kreditjeinek egyenlegét.

A számlázási profil kreditegyenlegének megtekintésével kapcsolatos tudnivalókért lásd [a számlázási profilhoz tartozó Azure-kredit egyenlegének nyomon követését](billing-mca-check-azure-credits-balance.md) ismertető cikket.

A számlázási profil díjainak megtekintésével kapcsolatos tudnivalókért lásd [a Microsoft-ügyfélszerződéshez tartozó számlán szereplő díjak ismertetését](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Egy részleg költségeinek megtekintése

A rendszer létrehoz egy számlázási szakaszt a nagyvállalati szerződésben használt összes részleg számára. A számlázási szakaszok költségeit az Azure Portalon tekintheti meg. További információkért lásd [az egyes számlázási szakaszok tranzakcióinak megtekintését](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections) ismertető szakaszt.

### <a name="view-charges-for-an-account"></a>Egy fiók költségeinek megtekintése

A nagyvállalati szerződéses regisztrációban létrehozott fiókok nem támogatottak az új számlázási fiókban. A fiók előfizetései a részlegek megfelelő számlázási szakaszához tartoznak. A fióktulajdonosok létrehozhatják és kezelhetik a számlázási szakaszaikhoz tartozó előfizetéseket.

A fiókhoz tartozó előfizetések összesített költségeinek megtekintéséhez be kell állítania egy költséghelyet az egyes előfizetésekhez. Ezután az Azure-beli használati adatok és díjak CSV-fájljának segítségével a költséghely alapján szűrheti az előfizetéseket.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>A használati adatok és díjak CSV-fájljának, valamint az árlista és az adózási dokumentumok letöltése

A rendszer havonta létrehoz egy számlát a számlázási fiók minden számlázási profilja számára. Minden számla esetében letöltheti az Azure-beli használati adatok és díjak CSV-fájlját, az árlistát és az adózási dokumentumokat (ha vannak). Az Azure-beli használati adatok és díjak CSV-fájlját az aktuális hónap költségeivel kapcsolatban is letöltheti.

Az Azure-beli használati adatok és díjak CSV-fájljának letöltésével kapcsolatban lásd [a Microsoft-ügyfélszerződés használati adatainak letöltését](billing-download-azure-daily-usage.md) ismertető cikket.

A díjszabás letöltésével kapcsolatos tudnivalókért lásd [a Microsoft-ügyfélszerződés díjszabásának letöltését](billing-ea-pricing.md) ismertető cikket.

Az adózási dokumentumok letöltésével kapcsolatos tudnivalókért lásd [a Microsoft-ügyfélszerződés adózási dokumentumainak megtekintését](billing-mca-download-tax-document.md#view-and-download-tax-documents) ismertető cikket.

### <a name="add-an-additional-enterprise-administrator"></a>További vállalati rendszergazdák hozzáadása

A számlázási profilhoz hozzáférést biztosíthat a felhasználóknak ahhoz, hogy megtekinthessék és kezelhessék a szervezete számlázását. A hozzáférés biztosításához használja az Azure Portal **Hozzáférés-vezérlés (IAM)** lapját.  A számlázási profil szerepköreivel kapcsolatos további információkért lásd [a számlázási profil szerepköreinek és azok feladatainak ismertetését](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

A számlázási profilhoz történő hozzáférés biztosításával kapcsolatos tudnivalókért lásd [a számlázási szerepkörök kezelését az Azure Portalon](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Új részleg létrehozása

A költségek igényei szerinti rendszerezéséhez a nagyvállalati szerződéses regisztráció részlegeihez hasonló számlázási szakaszokat hozhat létre. Új számlázási szakaszokat az Azure Portalon hozhat létre. További információkért lásd a [szakaszok a számlán történő létrehozását a költségek rendszerezéséhez](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Új fiók létrehozása

Hozzárendelheti az Azure-előfizetés számlázási szakaszainak létrehozói szerepkörét a felhasználókhoz, akiknek így a nagyvállalati szerződéses regisztrációban létrehozott fiókokhoz hasonlóan engedélyt adhat Azure-előfizetések létrehozására. A szerepkörök hozzárendelésével kapcsolatos további információért tekintse meg [a számlázási szerepkörök az Azure Portalon történő kezelését](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal) ismertető cikket.

## <a name="changes-for-department-administrators"></a>Módosítások a részlegszintű rendszergazdák esetében

Az alábbi módosítások érvényesek a Nagyvállalati Szerződés azon részlegszintű rendszergazdáira, akik Microsoft-ügyfélszerződésre lettek újítva.

- A rendszer létrehoz egy számlázási szakaszt a nagyvállalati szerződéses regisztráció egyes részlegei számára. A részleg(ek)et a számlázási szakasz(ok) segítségével kezelheti. A számlázási szakaszokkal kapcsolatos további információkért lásd [a számlázási szakaszok ismertetését](billing-mca-overview.md#invoice-sections).
- Az Azure-előfizetés létrehozói szerepkörének számlázási szakaszokban történő használatával a nagyvállalati szerződéses regisztrációban létrehozott fiókokhoz hasonlóan engedélyt adhat másoknak Azure-előfizetések létrehozására.
- A nagyvállalati szerződéses Azure Portal helyett az Azure Portallal kezelheti a szervezet számlázását.

Az új számlázási fiókban az alábbi szerepkörökkel rendelkezik:

**Számlázási szakasz tulajdonosa** – A rendszer hozzárendeli Önhöz a számlázási szakasz tulajdonosi szerepkörét a Nagyvállalati Szerződés részlege(i) számára létrehozott számlázási szakasz esetében. Ezzel a szerepkörrel megtekintheti és nyomon követheti a költségeket, valamint megszabhatja, hogy kik hozhatnak létre Azure-előfizetéseket és vásárolhatnak egyéb termékeket a számlázási szakasz esetében.

### <a name="view-charges-for-your-department"></a>A részleg költségeinek megtekintése

A részleg számára létrehozott számlázási szakasz költségeit az Azure Portal [Költségkezelés + számlázás lapján](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) tekintheti meg.

### <a name="add-an-additional-department-administrator"></a>További részlegszintű rendszergazdák hozzáadása

A rendszer létrehoz egy számlázási szakaszt a nagyvállalati szerződésben használt összes részleg számára. Ha hozzáférést szeretne biztosítani másoknak a számlázási szakasz megtekintéséhez és kezeléséhez, használja az Azure Portal **Hozzáférés-vezérlés (IAM)** lapját. A számlázásiszakasz-szerepkörökkel kapcsolatos további információkért lásd [a számlázásiszakasz-szerepkörök ismertetését](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

A számlázási szakaszhoz történő hozzáférés biztosításával kapcsolatos tudnivalókért lásd [a számlázási szerepkörök az Azure Portalon történő kezelését](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Új fiók létrehozása a részlegben

Hozzárendelheti a felhasználókhoz az Azure-előfizetés a részleg számára létrehozott számlázási szakaszának létrehozói szerepkörét. A szerepkörök hozzárendelésével kapcsolatos további információért tekintse meg [a számlázási szerepkörök az Azure Portalon történő kezelését](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal) ismertető cikket.

### <a name="view-charges-for-accounts-in-your-departments"></a>A részlegekben található fiókok költségeinek megtekintése

A nagyvállalati szerződéses regisztrációban létrehozott fiókok nem támogatottak az új számlázási fiókban. A fiók előfizetései a részlegek megfelelő számlázási szakaszához tartoznak. A fióktulajdonosok létrehozhatják és kezelhetik a számlázási szakaszaikhoz tartozó előfizetéseket.

A részlegben található fiókhoz tartozó előfizetések összesített költségeinek megtekintéséhez be kell állítania egy költséghelyet az egyes előfizetésekhez. Ezután az Azure-beli használati adatok és díjak fájljának segítségével a költséghely alapján szűrheti az előfizetéseket.

## <a name="changes-for-account-owners"></a>Módosítások a fióktulajdonosok esetében

A nagyvállalati szerződéses fióktulajdonosok engedélyt kapnak az Azure-előfizetések az új számlázási fiókban történő létrehozására. A meglévő Azure-előfizetések a részleg számára létrehozott számlázási szakaszhoz tartoznak. Ha a fiókja nem tartozik részleghez, az előfizetések egy Alapértelmezett számlázási szakasz nevű számlázási szakaszhoz tartoznak.

További Azure-előfizetések létrehozásához az alábbi szerepkört kapja az új számlázási fiókban.

**Azure-előfizetés létrehozója** – A rendszer hozzárendeli Önhöz az Azure-előfizetés létrehozói szerepkörét a nagyvállalati szerződéses részleg számára létrehozott számlázási szakasz esetében. Ha a fiókja nem tartozik részleghez, akkor az Azure-előfizetés létrehozói szerepkörét egy Alapértelmezett számlázási szakasz nevű szakaszon kapja meg. Ezzel a szerepkörrel létrehozhat Azure-előfizetéseket a számlázási szakaszban.

### <a name="create-an-azure-subscription"></a>Azure-előfizetés létrehozása

A számlázási szakaszhoz az Azure Portalon hozhat létre Azure-előfizetéseket. További információkért lásd a [további Azure-előfizetések a Microsoft-ügyfélszerződéshez történő létrehozását](billing-mca-create-subscription.md).

### <a name="view-charges-for-your-account"></a>A fiók költségeinek megtekintése

A fiókhoz tartozó előfizetések költségeinek megtekintéséhez lépjen az Azure Portal [Előfizetések lapjára](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Az Előfizetések lap megjeleníti az összes előfizetés költségeit.

### <a name="view-charges-for-a-subscription"></a>Az előfizetés költségeinek megtekintése

Az előfizetések költségeit az [Előfizetések lapon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) vagy az Azure költségelemzési szolgáltatásával tekintheti meg. Az Azure költségelemzési szolgáltatásával kapcsolatos további információkért lásd a [költségek a költségelemzési szolgáltatás használatával történő feltérképezését és elemzését](../cost-management/quick-acm-cost-analysis.md) ismertető cikket.

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- [A Microsoft-ügyfélszerződéshez tartozó számlázási fiók ismertetése](billing-mca-overview.md)
- [A számla értelmezése](billing-understand-your-bill.md)
- [Számlázással kapcsolatos tudnivalók](billing-understand-your-invoice.md)
- [Azure-előfizetések számlázási tulajdonjogának beszerzése más felhasználóktól](billing-mca-request-billing-ownership.md)
