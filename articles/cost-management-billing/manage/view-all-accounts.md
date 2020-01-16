---
title: Számlázási fiókok megtekintése az Azure Portalon | Microsoft Docs
description: Ismerje meg, hogyan tekintheti meg számlázási fiókjait az Azure Portalon.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 2768d6e146a37e86bb36353f661179ebd7b5033d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994128"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Számlázási fiókok és hatókörök a Azure Portal

A számlázási fiók az Azure-ba való regisztráció során jön létre. A számlázási fiókban kezelheti számláit, fizetéseit és nyomon követheti kiadásait. Több számlázási fiókhoz is rendelkezhet hozzáféréssel. Előfordulhat például, hogy az egyéni projektjei kezeléséhez regisztrált az Azure-ba. Emellett rendelkezhet hozzáféréssel a szervezete Nagyvállalati Szerződésén vagy egy Microsoft-ügyfélszerződésen keresztül is. Mindegyik regisztrációhoz egy külön számlázási fiók tartozik.

Azure Portal a következő típusú számlázási fiókokat támogatja:

- **Microsoft Online Services program**: a Microsoft Online Services programhoz tartozó számlázási fiók akkor jön létre, amikor az Azure-webhelyről regisztrál az Azure-ra. Erre példa az [ingyenes Azure-fiók](https://azure.microsoft.com/offers/ms-azr-0044p/), a [használatalapú fizetést használó fiók](https://azure.microsoft.com/offers/ms-azr-0003p/) és a [Visual Studio-előfizetés](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Nagyvállalati szerződés**: létrejön egy nagyvállalati szerződés számlázási fiókja, amikor a szervezet aláír egy [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) az Azure használatára.

- **Microsoft**-szerződés: a Microsoft ügyfél-szerződéshez tartozó számlázási fiók akkor jön létre, ha a szervezet Microsoft-képviselővel működik együtt egy Microsoft-ügyfél-szerződés aláírására. Egyes régiókban lévő ügyfelek, akik az Azure webhelyéről regisztrálhatnak az utólagos elszámolású [díjszabással](https://azure.microsoft.com/offers/ms-azr-0003p/) , vagy egy [ingyenes Azure-fiókkal](https://azure.microsoft.com/offers/ms-azr-0044p/) is rendelkezhetnek számlázási fiókkal egy Microsoft-ügyfél szerződéshez. További információkért tekintse meg [a Microsoft-ügyfélszerződés számlázási fiókjával kapcsolatos első lépéseket](../understand/mca-overview.md).

- **Microsoft partneri szerződés**: a Microsoft partneri szerződéshez tartozó számlázási fiók a Cloud Solution Provider (CSP) partnerei számára lett létrehozva, hogy az új kereskedelmi felhasználói élményben kezeljék ügyfeleit. A partnereknek rendelkezniük kell legalább egy ügyféllel egy [Azure-csomaggal](https://docs.microsoft.com/partner-center/purchase-azure-plan) a számlázási fiókjuk Azure Portal való kezeléséhez. További információ: Ismerkedés a [Microsoft partneri szerződés számlázási fiókjával](../understand/mpa-overview.md).

A számlázási fiók típusának meghatározásával kapcsolatban lásd [a számlázási fiók típusának ellenőrzését](#check-the-type-of-your-account) ismertető cikket.

## <a name="scopes-for-billing-accounts"></a>A számlázási fiókok hatóköre
A hatókör a számlázási fiókon belüli csomópont, amelyet a számlázás megtekintésére és kezelésére használhat. Itt kezelheti a számlázási adataikat, a befizetéseket, a számlákat és az általános fiókok felügyeletét.

### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

 ![Képernyőkép, amely megjeleníti a MOSP-hierarchiát](./media/view-all-accounts/mosp-hierarchy.png)

|Hatókör  |Meghatározás  |
|---------|---------|
|Számlázási fiók     | Egy olyan szerződést jelöl, amelyet az ügyfél elfogad az Azure használatához. Egy több érc-előfizetést tartalmaz.  |
|Előfizetés     |  Adott Azure-erőforrások csoportja. Ebben a hatókörben történik számlakiállítás. Az egyéb számlázási információk, például a fizetési módok és a használati cím ehhez a hatókörhöz vannak társítva.|

### <a name="enterprise-agreement"></a>Nagyvállalati szerződés

![Az EA-hierarchiát megjelenítő képernyőkép](./media/view-all-accounts/ea-hierarchy.png)

|Hatókör  |Meghatározás  |
|---------|---------|
|Számlázási fiók    | Egy Nagyvállalati Szerződésre való regisztrációt jelöl. Egy vagy több részleget és fiókot tartalmaz. Ebben a hatókörben történik számlakiállítás. |
|Részleg     |  Fiókok választható csoportosítása a költségek logikai csoportosításban való szegmentálása és a költségvetés beállítása alapján.     |
|Fiók     |  Egyetlen fióktulajdonost jelöl. A fiók tulajdonosai jogosultak olyan Azure-előfizetések létrehozására és kezelésére, amelyek számlázása a beléptetés után történik. |

### <a name="microsoft-customer-agreement"></a>Microsoft Ügyfélszerződés

![Az MCA-hierarchiát megjelenítő képernyőkép](./media/view-all-accounts/mca-hierarchy.png)

|Hatókör  |Feladatok  |
|---------|---------|
|Számlázási fiók     |   Egy olyan szerződést jelöl, amelyet az ügyfél elfogad a Microsoft-termékek és-szolgáltatások használatára. Egy vagy több számlázási profilt tartalmaz. |
|Számlázási profil     |   A számla és a kapcsolódó számlázási információk, például a fizetési módszerek és a számlázási cím. Egy vagy több számla szakaszt tartalmaz. |
|Számlázási szakasz     |   A számla költségeinek csoportosítását jelöli. Az Azure-előfizetések és egyéb vásárlások, például az Azure Marketplace és az App Source Products ehhez a hatókörhöz vannak társítva.    |

### <a name="microsoft-partner-agreement"></a>Microsoft partneri szerződés

![A MPA-hierarchiát bemutató képernyőkép](./media/view-all-accounts/mpa-hierarchy.png)

|Hatókör  |Feladatok  |
|---------|---------|
|Számlázási fiók     |   Egy partneri szerződést képvisel, amely az ügyfelek Microsoft-termékeit és-szolgáltatásait az új kereskedelmi élményben kezeli. Egy vagy több számlázási profilt és ügyfelet tartalmaz.   |
|Számlázási profil     |   A pénznem számláját jelöli.     |
|Ügyfél    |   Egy felhőalapú megoldás-szolgáltató (CSP) partner ügyfelét jelöli.  Az Azure-előfizetések és egyéb vásárlások, például az Azure Marketplace és az App Source Products ehhez a hatókörhöz vannak társítva.  |
|Viszonteladó    |   Viszonteladó, amely szolgáltatásokat nyújt az ügyfeleknek. Egy előfizetéshez választható mező, amely csak a CSP kétrétegű modellben lévő közvetett szolgáltatók esetében alkalmazható.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>A számlázás hatókörének módosítása az Azure Portalon

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. Az Áttekintés oldalon válassza a **váltás hatókör**lehetőséget.

   ![A számlázási hatóköröket megjelenítő képernyőkép](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > A váltás hatóköre nem jelenik meg, ha csak egy hatókörhöz fér hozzá.

4. Válasszon ki egy hatókört a részletek megtekintéséhez.

   ![A számlázási hatóköröket megjelenítő képernyőkép](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>A fiók típusának bejelölése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Következő lépések
- Ismerje meg, hogyan [elemezheti költségeit](../costs/quick-acm-cost-analysis.md).
