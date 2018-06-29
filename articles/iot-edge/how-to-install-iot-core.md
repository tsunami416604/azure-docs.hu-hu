---
title: Az Azure IoT peremhálózati telepíthető IoT Core |} Microsoft Docs
description: Az Azure IoT peremhálózati futásidejű Windows IoT Core eszköz telepítése
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ae5644a62b794dc8d6ace52f21a452fa70027d39
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029560"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Az IoT-Edge futásidejű telepíteni a Windows IoT alapvető – előzetes

Az Azure IoT peremhálózati és [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) együttműködve még kis eszközökön számítástechnikai biztonsági engedélyezése. Az Azure IoT peremhálózati futásidejű még apró egyetlen tábla számítógép (SBC) készülékek, amelyek nagyon gyakori az IoT-megoldásokat is futtathatók. 

Ez a cikk végigvezeti egy fejlesztési táblán futó Windows IoT Core runtime kiépítés. 

**Windows IoT Core jelenleg csak Intel x64-alapú processzort a támogatja az Azure IoT peremhálózati.**

## <a name="install-the-container-runtime"></a>A tároló futásidejű telepítése

1. A kártya konfigurálása **Build 17134 (RS4)** IoT Core kép. 
1. Kapcsolja be az eszközt, majd [PowerShell távolról történő bejelentkezés][lnk-powershell].
1. A PowerShell-konzolban a tároló futásidejű telepítése: 

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
   >A tároló futásidejű Moby project build kiszolgálóról, és csak tesztelési célokra. Nem tesztelt, által támogatott, vagy Docker által támogatott.

## <a name="finish-installing"></a>Telepítésének befejezése

Az IoT-Edge biztonsági démon telepítse és konfigurálja az utasításokat követve [Ez a cikk][lnk-install-windows-on-windows]

## <a name="next-steps"></a>További lépések

Most, hogy az IoT-Edge futásidejű-t futtató eszközeiken, megtudhatja, hogyan [központi telepítése és figyelése IoT peremhálózati modulok léptékű][lnk-deploy].

<!--Links-->
[lnk-install-windows-on-windows]: how-to-install-iot-edge-windows-with-windows.md#download-the-edge-daemon-package-and-install
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers