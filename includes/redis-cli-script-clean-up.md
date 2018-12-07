---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: b0b188e10fe0893734185ced186e3ce24dfd0d21
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019717"
---
## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után az alábbi parancs segítségével távolítsa el az erőforráscsoport az erőforráscsoport, az Azure Azure Cache Redis-példány és a kapcsolódó erőforrásokat.

```azurecli
az group delete --name contosoGroup
```