---
title: Önkiszolgáló alkalmazás-hozzárendelés konfigurálása | Microsoft Docs
description: Az önkiszolgáló alkalmazás-hozzáférés engedélyezése lehetővé teszi, hogy a felhasználók megtalálják a saját alkalmazásaikat
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
ms.topic: article
ms.date: 10/23/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbeb25f1190754b4264cfbab9d8a03a6b65c4dff
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895963"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Önkiszolgáló alkalmazás-hozzárendelés konfigurálása

Ahhoz, hogy a felhasználók saját maguk is felfedezzék az alkalmazásokat a hozzáférési paneljéről, engedélyeznie kell az **önkiszolgáló alkalmazásokhoz való hozzáférést** minden olyan alkalmazáshoz, amelyet engedélyezni kíván a felhasználók számára az önfelderítéshez és a hozzáférés kéréséhez. Ez a funkció olyan alkalmazásokhoz érhető el, amelyeket az Azure [ad](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)-katalógusból, az [Azure ad Application proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) -ból vagy [felhasználói vagy rendszergazdai hozzájárulással](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience)adott hozzá. 

Ez a funkció nagyszerű lehetőséget nyújt arra, hogy időt és pénzt takarítson meg informatikai csoportként, és a Azure Active Directory használatával korszerű alkalmazások központi telepítésének részeként erősen ajánlott.

A szolgáltatás használatával a következőket teheti:

-   Lehetővé teszi a felhasználók számára, hogy az IT-csoport kellemetlensége nélkül is felfedezzék az alkalmazásokat az [alkalmazás-hozzáférési panelről](https://myapps.microsoft.com/) .

-   Adja hozzá ezeket a felhasználókat egy előre konfigurált csoporthoz, így megtekintheti, hogy ki igényelt hozzáférést, távolítsa el a hozzáférést, és kezelje a hozzájuk rendelt szerepköröket.

-   Opcionálisan engedélyezheti az üzleti jóváhagyónak az alkalmazás-hozzáférési kérelmek jóváhagyását, hogy az informatikai csoportnak ne kelljen.

-   Opcionálisan akár 10 személyt is beállíthat, akik jóváhagyják az alkalmazáshoz való hozzáférést.

-   Opcionálisan engedélyezheti az üzleti jóváhagyó számára, hogy beállítsa azokat a jelszavakat, amelyeket a felhasználók az alkalmazásba való bejelentkezéshez használhatnak közvetlenül az üzleti jóváhagyó [alkalmazás-hozzáférési paneljéről](https://myapps.microsoft.com/).

-   Opcionálisan automatikusan hozzárendelheti az önkiszolgáló hozzárendelt felhasználókat egy alkalmazás-szerepkörhöz.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Az önkiszolgáló alkalmazás-hozzáférés engedélyezése lehetővé teszi, hogy a felhasználók megtalálják a saját alkalmazásaikat

Az önkiszolgáló alkalmazás-hozzáférés lehetővé teszi a felhasználók számára az alkalmazások önfelderítését, opcionálisan lehetővé teszi, hogy az üzleti csoport jóváhagyja az alkalmazásokhoz való hozzáférést. Lehetővé teheti az üzleti csoport számára, hogy az adott felhasználókhoz rendelt hitelesítő adatokat a hozzáférési panelektől közvetlenül a jelszó egyszeri bejelentkezésre szolgáló alkalmazásokban kezelhesse.

Az önkiszolgáló alkalmazások alkalmazáshoz való hozzáférésének engedélyezéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a Azure Active Directory bal oldali navigációs menüjében kattintson a **vállalati alkalmazások** elemre.

5.  kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

    * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6.  Válassza ki azt az alkalmazást, amely számára engedélyezni szeretné az önkiszolgáló hozzáférést a listáról.

7.  Az alkalmazás betöltése után kattintson az **önkiszolgáló** elemre az alkalmazás bal oldali navigációs menüjében.

8.  Az alkalmazás önkiszolgáló alkalmazásokhoz való hozzáférésének engedélyezéséhez kapcsolja be a **felhasználók számára az alkalmazáshoz való hozzáférés kérését?** váltson át az Igen értékre **.**

9.  Ezután válassza ki azt a csoportot, amelyhez hozzá szeretné adni az alkalmazáshoz hozzáférést kérő felhasználókat, kattintson a címke melletti választóra, **amelyhez hozzá** kívánja adni a felhasználókat, és válasszon ki egy csoportot.
  
    > [!NOTE]
    > A helyszínen szinkronizált csoportok nem támogatottak ahhoz a csoporthoz, amelyhez hozzáférést kérő felhasználóknak hozzá kell adni az alkalmazást.
  
10. Nem **kötelező:** Ha szeretné megkövetelni, hogy a felhasználók hozzáférhessenek-e az üzleti jóváhagyáshoz, akkor az **alkalmazáshoz való hozzáférés engedélyezése előtt állítsa be a jóváhagyás megkövetelése** beállítást. az **Igen**értékre vált.

11. Nem **kötelező: csak jelszó egyszeri bejelentkezést használó alkalmazások esetében,** ha engedélyezni szeretné, hogy ezek az üzleti jóváhagyók megadják az alkalmazásnak a jóváhagyott felhasználók számára eljuttatott jelszavakat, állítsa be a **jóváhagyók engedélyezése a felhasználó jelszavának beállításához alkalmazás?** Váltás az **Igen**értékre.

12. Nem **kötelező:** Azon üzleti jóváhagyók megadásához, akik számára engedélyezett az alkalmazáshoz való hozzáférés jóváhagyása, kattintson az **alkalmazáshoz való hozzáférés jóváhagyására jogosult** címke melletti választóra? a legfeljebb 10 egyéni üzleti jóváhagyó kiválasztásához.

     > [!NOTE]
     > A csoportok nem támogatottak.
     >
     >

13. Nem **kötelező:** **a szerepköröket közzétevő alkalmazások esetében**, ha az önkiszolgáló jóváhagyott felhasználókat szeretné hozzárendelni egy szerepkörhöz, kattintson arra a választóra, **amelynek a szerepkörét el szeretné rendelni a felhasználóknak az alkalmazásban?** a szerepkör kiválasztásához, amelyhez a felhasználók hozzá kell rendelni.

14. Kattintson a panel tetején található **Save (Mentés** ) gombra a befejezéshez.

Az önkiszolgáló alkalmazás konfigurálásának befejezése után a felhasználók az [alkalmazás-hozzáférési panelre](https://myapps.microsoft.com/) léphetnek, és a **+ Hozzáadás** gombra kattintva megkereshetik azokat az alkalmazásokat, amelyekhez engedélyezte az önkiszolgáló hozzáférést. Az üzleti jóváhagyók az [alkalmazás hozzáférési paneljén](https://myapps.microsoft.com/)is megtekinthetik az értesítéseket. Engedélyezheti az e-mailek értesítését, ha egy felhasználó hozzáférést kért egy olyan alkalmazáshoz, amelyhez jóváhagyás szükséges. 

Ezek a jóváhagyások csak az egyszeri jóváhagyási munkafolyamatokat támogatják, ami azt jelenti, hogy ha több jóváhagyót ad meg, akkor az egyetlen jóváhagyó jóváhagyhatja az alkalmazáshoz való hozzáférést.

## <a name="next-steps"></a>Következő lépések
[Azure Active Directory beállítása az önkiszolgáló csoportok felügyeletéhez](../users-groups-roles/groups-self-service-management.md)
