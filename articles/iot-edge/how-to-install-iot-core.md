---
title: "Az Azure IoT peremhálózati telepíthető IoT Core |} Microsoft Docs"
description: "Az Azure IoT peremhálózati futásidejű Windows IoT Core eszköz telepítése"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cc34e5cecafe485608ba428395b690ba57f71e9c
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Az IoT-Edge futásidejű telepíteni a Windows IoT alapvető – előzetes

Az Azure IoT peremhálózati és [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) együttműködve még kis eszközökön számítástechnikai biztonsági engedélyezése. Az Azure IoT peremhálózati futásidejű még apró egyetlen tábla számítógép (SBC) készülékek, amelyek nagyon gyakori az IoT-megoldásokat is futtathatók. 

Ez a cikk végigvezeti a futtatókörnyezet kiépítése a egy [MinnowBoard nagy rombuszhal] [ lnk-minnow] futó Windows IoT Core fejlesztési tábla. Windows IoT Core támogatja Azure IoT peremhálózati csak Intel x64-alapú processzort. 

## <a name="install-the-runtime"></a>A futtatókörnyezet telepítése

1. Telepítés [Windows 10 IoT Core irányítópult] [ lnk-core] a gazdarendszer.
1. Kövesse a [-eszköz beállításához] [ lnk-board] konfigurálása a tábla a MinnowBoard nagy rombuszhal és maximális Build 16299 lemezképpel. 
1. Kapcsolja be az eszközt, majd [PowerShell távolról történő bejelentkezés][lnk-powershell].
1. A PowerShell-konzolban a tároló futásidejű telepítése: 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-17.06.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >A tároló futásidejű Moby project build kiszolgálóról, és csak tesztelési célokra. Nem tesztelt, által támogatott, vagy Docker által támogatott.

1. Az IoT-Edge futásidejű telepítése, és a konfiguráció ellenőrzése:

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   Ezt a parancsfájlt az alábbi előnyöket nyújtja: 
   * Python 3.6.
   * Az IoT-Edge vezérlő parancsfájl (iotedgectl.exe)

A távoli PowerShell-ablakban zöld iotedgectl.exe eszközből tájékoztató kimeneti jelenhet meg. Ez nem feltétlenül jelent hibát. 

## <a name="next-steps"></a>Következő lépések

Most, hogy az IoT-Edge futásidejű-t futtató eszközeiken, megtudhatja, hogyan [központi telepítése és figyelése IoT peremhálózati modulok léptékű][lnk-deploy].

<!--Links-->
[lnk-minnow]: https://minnowboard.org/ 
[lnk-core]: https://docs.microsoft.com/windows/iot-core/connect-your-device/iotdashboard
[lnk-board]: https://developer.microsoft.com/windows/iot/Docs/GetStarted/mbm/sdcard/stable/getstartedstep2
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
