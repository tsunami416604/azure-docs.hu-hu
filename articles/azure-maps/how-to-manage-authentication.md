---
title: Azure Maps-hitelesítés kezelése |} A Microsoft Docs
description: Az Azure portal segítségével Azure Maps-hitelesítés kezelésére szolgál.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 414ca7156fd59ec1dc08e45c73e9eb30835078d4
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259924"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps-hitelesítés kezelése

Miután létrehozott egy Azure Maps-fiók, egy ügyfél-Azonosítót és a kulcsok az Azure Active Directory (Azure AD) és a megosztott kulcsos hitelesítés támogatására jönnek létre.

## <a name="view-authentication-details"></a>Hitelesítési részleteinek megtekintése

A hitelesítési adatait az Azure Portalon tekintheti meg. Lépjen a fiók, és válassza a **hitelesítési** a a **beállítások** menü.

![Hitelesítés részletei](./media/how-to-manage-authentication/how-to-view-auth.png)

 További tudnivalókért lásd: [hitelesítés az Azure Maps](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Azure AD-alkalmazás regisztrációjának beállítása

Miután létrehozott egy Azure Maps-fiók, kell létesítenie az Azure AD-bérlő és az Azure Maps-erőforrások közötti kapcsolat.

1. Az Azure AD paneljéről, és hozzon létre egy alkalmazás regisztrációját. Adjon meg egy nevet, a regisztráció. Az a **bejelentkezési URL-** adja meg a kezdőlapon a webalkalmazás / API-t (például https:\//localhost/). Ha már rendelkezik egy regisztrált alkalmazást, folytassa a 2. lépés.

    ![Appok regisztrálása](./media/how-to-manage-authentication/app-registration.png)

    ![Alkalmazás regisztráció részletei](./media/how-to-manage-authentication/app-create.png)

2. API delegált engedélyek hozzárendelése az Azure Maps, nyissa meg az alkalmazás alatt **alkalmazásregisztrációk**, majd válassza ki **beállítások**.  Válassza ki **szükséges engedélyek**, majd válassza ki **Hozzáadás**. Keresse meg és válassza **az Azure Maps** alatt **API kiválasztása**, majd válassza ki a **kiválasztása** gombra.

    ![Az Alkalmazásengedélyek API](./media/how-to-manage-authentication/app-permissions.png)

3. Alatt **engedélyek kiválasztása**, jelölje be **hozzáférés az Azure Maps**, majd válassza ki a **kiválasztása** gombra.

    ![Az Alkalmazásengedélyek API kiválasztása](./media/how-to-manage-authentication/select-app-permissions.png)

4. Befejezése lépésnél a vagy b, a hitelesítési módszertől függően.

    1. Ha az alkalmazás felhasználói jogkivonat-hitelesítést használ az Azure Maps Web SDK-val, engedélyezze a `oauthEnableImplicitFlow` beállítást igaz értékre a az alkalmazás regisztrációs részletei lap a Manifest szakasz szerint.
    
       ![Manifest aplikace](./media/how-to-manage-authentication/app-manifest.png)

    2. Ha az alkalmazás a kiszolgáló/alkalmazás-hitelesítést használ, lépjen a **kulcsok** alkalmazásregisztráció (díjcsomag) paneljén, és hozzon létre egy jelszót vagy egy nyilvános kulcsú tanúsítvány töltse fel az alkalmazásregisztráció. Ha kiválasztása után hozzon létre egy jelszót **mentése**, másolja be a jelszót később, és tárolja biztonságos helyen. Ezt a jelszót fogja használni az Azure ad-ből jogkivonatok beszerzéséhez.

       ![Alkalmazáskulcsok](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Az Azure Maps GRANT rbac-t

Az Azure Maps-fiók társítása az Azure AD-bérlővel, miután egy felhasználó vagy alkalmazás egy vagy több Azure Maps hozzáférés-vezérlés szerepköreinek való hozzárendelésével hozzáférés-vezérlés megadásához.

1. Lépjen a **hozzáférés-vezérlés (IAM)** válassza **szerepkör-hozzárendelések**, majd válassza ki **szerepkör-hozzárendelés hozzáadása**.

    ![Engedélyezés RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Az a **szerepkör-hozzárendelés hozzáadása** időszak alatt **szerepkör**, jelölje be **Azure Maps dátum olvasója (minta)**. Alatt **rendelhet hozzáféréseket**válassza **az Azure AD-felhasználó, csoport vagy szolgáltatásnév**. A **kiválasztása**, a felhasználó vagy alkalmazás kiválasztása. Kattintson a **Mentés** gombra.

    ![Szerepkör-hozzárendelés hozzáadása](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Elérhető az Azure Maps RBAC-szerepkörök megtekintése

Szerepköralapú hozzáférés-vezérlés (RBAC) szerepkörök érhetők el az Azure Maps megtekintéséhez lépjen a **hozzáférés-vezérlés (IAM)** válassza **szerepkörök**, majd keresse meg a szerepkörök verziótól kezdve, és **az Azure Maps**. Ezek azok a szerepkörök, amelyek hozzáférést biztosíthat.

![Elérhető szerepkörök megtekintése](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Az Azure Maps RBAC megtekintése

Az RBAC részletes hozzáférés-vezérlést biztosít.

Felhasználókat és alkalmazásokat, amelyek kapott RBAC az Azure Maps megtekintéséhez lépjen a **hozzáférés-vezérlés (IAM)**, jelölje be **szerepkör-hozzárendelések**, és szűrjön rá a **az Azure Maps**.

![Felhasználók és alkalmazások RBAC engedélyek megtekintése](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Az Azure Maps jogkivonatok kérelmezésére

Miután regisztrálja az alkalmazást, és azt az Azure Maps társított, hozzáférési jogkivonatokat kérhet.

* Ha az alkalmazás felhasználói jogkivonat-hitelesítést használ az Azure Maps Web SDK-val, szeretné-e a HTML-oldalt konfigurálása az Azure Maps ügyfél-Azonosítót és az Azure AD alkalmazás-azonosítóját.

* Ha az alkalmazás a kiszolgáló/alkalmazás-hitelesítést használ, szeretné-e jogkivonatot kérhet az Azure AD bejelentkezési végpont `https://login.microsoftonline.com` az Azure ad-ben erőforrás-azonosító `https://atlas.microsoft.com/`, az Azure Maps ügyfél-azonosító, az Azure AD alkalmazás-Azonosítóját és az Azure AD alkalmazás regisztrálása jelszó vagy tanúsítvány.

Hozzáférési jogkivonatok kér az Azure AD-felhasználók és az egyszerű szolgáltatások kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>További lépések

* Az Azure AD-hitelesítés és az Azure Maps Web SDK kapcsolatos további információkért lásd: [az Azure AD és az Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).