---
title: Telepítse az Azure IoT Edge IoT Core-on |} A Microsoft Docs
description: Az Azure IoT Edge-futtatókörnyezet telepíthető a Windows IoT Core-eszköz
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 17afc095fa0500ca98559a9523282d75949faa60
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564826"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Az IoT Edge-futtatókörnyezet telepítéséhez Windows IoT Core-on – előzetes verzió

Az Azure IoT Edge és [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) együttműködve engedélyezéséhez a peremhálózati számítástechnikai is kisebb eszközökön. Az Azure IoT Edge-futtatókörnyezet még nagyon kicsi, ami nagyon elterjedtek az IoT-iparág egyetlen tábla számítógép (az SBC-t) eszközök is futtathatja. 

Ez a cikk végigvezeti a modul a Windows IoT Core rendszert futtató fejlesztői táblához kiépítése. 

**Jelenleg a Windows IoT Core támogatja az Azure IoT Edge csak az Intel x64-alapú processzort.**

## <a name="install-the-container-runtime"></a>A tároló-modul telepítése

1. A kártya konfigurálása **17134 összeállítása (RS4)** IoT Core-lemezképet. 
1. Kapcsolja be az eszközt, majd [jelentkezzen be távolról PowerShell](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).
1. A PowerShell konzolon a tároló-futtatókörnyezet telepítéséhez: 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-0.0.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >A tároló-futtatókörnyezet a Moby project build kiszolgálóról, és csak tesztelési célokra szolgál. Nem tesztelt, által támogatott, vagy a Docker által támogatott.

## <a name="finish-installing"></a>Telepítésének befejezése

Az IoT Edge biztonsági démon telepítse és konfigurálja az utasításokat követve [Ez a cikk](how-to-install-iot-edge-windows-with-windows.md)

## <a name="next-steps"></a>További lépések

Most, hogy az IoT Edge-futtatókörnyezet-es eszközök, ismerje meg, hogyan [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md).