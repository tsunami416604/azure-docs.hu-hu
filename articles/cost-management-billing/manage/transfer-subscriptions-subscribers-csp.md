---
title: Azure-előfizetések átvitele az előfizetők és a CSP-k között
description: Ismerje meg, hogyan végezhető el az Azure-előfizetések átvitele az előfizetők és a CSP-k között.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 52dd9d2f6299f8d574934e7baec54333d2ffc0c8
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997574"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Azure-előfizetések átvitele az előfizetők és a CSP-k között

Ez a cikk magas szintű lépéseket mutat be az Azure-előfizetések felhőszolgáltatói (CSP-) partnerek és ügyfeleik közötti átviteléhez.

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>Nagyvállalati szerződés átvitele CSP-partner részére

A felhőszolgáltató [Azure-beli szakértői felügyelt szolgáltató (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) tanúsítvánnyal rendelkező, közvetlen számlázási partnerei kérvényezhetik az Azure-előfizetések átvitelét olyan ügyfeleik számára, akik közvetlen Nagyvállalati Szerződéssel (EA) rendelkeznek. Az előfizetések átvitele csak olyan ügyfelek esetében lehetséges, akik elfogadták a Microsoft-ügyfélszerződést, és rendelkeznek Azure-csomaggal.

A kérelem jóváhagyása után a felhőszolgáltató egyesített számlát biztosíthat az ügyfeleknek. A felhőszolgáltatói előfizetések átvitelével kapcsolatos részleteket lásd: [Azure-előfizetés számlázási tulajdonjogának beszerzése az MPA-fiókjához](mpa-request-ownership.md).

>[!IMPORTANT]
> Miután egy EA-előfizetés átkerül egy CSP-partnerhez, az EA-előfizetésre előzőleg vonatkozó valamennyi kvótanövelés az alapértelmezett értékre áll vissza. Ha további kvótára lenne szükség az előfizetés átvitelét követően, kérje meg CSP-szolgáltatóját, hogy küldjön be egy [kvótanövelési](https://docs.microsoft.com/azure/azure-portal/supportability/regional-quota-requests) kérelmet. 

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Egyéb előfizetés-átvitelek CSP-partnerek részére

Ha más Azure-előfizetést szeretne továbbítani egy felhőszolgáltatói partnernek, az előfizetőnek erőforrásokat kell áthelyeznie a forrás-előfizetésből a felhőszolgáltatói előfizetésbe. Az erőforrások előfizetések közötti áthelyezésével kapcsolatban a következő útmutatás nyújt segítséget.

1. A felhőszolgáltatói partnerrel együttműködésben hozzanak létre felhőszolgáltatói előfizetéseket az Azure-ban.
1. Győződjön meg arról, hogy felhőszolgáltatói forrás- és célelőfizetések ugyanahhoz az Azure Active Directory- (Azure AD-) bérlőhöz vannak társítva.  
    Az Azure CSP-előfizetések esetében az Azure AD-bérlőt nem lehet módosítani. Ehelyett hozzá kell adnia vagy rendelnie a forrás-előfizetést a CSP Azure AD-bérlőhöz. További információért lásd: [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directory-bérlőhöz](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - Ha egy előfizetést társít egy másik Azure AD-címtárhoz, akkor az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) használatával hozzárendelt szerepkörökkel rendelkező felhasználók elveszítik a hozzáférésüket. A hagyományos előfizetés-rendszergazdák, köztük a szolgáltatásadminisztrátor és a társrendszergazdák is elveszítik a hozzáférésüket.
    > - A szabályzat-hozzárendelések is törlődnek az előfizetésből, ha az előfizetés egy másik címtárhoz van társítva.
1. Az átvitelhez használt felhasználói fióknak [RBAC](add-change-subscription-administrator.md)-alapú tulajdonosi hozzáféréssel kell rendelkeznie mindkét előfizetés esetében.
1. A kezdés előtt [ellenőrizze](/rest/api/resources/resources/validatemoveresources), hogy az összes Azure-erőforrás áthelyezhető-e a forrás-előfizetésből a célelőfizetésbe.  
    Bizonyos Azure-erőforrások nem helyezhetők át az előfizetések között. Az áthelyezhető Azure-erőforrások teljes listájának megtekintéséhez lásd: [Áthelyezési művelet támogatása erőforrásokhoz](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - Az Azure CSP csak az Azure Resource Manager erőforrásait támogatja. Ha a forrás-előfizetésben található bármely Azure-erőforrás a klasszikus Azure üzemi modellel lett létrehozva, akkor a migrálás előtt ezt migrálnia kell az [Azure Resource Managerbe](https://docs.microsoft.com/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm). A weboldalt csak a partnerek tekinthetik meg.

1. Ellenőrizze, hogy az összes forrás-előfizetési szolgáltatás az Azure Resource Manager-modellt használja-e. Ezután átviheti az erőforrásokat a forrás-előfizetésből a célelőfizetésbe az [Azure-erőforrások áthelyezésével](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Az Azure-erőforrások előfizetések közötti áthelyezése a szolgáltatás leállását eredményezheti, az előfizetések erőforrásaitól függően.

## <a name="transfer-csp-subscription-to-other-offer"></a>CSP-előfizetés átvitele másik ajánlatba

Bármely más előfizetés egy felhőszolgáltatói partnertől bármely egyéb Azure-ajánlatba történő áthelyezésekor az előfizetőnek erőforrásokat kell áthelyeznie a felhőszolgáltatói forrás-előfizetések és a célelőfizetések között.

1. Hozzon létre célelőfizetéseket az Azure-ban.
1. Győződjön meg arról, hogy a forrás- és célelőfizetések ugyanahhoz az Azure Active Directory- (Azure AD-) bérlőhöz vannak társítva. Az Azure AD-bérlő módosításával kapcsolatos további információért lásd: [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directory-bérlőhöz](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    Vegye figyelembe, hogy a módosított címtár nem a CSP-előfizetés. Tegyük fel például, hogy egy CSP-ről végez átvitelt egy használatalapú fizetéses előfizetésre. Módosítania kell a használatalapú fizetéses előfizetés címtárát a helyes címtárra.

    > [!IMPORTANT]
    >  - Ha egy előfizetést társít egy másik címtárhoz, akkor az [RBAC](../../role-based-access-control/role-assignments-portal.md) használatával hozzárendelt szerepkörökkel rendelkező felhasználók elveszítik a hozzáférésüket. A hagyományos előfizetés-rendszergazdák, köztük a szolgáltatásadminisztrátor és a társrendszergazdák is elveszítik a hozzáférésüket.
    >  - A szabályzat-hozzárendelések is törlődnek az előfizetésből, ha az előfizetés egy másik címtárhoz van társítva.

1. Az átvitelhez használt felhasználói fióknak [RBAC](add-change-subscription-administrator.md)-alapú tulajdonosi hozzáféréssel kell rendelkeznie mindkét előfizetés esetében.
1. A kezdés előtt [ellenőrizze](/rest/api/resources/resources/validatemoveresources), hogy az összes Azure-erőforrás áthelyezhető-e a forrás-előfizetésből a célelőfizetésbe.
    > [!IMPORTANT]
    >  - Bizonyos Azure-erőforrások nem helyezhetők át az előfizetések között. Az áthelyezhető Azure-erőforrások teljes listájának megtekintéséhez lásd: [Áthelyezési művelet támogatása erőforrásokhoz](../../azure-resource-manager/management/move-support-resources.md).

1. Vigye át az erőforrásokat a forrás-előfizetésből a célelőfizetésbe az [Azure-erőforrások áthelyezésével](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Az Azure-erőforrások előfizetések közötti áthelyezése a szolgáltatás leállását eredményezheti, az előfizetések erőforrásaitól függően.

## <a name="next-steps"></a>További lépések
- [Azure-előfizetés számlázási tulajdonjogának beszerzése az MPA-fiókjához](mpa-request-ownership.md).
- További szükséges ismeretek: [Fiókok és előfizetések kezelése az Azure Billinggel](../index.yml).
