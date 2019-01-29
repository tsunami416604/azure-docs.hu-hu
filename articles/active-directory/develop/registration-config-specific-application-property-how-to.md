---
title: Hogyan kell kitölteni egy egyénileg fejlesztett alkalmazásba az adott mezők |} A Microsoft Docs
description: Meghatározott mezők kitöltéséhez, amikor regisztrál egy egyéni fejlesztésű alkalmazás az Azure AD útmutatást
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 90b7638acf6df721a16bdcca6ceebb6f9d65ca2c
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096823"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Hogyan kell kitölteni egy egyénileg fejlesztett alkalmazásba az adott mezők

Ez a cikk röviden a az alkalmazás regisztrációs űrlapot az összes elérhető mező lehetővé teszi a [az Azure portal](https://portal.azure.com).

## <a name="register-a-new-application"></a>Új alkalmazás regisztrálása

-   Új alkalmazás regisztrálásához lépjen a [az Azure portal](https://portal.azure.com).

-   A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directoryban.**

-   Válasszon **alkalmazásregisztrációk** kattintson **Hozzáadás**.

-   Ez az alkalmazás regisztrációs űrlap nyílt.

## <a name="fields-in-the-application-registration-form"></a>Az alkalmazás regisztrációs űrlap mezőinek


| Mező            | Leírás                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Name (Név)             | Az alkalmazás neve. Ennek tartalmaznia kell legalább 4 karakterből kell állnia.                |
| Alkalmazás típusa | **Webalkalmazást vagy webes API**: Egy webalkalmazás, webes API vagy mindkettő minősülő alkalmazás 
| |**Natív**: Olyan alkalmazás, amely a felhasználó eszközére vagy számítógépére telepíthető           |
| Bejelentkezési URL-cím      | Az URL-címet, ahol felhasználók bejelentkezhetnek az alkalmazás használata                                  |

Miután megadta a fenti mezőket, az alkalmazás regisztrálva van az Azure Portalon, és a rendszer átirányítja az alkalmazás lapot. A **beállítások** gombra az alkalmazás panelen megnyílik a beállítások lapot, amely rendelkezik, további testre szabhatja az alkalmazást. Az alábbi táblázat ismerteti a beállítások lapon az összes mezőt. Vegye figyelembe, hogy csak itt jelennének meg ezeket a mezőket, attól függően, hogy létrehozott egy webalkalmazást vagy natív alkalmazások egy része.

| Mező           | Leírás                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alkalmazásazonosító  | Amikor regisztrál egy alkalmazást, az Azure AD rendeli az alkalmazást egy alkalmazás azonosítóját. Az alkalmazás-azonosító egyedi módon azonosítja az alkalmazást az Azure AD a hitelesítési kérelmek, valamint erőforrások eléréséhez használható, mint a Graph API-t.                                                          |
| Alkalmazásazonosító URI      | Ez lehet egy egyedi URI-t, általában a következő formában **https://&lt;bérlői\_neve&gt;/&lt;alkalmazás\_neve&gt;.** Adni az erőforrást, amely a token kell kiadni, az egyedi azonosítóként a engedélyezési engedélyezési folyamat során használatos. A "aud" jogcím a kiállított hozzáférési jogkivonat szintén elérhetetlenné válik. |
| Új embléma feltöltése | Ezzel az alkalmazás emblémát tölthet fel. Az embléma .bmp, .jpg vagy .png formátumúnak kell lennie, és a fájl mérete kisebb, mint 100KB lehet. A kép 215 x 215 képpont, a középső kép mérete 94 x 94 képpont kell lennie.                                                       |
| Kezdőlap URL-címe   | Ez az alkalmazás regisztrációja során megadott bejelentkezési URL.                                                                                                                                                                                                                                              |
| Kijelentkezési URL      | Ez az egyszeri kijelentkezési kijelentkezési URL-címet. Az Azure AD kijelentkezési kérelmet küld az URL-címet a felhasználónak törölnie a munkamenetet az Azure ad-vel bármely más regisztrált alkalmazás használatával.                                                                                                                                       |
| Több-bérlős  | Ez a kapcsoló megadja, hogy használható-e az alkalmazás több bérlő. Általában ez azt jelenti, hogy külső szervezetek számára az alkalmazás használatának annak regisztrálásával a bérlőben, és a céges adatokhoz való hozzáférést.                                                                   |
| Válasz URL-címek      | A válasz URL-címek olyan a végpontok, ahol az Azure AD visszaadja az alkalmazás által kért jogkivonatokat.                                                                                                                                                                                                          |
| Átirányítási URI azonosítók   | Natív alkalmazások esetén ez a Ha a felhasználó a sikeres hitelesítést követően érkezik. Az Azure AD, ellenőrizze, hogy az OAuth 2.0 kérelemben megadja az átirányítási URI-t az alkalmazás megfelel a portálon a regisztrált értékeket.                                                            |
| Kulcsok            | Programozott hozzáférés webes API-k, felhasználói beavatkozás nélkül az Azure AD által védett kulcsokat is létrehozhat. Az a \* \*kulcsok\* \* lapon adja meg a kulcs leírása és a lejárati dátumot, és mentse a kulcs létrehozásához. Ellenőrizze, hogy helyre mentse, biztonságos, akkor később elérheti azt.             |

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](../manage-apps/what-is-application-management.md)
