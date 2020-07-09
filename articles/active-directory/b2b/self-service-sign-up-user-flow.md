---
title: Önkiszolgáló bejelentkezési felhasználói folyamat hozzáadása – Azure AD
description: A összevonása a Facebook használatával lehetővé teheti a külső felhasználók (vendégek) számára, hogy saját Facebook-fiókjával jelentkezzenek be az Azure AD-alkalmazásokba.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9fa9d9e23de9a4ec93cbef6d2696d7bec70d41d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85551247"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app-preview"></a>Önkiszolgáló bejelentkezési felhasználói folyamat hozzáadása egy alkalmazáshoz (előzetes verzió)
> [!NOTE]
> Az önkiszolgáló regisztráció a Azure Active Directory nyilvános előzetes funkciója. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Felhasználói folyamatokat hozhat létre a szervezete által készített alkalmazásokhoz. Ha a felhasználói folyamatot egy alkalmazással társítja, lehetővé teszi, hogy engedélyezze a regisztrálást az alkalmazáson. Több alkalmazást is kiválaszthat a felhasználói folyamathoz való társításhoz. Miután hozzárendelte a felhasználói folyamatot egy vagy több alkalmazáshoz, a felhasználók, akik meglátogatják az alkalmazást, regisztrálhatnak, és elérhetik a vendég fiókot a felhasználói folyamaton konfigurált beállítások használatával.

> [!NOTE]
> A felhasználói folyamatokat a szervezet által készített alkalmazásokkal társíthatja. A felhasználói folyamatok nem használhatók Microsoft-alkalmazásokhoz, például a SharePointhoz vagy a Teams szolgáltatáshoz.

## <a name="before-you-begin"></a>Előkészületek

### <a name="add-social-identity-providers-optional"></a>Közösségi identitású szolgáltatók hozzáadása (nem kötelező)

Az Azure AD az önkiszolgáló regisztráció alapértelmezett identitás-szolgáltatója. Ez azt jelenti, hogy a felhasználók alapértelmezés szerint egy Azure AD-fiókkal regisztrálhatnak. A közösségi identitással rendelkező szolgáltatók a Google-és Facebook-fiókok támogatásához is felvehetők a regisztrációs folyamatokban.

- [Facebook hozzáadása a közösségi identitású szolgáltatók listájához](facebook-federation.md)
- [A Google hozzáadása a közösségi identitású szolgáltatók listájához](google-federation.md)

> [!NOTE]
> Az aktuális előzetes verzióban, ha az önkiszolgáló regisztrációs felhasználói folyamat egy alkalmazáshoz van társítva, és a felhasználónak egy meghívót küld az alkalmazásnak, a felhasználó nem fog tudni Gmail-fiókot használni a meghívó beváltásához. Megkerülő megoldásként a felhasználó átléphet az önkiszolgáló bejelentkezési folyamaton. Vagy a meghívást beválthatják egy másik alkalmazáshoz való hozzáféréssel vagy a saját alkalmazások portálján a következő címen: https://myapps.microsoft.com .

### <a name="define-custom-attributes-optional"></a>Egyéni attribútumok definiálása (nem kötelező)

A felhasználói attribútumok a felhasználó által az önkiszolgáló regisztráció során gyűjtött értékek. Az Azure AD számos attribútumot tartalmaz, de létrehozhat egyéni attribútumokat is a felhasználói folyamatokban való használathoz. Ezeket az attribútumokat a Microsoft Graph API használatával is elolvashatja és elvégezheti. Lásd: [Egyéni attribútumok definiálása felhasználói folyamatokhoz](user-flow-add-custom-attributes.md).

## <a name="enable-self-service-sign-up-for-your-tenant"></a>Önkiszolgáló regisztráció engedélyezése a bérlő számára

Ahhoz, hogy önkiszolgáló regisztrációs felhasználói folyamatot lehessen hozzáadni az alkalmazásaihoz, engedélyeznie kell a szolgáltatást a bérlő számára. Az engedélyezése után a vezérlők elérhetővé válnak a felhasználói folyamat során, amely lehetővé teszi a felhasználói folyamat alkalmazáshoz való hozzárendelését.

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként.
2. Az **Azure-szolgáltatások**területen válassza a **Azure Active Directory**lehetőséget.
3. Válassza a **felhasználói beállítások**lehetőséget, majd a **külső felhasználók**területen válassza a **külső együttműködési beállítások kezelése**lehetőséget.
4. Állítsa be a **vendég önkiszolgáló regisztráció engedélyezése felhasználói folyamatokkal (előzetes verzió)** az **Igen**értékre.

   ![Vendég önkiszolgáló regisztrálásának engedélyezése](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)

## <a name="create-the-user-flow-for-self-service-sign-up"></a>Felhasználói folyamat létrehozása önkiszolgáló regisztrációhoz

Ezután létre kell hoznia a felhasználói folyamatot az önkiszolgáló regisztrációhoz, és hozzá kell adnia egy alkalmazáshoz.

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként.
2. Az **Azure-szolgáltatások**területen válassza a **Azure Active Directory**lehetőséget.
3. A bal oldali menüben válassza a **külső identitások**lehetőséget.
4. Válassza a **felhasználói folyamatok (előzetes verzió)** lehetőséget, majd válassza az **új felhasználói folyamat**elemet.

   ![Új felhasználói folyamat hozzáadása gomb](media/self-service-sign-up-user-flow/new-user-flow.png)

5. A **Létrehozás** lapon adja meg a felhasználói folyamat **nevét** . Vegye figyelembe, hogy a név automatikusan **B2X_1_**.
6. Az **Identity Providers** listán válasszon ki egy vagy több olyan identitás-szolgáltatót, amelyet a külső felhasználók használhatnak az alkalmazásba való bejelentkezéshez. A **Azure Active Directory regisztráció** alapértelmezés szerint ki van választva. (Lásd [még](#before-you-begin) a jelen cikk korábbi részében ismertetett tudnivalókat az identitás-szolgáltatók hozzáadásának megismeréséhez.)
7. A **felhasználói attribútumok**területen válassza ki a felhasználótól gyűjteni kívánt attribútumokat. További attribútumok esetén válassza a **továbbiak megjelenítése**lehetőséget. Válassza például a **továbbiak megjelenítése**lehetőséget, majd az **ország/régió**, a **megjelenítendő név**és az **Irányítószám**elemnél válassza az attribútumok és jogcímek lehetőséget. Válassza az **OK** lehetőséget.

   ![Új felhasználói folyamat létrehozása lap](media/self-service-sign-up-user-flow/create-user-flow.png)

8. Válassza a **Létrehozás** lehetőséget.
9. Az új felhasználói folyamat megjelenik a **felhasználói folyamatok (előzetes verzió)** listában. Ha szükséges, frissítse az oldalt.

## <a name="select-the-layout-of-the-attribute-collection-form"></a>Az attribútumok gyűjteménye űrlap elrendezésének kiválasztása

Megadhatja, hogy az attribútumok milyen sorrendben jelenjenek meg a regisztrációs oldalon. 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Azure Active Directory** lehetőséget.
2. Válassza ki a **külső identitások**lehetőséget, majd válassza a **felhasználói folyamatok (előzetes verzió)** lehetőséget.
3. Válassza ki az önkiszolgáló bejelentkezési felhasználói folyamatot a listából.
4. A **Testreszabás** **területen válassza a lapelrendezések elemet**.
5. A program felsorolja a begyűjteni kívánt attribútumokat. A Megjelenítés sorrendjének módosításához válasszon ki egy attribútumot, **majd válassza a**feljebb, a **lejjebb,** **a feljebb**vagy a **lentre ugrás**lehetőséget.
6. Kattintson a **Mentés** gombra.

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>Alkalmazások hozzáadása az önkiszolgáló bejelentkezési felhasználói folyamathoz

Most már hozzárendelheti az alkalmazásokat a felhasználói folyamathoz.

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként.
2. Az **Azure-szolgáltatások**területen válassza a **Azure Active Directory**lehetőséget.
3. A bal oldali menüben válassza a **külső identitások**lehetőséget.
4. Az **önkiszolgáló regisztráció**alatt válassza a **felhasználói folyamatok (előzetes verzió)** lehetőséget.
5. Válassza ki az önkiszolgáló bejelentkezési felhasználói folyamatot a listából.
6. A bal oldali menüben a **használat**alatt válassza az **alkalmazások**lehetőséget.
7. Válassza az **alkalmazás hozzáadása**lehetőséget.

   ![Alkalmazás társítása a felhasználói folyamathoz](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. Válassza ki az alkalmazást a listából. Vagy használja a keresőmezőt az alkalmazás megkereséséhez, majd jelölje ki.
9. Kattintson a **Kiválasztás** gombra.

## <a name="next-steps"></a>További lépések

- [A Google hozzáadása a közösségi identitású szolgáltatók listájához](google-federation.md)
- [Facebook hozzáadása a közösségi identitású szolgáltatók listájához](facebook-federation.md)
- [API-összekötők használata a felhasználói folyamatok webes API-kon keresztüli testreszabásához és bővítéséhez](api-connectors-overview.md)
- [Egyéni jóváhagyási munkafolyamat hozzáadása a felhasználói folyamathoz](self-service-sign-up-add-approvals.md)
