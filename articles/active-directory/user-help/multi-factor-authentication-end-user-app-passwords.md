---
title: Hogyan lehet az Alkalmazásjelszavak használata az Azure MFA-ban? | Microsoft Docs
description: Ezen a lapon segít leírásában alkalmazásjelszók vannak, és hogy mire szolgálnak az az Azure MFA figyelembe véve.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: lizross
ms.custom: end-user
ms.openlocfilehash: 290458e95aaed0cc85d83539d9d870c334df45df
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059430"
---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Mik az Azure multi-factor Authentication Alkalmazásjelszókat?
Bizonyos böngészőn kívüli alkalmazások, például az Apple natív e-mail-ügyfélprogram, amely az Exchange Active Sync jelenleg nem támogatja a multi-factor authentication szolgáltatás. Multi-factor Authentication hitelesítést felhasználónként kell engedélyezni. Ez azt jelenti, hogy ha egy felhasználó a többtényezős hitelesítés engedélyezve van, és a böngészőn kívüli alkalmazások használatához megpróbálnak, azok nem tudja ennek a végrehajtására. Egy alkalmazásjelszót lehetővé teszi, hogy ez megtörténjen. Feltételes hozzáférési házirendek és a felhasználónkénti MFA keresztül nem kényszeríti a multi-factor Authentication, az alkalmazásjelszavak nem hozható létre. Az alkalmazásjelszavak nem kell hozzáférés vezérléséhez feltételes hozzáférési szabályzatokat használó alkalmazásokat.

Ha már rendelkezik egy alkalmazásjelszót, akkor az eredeti jelszóval ezen böngészőn kívüli alkalmazások helyett ezzel. Ennek oka az, ha regisztrál a kétlépéses ellenőrzés, még közben nem ahhoz, hogy bárki a jelszavával jelentkezik, ha nem is végeznek a második ellenőrzési Microsoft. Az Apple natív levelezőprogramban a telefonon nem tud bejelentkezni, mert nem a kétlépéses ellenőrzéshez kérje meg. Ez a megoldás, ha egy biztonságosabb alkalmazásjelszót, amelyek nem használják ezeket az alkalmazásokat, amelyek nem támogatják a kétlépéses ellenőrzés napi, de csak. Az alkalmazásjelszót használnak, így alkalmazások multi-factor authentication kihagyásához és továbbra is működni.

> [!NOTE]
> Office 2013 ügyfelek (beleértve az Outlook) támogatja az új hitelesítési protokollok és használható a kétlépcsős ellenőrzéshez.  Ez azt jelenti, hogy engedélyezve van, az alkalmazásjelszavak nem szükséges, használja az Office 2013-ügyfelekkel.  További információkért lásd: [Office 2013 modern hitelesítés nyilvános előzetes verziójának bejelentése](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).


## <a name="how-to-use-app-passwords"></a>Az alkalmazásjelszavak használata
Az alábbiakban néhány Megjegyzendő tudnivalók alkalmazásjelszók használatával.

* Ne hozzon létre saját alkalmazásjelszókat. Ehelyett azok automatikusan jönnek létre. Csak meg kell adnia az alkalmazásjelszót alkalmazásonként egyszer, mivel a biztonságosabb, így egy könnyen megjegyezhető helyett egy összetettebb, az automatikusan generált jelszót használja.
* Jelenleg nincs egy legfeljebb 40 jelszó felhasználónként. Ha tárolórétegeket próbál létrehozni egy, a korlát elérése után, a program kéri előtt egy új létrehozásához törölje valamelyik meglévő alkalmazásjelszavát.
* Eszközönként, nem alkalmazásonként egy alkalmazásjelszót használjon. Például hozzon létre egy alkalmazásjelszót a hordozható számítógép, és használja azt az adott gépen lévő alkalmazásokat. Ezután hozzon létre egy második alkalmazás jelszavának asztali számítógépeken az alkalmazások használatára.
* Először regisztrálja a kétlépéses ellenőrzéshez kapnak egy alkalmazásjelszót.  Ha további azokat, létrehozhatja őket.



## <a name="creating-and-deleting-app-passwords"></a>Létrehozása és törlése az alkalmazásjelszók
Az első bejelentkezés során, kapnak egy alkalmazásjelszót, amelyet használhat.  Emellett akkor is törölhetnek és hozhatnak létre alkalmazásjelszókat később.  Ennek módja attól függ, hogyan használhatja a multi-factor authentication szolgáltatás. Meghatározásához, ahol kell alkalmazásjelszók kezeléséhez lépjen a következő kérdések megválaszolásával:

1. Használja a kétlépéses ellenőrzés személyes Microsoft-fiókja? Ha igen, tekintse át a [alkalmazásjelszókat, és a kétlépéses ellenőrzés](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) cikkben segítséget. Ha nem, továbbra is két kérdés.

2. OK, így a munkahelyi vagy iskolai fiók kétlépéses ellenőrzéshez. Lehet használni, jelentkezzen be az Office 365-alkalmazások? Ha igen, tekintse át [alkalmazásjelszó létrehozása Office 365-höz készült](https://support.office.com/article/Create-an-app-password-for-Office-365-3e7c860f-bda4-4441-a618-b53953ee1183) segítséget. Ha nem, továbbra is három kérdést.

3. Használja a kétlépéses ellenőrzés Microsoft Azure-ral? Ha igen, továbbra is a [kezelése az Azure Portalon alkalmazásjelszók](#manage-app-passwords-in-the-Azure-portal) című szakaszát. Ha nem, továbbra is kérdés negyedik.

4. Nem tudom, használhatja a kétlépéses ellenőrzés? Továbbra is a [kezelése a MyApps portálról szolgáltatással használt alkalmazásjelszók](#manage-app-passwords-with-the-myapps-portal) című szakaszát.


## <a name="manage-app-passwords-in-the-azure-portal"></a>Az Azure Portalon alkalmazásjelszók kezelése
Az Azure-ral használatakor a kétlépéses ellenőrzést, alkalmazásjelszavakat az Azure Portalon keresztül szeretné.



## <a name="manage-app-passwords-with-the-myapps-portal"></a>A MyApps portálról szolgáltatással használt alkalmazásjelszók kezelése.
Ha nem biztos abban, hogy a multi-factor authentication szolgáltatás használatáról, majd azt is mindig létrehozása és törlése alkalmazásjelszókat a myapps portálon keresztül.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>A MyApps portálról alkalmazásjelszó létrehozása
1. Jelentkezzen be [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Kattintson a jobb felső sarokban a nevét, és válasszon **profil**.
3. Válassza ki **további biztonsági ellenőrzés**.
   ![Válassza ki a további biztonsági ellenőrzés – képernyőkép](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Válassza ki **alkalmazásjelszók**.
   ![Válassza ki az alkalmazásjelszavak – képernyőkép](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Kattintson a **Create** (Létrehozás) gombra.
6. Adjon meg egy nevet az alkalmazásjelszót, majd kattintson **tovább**.
7. Az alkalmazás jelszó másolása a vágólapra, és illessze be az alkalmazásba.
   ![Alkalmazásjelszó létrehozása](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>A MyApps portálról alkalmazásjelszó törlése
1. Jelentkezzen be [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. A képernyő felső részén válassza ki a profilt.
3. Válassza ki **további biztonsági ellenőrzés**.

   ![Válassza ki a további biztonsági ellenőrzés – képernyőkép](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Válassza ki **alkalmazásjelszók**.

   ![Válassza ki az alkalmazásjelszavak – képernyőkép](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Kattintson a törölni kívánt alkalmazás jelszavával, mellett **törlése**.

   ![Alkalmazásjelszó törlése](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

6. Győződjön meg arról, hogy szeretné-e törölni, hogy a jelszó kattintva **Igen**.
7. Az alkalmazásjelszó törlése után kattintson **bezárásához**.

## <a name="next-steps"></a>További lépések

- [A kétlépéses ellenőrzési beállítások kezelése](multi-factor-authentication-end-user-manage-settings.md)

- Próbálja ki a [Microsoft Authenticator alkalmazás](microsoft-authenticator-app-how-to.md) ellenőrzése az alkalmazás értesítéseket, szövegek és a hívások fogadása helyett a bejelentkezések.
