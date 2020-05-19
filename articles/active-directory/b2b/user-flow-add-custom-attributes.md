---
title: Egyéni attribútumok hozzáadása az Azure AD felhasználói folyamataihoz
description: További információ az önkiszolgáló bejelentkezési felhasználói folyamatok attribútumainak testreszabásáról.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f187cc47d9c64c8257cc097734fa41e10629f1c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597449"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>Egyéni attribútumok definiálása felhasználói folyamatokhoz (előzetes verzió)
|     |
| --- |
| Az egyéni felhasználói attribútumok szolgáltatás a Azure Active Directory nyilvános előzetes verziója. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Az egyes alkalmazásokhoz különböző követelmények vonatkoznak a regisztráció során gyűjteni kívánt információkra. Az Azure AD az attribútumokban tárolt beépített információkkal (például Utónév, vezetéknév, város és irányítószám) rendelkezik. Az Azure AD segítségével kiterjesztheti a vendég fiókokban tárolt attribútumok készletét, ha a külső felhasználó egy felhasználói folyamaton keresztül jelentkezik be.

Létrehozhat egyéni attribútumokat a Azure Portalban, és használhatja azokat az önkiszolgáló bejelentkezési felhasználói folyamatokban. Ezeket az attribútumokat a [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api)használatával is elolvashatja és elvégezheti. Microsoft Graph API támogatja a bővítmény-attribútumokkal rendelkező felhasználók létrehozását és frissítését. A Graph API-bővítmény attribútumai az egyezmény használatával vannak elnevezve `extension_<Application-client-id>_attributename` . Például:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Az `<Application-client-id>` **alkalmazás (ügyfél) azonosítója**melletti **Alkalmazásregisztrációk** oldalon található. Ez az azonosító a bérlőre vonatkozik.

## <a name="create-a-custom-attribute"></a>Egyéni attribútum létrehozása

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként.
2. Az **Azure-szolgáltatások**területen válassza a **Azure Active Directory**lehetőséget.
3. A bal oldali menüben válassza a **külső identitások**lehetőséget.
4. Válassza az **egyéni felhasználói attribútumok (előzetes verzió)** lehetőséget. Megjelenik a rendelkezésre álló felhasználói attribútumok listája.

   ![Felhasználói attribútumok kiválasztása a regisztrációhoz](media/user-flow-add-custom-attributes/user-attributes.png)

5. Attribútum hozzáadásához válassza a **Hozzáadás**lehetőséget.
6. Az **attribútum hozzáadása** panelen adja meg a következő értékeket:

   - **Név** – adja meg az egyéni attribútum nevét (például "Shoesize").
   - **Adattípus** – válassza ki az adattípust (**karakterlánc**, **logikai**vagy **int**).
   - **Leírás** – nem kötelezően megadhatja az egyéni attribútum leírását belső használatra. Ez a leírás nem látható a felhasználó számára.

   ![Attribútum hozzáadása](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. Kattintson a **Létrehozás** gombra.

Az egyéni attribútum már elérhető a felhasználói attribútumok listájában és a felhasználói folyamatokban való használathoz. Egyéni attribútum csak akkor jön létre, amikor az első alkalommal használja a felhasználói folyamatokban, és nem, amikor hozzáadja a felhasználói attribútumok listájához.

Miután létrehozott egy új felhasználót egy olyan felhasználói folyamat használatával, amely az újonnan létrehozott egyéni attribútumot használja, az objektum [Microsoft Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer)kérdezhető le. Ekkor látnia kell a **ShoeSize** a regisztrációs útvonalon összegyűjtött attribútumok listájában, és az alkalmazásnak visszaadott tokenben tekintheti meg. A jogcímek az alkalmazásnak visszaadott tokenbe való felvételével kapcsolatos további információkért lásd: [címtárszolgáltatás választható jogcímek konfigurálása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-directory-extension-optional-claims)

## <a name="next-steps"></a>További lépések

[Önkiszolgáló bejelentkezési felhasználói folyamat hozzáadása egy alkalmazáshoz](self-service-sign-up-user-flow.md)