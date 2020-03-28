---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ace42278269ff6af31902dbecead81329815af12
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "67179355"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Üzenettéma létrehozása az Azure Portal használatával
1. A **Service Bus Névtér** lapon válassza a bal oldali menü **Témakörök** parancsát.
2. Az eszköztáron válassza a **+ Témakör** lehetőséget. 
4. Adja meg a témakör **nevét.** A többi beállítást hagyja az alapértelmezett értékükön.
5. Kattintson a **Létrehozás** gombra.

    ![Témakör létrehozása](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Előfizetések létrehozása a témához
1. Jelölje ki az előző szakaszban létrehozott **témakört.** 
    
    ![Téma kiválasztása](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. A **Service Bus Topic (Szervizbusz-témakör)** lapon válassza a bal oldali menü **Előfizetések** parancsát, majd válassza a **+ Előfizetés** lehetőséget az eszköztáron. 
    
    ![Előfizetés hozzáadása gomb](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Az **Előfizetés létrehozása** lapon adja meg az **S1** értéket az előfizetés **nevéhez,** majd válassza a **Létrehozás gombot.** 

    ![Előfizetési lap létrehozása](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Ismételje meg kétszer az előző lépést az **S2** és **S3**nevű előfizetések létrehozásához.