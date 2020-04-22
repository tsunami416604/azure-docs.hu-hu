---
title: Közös interfészek – IoT Plug and Play Preview | Microsoft dokumentumok
description: Az IoT Plug and Play fejlesztők közös felületeinek leírása
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5773ec2b3ea88fa9a507b7c1b0b84bb7ea305a94
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770490"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>Az IoT Plug and Play előzetes verzió közös interfészei

Az összes IoT Plug and Play eszköznek bizonyos közös felületeket kell megvalósítania. A gyakori felületek az IoT-megoldások számára előnyösek, mivel konzisztens funkcionalitást biztosítanak. [A hitelesítéshez](tutorial-build-device-certification.md) az eszköznek több közös felületet kell megvalósítania. A nyilvános modelltárházból lekérheti a közös felületdefiníciókat.

## <a name="summary-of-common-interfaces"></a>A közös kapcsolódási pontok összefoglalása

| Name (Név) | ID (Azonosító) | Leírás | Az Azure IoT SDK implementált | A képességmodellben deklarálni kell |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Modell információk | urn:azureiot:ModelDiscovery:Modellinformáció:1 | Az eszközök deklarálják a képességmodell azonosítóját és interfészeit. Minden IoT Plug and Play eszközhöz szükséges. | Igen | Nem |
| Digitális ikerügyfél SDK-információk | urn:azureiot:Ügyfél:SDKInformáció:1 | Ügyfél SDK az eszköz azure-hoz való csatlakoztatásához. A [hitelesítéshez](tutorial-build-device-certification.md) szükséges | Igen | Nem |
| Eszközadatok | urn:azureiot:DeviceManagement:DeviceInformation:1 | Hardver- és operációsrendszer-információk az eszközről. A [hitelesítéshez](tutorial-build-device-certification.md) szükséges | Nem | Igen |
| Modell definíciója | urn:azureiot:ModelDiscovery:Modelldefiníció:1 | Az eszközök számára, hogy deklarálja a teljes definíciót a képességi modell és interfészek. Akkor kell végrehajtani, ha a modelldefiníciók nem egy modelltárházban vannak tárolva. | Nem | Igen |
| Digital Twin | urn:azureiot:ModelDiscovery:DigitalTwin:1 | A megoldás fejlesztők számára, hogy lekérje a képesség modell azonosítóés felület azonosítók egy digitális iker. Ezt a felületet nem deklarálja vagy valósítja meg egy IoT Plug and Play eszköz. | Nem | Nem |

- Az Azure IoT SDK által megvalósított – függetlenül attól, hogy az Azure IoT SDK megvalósítja-e a felületeken deklarált képességeket. Az Azure IoT SDK-t használó IoT Plug and Play eszközöknek nem kell megvalósítaniuk ezt a felületet.
- A képességmodellben deklarálni kell – Ha "igen", `"implements":` ezt az összeköttetést az IoT Plug and Play eszköz eszközképességi modelljének szakaszán belül kell deklarálni.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Illesztőkapcsolatok definícióinak lekérése a nyilvános tárházból

### <a name="cli"></a>parancssori felület

Az Azure CLI Azure IoT-bővítmény használatával lekérheti a közös felületeket a nyilvános modelltárházból.

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS Code

1. A parancspaletta megnyitásához használja a **Ctrl+Shift+P** billentyűkombinációt.

1. Írja be a **Plug and Play parancsot,** majd válassza az **IoT Plug and Play: Open Model Repository parancsot.** Válassza **a Nyilvános tárház lehetőséget.** A nyilvános modell tárház a VS-kódban nyílik meg.

1. A nyilvános modelltárházba írja be a kapcsolat nevét a keresőmezőbe.

1. A felület helyi példányának létrehozásához jelölje ki azt a keresési eredmények között, majd kattintson a **Letöltés gombra.**

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett a gyakori felületekkel, az alábbiakban további forrásokat olvashat:

- [Digitális ikerdefiníciós nyelv (DTDL)](https://aka.ms/DTDL)
- [C eszköz SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
