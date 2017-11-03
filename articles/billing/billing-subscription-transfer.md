---
title: "Azure-előfizetés tulajdonjogának átruházása másik fiókra |} Microsoft Docs"
description: "Ismerteti, hogyan lehet egy Azure-előfizetés átvitele egy másik felhasználó, és a folyamat kapcsolatos néhány gyakran ismételt kérdések (GYIK)"
keywords: "azure-előfizetésre, az azure átviteli előfizetés átvitele, azure-előfizetés áthelyezése egy másik fiókot, azure új előfizetés tulajdonos, azure-előfizetés átvitele egy másik fiókkal"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/15/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9beef44b3fbaf26d49757544f32b97c7ef2cf425
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Egy másik fiókot az Azure-előfizetés tulajdonjogának átruházása

Az előfizetés egy másik felhasználónak az Account Center a vihetők át. E szolgáltatás használatával az előfizetés számlázási tulajdonosi másnak keresztül oldalon, a bejelentkezési fiók módosítása vagy előfizetés áthelyezése egy másik címtárhoz. Az előfizetés egy másik ajánlatra módosításához lásd [az Azure-előfizetéshez Váltás másik ajánlatra](billing-how-to-switch-azure-offer.md).

> [!IMPORTANT]
> 
> Jelenleg nem támogatjuk előfizetés átadásáról ingyenes próbaverzióra vagy [Azure a nyitott Nyomtatókategóriák](https://azure.microsoft.com/offers/ms-azr-0111p/) előfizetések. Megkerülő megoldásért tekintse meg a [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

<a id="supported"></a>

## <a name="whats-supported"></a>Támogatott források és műveletek:

Önkiszolgáló előfizetés átadása az ajánlatok vagy a következő táblázatban felsorolt előfizetéstípusok érhető el. Más előfizetések például átvitelére [szponzorálás](https://azure.microsoft.com/offers/ms-azr-0036p/) támogatja a csomagokat, vagy [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

| Csomag neve                                                                             | Csomag száma |
|----------------------------------------------------------------------------------------|--------------|
| [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*|MS-AZR - 0017P        |
| [A Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)          | MS-AZR - 0025P        |
| [MSDN platformok](https://azure.microsoft.com/offers/ms-azr-0062p/)                     | MS-AZR - 0062P        |
| [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/)                      | MS-AZR - 0003P        |
| [Fejlesztés/tesztelés – használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0023p/)             | MS-AZR - 0023P        |
| [A Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)           | MS-AZR - 0063P        |
| [A Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR - 0064P        |
| [A Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)         | MS-AZR - 0059P        |
| [A Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)    | MS-AZR - 0060P        |

\*[Keresztül EA portál](#EA)

## <a name="transfer-ownership-of-an-azure-subscription"></a>Azure-előfizetés tulajdonjogának átruházása

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Jelentkezzen be a(z) [Azure Account Center](https://account.windowsazure.com/Subscriptions) fiók rendszergazdaként. Ki-e a Fiókadminisztrátor az előfizetés, lásd: [gyakran ismételt kérdések](#faq).

1. Válassza ki az előfizetés átvitele.

1. Győződjön meg arról, hogy az előfizetés nem jogosult az önkiszolgáló átviteli ellenőrzésével a **kínálnak** és **kínálnak azonosító** rendelkező a [támogatott ajánlatok lista](#supported).

   ![Ellenőrizze az előfizetés az Account Center ajánlat Azonosítóját](./media/billing-subscription-transfer/image0.png)
1. Kattintson az **Előfizetés átadása** elemre.

   ![Azure-fiók előfizetések lap](./media/billing-subscription-transfer/image1.png)
1. Adja meg a címzettet.

   ![Átviteli előfizetés párbeszédpanel](./media/billing-subscription-transfer/image2.PNG)
1. A címzett automatikusan kapni fog egy elfogadási hivatkozást tartalmazó e-mailt.

   ![Előfizetés átviteli e-mail címzett](./media/billing-subscription-transfer/image3.png)
1. A címzett a hivatkozásra való kattintás után követnie kell az utasításokat, többek között a fizetési információk megadásával.

   ![Első előfizetési átviteli weblapot](./media/billing-subscription-transfer/image4.png)

   ![Második előfizetési átviteli weblapot](./media/billing-subscription-transfer/image5.png)
1. Sikerült! Az Előfizetés most át.

<a id="EA"></a>

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>A nagyvállalati szerződés (EA) ügyfelek előfizetés tulajdonjogának átruházása

A vállalati rendszergazda vihetők át a beléptetési belül előfizetések tulajdonjogát. Első lépésként tekintse meg a [átviteli fiók tulajdonjogának](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) a EA portálon.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Az előfizetés tulajdonosa elfogadása követő lépések

1. A Fiókadminisztrátor is. Tekintse át, és frissítse a szolgáltatás-rendszergazda és a Társadminisztrátorok. A rendszergazdák kezelése a [a klasszikus Azure portálon](https://manage.windowsazure.com) beállításainak megnyitásával. [További tudnivalók a rendszergazdai szerepkörökről](billing-add-change-azure-subscription-administrator.md).
1. Szerepköralapú hozzáférés-vezérlést (RBAC) használata az előfizetés és -szolgáltatásokra. Látogasson el az [Azure Portalra](https://portal.azure.com). [További tudnivalók az RBAC](../active-directory/role-based-access-control-configure.md)
1. Frissítse a hitelesítő adatokat, beleértve az előfizetés szolgáltatásokkal:
   1. A felhasználó előfizetéshez kapcsolódó erőforrásokat rendszergazdai jogosultságokat adjon felügyeleti tanúsítványok. További információkért lásd: [létrehozása és felügyeleti az Azure-tanúsítvány feltöltése](../cloud-services/cloud-services-certs-create.md)
   1. Tárelérési kulcsok szolgáltatások, például tároló. További információkért lásd: [tudnivalók az Azure storage-fiókok](../storage/common/storage-create-storage-account.md)
   1. Távoli hozzáférési hitelesítő adatok, szolgáltatások, például Azure virtuális gépeken. 
1. [Az előfizetés számlázási riasztások frissítése](billing-set-up-alerts.md) , a [Azure Account Center](https://account.windowsazure.com/Subscriptions). 
1. Ha a partnerrel dolgozik, fontolja meg a Partnerazonosítót. Ebben az előfizetésben. A partner Partnerazonosítóját, a frissítéshez a [Azure Account Center](https://account.windowsazure.com/Subscriptions).

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

### <a name="whoisaa"></a>A fiókadminisztrátor az előfizetés?

A Fiókadminisztrátor az a személy, aki regisztrált a vagy vásárolt Azure-előfizetést. Engedélyezett-e hozzáférni a [Account Center](https://account.azure.com/Subscriptions) és -előfizetések létrehozása, szakítsa meg a előfizetések, az előfizetés számlázási módosítása, vagy módosítsa a szolgáltatás-rendszergazda például különböző felügyeleti feladatok elvégzésére. Ha nem biztos, aki a fiókadminisztrátor az előfizetéshez, tegye a következőket megállapítása.

1. Látogasson el a [Azure-portálon az előfizetések oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki az előfizetést, és ellenőrizze, majd keresse meg a **beállítások**.
1. Válassza ki **tulajdonságok**. A fiókadminisztrátor az előfizetés jelenik meg a **Fiókadminisztrátor** mezőbe.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Nem minden átviszi a? Többek között az erőforráscsoportok, a virtuális gépek, a lemezek és a többi futó szolgáltatások?

Igen, az erőforrások, mint például virtuális gépek, a lemezek és a webhelyek átvitelét az új tulajdonos. Azonban bármely [rendszergazdai szerepkörök](billing-add-change-azure-subscription-administrator.md) és [szerepköralapú hozzáférés-vezérlés (RBAC)](../active-directory/role-based-access-control-configure.md) létrehozott házirendeket nem veszi át más címtárak között.

### <a id="no-button"></a>Miért nem látom, hogy az "Előfizetés átvitele" gombra?

Sajnos a önkiszolgáló előfizetés átvitele nem érhető el az ajánlat vagy ország. Az előfizetés átvitele [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Az előfizetés átadása szolgáltatás leállási eredményez?

Nincs hatással a szolgáltatás. Az előfizetés átvitele visszavonja az előfizetés aktuális rendszergazdai fiók alatt, és a címzett fiók alatt egy előfizetést hoz létre. Az új előfizetés az alapul szolgáló Azure-szolgáltatásokhoz való hozzá rendelve. Az előfizetés-azonosító változatlan marad.

### <a name="how-do-i-use-this-process-to-change-the-directory-for-subscription"></a>Hogyan használjam ezt a folyamatot előfizetéshez tartozó könyvtárat szeretne váltani?

Azure-előfizetéssel, amely a Fiókadminisztrátor tartozik a címtárban jön létre. Ha könyvtárat szeretne váltani, vigye át az előfizetést a célkönyvtárat egy felhasználói fiókhoz. Átadás elfogadásához a lépéseket, hogy a felhasználó befejeződésekor az előfizetés automatikusan átkerül a célkönyvtárat.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-organization-do-they-continue-to-have-access-to-my-resources"></a>Ha I átveszi egy másik szervezet előfizetés számlázási tulajdonosi jogokat, tegye azok továbbra is az erőforrások eléréséhez?

Ha az előfizetés át egy másik bérlőnek, az előző tenanthoz társított felhasználók elveszíti a hozzáférést az előfizetéshez. Akkor is, ha a felhasználó nem egy szolgáltatás-rendszergazdai vagy társadminisztrátori többé, előfordulhat, hogy továbbra is hozzáférhetnek az előfizetéseket a más védelmi mechanizmus, beleértve:

* A felhasználó előfizetéshez kapcsolódó erőforrásokat rendszergazdai jogosultságokat adjon felügyeleti tanúsítványok. További információkért lásd: [létrehozása és feltöltése az Azure felügyeleti tanúsítvánnyal](../cloud-services/cloud-services-certs-create.md).
* Tárelérési kulcsok szolgáltatások, például tároló. További információkért lásd: [tudnivalók az Azure storage-fiókok](../storage/common/storage-create-storage-account.md).
* Távoli hozzáférési hitelesítő adatok, szolgáltatások, például Azure virtuális gépeken.

Ha a címzett kell korlátozni az erőforrásokhoz való hozzáférést, akkor vegye figyelembe bármely a szolgáltatáshoz társított titkos kulcsok frissítése. A legtöbb erőforrást frissíthetik az alábbi lépéseket követve:

  1. Nyissa meg az [Azure Portal](https://portal.azure.com).
  2. A központ menüben válassza ki a **összes erőforrás**.
  3. Válassza ki az erőforrást.
  4. Az erőforrás panel kattintson **beállítások**. Itt megtekintheti, és frissítse a meglévő titkos kulcsok.

### <a name="if-i-transfer-the-subscription-in-the-middle-of-the-billing-cycle-does-the-recipient-pay-for-the-entire-billing-cycle"></a>Ha az előfizetés számlázási ciklus közepén átvinni, nem a címzett fizetési – a teljes számlázási ciklus?

A küldő bármilyen, hogy befejeződött-e az átvitel pontig jelentett használati felelős. A címzett nem az újabb verziók esetében az átviteli időt jelentett használati felelős. Előfordulhat, hogy néhány használati átvitel előtt történt, de ezek után történt. A használati a címzett számlázási tartalmazza.

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>A címzett rendelkezik használati és számlázási előzmények elérésére?

  A címzett számára elérhető összes információ mennyisége az utolsó számlázási, vagy ha az előfizetés áthelyezése történt, az első számlázási előtt létrehozott, az aktuális egyenleg. A többi a használati és számlázási előzmények nem viszi át az előfizetéshez.

### <a name="can-the-offer-be-changed-during-a-transfer"></a>Az ajánlat módosítható egy átvitel közben?

Az ajánlat azonosnak kell maradnia. Ha módosítani szeretné az ajánlatot, lásd: [az Azure-előfizetéshez Váltás másik ajánlatra](billing-how-to-switch-azure-offer.md).

### <a name="can-i-transfer-a-subscription-to-a-user-account-in-another-country"></a>Átvihető előfizetés egy másik országban egy felhasználói fiókot?

Egy előfizetés átvitele egy felhasználói fiókhoz, egy másik országban nem, nem támogatott. A címzett felhasználói fióknak ugyanabban az országban kell lennie.

### <a name="can-the-recipient-use-a-different-payment-method"></a>A címzett használhatja egy másik fizetési módot?

Igen. De az előfizetés számlázási előzmények be van-e osztani két fiókot.  

### <a name="is-the-payment-method-impacted-after-i-transferred-an-azure-subscription"></a>A fizetési módot feladatátvétele után az Azure-előfizetés átvitele?

Az előfizetés átadása elfogadásához hitelkártya vagy hasonló fizetési mód meg kell adni az előfizetés fizetési. Például ha Bálint Jane előfizetés továbbítja, és Jane fogadja el az átvitelt, Jane biztosítania kell a fizetési módot, az előfizetés fizetési. Az átvitel befejezése után, Jane az előfizetés nem Bob lesz számlázva.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Hogyan adatait és szolgáltatásait saját Azure-előfizetés át az új előfizetés?

Ha az előfizetés tulajdonosa nem tudja áthelyezni, manuálisan áttelepítheti az erőforrások. Lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.