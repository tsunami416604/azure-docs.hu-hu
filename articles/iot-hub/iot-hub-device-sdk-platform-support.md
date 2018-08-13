---
title: Azure IoT eszközoldali SDK-k Eszközplatform-támogatás |} A Microsoft Docs
description: Alapelvei – az Azure IoT eszközoldali SDK-k által támogatott platformok listája
author: yzhong94
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: yizhon
ms.openlocfilehash: cf3c80424c4626b62317bda537f9491cafc8198c
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043877"
---
# <a name="azure-iot-sdks-platform-support"></a>Az Azure IoT SDK-k Eszközplatform-támogatás

[Az Azure IoT SDK-k](iot-hub-devguide-sdks.md) kommunikáljon az IoT Hub és a széles körű nyelvi és eszközplatform-támogatás a Device Provisioning Service-kódtárak vannak.  Leggyakoribb platformokon futtatható az SDK-k és fejlesztők is port az adott platformon C SDK-t a következő a [portolása útmutatást](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md). 

A Microsoft támogatja az operációs rendszerek/platformok és keretrendszerek széles, és az Azure IoT C SDK-t is kiterjeszthető.  Néhány hivatalosan által támogatott a csapat számíthatnak támogatás szintjét képviselő szinten vannak csoportosítva.  Teljes mértékben támogatott platformok azt jelenti, hogy a Microsoft:
    - Folyamatos buildelési és teljes körű második megvizsgál a master és a LTS támogatott verzió(k)
    - Adja meg a telepítővarázslót, vagy a csomagok, ha van ilyen
    - Teljes körűen támogatja a Githubon

Emellett az C SDK be további platformok rendelkezik ültették át a partnerek listáját, és azok vannak karbantartása a platform absztrakciós réteg (PAL).  [Az Azure Certified for IoT Eszközkatalógus](https://catalog.azureiotsolutions.com/) is funkciók OS-platformokat, a különböző SDK-k listáját teszteltük ellen.  Az SDK-k emellett szintén rendszeresen hozhat létre, ezek a rendszerek, a korlátozott tesztelése, és támogatja:
- MBED2
- Arduino
- Windows CE 2013 (2018. október kivezetjük)
- .NET standard 1.3 és 2.1-es verziója a .NET Core és a .NET-keretrendszer 4.7
- Xamarin iOS, Android, az UWP
- Java Android

## <a name="supported-platforms"></a>Támogatott platformok
### <a name="c-sdk"></a>C SDK-T
| Operációs rendszer                  | Arch | Kompilátor jazyka             | A TLS-könyvtár       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc-5.4.0-s verziójával            | openssl - 1.0.2g |
| Ubuntu 18.04 LTS    | X64  | gcc-7.3.              | WolfSSL – 1,13.    |
| Ubuntu 18.04 LTS    | X64  | Clang 6.0.X          | Openssl – 1.1.0g  |
| OSX 10.13.4         | x64  | XCode 9.4.1          | Natív osx-ről        |
| Windows Server 2016 | x64  | A Visual Studio 14.0.X | SChannel          |
| Windows Server 2016 | x86  | A Visual Studio 14.0.X | SChannel          |
| Debian 9 Stretch    | x64  | gcc-7.3.              | Openssl – 1.1.0f  |

### <a name="python-sdk"></a>Python SDK
| Operációs rendszer                  | Arch | Kompilátor jazyka   | A TLS-könyvtár |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2.7-es | openssl     |
| Windows Server 2016 | x64  | Python 2.7-es | openssl     |
| Windows Server 2016 | x86  | Python 3.5 | openssl     |
| Windows Server 2016 | x64  | Python 3.5 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 2.7-es | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 3.4 | openssl     |
| MacOS High Sierra   | x64  | Python 2.7-es | openssl     |

### <a name="net-sdk"></a>.NET SDK
| Operációs rendszer                  | Arch | Keretrendszer            | Standard          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | A .NET core 2.1-es verziója        | A .NET standard 2.0 |
| Windows Server 2016 | X64  | A .NET core 2.1-es verziója        | A .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET-keretrendszer 4.7   | A .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET-keretrendszer 4.5.1 | –               |

### <a name="nodejs-sdk"></a>Node.js SDK
| Operációs rendszer                                           | Arch | Csomópont verziója |
|----------------------------------------------|------|--------------|
| Ubuntu 16.04 LTS (6 csomópont docker-rendszerkép használatával) | X64  | 6. csomópont       |
| Windows Server 2016                          | X64  | 6. csomópont       |

### <a name="java-sdk"></a>Java SDK
| Operációs rendszer                  | Arch | Java-verzió |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |

## <a name="partner-supported-platforms"></a>A partner támogatott platformok
| Partner             | Eszközök                            | Hivatkozás                     | Támogatás |
|---------------------|------------------------------------|--------------------------|---------|
| Qualcomm            | Qualcomm MDM9206 LTE IoT Modem     | [IoT SDK-hoz készült Qualcomm LTE](https://developer.qualcomm.com/software/lte-iot-sdk) | [Fórum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| St. Microelectronics | STM32L4 sorozat STM32F4 sorozat      | [X-ADATKOCKA-FELHŐ](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html)             | [Támogatás](https://www.st.com/content/st_com/en/support/support-home.html) |
|                     | STM32F7 sorozat                     | [AZURE-X-ADATKOCKA](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html)             |         |
|                     | IoT-csomópont STM32L4 felderítési csomag | [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html)          |         |
|                     |                                    | [AZURE-PI-CLD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            |         |
| Espressif           | ESP32                              | [ESP-Azure-hoz](https://github.com/espressif/esp-azure)                | [GitHubon](https://github.com/espressif/esp-azure)  |

## <a name="next-steps"></a>További lépések
- [Eszköz- és szolgáltatásspecifikus SDK-k](iot-hub-devguide-sdks.md)
- [Portolási útmutatót](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)