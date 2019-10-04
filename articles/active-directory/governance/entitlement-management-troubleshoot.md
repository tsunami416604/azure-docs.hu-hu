---
title: Az Azure AD-jogosultságok felügyeletének (előzetes verzió) hibáinak megoldása – Azure Active Directory
description: Ismerkedjen meg néhány olyan elemmel, amelyeket érdemes ellenőriznie Azure Active Directory jogosultsági felügyelet (előzetes verzió) megoldásához.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39ec27c75ff5ba9164b44b0524f90a4e28ab20f1
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488976"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Az Azure AD-jogosultságok felügyeletének (előzetes verzió) hibáinak megoldása

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk néhány olyan elemet ismertet, amely segítséget nyújt az Azure Active Directory (Azure AD) jogosultságok kezelésének hibaelhárításában.

## <a name="checklist-for-entitlement-management-administration"></a>Ellenőrzőlista a jogosultságok kezelésének felügyeletéhez

* Ha hozzáférés-megtagadási üzenetet kap a jogosultságok kezelésének konfigurálásakor, és Ön globális rendszergazda, győződjön meg arról, hogy a címtárban van egy [prémium szintű Azure ad P2 (vagy EMS E5) licenc](entitlement-management-overview.md#license-requirements).  
* Ha hozzáférési csomagok létrehozásakor vagy megtekintésekor kap hozzáférés-megtagadási üzenetet, és Ön egy katalógus-létrehozó csoport tagja, létre kell hoznia egy katalógust az első hozzáférési csomag létrehozása előtt.

## <a name="checklist-for-adding-a-resource"></a>Ellenőrzőlista erőforrás hozzáadásához

* Ahhoz, hogy egy alkalmazás egy hozzáférési csomagban lévő erőforrás legyen, legalább egy hozzárendelhető erőforrás-szerepkörrel kell rendelkeznie. A szerepköröket maga az alkalmazás definiálja, és az Azure AD-ben kezelik. Vegye figyelembe, hogy a Azure Portal olyan szolgáltatások egyszerű szolgáltatásait is megjelenítheti, amelyek nem választhatók ki alkalmazásként.  Az **Exchange Online** és a **SharePoint Online** például szolgáltatások, nem pedig a címtárban erőforrás-szerepkörökkel rendelkező alkalmazások, ezért nem szerepelhetnek hozzáférési csomagban.  Ehelyett a csoport alapú licencelés használatával hozzon létre egy megfelelő licencet azon felhasználók számára, akiknek hozzáférésre van szüksége a szolgáltatásokhoz.

* Ahhoz, hogy egy csoport egy hozzáférési csomagban lévő erőforrás legyen, módosítható kell lennie az Azure ad-ben.  A helyszíni Active Directoryból származó csoportok nem rendelhetők hozzá erőforrásként, mert a tulajdonos vagy a tag attribútumai nem módosíthatók az Azure AD-ben.  

* A SharePoint Online-dokumentumtárak és az egyes dokumentumok nem vehetők fel erőforrásként.  Ehelyett hozzon létre egy Azure AD biztonsági csoportot, amely tartalmazza azt a csoportot és egy hely szerepkört a hozzáférési csomagban, a SharePoint Online-ban pedig ezt a csoportot használja a dokumentumtárhoz vagy dokumentumhoz való hozzáférés szabályozásához.

* Ha vannak olyan felhasználók, akik már hozzá vannak rendelve egy hozzáférési csomaggal felügyelni kívánt erőforráshoz, akkor győződjön meg arról, hogy a felhasználók a megfelelő házirenddel vannak hozzárendelve a hozzáférési csomaghoz. Előfordulhat például, hogy egy olyan csoportot szeretne felvenni egy olyan hozzáférési csomagba, amely már rendelkezik a csoport felhasználóinak. Ha a csoportban lévő felhasználók folyamatos hozzáférést igényelnek, akkor megfelelő szabályzattal kell rendelkezniük a hozzáférési csomagokhoz, hogy ne veszítsék el a csoporthoz való hozzáférésüket. A hozzáférési csomagot úgy rendelheti hozzá, hogy kéri a felhasználókat, hogy kérjenek az adott erőforrást tartalmazó hozzáférési csomagot, vagy közvetlenül hozzárendelik azokat a hozzáférési csomaghoz. További információ: [meglévő hozzáférési csomag szerkesztése és kezelése](entitlement-management-access-package-edit.md).

## <a name="checklist-for-providing-external-users-access"></a>Ellenőrzőlista külső felhasználók hozzáférésének biztosításához

* Ha van egy B2B [engedélyezési lista](../b2b/allow-deny-list.md), akkor azok a felhasználók, akiknek a könyvtára nem engedélyezett, nem tudnak hozzáférést kérni.

* Győződjön meg arról, hogy nincsenek olyan [feltételes hozzáférési szabályzatok](../conditional-access/require-managed-devices.md) , amelyek megakadályozzák, hogy a külső felhasználók hozzáférjenek a hozzáféréshez, vagy hogy használhassák az alkalmazásokat a hozzáférési csomagokban.

## <a name="checklist-for-request-issues"></a>Ellenőrzőlista a kérelmekkel kapcsolatos problémákhoz

* Ha a felhasználó hozzáférést szeretne kérni egy hozzáférési csomaghoz, győződjön meg róla, hogy a hozzáférési csomaghoz tartozó **saját hozzáférési portál hivatkozást** használja. További információ: [a hozzáférési portál másolása hivatkozás](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* Ha egy felhasználó hozzáférési csomagot kér a saját hozzáférési portálra, akkor győződjön meg arról, hogy a szervezeti fiókjuk használatával végzi a hitelesítést. A szervezeti fiók lehet egy fiók az erőforrás-címtárban, vagy egy olyan címtárban, amely a hozzáférési csomag egyik szabályzatában szerepel. Ha a felhasználó fiókja nem szervezeti fiók, vagy a címtár nem szerepel a szabályzatban, akkor a felhasználó nem fogja látni a hozzáférési csomagot. További információ: hozzáférés [kérése egy hozzáférési csomaghoz](entitlement-management-request-access.md).

* Ha a felhasználó nem tud bejelentkezni az erőforrás-könyvtárba, nem fog tudni hozzáférést kérni a saját hozzáférési portálon. Mielőtt a felhasználó hozzáférést igényelhet, el kell távolítania a bejelentkezési blokkot a felhasználó profiljából. A bejelentkezési blokk eltávolításához kattintson a Azure Portal **Azure Active Directory**, majd a **felhasználók**elemre, majd a felhasználó elemre, végül a **profil**elemre. Szerkessze a Settings ( **Beállítások** ) szakaszt, és módosítsa a **Letiltás bejelentkezést** a **nem**értékre. További információ: [felhasználói profil adatainak hozzáadása vagy frissítése Azure Active Directory használatával](../fundamentals/active-directory-users-profile-azure-portal.md).  Azt is megtekintheti, hogy a felhasználó blokkolva lett-e egy [Identity Protection-házirend](../identity-protection/howto-unblock-user.md)miatt.

* Ha a saját hozzáférési portálon, ha a felhasználó egy kérelmező és egy jóváhagyó is, a jóváhagyások lapon nem fogja látni a hozzáférési csomagra vonatkozó kérelmét  . Ez szándékos viselkedés – a felhasználók nem hagyhatják jóvá a saját kérésükre. Győződjön meg arról, hogy a kért hozzáférési csomaghoz további jóváhagyók vannak konfigurálva a szabályzatban. További információ: [meglévő szabályzat szerkesztése](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Ha egy olyan új külső felhasználó, amely korábban még nem jelentkezett be a címtárba, olyan hozzáférési csomagot kap, amely tartalmazza a SharePoint Online-webhelyet, a hozzáférési csomagja nem teljes kézbesítésre kerül, amíg a fiók a SharePoint Online-ban nincs kiépítve.

## <a name="next-steps"></a>További lépések

- [A felhasználók hozzáférési jogosultságok kezelésével kapcsolatos jelentések megtekintése](entitlement-management-reports.md)
