---
title: Állítsa be a biztonsági adatokkal – Azure Active Directory alkalmazásjelszók |} A Microsoft Docs
description: Automatikusan létrehozott jelszavakat (alkalmazásjelszók) minden egyes böngészőn kívüli alkalmazással szeretné használni létrehozott elkülönítése megváltoztathassák egy saját jelszóra, biztonsági adatokkal.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: e527a0eaec433b96b5c37c5ec22f392a7166dfe8
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162444"
---
# <a name="manage-app-passwords-using-security-info-preview"></a>Biztonsági adatok (előzetes verzió) alkalmazást a jelszavak kezelése

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Bizonyos böngészőn kívüli alkalmazások, például az Outlook 2010, nem támogatja a kétlépéses ellenőrzést. Támogatás hiánya, az azt jelenti, hogy a kétlépéses ellenőrzés használja, ha az alkalmazás nem fog működni. A probléma megoldásához lekéréséhez egy automatikusan létrehozott jelszó használata minden egyes böngészőn kívüli alkalmazással, elkülönítve a normál jelszavát is létrehozhat.

Alkalmazásjelszavak használata esetén ne feledje:

- Alkalmazásjelszók a automatikusan létrehozott és csak egyszer megadott alkalmazásonként.

- Nincs egy legfeljebb 40 jelszó felhasználónként. Ha megpróbál létrehozni egyet, miután ezt a korlátot, kérni fogja az új létrehozásához engedélyezése előtt törli a meglévő jelszó.

- Eszközönként, nem alkalmazásonként egy alkalmazásjelszót használni. Hozzon létre például a laptopján az alkalmazások egyszeri jelszót és a egy másik egyetlen jelszóval, asztali számítógépeken az alkalmazások.

    >[!Note]
    >Office 2013 ügyfelek (beleértve az Outlook) támogatja az új hitelesítési protokollok és használható a kétlépcsős ellenőrzéshez. Ez a támogatás, az azt jelenti, hogy után a kétlépéses ellenőrzés be van kapcsolva, már nem kell az alkalmazásjelszavak Office 2013-ügyfelek számára. További információ: a [modern hitelesítés működéséről, az Office 2013 és az Office 2016 ügyfélalkalmazások](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) cikk.

## <a name="create-and-delete-app-passwords-using-security-info"></a>Hozzon létre vagy töröljön a biztonsági adatokkal alkalmazásjelszók

Ha a kétlépéses ellenőrzés használata a munkahelyi vagy iskolai fiókkal, és a rendszergazda bekapcsolta az adatok felhasználói élmény, törölhetnek és hozhatnak létre az alkalmazásjelszókat, a saját alkalmazások portál használatával.

Ha a biztonsági adatok élményt a rendszergazda még nem aktív, kövesse az utasításokat és az információkat a a [alkalmazásjelszavakat a kétlépéses ellenőrzés kezelése](multi-factor-authentication-end-user-app-passwords.md) szakaszban.

### <a name="to-create-app-passwords-using-the-my-apps-portal"></a>Alkalmazásjelszók használatával a saját alkalmazások portál létrehozása

1. Jelentkezzen be munkahelyi vagy iskolai fiókjával.

2. Nyissa meg myapps.microsoft.com, jelölje ki a nevet az oldal jobb felső sarokban, majd válassza ki **profil**.

3. Az a **fiók kezelése** területen válassza **biztonsági adatok szerkesztése**.

    ![Profil képernyő és Szerkesztés biztonsági információ hivatkozás kiemelésével](media/security-info/security-info-profile.png)

4. Az a **a fiókja biztonságának megőrzéséhez** képernyőn válassza ki **adja hozzá a biztonsági adatok**.

    ![Biztonsági adatai képernyőjén, meglévő, szerkeszthető információval](media/security-info/security-info-edit-add-info.png)

5. Az a **adja hozzá a biztonsági adatok** képernyőn válassza ki **alkalmazásjelszót**.

6. Az a **alkalmazásjelszó létrehozása** képernyőn adjon meg egy nevet az alkalmazásjelszót, majd válassza ki **tovább**.

    ![Képernyő, ahol nevezze el az alkalmazásjelszó](media/security-info/security-info-name-app-password.png)

7. Válassza ki **másolási** a jelszó másolása a vágólapra, és válassza a **tovább**.

    ![A másolási alkalmazásjelszót képernyő](media/security-info/security-info-create-app-password.png)
    
8. Ellenőrizze, hogy az alkalmazásjelszót megjelenik-e a **a fiókja biztonságának megőrzéséhez** képernyő.

    ![Tartsa biztonságos képernyő, az alkalmazásjelszóra](media/security-info/security-info-keep-secure-app-password.png)

### <a name="to-delete-app-passwords-using-the-my-apps-portal"></a>Alkalmazásjelszavak használata a saját alkalmazások portál törlése

1. Az a **a fiókja biztonságának megőrzéséhez** képernyőn válassza ki a **X** melletti törli az alkalmazásjelszót.

    ![Tartsa biztonságos képernyő, alkalmazásjelszó törlése](media/security-info/security-info-keep-secure-delete-app-password.png)

2. Az a **alkalmazásjelszó törlése** képernyőn válassza ki **törlése**.

    ![Jelszó képernyő törlése](media/security-info/security-info-keep-secure-delete-app-password2.png)

## <a name="next-steps"></a>További lépések

- Ha kell biztonsági adatait, hajtsa végre a következő témakör utasításait a [a biztonsági adatok kezelése](security-info-manage-settings.md) cikk.

- Biztonsági adatokat, és mi mindenre képes kapcsolatos további általános információ: [biztonsági adatok áttekintése](user-help-security-info-overview.md) 