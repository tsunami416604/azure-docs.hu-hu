---
title: Felhasználók és csoportok hozzárendelése egy alkalmazáshoz |} A Microsoft Docs
description: Felhasználók hozzárendelése az alkalmazáshoz való hozzáférés biztosítása
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: d357a9a7f249127289a256685d9555f777742b68
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356663"
---
# <a name="how-to-assign-users-and-groups-to-an-application"></a>Felhasználók és csoportok hozzárendelése egy alkalmazáshoz

Mielőtt a felhasználók ezt az alábbi egy adott alkalmazáshoz, első kell **hozzárendelheti őket az alkalmazás** való hozzáférést biztosít számukra:

-   Hozzáférés egy alkalmazás által **ellenőrizheti, hogy az alkalmazás URL-CÍMÉT közvetlenül** (más néven SP által kezdeményezett bejelentkezés).

-   Az alkalmazás eléréséhez a **felhasználói hozzáférési URL-címe** olyan alkalmazást **tulajdonságok** (más néven Identitásszolgáltató által kezdeményezett bejelentkezési) lap.

-   Tekintse meg az alkalmazások jelennek meg azok [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com/) vagy mobilalkalmazás.

-   Tekintse meg az alkalmazások jelennek meg azok [Office 365 Alkalmazásindítójában](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

## <a name="methods-to-assign-applications-with-azure-active-directory"></a>Módszerek az Azure Active Directoryval alkalmazások hozzárendelése 

3 módon hozzárendelheti az alkalmazásokat az Azure Active Directoryval:

-   [Felhasználó hozzárendelése közvetlenül egy alkalmazás-rendszergazdaként](#assign-a-user-directly-as-an-administrator)

-   [Csoport hozzárendelése közvetlenül egy alkalmazás-rendszergazdaként](#assign-a-group-directly-to-an-application-as-an-administrator)

-   [A felhasználók a saját alkalmazások kereséséhez önkiszolgáló alkalmazás-hozzáférés engedélyezése](#enable-self-service-application-access-to-allow-users-to-find-their-own-applications)

## <a name="assign-a-user-directly-as-an-administrator"></a>Felhasználó hozzárendelése közvetlenül rendszergazdaként

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

12. **Választható lehetőség:** Ha szeretné **egynél több felhasználó hozzáadása**, írjon be egy másik **teljes név** vagy **e-mail-cím** be a **Keresés név alapján, vagy e-mail-cím** keresőmezőbe, majd kattintson a jelölőnégyzet bejelölésével adja hozzá a felhasználót a **kijelölt** listája.

13. Amikor elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

Után rövid idő alatt a kiválasztott felhasználók tudják elindítani ezeket az alkalmazásokat a megoldás leírása szakaszban leírt módszerek használatával.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Csoport hozzárendelése közvetlenül egy alkalmazás-rendszergazdaként

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

12. **Választható lehetőség:** Ha szeretné **egynél több csoport hozzáadása**, írjon be egy másik **teljes csoportnév** be a **Keresés név vagy e-mail cím alapján** keresőmezőbe, és a jelölőnégyzet bejelölésével adja hozzá a csoport a **kijelölt** listája.

13. Ha befejezte a csoportok kiválasztásával, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** ablaktáblán válassza ki a szerepkör hozzárendelése a kiválasztott csoportokhoz.

15. Kattintson a **hozzárendelése** gombra az alkalmazás a kijelölt csoportokhoz hozzárendelni.

Rövid idő után a felhasználók a kijelölt csoportok lehet tudja majd elindítani ezeket az alkalmazásokat a megoldás leírása szakaszban leírt módszerek használatával. Ha ezek a dinamikus csoportok, lehet néhány további feldolgozás késéssel jelennek meg ezek a hozzárendelt csoportokat lévő felhasználók ezeket a hozzárendeléseket a.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>A felhasználók a saját alkalmazások kereséséhez önkiszolgáló alkalmazás-hozzáférés engedélyezése

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

10. **Választható lehetőség:** előtt egy üzleti jóváhagyás megkövetelése, ha a felhasználók jogosultak-e a hozzáférést, és állítsa a **az alkalmazáshoz való hozzáférés engedélyezéséhez jóváhagyás szükséges?** kapcsolót **Igen**.

11. **Választható lehetőség: A csak az a jelszavas egyszeri bejelentkezést használó alkalmazások** szeretné ezeket üzleti jóváhagyók kapnak az alkalmazáshoz jóváhagyott felhasználók jelszavainak, ha a **jóváhagyók beállíthatják a felhasználó a az alkalmazáshoz használt jelszavait?**  kapcsolót **Igen**.

12. **Választható lehetőség:** a munkahelyi jóváhagyónak, akik jogosultak a hagyja jóvá az alkalmazáshoz való hozzáférés megadásához kattintson a címke melletti a választó **kik hagyhatják jóvá az alkalmazáshoz való hozzáférést?** legfeljebb 10 egyéni kiválasztásához munkahelyi jóváhagyónak.

  >[!NOTE]
  >Csoportok nem támogatottak.
  >
  >

13. **Választható lehetőség:** **alkalmazásokhoz, amelyeknél a szerepkörök**, ha szeretné az önkiszolgáló felhasználók hozzárendelése egy szerepkörhöz, kattintson a Tovább gombra a választó a **melyik szerepkörhöz legyenek hozzárendelve a felhasználók ebben az alkalmazásban?** Válassza ki a szerepkört, amelyhez hozzá kell rendelni ezeket a felhasználókat.

14. Kattintson a **mentése** gombra a Befejezés gombra a panel tetején.

Miután elvégezte az önkiszolgáló alkalmazás-konfigurációs, felhasználók kaphatnak a [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com/) , és kattintson a **+ Hozzáadás** , amelyhez engedélyezte az önkiszolgáló alkalmazások gomb a hozzáférés. Munkahelyi jóváhagyónak is megjelenik egy értesítés a saját [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com/). Engedélyezheti egy e-mail értesíti őket, amikor a felhasználó által kért a jóváhagyást igénylő alkalmazásokhoz való hozzáférés. 

Ezek a jóváhagyások csak egyetlen jóváhagyási munkafolyamatok támogatja, ami azt jelenti, hogy több jóváhagyóval ad meg, ha egyetlen jóváhagyók bármelyike előfordulhat, hogy jóváhagyó hozzáférni az alkalmazáshoz.

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)
