---
title: Beszéd eszközök SDK Roobo intelligens hang fejlesztési Kit v1 – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: Előfeltételek és az első lépések a beszédfelismerő eszköz SDK-Roobo intelligens hang Dev csomag v1 utasításokat.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: e4aba26238a0d87c8e708ae27c7b2dbdb73f16ab
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604833"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Eszköz: Roobo intelligens hang Dev csomag

Ez a cikk az eszköz Roobo intelligens hang fejlesztési készletre vonatkozó információk.

## <a name="set-up-the-development-kit"></a>A fejlesztői készlet beállítása

1. A fejlesztői készlet két micro USB-összekötővel rendelkezik. A bal oldali összekötő adatelérésnek köszönhetően a csomag és az alábbi képen kiemelt Power. A jobb oldalon egy vezérlő, és meg van jelölve az a képen hibakeresést.

    ![Csatlakozás a fejlesztői csomag](media/speech-devices-sdk/qsg-1.png)

1. A csomag Power micro USB-kábelen keresztül a kiemelt port kapcsolódni egy számítógéphez, illetve power adapter használatával. Egy zöld power kijelző fog helyezzen üzembe az első tábla alatt.

1. Szabályozhatja a csomagban található, a hibakeresési port számítógép protokollal csatlakozni egy második micro USB-kábelen keresztül. Ez elengedhetetlen, használjon egy kiváló minőségű kábelt, megbízható kommunikációt.

1. Elhelyezés a fejlesztői készlet sem a kör alakú és lineáris konfigurációját.

    |Development kit konfigurálása|Tájolás|
    |-----------------------------|------------|
    |Kör alakú|Mintha a mikrofonok a felső határ irányuló|
    |Lineáris|Az oldalán a mikrofonok felénk (az alábbi ábrán látható)|

    ![lineáris dev csomag tájolása](media/speech-devices-sdk/qsg-2.png)

1. Telepítse a tanúsítványokat, és állítsa be az engedélyeket a hangeszköz. Egy parancssori ablakban írja be a következő parancsokat:

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
    > Ha hibaüzenet jelenik meg `no devices/emulators found` ezután ellenőrizze, hogy az USB-kábel csatlakoztatva van, és a egy kiváló minőségű kábel. Használhat `adb devices` ellenőrizze, hogy a számítógép kommunikálni tudjon az a csomagban található, azon eszközök listáját adja vissza.
    >
    > [!TIP]
    > A számítógép mikrofon és előadó, és ellenőrizze, hogy dolgozik a fejlesztői készlet mikrofonok vypnutí. Így nem fog véletlenül indít el az eszközt az audio a számítógépről.

1. Ha azt szeretné, a beszélő csatlakoztatása a fejlesztői csomag, akkor csatlakozhat ki a következő vonal. Egy jó minőségű speaker egy 3.5-ös mm analóg plug-kell kiválasztani.

    ![Vysor hang](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Fejlesztői információ

Fejlesztési kapcsolatos további információkért lásd: a [Roobo – fejlesztési útmutató](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Hang

Roobo biztosít olyan eszköz, amely rögzíti az összes hanganyag flash-memória. Hang elhárításának segíthet. Az eszköz egy verzióját minden egyes development kit konfiguráció biztosítunk. Az a [Roobo hely](https://ddk.roobo.com/), válassza ki az eszközt, és válassza a **Roobo eszközök** hivatkozásra a lap alján.

## <a name="next-steps"></a>További lépések

* [Az Android-mintaalkalmazás futtatása](speech-devices-sdk-android-quickstart.md)
