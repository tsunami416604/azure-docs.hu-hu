---
author: baanders
description: fájl belefoglalása a DefaultAzureCredential helyi hitelesítésének beállításához az Azure Digital Twins-mintákban – bevezetés nélkül
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8bf0590b867d37a22706f679bfbeee2140935637
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494703"
---
`DefaultAzureCredential`A-ben a minta a helyi környezetben hitelesítő adatokat keres, például egy Azure-bejelentkezést egy helyi [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) -ben vagy a Visual Studio/Visual Studio Code-ban. Ez azt jelenti, hogy **helyileg kell bejelentkeznie az Azure-ba** ezen mechanizmusok egyikével a minta hitelesítő adatainak beállításához.

Ha a Visual studiót vagy a Visual Studio Code-ot használja a kód futtatásához, győződjön meg arról, hogy a szerkesztőben ugyanazok az Azure-beli hitelesítő adatok vannak bejelentkezve, amelyeket az Azure Digital Twins-példány eléréséhez használni kíván.

Ellenkező esetben [telepítheti a helyi **Azure CLI**](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)-t, elindíthat egy parancssort a gépen, és futtathatja a `az login` parancsot az Azure-fiókba való bejelentkezéshez. Ezt követően a kód mintájának futtatásakor automatikusan be kell jelentkeznie.