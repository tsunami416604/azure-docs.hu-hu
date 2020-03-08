---
title: 'Gyors útmutató: Java-hoz készült Speech SDK (Windows, Linux, macOS) platform beállítása – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval beállíthatja a platformot Java (Windows, Linux, macOS) használatára a Speech Service SDK-val.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 7147f0d13c88c1d2e17e81a360a5aee55ee760ed
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78924714"
---
Ez az útmutató bemutatja, hogyan telepítheti a 64 bites Java 8 JRE [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) -t. Ha csak azt szeretné, hogy a csomag neve megkezdhető legyen a saját számára, a Java SDK nem érhető el a Maven Central adattárában. Akár Gradle, akár `pom.xml` függőségi fájlt használ, hozzá kell adnia egy egyéni tárházat, amely `https://csspeechstorage.blob.core.windows.net/maven/` mutat (lásd alább a csomag nevét).

> [!NOTE]
> A Speech Devices SDK-ról és a Roobo eszközről lásd: [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

- A Java Speech SDK csomag elérhető a következő operációs rendszerekhez:
  - Windows: 64 – csak bit
  - Mac: macOS X 10,13 vagy újabb verzió
  - Linux: 64-bit csak Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8

## <a name="prerequisites"></a>Előfeltételek

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) vagy [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java ide](https://www.eclipse.org/downloads/) (a Java már telepítve van)
- A támogatott linuxos platformokhoz telepíteni kell bizonyos könyvtárakat (`libssl` a Secure Sockets Layer támogatáshoz, és `libasound2` a hangtámogatáshoz). A könyvtárak megfelelő verzióinak telepítéséhez szükséges parancsokért tekintse meg az alábbi disztribúciót.

  - Ubuntu rendszeren futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - A Debian 9 rendszeren futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - A RHEL/CentOS 8 rendszeren futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

        ```sh
        sudo yum update
        sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
        ```

> [!NOTE]
> A RHEL/CentOS 8 rendszeren kövesse az [OpenSSL Linux rendszerhez való konfigurálásának](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)utasításait.

- Windows rendszeren a platformhoz a [Visual Studio C++ 2019-hez készült Microsoft vizualizációs terjeszthető](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) csomag szükséges. Vegye figyelembe, hogy az első telepítéskor szükség lehet a Windows újraindítására az útmutató folytatása előtt.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Eclipse-projekt létrehozása és a Speech SDK telepítése

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [windows](../quickstart-list.md)]
