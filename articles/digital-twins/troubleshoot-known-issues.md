---
title: Ismert problémák – Azure digitális Twins
description: Segítséget kaphat az Azure digitális Twins ismert problémáinak felismeréséhez és enyhítéséhez.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8fce451d9b806d2fa9a4f3d9e1c117de0aaa9fc0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531733"
---
# <a name="known-issues-in-azure-digital-twins"></a>Az Azure Digital Twins ismert problémái

Ez a cikk az Azure Digital Twins szolgáltatással kapcsolatos ismert problémákról nyújt információkat.

## <a name="managing-event-routes-in-the-azure-portal"></a>Az események útvonalának kezelése a Azure Portalban

Ha a portálon személyes [**Microsoft-fiók (MSA)**](https://account.microsoft.com/account/Account)jelentkezett be, például egy fiókkal, megjelenik egy képernyő, amely azt jelzi, *@outlook.com* hogy engedélyt kell kapnia az esemény- *útvonalak megtekintésére* , amikor az esemény-útvonalakat a portálon szeretné kezelni, a jogosultsági szinttől függetlenül.

:::image type="content" source="media/troubleshoot-known-issues/event-route-need-permission.png" alt-text="Képernyőkép az engedélyezési hiba Azure Portaláról, amikor az Azure Digital Twins-példányon próbál esemény-útvonalakat létrehozni":::

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Azok a felhasználók, akik jelenleg nem tudják kezelni az események útvonalait a portálon, továbbra is kezelhetik az események útvonalait az Azure Digital Twins API-kkal vagy a parancssori felülettel A probléma megoldásához váltson az egyik eszközre az esemény-útválasztási felügyelethez.

Az erre vonatkozó utasítások a következő [*útmutatóban találhatók: végpontok és útvonalak kezelése*](how-to-manage-routes.md).

### <a name="possible-causes"></a>Lehetséges okok

Személyes [Microsoft-fiók (MSA)](https://account.microsoft.com/account/Account), például egy fiókkal jelentkezett be a portálra *@outlook.com* . Az esemény-útvonalak kezelése a Azure Portal jelenleg csak az Azure-felhasználók számára érhető el a vállalati-tartományi fiókokban.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400-ügyfél hiba: helytelen kérelem" a Cloud Shell

A Cloud Shell parancsai időnként sikertelenek lehetnek a következő hibával: "400-es ügyfél-hiba: hibás kérelem az URL-címhez: http://localhost:50342/oauth2/token ", amelyet a teljes verem nyomkövetése követ.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Ez a parancs újbóli futtatásával és a `az login` következő bejelentkezési lépések végrehajtásával oldható fel.

Ezt követően újra kell futtatnia a parancsot.

### <a name="possible-causes"></a>Lehetséges okok

Ez az Cloud Shell ismert problémájának eredménye: a [*token Lekérése Cloud Shell időszakosan meghiúsul az 400-es ügyféllel kapcsolatos hiba miatt: hibás kérelem*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="next-steps"></a>Következő lépések

További információ az Azure Digital Twins biztonságáról és engedélyeiről:
* [*Fogalmak: az Azure Digital Twins-megoldások biztonsága*](concepts-security.md)