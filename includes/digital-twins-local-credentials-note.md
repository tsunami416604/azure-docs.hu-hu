---
author: baanders
description: DefaultAzureCredential-fájl belefoglalása az Azure Digital Twins-mintákba – Megjegyzés
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35996595158994308a78f4d1197dcdc8f00af618
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494498"
---
>[!NOTE]
> Ez a példa a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (a könyvtár részét képező `Azure.Identity` ) használatával hitelesíti a felhasználókat az Azure Digital Twins-példánnyal a helyi gépen való futtatásakor. Ilyen típusú hitelesítés esetén a minta az Azure-beli hitelesítő adatokat fogja keresni a helyi környezetben, például egy helyi [Azure parancssori](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) felületről vagy a Visual Studio/Visual Studio Code-ban.
>
> További információ a használatáról `DefaultAzureCredential` és egyéb hitelesítési lehetőségekről [*: útmutató: az alkalmazás-hitelesítési kód írása*](../articles/digital-twins/how-to-authenticate-client.md).