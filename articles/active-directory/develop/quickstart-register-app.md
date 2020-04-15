---
title: 'Rövid útmutató: Alkalmazások regisztrálása a Microsoft identity platformmal | Azure'
description: Ebben a rövid útmutatóban megtudhatja, hogyan vehet fel és regisztrálhat egy alkalmazást a Microsoft identity platformmal.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/12/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 79983678d13b810a521a00ba2c1978de92a5029f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309510"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Rövid útmutató: Alkalmazás regisztrálása a Microsoft identitásplatformmal

Ebben a rövid útmutatóban regisztrál egy alkalmazást az **Azure-portálon az alkalmazásregisztrációk** használatával. 

Az alkalmazás integrálva van a Microsoft identitásplatformregisztrálásával egy Azure Active Directory-bérlő. A nagyvállalati fejlesztők és a szoftverszolgáltatás-szolgáltatók kereskedelmi felhőszolgáltatásokat vagy üzletági alkalmazásokat fejleszthetnek, amelyek integrálhatók a Microsoft identitásplatformmal. Az integráció biztonságos bejelentkezést és engedélyezést biztosít az ilyen szolgáltatásokhoz.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Egy [Azure AD-bérlő.](quickstart-create-new-tenant.md)

## <a name="register-a-new-application-using-the-azure-portal"></a>Új alkalmazás regisztrálása az Azure Portal használatával

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiók több bérlőhöz is hozzáférést biztosít, válassza ki a fiókját a jobb felső sarokban. Állítsa be a portálmunkamenetet a kívánt Azure AD-bérlőre.
1. Keresse meg és válassza ki az **Azure Active Directoryt**. A **Kezelés csoportban**válassza **az Alkalmazásregisztrációk lehetőséget.**
1. Válassza **az Új regisztráció lehetőséget.**
1. Az **alkalmazás regisztrálása**mezőbe írja be a felhasználók számára megjelenítendő értelmezési alkalmazásnevet.
1. Adja meg, hogy ki használhatja az alkalmazást az alábbiak szerint:

    | Támogatott fióktípusok | Leírás |
    |-------------------------|-------------|
    | **Csak az ebben a szervezeti címtárban található fiókok** | Akkor válassza ezt a lehetőséget, ha üzletági (LOB) alkalmazást készít. Ez a beállítás nem érhető el, ha nem regisztrálja az alkalmazást egy címtárban.<br><br>E lehetőség választása esetén az alkalmazás csak egy Azure AD-bérlős lesz.<br><br>Ez a beállítás az alapértelmezett beállítás, kivéve, ha az alkalmazást egy könyvtáron kívül regisztrálja. Ha címtáron kívül regisztrálja az alkalmazást, a több Azure AD-bérlős alkalmazás és a személyes Microsoft-fiókok használata lesz az alapértelmezés. |
    | **Tetszőleges szervezeti címtárban található fiókok** | Akkor válassza ezt a lehetőséget, ha szeretne minden üzleti és az oktatási ügyfelet megcélozni.<br><br>E lehetőség választása esetén az alkalmazás csak több Azure AD-bérlős lesz.<br><br>Ha regisztrálta az alkalmazást az Azure AD csak egybérlős, frissítheti azt, hogy az Azure AD több-bérlős, és vissza az egy-bérlős keresztül **a hitelesítés i.** |
    | **Tetszőleges szervezeti címtárban található fiókok és személyes Microsoft-fiókok** | Akkor válassza ezt a lehetőséget, ha a lehető legszélesebb ügyfélkört szeretbé megcélozni.<br><br>E lehetőség választása esetén az alkalmazás több Azure AD-bérlős lesz, és személyes Microsoft-fiókok is használhatók lesznek.<br><br>Ha regisztrálta az alkalmazást Azure AD több-bérlős és személyes Microsoft-fiókként, nem módosíthatja ezt a beállítást a felhasználói felületen. Ehelyett az alkalmazásjegyzék-szerkesztőt kell használnia a támogatott fióktípusok módosításához. |

1. Az **Átirányítás URI (nem kötelező)** csoportban válassza ki az általad épített alkalmazás típusát: **Webes** vagy nyilvános **ügyfél (mobil & asztali)**. Ezután adja meg az alkalmazás átirányítási URI-ját vagy válasz URL-címét.

    * Webalkalmazás esetében adja meg alkalmazás alap URL-címét. A `https://localhost:31544` például a helyi gépen futó webalkalmazás URL-címe lehet. A felhasználók ezzel az URL-címmel jelentkeznek be egy webes ügyfélalkalmazásba.
    * Nyilvános ügyfélalkalmazások esetében adja meg az URI-t, amelyet az Azure AD a jogkivonatválaszok visszaadására használ. Adjon meg az alkalmazáshoz tartozó értéket, például: `myapp://auth`.

    A webalkalmazásokra és a natív alkalmazásokra vonatkozó példákat a [Microsoft identity platform](https://docs.microsoft.com/azure/active-directory/develop)rövid útmutatói ban talál.

1. Miután végzett, válassza a **Regisztrálás** lehetőséget.

    ![Új alkalmazás regisztrálásának képernyője az Azure Portalon](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Az Azure AD egy egyedi alkalmazást vagy ügyfélazonosítót rendel az alkalmazáshoz. A portál megnyitja az alkalmazás **áttekintése** lapot. Az alkalmazáshoz való képességek hozzáadásához más konfigurációs beállításokat is megadhat, például márkajelzést, tanúsítványokat és titkos kulcsokat, API-engedélyeket és egyebeket.

![Példa egy újonnan regisztrált alkalmazás áttekintő oldalára](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>További lépések

* A webes API-k eléréséhez olvassa [el a rövid útmutató: Ügyfélalkalmazás konfigurálása webes API-k eléréséhez](quickstart-configure-app-access-web-apis.md)

* Az engedélyekről az [Engedélyek és a hozzájárulás a Microsoft identity platform végpontjában](v2-permissions-and-consent.md)című témakörben olvashat.

* A webes API-k elérhetővé tévő témaköre A [rövid útmutató: Egy alkalmazás konfigurálása webes API-k felfedéséhez](quickstart-configure-app-expose-web-apis.md)című témakörben található.

* A támogatott fiókok kezeléséről a [Rövid útmutató: Az alkalmazás által támogatott fiókok módosítása](quickstart-modify-supported-accounts.md)című témakörben látható.

* Alkalmazás létrehozásához és funkciók hozzáadásához tekintse meg a Rövid útmutatókat a [Microsoft identity platformon.](https://docs.microsoft.com/azure/active-directory/develop)

* Ha szeretne többet megtudni a regisztrált alkalmazásokat jelölő két Azure AD-objektumról és azok kapcsolatáról, tekintse meg az [alkalmazás- és szolgáltatásnév-objektumokat](app-objects-and-service-principals.md) ismertető szakaszt.

* Ha többet szeretne megtudni az alkalmazások fejlesztése során használandó márkajelzési irányelvekről, olvassa el [az alkalmazások márkajelzési irányelvei című témakört.](howto-add-branding-in-azure-ad-apps.md)
