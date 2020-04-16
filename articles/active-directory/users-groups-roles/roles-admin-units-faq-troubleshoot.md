---
title: Felügyeleti egységek és gyakori kérdések – Gyakori kérdések – Azure Active Directory | Microsoft dokumentumok
description: Vizsgálja meg a felügyeleti egységeket a korlátozott hatókörű engedélyek delegálásához az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c9770b60edad6f956ae99c91b94a232cc48bbbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428146"
---
# <a name="troubleshooting-and-faq-for-administrative-units-in-azure-active-directory"></a>Hibaelhárítás és gyakori kérdések az Azure Active Directory felügyeleti egységeivel kapcsolatban

Részletesebb felügyeleti vezérlés az Azure Active Directoryban (Azure AD), a felhasználók hozzárendelhető egy Azure AD-szerepkör hatóköre egy vagy több felügyeleti egység (AUs) használatával. A powershell-parancsfájlok mintaaaa a gyakori feladatokhoz a. https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Nem tudok felügyeleti egységet létrehozni**

**A.** Csak egy globális rendszergazda vagy kiemelt szerepkör-rendszergazda hozhat létre felügyeleti egységet az Azure AD-ben. Ellenőrizze, hogy a felügyeleti egységet létrehozni próbáló felhasználó globális rendszergazdai vagy kiemelt szerepkör-rendszergazdai szerepkört kapott-e.

**K: Hozzáadtam egy csoportot a közigazgatási egységhez, de a csoport tagjai még mindig nem jelennek meg a közigazgatási egységben**

**A.** Ha hozzáad egy csoportot a felügyeleti egységhez, az nem eredményezi a csoport összes tagjának hozzáadását a felügyeleti egységhez. A felhasználókat közvetlenül hozzá kell rendelni a felügyeleti egységhez.

**K: Én csak hozzá / eltávolított tagja a közigazgatási egység, és még mindig megjelenik a UI**

**A.** Előfordulhat, hogy a felügyeleti egység egy vagy több tagjának hozzáadásának vagy eltávolításának feldolgozása néhány percet vesz igénybe, hogy tükrözze a **Felügyeleti egységek** lapot. Dönthet úgy, hogy vár néhány percet, amíg a felügyeleti egységek alatt tükröződik. Másik lehetőségként közvetlenül a társított erőforrás tulajdonságait, és nézd meg, ha a művelet befejeződött. Az Ati-k felhasználóiról és csoportjairól a [Felhasználók felügyeleti egységeinek listázása](roles-admin-units-add-manage-users.md) és [a csoport felügyeleti egységeinek listázása című](roles-admin-units-add-manage-groups.md)témakörben talál további információt.

**K: Egy felügyeleti egység delegált jelszórendszergazdájaként nem tudom alaphelyzetbe állítani egy adott felhasználó jelszavát**

**A.** Egy rendszergazda, aki rendszergazdai egységhez van rendelve, csak a felügyeleti egységhez rendelt felhasználók jelszavát állíthatja vissza. Győződjön meg arról, hogy az a felhasználó, amelyiknek a jelszó-visszaállítás sikertelen, ahhoz a felügyeleti egységhez tartozik, amelyhez a szerepkört hozzárendelte. Ha a felhasználó ugyanahhoz a felügyeleti egységhez tartozik, és továbbra sem tudja alaphelyzetbe állítani a felhasználó jelszavát, ellenőrizze a felhasználóhoz rendelt szerepköröket. A jogosultságok illetéktelen megszerzésének megakadályozása érdekében a rendszergazdai egység hatókörrel rendelkező rendszergazdája nem állíthatja alaphelyzetbe egy olyan felhasználó jelszavát, aki szervezeti szintű hatókörrel rendelkező szerepkörhöz van rendelve.

**K: Miért van szükség közigazgatási egységekre? Nem használhattuk volna a biztonsági csoportokat a hatókör meghatározásának módjaként?**

**A.** A biztonsági csoportok rendelkeznek egy meglévő cél- és engedélyezési modellel. A felhasználói rendszergazda, például kezelheti az Azure AD-szervezet összes biztonsági csoportjának tagságát, például csoportok használatával kezelheti az alkalmazásokhoz, például a Salesforce-hoz való hozzáférést. A felhasználói rendszergazda nem kezelheti magát a delegálási modellt, ami akkor következne be, ha a biztonsági csoportokat kiterjesztenék az "erőforrás-csoportosítás" forgatókönyvek támogatására. A felügyeleti egységek, például a Windows Server Active Directory szervezeti egységei, a címtárobjektumok széles körének hatókör-felügyeletét szolgálják. Maguk a biztonsági csoportok erőforrás-hatókörök tagjai lehetnek. A rendszergazda által kezelhető biztonsági csoportok biztonsági csoportok definiálása a biztonsági csoportok használatával zavaró lehet.

**K: Mit jelent csoportot hozzáadni egy felügyeleti egységhez?**

**A.** Ha csoportot ad hozzá egy felügyeleti egységhez, a csoport magában a felhasználói hatókörbe kerül, aki az adott rendszergazdai egységhez is hatókörrel rendelkezik. A felügyeleti egység felhasználói rendszergazdái kezelhetik magának a csoportnak a nevét és tagságát. Nem ad engedélyt a felügyeleti egység felhasználói rendszergazdájának a csoport felhasználóinak kezelésére (például a jelszavuk alaphelyzetbe állítására). Ahhoz, hogy a felhasználó rendszergazdája képes legyen kezelni a felhasználókat, a felhasználóknak a felügyeleti egység közvetlen tagjainak kell lenniük.

**K: Egy erőforrás (felhasználó vagy csoport) több felügyeleti egység tagja is lehet?**

**A.** Igen, egy erőforrás több felügyeleti egység tagja is lehet. Az erőforrást az erőforrásra vonatkozó engedélyekkel rendelkező összes szervezeti szintű és felügyeleti egységszintű rendszergazda is feltudja ügyintézésre.

**K: Elérhetők-e felügyeleti egységek a B2C szervezetekben?**

**A.** Nem, a felügyeleti egységek nem érhetők el a B2C szervezetek számára.

**K: Támogatottak a beágyazott felügyeleti egységek?**

**A.** A beágyazott felügyeleti egységek nem támogatottak.

**K: A felügyeleti egységek támogatottak a PowerShell és a Graph API-ban?**

**V:** Igen. A felügyeleti egységek támogatása a [PowerShell-parancsmag dokumentációjában](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) és [a mintaparancsfájlokban](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)található, és a támogatás a Microsoft Graph a [administrativeUnit erőforrástípushoz](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit)található.

## <a name="next-steps"></a>További lépések

- [Felügyeleti egységek a szerepkörök hatókörének korlátozására – áttekintés](directory-administrative-units.md)
- [Felügyeleti egységek kezelése](roles-admin-units-manage.md)