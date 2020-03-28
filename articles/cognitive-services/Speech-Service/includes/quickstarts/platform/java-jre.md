---
title: 'Rövid útmutató: Speech SDK Java (Windows, Linux, macOS) platform beállítása – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben az útmutatóban a platformot java (Windows, Linux, macOS) és az SDK beszédszolgáltatás használatával való beállításához állíthatja be.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 7147f0d13c88c1d2e17e81a360a5aee55ee760ed
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78924714"
---
Ez az útmutató bemutatja, hogyan telepítheti a [beszédfelismerési SDK-t](~/articles/cognitive-services/speech-service/speech-sdk.md) a 64 bites Java 8 JRE-hez. Ha csak azt szeretné, hogy a csomag neve a kezdéshez a saját, a Java SDK nem érhető el a Maven központi tárházban. Akár Gradle-t, akár `pom.xml` függőségi fájlt használ, hozzá kell adnia egy egyéni tárházat, amely a `https://csspeechstorage.blob.core.windows.net/maven/` csomag nevére mutat (lásd alább a csomag nevét).

> [!NOTE]
> A Speech Devices SDK-ról és a Roobo eszközről lásd: [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

- A Java Speech SDK csomag a következő operációs rendszerekhez érhető el:
  - Windows: csak 64 bites
  - Mac: macOS X 10.13-as vagy újabb verzió
  - Linux: 64 bites csak Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8

## <a name="prerequisites"></a>Előfeltételek

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) vagy [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (java szükséges már telepítve)
- A támogatott Linux platformokon`libssl` bizonyos könyvtárak at kell `libasound2` telepíteni (a biztonságos aljzatok réteg támogatásához és a hangtámogatáshoz). Az alábbi terjesztésben tájékatosan tájékasztok a könyvtárak megfelelő verzióinak telepítéséhez szükséges parancsokért.

  - Az Ubuntun futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - A Debian 9-en futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - Az RHEL/CentOS 8-on futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

        ```sh
        sudo yum update
        sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
        ```

> [!NOTE]
> Az RHEL/CentOS 8 rendszeren kövesse az [OpenSSL linuxos beállítására](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)vonatkozó utasításokat.

- Windows rendszerben a [Microsoft Visual Studio 2019-hez terjeszthető Microsoft Visual C++ újraterjeszthető](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) platformra van szükség. Ne feledje, hogy ennek első telepítése szükségessé teheti a Windows újraindítását, mielőtt folytatná ezt az útmutatót.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Eclipse-projekt létrehozása és a Beszédfelismerési SDK telepítése

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>További lépések

[!INCLUDE [windows](../quickstart-list.md)]
