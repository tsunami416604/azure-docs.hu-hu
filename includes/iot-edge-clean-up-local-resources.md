---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 2b61cc8c5c448c28e96b06afa3556688a82567ed
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202696"
---
### <a name="delete-local-resources"></a>Helyi erőforrások törlése

Ha el szeretné távolítani IoT Edge-futtatókörnyezetet és az ahhoz tartozó erőforrásokat az eszközéről, azt az eszköze operációs rendszerének megfelelő parancsokkal teheti meg. 

#### <a name="windows"></a>Windows

Az IoT Edge-futtatókörnyezet eltávolítása.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

Ha eltávolította az IoT Edge-futtatókörnyezetet, az általa létrehozott tárolók leállnak, de továbbra is ott lesznek az eszközön. Tekintse meg az összes tárolót.

   ```powershell
   docker ps -a
   ```

Törölje az eszközön létrehozott futtatókörnyezeti tárolókat.

   ```powershell
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Töröljön minden további tárolót, amely a `docker ps` kimenetében listázva volt – ezt a tárolók nevére való hivatkozással teheti meg. 

#### <a name="linux"></a>Linux

Távolítsa el az IoT Edge-futtatókörnyezetet.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Ha eltávolította az IoT Edge-futtatókörnyezetet, az általa létrehozott tárolók leállnak, de továbbra is ott lesznek az eszközön. Tekintse meg az összes tárolót.

   ```bash
   sudo docker ps -a
   ```

Törölje az eszközön létrehozott futtatókörnyezeti tárolókat.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Töröljön minden további tárolót, amely a `docker ps` kimenetében listázva volt – ezt a tárolók nevére való hivatkozással teheti meg. 

Távolítsa el a tároló-futtatókörnyezetet.

   ```bash
   sudo apt-get remove --purge moby
   ```