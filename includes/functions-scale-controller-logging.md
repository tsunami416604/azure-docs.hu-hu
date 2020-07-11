---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 49818cf59da2d63cef4bb0bdca38d38a2feafca5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86169908"
---
| | |
|--|--|
|**`<DESTINATION>`**| Az a cél, ahová a rendszer elküldi a naplókat. Az érvényes értékek: `AppInsights` és `Blob` .<br/>A használatakor `AppInsights` Győződjön meg arról, hogy [a Application Insights engedélyezve van a Function alkalmazásban](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration).<br/>A célhely beállításakor a `Blob` rendszer a naplókat az `azure-functions-scale-controller` Alkalmazásbeállítások alapértelmezett Storage-fiókjában nevű blob-tárolóban hozza létre `AzureWebJobsStorage` . |
|**`<VERBOSITY>`** | Meghatározza a naplózási szintet. A támogatott értékek:, `None` `Warning` és `Verbose` .<br/>Ha a értékre `Verbose` van állítva, a skálázási vezérlő a munkavégzők számának minden változása miatt naplózza az okot, valamint az ezekkel a döntésekkel kapcsolatos eseményindítókat is. A részletes naplók közé tartoznak az eseményindító-figyelmeztetések és az eseményindítók által a skálázási vezérlő futtatása előtt és után használt kivonatok. |

> [!CAUTION]
> Ne hagyja engedélyezve a méretezési vezérlő naplózását. Engedélyezze a naplózást, amíg nem gyűjtött elég adatokat, hogy megtudja, hogyan viselkedik a méretezési vezérlő, majd tiltsa le.