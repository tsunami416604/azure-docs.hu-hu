---
title: Korpirálás engedélyezése az Azure Active Directory B2C könyvtárában | Microsoft dokumentumok
description: További információ a kiskorúak azonosításáról az alkalmazás használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 56cbeb8e8fe21f4b39c2f5c6af43e83ae330e5d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189973"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Korozás engedélyezése az Azure Active Directory B2C-ben

>[!IMPORTANT]
>Ez a funkció nyilvános előzetes verzióban érhető el. Ne használjon szolgáltatást éles alkalmazásokhoz.
>

Az Azure Active Directory B2C (Azure AD B2C) korgatálása lehetővé teszi az alkalmazást használni kívánó kiskorúak azonosítását. Beállíthatja, hogy a kiskorú jelentkezzen be az alkalmazásba. A felhasználók visszatérhetnek az alkalmazáshoz, és azonosíthatják korcsoportjukat és szülői beleegyezési állapotukat. Az Azure AD B2C szülői beleegyezés nélkül letilthatja a kiskorúakat. Az Azure AD B2C is beállítható, hogy az alkalmazás döntse el, mi a teendő a kiskorúakkal.

Miután engedélyezte az életkori gating-ot a [felhasználói folyamatban,](user-flow-overview.md)a rendszer megkérdezi a felhasználókat, hogy mikor születtek, és melyik országban/régióban élnek. Ha egy felhasználó olyan bejelentkezik, amely korábban nem adta meg az adatokat, a következő bejelentkezéskor meg kell adnia azt. A szabályok minden alkalommal érvényesek, amikor egy felhasználó bejelentkezik.

Az Azure AD B2C a felhasználó által megadott adatok alapján állapítja meg, hogy ők is kiskorúak-e. Az **ageGroup** mező ezután frissül a fiókjában. Az érték `null`lehet `Undefined` `Minor`, `Adult`, `NotAdult`, , és .  Az **ageGroup** és **consentProvidedForMinor** mezőket ezután a **legalAgeGroupClassification**értékének kiszámításához használja a rendszer.

Az életkori gating két életkori értéket foglal magában: azt a kort, amelyet valaki már nem tekint kiskorúnak, és azt a kort, amikor a kiskorúnak szülői beleegyezéssel kell rendelkeznie. Az alábbi táblázat azokat a korszabályokat sorolja fel, amelyek a kiskorú és a beleegyezést igénylő kiskorú meghatározásához használatosak.

| Ország/régió | Ország/régió neve | Kisebb hozzájárulási életkor | Kisebb életkor |
| -------------- | ------------------- | ----------------- | --------- |
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

## <a name="age-gating-options"></a>Kor gating lehetőségek

### <a name="allowing-minors-without-parental-consent"></a>Kiskorúak szülői beleegyezés nélküli engedélyezése

A felhasználói folyamatok, amelyek lehetővé teszik a regisztráció, bejelentkezés, vagy mindkettő, dönthet úgy, hogy a kiskorúak beleegyezése nélkül az alkalmazásba. A szülői beleegyezéssel nem rendelkező kiskorúak a szokásos módon jelentkezhetnek be vagy regisztrálhatnak, és az Azure AD B2C azonosító jogkivonatot ad ki a **legalAgeGroupClassification** jogcímhez. Ez a jogcím határozza meg a felhasználók által tapasztalt élményt, például a szülői beleegyezés gyűjtését és a **consentProvidedForMinor** mező frissítését.

### <a name="blocking-minors-without-parental-consent"></a>Kiskorúak letiltása szülői beleegyezés nélkül

A felhasználói folyamatok, amelyek lehetővé teszik a regisztráció, bejelentkezés vagy mindkettő, dönthet úgy, hogy blokkolja a kiskorúak hozzájárulása nélkül az alkalmazás. Az Azure AD B2C-ben a letiltott felhasználók kezelésére a következő lehetőségek érhetők el:

- JSON küldése vissza az alkalmazásnak – ez a beállítás választ küld az alkalmazásnak, amely egy kiskorú le van tiltva.
- Hibaoldal megjelenítése – a felhasználó egy olyan oldal jelenik meg, amely tájékoztatja őket arról, hogy nem férhetnek hozzá az alkalmazáshoz.

## <a name="set-up-your-tenant-for-age-gating"></a>Állítsa be a bérlőt az életkori gatinghoz

Az életkor gating használata a felhasználói folyamat, konfigurálnia kell a bérlő további tulajdonságokkal.

1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja a felső menü **Directory + előfizetésszűrő** jének kiválasztásával. Válassza ki a bérlőt tartalmazó könyvtárat.
2. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, keresse meg és válassza az **Azure AD B2C**lehetőséget.
3. Válassza ki **a bérlők tulajdonságai** a bal oldali menüben.
2. Az **Életkor beállítás** szakaszban kattintson a Configure **(Kor) szakaszra.**
3. Várja meg, amíg a művelet befejeződik, és a bérlő lesz beállítva a kor gating.

## <a name="enable-age-gating-in-your-user-flow"></a>Kormegforgatás engedélyezése a felhasználói folyamatban

Miután a bérlő be van állítva az életkor gating használatára, majd használhatja ezt a funkciót a [felhasználói folyamatokban,](user-flow-versions.md) ahol engedélyezve van. A kormegforgatást a következő lépésekkel engedélyezheti:

1. Hozzon létre egy felhasználói folyamatot, amelyen engedélyezve van az életkor imitálása.
2. A felhasználói folyamat létrehozása után válassza a menü **Tulajdonságok parancsát.**
3. Az **Életkor kiforgatás a** **csoportban**válassza az Engedélyezve lehetőséget.
4. Ezután eldöntheti, hogyan szeretné kezelni a kiskorúként azonosító felhasználókat. A **Regisztráció vagy a bejelentkezés lehetőségnél**válassza a vagy `Allow minors to access your application` `Block minors from accessing your application`a lehetőséget. Ha a kiskorúak letiltása `Send a JSON back to the application` `Show an error message`lehetőség van kiválasztva, a vagy a lehetőséget választja.




