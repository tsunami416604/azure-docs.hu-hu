---
title: "Önkiszolgáló alkalmazás-hozzáférés és a delegált felügyelet az Azure Active Directoryval |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan önkiszolgáló alkalmazás-hozzáférés és a delegált felügyelet az Azure Active Directoryban."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 448a7fe8-a162-475e-9ba2-2e3ab59302bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: curtand
ms.reviewer: asmalser
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 39c62461c9659b0cb4422de88686283ba462c53b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="self-service-application-access-and-delegated-management-with-azure-active-directory"></a>Önkiszolgáló alkalmazás-hozzáférés és a delegált felügyelet az Azure Active Directoryval
Egy általános forgatókönyv a vállalati informatikai önkiszolgálói képességeit engedélyezése a végfelhasználók számára. A felhasználók, a nagy mennyiségű alkalmazások és a hozzáférés biztosítása döntések best-informed személy sok nem lehet a directory-rendszergazda. Gyakran a legjobb személy döntse el, hogy ki férhet hozzá a kérelmet, de egy más meghatalmazott rendszergazda. Azonban a felhasználó, aki használja az alkalmazást, és a felhasználó tudja, mit kell végezhető el a feladatot.

> [!IMPORTANT]
> A Microsoft javasolja, hogy az Azure Portalon található [Azure AD felügyeleti központból](https://aad.portal.azure.com) kezelje az Azure AD-t az ebben a cikkben javasolt klasszikus Azure portál helyett. 

Önkiszolgáló alkalmazás-hozzáférés csak a [Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/) P1 és P2 licencelési, amelyek lehetővé teszik a címtárban:

* Lehetővé teszi a felhasználók kattintva kérhet hozzáférést az "Beolvasása további alkalmazások" mozaikra használó alkalmazások a [Azure AD hozzáférési Panel](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)
* Mely alkalmazások használói kérhetnek való hozzáférés beállítása
* Függetlenül attól, egy jóváhagyásra szükség a felhasználók saját maguk rendelhető az alkalmazáshoz való hozzáférés beállítása
* Állítsa ki kell a kérelem jóváhagyása és minden egyes alkalmazás-hozzáférés kezelése

Ezt a képességet az összes előre integrált támogatott ma és összevont, jelszóalapú vagy egyetlen támogató egyéni alkalmazások bejelentkezés a a [Azure Active Directory alkalmazáskatalógusában](https://azure.microsoft.com/marketplace/active-directory/all/), beleértve az alkalmazások, például a Salesforce, Dropbox, Google Apps, és több.
Ez a cikk ismerteti, hogyan:

* A végfelhasználók számára, beleértve a nem kötelező jóváhagyási munkafolyamat konfigurálása az önkiszolgáló alkalmazás-hozzáférés konfigurálása 
* A legmegfelelőbb a szervezet dolgozói számára adott alkalmazásokra vonatkozó hozzáférés-kezelés delegálása, és lehetővé teszi az Azure AD hozzáférési panel hozzáférési kérelmek jóváhagyása, közvetlenül a kijelölt felhasználók hozzáférés hozzárendelése és (opcionálisan) a hitelesítő adatok beállítására használható Ha jelszóalapú egyszeri bejelentkezésre van beállítva alkalmazás-hozzáférés

## <a name="configuring-self-service-application-access"></a>Önkiszolgáló alkalmazás-hozzáférés konfigurálása
Önkiszolgáló alkalmazás-hozzáférés engedélyezése és konfigurálása, melyik alkalmazások is hozzáadhatók, illetve a végfelhasználók által kért kövesse ezeket az utasításokat.

1. Jelentkezzen be a [a klasszikus Azure portálon](https://manage.windowsazure.com/).

2.   Az a **Active Directory** területen válassza ki azt a címtárat, majd válassza ki a **alkalmazások** fülre. 

3. Válassza ki a **Hozzáadás** gombra, és a gyűjtemény kapcsoló használatával válassza ki, és hozzáadhat egy alkalmazást.

4. Az alkalmazás hozzáadása után fog kapni olyan alkalmazás első lépéseinek oldala. Kattintson a **konfigurálása egyszeri bejelentkezéshez**, válassza ki a kívánt egyszeri bejelentkezés módot, és a konfiguráció mentéséhez. 

5. Ezután válassza ki a **konfigurálása** fülre. Ahhoz, hogy a felhasználók számára hozzáférést kér az Azure AD hozzáférési panel ehhez az alkalmazáshoz, állítsa be **önkiszolgáló alkalmazás-hozzáférés engedélyezése** való **Igen**.
  
  ![][1]

6. Igény szerint állítsa be a hozzáférési kérelem jóváhagyási munkafolyamat, állítsa **hozzáférés biztosítása előtt jóvá kell hagyni** való **Igen**. Egy vagy több jóváhagyó választható használatával, majd a **jóváhagyóknak** gombra.

  Jóváhagyó lehet minden olyan felhasználó, a szervezet az Azure AD-fiókot, és lehet, a licencelési fiók felelős, vagy bármely más üzleti folyamat a szervezet megköveteli az alkalmazások való hozzáférés biztosítása előtt. A jóváhagyó is lehet a csoport tulajdonosa egy vagy több megosztott csoportok, és rendelhet hozzá a felhasználók ezeket a csoportokat számukra egy megosztott fiókkal való hozzáférésének egyik. 

  Ha nincsenek jóváhagyásra szükség, majd azonnal a felhasználónál az alkalmazás az Azure AD hozzáférési panel hozzá. Ha az alkalmazás beállítva fel az [automatikus felhasználólétesítés](active-directory-saas-app-provisioning.md), vagy be van állítva ["felhasználó által kezelt" jelszó SSO mód](active-directory-appssoaccess-whatis.md#password-based-single-sign-on), a felhasználó már rendelkezik egy felhasználói fiókot, és a jelszó ismerete.

7. Ha az alkalmazás konfigurációja használja a jelszó-alapú egyszeri bejelentkezést, akkor egy beállítást a használatának engedélyezése minden felhasználó nevében egyszeri bejelentkezési hitelesítő adatok beállítása a jóváhagyó érhető el. További információkért lásd [kezelési meghatalmazott](#delegated-application-access-management).

8. Végezetül a **a Self-Assigned felhasználók** tárolja az adott vagy az alkalmazáshoz hozzáféréssel rendelkező felhasználók csoport nevét jeleníti meg. A hozzáférés jóváhagyó a csoport tulajdonosa lesz. Ha látható a csoportnév nem létezik, akkor automatikusan létrejön. Opcionálisan a csoport nevét állítható be egy meglévő csoport nevét.

9. A konfiguráció mentéséhez kattintson **mentése** a képernyő alján. Most már a felhasználók a hozzáférés kéréséhez ennek az alkalmazásnak a hozzáférési panelen igényelheti is.

10. Próbálja meg a végfelhasználói élményt, jelentkezzen be a szervezet Azure AD hozzáférési panel https://myapps.microsoft.com, lehetőség szerint másik fiókkal, amely nem az alkalmazás jóváhagyó. 

11. Az a **alkalmazások** lapra, majd a **további alkalmazások beszerzése** csempére. Ez a csempe sablongalériából összes, amelyeken engedélyezve van az önkiszolgáló alkalmazás hozzáférése a könyvtárban, a Keresés és szűrés a bal oldali app kategória szerint képes alkalmazást jeleníti meg. 

12. A folyamat elindít egy kattint. Ha nincs jóváhagyási folyamat szükség, akkor az alkalmazás azonnal megjelenik a a **alkalmazások** után egy rövid megerősítő lapon. Ha jóváhagyásra szükség, majd megjelenik egy párbeszédpanel, ezzel jelezve, és a jóváhagyó egy e-mailt küld. Be kell jelentkeznie a hozzáférési panelre nem jóváhagyó, hogy a folyamat.

13. Az e-mailt irányítja a jóváhagyó jelentkezzen be az Azure AD hozzáférési panel, és hagyja jóvá a kérést. A felhasználónál, amennyiben elfogadja a kérelmet (és a jóváhagyó által végrehajtott semmilyen különleges folyamatot adhat meg), az alkalmazás csoportban jelennek meg azok **alkalmazások** lap, ahol bejelentkeznének bele.

## <a name="delegated-application-access-management"></a>Delegált alkalmazáshozzáférés-kezeléshez
Az alkalmazás hozzáférési jóváhagyó lehet jóváhagyása vagy megtagadja a hozzáférést a szóban forgó alkalmazás leginkább megfelelő személy, aki a szervezet bármely felhasználója. A felhasználói fiók kiépítése, felelős licencelési, vagy más üzleti folyamat a szervezet megköveteli az alkalmazásához való hozzáférést megelőzően.

A fent leírt önkiszolgáló alkalmazás-hozzáférés konfigurálásakor bármely alkalmazás hozzárendelése jóváhagyóknak lásd további **alkalmazások kezelése** csempe az Azure AD hozzáférési panel, amely azt is, hogy mely alkalmazások a a rendszergazda hozzáférést. Az alkalmazásra kattintva megnyílik számos lehetőség közül választhat a képernyőn látható.

![][2]

### <a name="approve-requests"></a>Kérelmek jóváhagyása
A **kérelmek jóváhagyása** csempe lehetővé teszi, hogy a jóváhagyóknak minden függőben lévő jóváhagyások alkalmazást jellemző megjelenítéséhez, és átirányítja a felhasználókat a jóváhagyások lapra, ahol a kérelmek is erősíthető vagy tagadható meg. A jóváhagyó automatikus e-mailek is kap, amikor kérést hoz létre, amely arra utasítja őket Mi a teendő.

### <a name="add-users"></a>Felhasználók hozzáadása
A **felhasználó hozzáadása** csempe lehetővé teszi, hogy a közvetlenül kijelölt felhasználónak hozzáférést biztosít az alkalmazás a jóváhagyóknak. Ez a csempe kattint, a jóváhagyó látja, egy párbeszédpanelen megtekintéséhez és kereséséhez a könyvtárban lévő felhasználók számára lehetővé teszi. Egy felhasználó eredmények hozzáadása az alkalmazásban, az adott felhasználó az Azure AD hozzáférési panelek vagy az Office 365 látható. Ha bármely manuális felhasználói létesítésének folyamatát kell használnia szükség, az alkalmazást, mielőtt a felhasználó nem jelentkezhet be, majd a jóváhagyó végre kell hajtania ezt a folyamatot hozzáférés engedélyezése előtt.  

### <a name="manage-users"></a>Felhasználók kezelése
A **felhasználók kezelése** csempe lehetővé teszi, hogy a jóváhagyóknak közvetlenül frissítéséhez, vagy távolítsa el, mely felhasználók férhetnek hozzá az alkalmazást. 

### <a name="configure-password-sso-credentials-if-applicable"></a>Jelszó egyszeri bejelentkezési hitelesítő adatok beállítása (ha van ilyen)
A **konfigurálása** csempe csak akkor is látható, ha az alkalmazás konfigurálása által a rendszergazdát, hogy a jelszó-alapú egyszeri bejelentkezéshez használja, és a rendszergazda adott a jóváhagyó meg jelszó egyszeri bejelentkezési hitelesítő adatokat, lásd korábban. Kiválasztásakor, a jóváhagyó találkozik hogyan továbbítja a hitelesítő adatok hozzárendeli a felhasználók számos lehetőség közül választhat:

![][3]

* **Felhasználók jelentkezzen be a saját jelszavukat** – ebben a módban a hozzárendelt felhasználók tudják, mit a felhasználónevek és jelszavak az alkalmazáshoz, és meg kell őket az első bejelentkezés során az alkalmazáshoz. A forgatókönyv megfelel-e a jelszó SSO esetben ha a [felhasználók kezelhetik a hitelesítő adatok](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).
* **Felhasználók automatikusan bejelentkeztetjük külön fiókokkal, amelyek kezelhető** – ebben a módban a hozzárendelt a felhasználóknak nem kell adja meg, vagy tudni alkalmazásspecifikus hitelesítő adataikat, ha az alkalmazás aláírása. Ehelyett a jóváhagyó hitelesítő adatok beállítása az egyes felhasználók hozzáférés hozzárendelése után a **felhasználó hozzáadása** csempére. A felhasználó a hozzáférési panel vagy Office 365 alkalmazásra kattint, automatikusan megtörténik a jóváhagyó által beállított hitelesítő adatok segítségével. A forgatókönyv megfelel-e a jelszó SSO esetben ha a [rendszergazdák hitelesítő adatok kezelése](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).
* **Felhasználók egyetlen fiókkal, amely a kezelhető automatikusan bejelentkeztetjük** -egy különleges esetben, ebben az esetben célszerű használni, ha az összes hozzárendelt felhasználó hozzáférést egy megosztott fiókkal. A funkció a leggyakrabban használt használati eset közösségi alkalmazásokkal, amikor egy szervezet egyetlen "Vállalati" fiókkal rendelkezik, és a frissítések ezekhez a fiókokhoz kell több felhasználó van. A forgatókönyv is megfelel a jelszó SSO esetben ha a [rendszergazdák hitelesítő adatok kezelése](active-directory-appssoaccess-whatis.md#password-based-single-sign-on). Ez a beállítás kiválasztása után azonban a jóváhagyó egyetlen közös fiók megadhatja a felhasználónevet és jelszót kéri. Ezt követően az összes hozzárendelt felhasználó jelentkezik be ezt a fiókot használja, ha az Azure AD hozzáférési panel vagy Office 365 alkalmazásra kattint.

## <a name="additional-resources"></a>További források
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)

<!--Image references-->
[1]: ./media/active-directory-self-service-application-access/ssaa_admin.PNG
[2]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app.PNG
[3]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app_config.PNG
