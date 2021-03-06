---
title: Ismert problémák – Azure digitális Twins
description: Segítséget kaphat az Azure digitális Twins ismert problémáinak felismeréséhez és enyhítéséhez.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q3
ms.openlocfilehash: a9735e355244d51464c66c10e02f97f03d2e67cd
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673468"
---
# <a name="known-issues-in-azure-digital-twins"></a>Az Azure Digital Twins ismert problémái

Ez a cikk az Azure Digital Twins szolgáltatással kapcsolatos ismert problémákról nyújt információkat.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400-ügyfél hiba: helytelen kérelem" a Cloud Shell

**Probléma leírása:** A (z) rendszeren futó Cloud Shell parancsai *https://shell.azure.com* időnként sikertelenek lehetnek a következő hibával: "400-es ügyfél-hiba: hibás kérelem az URL-címhez: http://localhost:50342/oauth2/token ", amelyet a teljes verem nyomkövetése követ.

| Ez hatással van rám? | Ok | Feloldás |
| --- | --- | --- |
| Az &nbsp; Azure &nbsp; Digital &nbsp; Twins-ban ez a következő parancsokra van hatással:<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | Ez az Cloud Shell ismert problémájának eredménye: a [*token Lekérése Cloud Shell időszakosan meghiúsul az 400-es ügyféllel kapcsolatos hiba miatt: hibás kérelem*](https://github.com/Azure/azure-cli/issues/11749).<br><br>Ez problémát jelent az Azure Digital ikrek instance Auth-tokenekkel és a Cloud Shell alapértelmezett [felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) -alapú hitelesítésével kapcsolatban. <br><br>Ez nem befolyásolja az Azure Digital Twins parancsait a `az dt` vagy a `az dt endpoint` parancssorból, mert más típusú hitelesítési tokent használnak (Azure Resource Manager alapján), amely nem rendelkezik a Cloud Shell felügyelt identitás-hitelesítésével. | Ennek egyik módja, ha újra szeretné futtatni a `az login` parancsot a Cloud Shellban, és végrehajtja a következő bejelentkezési lépéseket. Ez a folyamat a felügyelt identitások hitelesítésén kívülre vált, ami elkerüli a probléma gyökerét. Ezután futtassa újra a parancsot.<br><br>Azt is megteheti, hogy megnyithatja a Cloud Shell ablaktáblát a Azure Portalban, és elvégezheti a Cloud Shell munkát.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="A Cloud Shell ikon képe a Azure Portal ikon sávján" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>Végül egy másik megoldás, ha [telepíti az Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) -t a gépére, így helyileg is futtathatja az Azure CLI-parancsokat. A helyi CLI nem tapasztalja ezt a problémát. |


## <a name="missing-role-assignment-after-scripted-setup"></a>A szerepkör-hozzárendelés hiányzik a parancsfájlból történő telepítés után

**Probléma leírása:** Egyes felhasználók problémákat tapasztalhatnak a szerepkör-hozzárendelési részével kapcsolatban [*: példány és hitelesítés beállítása (parancsfájlba*](how-to-set-up-instance-scripted.md)foglalt). A parancsfájl nem jelzi a hibát, de az *Azure Digital Twins-beli adattulajdonosi* szerepkört nem sikerült hozzárendelni a felhasználóhoz, és ez a probléma hatással lehet más erőforrások létrehozására is az úton.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

| Ez hatással van rám? | Ok | Feloldás |
| --- | --- | --- |
| Annak megállapításához, hogy a szerepkör-hozzárendelés sikeresen be lett-e állítva a parancsfájl futtatása után, kövesse a telepítési cikk [*felhasználói szerepkör-hozzárendelés ellenőrzése*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) szakaszának utasításait. Ha a felhasználó nem jelenik meg ezzel a szerepkörrel, ez a probléma hatással van. | A személyes [Microsoft-fiók (MSA)](https://account.microsoft.com/account)szolgáltatásban bejelentkezett felhasználók esetében a felhasználó egyszerű azonosítója, amely a felhasználó bejelentkezési e-mail-címétől eltérő lehet, így megnehezíti a szkript felderítését és használatát a szerepkör megfelelő hozzárendeléséhez. | A megoldáshoz manuálisan is beállíthatja a szerepkör-hozzárendelést a [CLI-utasítások](how-to-set-up-instance-cli.md#set-up-user-access-permissions) vagy a [Azure Portal utasítások](how-to-set-up-instance-portal.md#set-up-user-access-permissions)használatával. |

## <a name="issue-with-interactive-browser-authentication"></a>Probléma az interaktív böngésző-hitelesítéssel

**Probléma leírása:** Az Azure-beli digitális Twins-alkalmazásokban az **[Azure. Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) Library** **1.2.0** használatával történő írás során problémák merülhetnek fel a [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) metódussal. Ez az "Azure. Identity. AuthenticationFailedException" hibaüzenetként jelenik meg, amikor egy böngészőablakban próbál hitelesíteni. Előfordulhat, hogy a böngészőablak nem indul el teljesen, vagy úgy tűnik, hogy sikeresen hitelesíti a felhasználót, míg az ügyfélalkalmazás továbbra is a hibával meghiúsul.

| Ez hatással van rám? | Ok | Feloldás |
| --- | --- | --- |
| Az &nbsp; érintett &nbsp; módszert &nbsp; a &nbsp; &nbsp; &nbsp; &nbsp; következő cikkek használják:<br><br>[*Oktatóanyag: ügyfélalkalmazás kódolása*](tutorial-code.md)<br><br>[*Útmutató: az alkalmazás-hitelesítési kód írása*](how-to-authenticate-client.md)<br><br>[*Útmutató: az Azure Digital Twins API-k és SDK-k használata*](how-to-use-apis-sdks.md) | Egyes felhasználók ezt a problémát a könyvtár verziójának **1.2.0** `Azure.Identity` . | A megoldásához frissítse alkalmazásait a [legújabb verziójának](https://www.nuget.org/packages/Azure.Identity) használatára `Azure.Identity` . A könyvtár verziójának frissítése után a böngészőnek be kell töltenie és hitelesítenie kell a várt módon. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure Digital Twins biztonságáról és engedélyeiről:
* [*Fogalmak: az Azure Digital Twins-megoldások biztonsága*](concepts-security.md)