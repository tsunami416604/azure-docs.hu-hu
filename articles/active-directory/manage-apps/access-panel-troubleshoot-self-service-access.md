---
title: Probléma az önkiszolgáló alkalmazás-hozzáférés használatával | Microsoft Docs
description: Az önkiszolgáló alkalmazás-hozzáféréssel kapcsolatos problémák elhárítása
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a8a748b11e695041aa6baece381bc8d7d068992
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760915"
---
# <a name="problem-using-self-service-application-access"></a>Probléma az önkiszolgáló alkalmazás-hozzáférés használatával

Az önkiszolgáló alkalmazás-hozzáférés lehetővé teszi a felhasználók számára az alkalmazások önfelderítését, opcionálisan lehetővé teszi, hogy az üzleti csoport jóváhagyja az alkalmazásokhoz való hozzáférést. Lehetővé teheti az üzleti csoport számára, hogy az adott felhasználókhoz rendelt hitelesítő adatokat a hozzáférési panelektől közvetlenül a jelszó egyszeri bejelentkezésre szolgáló alkalmazásokban kezelhesse.

Ahhoz, hogy a felhasználók saját maguk is felfedezzék az alkalmazásokat a hozzáférési paneljéről, engedélyeznie kell az **önkiszolgáló alkalmazásokhoz való hozzáférést** minden olyan alkalmazáshoz, amelyet engedélyezni kíván a felhasználók számára az önfelderítéshez és a hozzáférés kéréséhez.

## <a name="general-issues-to-check-first"></a>Általános problémák az első kereséshez

-   Győződjön meg arról, hogy az önkiszolgáló alkalmazás-hozzáférés helyesen van konfigurálva. Lásd "az önkiszolgáló alkalmazás-hozzáférés konfigurálása" című témakört.

-   Győződjön meg arról, hogy a felhasználó vagy csoport engedélyezte az önkiszolgáló alkalmazás-hozzáférés kérését.

-   Győződjön meg arról, hogy a felhasználó a megfelelő helyre látogat az önkiszolgáló alkalmazás-hozzáféréshez. a felhasználók megkereshetik az [alkalmazás-hozzáférési panelt](https://myapps.microsoft.com/) , és a **+ Hozzáadás** gombra kattintva megtalálhatják azokat az alkalmazásokat, amelyekhez engedélyezte az önkiszolgáló hozzáférést.

-   Ha az önkiszolgáló alkalmazás-hozzáférés nemrég lett konfigurálva, próbáljon meg újra bejelentkezni a felhasználó hozzáférési paneljére néhány perc múlva, hogy megtörtént-e az önkiszolgáló hozzáférésének módosítása.

## <a name="how-to-configure-self-service-application-access"></a>Az önkiszolgáló alkalmazás-hozzáférés konfigurálása

Az önkiszolgáló alkalmazások alkalmazáshoz való hozzáférésének engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. a Azure Active Directory bal oldali navigációs menüjében kattintson a **vállalati alkalmazások** elemre.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki azt az alkalmazást, amely számára engedélyezni szeretné az önkiszolgáló hozzáférést a listáról.

7. Az alkalmazás betöltése után kattintson az **önkiszolgáló** elemre az alkalmazás bal oldali navigációs menüjében.

8. Az alkalmazás önkiszolgáló alkalmazásokhoz való hozzáférésének engedélyezéséhez kapcsolja be a **felhasználók számára az alkalmazáshoz való hozzáférés kérését?** váltson át az Igen értékre **.**

9. Ezután válassza ki azt a csoportot, amelyhez hozzá szeretné adni az alkalmazáshoz hozzáférést kérő felhasználókat, kattintson a címke melletti választóra, **amelyhez hozzá** kívánja adni a felhasználókat, és válasszon ki egy csoportot.

10. Nem **kötelező:** Ha szeretné megkövetelni, hogy a felhasználók hozzáférhessenek-e az üzleti jóváhagyáshoz, akkor az **alkalmazáshoz való hozzáférés engedélyezése előtt állítsa be a jóváhagyás megkövetelése** beállítást. az **Igen**értékre vált.

11. **Opcionális: csak jelszó egyszeri bejelentkezést használó alkalmazásokhoz,** ha engedélyezni szeretné, hogy ezek az üzleti jóváhagyók megadják az alkalmazásnak a jóváhagyott felhasználók számára eljuttatott jelszavakat, állítsa be az **alkalmazáshoz tartozó felhasználók jelszavának engedélyezése a jóváhagyóknak beállítást?** váltás az **Igen**értékre.

12. Nem **kötelező:** Azon üzleti jóváhagyók megadásához, akik számára engedélyezett az alkalmazáshoz való hozzáférés jóváhagyása, kattintson az **alkalmazáshoz való hozzáférés jóváhagyására jogosult** címke melletti választóra? a legfeljebb 10 egyéni üzleti jóváhagyó kiválasztásához.

    >[!NOTE]
    > A csoportok nem támogatottak.
    >
    >

13. Nem **kötelező:** **a szerepköröket közzétevő alkalmazások esetében**, ha az önkiszolgáló jóváhagyott felhasználókat szeretné hozzárendelni egy szerepkörhöz, kattintson arra a választóra, amelynek a szerepkörét hozzá szeretné rendelni az **alkalmazáshoz?** lehetőségre kattintva válassza ki azt a szerepkört, amelyhez ezeket a felhasználókat hozzá kell rendelni.

14. Kattintson a panel tetején található **Save (Mentés** ) gombra a befejezéshez.

Az önkiszolgáló alkalmazás konfigurálásának befejezése után a felhasználók az [alkalmazás-hozzáférési panelre](https://myapps.microsoft.com/) léphetnek, és a **+ Hozzáadás** gombra kattintva megkereshetik azokat az alkalmazásokat, amelyekhez engedélyezte az önkiszolgáló hozzáférést. Az üzleti jóváhagyók az [alkalmazás hozzáférési paneljén](https://myapps.microsoft.com/)is megtekinthetik az értesítéseket. Engedélyezheti az e-mailek értesítését, ha egy felhasználó hozzáférést kért egy olyan alkalmazáshoz, amelyhez jóváhagyás szükséges. 

Ezek a jóváhagyások csak az egyszeri jóváhagyási munkafolyamatokat támogatják, ami azt jelenti, hogy ha több jóváhagyót ad meg, akkor egyetlen jóváhagyó jóváhagyhatja az alkalmazáshoz való hozzáférést.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépések nem oldják meg a problémát 

Ha elérhető, nyisson meg egy támogatási jegyet a következő információkkal:

-   Korrelációs hiba azonosítója

-   UPN (felhasználói e-mail-cím)

-   TenantID

-   Böngésző típusa

-   Az időzóna és az idő/időkeret a hiba bekövetkezésekor

-   Hegedűs nyomkövetései

## <a name="next-steps"></a>További lépések
[Az Azure Active Directory beállítása önkiszolgáló csoportkezelésre](../users-groups-roles/groups-self-service-management.md)
