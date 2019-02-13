---
title: Azure Maps-hitelesítés kezelése |} A Microsoft Docs
description: Az Azure portal segítségével Azure Maps-hitelesítés kezelésére szolgál.
author: walsehgal
ms.author: v-musehg
ms.date: 02/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: db0450ab8b765ec158201c6db149a7de7d135b98
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56143846"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps-hitelesítés kezelése

Miután létrehozott egy Azure Maps-fiók, Azure Active Directory (Azure AD) és a megosztott kulcsos hitelesítés támogatására három GUID jön létre.

Annak hitelesítési adatait lépjen a **hitelesítési** lap **beállítások** az Azure Portalon. Keresse meg a fiókjához. Válassza ki **hitelesítési** a menüből.


## <a name="view-authentication-details"></a>Hitelesítési részleteinek megtekintése

Megtekintheti az hitelesítési adatait, lépjen a **hitelesítési** lehetőséget a beállítások menüben.

![Nézet hitelesítés](./media/how-to-manage-authentication/how-to-view-auth.png)

 Hitelesítés és az Azure Maps kapcsolatos további információkért lásd: [hitelesítés az Azure Maps](https://aka.ms/amauth).


## <a name="configure-azure-ad-app-registration"></a>Az Azure AD-alkalmazás regisztrációjának beállítása

Az Azure térkép-fiók létrehozása után az Azure AD-bérlő és az Azure Maps Azure-erőforrások közötti kapcsolat szükség. 

1. Nyissa meg az AAD panel, és hozzon létre egy Alkalmazásregisztráció egy név és a bejelentkezési URL-címet a webalkalmazás kezdőlapjaként / API-t, mint például "https://localhost/". Ha már rendelkezik egy regisztrált alkalmazást, folytassa a 2. lépés.

    ![Appok regisztrálása](./media/how-to-manage-authentication/app-registration.png)

    ![Appok regisztrálása](./media/how-to-manage-authentication/app-create.png)

2. API delegált engedélyek hozzárendelése az Azure Maps alatt alkalmazásregisztrációk alkalmazás megnyitásával, majd kattintson a **beállítások**.  Válassza ki **szükséges engedélyek** válassza **Hozzáadás**. Keresése és kiválasztása az Azure Maps alatt **API kiválasztása** kattintson **kiválasztása**.

    ![Alkalmazás api-engedélyek](./media/how-to-manage-authentication/app-permissions.png)

3. Végül az engedélyek kiválasztása területen válassza a hozzáférés az Azure Maps, és kattintson a Kiválasztás elemre.

    ![Válassza ki az alkalmazás api-engedélyek](./media/how-to-manage-authentication/select-app-permissions.png)

4. Hajtsa végre a vagy b, attól függően, hitelesítés végrehajtására.

    1. Ha az alkalmazás kívánó felhasználó jogkivonat-hitelesítés használata az Azure Maps Web SDK-t, akkor engedélyeznie kell a `oauthEnableImplicitFlow` beállítást igaz értékre a az alkalmazás regisztrációs részletei lap a Manifest szakasz szerint. 
    
       ![Manifest aplikace](./media/how-to-manage-authentication/app-manifest.png)

    2. Ha az alkalmazás használ kiszolgáló/alkalmazás hitelesítési nyissa meg a **kulcsok** paneljén az alkalmazás regisztrációját, és hozzon létre egy jelszót, vagy töltse fel az alkalmazásregisztráció a nyilvános kulcsú tanúsítvány. Ha a jelszót, egyszer, **mentése**, másolja be a jelszót később, és tárolja biztonságos helyen, az aad-ből a tokenek beszerzésére fogja használni. 

       ![Alkalmazáskulcsok](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Az Azure Maps GRANT rbac-t

Miután az Azure Maps-fiók az Azure AD-bérlő társítva, hozzáférés-vezérlés a felhasználó vagy alkalmazás érhető el az Azure Maps access control szerepkör(ök) való hozzárendelésével adható meg.

1. Navigáljon a **hozzáférés-vezérlés** lehetőséget, majd kattintson **szerepkör-hozzárendelések**, és **szerepkör-hozzárendelés hozzáadása**.

    ![Engedélyezés RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. A szerepkör-hozzárendelés kiugró ablakban, jelölje be az Azure Maps dátum olvasója (minta) **szerepkör**, **rendelhet hozzáféréseket**: Azure AD-felhasználó, csoport vagy egyszerű szolgáltatásnév **kiválasztása** felhasználó vagy alkalmazás legördülő listából, és **mentése**.

    ![Szerepkör-hozzárendelés hozzáadása](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Elérhető az Azure Maps RBAC-szerepkörök megtekintése

Elérhető szerepköralapú hozzáférés-vezérlés szerepköreinek, amely képes lehet hozzáférést biztosítani, az Azure Maps megtekintéséhez lépjen **hozzáférés-vezérlés (IAM)** lehetőséget, majd kattintson a **szerepkörök**, és keresse meg a szerepkörök kezdve **Az azure Maps**.

![Elérhető szerepkörök megtekintése](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-role-based-access-control-rbac"></a>Az Azure Maps szerepköralapú hozzáférés-vezérlés (RBAC) megtekintése

Azure ad-ben szerepköralapú hozzáférés-vezérlés (RBAC) révén részletes hozzáférés-vezérlést tesz lehetővé. 

Felhasználók vagy alkalmazások engedéllyel rendelkező RBAC az Azure Maps megtekinteni, navigáljon a **hozzáférés-vezérlés (IAM)** beállításnál válassza **szerepkör-hozzárendelések**, és a szűrés **az Azure Maps**. Elérhető szerepkörök alatt jelenik meg.

![Az RBAC megtekintése](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Az Azure Maps jogkivonatok kérelmezésére

Miután az alkalmazás regisztrálása és az Azure Maps társított, most már igényelhet hozzáférési jogkivonatok.

* Ha az alkalmazás kívánó felhasználó jogkivonat-hitelesítés használata az Azure Maps Web SDK (Web), szeretné-e a html-oldalt konfigurálása az Azure Maps ügyfél-azonosító és az Azure AD alkalmazás-azonosító.


* A kiszolgáló/alkalmazás-hitelesítést használó alkalmazások jogkivonatot kérhet az Azure AD bejelentkezési végpont https://login.microsoftonline.com az Azure Maps ügyfél-azonosító, az Azure AD-alkalmazás-Azonosítóját és az Azure AD-alkalmazás regisztrációja jelszó vagy tanúsítvány.

A hozzáférési jogkivonatok kér az Azure AD-felhasználók és az egyszerű szolgáltatások további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>További lépések

* Az Azure AD-hitelesítés és az Azure Maps Web SDK kapcsolatos további információkért lásd: [az Azure AD és az Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).