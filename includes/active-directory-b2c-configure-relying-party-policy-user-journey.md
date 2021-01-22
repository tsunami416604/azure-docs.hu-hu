---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: f94076f06fb13bae2a26e8ab6003d7574a2dacfd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2021
ms.locfileid: "98674233"
---
## <a name="configure-the-relying-party-policy"></a>A függő entitás házirendjének konfigurálása

A függő entitások házirendje (például [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml)) Megadja azt a felhasználói utat, amelyet a Azure ad B2C végre fog hajtani. A **DefaultUserJourney** elem megkeresése a [függő entitáson](../articles/active-directory-b2c/relyingparty.md)belül. Frissítse a  **ReferenceId** , hogy az megfeleljen a felhasználói útvonal azonosítójának, amelyben felvette az identitás-szolgáltatót. 

Az alábbi példában a `CustomSignUpOrSignIn` felhasználói útra a **ReferenceId** a következőre van beállítva `CustomSignUpOrSignIn` :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Egyéni szabályzat feltöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. A **szabályzatok** területen válassza az **identitási élmény keretrendszere** elemet.
1. Válassza az **egyéni házirend feltöltése** lehetőséget, majd töltse fel a módosított két házirendet, a következő sorrendben: a kiterjesztési szabályzatot, például `TrustFrameworkExtensions.xml` a függő entitás házirendjét, például: `SignUpSignIn.xml` .

## <a name="test-your-custom-policy"></a>Egyéni szabályzat tesztelése

1. Válassza ki a függő entitás házirendjét, például: `B2C_1A_signup_signin`
1. **Alkalmazás** esetén válasszon ki egy korábban regisztrált webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **Futtatás most** gombra.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.

