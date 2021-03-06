---
title: Multi-Factor Authentication a Azure Active Directory B2Cban | Microsoft Docs
description: A Multi-Factor Authentication engedélyezése a Azure Active Directory B2C által védett, a felhasználók felé irányuló alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5d656a27017f3c7ec97362efc6207917ffcd1a56
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111249"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Multi-Factor Authentication engedélyezése Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A Azure Active Directory B2C (Azure AD B2C) közvetlenül az [Azure multi-Factor Authentication ad](../active-directory/authentication/concept-mfa-howitworks.md) -vel integrálódik, így egy második biztonsági réteget adhat hozzá az alkalmazásaiban való regisztráláshoz és bejelentkezési élményekhez. A többtényezős hitelesítés engedélyezése egyetlen sor kód írása nélkül. Ha már létrehozott egy regisztrációs és bejelentkezési felhasználói folyamatot, továbbra is engedélyezheti a többtényezős hitelesítést.

Ez a funkció megkönnyíti az alkalmazások számára a következő forgatókönyvek kezelését:

- Nincs szükség többtényezős hitelesítésre egy alkalmazáshoz való hozzáféréshez, de ehhez egy másikra van szükség. Például az ügyfél be tud jelentkezni egy automatikus biztosítási alkalmazásba egy közösségi vagy helyi fiókkal, de ellenőriznie kell a telefonszámot, mielőtt hozzáfér az ugyanabban a címtárban regisztrált Home Insurance-alkalmazáshoz.
- A többtényezős hitelesítés nem szükséges ahhoz, hogy általánosan hozzáférjen egy alkalmazáshoz, de ehhez a bizalmas részeit is hozzá kell férnie. Például az ügyfél bejelentkezhet egy banki alkalmazásba egy közösségi vagy helyi fiókkal, és ellenőrizheti a fiók egyenlegét, de a hálózati átvitel megkísérlése előtt ellenőriznie kell a telefonszámot.

## <a name="set-multi-factor-authentication"></a>Multi-Factor Authentication beállítása

::: zone pivot="b2c-user-flow"

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. A felső menüben a **könyvtár + előfizetés** szűrő használatával válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **felhasználói folyamatok** lehetőséget.
1. Válassza ki azt a felhasználói folyamatot, amelynek engedélyezni szeretné az MFA-t. Például *B2C_1_signinsignup*.
1. Válassza ki a **Tulajdonságok** elemet.
1. A **többtényezős hitelesítés** szakaszban válassza ki a kívánt **MFA-módszert**, majd az **MFA-kényszerítés** területen válassza az **Always on vagy a** **[feltételes](conditional-access-user-flow.md) (ajánlott)** lehetőséget. A feltételes hozzáféréshez hozzon létre egy [feltételes hozzáférési](conditional-access-identity-protection-setup.md) szabályzatot, és határozza meg azokat az alkalmazásokat, amelyekre alkalmazni szeretné a szabályzatot. 
1. Kattintson a Mentés gombra. Az MFA mostantól engedélyezve van ennél a felhasználói folyamatnál.

A **felhasználói folyamat futtatásával** ellenőrizheti a felhasználói élményt. Erősítse meg a következő helyzetet:

A multi-Factor Authentication lépése előtt létrejön egy ügyfél-fiók a bérlőben. A lépés során a rendszer megkéri az ügyfelet, hogy adjon meg egy telefonszámot, és ellenőrizze azt. Ha az ellenőrzés sikeres, a telefonszámot a rendszer a fiókhoz csatolja későbbi használatra. Annak ellenére, hogy az ügyfél megszakítja vagy kiesik, az ügyfél megkérheti, hogy a következő bejelentkezéskor ismét ellenőrizze a telefonszámot a többtényezős hitelesítés engedélyezésekor.

::: zone-end

::: zone pivot="b2c-custom-policy"

Ha engedélyezni szeretné, hogy Multi-Factor Authentication beolvassa a GitHubról az egyéni szabályzat kezdő csomagjait, frissítse a **SocialAndLocalAccountsWithMFA** Starter Pack csomag XML-fájljait a Azure ad B2C bérlői nevével. A **SocialAndLocalAccountsWithMFA**  lehetővé teszi a közösségi, helyi és multi-Factor Authentication lehetőségeket. További információ: Ismerkedés [az egyéni szabályzatokkal Active Directory B2Cban](custom-policy-get-started.md). 

::: zone-end