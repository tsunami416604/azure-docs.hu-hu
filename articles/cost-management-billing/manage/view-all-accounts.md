---
title: Számlázási fiókok megtekintése az Azure Portalon
description: Ismerje meg, hogyan tekintheti meg számlázási fiókjait az Azure Portalon.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: banders
ms.openlocfilehash: 4dab11afb2ddd910a4d6619d088031279ebf3172
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238006"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Számlázási fiókok és hatókörök az Azure Portalon

A számlázási fiók az Azure-ba való regisztráció során jön létre. A számlázási fiókban kezelheti számláit, fizetéseit és nyomon követheti kiadásait. Több számlázási fiókhoz is rendelkezhet hozzáféréssel. Előfordulhat például, hogy az egyéni projektjei kezeléséhez regisztrált az Azure-ba. Emellett rendelkezhet hozzáféréssel a szervezete Nagyvállalati Szerződésén vagy egy Microsoft-ügyfélszerződésen keresztül is. Mindegyik regisztrációhoz egy külön számlázási fiók tartozik.

Az Azure Portal a következő típusú számlázási fiókokat támogatja:

- **Microsoft Online Services Program**: A Microsoft Online Services Program számlázási fiókjai akkor jönnek létre, amikor az Azure-webhelyen keresztül regisztrál az Azure-ba. Erre példa az [ingyenes Azure-fiók](https://azure.microsoft.com/offers/ms-azr-0044p/), a [használatalapú fizetést használó fiók](https://azure.microsoft.com/offers/ms-azr-0003p/) és a [Visual Studio-előfizetés](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Nagyvállalati Szerződés**: Nagyvállalati Szerződéshez tartozó számlázási fiók akkor jön létre, amikor a szervezet [Nagyvállalati Szerződést (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) köt az Azure használatára.

- **Microsoft-ügyfélszerződés**: A Microsoft-ügyfélszerződéshez tartozó számlázási fiók akkor jön létre, amikor a szervezet a Microsoft képviselőjével együttműködve Microsoft-ügyfélszerződést köt. Egyes régiókban, ha a felhasználó az Azure-webhelyen regisztrál egy [használatalapú fizetést használó fiókot](https://azure.microsoft.com/offers/ms-azr-0003p/) vagy egy [ingyenes Azure-fiókot](https://azure.microsoft.com/offers/ms-azr-0044p/), külön számlázási fiókot kaphat a Microsoft-ügyfélszerződéshez is. További információkért tekintse meg [a Microsoft-ügyfélszerződés számlázási fiókjával kapcsolatos első lépéseket](../understand/mca-overview.md).

- **Microsoft-partnerszerződés**: A Microsoft-partnerszerződés számlázási fiókjai a felhőszolgáltatói partnerek (CSP-k) számára készülnek az új kereskedelmi felületen történő ügyfélkezeléshez. A partnereknek legalább egy [Azure-csomaggal](https://docs.microsoft.com/partner-center/purchase-azure-plan) rendelkező ügyfélre van szükségük ahhoz, hogy számlázási fiókjukat az Azure Portalon kezelhessék. További információért tekintse meg [a Microsoft-partnerszerződés számlázási fiókjával kapcsolatos első lépéseket](../understand/mpa-overview.md).

A számlázási fiók típusának meghatározásával kapcsolatban lásd [a számlázási fiók típusának ellenőrzését](#check-the-type-of-your-account) ismertető cikket.

## <a name="scopes-for-billing-accounts"></a>A számlázási fiókok hatóköre
A hatókör egy csomópont a számlázási fiókon belül. Ennek használatával lehet megtekinteni és kezelni a számlázást. Itt lehet kezelni a számlázási adatokat, a fizetéseket, a számlákat, és itt történik az általános fiókkezelés.

### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

 ![Az MOSP hierarchiáit megjelenítő képernyőkép](./media/view-all-accounts/mosp-hierarchy.png)

|Hatókör  |Meghatározás  |
|---------|---------|
|Számlázási fiók     | Azt a szerződést jelöli, amelynek elfogadásával az ügyfél jogosulttá válik az Azure használatára. Egy vagy több előfizetést tartalmaz.  |
|Előfizetés     |  Adott Azure-erőforrások csoportja. Ebben a hatókörben történik számlakiállítás. Az egyéb számlázási információk, például a fizetési módok és a használati cím, szintén ehhez a hatókörhöz vannak társítva.|

### <a name="enterprise-agreement"></a>Nagyvállalati Szerződés

![Az EA hierarchiáit megjelenítő képernyőkép](./media/view-all-accounts/ea-hierarchy.png)

|Hatókör  |Meghatározás  |
|---------|---------|
|Számlázási fiók    | Egy Nagyvállalati Szerződésre való regisztrációt jelöl. Egy vagy több részleget és fiókot tartalmaz. Ebben a hatókörben történik számlakiállítás. |
|Részleg     |  A fiókok választható csoportosítása a költségek logikai csoportosítás szerinti szegmentálásához és a költségvetés beállításához.     |
|Fiók     |  Egyetlen fióktulajdonost jelöl. A fióktulajdonosok engedéllyel rendelkeznek a regisztrációhoz számlázott Azure-előfizetések létrehozására és kezelésére. |

### <a name="microsoft-customer-agreement"></a>Microsoft-ügyfélszerződés

![Az MCA hierarchiáit megjelenítő képernyőkép](./media/view-all-accounts/mca-hierarchy.png)

|Hatókör  |Feladatok  |
|---------|---------|
|Számlázási fiók     |   Azt a szerződést jelöli, amelynek elfogadásával az ügyfél jogosulttá válik a Microsoft termékeinek és szolgáltatásainak használatára. Egy vagy több számlázási profilt tartalmaz. |
|Számlázási profil     |   A számlázát és a kapcsolódó számlázási információkat, például a fizetési módokat és a számlázási címet jeleníti meg. Egy vagy több számlázási szakaszt tartalmaz. |
|Számlázási szakasz     |   Egy költségcsoportosítást jelöl a számlán belül. Az Azure-előfizetések és más vásárlások, például az Azure Marketplace-en vagy az AppSource-on beszerzett termékek ehhez a hatókörhöz vannak társítva.    |

### <a name="microsoft-partner-agreement"></a>Microsoft-partnerszerződés

![Az MPA hierarchiáit megjelenítő képernyőkép](./media/view-all-accounts/mpa-hierarchy.png)

|Hatókör  |Feladatok  |
|---------|---------|
|Számlázási fiók     |   Olyan partnerszerződést jelöl, amely az ügyfelek Microsoft-termékeinek és -szolgáltatásainak az új kereskedelmi felületen való kezeléséhez szükséges. Egy vagy több számlázási profilt és ügyfelet tartalmaz.   |
|Számlázási profil     |   Egy adott pénznem szerinti számlát jelöl.     |
|Ügyfél    |   Felhőszolgáltató (CSP-) partnerek ügyfelét jelöli.  Az Azure-előfizetések és más vásárlások, például az Azure Marketplace-en vagy az AppSource-on beszerzett termékek ehhez a hatókörhöz vannak társítva.  |
|Viszonteladó    |   Az a viszonteladó, aki szolgáltatásokat nyújt az ügyfélnek. Ez egy nem kötelezően kitöltendő mező, és csak a közvetett szolgáltatókra vonatkozik a CSP kétrétegű modelljében.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>A számlázás hatókörének módosítása az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. Az áttekintés oldalon válassza a **Hatókör váltása** elemet.

   ![A számlázási hatóköröket megjelenítő képernyőkép](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Ha csak egyetlen hatókörhöz van hozzáférése, nem jelenik meg a Hatókör váltása lehetőség.

4. Válasszon ki egy hatókört a részletek megtekintéséhez.

   ![A számlázási hatóköröket megjelenítő képernyőkép](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>A fiók típusának megtekintése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [elemezheti költségeit](../costs/quick-acm-cost-analysis.md).
