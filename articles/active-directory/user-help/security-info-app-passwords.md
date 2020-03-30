---
title: Alkalmazásjelszavak létrehozása a Biztonsági adatok (előzetes verzió) lapról – Azure AD
description: Hozzon létre automatikusan létrehozott jelszavakat (alkalmazásjelszavakat) bármely nem böngészőalkalmazással vagy bármely olyan alkalmazással, amely nem támogatja a kétfaktoros ellenőrzést a szervezetben. Ez az alkalmazásjelszó elkülönül a normál jelszótól, és a Biztonsági adatok lapon állítható be.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: curtand
ms.openlocfilehash: 787fa67ee77997fd1f9967db3abdbfc83d4ffad2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064070"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>Alkalmazásjelszavak létrehozása a Biztonsági adatok (előnézet) lapon

Bizonyos alkalmazások, például az Outlook 2010, nem támogatják a kétlépéses ellenőrzést. A támogatás hiánya azt jelenti, hogy ha kétlépéses ellenőrzést használ a szervezetben, az alkalmazás nem fog működni. A probléma megoldásához létrehozhat egy automatikusan generált jelszót, amelyet minden nem böngészőalkalmazáshoz használhat, a normál jelszótól elkülönítve.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Előfordulhat, hogy a rendszergazda nem engedélyezi az alkalmazásjelszavak használatát. Ha nem látja **az alkalmazásjelszavakat,** azok nem érhetők el a szervezetben.

Az alkalmazásjelszavak használatakor fontos megjegyezni:

- Az alkalmazásjelszavak automatikusan generálódnak, és alkalmazásonként egyszer kell létrehozni és beírni őket.

- Felhasználónként legfeljebb 40 jelszó létezik. Ha a korlát után megpróbál létrehozni egyet, a rendszer kéri egy meglévő jelszó törlését, mielőtt az újat létrehozná.

    >[!Note]
    >Az Office 2013-ügyfelek (beleértve az Outlook programot is) támogatják az új hitelesítési protokollokat, és kétlépéses ellenőrzéssel használhatók. Ez a támogatás azt jelenti, hogy a kétlépéses ellenőrzés bekapcsolása után már nem lesz szüksége alkalmazásjelszavakra az Office 2013-ügyfelekhez. További információt az [Office 2013 és az Office 2016 ügyfélalkalmazások modern hitelesítési működése](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) című cikkben talál.

## <a name="create-new-app-passwords"></a>Új alkalmazásjelszavak létrehozása

Ha kétlépéses ellenőrzést használ munkahelyi vagy iskolai fiókjával, és a rendszergazda bekapcsolta a biztonsági adatok használatát, a **Biztonsági adatok** lapon létrehozhatja és törölheti az alkalmazásjelszavakat.

>[!Note]
>Ha a rendszergazda nem kapcsolta be a biztonsági adatok at, kövesse az Alkalmazásjelszavak kezelése a [kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-app-passwords.md) című szakasz utasításait és információit.

### <a name="to-create-a-new-app-password"></a>Új alkalmazásjelszó létrehozása

1. Jelentkezzen be munkahelyi vagy iskolai fiókjába, https://myprofile.microsoft.com/ és lépjen az oldalra.

    ![Saját profil lap, kiemelt biztonsági adatok hivatkozásai](media/security-info/securityinfo-myprofile-page.png)

2. Válassza a **Biztonsági adatok lehetőséget** a bal oldali navigációs ablakban vagy a Biztonsági **adatok** blokkban található hivatkozáson, majd válassza a Metódus **hozzáadása** lehetőséget a **Biztonsági adatok** lapon.

    ![Biztonsági adatok lap kiemelt Hozzáadási módszer beállítással](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. A **Metódus hozzáadása** lapon válassza az **Alkalmazásjelszó** elemet a legördülő listából, majd kattintson a **Hozzáadás gombra.**

    ![Metódus hozzáadása mező, az alkalmazásjelszó kijelölésével](media/security-info/securityinfo-myprofile-addpassword.png)

4. Írja be az alkalmazás jelszavát igénylő alkalmazás nevét, majd válassza a **Tovább**gombot.

    ![Alkalmazásjelszó lap az alkalmazás nevével](media/security-info/securityinfo-myprofile-password-appname.png)

5. Másolja a szöveget a **Jelszó** mezőbe, illessze be a jelszót az alkalmazás jelszómezőjébe (ebben a példában az Outlook 2010-be), majd válassza a **Kész gombot.**

    ![Alkalmazásjelszó lap az alkalmazás nevével](media/security-info/securityinfo-myprofile-password-copytext.png)

    A jelszó hozzáadódik, és sikeresen bejelentkezhet az alkalmazásba a jövőben.

## <a name="delete-your-app-passwords"></a>Az alkalmazásjelszavak törlése

Ha már nincs szüksége olyan alkalmazás használatára, amelyhez alkalmazásjelszó szükséges, törölheti a társított alkalmazásjelszót. Az alkalmazás jelszótörlésével felszabadul az egyik elérhető alkalmazásjelszó-hely a jövőben.

>[!Important]
>Ha véletlenül töröl egy alkalmazásjelszót, nem lehet visszavonni. A cikk [Új alkalmazásjelszavak létrehozása](#create-new-app-passwords) című szakaszának lépéseit követve létre kell hoznia egy új alkalmazásjelszót, majd újra be kell írnia az alkalmazásba.

### <a name="to-delete-an-app-password"></a>Alkalmazásjelszó törlése

1. A **Biztonsági adatok** lapon válassza a **Törlés** hivatkozást az adott alkalmazás **alkalmazásjelszó-beállítása** mellett.

    ![Hivatkozás az alkalmazásjelszó-módszer biztonsági adatokból való törléséhez](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Az **alkalmazás jelszavának**törléséhez válassza az **Igen** lehetőséget a megerősítést kérő mezőben. Az alkalmazás jelszóának törlése után törlődik a biztonsági adatokközül, és eltűnik a **Biztonsági adatok** lapról.

## <a name="for-more-information"></a>További tudnivalók

- A **Biztonsági adatok** lapról és beállításáról a [Biztonsági adatok áttekintése című témakörben olvashat bővebben.](user-help-security-info-overview.md)
