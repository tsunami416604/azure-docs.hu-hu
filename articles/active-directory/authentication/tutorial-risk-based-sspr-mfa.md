---
title: Kockázatalapú MFA és SSPR az Azure Identity Protection szolgáltatással
description: Ebben az oktatóanyagban Azure Identity Protection-integrációkat engedélyezünk a többtényezős hitelesítéshez és új jelszó önkiszolgáló kéréséhez a kockázatos viselkedés elkerülése érdekében.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: df3344efadbc915bba0c863979cae8b8fdff99b0
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512125"
---
# <a name="tutorial-use-risk-events-to-trigger-multi-factor-authentication-and-password-changes"></a>Oktatóanyag: Kockázati események eseményindító multi-factor Authentication és a jelszó módosítására használata

Az oktatóanyagban az Azure Active Directory (Azure AD) Identity Protectiont fogjuk engedélyezni, amely az Azure AD Premium P2 szolgáltatása, és sokkal több, mint egy egyszerű monitorozási és jelentéskészítési eszköz. A vállalat identitásainak védelme érdekében konfigurálhat olyan kockázatalapú szabályzatokat, amelyek automatikusan reagálnak a kockázatos viselkedési mintákra. Ezekkel a szabályzatokkal automatikusan blokkolható vagy kezdeményezhető a helyreállítás, beleértve a jelszómódosítások és a többtényezős hitelesítés kikényszerítését.

Az Azure AD Identity Protection-szabályzatok a meglévő feltételes hozzáférési szabályzatok mellett extra biztonsági rétegként használhatóak. Bár lehet, hogy a felhasználók soha nem viselkednek majd kockázatosan és aktiválják a szabályzatokat, rendszergazdaként nyugodt lehet, hogy gondoskodott a védelemről.

Kockázateseményeket kiváltó elemek lehetnek például a következők:

* Felhasználók, akiknek kiszivárogtak a hitelesítő adatai
* Bejelentkezések névtelen IP-címről
* Bejelentkezés szokatlan helyekről
* Bejelentkezések fertőzött eszközökről
* Bejelentkezések gyanús tevékenységeket mutató IP-címekkel
* Bejelentkezések ismeretlen helyekről

Az Azure AD Identity Protectionnel kapcsolatban további információt [az Azure AD Identity Protectiont bemutató](../active-directory-identityprotection.md) cikkben talál.

> [!div class="checklist"]
> * Az Azure MFA-regisztráció engedélyezése
> * Kockázatalapú jelszómódosítások engedélyezése
> * Kockázatalapú többtényezős hitelesítés engedélyezése

## <a name="prerequisites"></a>Előfeltételek

* Hozzáférés egy működő Azure AD-bérlőhöz, amely legalább egy próbaverziós Azure AD Premium P2 licenccel rendelkezik.
* Egy globális rendszergazdai jogosultsággal rendelkező fiók az Azure AD-bérlőn.
* Az előző oktatóanyagok elvégzése az új jelszó önkiszolgáló kérésével (SSPR) és a többtényezős hitelesítéssel (MFA) kapcsolatban.

## <a name="enable-risk-based-policies-for-sspr-and-mfa"></a>Kockázatalapú szabályzatok engedélyezése az SSPR-hez és az MFA-hoz

A kockázatalapú szabályzatok engedélyezése egyszerű. Az alábbi lépések végigvezetik egy példa konfiguráción.

### <a name="enable-users-to-register-for-multi-factor-authentication"></a>Többtényezős hitelesítésre való regisztrálás engedélyezése a felhasználók számára

Az Azure AD Identity Protection tartalmaz egy alapértelmezett szabályzatot, amelyek segítségével a felhasználók a multi-factor Authentication regisztrált, és könnyen azonosíthatja azokat az aktuális regisztrációs állapot. A szabályzat az engedélyezése esetén nem követeli meg a felhasználóktól a többtényezős hitelesítést, de felkéri őket az előzetes regisztrációra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a **Minden szolgáltatás** elemre, majd keresse meg az **Azure AD Identity Protection** szolgáltatást.
1. Kattintson az **MFA-regisztráció** lehetőségre.
1. Állítsa a Házirend érvényesítése beállítást **Be** értékre.
   1. A szabályzat beállításával az összes felhasználónak regisztrálnia kell a többtényezős hitelesítéshez használható módszereket.
1. Kattintson a **Save** (Mentés) gombra.

   ![Felhasználók MFA-regisztrációjának megkövetelése bejelentkezéskor az Azure AD Identity Protection használatával](./media/tutorial-risk-based-sspr-mfa/risk-based-require-mfa-registration.png)

### <a name="enable-risk-based-password-changes"></a>Kockázatalapú jelszómódosítások engedélyezése

A Microsoft kutatókkal, a bűnüldözési hatóságokkal, különféle belső biztonsági csapatokkal és egyéb megbízható forrásokkal együttműködve keres felhasználónév–jelszó párokat. Ha az ilyen párok valamelyike egyezik a környezetében lévő valamelyik fiókkal, a rendszer kockázatalapú jelszómódosítást indíthat a következő szabályzat használatával.

1. Kattintson a Felhasználói kockázati házirend lehetőségre.
1. A **Feltételek** területen válassza a **Felhasználói kockázat**, majd a **Közepes vagy nagyobb** lehetőséget.
1. Kattintson a Kiválasztás, majd a Kész gombra.
1. A **Hozzáférés** területen válassza a **Hozzáférés engedélyezése**, majd a **Jelszómódosítás megkövetelése** lehetőséget.
1. Kattintson a Kiválasztás gombra.
1. Állítsa a Házirend érvényesítése beállítást **Be** értékre.
1. Kattintson a **Mentés** gombra.

### <a name="enable-risk-based-multi-factor-authentication"></a>Kockázatalapú többtényezős hitelesítés engedélyezése

A legtöbb felhasználó viselkedése normális, amely követhető, és amikor eltérnek a normálistól, kockázatos lehet engedni, hogy egyszerűen bejelentkezzenek. Érdemes lehet blokkolni ezeket a felhasználókat, vagy egyszerűen megkérni, hogy többtényezős hitelesítés útján bizonyítsák, valóban azok, akiknek mondják magukat. Ha egy olyan szabályzatot szeretne engedélyezni, amely a kockázatos bejelentkezések alkalmával megköveteli a többtényezős hitelesítést, engedélyezze a következő szabályzatot.

1. Kattintson a Bejelentkezési kockázati házirend lehetőségre.
1. A **Feltételek** területen válassza a **Felhasználói kockázat**, majd a **Közepes vagy nagyobb** lehetőséget.
1. Kattintson a Kiválasztás, majd a Kész gombra.
1. A **Hozzáférés** területen válassza a **Hozzáférés engedélyezése**, majd a **Többtényezős hitelesítés megkövetelése** lehetőséget.
1. Kattintson a Kiválasztás gombra.
1. Állítsa a Házirend érvényesítése beállítást **Be** értékre.
1. Kattintson a **Mentés** gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte a tesztelést, és már nem szeretné, hogy a kockázatalapú szabályzatok engedélyezve legyenek, lépjen vissza a letiltani kívánt szabályzatokra, és állítsa a **Házirend érvényesítése** beállítást **Ki** értékre.
