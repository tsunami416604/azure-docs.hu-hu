---
author: baanders
description: fájl belefoglalása a DefaultAzureCredential helyi hitelesítésének beállításához az Azure Digital Twins-mintákban – bevezetés
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35643370d6fd313d42f954a578b73befc025c040
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329505"
---
### <a name="set-up-local-azure-credentials"></a>Helyi Azure-beli hitelesítő adatok beállítása

Ez a példa a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (a könyvtár részét képező `Azure.Identity` ) használatával hitelesíti a felhasználókat az Azure Digital Twins-példánnyal a helyi gépen való futtatásakor. További információ az ügyfélalkalmazások az Azure Digital Twins szolgáltatással való hitelesítésének különböző módjairól [*: útmutató: az alkalmazás-hitelesítési kód írása*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]