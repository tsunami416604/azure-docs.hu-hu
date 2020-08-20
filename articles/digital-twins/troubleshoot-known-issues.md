---
title: Ismert problémák – Azure digitális Twins
description: Segítséget kaphat az Azure digitális Twins ismert problémáinak felismeréséhez és enyhítéséhez.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 0c008061d2d4fafa96eda934d5026c92839a0bdb
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661486"
---
# <a name="known-issues-in-azure-digital-twins"></a>Az Azure Digital Twins ismert problémái

Ez a cikk az Azure Digital Twins szolgáltatással kapcsolatos ismert problémákról nyújt információkat.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400-ügyfél hiba: helytelen kérelem" a Cloud Shell

A Cloud Shell parancsai időnként sikertelenek lehetnek a következő hibával: "400-es ügyfél-hiba: hibás kérelem az URL-címhez: http://localhost:50342/oauth2/token ", amelyet a teljes verem nyomkövetése követ.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Ez a parancs újbóli futtatásával és a `az login` következő bejelentkezési lépések végrehajtásával oldható fel.

Ezt követően újra kell futtatnia a parancsot.

### <a name="possible-causes"></a>Lehetséges okok

Ez az Cloud Shell ismert problémájának eredménye: a [*token Lekérése Cloud Shell időszakosan meghiúsul az 400-es ügyféllel kapcsolatos hiba miatt: hibás kérelem*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="missing-role-assignment-after-scripted-setup"></a>A szerepkör-hozzárendelés hiányzik a parancsfájlból történő telepítés után

Egyes felhasználók problémákat tapasztalhatnak a szerepkör-hozzárendelési részével kapcsolatban [*: példány és hitelesítés beállítása (parancsfájlba*](how-to-set-up-instance-scripted.md)foglalt). A parancsfájl nem jelez hibát, de az *Azure Digital Twins tulajdonos (előzetes verzió)* szerepkör nem lett sikeresen hozzárendelve a felhasználóhoz, és ez hatással lehet más erőforrások létrehozására az úton.

Annak megállapításához, hogy a szerepkör-hozzárendelés sikeresen be lett-e állítva a parancsfájl futtatása után, kövesse a telepítési cikk [*felhasználói szerepkör-hozzárendelés ellenőrzése*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) szakaszának utasításait. Ha a felhasználó nem jelenik meg ezzel a szerepkörrel, ez a probléma hatással van.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

A megoldáshoz manuálisan is beállíthatja a szerepkör-hozzárendelést a parancssori felület vagy a Azure Portal használatával. 

Kövesse az alábbi utasításokat:
* [Parancssori felület](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [portál](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Lehetséges okok

A személyes [Microsoft-fiók (MSA)](https://account.microsoft.com/account)szolgáltatásban bejelentkezett felhasználók esetében a felhasználó egyszerű azonosítója, amely az ilyen típusú parancsokban különbözik a felhasználó bejelentkezési e-mail-címétől, így megnehezíti a szkript felderítését és használatát a szerepkör megfelelő hozzárendeléséhez.

## <a name="issue-with-interactive-browser-authentication"></a>Probléma az interaktív böngésző-hitelesítéssel

Ha az Azure ** [. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) Library**legújabb verziójának ( **1.2.0**) használatával ír be hitelesítési kódot az Azure-beli digitális Twins-alkalmazásokban, akkor a [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) metódussal kapcsolatos problémák léphetnek fel.

Az érintett módszert a következő cikkek használják: 
* [*Oktatóanyag: ügyfélalkalmazás kódolása*](tutorial-code.md)
* [*Útmutató: az alkalmazás-hitelesítési kód írása*](how-to-authenticate-client.md)
* [*Útmutató: az Azure Digital Twins API-k és SDK-k használata*](how-to-use-apis-sdks.md)

A probléma az "Azure. Identity. AuthenticationFailedException" hibaüzenettel is rendelkezik, amikor egy böngészőablakban próbálkozik a hitelesítéssel. Előfordulhat, hogy a böngészőablak nem indul el teljesen, vagy úgy tűnik, hogy sikeresen hitelesíti a felhasználót, míg az ügyfélalkalmazás továbbra is a hibával meghiúsul.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

A megoldáshoz az alkalmazások explicit módon használják az Azure. Identity **1.1.1**-es verzióját. A könyvtár ezen verziójával a böngészőnek be kell töltenie és hitelesítenie kell a várt módon.

>[!NOTE]
> Nem elegendő, ha a könyvtárat anélkül adja hozzá, hogy bármilyen verziót megad, mivel ez a legújabb **1.2.0**továbbra is alapértelmezett lesz. Explicit módon meg kell adnia a **1.1.1** -es verziót.

### <a name="possible-causes"></a>Lehetséges okok

Ez az Azure. Identity Library ( **1.2.0**) legújabb verziójával kapcsolatos nyitott problémához kapcsolódik: a [*hitelesítés nem sikerült a InteractiveBrowserCredential használatakor*](https://github.com/Azure/azure-sdk-for-net/issues/13940).

Ez a probléma akkor jelenik meg, ha az Azure Digital Twins-alkalmazásban **1.2.0** használ, vagy ha egy verzió megadása nélkül adja hozzá a könyvtárat a projekthez (ez a legújabb verzióra is vonatkozik).

## <a name="next-steps"></a>További lépések

További információ az Azure Digital Twins biztonságáról és engedélyeiről:
* [*Fogalmak: az Azure Digital Twins-megoldások biztonsága*](concepts-security.md)