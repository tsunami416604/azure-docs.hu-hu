---
title: Azure-előfizetések számlázási tulajdonjogának átruházása másik fiókra |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy másik fiókot, és néhány gyakori kérdések (GYIK) folyamatával kapcsolatos Azure-előfizetés számlázási tulajdonjogának átruházása
keywords: azure-előfizetéssel, az azure-átviteli előfizetésének átadására, azure-előfizetés áthelyezése egy másik azure-fiók módosítása előfizetés tulajdonosa, azure-előfizetés átvitele egy másik azure-fiók átviteli számlázás
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
ms.openlocfilehash: cff3c57c31526119ab81225a1c70b163173be937
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514436"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Azure-előfizetések számlázási tulajdonjogának átruházása másik fiókra

Érdemes az Azure-előfizetés számlázási tulajdonjogának átruházása, ha a szervezet hagynak el, vagy azt szeretné, hogy az előfizetés egy másik fiókba való. A rendszergazdák az új fiók engedély számlázási feladatok elvégzéséhez, mint például a fizetési mód megváltoztatása, díjaikat és az előfizetés megszüntetése számlázási átruházása másik fiókra biztosít.

Ha meg szeretné tartani a számlázási tulajdonosi jogokat, de módosítása az előfizetés, [Váltás másik ajánlatra az Azure-előfizetés](billing-how-to-switch-azure-offer.md). Ha azt szeretné, hogy ki az előfizetés-erőforrások is kezelhetők, [beépített szerepkörök az Azure-erőforrások](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Ha Ön egy vállalati Agreement(EA) ügyfél, a vállalati rendszergazdák átadhatja a számlázási tulajdonosi jogait az előfizetések fiókok között. További információkért lásd: [nagyvállalati szerződés (EA) előfizetések számlázási tulajdonjogának](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Azure-előfizetések számlázási tulajdonjogának átruházása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az előfizetést, amelyhez szeretné vinni a számlázási fiók rendszergazdaként. Ismerje meg, ha Ön rendszergazda, lásd: [– gyakori kérdések](#faq).

1. A Keresés **Cost Management és számlázás**.

   ![Az Azure portál keresési bemutató képernyőkép](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Válassza ki **előfizetések** a bal oldali panelen. A hozzáférést, attól függően szükség lehet egy számlázási hatókör majd válassza ki és **előfizetések** vagy **Azure-előfizetések**.

1. Válassza ki **más fiókhoz átviteli** , amelyet át szeretne az előfizetés. 

   ![Válassza ki az előfizetés átvitele](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Adja meg az e-mail címét, az előfizetés új tulajdonosaként, és válassza ki a fiók számlázási rendszergazda felhasználó **küldési átadására irányuló kérelmet**.

    > [!IMPORTANT]
    >
    > Az előfizetés számlázási tulajdonjogát egy felhasználói fiók egy másik Azure AD-ben átvitele esetén a bérlői, az összes [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) hozzárendelések kezeléséhez az előfizetésben található erőforrások véglegesen törlődnek. Csak az új tulajdonos hozzáférhet az előfizetés erőforrásainak kezeléséhez. További információkért lásd: [előfizetés átvitele egy felhasználó egy másik Azure AD-bérlőjében](../active-directory/managed-identities-azure-resources/known-issues.md).
  
    ![Üzenet személyhívóra átvitel](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. A felhasználó kap egy e-mailt, tekintse át az átadási kérelem utasításokat tartalmazó.

   ![Előfizetés átadási e-mail címzett](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Az átadási kérelem jóváhagyása, a felhasználó kiválasztja a hivatkozásra az e-mailben, és utasításokat. A felhasználónak kell válassza ki a használandó az előfizetés fizetési módját. Továbbá ha a felhasználó nem rendelkezik Azure-fiókra, azok kellene regisztrálhat egy új fiókot. 

   ![Első előfizetési átadás weblapot](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Második előfizetés-átadási weblap](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Második előfizetés-átadási weblap](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Sikerült! Az előfizetések átvitele esetén.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Előfizetés átadása egy másik Azure AD-bérlőjében fiókja

Feliratkozás az Azure-hoz az Azure Active Directory (AD) bérlő az Ön számára jön létre. A bérlő fiókjában jelöli. A bérlő az előfizetésekre és erőforrásokra való hozzáférés kezeléséhez használhatja.

Amikor egy új előfizetést hoz létre, az a fiók az Azure AD-bérlő üzemel. Ha szeretne biztosítani, mások az előfizetésre, vagy az erőforrások eléréséhez, ha címtártagot szeretne meghívni a bérlőbe szüksége. Ez segít, hogy ki férhet hozzá az előfizetésekre és erőforrásokra.

Ha az előfizetés számlázási tulajdonjogának átruházása egy fiókot egy másik Azure AD-bérlőjében, az előfizetés az új fiók bérlőhöz helyezett át. Felhasználók, csoportok vagy szolgáltatásnevek, akik [szerepköralapú hozzáférés-(vezérléssel RBAC)](../role-based-access-control/overview.md) kezeléséhez az előfizetésben található erőforrások elveszítik a hozzáférésüket. Az új fiókot az átadási kérelem megkapó csak a felhasználó hozzáférhet az erőforrások kezeléséhez. Hozzáférés biztosítása a felhasználók, akik eredetileg access, az új tulajdonos kellene [ezek a felhasználók manuális felvétele az előfizetés](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions"></a>A Visual Studio, a Microsoft Partner Network (MPN) és a használatalapú fejlesztési és tesztelési előfizetések átvitele

Visual Studio és a Microsoft Partner Network-előfizetések havi ismétlődő Azure-kredit társítva van. Ezeket az előfizetéseket, átvitel során a kreditjeit nem áll rendelkezésre a cél számlázási fiók. Az előfizetés az a cél számlázási fiók kreditet használja. Például ha Bálint továbbít egy Visual Studio Enterprise előfizetéssel Jane a 9. Szeptembertől a fiókhoz, és Jane fogadja el az átvitelt. Az átvitel befejezése után az előfizetés Jane a fiókban lévő kredit használatával indítja el. A kreditek alaphelyzetbe állítja a havonta a 9. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Nagyvállalati Szerződés (EA) előfizetések számlázási tulajdonjogának átruházása

A vállalati rendszergazda előfizetések tulajdonjogát átadhatja belül regisztrációs fiókok közötti. További információkért lásd: [Transfer fiók tulajdonjogának](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) az EA-portálon.

<a id="CSP"></a>

## <a name="next-steps-after-accepting-billing-ownership"></a>A számlázási tulajdonosi elfogadásával követő lépések

Ha az Azure-előfizetések számlázási tulajdonjogának elfogadott, javasoljuk, tekintse át a következő lépések:

1. Tekintse át, és más RBAC-szerepkörök, a szolgáltatás-rendszergazda és Társrendszergazdák frissítése. További tudnivalókért lásd: [hozzáadása vagy módosítása az Azure-előfizetés rendszergazdái](billing-add-change-azure-subscription-administrator.md) és [rbac-RÓL és az Azure portal-hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md).
1. Ez az előfizetés-szolgáltatások, beleértve a társított hitelesítő adatok frissítése:
   1. Felügyeleti tanúsítvány, amely a felhasználó-előfizetések erőforrásaihoz való rendszergazdai engedélyeket kap. További információkért lásd: [létrehozása és feltöltése, egy felügyeleti tanúsítvány az Azure-hoz](../cloud-services/cloud-services-certs-create.md)
   1. Hozzáférési kulcsok szolgáltatásokhoz, például tárolási. További információkért lásd: [tudnivalók az Azure storage-fiókok](../storage/common/storage-create-storage-account.md)
   1. Távoli hozzáférési hitelesítő adatok, szolgáltatások, például az Azure Virtual Machines.
1. Ha dolgozik egy partnerrel, fontolja meg a Partnerazonosítót. Ebben az előfizetésben frissítése. A partnerazonosító Társítását a frissítheti a [az Azure portal](https://portal.azure.com). További információkért lásd: [Partnerazonosító csatolása az Azure-fiókok](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Támogatott előfizetéstípusok

Az előfizetés átadása az Azure Portalon érhető el az alább felsorolt előfizetés esetében. Jelenleg az átvitel nem támogatott a [ingyenes próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p/) vagy [Azure a nyílt (aio-ra)](https://azure.microsoft.com/offers/ms-azr-0111p/) előfizetések. Megkerülő megoldásért tekintse meg a [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md). Át más előfizetésekre, például az [szponzorálási](https://azure.microsoft.com/offers/ms-azr-0036p/) vagy támogatási csomagok, [forduljon az Azure-támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [A Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [A Visual Studio Enterprise (MPN) előfizetők](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [MSDN-platformok](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [A Microsoft Azure-csomagot](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [A nagyvállalati szerződések portálján](#EA).

\*\* Csak során létrehozott jelentkezzen be az Azure webhelyén fiókok esetében támogatott. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Gyakori kérdések (GYIK) küldők számára

Felhasználók, akik az Azure-előfizetések számlázási tulajdonjogának átadása másik fiók át ezeket a gyakori kérdések vonatkoznak.

### <a name="whoisaa"></a> Számlázási rendszergazda az ki?

Egy számlázási rendszergazda egy olyan személy, aki a fiók számlázási kezelésére jogosult. Engedélyezett-e a számlázási eléréséhez a [az Azure portal](https://portal.azure.com) , és végezze el a számlázási különböző feladatokat, előfizetések, a nézet és a használatalapú számlákat és a frissítés létrehozása fizetési módok.

Az előfizetések, amelynek Ön a számlázási rendszergazda azonosításához használja a következő lépéseket:

1. Látogasson el a [Költségkezelés + számlázás lapján az Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Válassza ki **előfizetések** a bal oldali panelen. A hozzáférést, attól függően szükség lehet egy számlázási hatókör majd válassza ki és **előfizetések** vagy **Azure-előfizetések**
1. Az előfizetések lap, amelyekhez egy számlázási rendszergazda le az összes előfizetés listája.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Nem minden átviszi a? Erőforráscsoportok, a virtuális gépek, a lemezek és a többi futó szolgáltatások többek között?

Az erőforrások, például virtuális gépek, a lemezek és a websites adatforgalom az új fiókhoz. Azonban, előfizetés-fiókjába egy másik Azure AD-ben átvitele esetén a bérlői, bármely [rendszergazdai szerepkörök](billing-add-change-azure-subscription-administrator.md) és [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/role-assignments-portal.md) az előfizetéshez a hozzárendeléseket [nem átviteli](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Ezenkívül [alkalmazásregisztrációk](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) és a többi bérlő-specifikus szolgáltatások mellett az előfizetés átadása nem történik meg.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Tudok át a tulajdonjogot egy másik országban fiókja?
Sajnos ország közötti adatforgalom nem hajtható végre az Azure Portalon. Több országban, az Ön előfizetésének átadására [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>A két fiók rendszergazda vagyok. Áthelyezhetők-e egy előfizetés saját fiókok közül egy másikra?
Igen, előfizetés átadhatja a fiókok között. A fiókok adatbázistáblákhoz minősülnek két különböző felhasználói fiókok, így a fenti lépéseket használhatja az előfizetések közötti a fiókokhoz.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Az előfizetés átadása bármely szolgáltatás állásidőt eredményez?

Előfizetését átadhatja egy felhasználó a az Azure AD-bérlőhöz, van-e az erőforrásokat, az előfizetés nincs hatással.  Azonban ha az előfizetés átvitele egy másik felhasználó bérlői, felhasználók, csoportok és szolgáltatásnevek, akik [szerepköralapú hozzáférés-(vezérléssel RBAC)](../role-based-access-control/overview.md) kezeléséhez az előfizetésben található erőforrások elveszítik a hozzáférésüket. 

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>A címzett rendelkezik a használati és számlázási előzményei a hozzáférést?

A múlt havi költség-előfizetéséhez tartozó mindössze a címzett számára elérhető adatait. A többi a használati és számlázási előzmények nem viszi át az előfizetést

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Hogyan migrálhatok adatok és szolgáltatások az Azure-előfizetésem az új előfizetésbe?

Ha az előfizetés tulajdonjogának nem lehet manuálisan áttelepítheti az erőforrások. Lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Ha a Visual Studio vagy a Microsoft Partner Network előfizetést átvinni, nem kreditjeim átviszi az előfizetésnek az új fiók?

A kredit nem, az új fiók nem érhető el. A felhasználó, aki az átviteli kérelmet fogad el kell rendelkeznie a Visual Studio licenccel, fogadja el az átviteli kérelmet. Az előfizetés használja a Visual Studio-kredit, amely a felhasználói fiók érhető el. További információkért lásd: [átvitele a Visual Studio, a Microsoft Partner Network (MPN) és a használatalapú fejlesztési és tesztelési előfizetések](#transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions)


## <a name="frequently-asked-questions-faq-for-recipients"></a>Gyakori kérdések (GYIK) címzettek számára

Ezeket a gyakori kérdések vonatkozik a felhasználók, akik a számlázási tulajdonosi jogokat egy másik fiókba az Azure-előfizetés keresztül tart.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Egy másik fiókból előfizetés számlázási tulajdonjogának átvétele szeretnék, ha abban, hogy a fiók továbbra is, hogy az erőforrásokhoz való hozzáférés tegye a felhasználók?

Ha az előfizetés átkerült egy fiókot az Azure AD-bérlőhöz, minden felhasználók, csoportok és szolgáltatásnevek, akik [szerepköralapú hozzáférés-(vezérléssel RBAC)](../role-based-access-control/overview.md) kezeléséhez az előfizetésben található erőforrások megőrzése a hozzáférésüket. Az előfizetés az RBAC-hozzáféréssel rendelkező felhasználók megtekintéséhez használja az alábbi lépéseket:

1. Látogasson el a [az Azure portal előfizetés oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Ellenőrizze, és válassza ki a kívánt előfizetés kiválasztásához **hozzáférés-vezérlés (IAM)** a bal oldali panelen.
1. Válassza ki **szerepkör-hozzárendelések** az oldal tetején. A szerepkör-hozzárendeléseket lapon RBAC-hozzáféréssel rendelkezik az előfizetés összes felhasználót kilistázza.

Ha az előfizetés átkerült egy másik Azure AD-bérlővel, minden felhasználók, csoportok és szolgáltatásnevek, akik a fiók [szerepköralapú hozzáférés-(vezérléssel RBAC)](../role-based-access-control/overview.md) kezeléséhez az előfizetésben található erőforrások elveszítik a hozzáférésüket. Azonban akkor is, ha azok többé nincs RBAC-hozzáféréssel, előfordulhat, hogy továbbra is hozzáféréssel rendelkeznek az előfizetés révén néhány biztonsági mechanizmusok, beleértve:

* Felügyeleti tanúsítvány, amely a felhasználó-előfizetések erőforrásaihoz való rendszergazdai engedélyeket kap. További információkért lásd: [létrehozása és feltöltése az Azure felügyeleti tanúsítvánnyal](../cloud-services/cloud-services-certs-create.md).
* Hozzáférési kulcsok szolgáltatásokhoz, például tárolási. További információkat az [Azure Storage-fiókokkal kapcsolatos tudnivalókat](../storage/common/storage-create-storage-account.md) ismertető témakörben olvashat.
* Távoli hozzáférési hitelesítő adatok, szolgáltatások, például az Azure Virtual Machines.

Ha a címzett kell korlátozni, azok erőforrásokhoz való hozzáférést, akkor vegye figyelembe a szolgáltatáshoz társított titkos kulcsok frissítése. A legtöbb erőforrást az alábbi lépéseket követve frissítheti:

  1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
  2. A központ menüben válassza ki a **összes erőforrás**.
  3. Válassza ki az erőforrást.
  4. Az erőforrás oldalán kattintson a **beállítások**. Itt megtekintheti, és a meglévő titkos kódok frissítése.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Ha e átvenni a számlázási tulajdonosi jogait közepén az elszámolási időszakban előfizetést, kell fizetnie, a teljes elszámolási időszakban?

A fiók idő adatátviteli és újabb verziók esetében a jelentett használat kifizetni a felelős. Előfordulhat, hogy egyes használati került sor átvitele előtt, de ezt követően lett jelentve. A használati a fiókhoz tartozó számlázási tartalmazza.

### <a name="can-i-use-a-different-payment-method"></a>Használható egy másik fizetési mód?

Igen. Közben az átviteli kérelem elfogadása, kiválaszthat egy meglévő fizetési módot, amely csatlakoztatva van a fiókhoz, vagy adjon hozzá egy új fizetési mód.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a id="no-button"></a> Miért nem látom, hogy az "Előfizetés átadására" gomb?

Sajnos az önkiszolgáló előfizetés, átvitel nem érhető el, az Ön számlázási fiókjához. Jelenleg nem támogatjuk meg a nagyvállalati szerződés (EA) fiók az Azure Portal számlázási tulajdonjogának átadása. Ezen felül feldolgozása révén a Microsoft értékesítési közben létrehozott Ügyfélszerződéséhez Microsoft-fiókok nem támogatják előfizetések számlázási tulajdonjogának átadásáról. 

### <a id="no-button"></a> Az előfizetés miért nem írja be a támogatási átvitel? 

Sajnos az előfizetések nem minden típusú támogatja a számlázási tulajdonjogának átruházását. Adatátvitel támogató előfizetés-típusok listájának megtekintése: [előfizetéstípusok támogatott](#supported-subscription-types)

### <a id="no-button"></a> Miért kapok "hozzáférés megtagadva" hiba jelenik meg az előfizetés számlázási tulajdonjogának? 

Ez a hiba látni fogja, ha szeretne egy Microsoft Azure csomag előfizetés átadására, és nem rendelkezik a szükséges engedéllyel. Egy Microsoft Azure-csomag előfizetésének átadására, meg kell lenniük tulajdonosának vagy közreműködőjének, amelyhez az előfizetés elszámolási számla szakaszra. További információkért lásd: [számla szakasz-előfizetések kezelése](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- Tekintse át, és más RBAC-szerepkörök, a szolgáltatás-rendszergazda és Társrendszergazdák frissítése. További tudnivalókért lásd: [hozzáadása vagy módosítása az Azure-előfizetés rendszergazdái](billing-add-change-azure-subscription-administrator.md) és [rbac-RÓL és az Azure portal-hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md).
