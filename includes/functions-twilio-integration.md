---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "67178920"
---
Ez a minta a [Twilio](https://www.twilio.com/) szolgáltatás használatával SMS-üzeneteket küld a mobiltelefonjára. Azure Functions már támogatja a Twilio-t a [Twilio-kötésen](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio)keresztül, és a minta ezt a szolgáltatást használja.

Az első dolog, amire szüksége van egy Twilio-fiók. Létrehozhat egy ingyenes at https://www.twilio.com/try-twilio . Ha már rendelkezik fiókkal, adja hozzá a következő három **alkalmazás-beállítást** a Function alkalmazáshoz.

| Alkalmazás-beállítás neve | Érték leírása |
| - | - |
| **TwilioAccountSid**  | A Twilio-fiókhoz tartozó SID |
| **TwilioAuthToken**   | A Twilio-fiók hitelesítési jogkivonata |
| **TwilioPhoneNumber** | A Twilio-fiókhoz társított telefonszám. Ez az SMS-üzenetek küldésére szolgál. |