---
title: Beszédfelismerési eszközök SDK Roobo intelligens hang dev Kit v1 – Speech Service
titleSuffix: Azure Cognitive Services
description: Előfeltételek és utasítások a Speech Devices SDK első lépéseihez, Roobo Smart audio dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9add5b063b67ddcc4cd5bf93e7f5b570b004e5ca
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815587"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Eszköz: Roobo Smart audio dev Kit

Ez a cikk a Roobo Smart audio dev Kit eszközre vonatkozó információkat tartalmaz.

## <a name="set-up-the-development-kit"></a>A fejlesztői csomag beállítása

1. A fejlesztői készlet két Micro USB-összekötővel rendelkezik. A bal oldali összekötő a fejlesztői készlet bekapcsolására szolgál, és az alábbi képen Kiemelt teljesítményként jelenik meg. Az egyik az, hogy irányítsa, és a képen hibakeresést jelöl.

    ![A fejlesztői csomag csatlakoztatása](media/speech-devices-sdk/qsg-1.png)

1. A Power port SZÁMÍTÓGÉPekhez vagy hálózati adapterhez való csatlakoztatásához használjon egy Micro USB-kábelt a fejlesztői készlethez. A felső táblán egy zöld energiaellátási kijelző jelenik meg.

1. A fejlesztői csomag vezérléséhez csatlakoztassa a hibakeresési portot egy számítógéphez egy második Micro USB-kábel használatával. A megbízható kommunikáció biztosítása érdekében elengedhetetlen a kiváló minőségű kábelek használata.

1. A körkörös vagy lineáris konfigurációhoz tartozó fejlesztői készlet tájolása.

    |Fejlesztői csomag konfigurációja|Tájolás legördülő listából|
    |-----------------------------|------------|
    |Körkörös|Egyenesen, a felső határt megnéző mikrofonokkal|
    |Lineáris|Az oldalon, az Ön felé irányuló mikrofonokkal (az alábbi képen látható)|

    ![Lineáris fejlesztői csomag tájolása](media/speech-devices-sdk/qsg-2.png)

1. Telepítse a tanúsítványokat, és állítsa be a hangeszköz engedélyeit. Írja be a következő parancsokat a parancssor ablakába:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Ezek a parancsok az androidos hibakeresési hidat használják, `adb.exe`, amely a Android Studio telepítés részét képezi. Ez az eszköz a C:\Users\[Felhasználónév] \AppData\Local\Android\Sdk\platform-tools. található. Ezt a könyvtárat hozzáadhatja az elérési úthoz, hogy kényelmesebb legyen a `adb`meghívása. Ellenkező esetben meg kell adnia az ADB. exe telepítésének teljes elérési útját minden olyan parancsnál, amely meghívja a `adb`.
    >
    > Ha hibaüzenet jelenik meg `no devices/emulators found` akkor ellenőrizze, hogy az USB-kábel csatlakoztatva van-e, és kiváló minőségű kábel-e. A `adb devices` segítségével ellenőrizhető, hogy a számítógép tud-e kommunikálni a fejlesztői csomaggal, mert az eszközök listáját fogja visszaadni.
    >
    > [!TIP]
    > A számítógép mikrofonjának és hangszórójának elnémításával győződjön meg arról, hogy dolgozik a fejlesztői csomag mikrofonjában. Így nem fogja véletlenül elindítani az eszközt a számítógép hangján.

1. Ha hangszórót szeretne csatlakoztatni a fejlesztői csomaghoz, csatlakoztathatja azt a hangbemenethez. Válasszon egy kiváló minőségű hangszórót 3,5 mm-es analóg csatlakozóval.

    ![Vysor hang](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Fejlesztési információk

További fejlesztési információk: [Roobo fejlesztői útmutató](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Hang

A Roobo olyan eszközt biztosít, amely rögzíti az összes hang-és flash-memóriát. Hasznos lehet a hangproblémák elhárításában. Az eszköz egy verzióját az egyes fejlesztői csomagokhoz tartozó konfigurációkhoz biztosítjuk. A [Roobo oldalon](https://ddk.roobo.com/)válassza ki az eszközt, majd kattintson a lap alján található **Roobo-eszközök** hivatkozásra.

## <a name="next-steps"></a>Következő lépések

* [Az Android-minta alkalmazás futtatása](speech-devices-sdk-android-quickstart.md)
