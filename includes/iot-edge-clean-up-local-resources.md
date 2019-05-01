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
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866812"
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