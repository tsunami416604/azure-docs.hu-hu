---
title: Felügyeleti egységek hibaelhárítása és gyakori kérdések – Azure Active Directory | Microsoft dokumentumok
description: Vizsgálja meg a felügyeleti egységek korlátozott hatókörrel rendelkező engedélyek et az Azure Active Directoryban.
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
ms.openlocfilehash: 022658306d6e4d69174cc616d230cfe4892f1204
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684862"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD felügyeleti egységek: Hibaelhárítás és gyakori kérdések

Részletesebb felügyeleti vezérlés az Azure Active Directoryban (Azure AD), a felhasználók hozzárendelhető egy Azure AD-szerepkör egy hatóköre, amely egy vagy több felügyeleti egység (AUs) csak. A gyakori feladatokhoz használt PowerShell-parancsfájlok mintája a Felügyeleti egységek használata.For sample PowerShell-parancsfájlok for common tasks, see [Work with administrative units.](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Miért nem tudok felügyeleti egységet létrehozni?**

**A.** Csak egy *globális rendszergazda* vagy *kiemelt szerepkör-rendszergazda* hozhat létre felügyeleti egységet az Azure AD-ben. Ellenőrizze, hogy a felügyeleti egységet létrehozni próbáló felhasználó globális *rendszergazdai* vagy *kiemelt szerepkör-rendszergazdai* szerepkört kapott-e.

**K: Hozzáadtam egy csoportot a közigazgatási egységhez. Miért nem jelennek meg még mindig a csoport tagjai?**

**A.** Ha hozzáad egy csoportot a felügyeleti egységhez, az nem eredményezi a csoport összes tagjának hozzáadását. A felhasználókat közvetlenül hozzá kell rendelni a felügyeleti egységhez.

**K: Én csak hozzá (vagy eltávolították) tagja a közigazgatási egység. Miért nem jelenik meg (vagy jelenik meg a tag) a felhasználói felületen?**

**A.** Előfordulhat, hogy a felügyeleti egység egy vagy több tagjának hozzáadásának vagy eltávolításának feldolgozása néhány percet vesz igénybe, hogy megjelenjen a **Felügyeleti egységek** lapon. Másik lehetőségként közvetlenül a társított erőforrás tulajdonságait, és nézze meg, hogy a művelet befejeződött. Az Ati-k felhasználóiról és csoportjairól a [Felhasználók felügyeleti egységeinek listázása](roles-admin-units-add-manage-users.md) és [a csoport felügyeleti egységeinek listázása című](roles-admin-units-add-manage-groups.md)témakörben talál további információt.

**K: Rendszergazdai egység delegált jelszó-rendszergazdája vagyok. Miért nem tudom visszaállítani egy adott felhasználó jelszavát?**

**A.** A rendszergazdai egységként csak a rendszergazdai egységhez rendelt felhasználók számára állíthatja alaphelyzetbe a jelszavakat. Győződjön meg arról, hogy az a felhasználó, akinek a jelszó-alaphelyzetbe állítása sikertelen, ahhoz a felügyeleti egységhez tartozik, amelyhez hozzá van rendelve. Ha a felhasználó ugyanahhoz a felügyeleti egységhez tartozik, de továbbra sem tudja alaphelyzetbe állítani a jelszavát, ellenőrizze a felhasználóhoz rendelt szerepköröket. 

A jogosultságok illetéktelen megszerzésének megakadályozása érdekében a rendszergazdai egység hatókörrel rendelkező rendszergazdája nem állíthatja alaphelyzetbe a szervezetszintű hatókörrel rendelkező szerepkörhöz rendelt felhasználó jelszavát.

**K: Miért van szükség közigazgatási egységekre? Nem használhattuk volna a biztonsági csoportokat a hatókör meghatározásának módjaként?**

**A.** A biztonsági csoportok rendelkeznek egy meglévő cél- és engedélyezési modellel. A *felhasználói rendszergazda,* például kezelheti az Azure AD-szervezet összes biztonsági csoportjának tagságát. A szerepkör csoportok at használhat az alkalmazásokhoz, például a Salesforce-hoz való hozzáférés kezelésére. A *felhasználói rendszergazda* nem kezelheti magát a delegálási modellt, ami akkor következne be, ha a biztonsági csoportokat kiterjesztenék az "erőforrás-csoportosítás" forgatókönyvek támogatására. A felügyeleti egységek, például a Windows Server Active Directory szervezeti egységei, a címtárobjektumok széles körének hatókör-felügyeletét szolgálják. Maguk a biztonsági csoportok erőforrás-hatókörök tagjai lehetnek. A rendszergazda által kezelhető biztonsági csoportok biztonsági csoportok meghatározásához a biztonsági csoportok használata zavaró lehet.

**K: Mit jelent csoportot hozzáadni egy felügyeleti egységhez?**

**A.** Ha csoportot ad hozzá egy felügyeleti egységhez, az magában a csoportban szerepel a felügyeleti egységhez tartozó *felhasználói rendszergazda* felügyeleti hatóköre. A felügyeleti egység felhasználói rendszergazdái kezelhetik magának a csoportnak a nevét és tagságát. Nem ad engedélyt a *rendszergazdai jogosultsági körnek* a csoport felhasználóinak kezelésére (például a jelszavak alaphelyzetbe állításához). Ahhoz, hogy a *felhasználó rendszergazdája* képes legyen kezelni a felhasználókat, a felhasználóknak a felügyeleti egység közvetlen tagjainak kell lenniük.

**K: Egy erőforrás (felhasználó vagy csoport) több felügyeleti egység tagja is lehet?**

**A.** Igen, egy erőforrás több felügyeleti egység tagja is lehet. Az erőforrást az erőforrásra vonatkozó engedélyekkel rendelkező összes szervezeti szintű és felügyeleti egységhatókörrel rendelkező rendszergazda is feltudja ügyintézni.

**K: Elérhetők-e felügyeleti egységek a B2C szervezetekben?**

**A.** Nem, a felügyeleti egységek nem érhetők el a B2C szervezetek számára.

**K: Támogatottak a beágyazott felügyeleti egységek?**

**A.** Nem, a beágyazott felügyeleti egységek nem támogatottak.

**K: A felügyeleti egységek támogatottak a PowerShell és a Graph API- ban?**

**V:** Igen. A felügyeleti egységek támogatása a [PowerShell-parancsmag dokumentációjában](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) és [a mintaparancsfájlokban](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)található. 

A [Felügyeletiegység erőforrástípus](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit) támogatása a Microsoft Graphban.

## <a name="next-steps"></a>További lépések

- [Szerepkörök hatókörének korlátozása felügyeleti egységek használatával](directory-administrative-units.md)
- [Felügyeleti egységek kezelése](roles-admin-units-manage.md)
