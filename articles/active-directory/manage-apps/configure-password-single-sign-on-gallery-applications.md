---
title: Az Azure AD Gallery-alkalmazás jelszavas egyszeri bejelentkezésének konfigurálása | Microsoft Docs
description: Alkalmazás konfigurálása biztonságos jelszó-alapú egyszeri bejelentkezéshez, ha az már szerepel az Azure AD Application Galleryben
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: eb37fe247901b799a845ce75723a4a6b535cbb28
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422586"
---
# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Az Azure AD Gallery-alkalmazás jelszavas egyszeri bejelentkezésének konfigurálása

Ha hozzáad egy alkalmazást az [Azure ad-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)-katalógusból, dönthet arról, hogy a felhasználók hogyan szeretnének bejelentkezni az alkalmazásba. Ezt a lehetőséget bármikor megadhatja úgy, hogy kijelöli a [Azure Portal](https://portal.azure.com/)egy vállalati alkalmazásának **egyszeri bejelentkezési** navigációs elemét.

Az Ön számára elérhető egyszeri bejelentkezési módszerek egyike a [jelszó-alapú egyszeri bejelentkezési](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) lehetőség. Ez nagyszerű módszer az alkalmazások Azure AD-ba való integrálásának gyors megkezdésére, és lehetővé teszi a következőket:

-   Engedélyezze az **egyszeri bejelentkezést a felhasználók** számára az Azure ad-vel integrált alkalmazáshoz tartozó felhasználónevek és jelszavak biztonságos tárolásával és visszajátszásával

-   Olyan alkalmazások **támogatása, amelyeknek több bejelentkezési mezőre van szükségük** olyan alkalmazásokhoz, amelyek több, mint a Felhasználónév és a jelszó mezőt igényelnek a bejelentkezéshez

-   A felhasználónevek és jelszavak beviteli mezőinek **testreszabása** a felhasználók számára az alkalmazás- [hozzáférési panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) a hitelesítő adatok megadásakor

-   Saját felhasználónevek és jelszavak megadásának engedélyezése a **felhasználók** számára az [alkalmazás-hozzáférési panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) manuálisan begépelt összes meglévő alkalmazás-fiókhoz

-   A felhasználóhoz rendelt felhasználónevek és jelszavak megadásának engedélyezése az **üzleti csoport tagjai** számára az önkiszolgáló [alkalmazás-hozzáférési](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) szolgáltatás használatával

-   A **rendszergazda** megadhatja a felhasználóhoz rendelt felhasználóneveket és jelszavakat a hitelesítő adatok frissítése funkció használatával [, amikor felhasználót rendel](#assign-a-user-to-an-application-directly) hozzá egy alkalmazáshoz

-   Annak engedélyezése, hogy a **rendszergazda** megadjon egy csoport által használt megosztott felhasználónevet vagy jelszót a hitelesítő adatok frissítése funkció használatával, amikor [egy csoportot rendel hozzá](#assign-an-application-to-a-group-directly) egy alkalmazáshoz

A következő szakasz azt ismerteti, hogyan engedélyezheti a [jelszó-alapú egyszeri bejelentkezést](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) egy olyan alkalmazásra, amely már szerepel az [Azure ad Application Galleryben](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="overview-of-steps-required"></a>A szükséges lépések áttekintése
Egy alkalmazás Azure AD-katalógusból való konfigurálásához a következőket kell tennie:

-   [Alkalmazás hozzáadása az Azure AD-katalógusból](#add-an-application-from-the-azure-ad-gallery)

-   [Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez](#configure-the-application-for-password-single-sign-on)

-   Az alkalmazás társítása felhasználóhoz vagy csoporthoz

    -   [Felhasználó közvetlen kiosztása egy alkalmazáshoz](#assign-a-user-to-an-application-directly)

    -   [Alkalmazás közvetlen társítása egy csoporthoz](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-katalógusból

Ha alkalmazást szeretne hozzáadni az Azure AD-katalógusból, kövesse az alábbi lépéseket:

1.  Nyissa [](https://portal.azure.com) meg a Azure Portalt, és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5.  kattintson a **Hozzáadás** gombra a **vállalati alkalmazások** ablaktábla jobb felső sarkában.

6.  A **Hozzáadás a** katalógusból szakaszban a **név megadása** szövegmezőbe írja be az alkalmazás nevét.

7.  Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

8.  Az alkalmazás hozzáadása előtt módosíthatja a nevét a **név** szövegmezőből.

9.  Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

Rövid idő elteltével megtekintheti az alkalmazás konfigurációs paneljét.

## <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez

Az egyszeri bejelentkezés az alkalmazáshoz való konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Válassza ki a mód **jelszavas alapú bejelentkezését.**

9. [Felhasználók kiosztása az alkalmazáshoz](#assign-a-user-to-an-application-directly).

10. Emellett a felhasználók nevében is megadhat hitelesítő adatokat. Ehhez válassza ki a felhasználókat, majd kattintson a **hitelesítő adatok frissítése** lehetőségre, és írja be a felhasználónevet és a jelszót a felhasználók nevében. Ellenkező esetben a rendszer a felhasználók számára az indításkor kéri a hitelesítő adatok megadását.

## <a name="assign-a-user-to-an-application-directly"></a>Felhasználó közvetlen kiosztása egy alkalmazáshoz

Ha egy vagy több felhasználót közvetlenül szeretne hozzárendelni egy alkalmazáshoz, kövesse az alábbi lépéseket:

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7. Az alkalmazás betöltése után kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

8. Kattintson a **Hozzáadás** a gombot a **felhasználók és csoportok** listán, megnyílik a **hozzárendelés hozzáadása** ablaktáblán.

9. Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes név** vagy **e-mail-cím** Önt érdeklő való hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **felhasználói** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a felhasználó profilfényképének vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Választható** Ha **egynél több felhasználót**szeretne felvenni, írjon be egy másik **teljes nevet** vagy **e-mail-címet** a **Keresés név vagy e-mail-cím** Keresés mezőbe, és kattintson a jelölőnégyzetre a felhasználó a **kiválasztott** listához való hozzáadásához.

13. Amikor elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

## <a name="assign-an-application-to-a-group-directly"></a>Alkalmazás közvetlen társítása egy csoporthoz

Ha egy vagy több csoportot szeretne közvetlenül egy alkalmazáshoz rendelni, kövesse az alábbi lépéseket:

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7. Az alkalmazás betöltése után kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

8. Kattintson a **Hozzáadás** a gombot a **felhasználók és csoportok** listán, megnyílik a **hozzárendelés hozzáadása** ablaktáblán.

9. Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes csoportnév** a csoport Önt érdeklő való hozzárendelése a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **csoport** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a csoport profilfénykép, vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Választható** Ha egynél **több csoportot**szeretne felvenni, írjon be egy másik **teljes nevet** a **Keresés név vagy e-mail-cím** keresőmezőbe, majd a jelölőnégyzetre kattintva vegye fel a csoportot a **kiválasztott** listára.

13. Ha befejezte a csoportok kiválasztásával, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** ablaktáblán válassza ki a szerepkör hozzárendelése a kiválasztott csoportokhoz.

15. Kattintson a **hozzárendelése** gombra az alkalmazás a kijelölt csoportokhoz hozzárendelni.

Rövid idő elteltével a kiválasztott felhasználók el tudják indítani ezeket az alkalmazásokat a hozzáférési panelen.

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)
