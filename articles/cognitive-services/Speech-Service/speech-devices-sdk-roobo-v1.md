---
title: Beszédeszközök SDK Roobo Smart Audio Dev Kit v1 - Beszéd szolgáltatás
titleSuffix: Azure Cognitive Services
description: Előfeltételek és utasítások a beszédeszközök SDK, Roobo Smart Audio Dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9add5b063b67ddcc4cd5bf93e7f5b570b004e5ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815587"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Eszköz: Roobo Smart Audio Dev Kit

Ez a cikk eszközspecifikus információkat tartalmaz a Roobo Smart Audio Dev Kit számára.

## <a name="set-up-the-development-kit"></a>A fejlesztői készlet beállítása

1. A fejlesztőkészlet két mikro USB csatlakozóval rendelkezik. A bal oldali csatlakozó a fejlesztői készlet áramellátásához szükséges, és az alábbi képen power-ként van kiemelve. A helyes az, hogy ellenőrizzék azt, és van jelölve Debug a képen.

    ![A fejlesztői készlet csatlakoztatása](media/speech-devices-sdk/qsg-1.png)

1. A fejlesztői készletet mikro USB-kábellel csatlakoztathatja a hálózati porthoz egy PC-hez vagy hálózati adapterhez. A felső tábla alatt zöld energiajelző világít.

1. A fejlesztői készlet vezérléséhez csatlakoztassa a hibakeresési portot a számítógéphez egy második mikro-USB-kábellel. A megbízható kommunikáció érdekében elengedhetetlen, hogy kiváló minőségű kábelt használjunk.

1. Igazítsa a fejlesztői készletet a körkörös vagy lineáris konfigurációhoz.

    |Fejlesztői készlet konfigurációja|Tájolás|
    |-----------------------------|------------|
    |Körkörös|Függőlegesen, a mennyezet felé néző mikrofonokkal|
    |Lineáris|Az oldalán, mikrofonok felé (látható az alábbi képen)|

    ![Lineáris fejlesztői készlet orientáció](media/speech-devices-sdk/qsg-2.png)

1. Telepítse a tanúsítványokat, és állítsa be a hangeszköz engedélyeit. Írja be a következő parancsokat a parancssorablakba:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Ezek a parancsok az Android `adb.exe`Debug Bridge programot használják, amely az Android Studio telepítésének része. Ez az eszköz a C:\Users\[felhasználónévben található]\AppData\Local\Android\Sdk\platform-tools. Hozzáadhatja ezt a könyvtárat az elérési úthoz, hogy kényelmesebb legyen a meghívás. `adb` Ellenkező esetben minden meghívja a műveletben meg kell adnia az `adb`adb.exe telepítésének teljes elérési útját.
    >
    > Ha hibaüzenetet `no devices/emulators found` lát, ellenőrizze, hogy az USB-kábel csatlakoztatva van-e, és kiváló minőségű kábel. `adb devices` Ellenőrizheti, hogy a számítógép tud-e beszélni a fejlesztői készletpel, mivel az visszaadja az eszközök listáját.
    >
    > [!TIP]
    > Némítsa el a számítógép mikrofonját és hangszóróját, hogy biztosan együttműködjön a fejlesztőkészlet mikrofonjaival. Így nem fogja véletlenül elindítani az eszközt a számítógépről érkező hanggal.

1. Ha hangszórót szeretne csatlakoztatni a fejlesztői készlethez, csatlakoztathatja azt a hangvonalhoz. Válasszon egy jó minőségű hangszórót 3,5 mm-es analóg csatlakozóval.

    ![Vysor hang](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Fejlesztési információk

További fejlesztési információkért tekintse meg a [Roobo fejlesztési útmutatót](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Hang

Roobo biztosít egy eszköz, amely rögzíti az összes hang flash memória. Ez segíthet a hangproblémák elhárításában. Az eszköz egy verziója minden fejlesztői készlet konfigurációjához rendelkezésre áll. A [Roobo webhelyen](https://ddk.roobo.com/)jelölje ki az eszközt, majd válassza a lap alján található **Roobo Tools** hivatkozást.

## <a name="next-steps"></a>További lépések

* [Az Android mintaalkalmazás futtatása](speech-devices-sdk-android-quickstart.md)
