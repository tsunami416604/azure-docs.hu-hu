---
title: Bevezetés a Microsoft Partnerszerződéshez tartozó számlázási fiók használatába – Azure CSP
description: A Microsoft Partnerszerződéshez tartozó számlázási fiók ismertetése (CSP)
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 4dff56db9f1450ff7eb2a2b9ca6f4ca648e2ac38
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150096"
---
# <a name="get-started-with-your-microsoft-partner-agreement-billing-account"></a>Bevezetés a Microsoft Partnerszerződéshez tartozó számlázási fiók használatába

A számlázási fiók akkor jön létre, amikor regisztrálja magát az Azure használatára. A számlázási fiók használatával kezelheti a számlákat és a kifizetéseket, valamint nyomon követheti a költségeket. Több számlázási fiókhoz is rendelkezhet hozzáféréssel. Előfordulhat például, hogy az egyéni projektjei kezeléséhez regisztrált az Azure-ba. Emellett az Azure-hoz hozzáféréssel rendelkezhet a szervezet Nagyvállalati Szerződésén, Microsoft-ügyfélszerződésén vagy Microsoft Partnerszerződésén keresztül is. Mindegyik regisztrációhoz egy külön számlázási fiók tartozik.

Ez a cikk a Microsoft-partnerszerződések számlázási fiókjaira vonatkozik. Ezek a fiókok a felhőszolgáltatói partnerek (CSP-k) számára készülnek az ügyfelek számláinak az új kereskedelmi felületen való kezeléséhez. Az új felület csak olyan partnerek számára érhető el, akik legalább egy olyan ügyféllel rendelkeznek, aki elfogadta a Microsoft-ügyfélszerződést, illetve rendelkezik Azure-csomaggal. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-partnerszerződéshez](#check-access-to-a-microsoft-partner-agreement). Az [Azure-csomagok](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) a Microsoft Ügyfélszerződés feltételeiben rögzített használatalapú díjak szerint biztosítanak hozzáférést az ügyfelek számára az Azure-szolgáltatásokhoz.

## <a name="your-billing-account"></a>Az Ön számlázási fiókja

A Microsoft Partnerszerződés számlázási fiókja tartalmaz egy számlázási profilt minden pénznemhez, amelyet az üzletmenethez használ. A számlázási profilok segítségével pénznemek szerint kezelheti a számlákat. Amikor kapcsolatot létesít az ügyfelekkel, a pénznemtől függően az Azure-előfizetések és egyéb vásárlások számlázása a megfelelő számlázási profilokra történik.

Az alábbi ábrán a számlázási fiók, a számlázási profilok, az ügyfelek és a viszonteladók közötti kapcsolat látható.

![A Microsoft Partnerszerződés számlázási hierarchiáját bemutató ábra](./media/mpa-overview/mpa-hierarchy.svg)

A szervezetében **globális rendszergazda** és **rendszergazdai ügynök** szerepkörrel rendelkező felhasználók kezelhetik a számlázási fiókokat, a számlázási profilokat és az ügyfeleket. További információ: [Partnerközpont – Felhasználói szerepkörök és engedélyek hozzárendelése](/partner-center/permissions-overview)

## <a name="billing-profiles"></a>Számlázási profilok

A számlákat pénznem szerint a számlázási profilok segítségével kezelheti. A hónap elején a fiókhoz tartozó összes számlázási profilhoz létrejön egy havi számla. A számla az Azure-előfizetések és egyéb vásárlások előző havi díjait tartalmazza a számlázási profil pénznemében.

Az Azure Portalon megtekintheti a számlát és letöltheti a hozzá kapcsolódó dokumentumokat, mint a használati adatok fájljait és az árlistát. További információért lásd a [Microsoft Partnerszerződéshez tartozó számlák letöltésével](download-azure-invoice.md) kapcsolatos részt.

> [!IMPORTANT]
>
> A számlázási profilok számlái az Azure-csomagokkal, az SaaS-sel, az Azure Marketplace-szel és foglalásvásárlásokkal kapcsolatos díjakat tartalmaznak azon ügyfelekre vonatkozóan, akik még nem fogadták el a Microsoft-ügyfélszerződést, és nem rendelkeznek Azure-csomagokkal.

## <a name="customers"></a>Ügyfelek

Megtekintheti és kezelheti azokat az ügyfeleket, akik elfogadták a Microsoft-ügyfélszerződést és rendelkeznek Azure-csomaggal az Azure Portalon. Megtekintheti a díjakat és tranzakciókat, valamint létrehozhat és kezelhet Azure-előfizetéseket ezen felhasználók számára.

### <a name="enable-policy-to-give-visibility-into-cost"></a>Szabályzat engedélyezésével teheti átláthatóvá a költségeket

Szabályzattal vezérelheti, hogy az ügyfelek vállalatának felhasználói megtekinthetik-e és elemezhetik-e az Azure-használati költségeiket a használatalapú díjszabás alapján. A szabályzat alapértelmezés szerint ki van kapcsolva, és a felhasználók nem tekinthetik meg a költségeket. Ha engedélyezve van, az előfizetéshez megfelelő [Azure RBAC](../../role-based-access-control/overview.md) hozzáféréssel rendelkező felhasználók megtekinthetik és elemezhetik az előfizetés költségeit.

Szabályzat bekapcsolása:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

1. Végezzen keresést a **Költségkezelés + Számlázás** oldalon.

   ![Képernyőkép az Azure Portal keresőmezőjéről a Költségkezelés + számlázás keresőkifejezéssel.](./media/mpa-overview/search-cmb.png)

1. Válassza az **Ügyfelek** lehetőséget a bal oldalon, majd válasszon ki egy ügyfelet a listából.

   ![Képernyőkép az ügyfél kiválasztásáról](./media/mpa-overview/mpa-customers.png)

1. Válassza a **Szabályzatok** lehetőséget a bal oldalon.

   ![Képernyőkép a szabályzatokról](./media/mpa-overview/mpa-change-policy.png)

1. Válassza az **Igen** lehetőséget.

## <a name="resellers"></a>Viszonteladók

A CSP [kétrétegű modelljében](/partner-center) a közvetett szolgáltatók kiválaszthatják a viszonteladót, amikor előfizetéseket hoznak létre ügyfelek számára az Azure Portalon. A létrehozás után megtekinthetik az előfizetések listáját a viszonteladók szerint szűrve, valamint elemezhetik az ügyfelek költségeit az Azure költségelemzési szolgáltatásában viszonteladónként.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Microsoft-partnerszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>Következő lépések

A számlázási fiókkal kapcsolatos tudnivalókért tekintse meg az alábbi cikkeket:

- [További Azure-előfizetés létrehozása a Microsoft Partnerszerződésben](../manage/create-subscription.md)
- A számlázási adatok integrálása saját jelentéskészítő rendszerrel az [Azure számlázási API-k használatával](/rest/api/billing/)
- [Gyorsútmutató az Azure Cost Managementhez partnerek számára](../costs/get-started-partners.md)