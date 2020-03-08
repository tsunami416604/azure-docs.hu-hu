---
title: 'Gyors útmutató: alkalmazások regisztrálása a Microsoft Identity platformmal | Azure'
description: Ebből a rövid útmutatóból megtudhatja, hogyan adhat hozzá és regisztrálhat egy alkalmazást a Microsoft Identity platformmal.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/09/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: dc719064166be917d868c7b7fee6b126b4099840
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78893016"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Gyors útmutató: alkalmazás regisztrálása a Microsoft Identity platformmal

Ebben a rövid útmutatóban egy alkalmazást regisztrál a Azure Portal **Alkalmazásregisztrációk** felületén. Az alkalmazás integrálva van a Microsoft Identity platformmal. A nagyvállalati fejlesztők és a szolgáltatott szoftveres (SaaS) szolgáltatók olyan kereskedelmi felhőalapú szolgáltatásokat vagy üzletági alkalmazásokat hozhatnak létre, amelyek integrálhatók a Microsoft Identity platformmal. Az integráció biztonságos bejelentkezést és engedélyezést biztosít az ilyen szolgáltatásokhoz.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="register-a-new-application-using-the-azure-portal"></a>Új alkalmazás regisztrálása az Azure Portal használatával

1. Jelentkezzen be [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, illetve személyes Microsoft-fiókjával.
1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókját a jobb felső sarokban. Állítsa be a portál munkamenetét a kívánt Azure AD-bérlőre.
1. Keresse meg és válassza ki a **Azure Active Directory**. A **kezelés**területen válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Válassza az **új regisztráció**lehetőséget.
1. Az **alkalmazás regisztrálása**területen adja meg a felhasználók számára megjelenítendő, értelmezhető alkalmazás nevét.
1. A következőképpen adhatja meg, hogy ki használhatja az alkalmazást:

    | Támogatott fióktípusok | Leírás |
    |-------------------------|-------------|
    | **Csak az ebben a szervezeti címtárban található fiókok** | Akkor válassza ezt a lehetőséget, ha üzletági (LOB) alkalmazást készít. Ez a lehetőség nem érhető el, ha nem regisztrálja az alkalmazást egy címtárban.<br><br>E lehetőség választása esetén az alkalmazás csak egy Azure AD-bérlős lesz.<br><br>Ez az alapértelmezett beállítás, ha az alkalmazást egy címtáron kívül regisztrálja. Ha címtáron kívül regisztrálja az alkalmazást, a több Azure AD-bérlős alkalmazás és a személyes Microsoft-fiókok használata lesz az alapértelmezés. |
    | **Tetszőleges szervezeti címtárban található fiókok** | Akkor válassza ezt a lehetőséget, ha szeretne minden üzleti és az oktatási ügyfelet megcélozni.<br><br>E lehetőség választása esetén az alkalmazás csak több Azure AD-bérlős lesz.<br><br>Ha az alkalmazást csak az Azure AD-ben regisztrálta, akkor a **hitelesítés** lapon frissítheti azt, hogy az Azure ad több-bérlős legyen, és vissza az egybérlős példányra. |
    | **Tetszőleges szervezeti címtárban található fiókok és személyes Microsoft-fiókok** | Akkor válassza ezt a lehetőséget, ha a lehető legszélesebb ügyfélkört szeretbé megcélozni.<br><br>E lehetőség választása esetén az alkalmazás több Azure AD-bérlős lesz, és személyes Microsoft-fiókok is használhatók lesznek.<br><br>Ha az alkalmazást Azure AD több-bérlős és személyes Microsoft-fiókkal regisztrálta, akkor ez a beállítás nem módosítható a felhasználói felületen. Ehelyett az alkalmazásjegyzék-szerkesztőt kell használnia a támogatott fióktípusok módosításához. |

1. Az **átirányítási URI (nem kötelező)** területen válassza ki az Ön által felépített alkalmazás típusát: **webes** vagy **nyilvános ügyfél (mobil & asztali)** . Ezután adja meg az alkalmazáshoz tartozó átirányítási URI-t vagy válasz URL-címét.

    * Webalkalmazás esetében adja meg alkalmazás alap URL-címét. A `https://localhost:31544` például a helyi gépen futó webalkalmazás URL-címe lehet. A felhasználók ezzel az URL-címmel jelentkeznek be egy webes ügyfélalkalmazásba.
    * Nyilvános ügyfélalkalmazások esetében adja meg az URI-t, amelyet az Azure AD a jogkivonatválaszok visszaadására használ. Adjon meg az alkalmazáshoz tartozó értéket, például: `myapp://auth`.

    Webalkalmazásokra vagy natív alkalmazásokra vonatkozó példákért tekintse meg a gyors útmutatókat a [Microsoft Identity platformon](https://docs.microsoft.com/azure/active-directory/develop).

1. Miután végzett, válassza a **Regisztrálás** lehetőséget.

    ![Megjeleníti a képernyőt, amely új alkalmazást regisztrál a Azure Portal](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Az Azure AD egy egyedi alkalmazást vagy ügyfelet rendel hozzá az alkalmazáshoz. A portál megnyitja az alkalmazás **Áttekintés** lapját. Ha képességeket szeretne hozzáadni az alkalmazáshoz, kiválaszthat más konfigurációs beállításokat, például a védjegyezést, a tanúsítványokat és a titkos kulcsokat, az API-engedélyeket és egyebeket.

![Példa az újonnan regisztrált alkalmazások áttekintő oldalára](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan érheti el a webes API-kat.
> [!div class="nextstepaction"]
> [Gyors útmutató: ügyfélalkalmazás konfigurálása a webes API-k eléréséhez](quickstart-configure-app-access-web-apis.md)

* Az engedélyekkel kapcsolatos további tudnivalókért tekintse meg [a Microsoft Identity platform végpontjának engedélyeit és](v2-permissions-and-consent.md)a hozzájuk tartozó hozzájárulásukat.

* A webes API-k elérhetővé tétele: gyors útmutató [: alkalmazás konfigurálása webes API](quickstart-configure-app-expose-web-apis.md)-k megjelenítéséhez.

* A támogatott fiókok kezeléséhez tekintse meg a rövid útmutató [: alkalmazás által támogatott fiókok módosítása](quickstart-modify-supported-accounts.md)című témakört.

* Az alkalmazások létrehozásához és a funkciók hozzáadásához tekintse meg a gyors útmutatókat a [Microsoft Identity platformon](https://docs.microsoft.com/azure/active-directory/develop).

* Ha szeretne többet megtudni a regisztrált alkalmazásokat jelölő két Azure AD-objektumról és azok kapcsolatáról, tekintse meg az [alkalmazás- és szolgáltatásnév-objektumokat](app-objects-and-service-principals.md) ismertető szakaszt.

* Ha többet szeretne megtudni az alkalmazások fejlesztéséhez használni kívánt branding-irányelvekről, tekintse meg az [alkalmazások védjegyezési irányelveit](howto-add-branding-in-azure-ad-apps.md).
