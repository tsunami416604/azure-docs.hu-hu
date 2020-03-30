---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 41f2d4540f665137d34d262546cdc1a2edfbae3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77608735"
---
## <a name="prerequisites"></a>Előfeltételek
A CDN-felügyeleti kód írása előtt el kell végeznie néhány előkészítést, hogy a kód kommunikáljon az Azure Resource Manager használatával. Ehhez a készítményhez:

* Erőforráscsoport létrehozása az oktatóanyagban létrehozott CDN-profil tárolására
* Az Azure Active Directory konfigurálása az alkalmazás hitelesítésének biztosításához
* Engedélyek alkalmazása az erőforráscsoportra, hogy csak az Azure AD-bérlő jogosult felhasználói léphessenek kapcsolatba a CDN-profillal

### <a name="creating-the-resource-group"></a>Az erőforráscsoport létrehozása
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Kattintson az **Erőforrás létrehozása** gombra.
3. Keressen **az Erőforrás csoport ban,** és az Erőforráscsoport ablaktáblán kattintson a **Létrehozás gombra.**

    ![Új erőforráscsoport létrehozása](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Nevezze el az erőforráscsoportot *CdnConsoleTutorial*.  Válassza ki az előfizetést, és válasszon egy közeli helyet.  Ha szeretné, a **Pin az irányítópultra** jelölőnégyzetre kattintva rögzítheti az erőforráscsoportot a portál irányítópultján.  A rögzítés megkönnyíti a későbbi kereséset.  A beállítások elvégzése után kattintson a **Létrehozás gombra.**

    ![Az erőforráscsoport elnevezése](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Az erőforráscsoport létrehozása után, ha nem rögzítette az irányítópulton, a **Tallózás**gombra kattintva, majd **az Erőforráscsoportok**parancsra kattintva találhatja meg.  A megnyitásához kattintson az erőforráscsoportra.  Jegyezze fel **az előfizetésazonosítóját.** Később kell.

    ![Az erőforráscsoport elnevezése](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Az Azure AD-alkalmazás létrehozása és engedélyek alkalmazása
Az Azure Active Directoryval való alkalmazáshitelesítés két megközelítése van: egyéni felhasználók vagy egyszerű szolgáltatás. Az egyszerű szolgáltatás hasonló a Windows szolgáltatásfiókjához.  Ahelyett, hogy egy adott felhasználói engedélyeket a CDN-profilok, engedélyek helyett a szolgáltatás egyszerű.  A szolgáltatásnévi tagok általában automatizált, nem interaktív folyamatokhoz használatosak.  Annak ellenére, hogy ez az oktatóanyag egy interaktív konzolalkalmazást ír, a szolgáltatás egyszerű megközelítésére összpontosítunk.

Egyszerű szolgáltatás létrehozása több lépésből áll, beleértve egy Azure Active Directory-alkalmazás létrehozása.  Létrehozásához, fogunk követni [ezt a bemutató](../articles/active-directory/develop/howto-create-service-principal-portal.md).

> [!IMPORTANT]
> Ügyeljen arra, hogy kövesse az összes lépést a [kapcsolódó bemutató](../articles/active-directory/develop/howto-create-service-principal-portal.md).  *Fontos,* hogy pontosan a leírtak nak leírtak szerint töltse ki.  Győződjön meg arról, hogy vegye figyelembe a **bérlői azonosítót**, **a bérlői tartománynevet** (általában *.onmicrosoft.com* tartományt, kivéve, ha egyéni tartományt adott meg), **ügyfélazonosítót**és **ügyfélhitelesítési kulcsot,** mivel később szükségünk van ezekre az adatokra.  Ügyeljen arra, hogy őrizze az **ügyfélazonosítót** és **az ügyfélhitelesítési kulcsot,** mivel ezeket a hitelesítő adatokat bárki használhatja a műveletek egyszerű szolgáltatásként történő végrehajtásához.
>
> Amikor a Több-bérlős alkalmazás konfigurálása című lépéshez lép, válassza a **Nem**lehetőséget.
>
> Amikor eljut a [lépés: Rendelje hozzá az alkalmazást egy szerepkörhöz,](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)használja a korábban létrehozott erőforráscsoportot, *a CdnConsoleTutorial*, de az **Olvasó** szerepkör helyett rendelje hozzá a **CDN-profilközreműködői** szerepkört.  Miután hozzárendelte az alkalmazást a **CDN-profilközreműködői** szerepkört az erőforráscsoporthoz, térjen vissza ehhez az oktatóanyaghoz. 
>
>

Miután létrehozta az egyszerű szolgáltatást, és hozzárendelte a **CDN-profil közreműködői** szerepkört, az erőforráscsoport **Felhasználók** paneljének az alábbi hoz hasonlónak kell lennie.

![Felhasználók panel](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interaktív felhasználói hitelesítés
Ha egy egyszerű szolgáltatás helyett inkább interaktív egyéni felhasználói hitelesítést szeretne, a folyamat hasonló az egyszerű szolgáltatáshoz.  Valójában ugyanazt az eljárást kell követnie, de néhány kisebb változtatást kell végrehajtania.

> [!IMPORTANT]
> Csak akkor hajtsa végre a következő lépéseket, ha az egyszerű szolgáltatás helyett egyéni felhasználói hitelesítést használ.
>
>

1. Az alkalmazás létrehozásakor a **WebApplication**helyett válassza a **Natív alkalmazás**lehetőséget.

    ![Natív alkalmazás](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. A következő lapon a rendszer **átirányítási URI-t**kér.  Az URI nem lesz érvényesítve, de ne feledje, hogy mit írt be. Majd később szükséged lesz rá.
3. Nincs szükség **ügyfélhitelesítési kulcs létrehozására.**
4. Ahelyett, hogy egy egyszerű szolgáltatás hozzárendelése a **CDN-profil közreműködői** szerepkör, fogunk rendelni az egyes felhasználók vagy csoportok.  Ebben a példában láthatja, hogy *cdn demo felhasználót* rendeltem hozzá a **CDN-profil közreműködői** szerepkörhöz.  

    ![Egyéni felhasználói hozzáférés](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
