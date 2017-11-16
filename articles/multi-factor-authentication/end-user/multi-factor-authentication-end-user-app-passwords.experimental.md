---
title: "Hogyan Alkalmazásjelszók használható az Azure MFA? | Microsoft Docs"
description: "Ezen a lapon segít megérteni az alkalmazásjelszók és azok szerepét a az Azure MFA figyelembe véve a felhasználóknak."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: c1c5a83fc54744dca1da0c7049f34fcf0bd5dfb4
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Mik a Azure multi-factor Authentication Alkalmazásjelszókat?
Bizonyos böngészőn kívüli alkalmazások, például az Apple natív e-mail-ügyfélprogram, amely használja az Exchange Active Sync jelenleg nem támogatják a multi-factor authentication. Többtényezős hitelesítést felhasználónként kell engedélyezni.  Ez azt jelenti, hogy a felhasználó multi-factor Authentication hitelesítés nem használható, ha:

- A felhasználó engedélyezte a többtényezős hitelesítés
- A felhasználó próbál a böngészőn kívüli alkalmazások használatához.

Az alkalmazásjelszó lehetővé teszi, hogy a felhasználó használhatja az alkalmazást.

Miután egy alkalmazásjelszót, használhatja az eredeti jelszóval ezen böngészőn kívüli alkalmazások helyett. Amikor regisztrál a kétlépéses ellenőrzést, akkor még szólítja fel a bárki jelentkezzen be a jelszót, ha azok nem is hajtsa végre a második ellenőrzési lehetővé teszik a nem Microsoft. A telefonján Apple natív e-mail-ügyfélprogram nem tud bejelentkezni, ha Ön, mert nem a kétlépéses ellenőrzéshez kérje meg. Ez a probléma megoldása, ha egy biztonságosabb alkalmazásjelszót, amelyek nem használják napi. Az alkalmazásjelszók csak ezek az alkalmazások, amelyek nem támogatják a kétlépéses ellenőrzést vonatkoznak. Használja a jelszót, hogy az alkalmazások multi-factor authentication kihagyásához és továbbra is működnek.


> [!NOTE]
> Office 2013-ügyfelek (például az Outlook) támogatja az új hitelesítési protokollok és a kétlépéses ellenőrzéshez használttal használható. Alkalmazásjelszók esetén nincs szükség Office 2013 ügyfelek való használatra.  További információkért lásd: [Office 2013 modern hitelesítés nyilvános előzetes bejelentette](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).


## <a name="how-to-use-app-passwords"></a>Alkalmazásjelszavak használata
Az alábbiakban néhány feltétlenül tudni az alkalmazásjelszókról:

* Ne hozzon létre egy saját alkalmazásjelszókat. Ezek automatikusan jönnek létre.
* Jelenleg csak egy legfeljebb 40 jelszó felhasználónként. 
* Ha megpróbál létrehozni egy alkalmazásjelszót a határérték elérése után, összekapcsolta törölje az egyik a meglévő alkalmazásjelszavak előtt hozzon létre egy újat.
* Minden eszközhöz, ne alkalmazásonként egy alkalmazásjelszót használni. Például hozzon létre egy alkalmazásjelszót a hordozható számítógép, és azt használja az összes, az alkalmazások adott hordozható számítógépen. Ezután hozzon létre egy második alkalmazásjelszót az asztalon az alkalmazások használatára. 
* Lehetősége van egy alkalmazásjelszót az első alkalommal regisztrál a kétlépéses ellenőrzéshez.  Ha további megfelelően van szüksége, létrehozhat őket.



## <a name="creating-and-deleting-app-passwords"></a>Létrehozása és törlése alkalmazásjelszók
Az első bejelentkezés során lehetősége van az alkalmazásjelszó használható.  Hozhat létre, és később törölni alkalmazásjelszókat. Alkalmazásjelszók törlési módja attól függ, hogy többtényezős hitelesítés használatát. Annak meghatározásához, ahol kell az alkalmazásjelszók kezeléséhez lépjen a következő kérdések megválaszolása: 

1. Használhatók a kétlépéses ellenőrzést személyes Microsoft-fiókja? Ha igen, tekintse át a [alkalmazásjelszók és a kétlépéses ellenőrzést](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) cikk segítségét. Ha nem, továbbra is két kérdés.

2. OK, hogy a kétlépéses ellenőrzést használni a munkahelyi vagy iskolai fiókját. Használ az Office 365-alkalmazásokhoz való bejelentkezéshez? Ha igen, tekintse át [hozza létre az alkalmazásjelszót az Office 365](https://support.office.com/article/Create-an-app-password-for-Office-365-3e7c860f-bda4-4441-a618-b53953ee1183) segítségét. Ha nem, továbbra is a három kérdést. 

3. Használhatók a kétlépéses ellenőrzésről a Microsoft Azure-ban? Ha igen, továbbra is a [kezelése az Azure portálon alkalmazásjelszók](#manage-app-passwords-in-the-Azure-portal) című szakaszát. Ha nem, továbbra is négy kérdés.

4. Nem biztos benne, ahol használhatja a kétlépéses ellenőrzést? Továbbra is a [MyApps Portal alkalmazás jelszavak kezelése](#manage-app-passwords-with-the-myapps-portal) című szakaszát. 


## <a name="manage-app-passwords-in-the-azure-portal"></a>Az Azure portálon app jelszavak kezelése
Az Azure-ral használatakor a kétlépéses ellenőrzést, hozzon létre az Azure portálon keresztül szeretné.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Alkalmazásjelszók létrehozása az Azure-portálon
1. Jelentkezzen be a klasszikus Azure portálra.
2. A lap tetején kattintson a jobb gombbal a felhasználónevét, és válassza ki a további biztonsági ellenőrzés.
3. A proofup lapon, a lap tetején jelölje ki az alkalmazásjelszók
4. Kattintson a **Create** (Létrehozás) gombra.
5. Adjon meg egy nevet a jelszót, és kattintson a **következő**
6. A jelszót a vágólapra másolja és illessze be az alkalmazást.
   
   ![Felhő](./media/multi-factor-authentication-end-user-app-passwords/app2.png)


### <a name="to-delete-app-passwords-in-the-azure-portal"></a>Az Azure portálon alkalmazásjelszók törlése
1. Jelentkezzen be a klasszikus Azure portálra.
2. A lap tetején kattintson a jobb gombbal a felhasználónevét, és válassza ki a további biztonsági ellenőrzés.
3. A lap tetején mellett további biztonsági ellenőrzési, válasszon **alkalmazásjelszókat.**
4. A törölni kívánt alkalmazásjelszót, mellett válassza ki **törlése**.
5. Kattintson a törlés jóváhagyásához **Igen**.
6. Az alkalmazásjelszót a törölt kattinthat **bezárása**.


## <a name="manage-app-passwords-with-the-myapps-portal"></a>A MyApps portal szolgáltatással használt alkalmazásjelszók kezelése.
Ha nem biztos abban, hogy a multi-factor authentication használatát, majd bármikor létrehozása és törlése a myapps portálon keresztül alkalmazásjelszókat.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>A hozza létre az alkalmazásjelszót a Myapps portál használatával
1. Jelentkezzen be [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Kattintson a jobb felső sarokban, és válassza a **profil**.
3. Válassza ki **további biztonsági ellenőrzés**.
   ![Válassza ki a további biztonsági ellenőrzés – képernyőkép](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Válassza ki **alkalmazásjelszók**.
   ![Válassza ki az alkalmazásjelszók – képernyőkép](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Kattintson a **Create** (Létrehozás) gombra.
6. Adjon meg egy nevet a jelszót, és kattintson a **következő**.
7. A jelszót a vágólapra másolja és illessze be az alkalmazást.
   ![Hozza létre az alkalmazásjelszót](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Törli az alkalmazásjelszó Myapps portál használatával
1. Jelentkezzen be [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. A lap tetején jelölje ki a profilt.
3. Válassza ki **további biztonsági ellenőrzés**.

   ![Válassza ki a további biztonsági ellenőrzés – képernyőkép](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Válassza ki **alkalmazásjelszók**.

   ![Válassza ki az alkalmazásjelszók – képernyőkép](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. A törölni kívánt alkalmazásjelszót, mellett kattintson **törlése**.

   ![Az alkalmazásjelszó törlése](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

6. Győződjön meg arról, hogy szeretné-e, hogy a jelszó törlése gombra kattintva **Igen**.
7. Az alkalmazásjelszót a törölt kattinthat **bezárása**.

## <a name="next-steps"></a>Következő lépések

- [A kétlépéses ellenőrzés beállításait kezelheti](multi-factor-authentication-end-user-manage-settings.md)

- Próbálja ki a [Microsoft Authenticator alkalmazás](microsoft-authenticator-app-how-to.md) ellenőrzése a bejelentkezéseket app értesítések szövegek vagy hívások fogadása helyett. 
