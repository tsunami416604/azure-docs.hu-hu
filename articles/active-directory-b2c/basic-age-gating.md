---
title: Az Age kapuzás engedélyezése a Azure Active Directory B2Cban | Microsoft Docs
description: Ismerje meg, hogyan azonosíthatók a kiskorúak az alkalmazás használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1fc63b222fd2f08bb4b5596d58f825c8f6b1910e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836236"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Az Age kapuzás engedélyezése a Azure Active Directory B2Cban

>[!IMPORTANT]
>Ez a funkció nyilvános előzetes verzióban érhető el. Ne használja a szolgáltatást éles alkalmazásokhoz.
>

Azure Active Directory B2C (Azure AD B2C) Age kapuzás lehetővé teszi az alkalmazást használó kiskorúak azonosítását. Dönthet úgy is, hogy letiltja a kiskorú regisztrációját az alkalmazásba. A felhasználók visszatérhetnek az alkalmazáshoz, és meghatározhatják a korcsoport és a szülői hozzájárulásukat is. A Azure AD B2C szülői engedély nélkül blokkolhatja a kiskorúakat. A Azure AD B2C beállítható úgy is, hogy az alkalmazás eldöntse, mi a teendő a kiskorúakkal.

Miután engedélyezte az Age kapuzás a [felhasználói folyamat](user-flow-overview.md)során, a rendszer megkérdezi a felhasználókat, hogy mikor születtek és milyen országban/régióban laknak. Ha egy felhasználó bejelentkezik, amely korábban nem adta meg az adatokat, akkor a következő bejelentkezéskor meg kell adnia. A szabályok minden alkalommal érvényesek, amikor egy felhasználó bejelentkezik.

Azure AD B2C a felhasználó által megadott adatok alapján azonosítja, hogy azok kisebbek-e. A rendszer ezután frissíti a **beszerzésimennyiség** mezőt a fiókjában. Az érték lehet `null`, `Undefined`, `Minor`, `Adult`és `NotAdult`.  A rendszer ezután a **beszerzésimennyiség** és a **consentProvidedForMinor** mezőket használja a **legalAgeGroupClassification**értékének kiszámításához.

Az Age kapuzás két életkori értéket tartalmaz: az életkort, amelyet valaki már nem tekint kisebbnek, és azt, hogy a kiskorú gyermekeknek hogyan kell beleegyezniük. A következő táblázat felsorolja azokat a kor-szabályokat, amelyek egy kisebb és egy kisebb, a hozzájuk szükséges engedély definiálásához használatosak.

| Ország/régió | Ország/régió neve | Kisebb beleegyező életkor | Kisebb kor |
| -------------- | ------------------- | ----------------- | --------- |
| Alapértelmezett | None | None | 18 |
| AE | Egyesült Arab Emírségek | None | 21 |
| AT | Ausztria | 14 | 18 |
| BE | Belgium | 14 | 18 |
| BG | Bulgária | 16 | 18 |
| BH | Bahrein | None | 21 |
| CM | Kamerun | None | 21 |
| CY | Ciprus | 16 | 18 |
| CZ | Csehország | 16 | 18 |
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
| n/a | Namíbia | None | 21 |
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
| Egyesült Államok | Egyesült Államok | 13 | 18 |

## <a name="age-gating-options"></a>Kor kapuzás beállításai

### <a name="allowing-minors-without-parental-consent"></a>Szülői engedély nélküli kiskorúak engedélyezése

A regisztrációt, bejelentkezést vagy mindkettőt engedélyező felhasználói folyamatok esetében dönthet úgy is, hogy engedélyezi a kiskorúak számára, hogy beleegyezett az alkalmazásba. A szülői engedély nélküli kiskorúak bejelentkezhetnek, vagy a szokásos módon regisztrálhatnak, és Azure AD B2C a **legalAgeGroupClassification** jogcím azonosító jogkivonatot bocsát ki. Ez a jogcím határozza meg a felhasználók által használt felhasználói élményt, például a szülői engedély begyűjtését és a **consentProvidedForMinor** mező frissítését.

### <a name="blocking-minors-without-parental-consent"></a>Kiskorúak blokkolása szülői engedély nélkül

A regisztrációt, bejelentkezést vagy mindkettőt engedélyező felhasználói folyamatok esetében dönthet úgy is, hogy letiltja a kiskorúakat az alkalmazás beleegyezike nélkül. A következő beállítások érhetők el a letiltott felhasználók Azure AD B2Cban való kezelésére:

- JSON küldése az alkalmazásnak – ez a beállítás visszaküldi a választ az alkalmazásnak, amelyet a rendszer letiltott.
- Hibaüzenet megjelenítése – a felhasználó egy lap jelenik meg, amely tájékoztatja őket arról, hogy nem férhetnek hozzá az alkalmazáshoz.

## <a name="set-up-your-tenant-for-age-gating"></a>A bérlő beállítása az Age kapuzás

Ahhoz, hogy a kapuzás a felhasználói folyamatokban használhassa, konfigurálnia kell a bérlőt, hogy további tulajdonságokkal rendelkezzen.

1. Győződjön meg arról, hogy az Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza a **címtár + előfizetés** szűrőt. Válassza ki a bérlőt tartalmazó könyvtárat.
2. A Azure Portal bal felső sarkában válassza a **minden szolgáltatás** lehetőséget, keresse meg és válassza ki a **Azure ad B2C**.
3. Válassza ki a bérlő **tulajdonságait** a bal oldali menüben.
2. Az **Age kapuzás** szakaszban kattintson a configure ( **Konfigurálás**) elemre.
3. Várjon, amíg a művelet befejeződik, és a bérlő az Age kapuzás lesz beállítva.

## <a name="enable-age-gating-in-your-user-flow"></a>Az életkor kapuzás engedélyezése a felhasználói folyamatokban

Miután a bérlő beállította az Age kapuzás használatát, ezt a funkciót használhatja a [felhasználói folyamatokban](user-flow-versions.md) , ahol engedélyezve van. Az Age kapuzás a következő lépésekkel engedélyezhető:

1. Hozzon létre egy olyan felhasználói folyamatot, amelynél engedélyezve van az Age kapuzás.
2. A felhasználói folyamat létrehozása után válassza a menü **Tulajdonságok** elemét.
3. Az **Age kapuzás** szakaszban válassza az **engedélyezve**lehetőséget.
4. Ezután eldöntheti, hogyan kívánja kezelni a kiskorúakat azonosító felhasználókat. A **regisztrációhoz vagy a bejelentkezéshez**válassza `Allow minors to access your application` vagy `Block minors from accessing your application`lehetőséget. Ha kijelöli a kiskorúakat, válassza a `Send a JSON back to the application` vagy `Show an error message`lehetőséget.




