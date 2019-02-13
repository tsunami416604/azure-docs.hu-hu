---
title: Alkalmazás regisztrálása a Microsoft Identity Platformon (előzetes verzió) | Azure
description: Ismerje meg, hogyan adhat hozzá és regisztrálhat alkalmazást a Microsoft Identity Platformon.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/02/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: d99c25d6203bd86987430bdccd08f770022dc28c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165213"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform-preview"></a>Gyors útmutató: Alkalmazás regisztrálása a Microsoft identity platform (előzetes verzió)

A nagyvállalati fejlesztők és SaaS-szolgáltatók (szoftverszolgáltatók) a Microsoft Identity Platformmal integrálható kereskedelmi felhőszolgáltatásokat és üzletági alkalmazásokat fejleszthetnek, hogy a szolgáltatásaikhoz biztonságos bejelentkezést és engedélyezést biztosítsanak.

Ez a rövid útmutató bemutatja, hogyan adhat hozzá és regisztrálhat egy alkalmazást az Azure Portalon az **Alkalmazásregisztrációk (előzetes verzió)** használatával, hogy az alkalmazás integrálható legyen a Microsoft identitásplatformjával. Az új funkciók és fejlesztések azzal kapcsolatban, az új regisztrációk alkalmazásélményről kapcsolatos további információkért lásd: [ebben a blogbejegyzésben](https://developer.microsoft.com/graph/blogs/new-app-registration/). 

## <a name="prerequisite"></a>Előfeltétel

Első lépésként jóvá kell hagynia az előzetes verzió használatát az alkalmazások Azure Portalon való regisztrálására. E rövid útmutató lépései az új felhasználói felületen alapulnak, és csak akkor működnek, ha Ön jóváhagyta az előzetes verzió használatát.

## <a name="register-a-new-application-using-the-azure-portal"></a>Új alkalmazás regisztrálása az Azure Portal használatával

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. A bal oldali navigációs panelen válassza az **Azure Active Directory** szolgáltatást, majd az **Alkalmazásregisztrációk (előzetes verzió) > Új alkalmazás regisztrálása** lehetőséget.
1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:

    - **Név** – az alkalmazás felhasználói számára megjelenített, kifejező alkalmazásnevet adjon meg.
    - **Támogatott fióktípusok** – válassza ki, hogy mely fiókokat szeretné támogatni az alkalmazásában.

        | Támogatott fióktípusok | Leírás |
        |-------------------------|-------------|
        | **Csak az ebben a szervezeti címtárban található fiókok** | Akkor válassza ezt a lehetőséget, ha üzletági (LOB) alkalmazást készít. Ez a lehetőség nem érhető el, ha nem regisztrálja az alkalmazást egy címtárban.<br><br>E lehetőség választása esetén az alkalmazás csak egy Azure AD-bérlős lesz.<br><br>Ez az alapértelmezett lehetőség, kivéve, ha címtáron kívül regisztrálja az alkalmazást. Ha címtáron kívül regisztrálja az alkalmazást, a több Azure AD-bérlős alkalmazás és a személyes Microsoft-fiókok használata lesz az alapértelmezés. |
        | **Tetszőleges szervezeti címtárban található fiókok** | Akkor válassza ezt a lehetőséget, ha szeretne minden üzleti és az oktatási ügyfelet megcélozni.<br><br>E lehetőség választása esetén az alkalmazás csak több Azure AD-bérlős lesz.<br><br>Ha csak egy Azure AD-bérlősként regisztrálta az alkalmazást, a **Hitelesítés** panelen átválthatja több Azure AD-bérlősre, illetve visszaállíthatja egy Azure AD-bérlősre. |
        | **Tetszőleges szervezeti címtárban található fiókok és személyes Microsoft-fiókok** | Akkor válassza ezt a lehetőséget, ha a lehető legszélesebb ügyfélkört szeretbé megcélozni.<br><br>E lehetőség választása esetén az alkalmazás több Azure AD-bérlős lesz, és személyes Microsoft-fiókok is használhatók lesznek.<br><br>Ha több Azure AD-bérlősként regisztrálta az alkalmazást, és engedélyezte a személyes Microsoft-fiókok használatát, ezen nem változtathat a felhasználói felületen. Ehelyett az alkalmazásjegyzék-szerkesztőt kell használnia a támogatott fióktípusok módosításához. |

    - **Átirányítási URI (nem kötelező)** – válassza ki a készített alkalmazás típusát – **webes** vagy **nyilvános ügyfél (mobil és asztali)**, majd adja meg az alkalmazása átirányítási URI-ját (vagy válasz URL-címét).
        - Webalkalmazás esetében adja meg alkalmazás alap URL-címét. A `http://localhost:31544` például a helyi gépen futó webalkalmazás URL-címe lehet. A felhasználók ezzel az URL-címmel jelentkeznek be egy webes ügyfélalkalmazásba.
        - Nyilvános ügyfélalkalmazások esetében adja meg az URI-t, amelyet az Azure AD a jogkivonatválaszok visszaadására használ. Adjon meg az alkalmazáshoz tartozó értéket, például: `myapp://auth`.

    Webalkalmazásokra és natív alkalmazásokra konkrét példákat [rövid útmutatóinkban](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts) talál.

1. Miután végzett, válassza a **Regisztrálás** lehetőséget.

    [![Új alkalmazás regisztrálása az Azure Portalon](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

Az Azure AD egyedi alkalmazásazonosítót (ügyfélazonosítót) rendel az alkalmazásához, Önt pedig átirányítja az alkalmazás **Áttekintés** lapjára. Ha alkalmazását szeretné további funkciókkal bővíteni, más beállítási lehetőségeket, többek között védjegyzést, tanúsítványokat és titkos kulcsokat és API-engedélyeket is kiválaszthat.

[![Az újonnan regisztrált alkalmazás áttekintőlapja](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>További lépések

- További tudnivalók az [engedélyekről és a hozzájárulásról](v2-permissions-and-consent.md).
- Az alkalmazásregisztráció további beállítási lehetőségeinek – például hitelesítő adatoknak és engedélyeknek – és más bérlőkhöz tartozó felhasználók bejelentkezésének engedélyezéséről az alábbi rövid útmutatók nyújtanak felvilágosítást:
    - [Ügyfélalkalmazás konfigurálása a webes API-k elérésére](quickstart-configure-app-access-web-apis.md)
    - [Alkalmazás konfigurálása webes API-k közzétételére](quickstart-configure-app-expose-web-apis.md)
    - [Alkalmazás által támogatott fiókok módosítása](quickstart-modify-supported-accounts.md)
- A megfelelő [rövid útmutatót](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts) választva gyorsan készíthet alkalmazást, és olyan funkciókat adhat hozzá, mint a jogkivonatok beszerzése, a jogkivonatok frissítése, a felhasználók bejelentkeztetése és egyes felhasználóadatok megjelenítése.
- Ha szeretne többet megtudni a regisztrált alkalmazásokat jelölő két Azure AD-objektumról és azok kapcsolatáról, tekintse meg az [alkalmazás- és szolgáltatásnév-objektumokat](app-objects-and-service-principals.md) ismertető szakaszt.
- Ha szeretne többet megtudni az alkalmazások fejlesztése során alkalmazandó védjegyzési irányelvekről, olvassa el az [Védjegyzési útmutató az alkalmazásokhoz](howto-add-branding-in-azure-ad-apps.md) című cikket.
