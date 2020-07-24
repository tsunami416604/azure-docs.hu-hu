---
title: Ismert problémák – Azure digitális Twins
description: Segítséget kaphat az Azure digitális Twins ismert problémáinak felismeréséhez és enyhítéséhez.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: bdde2076039a6f7687e06edef6dfd6f6f5148ce4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044136"
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

## <a name="next-steps"></a>További lépések

További információ az Azure Digital Twins biztonságáról és engedélyeiről:
* [*Fogalmak: az Azure Digital Twins-megoldások biztonsága*](concepts-security.md)