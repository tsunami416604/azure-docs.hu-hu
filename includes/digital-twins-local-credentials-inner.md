---
author: baanders
description: fájl belefoglalása a DefaultAzureCredential helyi hitelesítésének beállításához az Azure Digital Twins-mintákban – bevezetés nélkül
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 3cb053f9673532ac19e2098678ec341f2f676486
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011258"
---
`DefaultAzureCredential`A-ben a minta a helyi környezetben hitelesítő adatokat keres, például egy Azure-bejelentkezést egy helyi [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) -ben, illetve a Visual Studióban vagy a Visual Studio Code-ban. Ebből kifolyólag *helyileg kell bejelentkeznie az Azure* -ba ezen mechanizmusok egyikével a minta hitelesítő adatainak beállításához.

Ha a Visual studiót vagy a Visual Studio Code-ot használja a kód futtatásához, győződjön meg arról, hogy a szerkesztőbe bejelentkezett ugyanazzal az Azure-beli hitelesítő adatokkal, amelyeket használni szeretne az Azure Digital Twins-példányhoz való hozzáféréshez.

Ellenkező esetben [telepítheti a helyi Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)-t, elindíthat egy parancssort a gépen, és futtathatja a `az login` parancsot az Azure-fiókba való bejelentkezéshez. A bejelentkezést követően a kód mintájának futtatásakor automatikusan be kell jelentkeznie.