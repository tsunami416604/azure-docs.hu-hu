---
title: Az Azure Service Bus névtere és várólistájának létrehozása az Azure-sablon használatával
description: 'Rövid útmutató: Hozzon létre egy Service Bus-névteret és egy várólistát az Azure Resource Manager-sablonnal'
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
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422616"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Rövid útmutató: Hozzon létre egy Service Bus-névteret és egy várólistát egy Azure Resource Manager-sablon használatával

Ez a cikk bemutatja, hogyan használhatja az Azure Resource Manager sablont, amely létrehoz egy Service Bus névtér és egy várólista a névtérben. A cikk bemutatja, hogyan adja meg, hogy mely erőforrások vannak telepítve, és hogyan határozhatja meg a központi telepítés végrehajtásakor megadott paramétereket. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

None

## <a name="create-a-service-bus-namespace-and-a-queue"></a>Service Bus-névtér és várólista létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik.](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue)

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json" range="1-75" highlight="31-63":::

A sablonban meghatározott erőforrások a következők:

- [**Microsoft.ServiceBus/névterek**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/névterek/várólisták**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> A következő Azure Resource Manager-sablonok tölthetők le és telepíthetik.
>
> * [Service Bus-névtér létrehozása várólista- és engedélyezési szabállyal](service-bus-resource-manager-namespace-auth-rule.md)
> * [Service Bus-névtér létrehozása témakörrel és előfizetéssel](service-bus-resource-manager-namespace-topic.md)
> * [Service Bus-névtér létrehozása](service-bus-resource-manager-namespace.md)
> * [Service Bus-névtér létrehozása témakörrel, előfizetéssel és szabállyal](service-bus-resource-manager-namespace-topic-with-rule.md)

További sablonokat az [Azure gyorsindítási sablonjaiból](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular) talál.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Ezzel a sablonnal üzembe helyezhet egy Service Bus-névteret várólistával.

[A Service Bus-várólisták](service-bus-queues-topics-subscriptions.md#queues) első be, első ki (FIFO) üzenetkézbesítést kínálnak egy vagy több versenytárs fogyasztónak.

A központi telepítés automatikus futtatásához kattintson a következő gombra: Hozzon létre egy új erőforráscsoportot a központi telepítéshez, hogy később könnyen letakaríthassa.

[![Üzembe helyezés az Azure-ban](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

1. Válassza **az értesítések** a tetején, hogy az állapotát a központi telepítés. Várjon, amíg a telepítés sikeres lesz. Ezután válassza az Értesítési üzenet **Ugrás erőforráscsoportra** lehetőséget a Service Bus-névteret tartalmazó erőforráscsoport lapjának megugrásához. 

    ![Értesítés a központi telepítésről](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Ellenőrizze, hogy a Service Bus névtere az erőforrások listájában. 

    ![Erőforráscsoport - névtér](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Válassza ki a névteret a listából a **Service Bus névtér** lap megtekintéséhez. 

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

1. Az Azure Portalon keresse meg az **erőforráscsoport erőforráscsoport** lapját.
2. Az eszköztáron kattintson az **Erőforráscsoport törlése** gombra. 
3. Írja be az erőforráscsoport nevét, és válassza a **Törlés gombot.** 

    ![Erőforráscsoport - törlés](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>További lépések

A névtérhez/várólista-hoz engedélyezési szabály létrehozásáról az alábbi témakörben olvashat:

[Service Bus engedélyezési szabály létrehozása névtérhez és várólistához egy Azure Resource Manager-sablon használatával](service-bus-resource-manager-namespace-auth-rule.md)

Ebből a cikkből megtudhatja, hogyan kezelheti ezeket az erőforrásokat:

* [A Service Bus kezelése a PowerShell használatával](service-bus-manage-with-ps.md)
* [A Service Bus-erőforrások kezelése a Service Bus Intézővel](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
