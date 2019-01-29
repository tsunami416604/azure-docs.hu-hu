---
title: Az önkiszolgáló alkalmazás-hozzárendelés konfigurálása |} A Microsoft Docs
description: A felhasználók a saját alkalmazások kereséséhez önkiszolgáló alkalmazás-hozzáférés engedélyezése
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: barbkess
ms.openlocfilehash: b2d08624a95d799962e331e3b4708dfa792d3ca8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55169134"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Az önkiszolgáló alkalmazás-hozzárendelés konfigurálása

Mielőtt a felhasználók saját felderítheti alkalmazásokat a hozzáférési paneljükön, engedélyeznie kell a **önkiszolgáló alkalmazás-hozzáférés** bármely olyan alkalmazások, amelyek szeretné engedélyezni a felhasználók számára helyi felderítését, és kérjen hozzáférést.

Ez a funkció kiválóan alkalmas, hogy időt és pénzt takaríthat meg, egy IT-csoport és az Azure Active Directoryval a modern alkalmazások központi telepítésének részeként erősen ajánlott.

Ezzel a funkcióval a következőket teheti:

-   Önálló felderítése az alkalmazások a felhasználók a [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com/) anélkül, hogy az informatikai csoport bothering.

-   Ezek a felhasználók hozzáadása az előre konfigurált csoporthoz, tekintse meg, akik hozzáférést igényelt, távolítsa el a hozzáférést, és a hozzájuk rendelt szerepkörök kezelése.

-   Szükség esetén lehetővé teszik egy üzleti jóváhagyó hozzáférés alkalmazáskérések jóváhagyása, így nem kell az informatikai csoport.

-   Választható lehetőségként konfigurálhatja akár 10 számára, akik előfordulhat, hogy hagyja jóvá az alkalmazáshoz való hozzáférést.

-   Szükség esetén engedélyezze az üzleti jóváhagyó beállítása a jelszavakat felhasználókhoz segítségével jelentkezzen be az alkalmazás közvetlenül a az üzleti jóváhagyó [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com/).

-   Szükség esetén automatikusan hozzárendelni közvetlenül egy alkalmazás-szerepkörhöz hozzárendelt felhasználók önkiszolgáló.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>A felhasználók a saját alkalmazások kereséséhez önkiszolgáló alkalmazás-hozzáférés engedélyezése

Az önkiszolgáló alkalmazás-hozzáférés kiválóan alkalmas, hogy a felhasználók saját felderíteni az alkalmazások, igény szerint jóváhagyása ezeknek az alkalmazásoknak hozzáférést az üzleti csoport lehetővé teszik. Engedélyezheti, hogy az üzleti csoportok a hozzáférési paneljükön jelszó egyszeri bejelentkezést az alkalmazások jobb a felhasználókhoz rendelt hitelesítő adatok kezelésére.

Ahhoz, hogy az önkiszolgáló alkalmazás-hozzáférést egy alkalmazáshoz, hajtsa végre az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüből.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

    * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az önkiszolgáló engedélyezni kívánt alkalmazást a listából való hozzáférést.

7.  Ha az alkalmazás betöltött, kattintson a **önkiszolgáló** az alkalmazás bal oldali navigációs menüjében.

8.  Ehhez az alkalmazáshoz önkiszolgáló alkalmazás-hozzáférés engedélyezéséhez kapcsolja be a **engedélyezése a felhasználók számára az alkalmazáshoz való hozzáférés kérése?** kapcsolót **igen.**

9.  Ezután, amelyekhez a felhasználók, akik kérése az alkalmazáshoz való hozzáférést kell adni a csoport kijelöléséhez kattintson a választó a felirat melletti **melyik csoporthoz lesz hozzáadva a hozzárendelt felhasználók?** , és válasszon ki egy csoportot.
  
    > [!NOTE]
    > Szinkronizálja a helyi csoportok nem támogatottak a csoportot, amelyhez az alkalmazáshoz való hozzáférést kérő felhasználók hozzáadásra kerüljenek használatra.
  
10. **Nem kötelező:** Ha szeretne egy üzleti jóváhagyás szükséges felhasználók férhessenek hozzá, állítsa be a **az alkalmazáshoz való hozzáférés engedélyezéséhez jóváhagyás szükséges?** kapcsolót **Igen**.

11. **Nem kötelező: Csak az a jelszavas egyszeri bejelentkezést használó alkalmazások számára** szeretné ezeket üzleti jóváhagyók kapnak az alkalmazáshoz jóváhagyott felhasználók jelszavainak, ha a **jóváhagyók beállíthatják a felhasználói jelszavakat ehhez a alkalmazás?**  kapcsolót **Igen**.

12. **Nem kötelező:** A munkahelyi jóváhagyónak, akik jogosultak a hagyja jóvá az alkalmazáshoz való hozzáférés megadásához kattintson a címke melletti a választó **kik hagyhatják jóvá az alkalmazáshoz való hozzáférést?** legfeljebb 10 egyes munkahelyi jóváhagyónak kiválasztásához.

     > [!NOTE]
     > Csoportok nem támogatottak.
     >
     >

13. **Nem kötelező:** **Alkalmazások, amelyeknél a szerepkörök**, ha szeretné az önkiszolgáló felhasználók hozzárendelése egy szerepkörhöz, kattintson a Tovább gombra a választó a **melyik szerepkörhöz legyenek hozzárendelve a felhasználók ebben az alkalmazásban?** , válassza ki a szerepkört, amelyhez Ezeknek a felhasználóknak hozzá kell rendelni.

14. Kattintson a **mentése** gombra a Befejezés gombra a panel tetején.

Miután elvégezte az önkiszolgáló alkalmazás-konfigurációs, felhasználók kaphatnak a [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com/) , és kattintson a **+ Hozzáadás** , amelyhez engedélyezte az önkiszolgáló alkalmazások gomb a hozzáférés. Munkahelyi jóváhagyónak is megjelenik egy értesítés a saját [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com/). Engedélyezheti egy e-mail értesíti őket, amikor a felhasználó által kért a jóváhagyást igénylő alkalmazásokhoz való hozzáférés. 

Ezek a jóváhagyások csak egyetlen jóváhagyási munkafolyamatok támogatja, ami azt jelenti, hogy több jóváhagyóval ad meg, ha egyetlen jóváhagyók bármelyike előfordulhat, hogy jóváhagyó hozzáférni az alkalmazáshoz.

## <a name="next-steps"></a>További lépések
[Az Azure Active Directory beállítása önkiszolgáló csoportkezelésre](../users-groups-roles/groups-self-service-management.md)
