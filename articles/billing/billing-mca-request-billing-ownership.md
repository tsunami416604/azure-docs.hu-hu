---
title: Azure-előfizetés tulajdonjogának beolvasása számlázási |} A Microsoft Docs
description: Útmutató az Azure-előfizetések számlázási tulajdonjogának kérhet más felhasználókkal.
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
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 01d34ef722034ef5b56c842bab97a3415a02b0bb
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341867"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-users"></a>Első számlázási tulajdonosi jogokat, más felhasználók az Azure-előfizetések

Ha a meglévő számlázási tulajdonosi elhagyja a szervezet, vagy az Ön számlázási fiókjához fizethet fizetni szeretne saját tulajdonba vétel Azure-előfizetések érdemes.

Saját tulajdonba vétel Azure-előfizetést a meglévő tulajdonosai a többi számlázási fiókok kérést küldhet. A számla szakasz-előfizetések számlázási felelőssége saját tulajdonba továbbítja.

A számlázási tulajdonjog igénylése, rendelkeznie kell egy **számla szakasz tulajdonosa** vagy **számla szakasz közreműködői**. További tudnivalókért lásd: [szakasz szerepkörök feladatok számlázása](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access-to-a-microsoft-customer-agreement).

## <a name="request-billing-ownership-in-the-azure-portal"></a>Az Azure Portal számlázási tulajdonjogának igénylése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Keresés **Cost Management és számlázás**.

   ![Költségkezelés + számlázás az Azure portál keresési bemutató képernyőkép](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Nyissa meg a számlán szakaszban. A hozzáférést, attól függően szükség lehet jelöljön ki egy számlázási fiók vagy a számlázási profilja. A számlázási fiók vagy a profil, válassza ki a **szakaszok számlázása** és majd egy számla szakaszban.
   <!-- Todo - Add a screenshot -->

4. Válassza ki **irányuló kérelmek** bal alsó oldalán.

5. A lap tetején válassza **Hozzáadás**.

6. Adja meg a számlázási tulajdonosi jogokat a kért felhasználó e-mail-címét. A felhasználó fiókja rendszergazda a Microsoft Online Service Program számlázási fiók vagy a nagyvállalati szerződésben a fiók tulajdonosa kell lennie. További információkért lásd: [a számlázási fiókok megtekintése az Azure Portalon](billing-view-all-accounts.md).

   ![Képernyőkép egy új átadási kérelem hozzáadása](./media/billing-mca-request-billing-ownership/mca-new-transfer-request.png)

7. Válassza ki **küldési átadására irányuló kérelmet**.

8. A felhasználó kap egy e-mailt, tekintse át az átadási kérelem utasításokat tartalmazó.

   ![Képernyőkép, hogy mutat be tekintse át az átviteli kérelem e-mail](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

9. Az átadási kérelem jóváhagyása, a felhasználó kiválasztja a hivatkozásra az e-mailben, és utasításokat.

    ![Képernyőkép, hogy mutat be tekintse át az átviteli kérelem e-mail](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-status-of-your-transfer-request-in-the-azure-portal"></a>Az Azure Portalon az átadási kérelem állapotának ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Keresés **Cost Management és számlázás**.

   ![Költségkezelés + számlázás az Azure portál keresési bemutató képernyőkép](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Nyissa meg a számlán szakaszban. A hozzáférést, attól függően szükség lehet jelöljön ki egy számlázási fiók vagy a számlázási profilja. A számlázási fiók vagy a profil, válassza ki a **szakaszok számlázása** és majd egy számla szakaszban.
   <!-- Todo - Add a screenshot -->

4. Válassza ki **irányuló kérelmek** bal alsó oldalán.

5. Az átvitel kérelmeket tartalmazó lapot az alábbi információkat jeleníti meg:

    ![Adatátviteli kérések listáját bemutató képernyőkép](./media/billing-mca-request-billing-ownership/mca-view-transfer-requests.png)

   |Oszlop|Meghatározás|
   |---------|---------|
   |Kérelem dátuma|Ha az átviteli kérelem elküldésének dátuma|
   |Címzett|A felhasználót, hogy a számlázási tulajdonosi kérelmet küldött e-mail-címe|
   |Lejárati dátum|A dátum, amikor a kérelem lejár|
   |status|Az átadási kérelem állapota|

    Az átadási kérelem a következő állapotok egyike lehet:

   |status|Meghatározás|
   |---------|---------|
   |Folyamatban|A felhasználó még nem fogadja az átviteli kérelem|
   |Feldolgozás|A felhasználó az átviteli kérelem jóváhagyva. A felhasználó által kiválasztott előfizetések számlázási első átkerülnek a számla szakasz|
   |Befejezve| A számlázás a felhasználó által kiválasztott előfizetések átkerül a számla szakasz|
   |Befejeződött hibákkal|A vonatkozó kérelem befejeződött, de az egyes előfizetések, a felhasználó által kiválasztott számlázási nem helyezhető át.|
   |Elévült|A felhasználó nem fogadja el a kérelmet az idő és lejárt|
   |Megszakítva|Valaki hozzáférő az átviteli kérelem a kérelem meg lett szakítva|
   |Elutasítva|A felhasználó elutasította az átviteli kérelem|

6. Válassza ki a átviteli kérést, a részletek megtekintéséhez. Az átviteli részleteit megjelenítő oldalon az alábbi információkat jeleníti meg: <!-- Todo - Add a screenshot -->

   |Oszlop  |Meghatározás|
   |---------|---------|
   |Átadási kérelem azonosítója|Az átadási kérelem egyedi azonosítója. Küldjön egy támogatási kérést, ha az azonosító megoszthatja a támogatási kérelem gyorsíthatja fel az Azure-támogatás|
   |Az átadási kérelem időpontja|Ha az átviteli kérelem elküldésének dátuma|
   |Az átadási kérelem szerint|Az átviteli kérelmet küldő felhasználó e-mail-címe|
   |Az átadási kérelem lejárata| A dátum, amikor az átadási kérelem lejárata|
   |Címzett e-mail-címe|A felhasználót, hogy a számlázási tulajdonosi kérelmet küldött e-mail-címe|
   |Címzett a rendszer elküldte a hivatkozást|A felhasználó, tekintse át az átviteli kérelmet küldött az URL-cím|

## <a name="additional-information"></a>További információ

A következő szakasz az előfizetések átvitele további információkat tartalmaz.

### <a name="no-service-downtime"></a>A szolgáltatás leállása nélkül

Az előfizetés Azure-szolgáltatások folyamatosan futó megszakítás nélkül. A Microsoft csak a számlázást, a felhasználó által átvitele Azure-előfizetések átmenet.

### <a name="disabled-subscriptions"></a>Letiltott előfizetések

Letiltott előfizetésekbe nem helyezhető át. Az előfizetések a számlázási tulajdonosi aktív állapotban kell lennie.

### <a name="azure-resources-transfer"></a>Azure-erőforrások átvitel

Az előfizetések közül az összes erőforrások, például a virtuális gépek, a lemezek és a websites adatforgalom.

### <a name="azure-marketplace-products-transfer"></a>Az Azure Marketplace-en termékek átvitel

Azure Marketplace-termékekkel és a megfelelő előfizetések átvitele.

### <a name="azure-reservations-transfer"></a>Az Azure foglalások átvitel

Azure foglalások automatikusan előfizetések nem változik. [Lépjen kapcsolatba az Azure-támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) foglalások áthelyezni.

### <a name="access-to-azure-services"></a>Az Azure-szolgáltatásokhoz való hozzáférés

Az áttérés során nincs hatással az Azure-erőforrásokhoz való hozzáférést, amelyet az Azure RBAC (szerepköralapú hozzáférés-vezérlés) használatával.

### <a name="azure-support-plan"></a>Azure támogatási csomagot

Az Azure-támogatás az előfizetés nem vesz át. Ha a felhasználói adatforgalom az összes Azure-előfizetéssel, kérje a támogatási csomag lemondása.

### <a name="charges-for-transferred-subscription"></a>Átvitt előfizetési díjai

Az előfizetés számlázási eredeti tulajdonosa felelős, amely a jelzett addig a pontig, hogy befejeződött-e az adatátviteli díjakat. Jelentett idő adatátviteli és újabb verziók esetében a díjak a számla szakasz feladata. Előfordulhat, hogy néhány díjak került sor átvitele előtt, de ezt követően lett jelentve. Ezek a díjak a számla szakaszban jelennek meg.

### <a name="supported-offers"></a>Támogatott ajánlatok

Előfizetés típusa és ajánlatokkal, kivéve CSP kínál továbbíthatók.

### <a name="cancel-a-transfer-request"></a>Egy átviteli kérelem visszavonása

Az átadási kérelem vonhatja vissza addig, amíg a kérelem jóváhagyása vagy próbálkozásai sikertelenek lesznek. Az átviteli kérelem megszakítása [nyissa meg az átvitel részleteit megjelenítő oldalra](#check-status-of-your-transfer-request-in-the-Azure-portal) és válassza a lap alján.

### <a name="software-as-a-service-saas-transfer"></a>A szoftverszolgáltatások (SaaS) átvitel szoftver

SaaS-termékek az előfizetés átadása nem történik. Kérje meg a felhasználót, hogy [forduljon az Azure-támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) számlázási tulajdonjogának SaaS-termékek. Együtt a számlázási tulajdonosi jogokat a felhasználó is átadhatja tulajdonjogát. Erőforrás-tulajdonosi lehetővé teszi a felügyeleti műveleteket, például a Törlés és a termék részleteinek megtekintése. Felhasználói erőforrás tulajdonjogának a Szolgáltatottszoftver-termék az erőforrás tulajdonosának kell lennie.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, kell [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- Az Azure-előfizetések számlázási tulajdonjogának kerül a számlán szakaszban. Nyomon követheti, ezek a szolgáltatások díjai a [az Azure portal](https://portal.azure.com).
- Engedélyek megtekintése és kezelése a számlázási ezekért az előfizetésekért biztosítása másokkal. További információkért lásd: [szakasz szerepkörök és feladatok számla](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).