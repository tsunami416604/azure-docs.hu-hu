---
title: Névtér és üzenetsor létrehozása Azure Service Bus Azure-sablon használatával
description: 'Gyors útmutató: Service Bus névtér és üzenetsor létrehozása Azure Resource Manager sablon használatával'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/30/2020
ms.author: spelluru
ms.openlocfilehash: b08253104eeb61f6bb09fde507473d235a996494
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80422616"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Gyors útmutató: Service Bus névtér és üzenetsor létrehozása Azure Resource Manager sablon használatával

Ez a cikk bemutatja, hogyan használható egy Azure Resource Manager-sablon, amely létrehoz egy Service Bus névteret és egy, az adott névtéren belüli várólistát. A cikk azt ismerteti, hogyan határozható meg, hogy mely erőforrások legyenek telepítve, és Hogyan határozható meg a központi telepítés végrehajtásakor megadott paraméterek. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

None

## <a name="create-a-service-bus-namespace-and-a-queue"></a>Service Bus névtér és üzenetsor létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue)származik.

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json" range="1-75" highlight="31-63":::

A sablonban definiált erőforrások a következők:

- [**Microsoft. ServiceBus/névterek**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft. ServiceBus/névterek/várólisták**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> A következő Azure Resource Manager sablonok tölthetők le és üzemelő példányban.
>
> * [Service Bus névtér létrehozása a várólista-és engedélyezési szabállyal](service-bus-resource-manager-namespace-auth-rule.md)
> * [Service Bus névtér létrehozása témakörrel és előfizetéssel](service-bus-resource-manager-namespace-topic.md)
> * [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [Service Bus névtér létrehozása témakörrel, előfizetéssel és szabállyal](service-bus-resource-manager-namespace-topic-with-rule.md)

További sablont az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular) talál

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Ezzel a sablonnal egy Service Bus névteret helyez üzembe egy várólistával.

[Service Bus Queues](service-bus-queues-topics-subscriptions.md#queues) először a, az első kimenő (FIFO) üzenet kézbesítését kínálja egy vagy több versenytárs fogyasztónak.

Ha a központi telepítést automatikusan szeretné futtatni, kattintson a következő gombra: hozzon létre egy új erőforráscsoportot a központi telepítéshez, hogy később könnyen lehessen megtisztítani.

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

1. A központi telepítés állapotának megtekintéséhez válassza a felül található **értesítések** lehetőséget. Várjon, amíg az üzembe helyezés befejeződik. Ezután válassza az **Ugrás az erőforráscsoporthoz** lehetőséget az értesítési üzenetben, hogy megnyissa az Service Bus névteret tartalmazó erőforráscsoport lapját. 

    ![Értesítés az üzembe helyezésről](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Ellenőrizze, hogy megjelenik-e a Service Bus névtere az erőforrások listájában. 

    ![Erőforráscsoport – névtér](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. A **Service Bus névtér** lap megjelenítéséhez válassza ki a névteret a listából. 

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

1. A Azure Portal navigáljon az **erőforráscsoport oldalához** .
2. Az eszköztáron kattintson az **Erőforráscsoport törlése** gombra. 
3. Írja be az erőforráscsoport nevét, és válassza a **Törlés**lehetőséget. 

    ![Erőforráscsoport – törlés](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>További lépések

Tekintse meg a következő témakört, amely bemutatja, hogyan hozhat létre egy engedélyezési szabályt a névtérhez vagy a várólistához:

[Service Bus engedélyezési szabály létrehozása a névtérhez és a várólistához Azure Resource Manager sablon használatával](service-bus-resource-manager-namespace-auth-rule.md)

Ismerje meg, hogyan kezelheti ezeket az erőforrásokat a következő cikkek megtekintésével:

* [A Service Bus kezelése a PowerShell használatával](service-bus-manage-with-ps.md)
* [Service Bus erőforrások kezelése a Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
