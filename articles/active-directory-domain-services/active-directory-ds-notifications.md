---
title: 'Az Azure Active Directory Domain Services: Értesítési beállítások |} A Microsoft Docs'
description: Értesítési beállítások az Azure AD tartományi szolgáltatásokhoz
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: ergreenl
ms.openlocfilehash: c6d827629850de88940f41febeeca61b812d692e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958525"
---
# <a name="notification-settings-in-azure-ad-domain-services"></a>Értesítési beállítások az Azure AD tartományi szolgáltatásokban

Az Azure AD tartományi szolgáltatásokhoz értesítések frissíteni kell a lehető legrövidebb időn belül a felügyelt tartományra a Szolgáltatásállapot-riasztás észlelt a rendszer teszi lehetővé.  

Ez a funkció csak a felügyelt tartományok, amelyek nem klasszikus virtuális hálózatokon érhető el.


## <a name="how-to-check-your-azure-ad-domain-services-email-notification-settings"></a>Az Azure AD tartományi szolgáltatások e-mail értesítési beállítások ellenőrzése

1. Keresse meg a [Azure AD tartományi szolgáltatások lap](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) az Azure Portalon
2. Válassza ki a felügyelt tartományra a táblázatból
3. A bal oldali navigációs sávján válassza **értesítési beállítások**

A lapon azt sorolja fel az át az e-mail címzett e-mail-értesítések az Azure AD tartományi szolgáltatásokhoz.

## <a name="what-does-an-email-notification-look-like"></a>Milyen kinéznie e-mailben értesítést?

A következő képen látható a következő e-mail-értesítés egy példát:

![Példa e-mail-értesítés](./media/active-directory-domain-services-alerts/email-alert.png)

Az e-mailben adja meg a riasztás-e a felügyelt tartomány, valamint, hogy az Azure AD tartományi szolgáltatások health-oldal az Azure Portalon az idő az észlelési és a egy hivatkozást küldjön.

> [!WARNING]
> Mindig győződjön meg arról, hogy az e-mail érkezik egy ellenőrzött Microsoft küldő előtt az e-mailek hivatkozásokra. Az e-maileket mindig származnak az e-mailben azure-noreply@microsoft.com
>


## <a name="why-would-i-receive-email-notifications"></a>Miért szeretne email értesítéseket kapni?

Az Azure AD Domain Services e-mailben értesítést küld a fontos frissítéseket a tartománnyal kapcsolatban.  Ezek az értesítések csak sürgős kérdések, amely negatív hatással lesz a szolgáltatás, azonnal foglalkozzon vonatkoznak. Minden egyes e-mail értesítés által a felügyelt tartományra a riasztás aktiválódik. Ezek a riasztások az Azure Portalon is megjelenik, és meg lehet nézni az [health-oldal az Azure AD tartományi szolgáltatások](active-directory-ds-check-health.md).

Az Azure AD Domain Services nem küld e-maileket a hirdetményt, a frissítések vagy a értékesítési célra listáját.

## <a name="when-will-i-receive-email-notifications"></a>Amikor kap e-mail-értesítések?

Egy értesítés jelenik meg azonnal amikor egy [új riasztás](active-directory-ds-troubleshoot-alerts.md) a felügyelt tartományban található. Ha a riasztás nem szűnik meg, e-mailben értesítést küld emlékeztetőként naponta négy.

## <a name="who-should-receive-the-email-notifications"></a>Ki kapja meg az e-mail-értesítések?


 Azt javasoljuk, hogy állítható össze, akik képesek felügyelni, és hajtsa végre a módosításokat a felügyelt tartományhoz az Azure AD tartományi szolgáltatásokat az e-mailek címzettjeinek listája. Ez a lista e-mail kell értelmezhetők, az "első válaszadók" a talált hibák a. Ha több mint öt további e-mailek, amelyet szeretne hozzáadni, javasolt létrehozni egy terjesztési lista listához való hozzáadásához az értesítési helyette.

Ön az Azure AD tartományi szolgáltatásokkal kapcsolatos értesítéseket küldenek legfeljebb öt további e-mailt adhat hozzá. Emellett akkor is választja, hogy a címtár minden globális rendszergazda, és az "AAD DC rendszergazdák" csoport minden tagja az Azure AD Domain Services email értesítéseket kapni. Az Azure AD Domain Services csak fog értesítések küldése akár 100 e-mail-címeket, beleértve a globális rendszergazdák és AAD DC rendszergazdák listáját.


## <a name="how-to-add-an-additional-email-recipient"></a>Egy további e-mail címzett felvétele

> [!WARNING]
> Az értesítési beállítások módosítása, ha módosítja a teljes felügyelt tartományba, nem csak magát az értesítési beállításait.

1. Keresse meg a [Azure AD tartományi szolgáltatások lap](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) az Azure Portalon.
2. Kattintson a felügyelt tartományra.
3. A bal oldali navigációs sávján kattintson **értesítési beállítások**.
4. Adja hozzá az e-mailt, írja be az e-mail-címet a további címzettek táblában.
5. Kattintson "a Mentés" a top-oldali navigációs menüben.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

#### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>E-mail-értesítés egy riasztás érkezett, de ha jelentkezve az Azure Portalra riasztás nem létezik. Mi történt?

Ha egy riasztás oldódik meg, a riasztás eltűnik az Azure Portalról. A legvalószínűbb oka, hogy valaki más e-mail-értesítéseket fogadó a felügyelt tartomány a riasztás feloldása, vagy az Azure AD tartományi szolgáltatások által automatikusan Megoldva volt.


#### <a name="why-can-i-not-edit-the-notification-settings"></a>Miért nem frissíthetem az értesítési beállítások?

Ha nem fér hozzá az értesítési beállítások oldal az Azure Portalon, az engedélyek szerkesztése az Azure AD Domain Services nem rendelkezik. A globális rendszergazdai vagy beolvasni az Azure AD Domain Services-erőforrások szerkesztési, vagy el kell távolítani a címzettlistából kapcsolatba kell lépnie.

#### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Nincs e-mail értesítéseket kap, annak ellenére, hogy szeretnék a saját e-mail-cím megadott szerepkörömhöz. Hogy miért?

Ellenőrizze a levélszemét és a Levélszemét mappát, az e-mailben, az értesítés, és ellenőrizze, hogy az engedélyezési listára a feladó (azure-noreply@microsoft.com).

## <a name="next-steps"></a>További lépések
- [Riasztások a felügyelt tartományon](active-directory-ds-troubleshoot-alerts.md)
- [További információ az Azure AD tartományi szolgáltatások](active-directory-ds-overview.md)
- [Lépjen kapcsolatba a termékcsoport](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory Domain Services termékért felelős csoport [visszajelzés és támogatás](active-directory-ds-contact-us.md).
