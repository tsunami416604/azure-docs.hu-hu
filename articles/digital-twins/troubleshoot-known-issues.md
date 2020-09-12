---
title: Ismert problémák – Azure digitális Twins
description: Segítséget kaphat az Azure digitális Twins ismert problémáinak felismeréséhez és enyhítéséhez.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: e152c0227008dd12088660b2390a8d0a5f54de96
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290778"
---
# <a name="known-issues-in-azure-digital-twins"></a>Az Azure Digital Twins ismert problémái

Ez a cikk az Azure Digital Twins szolgáltatással kapcsolatos ismert problémákról nyújt információkat.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400-ügyfél hiba: helytelen kérelem" a Cloud Shell

A Cloud Shell parancsai időnként sikertelenek lehetnek a következő hibával: "400-es ügyfél-hiba: hibás kérelem az URL-címhez: http://localhost:50342/oauth2/token ", amelyet a teljes verem nyomkövetése követ.

Kifejezetten az Azure Digital Twins esetében ez a következő parancsokra van hatással:
* `az dt route`
* `az dt model`
* `az dt twin`

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Ez a parancs újbóli futtatásával oldható fel `az login` Cloud shell és a további bejelentkezési lépések végrehajtásával. Ezután futtassa újra a parancsot.

Egy másik megoldás, ha [telepíti az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -t a gépére, így helyileg is futtathatja az Azure CLI-parancsokat. A helyi CLI nem tapasztalja ezt a problémát.

### <a name="possible-causes"></a>Lehetséges okok

Ez az Cloud Shell ismert problémájának eredménye: a [*token Lekérése Cloud Shell időszakosan meghiúsul az 400-es ügyféllel kapcsolatos hiba miatt: hibás kérelem*](https://github.com/Azure/azure-cli/issues/11749).

Ez problémát jelent az Azure Digital ikrek instance Auth-tokenekkel és a Cloud Shell alapértelmezett [felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) -alapú hitelesítésével kapcsolatban. A Futtatás hibaelhárítási lépése `az login` nem a felügyelt identitások hitelesítését, hanem ezt a problémát fokozza.

Ez nem befolyásolja az Azure Digital Twins parancsait a `az dt` vagy a `az dt endpoint` parancssorból, mert más típusú hitelesítési tokent HASZNÁLNAK (ARM-alapú), amely nem rendelkezik a Cloud Shell felügyelt identitás-hitelesítésével.

## <a name="missing-role-assignment-after-scripted-setup"></a>A szerepkör-hozzárendelés hiányzik a parancsfájlból történő telepítés után

Egyes felhasználók problémákat tapasztalhatnak a szerepkör-hozzárendelési részével kapcsolatban [*: példány és hitelesítés beállítása (parancsfájlba*](how-to-set-up-instance-scripted.md)foglalt). A parancsfájl nem jelez hibát, de az *Azure Digital Twins tulajdonos (előzetes verzió)* szerepkör nem lett sikeresen hozzárendelve a felhasználóhoz, és ez a probléma hatással lehet más erőforrások létrehozására az úton.

Annak megállapításához, hogy a szerepkör-hozzárendelés sikeresen be lett-e állítva a parancsfájl futtatása után, kövesse a telepítési cikk [*felhasználói szerepkör-hozzárendelés ellenőrzése*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) szakaszának utasításait. Ha a felhasználó nem jelenik meg ezzel a szerepkörrel, ez a probléma hatással van.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

A megoldáshoz manuálisan is beállíthatja a szerepkör-hozzárendelést a parancssori felület vagy a Azure Portal használatával. 

Kövesse az alábbi utasításokat:
* [Parancssori felület](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [portál](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Lehetséges okok

A személyes [Microsoft-fiók (MSA)](https://account.microsoft.com/account)szolgáltatásban bejelentkezett felhasználók esetében a felhasználó egyszerű azonosítója, amely a felhasználó bejelentkezési e-mail-címétől eltérő lehet, így megnehezíti a szkript felderítését és használatát a szerepkör megfelelő hozzárendeléséhez.

## <a name="issue-with-interactive-browser-authentication"></a>Probléma az interaktív böngésző-hitelesítéssel

Az Azure-beli digitális Twins-alkalmazásokban az ** [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) Library** **1.2.0** használatával történő írás során problémák merülhetnek fel a [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) metódussal.

Ez nem a könyvtár legújabb verziója. A legújabb verzió az **1.2.2**.

Az érintett módszert a következő cikkek használják: 
* [*Oktatóanyag: ügyfélalkalmazás kódolása*](tutorial-code.md)
* [*Útmutató: az alkalmazás-hitelesítési kód írása*](how-to-authenticate-client.md)
* [*Útmutató: az Azure Digital Twins API-k és SDK-k használata*](how-to-use-apis-sdks.md)

A probléma az "Azure. Identity. AuthenticationFailedException" hibaüzenettel is rendelkezik, amikor egy böngészőablakban próbálkozik a hitelesítéssel. Előfordulhat, hogy a böngészőablak nem indul el teljesen, vagy úgy tűnik, hogy sikeresen hitelesíti a felhasználót, míg az ügyfélalkalmazás továbbra is a hibával meghiúsul.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

A megoldáshoz frissítse alkalmazásait az `Azure.Identity` **1.2.2**-es verzió használatára. A könyvtár ezen verziójával a böngészőnek be kell töltenie és hitelesítenie kell a várt módon.

### <a name="possible-causes"></a>Lehetséges okok

Ez a könyvtár legújabb verziójával (1.2.0) kapcsolatos nyitott problémához kapcsolódik `Azure.Identity` : a **1.2.0** [*hitelesítés sikertelen a InteractiveBrowserCredential használatakor*](https://github.com/Azure/azure-sdk-for-net/issues/13940).

Ez a probléma akkor jelenik meg, ha az Azure Digital Twins-alkalmazásban **1.2.0** használ, vagy ha egy verzió megadása nélkül adja hozzá a könyvtárat a projekthez (ez a legújabb verzióra is vonatkozik).

## <a name="next-steps"></a>Következő lépések

További információ az Azure Digital Twins biztonságáról és engedélyeiről:
* [*Fogalmak: az Azure Digital Twins-megoldások biztonsága*](concepts-security.md)