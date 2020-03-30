---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67178920"
---
Ez a minta magában foglalja a [Twilio](https://www.twilio.com/) szolgáltatás segítségével SMS-üzenetek küldése egy mobiltelefonra. Az Azure Functions már támogatja a Twilio a [Twilio-kötés,](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio)és a minta használja ezt a funkciót.

Az első dolog, amire szüksége van egy Twilio-fiók. Létrehozhat egyet ingyenesen https://www.twilio.com/try-twilioa. Miután rendelkezik fiókkal, adja hozzá a következő három **alkalmazásbeállítást** a függvényalkalmazáshoz.

| Alkalmazásbeállítás neve | Érték leírása |
| - | - |
| **TwilioAccountSid**  | A Twilio-fiók sid-je |
| **TwilioAuthToken**   | A Twilio-fiók hitelesítési tokenje |
| **TwilioPhoneNumber** | A Twilio-fiókhoz társított telefonszám. Ezzel sms-üzenetek küldésére szolgál. |