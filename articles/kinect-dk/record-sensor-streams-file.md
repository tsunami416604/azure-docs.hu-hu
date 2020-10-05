---
title: Rövid útmutató – az Azure Kinect Sensor streamek rögzítése fájlba
description: Ebből a rövid útmutatóból megtudhatja, hogyan rögzíthet adatfolyamokat az érzékelő SDK-ból egy fájlba.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Azure, Kinect, Record, Play back, Reader, Matroska, MKV, streamek, mélység, RGB, kamera, szín, IMU, hang, érzékelő
ms.openlocfilehash: 3dab147b593bf012bd6cd9c95d0195e84a2cbcf1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277957"
---
# <a name="quickstart-record-azure-kinect-sensor-streams-to-a-file"></a>Gyors útmutató: az Azure Kinect Sensor streamek rögzítése fájlba

Ebből a rövid útmutatóból megtudhatja, hogyan rögzíthet adatstreameket az érzékelő SDK-ból egy fájlba az [Azure Kinect Recorder](azure-kinect-recorder.md) eszköz használatával.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató feltételezi, hogy:

- Az Azure Kinect DK csatlakoztatva van a gazdagéphez, és megfelelően működik.
- Elkészült a hardver [beállításával](set-up-azure-kinect-dk.md) .

## <a name="create-recording"></a>Rögzítés létrehozása

1. Nyisson meg egy parancssort, és adja meg az [Azure Kinect Recorder](azure-kinect-recorder.md)elérési útját, amely a telepített eszközök könyvtárban található `k4arecorder.exe` . Például: `C:\Program Files\Azure Kinect SDK\tools\k4arecorder.exe`.
2. 5 másodpercet rögzítsen.

    `k4arecorder.exe -l 5 %TEMP%\output.mkv`

3. Alapértelmezés szerint a felvevő a NFOV Unbinned mélységi módot használja, és a rendszer 30 fps-ként, a IMU-adatokat is beleértve. A rögzítési lehetőségek és tippek teljes áttekintését az [Azure Kinect Recorder](azure-kinect-recorder.md)című témakörben találja.

## <a name="play-back-recording"></a>Rögzítés visszajátszása

A rögzítést az [Azure Kinect Viewer](azure-kinect-viewer.md) használatával lehet lejátszani.

1. Dob [`k4aviewer.exe`](azure-kinect-viewer.md)
2. Bontsa ki az **Open Recording** fület, és nyissa meg a rögzítést.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan rögzíthet szenzor-adatfolyamokat egy fájlba, itt az ideje, hogy

> [!div class="nextstepaction"]
> [Az első Azure Kinect-alkalmazás összeállítása](build-first-app.md)
