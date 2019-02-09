---
title: Felhasználók és csoportok hozzárendelése egy alkalmazáshoz |} A Microsoft Docs
description: Felhasználók hozzárendelése az alkalmazáshoz való hozzáférés biztosítása
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: celested
ms.openlocfilehash: d5fa3c84e7934511e5b37ac1e1396c7fe58c00ae
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962026"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Felhasználók és csoportok hozzárendelése egy alkalmazáshoz az Azure Active Directoryban
Ez a cikk bemutatja, hogyan felhasználók vagy csoportok hozzárendelése az Azure Active Directoryban (Azure AD-) alkalmazáshoz. Felhasználók először hozzá kell rendelni egy alkalmazás előtt a rendszergazda is hozzáférést biztosít nekik a következőket:

-   Hozzáférés egy alkalmazás által **ellenőrizheti, hogy az alkalmazás URL-CÍMÉT közvetlenül** (más néven SP által kezdeményezett bejelentkezés).

-   Az alkalmazás eléréséhez a **felhasználói hozzáférési URL-címe** olyan alkalmazást **tulajdonságok** (más néven Identitásszolgáltató által kezdeményezett bejelentkezési) lap.

-   Tekintse meg az alkalmazások jelennek meg azok [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com/) vagy mobilalkalmazás.

-   Tekintse meg az alkalmazások jelennek meg azok [Office 365 Alkalmazásindítójában](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

## <a name="prerequisties"></a>Prerequisties
Felhasználók és csoportok alkalmazásokhoz való hozzárendelés előtt meg kell kérnie a felhasználó-hozzárendelés. A felhasználó-hozzárendelés szükséges:

1. Jelentkezzen be rendszergazdai fiókkal az Azure Portalon.
2. Kattintson a a **minden szolgáltatás** a főmenü elemére.
3. Válassza ki azt a könyvtárat az alkalmazás használ.
4. Kattintson a **vállalati alkalmazások** fülre.
5. Ebben a könyvtárban társított alkalmazások listájából válassza ki az alkalmazást.
6. Kattintson a **tulajdonságok** fülre.
7. Módosítsa a **kell felhasználó-hozzárendelés?** váltógomb Igen.
8. Kattintson a **mentése** gombra a képernyő tetején.

## <a name="assign-users"></a>Felhasználók hozzárendelése

Közvetlenül rendelni egy vagy több felhasználó egy alkalmazást, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüből.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

    * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7.  Ha az alkalmazás betöltött, kattintson a **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **Hozzáadás** a gombot a **felhasználók és csoportok** listán, megnyílik a **hozzárendelés hozzáadása** ablaktáblán.

9.  Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes név** vagy **e-mail-cím** Önt érdeklő való hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **felhasználói** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a felhasználó profilfényképének vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Nem kötelező:** Ha szeretné **egynél több felhasználó hozzáadása**, írjon be egy másik **teljes név** vagy **e-mail-cím** be a **Keresés név vagy e-mail cím alapján** keresőmezőbe, majd kattintson a jelölőnégyzet bejelölésével adja hozzá a felhasználót a **kijelölt** listája.

13. Amikor elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

Után rövid idő alatt a kiválasztott felhasználók tudják elindítani ezeket az alkalmazásokat a megoldás leírása szakaszban leírt módszerek használatával.

## <a name="assign-groups"></a>Csoportok hozzárendelése

Közvetlenül egy alkalmazás hozzárendelése egy vagy több csoportot, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüből.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

    * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7.  Ha az alkalmazás betöltött, kattintson a **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **Hozzáadás** a gombot a **felhasználók és csoportok** listán, megnyílik a **hozzárendelés hozzáadása** ablaktáblán.

9.  Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes csoportnév** a csoport Önt érdeklő való hozzárendelése a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **csoport** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a csoport profilfénykép, vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Nem kötelező:** Ha szeretné **egynél több csoport hozzáadása**, írjon be egy másik **teljes csoportnév** be a **Keresés név vagy e-mail cím alapján** keresőmezőbe, és kattintson a jelölőnégyzetbe, ez a csoport hozzáadása az a **kijelölt** listája.

13. Ha befejezte a csoportok kiválasztásával, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** ablaktáblán válassza ki a szerepkör hozzárendelése a kiválasztott csoportokhoz.

15. Kattintson a **hozzárendelése** gombra az alkalmazás a kijelölt csoportokhoz hozzárendelni.

Rövid idő után a felhasználók a kijelölt csoportok lehet tudja majd elindítani ezeket az alkalmazásokat a megoldás leírása szakaszban leírt módszerek használatával. Ha ezek a dinamikus csoportok, lehet néhány további feldolgozás késéssel jelennek meg ezek a hozzárendelt csoportokat lévő felhasználók ezeket a hozzárendeléseket a.

## <a name="enable-self-service-application-access"></a>Az önkiszolgáló alkalmazás-hozzáférés engedélyezése

Az önkiszolgáló alkalmazás-hozzáférés kiválóan alkalmas, hogy a felhasználók saját felderíteni az alkalmazások, igény szerint jóváhagyása ezeknek az alkalmazásoknak hozzáférést az üzleti csoport lehetővé teszik. Engedélyezheti, hogy az üzleti csoportok a hozzáférési paneljükön jelszó egyszeri bejelentkezést az alkalmazások jobb a felhasználókhoz rendelt hitelesítő adatok kezelésére.

Ahhoz, hogy az önkiszolgáló alkalmazás-hozzáférést egy alkalmazáshoz, hajtsa végre az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüből.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az önkiszolgáló engedélyezni kívánt alkalmazást a listából való hozzáférést.

7.  Ha az alkalmazás betöltött, kattintson a **önkiszolgáló** az alkalmazás bal oldali navigációs menüjében.

8.  Ehhez az alkalmazáshoz önkiszolgáló alkalmazás-hozzáférés engedélyezéséhez kapcsolja be a **engedélyezése a felhasználók számára az alkalmazáshoz való hozzáférés kérése?** kapcsolót **igen.**

9.  Ezután, amelyekhez a felhasználók, akik kérése az alkalmazáshoz való hozzáférést kell adni a csoport kijelöléséhez kattintson a választó a felirat melletti **melyik csoporthoz lesz hozzáadva a hozzárendelt felhasználók?** , és válasszon ki egy csoportot.

10. **Nem kötelező:** Ha szeretne egy üzleti jóváhagyás szükséges felhasználók férhessenek hozzá, állítsa be a **az alkalmazáshoz való hozzáférés engedélyezéséhez jóváhagyás szükséges?** kapcsolót **Igen**.

11. **Nem kötelező: Csak az a jelszavas egyszeri bejelentkezést használó alkalmazások számára** szeretné ezeket üzleti jóváhagyók kapnak az alkalmazáshoz jóváhagyott felhasználók jelszavainak, ha a **jóváhagyók beállíthatják a felhasználói jelszavakat ehhez a alkalmazás?**  kapcsolót **Igen**.

12. **Nem kötelező:** A munkahelyi jóváhagyónak, akik jogosultak a hagyja jóvá az alkalmazáshoz való hozzáférés megadásához kattintson a címke melletti a választó **kik hagyhatják jóvá az alkalmazáshoz való hozzáférést?** legfeljebb 10 egyes munkahelyi jóváhagyónak kiválasztásához.

  >[!NOTE]
  >Csoportok nem támogatottak.
  >
  >

13. **Nem kötelező:** **Alkalmazások, amelyeknél a szerepkörök**, ha szeretné az önkiszolgáló felhasználók hozzárendelése egy szerepkörhöz, kattintson a Tovább gombra a választó a **melyik szerepkörhöz legyenek hozzárendelve a felhasználók ebben az alkalmazásban?** , válassza ki a szerepkört, amelyhez Ezeknek a felhasználóknak hozzá kell rendelni.

14. Kattintson a **mentése** gombra a Befejezés gombra a panel tetején.

Miután elvégezte az önkiszolgáló alkalmazás-konfigurációs, felhasználók kaphatnak a [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com/) , és kattintson a **+ Hozzáadás** , amelyhez engedélyezte az önkiszolgáló alkalmazások gomb a hozzáférés. Munkahelyi jóváhagyónak is megjelenik egy értesítés a saját [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com/). Engedélyezheti egy e-mail értesíti őket, amikor a felhasználó által kért a jóváhagyást igénylő alkalmazásokhoz való hozzáférés. 

Ezek a jóváhagyások csak egyetlen jóváhagyási munkafolyamatok támogatja, ami azt jelenti, hogy több jóváhagyóval ad meg, ha egyetlen jóváhagyók bármelyike előfordulhat, hogy jóváhagyó hozzáférni az alkalmazáshoz.

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)
