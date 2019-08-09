---
title: Azure IoT Device SDK-platform támogatása | Microsoft Docs
description: Fogalmak – az Azure IoT Device SDK-k által támogatott platformok listája
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: robinsh
ms.openlocfilehash: 356962c76bf942c56ace0798ac3cca3d815bc507
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883107"
---
# <a name="azure-iot-sdks-platform-support"></a>Azure IoT SDK-platform támogatása

Az [Azure IoT SDK](iot-hub-devguide-sdks.md) -k olyan kódtárak, amelyekkel a IoT hub és az eszközök kiépítési szolgáltatása széles körű nyelvi és platform-támogatást használhat. Az SDK-k a leggyakoribb platformokon futnak, és a fejlesztők a [Porting útmutatót](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)követve a C SDK-t egy adott platformra is elérhetik. 

A Microsoft számos operációs rendszert/platformot/keretrendszert támogat, és az Azure IoT C SDK-val bővíthető. Néhányat a csapat hivatalosan támogat, és olyan rétegekbe vannak csoportosítva, amelyek a támogatási felhasználók által elvárt szintet jelölik. A *teljes mértékben támogatott platformok* azt jelentik, hogy a Microsoft:

- Folyamatosan épít és futtat teljes körű vizsgálatokat a Master és az LTS által támogatott verzió (k) alapján.  A tesztelési lefedettség különböző verziókban való biztosításához általában a legújabb LTS-verziót és a legnépszerűbb verziót teszteljük.  Az azonos platform más verziói a platform verziójának kompatibilitása révén is támogatottak.
- Telepítési útmutatót vagy csomagokat biztosít, ha vannak ilyenek.
- Teljes mértékben támogatja a-platformokat a GitHubon.

Emellett a partnerek listája a C SDK-t több platformra is elkészítette, és megtartja a platform absztrakciós rétegét (PAL). Az [IoT-eszközök katalógusának Azure Certified minősítése](https://catalog.azureiotsolutions.com/) a különböző SDK-k által tesztelt operációsrendszer-platformok listáját is tartalmazza. Az SDK-k emellett rendszeresen építik ezeket a platformokat korlátozott teszteléssel és támogatással:

* MBED2
* Arduino
* Windows CE 2013 (a 2018. októberi elavult)
* .NET Standard 1,3 .net Core 2,1 és .NET Framework 4,7 használatával
* Xamarin iOS, Android, UWP

## <a name="supported-platforms"></a>Támogatott platformok

Több platform is támogatott.

### <a name="c-sdk"></a>C SDK

| OS                  | Arch | Fordítóprogram             | TLS-könyvtár       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc-5.4.0            | OpenSSL – 1.0.2 g |
| Ubuntu 18,04 LTS    | X64  | GCC – 7,3              | WolfSSL – 1,13    |
| Ubuntu 18,04 LTS    | X64  | Csenget 6.0. X          | OpenSSL – 1.1.0 g  |
| OSX-10.13.4         | x64  | XCode 9.4.1          | Natív OSX        |
| Windows Server 2016 | x64  | Visual Studio 14.0. X | SChannel          |
| Windows Server 2016 | x86  | Visual Studio 14.0. X | SChannel          |
| Debian 9 stretch    | x64  | GCC – 7,3              | OpenSSL – 1.1.0 f  |

### <a name="python-sdk"></a>Python SDK

| OS                  | Arch | Fordítóprogram   | TLS-könyvtár |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2.7 | OpenSSL     |
| Windows Server 2016 | x64  | Python 2.7 | OpenSSL     |
| Windows Server 2016 | x86  | Python 3,5 | OpenSSL     |
| Windows Server 2016 | x64  | Python 3,5 | OpenSSL     |
| Ubuntu 18,04 LTS    | x86  | Python 2.7 | OpenSSL     |
| Ubuntu 18,04 LTS    | x86  | Python 3,4 | OpenSSL     |
| MacOS magas Sierra   | x64  | Python 2.7 | OpenSSL     |

### <a name="net-sdk"></a>.NET SDK

| OS                  | Arch | Keretében            | Standard          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET Core 2.1        | .NET Standard 2,0 |
| Windows Server 2016 | X64  | .NET Core 2.1        | .NET Standard 2,0 |
| Windows Server 2016 | X64  | .NET-keretrendszer 4,7   | .NET Standard 2,0 |
| Windows Server 2016 | X64  | .NET-keretrendszer 4.5.1 | –               |

### <a name="nodejs-sdk"></a>Node.js SDK

| OS                                           | Arch | Csomópont verziója |
|----------------------------------------------|------|--------------|
| Ubuntu 16,04 LTS (a 6-os csomóponttal rendelkező Docker-rendszerkép használatával) | X64  | 6\. csomópont       |
| Windows Server 2016                          | X64  | 6\. csomópont       |

### <a name="java-sdk"></a>Java SDK

| OS                  | Arch | Java-verzió |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |
| Android API 28 | X64  | Java 8       |
| Android-dolgok | X64  | Java 8      |

## <a name="partner-supported-platforms"></a>Partner által támogatott platformok

Az ügyfelek kiterjeszthetik a platform támogatását az Azure IoT C SDK-val, pontosabban létrehozva az SDK platform absztrakciós rétegét (PAL). A Microsoft együttműködik a partnerekkel a kiterjesztett támogatás biztosításához. A partnerek listája több platformra portolta a C SDK-t, és megtartja a PAL-t.

| Partner             | Eszközök                            | Összekapcsolás                     | Támogatás |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHubon](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT modem     | [Qualcomm LTE a IoT SDK-hoz](https://developer.qualcomm.com/software/lte-iot-sdk) | [Fórum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST MICROELECTRONICS | STM32L4 sorozat <br/> STM32F4 sorozat <br/>  STM32F7 sorozat <br/>  A IoT-csomópont STM32L4-felderítési készlete    | [X-CUBE-CLOUD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html) <br/> [X-CUBE-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html) <br/> [P-NUCLEO – AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Támogatás](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF Kezdőpanel <br/> CC3220S Kezdőpanel <br/> MSP432E4 Kezdőpanel      | [A SimpleLink készült Azure IoT beépülő modul](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E fórum](https://e2e.ti.com) <br/> [TI E2E fórum CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [TI E2E fórum MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="next-steps"></a>További lépések

* [Eszköz- és szolgáltatásspecifikus SDK-k](iot-hub-devguide-sdks.md)
* [A Porting útmutatója](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
