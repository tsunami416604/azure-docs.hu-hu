---
title: Az Azure AD tagjogosultság-kezelés (előzetes verzió) – az Azure Active Directory hibaelhárítása
description: Ismerje meg azokat az elemeket, ellenőrizze, az Azure Active Directory tagjogosultság-kezelés (előzetes verzió) háríthatja el.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2526ef10c3080dae1b32881a109a9436a0fd390
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473819"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Hibaelhárítás az Azure ad-ben tagjogosultság-kezelés (előzetes verzió)

> [!IMPORTANT]
> Az Azure Active Directory (Azure AD) tagjogosultság-kezelés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk ismerteti az egyes elemeket, ellenőrizze, hogy az Azure Active Directory (Azure AD) tagjogosultság-kezelés háríthatja el.

## <a name="checklist-for-entitlement-management-administration"></a>Ellenőrzőlista a jogosultság felügyelethez

* Ha a "hozzáférés megtagadva" üzenet tagjogosultság-kezelés konfigurálása során kap, és a egy globális rendszergazdai, ügyeljen arra, hogy a címtár- [Azure AD Premium P2 (vagy az EMS E5 csomag) licenccel](entitlement-management-overview.md#license-requirements).  
* Ha megjelenik a "hozzáférés megtagadva" üzenet létrehozásakor vagy a hozzáférési csomagok megtekintése, és Ön a katalógus létrehozó csoport tagja, létre kell hoznia a katalógust az első hozzáférési csomag létrehozása előtt.

## <a name="checklist-for-adding-a-resource"></a>Ellenőrzőlista az erőforrás hozzáadása

* Egy alkalmazás erőforrás-hozzáférési csomagban, rendelhető legalább egy erőforrás-szerepkörrel kell rendelkeznie. A szerepkörök maga az alkalmazás által meghatározott, és az Azure AD-ben felügyeli. Vegye figyelembe, hogy az Azure Portalon is megjelenítése szolgáltatásnevek szolgáltatásokhoz, mint az alkalmazások nem jelölhető.  Különösen **Exchange online-hoz** és **SharePoint online-hoz** szolgáltatást, nem az alkalmazásokat, amelyek az erőforrás-szerepkörökkel rendelkezik a címtárban, így nem szerepelhet egy hozzáférés-csomag.  Ehelyett használjon Csoportalapú licencelés létesíteni egy felhasználóhoz, aki ezeket a szolgáltatásokat el kell érnie a megfelelő licenccel.

* Egy csoport egy erőforrás-hozzáférési csomagban módosítható az Azure ad-ben is képesnek kell lennie.  Csoportok visszaíródnak a helyszíni Active Directoryban erőforrásként nem lehet hozzárendelni, mert a tulajdonos vagy a tag attribútumai nem módosítható az Azure ad-ben.  

* A SharePoint Online-Dokumentumkönyvtárak és az egyes dokumentumok nem adható hozzá erőforrásként.  Ehelyett hozzon létre egy Azure AD biztonsági csoportot, a csoport és a egy helyrendszer-szerepkör tartalmazza a hozzáférés csomagban és a SharePoint online-ban, hogy a csoport használatával férhet hozzá a dokumentumtárhoz vagy dokumentum.

* Ha rendelkezik már hozzá van rendelve egy erőforrás, amely egy hozzáférés-csomagot a kezelni kívánt felhasználókat, győződjön meg, hogy a felhasználók vannak hozzárendelve a hozzáférés csomagot egy megfelelő szabályzatot. Például érdemes egy csoport egy hozzáférés-csomagot, amely már rendelkezik a csoportban lévő felhasználók felvétele. A csoport igényelnek a felhasználók folyamatos hozzáférést, ha a hozzáférési csomagok egy megfelelő szabályzatot úgy, hogy azok ne veszítse el hozzáférésüket a csoporthoz kell rendelkezniük. A hozzáférés csomag vagy azzal, hogy a felhasználók számára a hozzáférés csomagot, hogy az erőforrást tartalmazó kérelmek, vagy közvetlenül rendel a hozzáférés csomag rendelhet. További információkért lásd: [szerkesztése és kezelése a meglévő hozzáférési csomag](entitlement-management-access-package-edit.md).

## <a name="checklist-for-providing-external-users-access"></a>Ellenőrzőlista a külső felhasználók számára hozzáférést biztosít.

* Ha egy B2B [engedélyezési lista](../b2b/allow-deny-list.md), amelynek könyvtárak nem engedélyezett felhasználók nem fognak tudni hozzáférés kérése majd.

* Győződjön meg arról, hogy nincs [feltételes hozzáférési szabályzatok](../conditional-access/require-managed-devices.md) , amely megakadályozná a külső felhasználók hozzáférést kérő, illetve az alkalmazások használatának a hozzáférési csomagok.

## <a name="checklist-for-request-issues"></a>Ellenőrzőlista a kérelem kapcsolatos problémák

* Amikor a felhasználó igényelhet hozzáférést egy hozzáférés-csomagot használni szeretne, győződjön meg, hogy használják az **saját hozzáférési portál hivatkozása** az access-csomag számára. További információkért lásd: [másolási saját hozzáférési portál hivatkozása](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* Amikor egy felhasználó bejelentkezik a saját Access Portalra kérése egy hozzáférés-csomagot, győződjön meg a szervezeti fiókjával hitelesítéshez. A szervezeti fiókkal vagy egy fiókot az erőforrás-könyvtár vagy egy könyvtárat, amely a házirendek a hozzáférés csomag egyik része lehet. A felhasználói fiók nem egy szervezeti fiókhoz, vagy a könyvtárban nem szerepel a házirendet, majd a felhasználók nem látják a hozzáférés-csomag. További információkért lásd: [igényelhet hozzáférést egy hozzáférési csomag](entitlement-management-request-access.md).

* Ha egy felhasználó bejelentkezésének az erőforrás-könyvtár zárolva van, nem igényelhetnek hozzáférést a saját hozzáférés portálon. Mielőtt a felhasználó kérhet hozzáférést, el kell távolítani a bejelentkezés letiltása a felhasználó profilját. Az Azure Portalon a bejelentkezési blokk eltávolításához kattintson **Azure Active Directory**, kattintson a **felhasználók**, kattintson arra a felhasználóra, és kattintson **profil**. Szerkessze a **beállítások** szakaszban, és módosítsa **bejelentkezés blokkolása** való **nem**. További információkért lásd: [hozzáadása vagy módosítása egy felhasználói profil adatait az Azure Active Directoryval](../fundamentals/active-directory-users-profile-azure-portal.md).  Ellenőrizheti azt is, ha a felhasználó miatt lett letiltva egy [Identity Protection-házirend](../identity-protection/howto-unblock-user.md).

* A saját hozzáférés-portálon, ha egy felhasználó a kérelmező és a egy jóváhagyó nem láthatják a kérelem egy hozzáférés-csomaghoz a a **jóváhagyások** lapot. Ez a viselkedés szándékos – a felhasználó nem hagyhatja jóvá a saját kérelmet. Győződjön meg arról, hogy a hozzáférés csomag e a kért további jóváhagyók a szabályzat konfigurálva van. További információkért lásd: [valamely meglévő szabályzatot](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Új külső felhasználót, amely nem korábban jelentkezett be a címtár, kap egy hozzáférés-csomagot, beleértve a SharePoint Online-helyhez, ha a hozzáférési csomag jelennek meg, amíg azok a fiókok a SharePoint online-ban üzembe nincs teljes egészében szállított.

## <a name="next-steps"></a>További lépések

- [Hogyan érik el a felhasználók tagjogosultság-kezelés a jelentések megtekintése](entitlement-management-reports.md)
