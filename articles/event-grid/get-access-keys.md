---
title: Event Grid erőforrás elérési kulcsának lekérése
description: Ez a cikk egy Event Grid témakör vagy tartomány elérési kulcsának lekérését ismerteti
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: e5694fe0b5f22f7f76285c344627005ea727ae3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86105863"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Hozzáférési kulcsok beolvasása Event Grid erőforrásokhoz (témakörök vagy tartományok)
A hozzáférési kulcsok használatával hitelesítheti az alkalmazás közzétételi eseményeit Azure Event Grid erőforrásokra (témakörökre és tartományokra). Javasoljuk, hogy rendszeresen újragenerálja a kulcsokat, és biztonságosan tárolja őket. A szolgáltatás két hozzáférési kulccsal rendelkezik, így az egyik kulccsal kezelheti a kapcsolatokat, miközben újragenerálja a másikat.

Ez a cikk azt ismerteti, hogyan lehet hozzáférési kulcsokat beolvasni egy Event Grid erőforráshoz (témakörhöz vagy tartományhoz) a Azure Portal, a PowerShell vagy a parancssori felület használatával. 

## <a name="azure-portal"></a>Azure Portal
A Azure Portal váltson a témakör vagy tartomány **Event Grid témakör** vagy **Event Grid tartomány** lap **hozzáférési kulcsok** lapjára.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Hozzáférési kulcsok lapja":::

## <a name="azure-powershell"></a>Azure PowerShell
A [Get-AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey?view=azps-4.3.0) paranccsal elérheti a témakörök elérési kulcsait. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Használja a [Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey?view=azps-4.3.0) parancsot a tartományok hozzáférési kulcsainak lekéréséhez. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
A témakörök elérési kulcsainak megtekintéséhez használja az az [eventgrid témakört](/cli/azure/eventgrid/topic/key?view=azure-cli-latest#az-eventgrid-topic-key-list) . 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Az [az eventgrid domain Key List](/cli/azure/eventgrid/domain/key?view=azure-cli-latest#az-eventgrid-domain-key-list) paranccsal érheti el a tartományok hozzáférési kulcsait. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikket: [közzétételi ügyfelek hitelesítése](security-authenticate-publishing-clients.md). 
