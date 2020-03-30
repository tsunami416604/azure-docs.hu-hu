---
title: Az erőforrásokat és az adatokat fenyegető veszélyek kezelése
titleSuffix: Azure AD B2C
description: Ismerje meg az Azure Active Directory B2C szolgáltatásmegtagadási és jelszótámadások észlelési és kockázatcsökkentési technikáit.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a438363b054361420222804dffac7973470e82e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183601"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Erőforrásokkal és adatokkal szembeni fenyegetések kezelése az Azure Active Directory B2C-ben

Az Azure Active Directory B2C (Azure AD B2C) olyan beépített funkciókkal rendelkezik, amelyek segítenek az erőforrásokat és az adatokat fenyegető veszélyek elleni védelemben. Ezek a fenyegetések közé tartozik a szolgáltatásmegtagadási és jelszótámadások. A szolgáltatásmegtagadási támadások miatt az erőforrások nem érhetők el a tervezett felhasználók számára. A jelszótámadások az erőforrásokhoz való jogosulatlan hozzáférést eredményeznek.

## <a name="denial-of-service-attacks"></a>Szolgáltatásmegtagadási támadások

Az Azure AD B2C syn cookie-val védekezik a SYN árvízi támadások ellen. Az Azure AD B2C szolgáltatásmegtagadási támadások ellen is védelmet nyújt a díjak és a kapcsolatok korlátozásának használatával.

## <a name="password-attacks"></a>Jelszótámadások

A felhasználók által beállított jelszavaknak ésszerűen összetettnek kell lenniük. Az Azure AD B2C jelszótámadások hoz létre kockázatcsökkentési technikákat. A megoldás magában foglalja a találgatásos jelszótámadások és a szótárjelszó-támadások észlelését. Különböző jelek használatával az Azure AD B2C elemzi a kérelmek integritását. Az Azure AD B2C intelligens módon megkülönbözteti a felhasználókat a hackerektől és a botnetektől.

Az Azure AD B2C kifinomult stratégiát használ a fiókok zárolásához. A fiókok a kérelem IP-címe és a megadott jelszavak alapján vannak zárolva. A zárolás időtartama is növekszik a valószínűsége, hogy ez egy támadás. A jelszó 10-szer sikertelen kipróbálása (az alapértelmezett kísérlet küszöbértéke) után egyperces zárolás következik be. A következő alkalommal, amikor a bejelentkezés sikertelen a fiók zárolásának feloldása után (azaz a fiók automatikus zárolása után a szolgáltatás a zárolási időszak lejárta után), egy másik egyperces zárolás következik be, és minden sikertelen bejelentkezésnél folytatódik. Ugyanazt a jelszót többször i. nem számít több sikertelen bejelentkezésnek.

Az első 10 kizárási időszak egy perces. A következő 10 zárolási időszak kissé hosszabb, és minden 10 zárolási időszak után növekszik az időtartam. A zárolásszámláló a sikeres bejelentkezés után nullára áll vissza, ha a fiók nincs zárolva. A zárolási időszakok akár öt óráig is tarthatnak.

## <a name="manage-password-protection-settings"></a>Jelszavas védelmi beállítások kezelése

A jelszavas védelmi beállítások, köztük a zárolási küszöbérték kezelése:

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com)
1. A **felső** menü Directory + előfizetésszűrőjével válassza ki az Azure AD B2C-bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. A **Biztonság**csoportban válassza a **Hitelesítési módszerek (előzetes verzió)** lehetőséget, majd a **Jelszavas védelem**lehetőséget.
1. Adja meg a kívánt jelszavas védelmi beállításokat, majd válassza a **Mentés gombot.**

    ![Az Azure Portal jelszavas védelem lapja az Azure AD-beállításokban](./media/threat-management/portal-02-password-protection.png)
    <br />*A zárolási küszöbérték et 5-re állítja a **Jelszavas védelem** beállításai ban.*

## <a name="view-locked-out-accounts"></a>Kizárt fiókok megtekintése

A zárolt fiókokról az Active Directory [bejelentkezési tevékenységjelentésében](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)kaphat információt. Az **Állapot csoportban**válassza a **Hiba**lehetőséget. Sikertelen bejelentkezési kísérletek zárolt fiókot jeleznek a `50053` **bejelentkezési hibakóddal:**

![Az Azure AD bejelentkezési jelentésének szakasza zárolt fiókkal](./media/threat-management/portal-01-locked-account.png)

A bejelentkezési tevékenységjelentés azure-active directoryban való megtekintéséről a [Bejelentkezési tevékenységjelentés hibakódjai](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)című témakörben olvashat.
