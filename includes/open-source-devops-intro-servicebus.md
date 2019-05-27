---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66142183"
---
[Az Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) vállalat [integrációs](https://azure.microsoft.com/product-categories/integration/) közvetítő. A Service bus a kommunikációt két típusát támogatja: üzenetsorokat és témaköröket. 

Üzenetsorok aszinkron típusú kommunikációs alkalmazások közötti támogatja. Egy alkalmazás üzenetet küld egy üzenetsorba, amely tárolja az üzeneteket. A fogadó alkalmazásnak ezután csatlakozik, és beolvassa az üzeneteket az üzenetsorból.

Témakörök támogatják a közzétételi-feliratkozási mintát, amely lehetővé teszi, hogy az üzenet létrehozója és a Üzenő receiver(s) között egy-a-többhöz kapcsolat.