---
title: Telepítse az Azure IoT Edge IoT Core-on |} A Microsoft Docs
description: Az Azure IoT Edge-futtatókörnyezet telepíthető a Windows IoT Core-eszköz
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f57db00894dab80f96f45111331d47a173520ced
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39575998"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Az IoT Edge-futtatókörnyezet telepítéséhez Windows IoT Core-on – előzetes verzió

Az Azure IoT Edge és [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) együttműködve engedélyezéséhez a peremhálózati számítástechnikai is kisebb eszközökön. Az Azure IoT Edge-futtatókörnyezet még nagyon kicsi, ami nagyon elterjedtek az IoT-iparág egyetlen tábla számítógép (az SBC-t) eszközök is futtathatja. 

Ez a cikk végigvezeti a modul a Windows IoT Core rendszert futtató fejlesztői táblához kiépítése. 

**Jelenleg a Windows IoT Core támogatja az Azure IoT Edge csak az Intel x64-alapú processzort.**

## <a name="install-the-container-runtime"></a>A tároló-modul telepítése

1. A kártya konfigurálása **17134 összeállítása (RS4)** IoT Core-lemezképet. 
1. Kapcsolja be az eszközt, majd [jelentkezzen be távolról PowerShell][lnk-powershell].
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

Az IoT Edge biztonsági démon telepítse és konfigurálja az utasításokat követve [Ez a cikk][lnk-install-windows-on-windows]

## <a name="next-steps"></a>További lépések

Most, hogy az IoT Edge-futtatókörnyezet-es eszközök, ismerje meg, hogyan [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok][lnk-deploy].

<!--Links-->
[lnk-install-windows-on-windows]: how-to-install-iot-edge-windows-with-windows.md
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
