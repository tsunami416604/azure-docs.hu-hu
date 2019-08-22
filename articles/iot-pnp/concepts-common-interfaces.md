---
title: Általános felületek – IoT Plug and Play előzetes verzió | Microsoft Docs
description: A IoT Plug and Play-fejlesztőknek készült általános interfészek leírása
author: ChrisGMsft
ms.author: chrisgre
ms.date: 07/16/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 848210509bf9ab0ffec35004cbb07e39d6de1bc0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879603"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT Plug and Play előzetes verzió általános felületek

Minden IoT-Plug and Play eszköznek várhatóan végre kell hajtania néhány gyakori felületet. A közös interfészek előnyben IoT a megoldásokat, mivel konzisztens funkciókat biztosítanak. A [minősítés](tutorial-build-device-certification.md) megköveteli, hogy az eszköz több közös felületet implementáljon. A Common Interface-definíciókat a globális modell tárházból kérheti le.

## <a name="summary-of-common-interfaces"></a>Általános felületek összefoglalása

| Name (Név) | id | Leírás | Az Azure IoT SDK implementálja | Be kell jelenteni a képesség modellben |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Modell adatai | urn: azureiot: ModelDiscovery: ModelInformation: 1 | Az eszközök számára, hogy deklarálják a képesség modell AZONOSÍTÓját és felületeit. Minden IoT Plug and Play eszközhöz szükséges. | Igen | Nem |
| Digitális Twin Client SDK-információk | urn: azureiot: ügyfél: SDKInformation: 1 | Az eszközt az Azure-hoz csatlakoztató ügyfél-SDK. A [minősítéshez](tutorial-build-device-certification.md) szükséges | Igen | Nem |
| Eszköz adatai | urn: azureiot: DeviceManagement: DeviceInformation: 1 | Hardver-és operációsrendszer-információk az eszközről. A [minősítéshez](tutorial-build-device-certification.md) szükséges | Nem | Igen |
| Modell definíciója | urn: azureiot: ModelDiscovery: ModelDefinition: 1 | Az eszközök számára, hogy deklarálják a képességeinek modelljét és felületét teljes definícióját. Akkor kell megvalósítani, ha a modell-definíciók nem találhatók meg a modell-tárházban. | Nem | Igen |
| Digitális Twin | urn: azureiot: ModelDiscovery: DigitalTwin: 1 | A megoldás fejlesztői számára a Digital Twin modell AZONOSÍTÓjának és illesztőfelület-azonosítóinak beolvasása. Ez az illesztőfelület nincs deklarálva vagy implementálva egy IoT Plug and Play eszközön. | Nem | Nem |

- Az Azure IoT SDK által implementálva – azt határozza meg, hogy az Azure IoT SDK implementálja-e az illesztőfelületekben deklarált képességeket. Az Azure IoT SDK-t használó eszközök Plug and Play IoT nem kell megvalósítani ezt a felületet.
- Szerepelnie kell a (z) képesség-modellben – ha az igen, akkor ezt a `"implements":` felületet a IoT Plug and Play eszközön található eszköz-képesség modell szakaszában kell deklarálni.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Illesztőfelület-definíciók beolvasása a nyilvános tárházból

### <a name="cli"></a>parancssori felület

Az Azure IoT-bővítmény az Azure CLI-hez az általános felületek a globális modell tárházból való lekéréséhez használható.

```cmd/sh
az iot pnp interface show --interface {InterfaceID} --login {ModelRepoConnectionString}
```

```cmd/sh
az iot pnp model show --interface {InterfaceID} --login {ModelRepoConnectionString}
```

### <a name="vs-code"></a>VS Code

1. A Command paletta megnyitásához használja a **CTRL + SHIFT + P** billentyűkombinációt.

1. Adja meg **Plug and Play** , majd válassza **ki a IoT Plug and Play: Nyissa meg** a Model repository parancsot. Válassza a **globális modell tárház**lehetőséget. Megnyílik a Global Model adattár a VS Code-ban.

1. A globális modell adattárában adja meg a kapcsolat nevét a keresőmezőbe.

1. Az interfész helyi másolatának létrehozásához válassza ki azt a keresési eredmények között, majd válassza a **Letöltés**lehetőséget.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az általános interfészeket, néhány további erőforrást is talál:

- [Digital Twin Definition Language (DTDL)](https://aka.ms/DTDL)
- [C eszköz-SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
