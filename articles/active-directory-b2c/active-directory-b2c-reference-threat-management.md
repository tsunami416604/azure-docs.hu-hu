---
title: A Azure Active Directory B2C erőforrásaira és adatforrásaira vonatkozó fenyegetések kezelése
description: Ismerje meg az észlelési és kockázatcsökkentő technikákat a szolgáltatásmegtagadási támadások és a Azure Active Directory B2C a jelszavak elleni támadásokhoz.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 281bd73671352e1e525e11a7bfde1882d3ef8864
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065422"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>A Azure Active Directory B2C erőforrásaira és adatforrásaira vonatkozó fenyegetések kezelése

A Azure Active Directory B2C (Azure AD B2C) beépített funkciókkal rendelkezik, amelyek segítségével védelmet biztosíthat az erőforrásokkal és az adataival szembeni fenyegetések ellen. Ezek a fenyegetések a szolgáltatásmegtagadási támadások és a jelszavak elleni támadásokat is tartalmazzák. A szolgáltatásmegtagadást okozó támadások a kívánt felhasználók számára elérhetetlenné tehetik az erőforrásokat. A jelszó-támadások az erőforrásokhoz való jogosulatlan hozzáféréshez vezethetnek.

## <a name="denial-of-service-attacks"></a>Szolgáltatásmegtagadási támadások

A Azure AD B2C SYN-cookie-val védi az SYN FLOOD támadásokat. A Azure AD B2C a díjszabás és a kapcsolatok korlátainak használatával is védelmet nyújt a szolgáltatásmegtagadási támadások ellen.

## <a name="password-attacks"></a>Jelszavas támadások

A felhasználók által beállított jelszavakat ésszerűen összetettnek kell lenniük. Azure AD B2C a jelszavak elleni támadásokra vonatkozó enyhítő technikákat tartalmaz. A mérséklés magában foglalja a találgatásos jelszavak támadásait és a szótár jelszavas támadásait. A különböző jelek használatával a Azure AD B2C elemzi a kérelmek integritását. Azure AD B2C úgy lett kialakítva, hogy intelligens módon megkülönböztesse a kívánt felhasználókat a hackerek és a botnetek számára.

Azure AD B2C egy kifinomult stratégiát használ a fiókok zárolásához. A fiókok zárolása a kérelem IP-címe és a beírt jelszavak alapján történik. A zárolás időtartama a támadás valószínűsége alapján is nő. Ha a jelszó 10 alkalommal sikertelen volt (az alapértelmezett kísérlet küszöbértéke), egy egyperces zárolás történik. Amikor a rendszer a fiók zárolásának következő lépése után nem sikerül bejelentkezni (azaz azt követően, hogy a szolgáltatás automatikusan zárolta a szolgáltatást a zárolási időszak lejárta után), egy másik egyperces zárolás lép fel, és folytatja az összes sikertelen bejelentkezést. A jelszó többszöri megadásával nem számít több sikertelen bejelentkezésnek.

Az első 10 zárolási időszak egy percig tart. A következő 10 zárolási időszak valamivel hosszabb, és minden 10 zárolási időszak után növekszik az időtartam. A zárolási számláló a sikeres bejelentkezés után nullára állítja vissza, ha a fiók nincs zárolva. A zárolási időszakok akár öt óráig is tarthatnak.

## <a name="manage-password-protection-settings"></a>Jelszavas védelem beállításainak kezelése

A jelszavas védelem beállításainak kezelése, beleértve a zárolási küszöbértéket:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a portál jobb felső menüjében, majd válassza ki a Azure ad B2C bérlőt.
1. A bal oldali menüben válassza a **Azure Active Directory** lehetőséget (vagy válassza a **minden szolgáltatás** lehetőséget a portál bal felső részén, majd keresse meg és válassza ki a *Azure Active Directory*).
1. A **Biztonság**területen válassza a **hitelesítési módszerek**, majd a **jelszavas védelem**lehetőséget.
1. Adja meg a kívánt jelszavas védelmi beállításokat, majd válassza a **Mentés**lehetőséget.

    ![Azure Portal jelszavas védelem lap az Azure AD-beállításokban](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
    <br />*A zárolási küszöbérték beállítása 5 értékre a **jelszavas védelem** beállításaiban*.

## <a name="view-locked-out-accounts"></a>Kizárt fiókok megtekintése

A kizárt fiókokkal kapcsolatos információk beszerzéséhez tekintse meg a Active Directory [bejelentkezési tevékenység jelentését](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md). Az **állapot**területen válassza a **hiba**lehetőséget. Sikertelen bejelentkezési kísérletek a **bejelentkezési hibakód** alapján `50053` zárolt fiókot jeleznek:

![Az Azure AD bejelentkezési jelentés szakasza, amely a zárolt fiókot mutatja](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

Ha szeretné megtudni, hogyan tekintheti meg a bejelentkezési tevékenység jelentését Azure Active Directoryban, tekintse meg a [bejelentkezési tevékenység jelentésének hibakódait](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
