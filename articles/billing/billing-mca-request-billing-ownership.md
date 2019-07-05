---
title: Azure-előfizetés tulajdonjogának beolvasása számlázás
description: Útmutató az Azure-előfizetések számlázási tulajdonjogának kérhet más felhasználókkal.
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
ms.openlocfilehash: 4c9a8c866a157757121e6a9dd07a0a8559937c5e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490838"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Első számlázási tulajdonosi jogokat, más fiókok az Azure-előfizetések

Ha a meglévő számlázási tulajdonosi elhagyja a szervezet, vagy az Ön számlázási fiókjához fizethet fizetni szeretne Azure-előfizetés tulajdonjogának átvétele érdemes. A fiók-előfizetések számlázási felelőssége saját tulajdonba továbbítja.

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-for-access).

A számlázási tulajdonjog igénylése, rendelkeznie kell egy **számla szakasz tulajdonosa** vagy **számla szakasz közreműködői**. További tudnivalókért lásd: [szakasz szerepkörök feladatok számlázása](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>A számlázási tulajdonosi kérése

1. Jelentkezzen be a [az Azure portal](http://portal.azure.com) számla szakasz tulajdonosa vagy a Microsoft vevői szerződés számlázási fiók.

2. Keresse meg **Cost Management és számlázás**.

   ![Költségkezelés + számlázás az Azure portál keresési bemutató képernyőkép](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Válassza ki **szakaszok számlázása** bal oldalán. A hozzáférést, attól függően szükség lehet jelöljön ki egy számlázási fiók vagy a számlázási profilja. Válassza ki a számlázási fiók vagy profil **szakaszok számlázása**.
   
   ![Képernyőkép a kiválasztásával szakaszok számlázása](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Válasszon ki egy számla szakasz a listából. Az előfizetés tulajdonjogának átvétele, miután azok díját számla ebben a szakaszban.

5. Válassza ki **irányuló kérelmek** a bal alsó oldalán, és válassza ki a **Hozzáadás**.
 
   ![Képernyőfelvétel bemutatja adatátviteli kérések kiválasztása](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Adja meg a számlázási tulajdonosi jogokat a kért felhasználó e-mail-címét. A felhasználó fiókja rendszergazda a Microsoft Online Service Program számlázási fiók vagy a nagyvállalati szerződésben a fiók tulajdonosa kell lennie. További információkért lásd: [a számlázási fiókok megtekintése az Azure Portalon](billing-view-all-accounts.md). Válassza ki **küldési átadására irányuló kérelmet**.

   ![Képernyőkép a átadási kérelem elküldése](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. A felhasználó kap egy e-mailt, tekintse át az átadási kérelem utasításokat tartalmazó.

   ![Képernyőkép, hogy mutat be tekintse át az átviteli kérelem e-mail](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. Az átadási kérelem jóváhagyása, a felhasználó kiválasztja a hivatkozásra az e-mailben, és utasításokat.

    ![Képernyőkép, hogy mutat be tekintse át az átviteli kérelem e-mail](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Az átadási kérelem állapotának ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg **Cost Management és számlázás**.

   ![Költségkezelés + számlázás az Azure portál keresési bemutató képernyőkép](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Válassza ki **szakaszok számlázása** bal oldalán. A hozzáférést, attól függően szükség lehet jelöljön ki egy számlázási fiók vagy a számlázási profilja. Válassza ki a számlázási fiók vagy profil **szakaszok számlázása**.
   
   ![Képernyőkép a kiválasztásával szakaszok számlázása](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Válassza ki a számlát a szakasz a listából, amelynek az átadási kérelem elküldve.

5. Válassza ki **irányuló kérelmek** bal alsó oldalán. Az átvitel kérelmeket tartalmazó lapot az alábbi információkat jeleníti meg:

    ![Adatátviteli kérések listáját bemutató képernyőkép](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Oszlop|Meghatározás|
   |---------|---------|
   |Kérelem dátuma|Ha az átviteli kérelem elküldésének dátuma|
   |Címzett|A felhasználót, hogy a számlázási tulajdonosi kérelmet küldött e-mail-címe|
   |Lejárat dátuma|A dátum, amikor a kérelem lejár|
   |Állapot|Az átadási kérelem állapota|

    Az átadási kérelem a következő állapotok egyike lehet:

   |Állapot|Meghatározás|
   |---------|---------|
   |Folyamatban van|A felhasználó még nem fogadja az átviteli kérelem|
   |Feldolgozás|A felhasználó az átviteli kérelem jóváhagyva. A felhasználó által kiválasztott előfizetések számlázási első átkerülnek a számla szakasz|
   |Befejeződött| A számlázás a felhasználó által kiválasztott előfizetések átkerül a számla szakasz|
   |Befejeződött hibákkal|A vonatkozó kérelem befejeződött, de az egyes előfizetések, a felhasználó által kiválasztott számlázási nem helyezhető át.|
   |Elévült|A felhasználó nem fogadja el a kérelmet az idő és lejárt|
   |Megszakítva|Valaki hozzáférő az átviteli kérelem a kérelem meg lett szakítva|
   |Elutasított|A felhasználó elutasította az átviteli kérelem|

7. Válassza ki a átviteli kérést, a részletek megtekintéséhez. Az átviteli részleteit megjelenítő oldalon az alábbi információkat jeleníti meg:
   
   ![Átvitt előfizetések listáját bemutató képernyőkép](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Oszlop  |Meghatározás|
   |---------|---------|
   |Átadási kérelem azonosítója|Az átadási kérelem egyedi azonosítója. Küldjön egy támogatási kérést, ha az azonosító megoszthatja a támogatási kérelem gyorsíthatja fel az Azure-támogatás|
   |Az átadási kérelem időpontja|Ha az átviteli kérelem elküldésének dátuma|
   |Az átadási kérelem szerint|Az átviteli kérelmet küldő felhasználó e-mail-címe|
   |Az átadási kérelem lejárata| A dátum, amikor az átadási kérelem lejárata|
   |Címzett e-mail-címe|A felhasználót, hogy a számlázási tulajdonosi kérelmet küldött e-mail-címe|
   |Címzett a rendszer elküldte a hivatkozást|A felhasználó, tekintse át az átviteli kérelmet küldött az URL-cím|

## <a name="supported-subscription-types"></a>Támogatott előfizetéstípusok

Az alábbiakban a számlázási tulajdonosi jogait a előfizetéstípusok igényelhet.

- [A művelet csomag](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure in Open licencprogram](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass-szponzorálás](https://azure.microsoft.com/offers/azure-pass/)\*
- [Nagyvállalati fejlesztés és tesztelés](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Ingyenes próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [A Microsoft Azure-csomagot](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Támogatott Microsoft Azure-ajánlat](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft nagyvállalati szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [A Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN-platformok](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [A Visual Studio Enterprise (BizSpark) előfizetők](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [A Visual Studio Enterprise (MPN) előfizetők](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [A Visual Studio Enterprise-előfizetők](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [A Visual Studio Test Professional-előfizetők](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Elérhető az előfizetés kreditegyenlege nem lesz elérhető az új fiókot az átadást követően.

\*\* Csak a támogatott során létrehozott előfizetések az Azure-webhelyen regisztrálhat.


## <a name="additional-information"></a>További információ

A következő szakasz az előfizetések átvitele további információkat tartalmaz.

### <a name="no-service-downtime"></a>A szolgáltatás leállása nélkül

Az előfizetés Azure-szolgáltatások folyamatosan futó megszakítás nélkül. A Microsoft csak a számlázást, a felhasználó által átvitele Azure-előfizetések átmenet.

### <a name="disabled-subscriptions"></a>Letiltott előfizetés

Letiltott előfizetésekbe nem helyezhető át. Az előfizetések a számlázási tulajdonosi aktív állapotban kell lennie.

### <a name="azure-resources-transfer"></a>Azure-erőforrások átvitel

Az előfizetések közül az összes erőforrások, például a virtuális gépek, a lemezek és a websites adatforgalom.

### <a name="azure-marketplace-products-transfer"></a>Az Azure Marketplace-en termékek átvitel

Azure Marketplace-termékekkel és a megfelelő előfizetések átvitele.

### <a name="azure-reservations-transfer"></a>Az Azure foglalások átvitel

Azure foglalások automatikusan előfizetések nem változik. [Lépjen kapcsolatba az Azure-támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) foglalások áthelyezni.

### <a name="access-to-azure-services"></a>Az Azure-szolgáltatásokhoz való hozzáférés

Hozzáférés a meglévő felhasználók, csoportok vagy az egyszerű szolgáltatások használatával (az Azure RBAC (szerepköralapú hozzáférés-vezérlés)) hozzárendelt [... /Role-based-Access-Control/overview.md] nincs hatással a váltás során.

### <a name="azure-support-plan"></a>Azure támogatási csomagot

Az Azure-támogatás az előfizetés nem vesz át. Ha a felhasználói adatforgalom az összes Azure-előfizetéssel, kérje a támogatási csomag lemondása.

### <a name="charges-for-transferred-subscription"></a>Átvitt előfizetési díjai

Az előfizetés számlázási eredeti tulajdonosa felelős, amely a jelzett addig a pontig, hogy befejeződött-e az adatátviteli díjakat. Jelentett idő adatátviteli és újabb verziók esetében a díjak a számla szakasz feladata. Előfordulhat, hogy néhány díjak került sor átvitele előtt, de ezt követően lett jelentve. Ezek a díjak a számla szakaszban jelennek meg.

### <a name="cancel-a-transfer-request"></a>Egy átviteli kérelem visszavonása

Az átadási kérelem vonhatja vissza addig, amíg a kérelem jóváhagyása vagy próbálkozásai sikertelenek lesznek. Az átadási kérelem megszüntetéséhez nyissa meg a [átviteli részleteit megjelenítő oldalon](#check-the-transfer-request-status) és válassza a lap alján.

### <a name="software-as-a-service-saas-transfer"></a>A szoftverszolgáltatások (SaaS) átvitel szoftver

SaaS-termékek az előfizetés átadása nem történik. Kérje meg a felhasználót, hogy [forduljon az Azure-támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) számlázási tulajdonjogának SaaS-termékek. Együtt a számlázási tulajdonosi jogokat a felhasználó is átadhatja tulajdonjogát. Erőforrás-tulajdonosi lehetővé teszi a felügyeleti műveleteket, például a Törlés és a termék részleteinek megtekintése. Felhasználói erőforrás tulajdonjogának a Szolgáltatottszoftver-termék az erőforrás tulajdonosának kell lennie.

## <a name="check-for-access"></a>Hozzáférés-ellenőrzés
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha segítségre van szüksége, kell [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- Az Azure-előfizetések számlázási tulajdonjogának kerül a számlán szakaszban. Nyomon követheti, ezek a szolgáltatások díjai a [az Azure portal](https://portal.azure.com).
- Engedélyek megtekintése és kezelése a számlázási ezekért az előfizetésekért biztosítása másokkal. További információkért lásd: [szakasz szerepkörök és feladatok számla](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
