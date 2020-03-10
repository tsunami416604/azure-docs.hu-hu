---
title: Felhasználók és csoportok hozzárendelése egy alkalmazáshoz |} A Microsoft Docs
description: Felhasználók hozzárendelése az alkalmazáshoz való hozzáférés biztosítása
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd6b13dc56f8f948d50e2b3564712ed8f5b1476
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376437"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Felhasználók és csoportok hozzárendelése egy alkalmazáshoz az Azure Active Directoryban
Ez a cikk bemutatja, hogyan felhasználók vagy csoportok hozzárendelése az Azure Active Directoryban (Azure AD-) alkalmazáshoz. Felhasználók először hozzá kell rendelni egy alkalmazás előtt a rendszergazda is hozzáférést biztosít nekik a következőket:

-   Az alkalmazás eléréséhez nyissa **meg közvetlenül az alkalmazás URL-címét** (más néven az SP által kezdeményezett bejelentkezés).

-   Egy alkalmazás eléréséhez használja a **felhasználói hozzáférési URL-címet** az alkalmazás **Tulajdonságok** LAPJÁN (más néven identitásszolgáltató-alapú bejelentkezés).

-   Egy alkalmazás megjelenik az [alkalmazás hozzáférési paneljén](https://myapps.microsoft.com/) vagy a mobileszköz-alkalmazásban.

-   Tekintse meg az [Office 365 Application Launcher](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a)alkalmazásban megjelenő alkalmazást.

A csoport alapú hozzárendelések elérhetőségét a licencszerződés határozza meg. A csoport alapú hozzárendelés csak biztonsági csoportok esetén támogatott. A beágyazott csoporttagságok és a O365 csoportok jelenleg nem támogatottak.

## <a name="configure-the-application-to-require-assignment"></a>Az alkalmazás konfigurálása hozzárendelés megköveteléséhez

Egy alkalmazás konfigurálható úgy, hogy a hozzáféréshez megkövetelje a hozzárendelést. Hozzárendelés megkövetelése:

1. Jelentkezzen be a Azure Portal egy rendszergazdai fiókkal vagy az alkalmazás tulajdonosaként a **vállalati alkalmazások**területen.
2. Kattintson a főmenü **minden szolgáltatás** elemére.
3. Válassza ki azt a könyvtárat az alkalmazás használ.
4. Kattintson a **vállalati alkalmazások** fülre.
5. Ebben a könyvtárban társított alkalmazások listájából válassza ki az alkalmazást.
6. Kattintson a **Tulajdonságok** fülre.
7. Módosítani kívánja a **felhasználó-hozzárendelést?** váltson Igen értékre.
8. Kattintson a **Save (Mentés** ) gombra a képernyő tetején.

## <a name="assign-users"></a>Felhasználók hozzárendelése

Közvetlenül rendelni egy vagy több felhasználó egy alkalmazást, kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként vagy nem rendszergazdai alkalmazás tulajdonosaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a Azure Active Directory bal oldali navigációs menüjében kattintson a **vállalati alkalmazások** elemre.

5.  kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

    * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6.  Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7.  Az alkalmazás betöltése után kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **Hozzáadás** gombra a **felhasználók és csoportok** lista tetején a **hozzárendelés hozzáadása** ablaktábla megnyitásához.

9.  a **hozzárendelés hozzáadása** panelen kattintson a **felhasználók és csoportok** választóra.

10. Írja be annak a felhasználónak a **teljes nevét** vagy **e-mail-címét** , **akit a keresés név vagy e-mail cím keresőmező alapján** szeretne hozzárendelni.

11. A **jelölőnégyzet**megjelenítéséhez vigye a kurzort a listában szereplő **felhasználó** fölé. Kattintson a felhasználó profiljának fényképe vagy emblémája melletti jelölőnégyzetre a felhasználó a **kiválasztott** listához való hozzáadásához.

12. Nem **kötelező:** Ha **egynél több felhasználót szeretne felvenni**, írjon be egy másik **teljes nevet** vagy **e-mail-címet** a **Keresés név vagy e-mail-cím** Keresés mezőbe, és kattintson a jelölőnégyzetre a felhasználó a **kiválasztott** listához való hozzáadásához.

13. Ha befejezte a felhasználók kiválasztását, kattintson a **kiválasztás** gombra, és adja hozzá őket az alkalmazáshoz hozzárendelni kívánt felhasználók és csoportok listájához.

14. Nem **kötelező:** kattintson a **szerepkör** kiválasztása lehetőségre a **hozzárendelés hozzáadása** panelen, és válassza ki a kiválasztott felhasználókhoz hozzárendelni kívánt szerepkört.

15. Kattintson a **hozzárendelés** gombra az alkalmazás a kijelölt felhasználókhoz való hozzárendeléséhez.

Rövid idő elteltével a kiválasztott felhasználók el tudják indítani ezeket az alkalmazásokat a megoldás leírása szakaszban leírt módszerek alapján.

## <a name="assign-groups"></a>Csoportok hozzárendelése

Közvetlenül egy alkalmazás hozzárendelése egy vagy több csoportot, kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy nem rendszergazdai alkalmazásként egy hozzárendelt prémium szintű Azure ad-licenccel.

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a Azure Active Directory bal oldali navigációs menüjében kattintson a **vállalati alkalmazások** elemre.

5.  kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

    * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6.  Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7.  Az alkalmazás betöltése után kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **Hozzáadás** gombra a **felhasználók és csoportok** lista tetején a **hozzárendelés hozzáadása** ablaktábla megnyitásához.

9.  a **hozzárendelés hozzáadása** panelen kattintson a **felhasználók és csoportok** választóra.

10. Írja be annak a csoportnak a **teljes csoportjának nevét** , amelyet szeretne a **Keresés név vagy e-mail-cím** keresése mezőbe rendelni.

11. A **jelölőnégyzet**megjelenítéséhez vigye a kurzort a listában a **csoport** fölé. Kattintson a csoport profiljának fényképe vagy emblémája melletti jelölőnégyzetre a felhasználó a **kiválasztott** listához való hozzáadásához.

12. Nem **kötelező:** Ha egynél **több csoportot szeretne felvenni**, írjon be egy másik **teljes nevet** a **Keresés név vagy e-mail-cím** keresőmezőbe, majd a jelölőnégyzetre kattintva vegye fel a csoportot a **kiválasztott** listára.

13. Ha befejezte a csoportok kijelölését, kattintson a **kiválasztás** gombra, és adja hozzá őket az alkalmazáshoz hozzárendelni kívánt felhasználók és csoportok listájához.

14. Nem **kötelező:** kattintson a **szerepkör** kiválasztása lehetőségre a **hozzárendelés hozzáadása** panelen, és válassza ki a kiválasztott csoportokhoz hozzárendelni kívánt szerepkört.

15. Kattintson a **hozzárendelés** gombra az alkalmazás a kijelölt csoportokhoz való hozzárendeléséhez.

Rövid idő elteltével a kiválasztott csoportokban lévő felhasználók el tudják indítani ezeket az alkalmazásokat a megoldás leírása szakaszban leírt módszerek alapján. Ha ezek a dinamikus csoportok, lehet néhány további feldolgozás késéssel jelennek meg ezek a hozzárendelt csoportokat lévő felhasználók ezeket a hozzárendeléseket a.

## <a name="enable-self-service-application-access"></a>Az önkiszolgáló alkalmazás-hozzáférés engedélyezése

Az önkiszolgáló alkalmazás-hozzáférés kiválóan alkalmas, hogy a felhasználók saját felderíteni az alkalmazások, igény szerint jóváhagyása ezeknek az alkalmazásoknak hozzáférést az üzleti csoport lehetővé teszik. Engedélyezheti, hogy az üzleti csoportok a hozzáférési paneljükön jelszó egyszeri bejelentkezést az alkalmazások jobb a felhasználókhoz rendelt hitelesítő adatok kezelésére.

Ahhoz, hogy az önkiszolgáló alkalmazás-hozzáférést egy alkalmazáshoz, hajtsa végre az alábbi lépéseket:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. a Azure Active Directory bal oldali navigációs menüjében kattintson a **vállalati alkalmazások** elemre.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki az önkiszolgáló engedélyezni kívánt alkalmazást a listából való hozzáférést.

7. Az alkalmazás betöltése után kattintson az **önkiszolgáló** elemre az alkalmazás bal oldali navigációs menüjében.

8. Az alkalmazás önkiszolgáló alkalmazásokhoz való hozzáférésének engedélyezéséhez kapcsolja be a **felhasználók számára az alkalmazáshoz való hozzáférés kérését?** váltson át az Igen értékre **.**

9. Ezután válassza ki azt a csoportot, amelyhez hozzá szeretné adni az alkalmazáshoz hozzáférést kérő felhasználókat, kattintson a címke melletti választóra, **amelyhez hozzá** kívánja adni a felhasználókat, és válasszon ki egy csoportot.

10. Nem **kötelező:** Ha szeretné megkövetelni, hogy a felhasználók hozzáférhessenek-e az üzleti jóváhagyáshoz, akkor az **alkalmazáshoz való hozzáférés engedélyezése előtt állítsa be a jóváhagyás megkövetelése** beállítást. az **Igen**értékre vált.

11. **Opcionális: csak jelszó egyszeri bejelentkezést használó alkalmazásokhoz,** ha engedélyezni szeretné, hogy ezek az üzleti jóváhagyók megadják az alkalmazásnak a jóváhagyott felhasználók számára eljuttatott jelszavakat, állítsa be az **alkalmazáshoz tartozó felhasználók jelszavának engedélyezése a jóváhagyóknak beállítást?** váltás az **Igen**értékre.

12. Nem **kötelező:** Azon üzleti jóváhagyók megadásához, akik számára engedélyezett az alkalmazáshoz való hozzáférés jóváhagyása, kattintson az **alkalmazáshoz való hozzáférés jóváhagyására jogosult** címke melletti választóra? a legfeljebb 10 egyéni üzleti jóváhagyó kiválasztásához.

    >[!NOTE]
    >Csoportok nem támogatottak.
    >
    >

13. Nem **kötelező:** **a szerepköröket közzétevő alkalmazások esetében**, ha az önkiszolgáló jóváhagyott felhasználókat szeretné hozzárendelni egy szerepkörhöz, kattintson arra a választóra, amelynek a szerepkörét hozzá szeretné rendelni az **alkalmazáshoz?** lehetőségre kattintva válassza ki azt a szerepkört, amelyhez ezeket a felhasználókat hozzá kell rendelni.

14. A befejezéshez kattintson a **Mentés** gombra a panel tetején.

Az önkiszolgáló alkalmazás konfigurálásának befejezése után a felhasználók az [alkalmazás-hozzáférési panelre](https://myapps.microsoft.com/) léphetnek, és a **+ Hozzáadás** gombra kattintva megkereshetik azokat az alkalmazásokat, amelyekhez engedélyezte az önkiszolgáló hozzáférést. Az üzleti jóváhagyók az [alkalmazás hozzáférési paneljén](https://myapps.microsoft.com/)is megtekinthetik az értesítéseket. Engedélyezheti egy e-mail értesíti őket, amikor a felhasználó által kért a jóváhagyást igénylő alkalmazásokhoz való hozzáférés. 

Ezek a jóváhagyások csak az egyszeri jóváhagyási munkafolyamatokat támogatják, ami azt jelenti, hogy ha több jóváhagyót ad meg, akkor egyetlen jóváhagyó jóváhagyhatja az alkalmazáshoz való hozzáférést.

## <a name="next-steps"></a>Következő lépések
[Egyszeri bejelentkezés biztosítása az alkalmazásokba az Application proxy használatával](application-proxy-configure-single-sign-on-with-kcd.md)
