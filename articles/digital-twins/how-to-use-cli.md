---
title: Az Azure digitális Twins parancssori felületének használata
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan kezdheti el az első lépéseket, és hogyan használhatja az Azure digitális Twins parancssori felületét.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 53b20ded8e4b4a003beff1ef8489ecd9ff3451ac
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612802"
---
# <a name="use-the-azure-digital-twins-cli"></a>Az Azure digitális Twins parancssori felületének használata

A Azure Portal Azure Digital Twins-példányának kezelése mellett az Azure Digital Twins egy **parancssori felülettel (CLI)** is rendelkezik, amely a szolgáltatással kapcsolatos legfontosabb műveletek végrehajtásához használható, beleértve a következőket:
* Azure digitális Twins-példány kezelése
* Modellek kezelése
* Digitális ikrek kezelése
* Kettős kapcsolatok kezelése
* Végpontok konfigurálása
* [Útvonalak](concepts-route-events.md) kezelése
* [Biztonság](concepts-security.md) konfigurálása szerepköralapú hozzáférés-vezérléssel (RBAC)

Az Azure Digital Twins-parancsok az Azure [CLI Azure IoT-bővítményének](https://github.com/Azure/azure-iot-cli-extension)részét képezik. A parancsok dokumentációját a következő parancs részeként tekintheti meg `az iot` : az [DT](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest).

## <a name="deploy-and-validate"></a>Üzembe helyezés és ellenőrzés

A példányok általános kezelése mellett a CLI is hasznos eszköz az üzembe helyezéshez és az érvényesítéshez.
* A vezérlési sík parancsaival az új példányok üzembe helyezése ismételhető vagy automatizált lehet.
* Az adatsík parancsai segítségével gyorsan ellenőrizheti a példányban lévő értékeket, és ellenőrizheti, hogy a műveletek a várt módon fejeződött-e be.

## <a name="next-steps"></a>Következő lépések

A CLI-parancsok alternatívájaként lásd: Azure digitális Twins-példányok kezelése API-k és SDK-k használatával:
* [Útmutató: az Azure Digital Twins API-k és SDK-k használata](how-to-use-apis-sdks.md)
