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
ms.date: 04/25/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ca7955186d6c6fb1dff2acd6a2d5badd1cedaef
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541510"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Hibaelhárítás az Azure ad-ben tagjogosultság-kezelés (előzetes verzió)

> [!IMPORTANT]
> Az Azure Active Directory (Azure AD) tagjogosultság-kezelés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk ismerteti az egyes elemeket, ellenőrizze, hogy az Azure Active Directory (Azure AD) tagjogosultság-kezelés háríthatja el.

## <a name="checklist-for-entitlement-management-administration"></a>Ellenőrzőlista a jogosultság felügyelethez

* Ha a "hozzáférés megtagadva" üzenet tagjogosultság-kezelés konfigurálása során kap, és a egy globális rendszergazdai, ügyeljen arra, hogy a címtár- [Azure AD Premium P2 (vagy az EMS E5 csomag) licenccel](entitlement-management-overview.md#prerequisites).  
* Ha megjelenik a "hozzáférés megtagadva" üzenet létrehozásakor vagy a hozzáférési csomagok megtekintése, és Ön a katalógus létrehozó csoport tagja, létre kell hoznia a katalógust az első hozzáférési csomag létrehozása előtt.

## <a name="checklist-for-adding-a-resource"></a>Ellenőrzőlista az erőforrás hozzáadása

* Ha rendelkezik már hozzá van rendelve egy erőforrás, amely egy hozzáférés-csomagot a kezelni kívánt felhasználókat, győződjön meg, hogy a felhasználók vannak hozzárendelve a hozzáférés csomagot egy megfelelő szabályzatot. Például érdemes egy csoport egy hozzáférés-csomagot, amely már rendelkezik a csoportban lévő felhasználók felvétele. A csoport igényelnek a felhasználók folyamatos hozzáférést, ha a hozzáférési csomagok egy megfelelő szabályzatot úgy, hogy azok ne veszítse el hozzáférésüket a csoporthoz kell rendelkezniük. A hozzáférés csomag vagy azzal, hogy a felhasználók számára a hozzáférés csomagot, hogy az erőforrást tartalmazó kérelmek, vagy közvetlenül rendel a hozzáférés csomag rendelhet. További információkért lásd: [szerkesztése és kezelése a meglévő hozzáférési csomag](entitlement-management-access-package-edit.md).

## <a name="checklist-for-request-issues"></a>Ellenőrzőlista a kérelem kapcsolatos problémák

* Amikor a felhasználó igényelhet hozzáférést egy hozzáférés-csomagot használni szeretne, győződjön meg, hogy használják az **saját hozzáférési portál hivatkozása** az access-csomag számára. További információkért lásd: [másolási saját hozzáférési portál hivatkozása](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* Amikor egy felhasználó bejelentkezik a saját Access Portalra kérése egy hozzáférés-csomagot, győződjön meg a szervezeti fiókjával hitelesítéshez. A szervezeti fiókkal vagy egy fiókot az erőforrás-könyvtár vagy egy könyvtárat, amely a házirendek a hozzáférés csomag egyik része lehet. A felhasználói fiók nem egy szervezeti fiókhoz, vagy a könyvtárban nem szerepel a házirendet, majd a felhasználók nem látják a hozzáférés-csomag. További információkért lásd: [igényelhet hozzáférést egy hozzáférési csomag](entitlement-management-request-access.md).

* Ha egy felhasználó bejelentkezésének az erőforrás-könyvtár zárolva van, nem igényelhetnek hozzáférést a saját hozzáférés portálon. Mielőtt a felhasználó kérhet hozzáférést, el kell távolítani a bejelentkezés letiltása a felhasználó profilját. Az Azure Portalon a bejelentkezési blokk eltávolításához kattintson **Azure Active Directory**, kattintson a **felhasználók**, kattintson arra a felhasználóra, és kattintson **profil**. Szerkessze a **beállítások** szakaszban, és módosítsa **bejelentkezés blokkolása** való **nem**. További információkért lásd: [hozzáadása vagy módosítása egy felhasználói profil adatait az Azure Active Directoryval](../fundamentals/active-directory-users-profile-azure-portal.md)

* A saját hozzáférés-portálon, ha egy felhasználó a kérelmező és a egy jóváhagyó nem láthatják a kérelem egy hozzáférés-csomaghoz a a **jóváhagyások** lapot. Ez a viselkedés szándékos – a felhasználó nem hagyhatja jóvá a saját kérelmet. Győződjön meg arról, hogy a hozzáférés csomag e a kért további jóváhagyók a szabályzat konfigurálva van. További információkért lásd: [valamely meglévő szabályzatot](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Új külső felhasználót, amely nem korábban jelentkezett be a címtár, kap egy hozzáférés-csomagot, beleértve a SharePoint Online-helyhez, ha a hozzáférési csomag jelennek meg, amíg azok a fiókok a SharePoint online-ban üzembe nincs teljes egészében szállított.

## <a name="next-steps"></a>További lépések

- [Hogyan érik el a felhasználók tagjogosultság-kezelés a jelentések megtekintése](entitlement-management-reports.md)
