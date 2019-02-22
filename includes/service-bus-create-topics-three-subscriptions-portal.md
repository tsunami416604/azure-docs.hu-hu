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
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588670"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Üzenettéma létrehozása az Azure Portal használatával
1. Az a **Service Bus-Namespace** lapon jelölje be **témakörök** a bal oldali menüben.
2. Válassza ki **+ témakör** az eszköztáron. 
4. Adjon meg egy **neve** a témakörben. A többi beállítást hagyja az alapértelmezett értékükön.
5. Kattintson a **Létrehozás** gombra.

    ![Témakör létrehozása](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>A témakör az előfizetések létrehozása
1. Válassza ki a **témakör** az előző szakaszban létrehozott. 
    
    ![Válassza ki a témakör](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. Az a **Service Bus-témakör** lapon jelölje be **előfizetések** a bal oldali menüben, és válassza ki a **+ előfizetés** az eszköztáron. 
    
    ![Előfizetés gomb hozzáadása](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Az a **előfizetés létrehozása** lap, adja meg **S1** a **neve** az előfizetést, és válassza ki a **létrehozás**. 

    ![Előfizetés-lap létrehozása](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Ismételje meg az előző lépésben kétszer-előfizetéseket hozhasson létre nevű **S2** és **S3**.