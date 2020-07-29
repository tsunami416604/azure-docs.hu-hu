---
author: baanders
description: fájl belefoglalása a lehetséges további követelményekhez az Azure digitális Twins-telepítőben
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 57be62d0f74f19e35eedf8720e2a6f5cbd3b45d4
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87335757"
---
Lehetséges, hogy a szervezete az előfizetések tulajdonosainak további műveleteit igényli az alkalmazások regisztrálásának sikeres beállításához (és így a használható Azure Digital Twins-példányok beállításának befejezéséhez). A szükséges lépések a szervezete által megadott beállításoktól függően változhatnak.

Íme néhány gyakori lehetséges tevékenység, amelyet a tulajdonosnak esetleg kell végrehajtania. Ezek és egyéb műveletek a Azure Portal [*Azure ad alkalmazás regisztrációk*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) oldaláról is elvégezhetők.
* Adja meg a rendszergazdai jóváhagyást az alkalmazás regisztrálásához. Előfordulhat, hogy a szervezete *rendszergazdai* jogosultságokkal rendelkezik, globálisan be van kapcsolva az Azure ad-ben az előfizetésben lévő összes alkalmazás regisztrálásához. Ha igen, a tulajdonosnak be kell jelölnie ezt a gombot a vállalat számára az alkalmazás regisztrációjának *API-engedélyei* oldalon, hogy az alkalmazás regisztrációja érvényes legyen:

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/grant-admin-consent.png" alt-text="A "rendszergazdai jóváhagyás megadása" gomb portál nézete az API-engedélyek alatt":::
  - Ha a beleegyező engedély megadása sikeres volt, az Azure Digital Twins bejegyzésének meg kell jelenítenie a _ **(vállalata)** számára engedélyezett_ *állapotinformációkat* .
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/granted-admin-consent.png" alt-text="A vállalat számára az API-engedélyek alatt megadott rendszergazdai engedély portál nézete":::
* Nyilvános ügyfél-hozzáférés aktiválása
* A webes és asztali hozzáférés adott válasz URL-címeinek beállítása
* Implicit OAuth2 hitelesítési folyamatok engedélyezése

Az alkalmazások regisztrálásával és más lehetőségeivel kapcsolatos további információkért lásd: [*alkalmazás regisztrálása a Microsoft Identity platformon*](https://docs.microsoft.com/graph/auth-register-app-v2).

Most már rendelkezik egy Azure Digital Twins-példánnyal, amely jogosult a felügyeletére, és beállította, hogy az ügyfélalkalmazások hozzáférjenek az alkalmazáshoz.