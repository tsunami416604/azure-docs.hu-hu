---
title: Egy egyéni által fejlesztett alkalmazás adott mezők kitöltésére |} Microsoft Docs
description: Útmutatás bizonyos mezők kitöltéséhez, amikor regisztrál egy egyéni fejlett alkalmazást az Azure ad szolgáltatással
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: cd4313efb5d08842ba12ec00e6e5160214800d56
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "34055617"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Egy egyéni által fejlesztett alkalmazás adott mezők kitöltésére

Ez a cikk röviden az alkalmazás regisztráláshoz az összes elérhető mezők lehetővé teszi a [Azure-portálon](https://portal.azure.com).

## <a name="register-a-new-application"></a>Egy új alkalmazás regisztrálása

-   Új alkalmazás regisztrálásához lépjen a [Azure-portálon](https://portal.azure.com).

-   Kattintson a bal oldali navigációs ablak **Azure Active Directoryban.**

-   Válasszon **App regisztrációk** kattintson **Hozzáadás**.

-   Ez az alkalmazás regisztrációs űrlap nyisson meg.

## <a name="fields-in-the-application-registration-form"></a>Az alkalmazás regisztrációs űrlap mezők


| Mező            | Leírás                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Name (Név)             | Az alkalmazás nevét. Ennek tartalmaznia kell legalább 4 karakter.                |
| Alkalmazás típusa | **Webalkalmazást vagy webes API**: egy alkalmazás, amely egy webes alkalmazás, egy webes API vagy mindkettő 
| |**Natív**: a felhasználói eszköz vagy a számítógépen telepített alkalmazás           |
| Bejelentkezési URL      | Az URL-cím, ahol felhasználó tud egyszerre bejelentkezni az alkalmazás használatára                                  |

Miután megadta a fenti mezők, az alkalmazás regisztrálva van az Azure portálon, és a rendszer visszairányítja az alkalmazás oldalra. A **beállítások** az alkalmazás ablaktáblájának gombra a beállítások oldalon, amelynek a segítségével testre szabhatja az alkalmazás további mezők megnyílik. Az alábbi táblázat ismerteti a beállítások lapon található összes mezőhöz. Vegye figyelembe, hogy csak mutatunk be ezeket a mezőket, attól függően, hogy egy webes alkalmazás vagy a natív alkalmazás létrehozása egy részét.

| Mező           | Leírás                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alkalmazásazonosító  | Ha egy alkalmazás regisztrálása az Azure AD hozzárendeli az alkalmazás egy azonosítót. Az azonosító az alkalmazást az Azure AD hitelesítési kérelmek egyedi azonosításához, valamint erőforrások eléréséhez használható alkalmazás, például a Graph API-val.                                                          |
| Alkalmazásazonosító URI      | Egy egyedi URI Azonosítóját, általában az űrlap legyen **https://&lt;bérlői\_neve&gt;/&lt;alkalmazás\_neve&gt;.** Ez használható az engedélyezési grant folyamat során adhatja meg az erőforrást, amely a token számára kell kiadni, az egyedi azonosítóként. A "és" jogcím a kiállított jogkivonat is válik. |
| Új embléma feltöltése | Ezzel az alkalmazás embléma feltöltéséhez. Az embléma .bmp, .jpg vagy .png formátumúnak kell lennie, és a fájlméret legfeljebb 100KB kell lennie. A kép méretei 215 x 215 képpont, központi lemezkép dimenziókkal 94 x 94 képpontban kell lennie.                                                       |
| Kezdőlap URL-címe   | Ez az alkalmazás regisztrálása során megadott bejelentkezési URL.                                                                                                                                                                                                                                              |
| Kijelentkezési URL      | Ez az egyetlen kijelentkezési kijelentkezési URL-címet. Az Azure AD kijelentkezési kérést küld az URL-cím, amikor a felhasználó törli a munkamenetet és az Azure AD más regisztrált alkalmazás használatával.                                                                                                                                       |
| Több-bérlős  | Ez a kapcsoló határozza meg, hogy használható-e az alkalmazás által több bérlő. Általában ez azt jelenti, hogy külső szervezetek használni az alkalmazás regisztrálása a bérlőben, és a szervezeti adatokhoz való hozzáférés biztosítása.                                                                   |
| Válasz URL-címek      | A válasz URL-címei a végpontok ahol az Azure AD adja vissza az alkalmazás által kért jogkivonatokhoz.                                                                                                                                                                                                          |
| Átirányítási URI azonosítók   | Natív alkalmazások ez pedig ahol a sikeres hitelesítést követően a felhasználó küldése. Az Azure AD, ellenőrizze, hogy az OAuth 2.0 kérelemben megadja az átirányítási URI-t az alkalmazás megfelel a regisztrált értékeket a portálon.                                                            |
| Kulcsok            | Programozott hozzáférés webes API-k, felhasználói beavatkozás nélkül az Azure AD által védett kulcsokat hozhat létre. Az a \* \*kulcsok\* \* lapon adja meg egy kulcs leírását és a lejárati dátum és a kulcs létrehozásához mentéséhez. Ügyeljen arra, hogy mentse a munkafüzetet valahol biztonságos, akkor nem fog tudni férni később.             |

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](manage-apps/what-is-application-management.md)
