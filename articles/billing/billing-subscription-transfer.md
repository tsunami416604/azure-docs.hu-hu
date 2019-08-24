---
title: Az Azure-előfizetés számlázási tulajdonjogának átadása másik fiókba | Microsoft Docs
description: Ismerteti, hogyan lehet átvinni egy Azure-előfizetés számlázási tulajdonjogát egy másik fiókba, és néhány gyakori kérdést (GYIK) a folyamatról
keywords: Azure-előfizetés átvitele, Azure-átviteli előfizetés, Azure-előfizetés áthelyezése másik fiókra, Azure-előfizetés tulajdonosa, Azure-előfizetés átszállítása másik fiókra, Azure-adatátviteli számlázás
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e4496e2b5d2b21fd878ef68665b8e5b06fa6cc5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012550"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Azure-előfizetés számlázási tulajdonjogának átadása másik fiókra

Előfordulhat, hogy át szeretné vinni az Azure-előfizetése számlázási tulajdonjogát, ha elhagyja a szervezetét, vagy ha azt szeretné, hogy az előfizetése egy másik fiókra legyen kiszámlázva. A számlázási tulajdonjog egy másik fiókba való átvitele lehetővé teszi a rendszergazdák számára az új fiók engedélyeit olyan számlázási feladatok elvégzéséhez, mint például a fizetési mód módosítása, a díjak megtekintése és az előfizetés megszakítása.

Ha meg szeretné őrizni a számlázási tulajdonjogot, de módosítani kívánja az előfizetés típusát, tekintse meg az [Azure-előfizetés másik ajánlatra váltását](billing-how-to-switch-azure-offer.md)ismertető témakört. Ha szeretné szabályozni, hogy kik kezelhetik az előfizetésben lévő erőforrásokat, tekintse meg az [Azure-erőforrások beépített szerepkörei](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)című részt.

Ha Ön Nagyvállalati Szerződés (EA) ügyfél, a vállalati rendszergazdák átvihetik az előfizetések számlázási tulajdonjogát a fiókok között. További információ: [nagyvállalati szerződés (EA) előfizetések számlázási tulajdonjogának](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions)átadása.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Azure-előfizetés számlázási tulajdonjogának átruházása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) az átvinni kívánt előfizetéssel rendelkező számlázási fiók rendszergazdájaként. Ha szeretné megtudni, hogy rendszergazda-e, tekintse meg a [Gyakori kérdések](#faq)című témakört.

1. A Keresés **Cost Management és számlázás**.

   ![Az Azure portál keresési bemutató képernyőkép](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Válassza az előfizetések lehetőséget a bal oldali ablaktáblán. A hozzáféréstől függően előfordulhat, hogy ki kell választania egy számlázási hatókört, majd ki kell választania az előfizetéseket vagy az **Azure**-előfizetéseket.

1. Válassza az átvitel **Számlázási tulajdonos** lehetőséget az átvinni kívánt előfizetéshez. 

   ![Válassza ki az átvinni kívánt előfizetést](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Adja meg egy olyan felhasználó e-mail-címét, aki az előfizetés új tulajdonosa lesz a fiók számlázási rendszergazdája.

1. Ha egy másik Azure AD-bérlő egyik fiókjába viszi át az előfizetést, válassza ki, hogy szeretné-e áthelyezni az előfizetést az új fiók bérlője számára. További információ: [előfizetés átadása egy másik Azure ad-bérlői fiókba](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)

    > [!IMPORTANT]
    >
    > Ha úgy dönt, hogy áthelyezi az előfizetést az új fiók Azure AD-bérlőbe, a rendszer véglegesen eltávolítja az előfizetés erőforrásainak kezeléséhez szükséges [szerepköralapú hozzáférés-vezérlési (RBAC)](../role-based-access-control/overview.md) hozzárendeléseket. Csak az új fiókban lévő azon felhasználó férhet hozzá az előfizetés erőforrásainak kezeléséhez, aki elfogadja az adatátviteli kérelmét. További információ: [előfizetés átadása egy felhasználónak egy másik Azure ad-bérlőben](../active-directory/managed-identities-azure-resources/known-issues.md). Másik lehetőségként törölheti az előfizetés Azure AD-bérlője számára a számlázási tulajdonjog átadását anélkül, hogy az előfizetést áthelyezi az új fiók bérlője számára. Ha így tesz, a rendszer megőrzi az Azure-erőforrások kezelésének meglévő RBAC-engedélyeit.
  
    ![Továbbítási lap küldése](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Válassza az átadási **kérelem küldése**lehetőséget.

1. A felhasználó egy e-mailt kap, amely útmutatást nyújt az adatátviteli kérelem áttekintéséhez.

   ![Előfizetés-átadási e-mail küldése a címzettnek](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Az adatátviteli kérelem jóváhagyásához a felhasználó kiválasztja az e-mailben szereplő hivatkozást, és követi az utasításokat. A felhasználónak ki kell választania egy fizetési módot, amelyet az előfizetés kifizetéséhez fog használni. Továbbá, ha a felhasználónak nincs Azure-fiókja, regisztrálnia kell egy új fiókot. 

   ![Első előfizetés-átvitel weblapja](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Második előfizetés-átvitel weblapja](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Második előfizetés-átvitel weblapja](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Sikerült! Az előfizetés átadása folyamatban van.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Előfizetés átruházása egy másik Azure AD-bérlőben lévő fiókba

A rendszer létrehoz egy Azure Active Directory (AD) bérlőt, amikor regisztrál az Azure-ra. A bérlő a fiókját jelöli. A Bérlővel kezelheti az előfizetések és az erőforrások elérését.

Amikor új előfizetést hoz létre, azt a fiókja Azure AD-bérlője tárolja. Ha szeretné, hogy mások is hozzáférhessenek az előfizetéséhez vagy erőforrásaihoz, meg kell hívnia őket a bérlőhöz való csatlakozáshoz. Ez segít az előfizetésekhez és az erőforrásokhoz való hozzáférés szabályozásában.

Ha egy másik Azure AD-bérlőben lévő fiókra helyezi át az előfizetés számlázási tulajdonjogát, akkor az előfizetést áthelyezheti az új fiók bérlője számára. Ha így tesz, minden olyan felhasználó, csoport vagy szolgáltatás, aki [szerepköralapú hozzáféréssel (RBAC)](../role-based-access-control/role-assignments-portal.md) rendelkezik az előfizetések kezeléséhez, elveszíti a hozzáférését. Csak az új fiókban lévő azon felhasználó férhet hozzá az erőforrások kezeléséhez, aki elfogadja az adatátviteli kérelmét. Ahhoz, hogy hozzáférést biztosítson azoknak a felhasználóknak, akik eredetileg hozzáfértek, az új tulajdonosnak [manuálisan kell hozzáadnia ezeket a felhasználókat az](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)előfizetéshez.


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>A Visual Studio, az MPN és a pay szolgáltatás továbbítása fejlesztési/tesztelési előfizetések esetén

A Visual Studio-és Microsoft Partner Network-előfizetések havonta ismétlődő Azure-Kredittel rendelkeznek. Ha átadja ezeket az előfizetéseket, a jóváírás nem érhető el a cél számlázási fiókban. Az előfizetés a cél számlázási fiókban található kreditet használja. Ha például a Bob egy Visual Studio Enterprise-előfizetést küld Jane számlájára szeptember 9-én, és Jane fogadja el az átvitelt. Az átvitel befejezése után az előfizetés a Jane-fiókban található kreditet használja. A kredit minden hónapban a kilencedik időpontra lesz visszaállítva. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Nagyvállalati Szerződés (EA) előfizetések számlázási tulajdonjogának átadása

A vállalati rendszergazda átviheti az előfizetések tulajdonjogát egy regisztráción belüli fiókok között. További információ: a [fiók tulajdonjogának](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) átadása az EA Portalon.

## <a name="next-steps-after-accepting-billing-ownership"></a>A számlázási tulajdonjog elfogadása utáni további lépések

Ha elfogadta egy Azure-előfizetés számlázási tulajdonjogát, javasoljuk, hogy tekintse át a következő lépéseket:

1. Tekintse át és frissítse a szolgáltatás-rendszergazdát, a társ-rendszergazdákat és az egyéb RBAC-szerepköröket. További információ: Azure- [előfizetések rendszergazdáinak hozzáadása vagy módosítása](billing-add-change-azure-subscription-administrator.md) és [a hozzáférés kezelése a RBAC és a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md).
1. Az előfizetéshez tartozó szolgáltatásokhoz társított hitelesítő adatok frissítése, beleértve a következőket:
   1. A felhasználói rendszergazdai jogosultságokat biztosító felügyeleti tanúsítványok az előfizetési erőforrásokhoz. További információ: [felügyeleti tanúsítvány létrehozása és feltöltése az Azure-hoz](../cloud-services/cloud-services-certs-create.md)
   1. Hozzáférési kulcsok olyan szolgáltatásokhoz, mint a Storage. További információ: [Tudnivalók az Azure Storage](../storage/common/storage-create-storage-account.md) -fiókokról
   1. Távelérési hitelesítő adatok olyan szolgáltatásokhoz, mint az Azure Virtual Machines.
1. Ha partnerrel dolgozik, érdemes lehet frissíteni a partner AZONOSÍTÓját ezen az előfizetésen. A partner AZONOSÍTÓját a [Azure Portal](https://portal.azure.com)frissítheti. További információ: [partner-azonosító csatolása az Azure-fiókokhoz](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Támogatott előfizetési típusok

Az előfizetések átvitele a Azure Portal az alább felsorolt előfizetési típusoknál érhető el. Az átvitel jelenleg nem támogatott az [ingyenes próbaverziós](https://azure.microsoft.com/offers/ms-azr-0044p/) vagy [Azure in Open Licencprogram (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) előfizetésekben. Megkerülő megoldásért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy](../azure-resource-manager/resource-group-move-resources.md)előfizetésre. Más előfizetések, például [](https://azure.microsoft.com/offers/ms-azr-0036p/) a szponzorálási vagy támogatási csomagok átviteléhez [forduljon az Azure ügyfélszolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [A Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [A Visual Studio Enterprise (MPN) előfizetők](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [MSDN platformok](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Utólagos elszámolású fejlesztési/tesztelési funkciók](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure terv](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\*[Az EA portálon keresztül](#EA).

\*\*Csak az Azure webhelyén való regisztráció során létrehozott fiókok esetében támogatott. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Gyakori kérdések (GYIK) a küldők számára

Ezek a gyakori kérdések azokra a felhasználókra vonatkoznak, akik egy Azure-előfizetés számlázási tulajdonjogát egy másik fiókba ruházzák át.

### <a name="whoisaa"></a>Ki a fiók számlázási rendszergazdája?

A számlázási rendszergazda olyan személy, aki jogosult a fiók számlázásának kezelésére. Jogosultak a [Azure Portal](https://portal.azure.com) számlázására, valamint különböző számlázási feladatok elvégzésére, például előfizetések létrehozására, a számlák megtekintésére és számlázására, illetve a fizetési módok frissítésére.

Az alábbi lépéseket követve azonosíthatja azokat a fiókokat, amelyekhez számlázási rendszergazdaként van szüksége:

1. Látogasson el [Azure Portal Cost Management + számlázási oldalára](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Válassza ki az **összes számlázási hatókört** a bal oldali ablaktáblán. 
1. Az előfizetések lap felsorolja az összes olyan előfizetést, amelynek Ön számlázási rendszergazdája.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Mindent átvisz? Többek között az erőforráscsoportok, a virtuális gépek, a lemezek és az egyéb futó szolgáltatások?

Az összes erőforrás, például a virtuális gépek, a lemezek és a webhelyek átadása az új fiókba. Ha azonban egy másik Azure AD-bérlőben lévő fiókra is átviszi az előfizetést, akkor az előfizetés összes [rendszergazdai szerepköre](billing-add-change-azure-subscription-administrator.md) és [SZEREPKÖRALAPÚ Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) hozzárendelése [nem kerül át](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Emellett az [alkalmazások regisztrációi](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) és egyéb bérlői szolgáltatásai nem vihetők át az előfizetéssel együtt.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Átadhatom a tulajdonjogot egy másik országban lévő fióknak?
Sajnos a Azure Portalon nem hajthatók végre több ország közötti adatátvitel. Az előfizetés országok közötti átviteléhez [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)az ügyfélszolgálathoz.

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Két fiók rendszergazdája vagyok. Átvihetek egy előfizetést az egyik fiókból a másikba?
Igen, a fiókok között átviheti az előfizetést. A fiókok fogalmilag két különböző felhasználó fiókjai, így a fenti lépéseket követve viheti át az előfizetéseket a fiókok között.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Az előfizetés-átvitel bármilyen szolgáltatási állásidőt eredményez?

Ha egy előfizetést ugyanahhoz az Azure AD-bérlőhöz tartozó fiókba helyez át, az előfizetésben futó erőforrásokra nincs hatással. Ha azonban egy másik bérlőn lévő fiókra viszi át az előfizetést, és úgy dönt, hogy áthelyezi az előfizetést a bérlőre, az összes olyan felhasználót, csoportot és szolgáltatást, aki [szerepköralapú hozzáféréssel (RBAC)](../role-based-access-control/overview.md) rendelkezik az előfizetés erőforrásainak kezeléséhez, elveszíti a hozzáférését . Ez a szolgáltatás leállását okozhatja.

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>Az új fiókkal rendelkező felhasználók hozzáférhetnek a használati és számlázási előzményekhez?

Az új fiókban lévő felhasználók számára elérhető információk az előfizetése utolsó havi díja. A használat és a számlázási előzmények többi része nem kerül át az előfizetésbe

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Hogyan az Azure-előfizetéshez tartozó adatátviteli és-szolgáltatásokra vonatkozó új előfizetést?

Ha nem tudja átvinni az előfizetés tulajdonjogát, manuálisan is áttelepítheti az erőforrásokat. Lásd: [erőforrások áthelyezése új erőforrás-csoportba vagy](../azure-resource-manager/resource-group-move-resources.md)előfizetésbe.

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Ha a Visual studiót vagy Microsoft Partner Network-előfizetést átviszem, a kreditem az új fiók előfizetésével együtt történik?

Nem, a kredit nem érhető el az új fiókban. Az átadási kérelmet fogadó felhasználónak rendelkeznie kell egy Visual Studio-licenccel az átviteli kérelem elfogadásához. Az előfizetés a felhasználói fiókban elérhető Visual Studio-kreditet használja. További információ: a [Visual Studio, a Microsoft Partner Network (MPN) átadása és a fizetési és tesztelési](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions)előfizetések fizetése.


## <a name="frequently-asked-questions-faq-for-recipients"></a>Gyakori kérdések (GYIK) a címzettek számára

Ezek a gyakori kérdések a felhasználókra vonatkoznak, akik elfogadják egy Azure-előfizetés számlázási tulajdonjogát egy másik fiókból.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Ha átveszem egy előfizetés számlázási tulajdonjogát egy másik fiókból, akkor a fiókban lévő felhasználók továbbra is hozzáférhetnek az erőforrásokhoz?

Igen. Ha azonban a fiókja egy olyan Azure AD-bérlőben található, amely eltér az előfizetés bérlőtől, és az átadási kérelmet küldő felhasználó áthelyezi az előfizetést a fiók bérlője, a [rendszergazdai szerepkörök](billing-add-change-azure-subscription-administrator.md) és [SZEREPKÖRALAPÚ Access Control (RBAC) számára. ](../role-based-access-control/role-assignments-portal.md)a hozzárendelések el lesznek távolítva. Az alábbi lépéseket követve megtekintheti azokat a felhasználókat, akik [szerepköralapú hozzáférési (RBAC)](../role-based-access-control/overview.md) hozzáféréssel rendelkeznek az előfizetés erőforrásainak kezeléséhez:

1. Látogasson el a [Azure Portal előfizetés lapjára](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki az ellenőrizni kívánt előfizetést, majd a bal oldali ablaktáblán válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
1. Válassza ki a **szerepkör** -hozzárendeléseket az oldal tetején. A szerepkör-hozzárendelések lap felsorolja az összes olyan felhasználót, aki RBAC hozzáféréssel rendelkezik az előfizetéshez.

Még akkor is, ha a [szerepköralapú Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) hozzárendelések el lettek távolítva az átvitel során, előfordulhat, hogy az eredeti tulajdonos fiókban lévő felhasználók továbbra is hozzáférhetnek az előfizetéshez bizonyos biztonsági mechanizmusok segítségével, többek között:

* A felhasználói rendszergazdai jogosultságokat biztosító felügyeleti tanúsítványok az előfizetési erőforrásokhoz. További információ: [felügyeleti tanúsítvány létrehozása és feltöltése az Azure-hoz](../cloud-services/cloud-services-certs-create.md).
* Hozzáférési kulcsok olyan szolgáltatásokhoz, mint a Storage. További információkat az [Azure Storage-fiókokkal kapcsolatos tudnivalókat](../storage/common/storage-create-storage-account.md) ismertető témakörben olvashat.
* Távelérési hitelesítő adatok olyan szolgáltatásokhoz, mint az Azure Virtual Machines.

Ha a címzettnek korlátoznia kell az erőforrásokhoz való hozzáférést, érdemes megfontolnia a szolgáltatáshoz társított titkok frissítését. A legtöbb erőforrás a következő lépések segítségével frissíthető:

  1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
  2. A központi menüben válassza a **minden erőforrás**elemet.
  3. Válassza ki az erőforrást.
  4. Az erőforrás lapon kattintson a **Beállítások**elemre. Itt megtekintheti és frissítheti a meglévő titkokat.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Ha átveszem az előfizetés számlázási tulajdonjogát a számlázási ciklus közepén, akkor fizetnem kell a teljes számlázási ciklusért?

A fiók felelős minden olyan használatért, amelyet az átvitel időpontjától kezdve jelentettek. Előfordulhat, hogy az átvitel előtt már történtek használatban, de ezt követően jelent meg. A használatot a fiókja számlája tartalmazza.

### <a name="can-i-use-a-different-payment-method"></a>Használhatok más fizetési módot?

Igen. Az átadási kérelem elfogadásakor kiválaszthat egy meglévő, a fiókjához társított fizetési módot, vagy hozzáadhat egy új fizetési módot.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a id="no-button"></a>Miért nem jelenik meg az "előfizetés átvitele" gomb?

A saját számlázási fiókjához nem érhető el az önkiszolgáló előfizetés-átvitel. Jelenleg nem támogatott az előfizetések számlázási tulajdonjogának átadása Nagyvállalati Szerződés (EA) fiókokban a Azure Portalban. Emellett a Microsoft-képviselők használata során létrehozott Microsoft Customer Agreement-fiókok nem támogatják a számlázási tulajdonjog átruházását. 

### <a id="no-button"></a>Miért nem támogatja az előfizetés típusa az átvitelt? 

Nem minden típusú előfizetés támogatja a számlázási tulajdonjogok átvitelét. Az átadást támogató előfizetési típusok listájának megtekintéséhez lásd: [támogatott előfizetési típusok](#supported-subscription-types)

### <a id="no-button"></a>Miért kapok hozzáférés-megtagadási hibát, amikor megpróbálok átvinni egy előfizetés számlázási tulajdonjogát? 

Ez a hiba akkor jelenik meg, ha egy Microsoft Azure csomag-előfizetést próbál átvinni, és nem rendelkezik a szükséges engedélyekkel. Microsoft Azure csomag előfizetésének átadásához olyan tulajdonosnak vagy közreműködőnek kell lennie a számla szakaszban, amelyhez az előfizetés számlázása történik. További információ: a [számlázásra vonatkozó előfizetések kezelése szakasz](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- Tekintse át és frissítse a szolgáltatás-rendszergazdát, a társ-rendszergazdákat és az egyéb RBAC-szerepköröket. További információ: Azure- [előfizetések rendszergazdáinak hozzáadása vagy módosítása](billing-add-change-azure-subscription-administrator.md) és [a hozzáférés kezelése a RBAC és a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md).
