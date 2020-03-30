---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186995"
---
## <a name="ropc-flow-notes"></a>ROPC-folyamatjegyzetek
Az Azure Active Directory B2C (Azure AD B2C) könyvtárában a következő beállítások támogatottak:

- **Natív ügyfél**: A hitelesítés során a felhasználói beavatkozás akkor történik, amikor a kód egy felhasználói oldali eszközön fut. Az eszköz lehet egy mobil alkalmazás, amely fut egy natív operációs rendszer, például az Android és iOS.
- **Nyilvános ügyfélfolyamat:** Csak az alkalmazás által összegyűjtött felhasználói hitelesítő adatok kerülnek elküldésre az API-hívásban. Az alkalmazás hitelesítő adatait a rendszer nem küldi el.
- **Új jogcímek hozzáadása:** Az azonosító jogkivonat tartalma módosítható új jogcímek hozzáadásához.

A következő folyamatok nem támogatottak:

- **Kiszolgáló-kiszolgáló**: Az identitásvédelmi rendszernek megbízható IP-címre van szüksége, amelyet a hívótól (a natív ügyféltől) kell begyűjteni az interakció részeként. Kiszolgálóoldali API-hívás esetén csak a kiszolgáló IP-címe lesz használva. Ha túllépi a sikertelen hitelesítések dinamikus küszöbértékét, az identitásvédelmi rendszer ismétlődő IP-címet azonosíthat támadóként.
- **Bizalmas ügyfélfolyamat:** Az alkalmazásügyfél-azonosító érvényesítve van, de az alkalmazás titkos kulcsot nem érvényesíti.

A ROPC-folyamat használatakor vegye figyelembe a következőket:

- A ROPC nem működik, ha megszakad a felhasználói beavatkozást igényelő hitelesítési folyamat. Ha például egy jelszó lejárt, vagy módosítani kell, többtényezős hitelesítésre van szükség, vagy ha több információra van szükség a bejelentkezés során (például a felhasználó hozzájárulása).
- A ROPC csak a helyi fiókokat támogatja. A felhasználók nem jelentkezhetnek be összevont identitásszolgáltatókkal, például a Microsofttal, a Google+-szal, a Twitterrel, az AD-FS-sel vagy a Facebookkal.
- A Munkamenet-kezelés szolgáltatás, beleértve a bejelentkezett (KMSI) bejelentkezést is, nem alkalmazható.
