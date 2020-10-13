---
title: Az Azure Digital Twins CLI használata
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan kezdheti el az első lépéseket, és hogyan használhatja az Azure digitális Twins parancssori felületét.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 65462937db4711066bdb8b31cc22de508321d701
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950907"
---
# <a name="use-the-azure-digital-twins-cli"></a>Az Azure Digital Twins CLI használata

A Azure Portal Azure Digital Twins-példányának kezelése mellett az Azure Digital Twins egy **parancssori felülettel (CLI)** is rendelkezik, amely a szolgáltatással kapcsolatos legfontosabb műveletek végrehajtásához használható, beleértve a következőket:
* Azure digitális Twins-példány kezelése
* Modellek kezelése
* Digitális ikrek kezelése
* Kettős kapcsolatok kezelése
* Végpontok konfigurálása
* [Útvonalak](concepts-route-events.md) kezelése
* [Biztonság](concepts-security.md) konfigurálása szerepköralapú hozzáférés-vezérléssel (RBAC)

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>Felhasználás (üzembe helyezés és ellenőrzés)

A példányok általános kezelése mellett a CLI is hasznos eszköz az üzembe helyezéshez és az érvényesítéshez.
* A vezérlési sík parancsaival az új példányok üzembe helyezése ismételhető vagy automatizált lehet.
* Az adatsík parancsai segítségével gyorsan ellenőrizheti a példányban lévő értékeket, és ellenőrizheti, hogy a műveletek a várt módon fejeződött-e be.

## <a name="get-the-extension"></a>Bővítmény beszerzése

Az Azure Digital Twins-parancsok az Azure [CLI Azure IoT-bővítményének](https://github.com/Azure/azure-iot-cli-extension)részét képezik. A parancsok és azok használatának teljes listáját a következő parancs dokumentációjában tekintheti meg `az iot` : az [ *DT* Command Reference](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true).

Az alábbi lépésekkel ellenőrizheti, hogy a bővítmény legújabb verziója van-e telepítve. Ezeket a parancsokat a [Azure Cloud Shell](../cloud-shell/overview.md) vagy egy [helyi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)-ben futtathatja.

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>További lépések

Ismerkedjen meg a parancssori felülettel és a teljes körű parancsaival a dokumentációs dokumentációban:
* [*az DT* Command Reference](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)
