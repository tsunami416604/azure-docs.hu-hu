---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6a5c86dc1b10ed79e5dabbf5be7bf3ee2b677dee
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807688"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A bot Service-végpontnak érvényes HTTPS URI-nak kell lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Átvitel közben az adatokat illetéktelenek is módosíthatják. Léteznek protokollok, amelyek titkosítást biztosítanak a visszaélések és az illetéktelen módosítási problémák megoldása érdekében. Annak biztosítása érdekében, hogy a robotok csak titkosított csatornákon keresztül kommunikáljanak, állítsa a végpontot érvényes HTTPS URI-ra. Ez biztosítja, hogy a HTTPS protokoll az adatátvitel titkosítására szolgáljon, és ez gyakran követelmény a szabályozási vagy iparági szabványoknak való megfeleléshez is. Látogasson el ide: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines) . |naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Services/BotService_ValidEndpoint_Audit.json) |
