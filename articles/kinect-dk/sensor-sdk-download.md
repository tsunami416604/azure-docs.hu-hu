---
title: Azure Kinect Sensor SDK letöltése
description: Ismerje meg, hogyan töltheti le és telepítheti az Azure Kinect Sensor SDK-t Windows és Linux rendszereken.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, frissítés letöltése, legújabb, elérhető, telepítés
ms.openlocfilehash: 2fd14781c42192c713d826729f8fab6c698d6321
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505477"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Azure Kinect Sensor SDK letöltése

Ezen a lapon az Azure Kinect Sensor SDK egyes verzióihoz tartozó letöltési hivatkozások találhatók. A telepítő biztosítja az Azure Kinect fejlesztéséhez szükséges összes fájlt.

## <a name="azure-kinect-sensor-sdk-contents"></a>Az Azure Kinect Sensor SDK tartalma

- Fejlécek és könyvtárak az Azure Kinect DK használatával történő alkalmazás létrehozásához.
- Az Azure Kinect DK-t használó alkalmazások számára szükséges újraterjeszthető DLL-ek.
- Az [Azure Kinect Viewer](azure-kinect-viewer.md).
- Az [Azure Kinect-felvevő](azure-kinect-recorder.md).
- Az [Azure Kinect belső vezérlőprogram eszköz](azure-kinect-firmware-tool.md).

## <a name="windows-installation-instructions"></a>Windows telepítési utasítások

[Itt](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md)megtalálhatja az Azure Kinect Sensor SDK és a belső vezérlőprogram legújabb és korábbi verzióinak telepítési részleteit.

[Itt](https://github.com/microsoft/Azure-Kinect-Sensor-SDK)megtalálhatja a forráskódot.

> [!NOTE]
> Az SDK telepítésekor jegyezze fel a telepítési útvonalat. Például: "C:\Program Files\Azure Kinect SDK 1,2". Az ezen az elérési úton található cikkekben hivatkozott eszközöket megtalálja.

## <a name="linux-installation-instructions"></a>Linuxos telepítési utasítások

Jelenleg az egyetlen támogatott disztribúció az Ubuntu 18,04. További terjesztések támogatásának kéréséhez tekintse meg [ezt a lapot](https://aka.ms/azurekinectfeedback).

Először konfigurálnia kell a [Microsoft Package repositoryját](https://packages.microsoft.com/), az [itt](/windows-server/administration/linux-package-repository-for-microsoft-software)található utasításokat követve.

Most már telepítheti a szükséges csomagokat. A `k4a-tools` csomag tartalmazza az [Azure Kinect Viewert](azure-kinect-viewer.md), az [Azure Kinect Recorder](record-sensor-streams-file.md)és az [Azure Kinect belső vezérlőprogram eszközét](azure-kinect-firmware-tool.md). A telepítéséhez futtassa a következőt

 `sudo apt install k4a-tools`

 A `libk4a<major>.<minor>-dev` csomag tartalmazza a felépíteni kívánt fejléceket és CMAK-fájlokat `libk4a` .
A `libk4a<major>.<minor>` csomag tartalmazza a függőben lévő végrehajtható fájlok futtatásához szükséges megosztott objektumokat `libk4a` .

 Az alapszintű oktatóanyagokhoz szükség van a `libk4a<major>.<minor>-dev` csomagra. A telepítéséhez futtassa a következőt

 `sudo apt install libk4a1.1-dev`

Ha a parancs sikeres, az SDK készen áll a használatra.

## <a name="change-log-and-older-versions"></a>A napló és a régebbi verziók módosítása

[Itt](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md)megtalálhatja az Azure Kinect Sensor SDK változási naplóját.

Ha az Azure Kinect Sensor SDK régebbi verziójára van szüksége, keresse meg [itt](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Következő lépések

[Az Azure Kinect DK beállítása](set-up-azure-kinect-dk.md)