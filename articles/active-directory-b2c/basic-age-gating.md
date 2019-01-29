---
title: Engedélyezze a korhatáralapú hozzáférés Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg az alkalmazás használatával kiskorúak azonosítása.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e3e60a7ee435cb23bd6a7f78d409691c94d6c49e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153266"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Azure Active Directory B2C korhatáralapú hozzáférés engedélyezése

>[!IMPORTANT]
>Ez a funkció nyilvános előzetes verzióban érhető el. Az éles környezetben ne használja a funkciót. 
>

Korhatáralapú hozzáférés Azure Active Directory (Azure AD) B2C lehetővé teszi az alkalmazás használni kívánt kiskorúak azonosításához. Választhatja a kisebb a jelentkezik be az alkalmazás letiltása. Felhasználók is lépjen vissza az alkalmazást, és azok korcsoport és azok szülői beleegyezési állapotának azonosításához. Az Azure AD B2C blokkolhatja kiskorú szülői beleegyezése nélkül. Az Azure AD B2C is beállíthatja, hogy döntéshozatalra kiskorú, hogy az alkalmazás.

Miután engedélyezte a korhatáralapú hozzáférés a [felhasználói folyamat](active-directory-b2c-reference-policies.md), amikor születtek, és milyen ország rendszer kéri a felhasználóktól, élő. Ha egy felhasználó bejelentkezik, amely korábban még nem megadta az adatokat, azok kell adja meg a következő bejelentkezéskor. A szabályok érvényesek minden alkalommal, amikor egy felhasználó bejelentkezik.

Az Azure AD B2C-t, amelyet a felhasználó azonosítására, akár egy kisebb információkat használja. A **korcsoport** mező majd frissül a fiókban. Az érték lehet `null`, `Undefined`, `Minor`, `Adult`, és `NotAdult`.  A **korcsoport** és **consentProvidedForMinor** mező értékének kiszámítása majd használható **legalAgeGroupClassification**.

Korhatáralapú magában foglalja a két kora érték: az életkor valaki már nem tekinthető egy kisebb, és a korát, amellyel egy kisebb szülői beleegyezési kell rendelkeznie. Az alábbi táblázat az életkor szabályok egy kisebb és a egy kisebb igénylő jóváhagyási meghatározásához használt.

| Ország | Ország neve | Kisebb hozzájárulási kora | Kisebb kora |
| ------- | ------------ | ----------------- | --------- |
| Alapértelmezett | None | None | 18 |
| AE | Egyesült Arab Emírségek | None | 21 |
| AT | Ausztria | 14 | 18 |
| BE | Belgium | 14 | 18 |
| BG | Bulgária | 16 | 18 |
| BH | Bahrein | None | 21 |
| CM | Kamerun | None | 21 |
| CY | Ciprus | 16 | 18 |
| CZ | Cseh Köztársaság | 16 | 18 |
| DE | Németország | 16 | 18 |
| DK | Dánia | 16 | 18 |
| EE | Észtország | 16 | 18 |
| EG | Egyiptom | None | 21 |
| ES | Spanyolország | 13 | 18 |
| JK | Franciaország | 16 | 18 |
| GB | Egyesült Királyság | 13 | 18 |
| GR | Görögország | 16 | 18 |
| HR | Horvátország | 16 | 18 |
| HU | Magyarország | 16 | 18 |
| IE | Írország | 13 | 18 |
| IT | Olaszország | 16 | 18 |
| KR | Koreai Köztársaság | 14 | 18 |
| LT | Litvánia | 16 | 18 |
| LU | Luxemburg | 16 | 18 |
| LV | Lettország | 16 | 18 |
| MT | Málta | 16 | 18 |
| NA | Namíbia | None | 21 |
| NL | Hollandia | 16 | 18 |
| PL | Lengyelország | 13 | 18 |
| PT | Portugália | 16 | 18 |
| RO | Románia | 16 | 18 |
| SE | Svédország | 13 | 18 |
| SG | Szingapúr | None | 21 |
| SI | Szlovénia | 16 | 18 |
| SK | Szlovákia | 16 | 18 |
| TD | Csád | None | 21 |
| TH | Thaiföld | None | 20 |
| TW | Tajvan | None | 20 | 
| USA | Egyesült Államok | 13 | 18 |

## <a name="age-gating-options"></a>Korhatáralapú hozzáférés-beállítások
 
### <a name="allowing-minors-without-parental-consent"></a>Kiskorú szülői beleegyezési nélkül engedélyezése

A felhasználói folyamatok, amelyek lehetővé teszik a regisztrációs, bejelentkezési vagy mindkettőt, dönthet úgy, hogy az alkalmazásba kiskorúak beleegyezése nélkül. Kiskorú szülői beleegyezési nélkül engedélyezettek a bejelentkezéshez, vagy iratkozzon fel, ahogy a normál és az Azure AD B2C-azonosító jogkivonat a problémák a **legalAgeGroupClassification** jogcím. Ez a jogcím határozza meg a felhasználói élményt, hogy a felhasználók rendelkeznek, például a szülői beleegyezési gyűjtése és frissítése a **consentProvidedForMinor** mező.

### <a name="blocking-minors-without-parental-consent"></a>Kiskorú szülői beleegyezési nélkül blokkolása

Felhasználói folyamatok, amelyek lehetővé teszik a regisztrációs, bejelentkezési vagy mindkettőt kiválaszthatja az alkalmazás hozzájárulása nélkül kiskorúak letiltása. Az Azure AD B2C-ben a letiltott felhasználók az alábbi lehetőségek érhetők el:

- A JSON küldése az alkalmazásnak – ezt a lehetőséget választ küld az alkalmazásnak, hogy egy kisebb blokkolása.
- A felhasználó számára megjelenik egy oldal, amely tájékoztatja őket, hogy nem tudják elérni az alkalmazást - hibalap megjelenítése.

## <a name="set-up-your-tenant-for-age-gating"></a>Állítsa be a bérlő korhatáralapú

Felhasználói folyamat korhatáralapú hozzáférés használatához szüksége be a bérlőt, hogy a további tulajdonságok.

1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben. Válassza ki azt a könyvtárat, amely tartalmazza a bérlő. 
2. Válassza ki **minden szolgáltatás** az Azure portal bal felső sarkában lévő keresése és kiválasztása **Azure AD B2C-vel**.
3. Válassza ki **tulajdonságok** a bal oldali menüben a bérlő számára.
2. Alatt a **korhatáralapú** területén kattintson a **konfigurálása**.
3. Várjon, amíg a művelet befejeződik, és a bérlő korhatáralapú hoznak létre.

## <a name="enable-age-gating-in-your-user-flow"></a>A felhasználói folyamat korhatáralapú hozzáférés engedélyezése

Miután a bérlő beállított használati korhatáralapú, felhasználhatja ennek a funkciónak a [felhasználói folyamatok](user-flow-versions.md) ahol engedélyezve van. Engedélyezi a korhatáralapú az alábbi lépéseket követve:

1. Hozzon létre egy felhasználói folyamat, amely rendelkezik a korhatáralapú engedélyezve van.
2. Miután a felhasználói folyamatot hoz létre, válassza ki **tulajdonságok** menüjében.
3. Az a **korhatáralapú** szakaszban jelölje be **engedélyezve**.
4. Ezután döntse el, hogyan szeretné kezelni a felhasználókat, amelyek kiskorúak azonosításához. A **regisztrálási vagy bejelentkezési**, választja `Allow minors to access your application` vagy `Block minors from accessing your application`. Ha blokkolja a kiskorúak van kiválasztva, válassza `Send a JSON bcak to the application` vagy `Show an error message`. 




