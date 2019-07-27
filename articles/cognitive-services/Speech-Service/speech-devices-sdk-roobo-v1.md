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
ms.openlocfilehash: 08bac7cd833f52d2dfec4561c2f87330a4119748
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552877"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Eszköz: Roobo Smart audio dev Kit

Ez a cikk a Roobo Smart audio dev Kit eszközre vonatkozó információkat tartalmaz.

## <a name="set-up-the-development-kit"></a>A fejlesztői készlet beállítása

1. A fejlesztői készlet két Micro USB-összekötővel rendelkezik. A bal oldali összekötő a fejlesztői készlet bekapcsolására szolgál, és az alábbi képen Kiemelt teljesítményként jelenik meg. Az egyik az, hogy irányítsa, és a képen hibakeresést jelöl.

    ![Csatlakozás a fejlesztői csomag](media/speech-devices-sdk/qsg-1.png)

1. A Power port SZÁMÍTÓGÉPekhez vagy hálózati adapterhez való csatlakoztatásához használjon egy Micro USB-kábelt a fejlesztői készlethez. A felső táblán egy zöld energiaellátási kijelző jelenik meg.

1. A fejlesztői csomag vezérléséhez csatlakoztassa a hibakeresési portot egy számítógéphez egy második Micro USB-kábel használatával. A megbízható kommunikáció biztosítása érdekében elengedhetetlen a kiváló minőségű kábelek használata.

1. Elhelyezés a fejlesztői készlet sem a kör alakú és lineáris konfigurációját.

    |Development kit konfigurálása|Tájolás|
    |-----------------------------|------------|
    |Kör alakú|Mintha a mikrofonok a felső határ irányuló|
    |Lineáris|Az oldalán a mikrofonok felénk (az alábbi ábrán látható)|

    ![lineáris dev csomag tájolása](media/speech-devices-sdk/qsg-2.png)

1. Telepítse a tanúsítványokat, és állítsa be a hangeszköz engedélyeit. Egy parancssori ablakban írja be a következő parancsokat:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Ezek a parancsok használata az Android-hibakeresési híd `adb.exe`, amely az Android Studio telepítést része. Ez az eszköz található C:\Users\[felhasználónév] \AppData\Local\Android\Sdk\platform-eszközöket. Ebben a címtárban is hozzáadhat az elérési úthoz, hogy kényelmesebbé meghívásához `adb`. Ellenkező esetben meg kell adnia a teljes elérési útja a telepített minden parancshoz, amely meghívja a adb.exe `adb`.
    >
    > Ha hibaüzenet `no devices/emulators found` jelenik meg, ellenőrizze, hogy az USB-kábel csatlakoztatva van-e, és kiváló minőségű kábel-e. A használatával `adb devices` ellenőrizhető, hogy a számítógép tud-e kommunikálni a fejlesztői csomaggal, mert az eszközök listáját fogja visszaadni.
    >
    > [!TIP]
    > A számítógép mikrofon és előadó, és ellenőrizze, hogy dolgozik a fejlesztői készlet mikrofonok vypnutí. Így nem fog véletlenül indít el az eszközt az audio a számítógépről.

1. Ha azt szeretné, a beszélő csatlakoztatása a fejlesztői csomag, akkor csatlakozhat ki a következő vonal. Válasszon egy kiváló minőségű hangszórót 3,5 mm-es analóg csatlakozóval.

    ![Vysor hang](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Fejlesztési információk

További fejlesztési információk: [Roobo fejlesztői útmutató](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Hang

A Roobo olyan eszközt biztosít, amely rögzíti az összes hang-és flash-memóriát. Hang elhárításának segíthet. Az eszköz egy verzióját minden egyes development kit konfiguráció biztosítunk. A [Roobo oldalon](https://ddk.roobo.com/)válassza ki az eszközt, majd kattintson a lap alján található **Roobo-eszközök** hivatkozásra.

## <a name="next-steps"></a>További lépések

* [Az Android-minta alkalmazás futtatása](speech-devices-sdk-android-quickstart.md)
