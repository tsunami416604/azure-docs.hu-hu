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
ms.openlocfilehash: 5980b8b701695d002d58b6b5239e6fa04c02fb2f
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248945"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>A Microsoft vevői szerződés az Ön számlázási fiókjához a teljes nagyvállalati szerződés feladatok

Ha a szervezet egy Microsoft Ügyfélszerződéséhez, hogy újítsa meg a nagyvállalati szerződésre való beléptetésről van bejelentkezve, majd be kell állítania az új számlázási fiók, a szerződés létrehozva. Az új számlázási fiók a, továbbfejlesztett számlázási és a cost management lehetőségei a egy új egyszerű és egységesített kezelést biztosít. További információkért lásd: [számlázási fiók beállítása a Microsoft vevői szerződés](billing-mca-setup-account.md).

A telepítés befejezése után ez a cikk segítségével megismerheti, hogyan hajthat végre a nagyvállalati szerződésre való beléptetésről végrehajtott feladatokat.

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-your-access-to-a-microsoft-customer-agreement).

## <a name="mapping-of-enterprise-agreements-billing-to-the-new-billing-account"></a>Az új számlázási fiókjához nagyvállalati szerződés számlázási leképezése

A számlázás pedig az új fiók eltér a nagyvállalati szerződésre való beléptetésről van rendezve. Az alábbi táblázat az új számlázási fiókba, a nagyvállalati szerződésre való beléptetésről számlázási hozzárendelését ismerteti. Az új számlázási fiók kapcsolatos további információkért lásd: [Microsoft Ügyfélszerződéséhez Ismerkedés az Ön számlázási fiókjához](billing-mca-overview.md).

| Nagyvállalati szerződés   | Microsoft ügyfélszerződés    |
|------------------------|--------------------------------------------------------|
| Regisztráció            | A számlázási profilja segítségével kezelheti a számlázási szervezetben, a nagyvállalati szerződésre való beléptetésről hasonló. Vállalati rendszergazdák a számlázási profilja tulajdonosai lesznek. Számlázási profilokkal kapcsolatos további tudnivalókért lásd: [számlázási a profilok megismerése](billing-mca-overview.md#understand-billing-profiles).
| Részleg            | Egy számla szakasz használata a költségeket, a nagyvállalati szerződésre való beléptetésről részlegek hasonló rendszerezéséhez. Részleg számla szakaszok válik, és a részleg rendszergazdái válnak a megfelelő számla szakaszok tulajdonosai. Számla szakaszok kapcsolatos további információkért lásd: [Understand számla szakaszok](billing-mca-overview.md#understand-invoice-sections). |
| Fiók               | A fiókok létrejöttek a nagyvállalati szerződésbe, az új számlázási fiók nem támogatottak. A fiók előfizetés tartozik, azok számára a megfelelő számla szakaszra. Fióktulajdonosok is előfizetéseinek létrehozásához és kezeléséhez a számla szakaszokat. |

## <a name="tasks-performed-by-an-enterprise-administrator"></a>A vállalati rendszergazdák által végrehajtott feladatok

Ha Ön egy vállalati rendszergazda engedélyezte a nagyvállalati szerződéssel, van egy Microsoft-ügyfél megállapodás megújítani, a következő szerepkörök a a szerződés létrehozott új számlázási fiók rendelt:

**A számlázás profiltulajdonossal** -jött létre, amikor a szerződés aláírásának számlázási profilon számlázási profil tulajdonosi szerepkör van rendelve. A szerepkör lehetővé teszi a szervezet számára a számlázás kezelését. Díjak és számlák megtekintéséhez, rendszerezheti a költségek, a számlán, fizetési módok kezelése és ki férhet hozzá a szervezet számlái.

**Számla szakasz tulajdonosa** – minden számla szakaszt, amely a nagyvállalati szerződésre való beléptetésről részlegei jön létre a számla szakasz tulajdonosi szerepkör van rendelve. A szerepkör lehetővé teszi, hogy Ön szabályozza, ki is az Azure-előfizetések létrehozása, és vásárolja meg más termékekkel.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>A szervezet díjait és a kreditek egyenleg megtekintése

A számlázási profilja segítségével nyomon követheti a költségek és az Azure-kreditek egyenleg a nagyvállalati szerződésre való beléptetésről hasonlóan a szervezet számára.

A számlázási profilja kreditegyenlegét megtekintése kapcsolatban lásd: [kreditegyenlegét nyomon követése az Azure számlázási profilját](billing-mca-check-azure-credits-balance.md).

Megtudhatja, hogyan kell fizetni egy számlázási profilja megtekintéséhez, lásd: [a költségek, a Microsoft Ügyfélszerződéséhez számlán ismertetése](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Intézet költségek megtekintése

Az egyes részlegek, a nagyvállalati szerződést kellett egy számla szakasz jön létre. Megtekintheti a számla szakasz díjak az Azure Portalon. További információkért lásd: [tranzakciók számla szakaszonként megjelenítéséhez](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Egy olyan fiók költségek megtekintése

A fiókok, a nagyvállalati szerződésre való beléptetésről létrejöttek az új számlázási fiók nem támogatottak. A fiók előfizetés tartozik, azok számára a megfelelő számla szakaszra. Fióktulajdonosok is előfizetéseinek létrehozásához és kezeléséhez a számla szakaszokat.

Összesített költségét fiókhoz tartoztak előfizetések megtekintéséhez be kell egy erőforrás minden egyes előfizetés esetén. Ezt követően használhatja az Azure használat és költségek csv-fájl szűrése a költséghely szerint az előfizetéseket.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Használat és költségek csv letöltése, árlista és adó dokumentumok

A havi számla jön létre minden egyes számlázási profilja az Ön számlázási fiókjához. Minden számlán a letöltheti az Azure használat és költségek csv-fájl, árlista és adó dokumentum (ha van). Az Azure használat és költségek csv-fájl az aktuális hónap díjakat is letölthető.

Az Azure használat és költségek csv-fájl letöltésére megismerkedni, lásd: [töltse le az Azure számlázási és napi használati adatok megtekintéséhez vagy](billing-download-azure-invoice-daily-usage-date.md). <!--Todo update the link -->

Megtudhatja, hogyan töltse le a lap- és adózási dokumentumok ár, [letöltési adó dokumentumok számláit](billing-download-azure-invoice-daily-usage-date.md). <!--Todo update the link -->

### <a name="add-an-additional-enterprise-administrator"></a>A vállalati további rendszergazdák hozzáadása

Használja a **hozzáférés Control(IAM)** oldal megtekintése és kezelése a számlázási profilja hozzáférés biztosítása más az Azure Portalon. Profil szerepkörök számlázással kapcsolatos további információkért lásd: [számlázási profil szerepkörök és feladatok](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Adja meg, a számlázási profilja megismeréséhez, tekintse meg a [kezelése az Azure Portal számlázási szerepkörök](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Hozzon létre egy új szervezeti egység

Az új számlázási fiók egy számla szakaszban használhatja a saját igényei szerint, a nagyvállalati szerződésre való beléptetésről részlegek hasonló költségeket rendszerezéséhez. Számla című új szakasszal létrehozhat egy új szervezeti egység az Azure beállítása az Azure Portalon. További tudnivalókért lásd: [szakaszok létrehozása a számla a költségek rendszerezéséhez](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Új fiók létrehozása

A számlázási környezetét a nagyvállalati szerződést az új számlázási fiók nem támogatott. Azonban megadhat Azure-előfizetés létrehozója szerepkör a felhasználók számára, hogy azokat az Azure-előfizetéseket hozhasson létre a szervezetében. További információkért lásd: [engedélyezze a más Azure-előfizetések létrehozására](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

## <a name="tasks-performed-by-a-department-administrator"></a>A részleg rendszergazdája által végrehajtott feladatok

Ha egy részleg rendszergazdája, amely a Microsoft ügyfél-megállapodás megújítani nagyvállalati szerződéssel, a következő szerepkörök az új számlázási fiókjához rendelt:

**Számla szakasz tulajdonosa** -jön létre a szervezeti egységek, a nagyvállalati szerződés kellett számla szakasz a számla szakasz tulajdonosi szerepkör van rendelve. A szerepkör lehetővé teszi megtekintése és költségek nyomon követése és vezérlés ki Azure-előfizetések létrehozására és egyéb termékek, a számla szakaszának vásárolni.

Ha egy részleg rendszergazdája több részlegek számára a nagyvállalati szerződés, rendelt számla szakasz tulajdonosi szerepkör a részlegek számára létrehozott összes számla szakaszokat.

### <a name="view-charges-for-your-department"></a>A részleg költségek megtekintése

Az egyes részlegek, a nagyvállalati szerződést kellett egy számla szakasz jön létre. A számla a szakasz a nagyvállalati szerződés neve megegyezik a szervezeti egység tartalmaz. Az Azure Portalon is megtekintheti a számla szakaszban kell fizetni. <!-- Todo Add a link -->

### <a name="add-an-additional-department-administrator"></a>A további részleg rendszergazda hozzáadása

Az egyes részlegek, a nagyvállalati szerződést kellett egy számla szakasz jön létre. Használhatja a **hozzáférés Control(IAM)** oldal megtekintése és kezelése a számla szakasz hozzáférés biztosítása más az Azure Portalon. Számla szakasz szerepkörök kapcsolatos további információkért lásd: [profil szerepkörök és feladatok számla](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Adja meg, a számla a szakasz a kezelésével kapcsolatos információkért lásd: [kezelése az Azure Portal számlázási szerepkörök](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Hozzon létre egy új fiókot a osztály

A számlázási környezetét a nagyvállalati szerződést az új számlázási fiók nem támogatott. Azonban megadhat Azure-előfizetés létrehozója szerepkör a felhasználók számára, hogy azokat az Azure-előfizetéseket hozhasson létre a szervezetében. További információkért lásd: [engedélyezze a más Azure-előfizetések létrehozására](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

### <a name="view-charges-for-accounts-in-your-departments"></a>A szervezeti fiókokat a költségek megtekintése

A számlázási környezetét a nagyvállalati szerződést az új számlázási fiók nem támogatott. A fiók-előfizetések tartoznak a számla a szakasz a részleg számára létrehozott. Fióktulajdonosok is előfizetéseinek létrehozásához és kezeléséhez a számla szakaszokat.

Összesített költségét a részleg egy fiókhoz tartoztak előfizetések megtekintéséhez be kell egy erőforrás minden egyes előfizetés esetén. Ezt követően használhatja azt, hogy a használat és költségek az Azure file szűrése a költséghely szerint az előfizetéseket. <!-- Todo - can they go to cost analysis -->

## <a name="tasks-performed-by-an-account-owner"></a>Fiók tulajdonosa által végrehajtott feladatok

Ha Ön nagyvállalati szerződéssel, van egy Microsoft-ügyfél megállapodás megújítani a fiók tulajdonosa, az új számlázási fiók, a szerződés létrehozva a következő szerepkört rendelt:

**Azure-előfizetés létrehozója** -a a számla szakaszban létrehozott azure-előfizetés létrehozója szerepkört a nagyvállalati szerződés osztály vannak hozzárendelve. Ha a fiókja nem tartozik egy részleg, az előfizetések a számla szakasz nevű alapértelmezett számla szakaszban fog tartozni, és a szakasz az Azure-előfizetés létrehozója kap. A szerepkör lehetővé teszi a számla szakaszának Azure-előfizetések létrehozására.

### <a name="view-charges-for-your-account"></a>A fiók költségek megtekintése

A számlázási környezetét a nagyvállalati szerződést az új számlázási fiók nem támogatott. A fiók-előfizetések tartoznak a számla szakaszban, a fiók számára létrehozott.

Egy fiókhoz tartoztak-szolgáltatások díjai megtekintéséhez nyissa meg a **előfizetések lapon** az Azure Portalon. Az előfizetések lap az összes előfizetéshez tartozó díjakat jeleníti meg. <!-- Todo - can they go to cost analysis -->

### <a name="view-charges-for-a-subscription"></a>Egy előfizetéshez tartozó költségek megtekintése

Az Azure cost analysis segítségével egy előfizetéshez tartozó díjaikat. További információkért lásd: [vizsgálata és elemzése a költségeket a költségelemzés](../cost-management/quick-acm-cost-analysis.md).

### <a name="create-an-azure-subscription"></a>Azure-előfizetés létrehozása

A számla című rész ismerteti az Azure Portalon az Azure-előfizetéseket hozhat létre. További információkért lásd: [további Azure-előfizetés létrehozása a Microsoft ügyfél-szerződés](billing-mca-create-subscription.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, kell [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- [A Microsoft vevői szerződés számlázási fiók ismertetése](billing-mca-overview.md)
- [A számla ismertetése](billing-understand-your-bill.md)
- [Számlázással kapcsolatos tudnivalók](billing-understand-your-invoice.md)
- [Első számlázási tulajdonosi jogokat, más felhasználók az Azure-előfizetések](billing-mca-request-billing-ownership.md)
