---
title: Az Azure-előfizetés tulajdonjogának átruházása másik fiókra |} A Microsoft Docs
description: Ismerteti, hogyan viheti át az Azure-előfizetés és a egy másik felhasználó a folyamattal kapcsolatos néhány gyakori kérdések (GYIK)
keywords: átviteli előfizetés azure-előfizetéssel, azure, azure-előfizetés áthelyezése egy másik azure-fiók módosítása előfizetés tulajdonosa, azure-előfizetés átruházása másik fiókra
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d9af3b1b9c267bc09b370717913722a6c53ad5a1
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678819"
---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Azure-előfizetés tulajdonjogának átruházása másik fiókra

Az előfizetés egy másik felhasználó módosíthatja a Fiókadminisztrátor és kézi előfizetés számlázási tulajdonjogát át az Account Center webhelyen ruházhatja át. Ha módosítani szeretné az előfizetés egy másik ajánlatra, lásd: [Váltás másik ajánlatra az Azure-előfizetés](billing-how-to-switch-azure-offer.md).

> [!IMPORTANT]
> 
> Új Azure AD-előfizetés átvitele esetén a bérlőben, az összes szerepkör-hozzárendelések [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) véglegesen törlődnek a forrás-bérlőből, és nem lesznek áttelepítve a cél-bérlőhöz. Akkor is, manuálisan újra létrehozni a felügyelt identitások az Azure-erőforrásokhoz. További információkért lásd: [– gyakori kérdések és ismert problémái felügyelt identitások](../active-directory/managed-identities-azure-resources/known-issues.md).

## <a name="transfer-ownership-of-an-azure-subscription"></a>Azure-előfizetés tulajdonjogának átruházása

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Jelentkezzen be a következő [Azure Account Center](https://account.windowsazure.com/Subscriptions) fiókadminisztrátorként Ismerje meg, hogy ki-e az előfizetés fiókjának rendszergazdája, lásd: [– gyakori kérdések](#faq).

1. Válassza ki az előfizetés átvitele.

1. Győződjön meg arról, hogy az előfizetés nem jogosult önkiszolgáló átvitel ellenőrzésével a **ajánlat** és **Ajánlatazonosító** együtt a [támogatott ajánlatok listája](#supported).

   ![Ellenőrizze az Account Center webhelyen előfizetés ajánlat azonosítója](./media/billing-subscription-transfer/image0.png)
1. Kattintson az **Előfizetés átadása** elemre.

   ![Azure-fiókba az előfizetések lap](./media/billing-subscription-transfer/image1.png)
1. Adja meg a címzettet.

   > [!IMPORTANT]
   > 
   > Új Azure AD-előfizetés átvitele esetén a bérlőben, az összes szerepkör-hozzárendelések [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) véglegesen törlődnek a forrás-bérlőből, és nem lesznek áttelepítve a cél-bérlőhöz. Akkor is, manuálisan újra létrehozni a felügyelt identitások az Azure-erőforrásokhoz. További információkért lásd: [– gyakori kérdések és ismert problémái felügyelt identitások](../active-directory/managed-identities-azure-resources/known-issues.md).

   ![Átviteli előfizetés párbeszédpanel](./media/billing-subscription-transfer/image2.PNG)

1. A címzett automatikusan kapni fog egy elfogadási hivatkozást tartalmazó e-mailt.

   ![Előfizetés átadási e-mail címzett](./media/billing-subscription-transfer/image3.png)
1. A címzett a hivatkozásra való kattintás után követnie kell az utasításokat, többek között a fizetési információk megadásával.

   ![Első előfizetési átadás weblapot](./media/billing-subscription-transfer/image4.png)

   ![Második előfizetés-átadási weblap](./media/billing-subscription-transfer/image5.png)
1. Sikerült! Az előfizetések átvitele esetén.

<a id="EA"></a>

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>Előfizetés tulajdonjogának átadása az ügyfelek a nagyvállalati szerződés (EA)

A vállalati adminisztrátor átadhatja az előfizetések tulajdonjogát egy adott regisztráción belül. Első lépésként lásd [Transfer fiók tulajdonjogának](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) az EA-portálon.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Egy előfizetés tulajdonjogának elfogadásával követő lépések

1. Most már áll a fiók rendszergazdája. Tekintse át, és más RBAC-szerepkörök, a szolgáltatás-rendszergazda és Társrendszergazdák frissítése. További tudnivalókért lásd: [hozzáadása vagy módosítása az Azure-előfizetés rendszergazdái](billing-add-change-azure-subscription-administrator.md) és [rbac-RÓL és az Azure portal-hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md).
1. Ez az előfizetés-szolgáltatások, beleértve a társított hitelesítő adatok frissítése:
   1. Felügyeleti tanúsítvány, amely a felhasználó-előfizetések erőforrásaihoz való rendszergazdai engedélyeket kap. További információkért lásd: [létrehozása és feltöltése, egy felügyeleti tanúsítvány az Azure-hoz](../cloud-services/cloud-services-certs-create.md)
   1. Hozzáférési kulcsok szolgáltatásokhoz, például tárolási. További információkért lásd: [tudnivalók az Azure storage-fiókok](../storage/common/storage-create-storage-account.md)
   1. Távoli hozzáférési hitelesítő adatok, szolgáltatások, például az Azure Virtual Machines. 
1. Ha dolgozik egy partnerrel, fontolja meg a Partnerazonosítót. Ebben az előfizetésben frissítése. A partnerazonosító Társítását a frissítheti a [az Azure portal](https://portal.azure.com).

<a id="supported"></a>

## <a name="whats-supported"></a>Mit támogat:

Önkiszolgáló előfizetés átadása az ajánlatok és az alábbi táblázatban szereplő előfizetés-típusok érhető el. Jelenleg nem tudja áthelyezni egy ingyenes próbaverzióra vagy [Azure a nyílt (aio-ra)](https://azure.microsoft.com/offers/ms-azr-0111p/) előfizetések. Megkerülő megoldásért tekintse meg a [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md). Át más előfizetésekre, például az [szponzorálási](https://azure.microsoft.com/offers/ms-azr-0036p/) vagy támogatási csomagok, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

| Csomag neve                                                                             | Csomag száma |
|----------------------------------------------------------------------------------------|--------------|
| [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*|MS-AZR-0017P        |
| [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)          | MS-AZR-0025P        |
| [MSDN-platformok](https://azure.microsoft.com/offers/ms-azr-0062p/)                     | MS-AZR-0062P        |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/)                      | MS-AZR-0003P        |
| [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)             | MS-AZR-0023P        |
| [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)           | MS-AZR-0063P        |
| [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P        |
| [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)         | MS-AZR-0059P        |
| [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)    | MS-AZR-0060P        |

\* [A nagyvállalati szerződések portálján](#EA)

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

### <a name="whoisaa"></a> Az előfizetés Fiókadminisztrátori fiókjával az számára?

A fiók rendszergazdája az a személy, aki regisztrált vagy az Azure-előfizetést vásárolt. Engedélyezett-e hozzáférni a [Account Center](https://account.azure.com/Subscriptions) és -előfizetések létrehozása, előfizetések megszakítja, módosítsa az előfizetéshez tartozó számlázási vagy a szolgáltatás-rendszergazda módosíthatja, különböző felügyeleti feladatok elvégzésére. Ha nem biztos benne, hogy az adott előfizetésnek ki a fiókadminisztrátora, az alábbi lépésekkel kiderítheti.

1. Nyissa meg [az Azure Portal Előfizetések oldalát](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki az előfizetést, gombra, majd keresse meg a kívánt **beállítások**.
1. Válassza ki **tulajdonságok**. Az előfizetés fiókadminisztrátori fiókjával jelenik meg a **Fiókadminisztrátor** mezőbe.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Nem minden átviszi a? Erőforráscsoportok, a virtuális gépek, a lemezek és a többi futó szolgáltatások többek között?

Az erőforrások, például virtuális gépek, a lemezek és a websites átvitelét az új tulajdonos. Azonban bármely [rendszergazdai szerepkörök](billing-add-change-azure-subscription-administrator.md) és [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/role-assignments-portal.md) szabályzatok beállítását nem veszi át az eltérő címtárak között. Ezenkívül [alkalmazásregisztrációk](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) és a többi bérlő-specifikus szolgáltatások mentén átadása nem történik meg.

### <a id="no-button"></a> Miért nem látom, hogy az "Előfizetés átadására" gomb?

Sajnos az önkiszolgáló előfizetés-átadás nem érhető el az ajánlat vagy ország. Az előfizetés átadására irányuló [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Az előfizetés átadása bármely szolgáltatás állásidőt eredményez?

Ez nincs hatással a szolgáltatáshoz. Az előfizetés átadását megszakítja az előfizetés alapján a jelenlegi fiókot rendszergazdai és a egy előfizetést a fiókja alatt hoz létre. Az új előfizetés társítva az alapul szolgáló Azure-szolgáltatások. Az előfizetés-azonosító változatlan marad.

### <a name="how-do-i-use-this-process-to-change-the-directory-for-subscription"></a>Hogyan használhatom ezt a folyamatot az előfizetéshez tartozó címtár módosítása?

Azure-előfizetés jön létre a címtárban, a fiók rendszergazdája tartozik. A könyvtár módosításához vigye át az előfizetést egy felhasználói fiókot a célként megadott könyvtárban. Az átadás elfogadásához lépéseket, hogy a felhasználó befejezésekor az előfizetést automatikusan helyezett át. a céloldali könyvtár.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-organization-do-they-continue-to-have-access-to-my-resources"></a>Egy másik szervezet előfizetés számlázási tulajdonjogának átvétele szeretnék, ha tegye azokat továbbra is, hogy az erőforrásokhoz való hozzáférést?

Ha az előfizetés átkerült egy másik bérlőhöz, a felhasználók az előző tenanthoz társított elveszíti a hozzáférést az előfizetéshez. Akkor is, ha a felhasználó már nem egy szolgáltatás-rendszergazdai vagy társadminisztrátori, akkor előfordulhat, hogy továbbra is hozzáférhetnek keresztül más biztonsági mechanizmusok, beleértve az előfizetéshez:

* Felügyeleti tanúsítvány, amely a felhasználó-előfizetések erőforrásaihoz való rendszergazdai engedélyeket kap. További információkért lásd: [létrehozása és feltöltése az Azure felügyeleti tanúsítvánnyal](../cloud-services/cloud-services-certs-create.md).
* Hozzáférési kulcsok szolgáltatásokhoz, például tárolási. További információkat az [Azure Storage-fiókokkal kapcsolatos tudnivalókat](../storage/common/storage-create-storage-account.md) ismertető témakörben olvashat.
* Távoli hozzáférési hitelesítő adatok, szolgáltatások, például az Azure Virtual Machines.

Ha a címzett kell azok erőforrásokhoz való hozzáférés korlátozása, akkor vegye figyelembe a szolgáltatáshoz társított titkos kulcsok frissítése. A legtöbb erőforrást az alábbi lépéseket követve frissítheti:

  1. Nyissa meg az [Azure Portal](https://portal.azure.com).
  2. A központ menüben válassza ki a **összes erőforrás**.
  3. Válassza ki az erőforrást.
  4. Az erőforrás panelen kattintson a **beállítások**. Itt megtekintheti, és a meglévő titkos kódok frissítése.

### <a name="if-i-transfer-the-subscription-in-the-middle-of-the-billing-cycle-does-the-recipient-pay-for-the-entire-billing-cycle"></a>Ha az a számlázási ciklus közepén vált előfizetést átvinni, nem a címzett a teljes számlázás használatalapú ciklus?

A küldő fizetési addig a pontig, hogy befejeződött-e az átvitel jelentett használatot a felelős. A címzett idő adatátviteli és újabb verziók esetében a jelentett használat felelős. Előfordulhat, hogy egyes használati került sor átvitele előtt, de ezt követően lett jelentve. A használati a címzett számlán szerepel.

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>A címzett rendelkezik a használati és számlázási előzményei a hozzáférést?

  Mindössze az előző számla összegét a címzett számára elérhető adatait, vagy ha az előfizetés átruházták, az első számlázási előtt létrehozott, az aktuális egyenleg. A többi a használati és számlázási előzmények nem viszi át az előfizetéshez.

### <a name="can-the-offer-be-changed-during-a-transfer"></a>Az ajánlat módosítható egy átvitel közben?

Az ajánlat azonosnak kell maradnia. Ha módosítani szeretné az ajánlatot, tekintse meg a [Váltás másik ajánlatra az Azure-előfizetés](billing-how-to-switch-azure-offer.md).

### <a name="can-i-transfer-a-subscription-to-a-user-account-in-another-country"></a>Átvihető-e egy előfizetés egy felhasználói fiókot egy másik országban?

Előfizetés átvitele egy felhasználói fiókot egy másik országban nem, nem támogatott. A címzett felhasználói fióknak ugyanabban az országban kell lennie.

### <a name="can-the-recipient-use-a-different-payment-method"></a>A címzett használható egy másik fizetési mód?

Igen. De az előfizetés számlázási előzmények két fiók között van felosztva.  

### <a name="is-the-payment-method-impacted-after-i-transferred-an-azure-subscription"></a>A fizetési módot kihatással van szükségem egy Azure-előfizetés átvitele után?

Egy előfizetés-átadás elfogadásához hitelkártya vagy hasonló fizetési módot kell megadni az előfizetés. Például ha Bob Jane továbbítja egy előfizetést, és Jane fogadja el az átvitelt, Jane az előfizetés egy fizetési módot kell megadni. Az átvitel befejezése után az előfizetés nem Bob Jane alapján.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Hogyan migrálhatok adatok és szolgáltatások az Azure-előfizetésem az új előfizetésbe?

Ha az előfizetés tulajdonjogának nem lehet manuálisan áttelepítheti az erőforrások. Lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).