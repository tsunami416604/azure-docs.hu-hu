---
title: Azure-előfizetés számlázási tulajdonjogának beszerzése
description: Megtudhatja, hogyan kérhet az Azure-előfizetésekre vonatkozó számlázási tulajdonjogot a többi felhasználótól.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 10f1052f9acf9bf91c1d7fb0b64a1d3285487cf3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200727"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Azure-előfizetések számlázási tulajdonjogának beszerzése más fiókoktól

Az Azure-előfizetések számlázási tulajdonjogának átvételére akkor lehet szükség, ha a meglévő számlázási tulajdonos elhagyja a szervezetet, vagy ha a saját számlázási fiókján keresztül szeretné fizetni az előfizetéseket. A tulajdonjog átadásával az Ön fiókjára hárul az előfizetések számlázásának felelőssége.

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó számlázási fiókokra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel a Microsoft-ügyfélszerződéshez](#check-for-access).

Számlázási tulajdonjogot csak a **számlaszakaszok tulajdonosai** és a **számlaszakaszok közreműködői** igényelhetnek. További tudnivalókért lásd a [számlaszakaszokra vonatkozó szerepkörök feladatait](understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Számlázási tulajdonjog kérése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy Microsoft-ügyfélszerződéses számlázási fiók számlaszakaszának tulajdonosaként vagy közreműködőjeként.

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![Képernyőkép az Azure Portal keresőmezőjéről a költségkezelés + számlázás keresőkifejezéssel](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. A számlázási hatókörök lapon válassza ki azt a számlázási fiókot, amellyel fizetni kívánja az előfizetés használatát. A számlázási fióknak **Microsoft-ügyfélszerződés** típusú lesz.

    ![Képernyőkép a „költségkezelés + számlázás” kifejezés portálon történő kereséséről](./media/mca-request-billing-ownership/list-of-scopes.png)

    > [!NOTE]
    >
    > Az Azure Portal megjegyzi az utoljára használt számlázási hatókört, és megjeleníti a Költségkezelés + Számlázás lap következő megnyitásakor. A számlázási hatókörök lap nem jelenik meg, ha már korábban meglátogatta a Költségkezelés + Számlázás lapot. Ebben az esetben ellenőrizze, hogy a [megfelelő hatókörben](#check-for-access) van-e. Ha nem, [váltson hatókört](view-all-accounts.md#switch-billing-scope-in-the-azure-portal), és válassza ki a Microsoft-ügyfélszerződéshez kapcsolódó számlázási fiókot.

4. Válassza a **Számlázási profilok** lehetőséget a bal oldalon.

    ![Képernyőkép a számlázási profilok kiválasztásáról](./media/mca-request-billing-ownership/mca-select-profiles.png)     

    > [!Note]
    >
    > Ha nem látja a Számlázási profilokat, nem a megfelelő számlázási hatókört használja. Először ki kell választania egy Microsoft-ügyfélszerződéshez tartozó számlázási fiókot, majd a Számlázási profilokat. További információ a hatókörök módosításáról: [A számlázás hatókörének módosítása az Azure Portalon](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. Válasszon ki egy **Számlázási profilt** a listából. Az előfizetések használata a tulajdonjog átvétele után ebben a számlázási profilban lesz számlázva.

6. A bal oldalon válassza a **Számlaszakaszok** lehetőséget.

    ![Képernyőkép a számlázási szakaszok kiválasztásáról](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)   

7. Válasszon egy számlaszakaszt a listából. Az előfizetések használata a tulajdonjog átvétele után a számlázási profil számlájának ehhez a szakaszához lesz hozzárendelve.

8. Válassza a bal alsó részen az **Átadási kérelmek** lehetőséget, majd az **Új kérelem hozzáadása** elemet.

    ![Képernyőkép az átadási kérelmek kiválasztásáról](./media/mca-request-billing-ownership/mca-select-transfer-requests.png)

9. Adja meg annak a felhasználónak az e-mail-címét, akitől a számlázási tulajdonjogot kéri. A felhasználónak egy Microsoft Online Services Program számlázási fiókjának adminisztrátorának vagy egy Nagyvállalati Szerződéssel rendelkező fiók tulajdonosának kell lennie. További információk: [A számlázási fiókok megjelenítése az Azure Portalon](view-all-accounts.md). Válassza az **Átadási kérelem küldése** lehetőséget.

    ![Képernyőkép egy átadási kérelem küldéséről](./media/mca-request-billing-ownership/mca-send-transfer-requests.png)

10. A felhasználó egy e-mailt kap, amely felszólítja az átadási kérelem áttekintéséra.

    ![Képernyőkép egy átadási kérelem áttekintésére felszólító e-mailről](./media/mca-request-billing-ownership/mca-review-transfer-request-email.png)

11. Az átadási kérelem jóváhagyásához a felhasználó kiválasztja az e-mailben szereplő hivatkozást, és követi az útmutatást.

    ![Képernyőkép egy átadási kérelem áttekintésére felszólító e-mailről](./media/mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Az átadási kérelem állapotának ellenőrzése

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![Képernyőkép az Azure Portal keresőmezőjéről a költségkezelés + számlázás keresőkifejezéssel](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. A számlázási hatókörök lapon válassza ki azt a számlázási fiókot, amelynek az átadási kérelmet küldte.

4. Válassza a **Számlázási profilok** lehetőséget a bal oldalon.

    ![Képernyőkép a számlázási profilok kiválasztásáról](./media/mca-request-billing-ownership/mca-select-profiles.png)     

5. Válassza ki azt a **Számlázási profilt**, amelynek az átadási kérelmet küldte.

6. A bal oldalon válassza a **Számlaszakaszok** lehetőséget.

    ![Képernyőkép a számlázási szakaszok kiválasztásáról](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)   

7. Válassza ki a listából a számlaszakaszt, amelyre az elküldött átadási kérelem vonatkozik.

8. Válassza a bal alsó részen az **Átadási kérelmek** lehetőséget. Az Átadási kérelmek lapon az alábbi információk jelennek meg:

    ![Képernyőkép az átadási kérelmek listájáról](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Oszlop|Meghatározás|
   |---------|---------|
   |Kérelem dátuma|Az átadási kérelem elküldésének dátuma|
   |Címzett|A felhasználó e-mail-címe, ahová a számlázási tulajdonjog átadásának kérése el lett küldve|
   |Lejárati dátum|A kérelem lejáratának dátuma|
   |status|Az átadási kérelem állapota|

    Az átadási kérelem állapota a következők egyike lehet:

   |status|Meghatározás|
   |---------|---------|
   |Folyamatban|A felhasználó nem fogadta el az átadási kérelmet|
   |Feldolgozás|A felhasználó jóváhagyta az átadási kérelmet. A felhasználó által kiválasztott előfizetések számlázása átkerül az Ön számlaszakaszába|
   |Befejezve| A felhasználó által kiválasztott előfizetések számlázása átkerült az Ön számlaszakaszába|
   |Befejeződött, hibákkal|A kérelem teljesítve lett, de néhány, a felhasználó által kiválasztott előfizetés átadási nem sikerült|
   |Lejárt|A felhasználó nem fogadta el időben a kérelmet, így a kérelem lejárt|
   |Megszakítva|Valaki, akinek az átadási kérelemhez hozzáférése volt, megszakította a kérelmet|
   |Elutasítva|A felhasználó elutasította az átadási kérelmet|

9. Válasszon ki egy átadási kérelmet a részletei megtekintéséhez. Az átadási részleteinek lapján az alábbi információk jelennek meg:

    ![Képernyőkép az átadott kérelmek listájáról](./media/mca-request-billing-ownership/mca-transfer-completed.png)

   |Oszlop  |Meghatározás|
   |---------|---------|
   |Átadási kérelem azonosítója|Az átadási kérelem egyedi azonosítója. Ha támogatási kérést nyújt be, ossza meg az azonosítót az Azure-támogatással, hogy a támogatási kérésre gyorsabban kapjon választ|
   |Átadási kérelem dátuma|Az átadási kérelem elküldésének dátuma|
   |Átadási kérelem feladója|Az átadási kérelmet elküldő felhasználó e-mail-címe|
   |Átadási kérelem lejárata| Az átadási kérelem lejáratának dátuma|
   |Címzett e-mail-címe|A felhasználó e-mail-címe, ahová a számlázási tulajdonjog átadásának kérése el lett küldve|
   |A címzettnek elküldött átadási hivatkozás|A felhasználónak küldött URL, hogy tekintse át az átadási kérelmet|

## <a name="supported-subscription-types"></a>Támogatott előfizetési típusok

Az alább felsorolt előfizetés-típusok számlázási tulajdonjogát lehet kérelmezni.

- [Action Pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Azure in Open licencprogram](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass Sponsorship](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Ingyenes próba](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure-csomag](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Támogatott Microsoft Azure-ajánlat](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft nagyvállalati szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN platformok](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise- (BizSpark-) előfizetők](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise- (MPN-) előfizetők](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise-előfizetők](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional-előfizetők](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Ha az előfizetésben tartalmaz bármennyi elérhető kreditet, az az átadást követően az új fiókban már nem lesz elérhető.

\*\* Csak az olyan előfizetéseknél támogatott, amelyek az Azure-webhelyre való regisztráció során létrehozott fiókokhoz tartoznak.


## <a name="additional-information"></a>További információ

A következő szakasz további információkkal szolgál az előfizetések átadásáról.

### <a name="no-service-downtime"></a>Nincs szolgáltatáskiesés

Az előfizetésben lévő Azure-szolgáltatások megszakítás nélkül futnak tovább. Csak a felhasználó által az átadásra kiválasztott Azure-előfizetések számlázási kapcsolatát állítjuk át.

### <a name="disabled-subscriptions"></a>Letiltott előfizetések

Letiltott előfizetéseket nem lehet átadni. Csak az aktív állapotú előfizetések számlázási tulajdonjoga adható át.

### <a name="azure-resources-transfer"></a>Azure-erőforrások átadása

Az előfizetésben található összes erőforrás, például virtuális gép, lemez és webhely átadódik az új fiókba.

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace-termékek átadása

Az Azure Marketplace-termékek is átadódnak a hozzájuk tartozó előfizetésekkel együtt.

### <a name="azure-reservations-transfer"></a>Azure-foglalások átadása

Az Azure-foglalásokat a rendszer nem helyezi át automatikusan. A foglalások áthelyezéséhez [forduljon az Azure ügyfélszolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="access-to-azure-services"></a>Hozzáférés az Azure-szolgáltatásokhoz

A meglévő felhasználók, csoportok vagy szolgáltatásnevek hozzáféréseire, amelyek az (Azure RBAC (szerepköralapú hozzáférés-vezérlés))[../role-based-access-control/overview.md] segítségével lettek hozzárendelve, az átadás nincs hatással.

### <a name="azure-support-plan"></a>Azure támogatási csomagot

Az Azure-támogatás nem adható át az előfizetésekkel együtt. Ha a felhasználó az összes Azure-előfizetését átadja, akkor kérje meg, hogy mondja le a támogatási csomagját.

### <a name="charges-for-transferred-subscription"></a>Átadott előfizetések díjai

Az előfizetések eredeti számlázási tulajdonosa felelős minden olyan költségért, amelyet az átadás befejezésének időpontjáig jelentettek. Az Ön számlaszakasza felelős az átadás időpontjától kezdve jelentett díjakért. Előfordulhat, hogy egy díj kiszabása az átadás előtt történt, de csak az átadást követően lett jelentve. Az ilyen díjak megjelennek a számlaszakaszában.

### <a name="cancel-a-transfer-request"></a>Átadási kérelem megszakítása

Az átadási kérelem megszakítható, ha még nem lett jóváhagyva vagy elutasítva. A megszakításhoz lépjen az [átadás részleteinek oldalára](#check-the-transfer-request-status), és válassza ki az oldal alján a Lemondás lehetőséget.

### <a name="software-as-a-service-saas-transfer"></a>Szolgáltatott szoftver (SaaS) átadása

A SaaS-termékek nem adhatók át az előfizetésekkel együtt. Kérje meg a felhasználót, hogy az SaaS-termékek számlázási tulajdonjogának átadásához [lépjen kapcsolatba az Azure ügyfélszolgálatával](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). A számlázási tulajdonjoggal együtt a felhasználó az erőforrásai tulajdonjogát is átadhatja. Az erőforrás-tulajdonjoga lehetővé teszi felügyeleti műveletek elvégzését, például a termék adatainak törlését és megtekintését. A felhasználónak az SaaS-termékben erőforrás-tulajdonosnak kell lennie ahhoz, hogy átadhassa az erőforrás tulajdonjogát.

## <a name="check-for-access"></a>Hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>Következő lépések

- Az Azure-előfizetések számlázási tulajdonjoga átadódik az Ön számlaszakaszába. Az ezen előfizetésekre vonatkozó díjak alakulását nyomon követheti az [Azure Portalon](https://portal.azure.com).
- Engedélyt adhat másoknak is, hogy megtekintsék és kezeljék ezen előfizetések számlázását. További információkért lásd [a számlaszakasz szerepköreit és azok feladatait](understand-mca-roles.md#invoice-section-roles-and-tasks) ismertető részt.
