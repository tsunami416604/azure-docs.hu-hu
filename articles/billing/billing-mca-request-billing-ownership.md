---
title: Azure-előfizetések számlázási tulajdonjogának beolvasása
description: Megtudhatja, hogyan kérheti az Azure-előfizetések számlázási tulajdonjogát más felhasználóktól.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 110bd975de1d865c2aa3d3b088c98cdc8b9e45ad
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019563"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Más fiókoktól származó Azure-előfizetések számlázási tulajdonjogának beolvasása

Előfordulhat, hogy az Azure-előfizetések tulajdonjogát szeretné átvenni, ha a meglévő számlázási tulajdonos elhagyja a szervezetet, vagy az előfizetéseket a számlázási fiókján keresztül szeretné fizetni. A tulajdonjog átadásával a fiókra vonatkozó előfizetések számlázási feladatai vannak.

Ez a cikk egy Microsoft-ügyfél szerződéshez tartozó számlázási fiókra vonatkozik. [Ellenőrizze, hogy van-e hozzáférése Microsoft-ügyfél szerződéshez](#check-for-access).

A számlázási tulajdonjog igényléséhez a **számla szakasz tulajdonosának** vagy a **számla szakaszának közreműködőnek**kell lennie. További információ: a [számla szakasz szerepköreinek feladatai](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Számlázási tulajdonos igénylése

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) számla szakaszának tulajdonosaként vagy közreműködőként a Microsoft Customer szerződéshez tartozó számlázási fiókhoz.

2. Keressen rá **Cost Management + számlázásra**.

   ![A Cost Management + számlázás Azure Portal keresését bemutató képernyőkép](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Válassza ki a **számla szakaszt** a bal oldali oldalon. A hozzáféréstől függően előfordulhat, hogy ki kell választania egy számlázási fiókot vagy számlázási profilt. A Számlázási fiók vagy profil területen válassza a **Számlázási csoportok**lehetőséget.
   
   ![A számla fejezeteinek kijelölését bemutató képernyőkép](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Válasszon ki egy számla szakaszt a listából. Az előfizetések tulajdonjogának átvétele után a számlázás a számla szakaszba kerül.

5. Válassza az **átvitel kérések** lehetőséget a bal alsó részen, majd válassza a **Hozzáadás**lehetőséget.
 
   ![Az adatátviteli kérelmek kiválasztását bemutató képernyőkép](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Adja meg annak a felhasználónak az e-mail-címét, akinek a számlázási tulajdonjogát kéri. A felhasználónak rendszergazdai fiókkal kell rendelkeznie a Microsoft Online Services program számlázási fiókjához, vagy egy Nagyvállalati Szerződés fiók tulajdonosa. További információ: [a számlázási fiókok megtekintése Azure Portalban](billing-view-all-accounts.md). Válassza az átadási **kérelem küldése**lehetőséget.

   ![Az átadási kérelem küldését bemutató képernyőkép](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. A felhasználó egy e-mailt kap, amely útmutatást nyújt az adatátviteli kérelem áttekintéséhez.

   ![A felülvizsgálati kérelem e-mail-címét bemutató képernyőkép](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. Az adatátviteli kérelem jóváhagyásához a felhasználó kiválasztja az e-mailben szereplő hivatkozást, és követi az utasításokat.

    ![A felülvizsgálati kérelem e-mail-címét bemutató képernyőkép](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Az átadási kérelem állapotának keresése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá **Cost Management + számlázásra**.

   ![A Cost Management + számlázás Azure Portal keresését bemutató képernyőkép](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Válassza ki a **számla szakaszt** a bal oldali oldalon. A hozzáféréstől függően előfordulhat, hogy ki kell választania egy számlázási fiókot vagy számlázási profilt. A Számlázási fiók vagy profil területen válassza a **Számlázási csoportok**lehetőséget.
   
   ![A számla fejezeteinek kijelölését bemutató képernyőkép](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Válassza ki a számla szakaszt abból a listából, amelyhez az átadási kérelmet elküldte.

5. Válassza ki az átadási kérelmeket a bal alsó oldalon. Az adatátviteli kérelmek lap a következő információkat jeleníti meg:

    ![Az adatátviteli kérelmek listáját megjelenítő képernyőkép](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Oszlop|Meghatározás|
   |---------|---------|
   |Kérelem dátuma|Az átadási kérelem elküldésének dátuma|
   |Címzett|Annak a felhasználónak az e-mail-címe, akinek a számlázási tulajdonjogának átadására irányuló kérelmet elküldte|
   |Lejárati dátum|A kérelem lejáratának dátuma|
   |Állapot|Az adatátviteli kérelem állapota|

    Az átviteli kérelem a következő állapotok egyikével rendelkezhet:

   |Állapot|Meghatározás|
   |---------|---------|
   |Folyamatban|A felhasználó nem fogadta el az áthelyezési kérelmet|
   |Feldolgozás|A felhasználó jóváhagyta az adatátviteli kérést. A felhasználó által kiválasztott előfizetések számlázása a számla szakaszba kerül|
   |Befejeződött| A felhasználó által kiválasztott előfizetések számlázása a számla szakaszba kerül|
   |Hibákkal fejeződött be|A kérelem befejeződött, de a felhasználó által kiválasztott előfizetések számlázása nem sikerült|
   |Elévült|A felhasználó nem fogadta el időben a kérést, és lejárt|
   |Megszakítva|Az adatátviteli kérelemhez hozzáféréssel rendelkező valaki megszakította a kérelmet|
   |Elutasítva|A felhasználó elutasította az áthelyezési kérelmet|

7. Válasszon egy átadási kérelmet a részletek megtekintéséhez. Az átvitel részletei oldalon az alábbi információk jelennek meg:
   
   ![Az átvitt előfizetések listáját megjelenítő képernyőkép](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Oszlop  |Meghatározás|
   |---------|---------|
   |Adatátviteli kérelem azonosítója|Az adatátviteli kérelem egyedi azonosítója. Ha támogatási kérelmet küld, ossza meg az azonosítót az Azure-támogatással a támogatási kérés felgyorsításához|
   |Átvitel kérelmezve|Az átadási kérelem elküldésének dátuma|
   |A kért átvitel|Az átadási kérelmet küldő felhasználó e-mail-címe|
   |Az áthelyezési kérelem lejár| Az átadási kérelem lejáratának dátuma|
   |Címzett e-mail-címe|Annak a felhasználónak az e-mail-címe, akinek a számlázási tulajdonjogának átadására irányuló kérelmet elküldte|
   |Átvitt hivatkozás küldése a címzettnek|Az átadási kérelem áttekintésére a felhasználónak elküldött URL-cím|

## <a name="supported-subscription-types"></a>Támogatott előfizetési típusok

Az alább felsorolt előfizetési típusok számlázási tulajdonjogát kérheti le.

- [Action Pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure in Open-licencelés](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass szponzorálás](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise dev/test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Ingyenes próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Utólagos elszámolású fejlesztési/tesztelési funkciók](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure terv](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure szponzorált ajánlat](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft nagyvállalati szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN platformok](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise-(BizSpark-) előfizetők](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [A Visual Studio Enterprise (MPN) előfizetők](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise-előfizetők](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio test Professional-előfizetők](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\*Az előfizetés után az előfizetésben elérhető kreditek az átvitel után nem lesznek elérhetők az új fiókban.

\*\*Csak az Azure webhelyre való regisztráció során létrehozott fiókokban támogatott.


## <a name="additional-information"></a>További információ

A következő szakasz további információkat tartalmaz az előfizetések átviteléről.

### <a name="no-service-downtime"></a>Nincs szolgáltatás leállása

Az előfizetésben található Azure-szolgáltatások megszakítás nélkül futnak. Csak a felhasználó által az átvitelre kiválasztott Azure-előfizetések számlázási kapcsolatát fogjuk áttérni.

### <a name="disabled-subscriptions"></a>Letiltott előfizetések

A letiltott előfizetések nem vihetők át. Az előfizetésnek aktív állapotban kell lennie, hogy átvigye a számlázási tulajdonjogát.

### <a name="azure-resources-transfer"></a>Azure-erőforrások átvitele

Az előfizetések, például a virtuális gépek, a lemezek és a webhelyek átvitelének összes erőforrása.

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace-termékek átvitele

Az Azure Marketplace-termékek a megfelelő előfizetésekkel együtt továbbítanak.

### <a name="azure-reservations-transfer"></a>Azure Reservations átvitel

Azure Reservations a rendszer nem helyezi át automatikusan az előfizetéseket. A foglalások áthelyezéséhez [forduljon az Azure támogatási szolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

### <a name="access-to-azure-services"></a>Hozzáférés az Azure-szolgáltatásokhoz

Hozzáférés meglévő felhasználókhoz, csoportokhoz vagy egyszerű szolgáltatásokhoz (Azure RBAC (szerepköralapú hozzáférés-vezérlés)) [.. a/role-based-Access-Control/Overview.MD] nem érinti az áttérést.

### <a name="azure-support-plan"></a>Azure támogatási csomagot

Az Azure-támogatás nem továbbítja az előfizetéseket. Ha a felhasználó az összes Azure-előfizetést átviszi, kérje meg őket, hogy szakítsák meg a támogatási csomagot.

### <a name="charges-for-transferred-subscription"></a>Az átvitt előfizetés díjai

Az előfizetések eredeti számlázási tulajdonosa felelős minden olyan költségért, amelyet az átvitel befejezésének időpontjáig jelentettek. A számla szakasz felelős az átvitel időpontjában jelentett díjakért. Előfordulhat, hogy az átvitel előtt már történtek díjak, de ezt követően jelent meg. Ezek a díjak a számla szakaszban jelennek meg.

### <a name="cancel-a-transfer-request"></a>Adatátviteli kérelem megszakítása

A kérelem jóváhagyása vagy elutasítása után megszakíthatja az adatátviteli kérést. Az átadási kérelem megszakításához lépjen az [átvitel adatai lapra](#check-the-transfer-request-status) , és válassza a Mégse lehetőséget az oldal alján.

### <a name="software-as-a-service-saas-transfer"></a>Szolgáltatott szoftver (SaaS) átvitele

Az SaaS-termékek nem vihetők át az előfizetésekre. Kérje meg a felhasználót, hogy [lépjen kapcsolatba az Azure támogatási szolgálatával](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az SaaS-termékek számlázási tulajdonjogának átadásához. A számlázási tulajdonossal együtt a felhasználó is átviheti az erőforrás-tulajdonjogot. Az erőforrás-tulajdonos lehetővé teszi a kezelési műveletek végrehajtását, például a termék adatainak törlését és megtekintését. A felhasználónak az SaaS-termék erőforrás-tulajdonosának kell lennie az erőforrás tulajdonjogának átadásához.

## <a name="check-for-access"></a>Hozzáférés keresése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha segítségre van szüksége, [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- Az Azure-előfizetések számlázási tulajdonjoga a számla szakaszba kerül. Kövesse nyomon az előfizetések díját a Azure Portalban [](https://portal.azure.com).
- Adja meg másoknak az előfizetések számlázásának megtekintését és kezelését. További információ: a [számla szakasz szerepkörei és feladatai](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
