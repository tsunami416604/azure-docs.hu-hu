---
title: Alkalmazásjelszavak beállítása a Security info (előzetes verzió) lap – Azure Active Directory-ből |} A Microsoft Docs
description: Állítsa be automatikusan generált jelszót (alkalmazásjelszók) használja a semmilyen böngészőn kívüli alkalmazáshoz, vagy minden olyan alkalmazás, amely nem támogatja a kétfaktoros ellenőrzési, a szervezetben. Ezzel az alkalmazásjelszóval elkülönül megváltoztathassák egy saját jelszóra, és a biztonsági adatok lapján állíthatja.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55dfab0c60e77b86157a005db34c37917a5e08d2
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341102"
---
# <a name="manage-app-passwords-from-your-security-info-preview-page"></a>A biztonsági adatok (előzetes verzió) lapján az alkalmazásjelszók kezelése
Bizonyos alkalmazások, például az Outlook 2010, nem támogatják a kétlépéses ellenőrzést. Támogatás hiánya, az azt jelenti, hogy a szervezet használja kétlépéses ellenőrzést, ha az alkalmazás nem fog működni. A probléma megoldásához lekéréséhez egy automatikusan létrehozott jelszó használata minden egyes böngészőn kívüli alkalmazással, elkülönítve a normál jelszavát is létrehozhat.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>A rendszergazda nem engedélyezi az alkalmazásjelszavak használata. Ha nem lát **alkalmazásjelszók** szükség esetén azok még nem érhető el a szervezetben.

Alkalmazásjelszavak használata esetén ne feledje:

- Alkalmazásjelszók a automatikusan létrehozott és csak egyszer megadott alkalmazásonként.

- Nincs egy legfeljebb 40 jelszó felhasználónként. Ha megpróbál létrehozni egyet, miután ezt a korlátot, kérni fogja az új létrehozásához engedélyezése előtt törli a meglévő jelszó.

- Eszközönként, nem alkalmazásonként egy alkalmazásjelszót használni. Hozzon létre például a laptopján az alkalmazások egyszeri jelszót és a egy másik egyetlen jelszóval, asztali számítógépeken az alkalmazások.

    >[!Note]
    >Office 2013 ügyfelek (beleértve az Outlook) támogatja az új hitelesítési protokollok és használható a kétlépcsős ellenőrzéshez. Ez a támogatás, az azt jelenti, hogy után a kétlépéses ellenőrzés be van kapcsolva, már nem kell az alkalmazásjelszavak Office 2013-ügyfelek számára. További információ: a [modern hitelesítés működéséről, az Office 2013 és az Office 2016 ügyfélalkalmazások](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) cikk.

## <a name="create-new-app-passwords"></a>Hozzon létre új alkalmazásjelszavakat
Ha a kétlépéses ellenőrzést használja a munkahelyi vagy iskolai fiókjával, és a rendszergazda bekapcsolta az adatok felhasználói élmény, létrehozhat és törölni az alkalmazásjelszók használatával a **biztonsági adatok** lap.

>[!Note]
>Ha a biztonsági adatok élményt a rendszergazda még nem aktív, kövesse az utasításokat és az információkat a a [alkalmazásjelszavakat a kétlépéses ellenőrzés kezelése](multi-factor-authentication-end-user-app-passwords.md) szakaszban.

### <a name="to-create-a-new-app-password"></a>Alkalmazásjelszó létrehozása
1. Jelentkezzen be munkahelyi vagy iskolai fiókjával, és keresse meg a https://myprofile.microsoft.com/ lapot.

    ![Saját profil lapot, amely a kijelölt biztonsági adatai hivatkozások](media/security-info/securityinfo-myprofile-page.png)

2. Válassza ki **biztonsági adatok** a bal oldali navigációs ablaktáblán vagy a hivatkozás a **biztonsági adatok** letiltása, és válassza ki **metódus hozzáadása** a a **biztonsági adatai**  lapot.

    ![Biztonsági adatok weblapját a kiemelt Hozzáadás metódus beállítás](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Az a **adjon meg egy metódust** lapon jelölje be **alkalmazásjelszót** a legördülő listából válassza ki, és válassza ki a **Hozzáadás**.

    ![A kiválasztott alkalmazás jelszavának metódus mezőben hozzáadása](media/security-info/securityinfo-myprofile-addpassword.png)

4. Adja meg az alkalmazást, amelyet az alkalmazás jelszó szükséges a, és válassza **tovább**.

    ![Alkalmazásjelszavak lapon, az alkalmazás neve](media/security-info/securityinfo-myprofile-password-appname.png)

5. A szöveg másolása a **jelszó** mezőbe, majd válassza ki és illessze be a jelszót az alkalmazást (az ebben a példában az Outlook 2010) jelszó területén **kész**.

    ![Alkalmazásjelszavak lapon, az alkalmazás neve](media/security-info/securityinfo-myprofile-password-copytext.png)
    
    A jelszó kerül, és sikeresen bejelentkezhet az alkalmazásba, a jövőben.

## <a name="delete-your-app-passwords"></a>Az alkalmazásjelszavak törlése
Ha már nincs szüksége egy alkalmazást, amely egy alkalmazásjelszót kell használni, törölheti a társított alkalmazás jelszót. Az alkalmazásjelszó törlése szabadít fel az elérhető alkalmazás jelszóval kritikus pontok elkerülése érdekében használható egyik a jövőben.

>[!Important]
>Ha véletlenül töröl egy alkalmazásjelszót, nincs semmilyen módon nem lehet visszavonni. Kell új jelszót létrehozni, és adja meg újra az alkalmazásba, a lépések a [új Alkalmazásjelszavakat](#create-new-app-passwords) című szakaszát.

### <a name="to-delete-an-app-password"></a>Alkalmazásjelszó törlése

1. Az a **biztonsági adatok** lapon válassza ki a **törlése** mellett kapcsolni a **alkalmazásjelszót** lehetőség az adott alkalmazás.

    ![Hivatkozás törlése a jelszó használata a biztonsági adatok](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Válassza ki **Igen** a megerősítés mezőben törlése a **alkalmazásjelszót**. Miután a rendszer törli az alkalmazásjelszót, a rendszer eltávolítja a biztonsági adatait és, eltűnik a **biztonsági adatok** lapot.

## <a name="for-more-information"></a>További tudnivalók
- További információ a **biztonsági adatok** lapot, és állítsa be, olvassa el [biztonsági adatok áttekintése](user-help-security-info-overview.md)
