---
title: Az IoT hub IP-címének megismerése | Microsoft Docs
description: Ismerje meg, hogyan kérdezheti le az IoT hub IP-címét és annak tulajdonságait. Az IoT hub IP-címe bizonyos helyzetekben változhat, például a vész-helyreállítás vagy a regionális feladatátvétel során.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 7af40404550fb78af891563d8256f23620781b24
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841533"
---
# <a name="understanding-the-ip-address-of-your-iot-hub"></a>Az IoT hub IP-címének megismerése

Az IoT hub IP-címe egy elosztott terhelésű nyilvános végpont a hub számára, nem pedig dedikált IP-cím. A címeket az az Azure-régió hálózati címtartomány határozza meg, ahol az üzembe van helyezve. Ez az IP-cím előzetes értesítés nélkül változhat. Az adatközpontok hálózati frissítései, az adatközpont vész-helyreállítása vagy az IoT hub regionális feladatátvétele megváltoztathatja az IoT hub IP-címét. Tekintse meg [IoT hub magas rendelkezésre állást és](iot-hub-ha-dr.md) a vész-helyreállítást az Azure IoT hub regionális feladatátvétel és rendelkezésre állás további részleteiért.

Az IoT hub IP-címe a feladatátvételt követően egy másik régióba módosul. A funkció teszteléséhez kövesse az [IoT hub manuális feladatátvételét](tutorial-manual-failover.md)ismertető oktatóanyagot.

## <a name="discover-your-iot-hub-ip-address"></a>Az IoT hub IP-címének felderítése

A IoT Hub IP-címe a CNAME ([*IoT-hub-Name*]. Azure-Devices.net) névkeresési DNS-címkeresés használatával oldható fel. Az **nslookup** használatával ellenőrizheti egy IoT hub-példány IP-címét:

```cmd/sh
nslookup {YourIoTHubName}.azure-devices.net
```

Ez az IP-cím értesítés nélkül megváltozhat. Feladatátvétel vagy vész-helyreállítási forgatókönyv esetén le kell kérdezni a IoT hub IP-címét a másodlagos régióban.

## <a name="outbound-firewall-rules-for-iot-hub"></a>IoT hub kimenő tűzfalszabályok

Próbálja meg létrehozni a tűzfalszabályok és a szűrést az IoT hub állomásneve vagy tartománya alapján. Ha csak bizonyos címekre engedélyezi a kimenő forgalmat, a IoT hub IP-címét rendszeresen lekérdezheti, és frissítheti a tűzfalszabályok beállításait.

## <a name="support-for-ipv6"></a>IPv6-támogatás 

Az IPv6 jelenleg nem támogatott IoT Hubon.