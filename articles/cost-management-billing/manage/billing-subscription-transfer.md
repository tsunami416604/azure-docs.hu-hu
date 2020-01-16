---
title: Azure-előfizetés számlázási tulajdonjogának átadása
description: Leírja, hogyan lehet átruházni az Azure-előfizetés számlázási tulajdonjogát egy másik fiókra, és megválaszol néhány gyakori kérdést (GYIK) a folyamatra vonatkozóan.
keywords: azure-előfizetés átadása, átadás azure-előfizetés, azure-előfizetés áthelyezése másik fiókba, azure-előfizetés tulajdonosának módosítása, azure-előfizetés átvitele másik fiókba, azure számlázás átadása
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6f43f7424cddae8659301be0ddc4842412a62952
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75993543"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Azure-előfizetés számlázási tulajdonjogának átadása másik fióknak

Az Azure-előfizetés számlázási tulajdonjogának átadására akkor lehet szükség, ha elhagyja a szervezetet, vagy egy másik fióknak kell küldeni az előfizetésre vonatkozó számlákat. A számlázási tulajdonjog másik fióknak való átadása lehetővé teszi az új fiók rendszergazdái számára különböző számlázási feladatok végrehajtását, például a fizetési mód megváltoztatását, a díjak megtekintését és az előfizetés lemondását.

Ha meg szeretné tartani a számlázási tulajdonjogot, de módosítani kívánja az előfizetés típusát, tekintse meg [az Azure-előfizetés másik ajánlatra váltását](switch-azure-offer.md) bemutató cikket. Ha szeretné szabályozni, hogy ki kezelheti az előfizetés erőforrásait, tekintse meg [az Azure-erőforrások beépített szerepköreit](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

A Nagyvállalati Szerződéssel (EA) rendelkező ügyfelek esetében a vállalati rendszergazdák adhatják át az előfizetések számlázási tulajdonjogát az egyes fiókok között. További információ: [Nagyvállalati Szerződéssel (EA) rendelkező előfizetések számlázási tulajdonjogának átadása](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Azure-előfizetés számlázási tulajdonjogának átadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az átadni kívánt előfizetést tartalmazó számlázási fiók rendszergazdájaként. Ha ellenőrizni szeretné, hogy Ön rendszergazda-e, tekintse meg a [gyakori kérdéseket](#faq).

1. Végezzen keresést a **Költségkezelés + Számlázás** oldalon.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. A bal oldali panelen válassza az **Előfizetések** elemet. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási hatókört, majd az **Előfizetések** vagy az **Azure-előfizetések** lehetőséget.

1. Az átadni kívánt előfizetésnél válassza a **Számlázási tulajdonjog átadása** lehetőséget.

   ![Válassza ki az átadni kívánt előfizetést.](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Adja meg egy olyan felhasználó e-mail-címét, aki annak a fióknak a számlázási adminisztrátora, amely az előfizetés új tulajdonosa lesz.

1. Ha az előfizetését egy másik Azure AD-bérlőn lévő fiókba helyezi át, válassza ki, hogy át kívánja-e helyezni az előfizetést az új fiók bérlőjére. További információért lásd az [előfizetés egy másik Azure AD-bérlőn lévő fiókra történő átadását](#transferring-subscription-to-an-account-in-another-azure-ad-tenant) ismertető cikket.

    > [!IMPORTANT]
    >
    > Ha úgy dönt, hogy átadja az előfizetést az új fiók Azure AD-bérlőjére, azzal véglegesen eltávolítja az előfizetés erőforrásainak kezeléséhez szükséges összes [szerepköralapú hozzáférés-vezérlési (RBAC)](../../role-based-access-control/overview.md) hozzárendelést. Csak az új fiók átadási kérelmet elfogadó felhasználójának lesz hozzáférése az előfizetés erőforrásainak kezeléséhez. További információért lásd az [előfizetés egy másik Azure AD-bérlőn lévő felhasználónak történő átadását](../../cognitive-services/acoustics/known-issues.md) ismertető cikket. Másik lehetőségként törölheti az Előfizetés Azure AD-bérlője jelölőnégyzet jelölését, így anélkül adhatja át a számlázási tulajdonjogot az új fiók bérlőjének, hogy áthelyezné az előfizetést. Ha ezt a módszert választja, megtarthatja az Azure-erőforrások kezeléséhez szükséges meglévő RBAC-engedélyeket.

    ![Átadási kérelem küldése lap](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Válassza az **Átadási kérelem küldése** lehetőséget.

1. A felhasználó egy e-mailt kap, amely felszólítja az átadási kérelem áttekintéséra.

   ![Az előfizetés átadásáról szóló e-mail, amely el lett küldve a címzettnek](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Az átadási kérelem jóváhagyásához a felhasználó kiválasztja az e-mailben szereplő hivatkozást, és követi az útmutatást. A felhasználónak ki kell választania az előfizetés díjának fizetésére használt fizetési módot. Ha a szóban forgó felhasználó még nem rendelkezik Azure-fiókkal, regisztrálnia kell egyet.

   ![Előfizetés-átadás: első weblap](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Előfizetés-átadás: második weblap](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Előfizetés-átadás: második weblap](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Sikerült! Az előfizetés átadása ezzel megtörtént.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Előfizetés átadása egy másik Azure AD-bérlőn található fióknak

Amikor regisztrál az Azure-ba, a rendszer létrehoz Önnek egy Azure Active Directory- (AD)-bérlőt. A bérlő képviseli az Ön fiókját. A bérlő segítségével szabályozhatja az előfizetéseihez és az erőforrásaihoz való hozzáférést.

Amikor új előfizetést hoz létre, az a fiókja Azure AD-bérlőjén lesz tárolva. Ha másoknak kíván hozzáférést biztosítani az előfizetéshez vagy annak erőforrásaihoz, meg kell őket hívnia, hogy csatlakozzanak a bérlőjéhez. Így szabályozhatja az előfizetésekhez és az erőforrásokhoz való hozzáférést.

Amikor az előfizetése számlázási tulajdonjogát egy másik Azure AD-bérlőn lévő fióknak adja át, az előfizetést is áthelyezheti az új fiók bérlőjére. Ha így tesz, akkor az összes korábbi [szerepköralapú hozzáféréssel (RBAC)](../../role-based-access-control/role-assignments-portal.md) rendelkező felhasználó, csoport és szolgáltatásnév elveszíti az előfizetés és az előfizetés erőforrásainak kezeléséhez szükséges hozzáférést. Csak az új fiók átadást elfogadó felhasználójának lesz hozzáférése az erőforrások kezeléséhez. A korábban hozzáféréssel rendelkező felhasználóknak csak úgy biztosítható ismét hozzáférés, ha az új tulajdonos [manuálisan hozzáadja ezeket a felhasználókat az előfizetéshez](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Visual Studio-, MPN- és Pay-As-You-Go Dev/Test-előfizetések átadása

A Visual Studio- és a Microsoft Partner Network-előfizetésekhez havonta ismétlődő Azure-kredit tartozik. Az előfizetések átadásakor ez a kredit nem válik elérhetővé a cél számlázási fiókban. Az előfizetés a cél számlázási fiókban rendelkezésre álló kreditet használja fel. Például: Bob átad egy Visual Studio Enterprise-előfizetést Jane fiókjába szeptember 9-én, és Jane elfogadja a tranzakciót. Az átadás befejezését követően az előfizetés Jane fiókjának kreditjét kezdi használni. A kreditek mennyisége minden hónap 9. napján visszaáll az alapértékre.


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Nagyvállalati Szerződéssel (EA) rendelkező előfizetések számlázási tulajdonjogának átadása

A vállalati adminisztrátor átadhatja az előfizetések tulajdonjogát egyik fiókból a másiknak egy adott regisztráción belül. További információkért lásd a [fiók tulajdonjogának átadását](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) ismertető cikket az EA Portalon.

## <a name="next-steps-after-accepting-billing-ownership"></a>A számlázási tulajdonjog elfogadását követő lépések

Ha elfogadta egy Azure-előfizetés számlázási tulajdonjogát, javasoljuk, hogy tekintse át a következő lépéseket:

1. Tekintse át és frissítse a szolgáltatásadminisztrátori, a társadminisztrátori és az egyéb RBAC-szerepköröket. További információk: [Azure-előfizetés-adminisztrátorok hozzáadása vagy módosítása](add-change-subscription-administrator.md) és [Hozzáférés kezelése az RBAC és az Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).
1. Frissítse az előfizetés szolgáltatásaihoz tartozó hitelesítő adatokat, például:
   1. Felügyeleti tanúsítványok, amelyek rendszergazdai jogosultságokat biztosítanak a felhasználónak az előfizetés erőforrásaihoz. További információk: [Felügyeleti tanúsítvány létrehozása és feltöltése az Azure szolgáltatáshoz](../../cloud-services/cloud-services-certs-create.md)
   1. A Storage-hoz hasonló szolgáltatások hozzáférési kulcsai. További információkat az [Azure Storage-fiókokkal kapcsolatos tudnivalókat](../../storage/common/storage-create-storage-account.md) ismertető témakörben olvashat.
   1. Az olyan szolgáltatások távelérési hitelesítő adatai, mint az Azure-beli virtuális gépek.
1. Ha egy partnerrel közösen dolgozik, fontolja meg a partnerazonosító frissítését ezen az előfizetésen. A partnerazonosítót az [Azure Portalon](https://portal.azure.com) frissítheti. További információért lásd a [partnerazonosítók az Azure-fiókokhoz csatolását](link-partner-id.md) ismertető cikket.

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Támogatott előfizetési típusok

Az előfizetések Azure Portalon történő átadása az alábbi előfizetési típusok esetében érhető el. Az átadás lehetősége jelenleg nem támogatott az [ingyenes próba-előfizetések](https://azure.microsoft.com/offers/ms-azr-0044p/) és az [Azure in Open- (AIO-)](https://azure.microsoft.com/offers/ms-azr-0111p/) előfizetések esetében. Áthidaló megoldásért tekintse meg az [erőforrások új erőforráscsoportba vagy előfizetésbe való áthelyezését](../../azure-resource-manager/management/move-resource-group-and-subscription.md) ismertető témakört. Egyéb előfizetések, például [Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) vagy támogatási csomagok átviteléhez [forduljon az Azure ügyfélszolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise- (MPN-) előfizetők](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN-platformok](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure-csomag](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [az EA portálon keresztül](#EA).

\*\*Csak az Azure webhelyre való regisztráció során létrehozott fiókok esetében támogatott.

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Gyakori kérdések (GYIK) küldők számára

Ezek a gyakori kérdések azoknak a felhasználóknak szólnak, akik átadják egy Azure-előfizetés számlázási tulajdonjogát egy másik fióknak.

### <a name="whoisaa"></a> Ki a fiók számlázási adminisztrátora?

A számlázási adminisztrátor az a személy, aki jogosult kezelni a fiók számlázási feladatait. Ő férhet hozzá a számlákhoz az [Azure Portalon](https://portal.azure.com), és ő végzi el a különböző számlázási feladatokat, mint az előfizetések létrehozása, a számlák áttekintése és kifizetése, valamint a fizetési módok frissítése.

A következő módon tudhatja meg, hogy Ön mely fiókok számlázási adminisztrátora:

1. Nyissa meg [az Azure Portal Költségkezelés + számlázás oldalát](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. A bal oldali panelen válassza a **Minden számlázási hatókör** lehetőséget.
1. Az Előfizetések lapon megtalálható az összes előfizetés, amelynek Ön a számlázási adminisztrátora.

Ha nem biztos benne, hogy az adott előfizetésnek ki a fiókadminisztrátora, az alábbi lépésekkel kiderítheti.

1. Nyissa meg [az Azure Portal Előfizetések oldalát](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki az ellenőrizni kívánt előfizetést, majd lépjen a **Beállítások** területre.
1. Válassza ki a **Tulajdonságok** elemet. Az előfizetés fiókadminisztrátora a **Fiókadminisztrátor** mezőben látható.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Minden át lesz helyezve az új fiókba, beleértve az erőforráscsoportokat, a virtuális gépeket, a lemezeket és az egyéb futó szolgáltatásokat?

Az összes erőforrás, például virtuális gép, lemez és webhely át lesz helyezve az új fiókba. Ugyanakkor ha az előfizetést egy másik Azure AD-bérlőben található fiókba helyezi át, az előfizetés [rendszergazdai szerepkörei](add-change-subscription-administrator.md) és [szerepköralapú hozzáférés-vezérlési (RBAC-)](../../role-based-access-control/role-assignments-portal.md) hozzárendelései [nem lesznek átadva](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Ezenkívül az [alkalmazásregisztrációkat](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) és más, bérlőspecifikus szolgáltatásokat sem adja át az előfizetéssel.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Átadhatom a fiók tulajdonjogát egy másik országban található felhasználónak?
Sajnos az Azure Portalon nem hajthatók végre országok közötti átadási műveletek. Az előfizetés országok közötti átadásához [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Két fiók rendszergazdája vagyok. Megtehetem, hogy átadom az egyik fiókomban található előfizetést a másik fiókomnak?
Igen, átadhat előfizetéseket a fiókjai között. A fiókjai technikailag két különálló felhasználó fiókjainak tekinthetők, ezért az előfizetések fiókok közötti átadására vonatkozó fenti eljárás ebben az esetben alkalmazható.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Az előfizetés átadása jár valamennyi szolgáltatáskieséssel?

Egy előfizetésnek az ugyanazon Azure AD-bérlőn lévő fiókba való átadása nincs hatással az előfizetésen futó erőforrásokra. Ha azonban az előfizetést egy másik bérlő fiókjába helyezi át, és úgy dönt, hogy áthelyezi az előfizetést a bérlőre, akkor az összes korábbi, [szerepköralapú hozzáféréssel (RBAC)](../../role-based-access-control/overview.md) rendelkező felhasználó, csoport és szolgáltatásnév elveszíti az előfizetésben lévő erőforrások kezeléséhez való hozzáférést. Ez szolgáltatáskieséssel járhat.

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>Az új fiók felhasználói hozzáférnek a használati és számlázási előzményekhez?

Az új fiókban lévő felhasználók számára elérhető egyetlen információ az előfizetése utolsó havi díja. A használati és számlázási előzmények közül más nem kerül át az előfizetéssel együtt.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Hogyan migrálhatom az Azure-előfizetésem adatait és szolgáltatásait az új előfizetésre?

Ha nem tudja átadni az előfizetés tulajdonjogát, manuálisan is elvégezheti az erőforrások migrálását. Lásd [az erőforrások új erőforráscsoportba vagy előfizetésbe való áthelyezését](../../azure-resource-manager/management/move-resource-group-and-subscription.md) ismertető témakört.

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Visual Studio- vagy Microsoft Partner Network-előfizetés átadásakor az előfizetéssel együtt a kreditek is átkerülnek az új fiókba?

Nem, a kreditek nem lesznek elérhetők az új fiókban. Az átadási kérelmet elfogadó felhasználónak rendelkeznie kell Visual Studio-licenccel a kérelem elfogadásához. Az előfizetés a felhasználó fiókjában rendelkezésre álló Visual Studio-krediteket használja. További információért lásd: [Visual Studio-, Microsoft Partner Network- (MPN-) és Pay-As-You-Go Dev/Test-előfizetések átadása](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions).


## <a name="frequently-asked-questions-faq-for-recipients"></a>Gyakori kérdések (GYIK) címzettek számára

Ezek a gyakori kérdések azoknak a felhasználóknak szólnak, akik elfogadják egy Azure-előfizetés számlázási tulajdonjogát egy másik fióktól.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Ha átveszem egy előfizetés számlázási tulajdonjogát egy másik fióktól, a szóban forgó fiók felhasználói továbbra is hozzáférhetnek az erőforrásaimhoz?

Igen. Ugyanakkor ha a fiók az előfizetés bérlőjétől eltérő Azure AD-bérlőn található, és az átadási kérelmet küldő felhasználó áthelyezi az előfizetést az Ön fiók bérlőjére, az összes [rendszergazdai szerepkör](add-change-subscription-administrator.md) és [szerepköralapú hozzáférés-vezérlési (RBAC-)](../../role-based-access-control/role-assignments-portal.md) hozzárendelés el lesz távolítva. Az előfizetés erőforrásainak kezeléséhez [szerepköralapú hozzáféréssel](../../role-based-access-control/overview.md) rendelkező felhasználók megtekintéséhez végezze el a következő lépéseket:

1. Nyissa meg [az Azure Portal Előfizetés lapját](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki a megtekinteni kívánt előfizetést, majd a bal oldali panelen válassza a **Hozzáférés-vezérlés (IAM)** elemet.
1. Válassza a lap tetején található **Szerepkör-hozzárendelések** elemet. A Szerepkör-hozzárendelések oldal felsorolja az összes felhasználót, aki RBAC-hozzáféréssel rendelkezik az előfizetéshez.

A [Szerepköralapú hozzáférés-vezérlési (RBAC-)](../../role-based-access-control/role-assignments-portal.md) hozzárendelések az átadás során történő eltávolítása esetén is előfordulhat, hogy az eredeti tulajdonosi fiók felhasználói hozzáféréssel rendelkeznek az előfizetéshez valamilyen biztonsági mechanizmuson keresztül, például:

* Felügyeleti tanúsítványok, amelyek rendszergazdai jogosultságokat biztosítanak a felhasználónak az előfizetés erőforrásaihoz. További információért lásd: [Felügyeleti tanúsítvány létrehozása és feltöltése az Azure szolgáltatáshoz](../../cloud-services/cloud-services-certs-create.md).
* A Storage-hoz hasonló szolgáltatások hozzáférési kulcsai. További információkat az [Azure Storage-fiókokkal kapcsolatos tudnivalókat](../../storage/common/storage-create-storage-account.md) ismertető témakörben olvashat.
* Az olyan szolgáltatások távelérési hitelesítő adatai, mint az Azure-beli virtuális gépek.

Ha a címzettnek korlátoznia kell a hozzáférését az erőforrásaihoz, érdemes megfontolnia a szolgáltatáshoz társított titkos kódok frissítését. A legtöbb erőforrást a következő lépésekkel lehet frissíteni:

  1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
  2. A központi menüben válassza a **Minden erőforrás** elemet.
  3. Válassza ki az erőforrást.
  4. Az erőforrás lapján kattintson a **Beállítások** elemre. Itt tekintheti meg és frissítheti a meglévő titkos kódokat.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Ha egy számlázási ciklus közepén veszem át egy előfizetés számlázási tulajdonjogát, akkor a teljes számlázási ciklust ki kell fizetnem?

Az Ön fiókja felelős az átadás időpontjától kezdve jelentkező használati díjak megfizetéséért. Előfordulhat, hogy bizonyos használatra az átadás előtt került sor, de csak az átadást követően lett jelentve. Ez is az Ön fiókjának számlájában fog szerepelni.

### <a name="can-i-use-a-different-payment-method"></a>Használhatok más fizetési módot?

Igen. Az átadási kérelem elfogadásakor kiválaszthatja a fiókja meglévő fizetési módját, vagy hozzáadhat egy új fizetési módot.

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>Hogyan adhatom át a nagyvállalati szerződéssel (EA) rendelkező előfizetés fióktulajdonjogát, ha az eredeti fióktulajdonos már nem a vállalat munkatársa?

A vállalati rendszergazda bármilyen fiók tulajdonjogát frissítheti, még azután is, hogy az eredeti fióktulajdonos már nem a vállalat munkatársa. Ehhez [a fiók tulajdonjogának átvitele az összes előfizetésre vonatkozóan](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) eljárás utasításait kell követnie az EA Portalon.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a id="no-button"></a> Miért nem látom Az előfizetés átadása gombot?

Az önkiszolgáló előfizetés-átadási funkció nem érhető el a számlázási fiókjában. Jelenleg a nagyvállalati szerződéses (EA-) fiókok esetében nem támogatott az előfizetések számlázási tulajdonjogának átadása az Azure Portalon. Ezenkívül a Microsoft-képviselővel közösen létrehozott Microsoft-ügyfélszerződési fiókok nem támogatják a számlázási tulajdonjog átadását.

### <a id="no-button"></a> Miért nem támogatja az előfizetési típusom az átadást?

Nem minden előfizetés-típus támogatja a számlázási tulajdonjog átadását. Az átadást támogató előfizetési típusok teljes listáját a [támogatott előfizetési típusokat](#supported-subscription-types) felsoroló témakörben tekintheti át.

### <a id="no-button"></a> Miért jelenik meg hozzáférés megtagadva hiba, amikor megpróbálom átadni az előfizetés számlázási tulajdonjogát?

Akkor kapja ezt a hibaüzenetet, ha úgy próbálja átadni egy Microsoft Azure-csomag előfizetését, hogy nem rendelkezik a szükséges engedéllyel. A Microsoft Azure-csomag előfizetésének átadásához tulajdonosnak vagy közreműködőnek kell lennie abban a számlázási szakaszban, ahol az előfizetés számlái megjelennek. További információk: [A számlázási szakaszokhoz tartozó előfizetések kezelése](understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Következő lépések

- Tekintse át és frissítse a szolgáltatásadminisztrátori, a társadminisztrátori és az egyéb RBAC-szerepköröket. További információk: [Azure-előfizetés-adminisztrátorok hozzáadása vagy módosítása](add-change-subscription-administrator.md) és [Hozzáférés kezelése az RBAC és az Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).
