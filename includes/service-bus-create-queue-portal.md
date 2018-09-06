---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ed298fc8f13685c4872c4c54ba1e447debea79f
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702636"
---
Győződjön meg róla, hogy már létrehozott egy Service Bus-névteret az [itt][namespace-how-to] ismertetettek szerint.

1. Jelentkezzen be az [Azure Portalra][azure-portal].
2. A portál bal oldali navigációs panelén kattintson a **Service Bus** (ha a **Service Bus** nem látható, akkor a **Minden szolgáltatás**) elemre.
3. Kattintson a névtérre, amelyben az üzenetsort létre kívánja hozni. Ebben az esetben ez az **sbnstest1**.
   
    ![Üzenetsor létrehozása][createqueue1]
4. A névtér ablakban kattintson az **Üzenetsorok** lehetőségre, majd a **Üzenetsorok** ablakban kattintson a **+ Üzenetsor** lehetőségre.
   
    ![Üzenetsorok kiválasztása][createqueue2]
5. Adjon meg egy nevet az üzenetsor **Név** mezőjében, a többi érték alapértelmezését pedig ne módosítsa.
   
    ![Új kiválasztása][createqueue3]
6. Az ablak alján kattintson a **Létrehozás** elemre.

[createqueue1]: ./media/service-bus-create-queue-portal/create-queue1.png
[createqueue2]: ./media/service-bus-create-queue-portal/create-queue2.png
[createqueue3]: ./media/service-bus-create-queue-portal/create-queue3.png

[namespace-how-to]: ../articles/service-bus-messaging/service-bus-create-namespace-portal.md
[azure-portal]: https://portal.azure.com
