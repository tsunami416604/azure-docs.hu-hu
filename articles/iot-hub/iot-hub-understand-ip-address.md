---
title: Az IoT hub IP-címének megismerése | Microsoft Docs
description: Ismerje meg, hogyan kérdezheti le az IoT hub IP-címét és annak tulajdonságait. Az IoT hub IP-címe bizonyos helyzetekben változhat, például a vész-helyreállítás vagy a regionális feladatátvétel során.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 5c77fb30ef60c1ad82d0a87442bc8af186c54321
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383912"
---
# <a name="iot-hub-ip-addresses"></a>IP-címek IoT Hub

Az IoT hub IP-címeinek előtagjait a *AzureIoTHub* [szolgáltatás címkéjén](../virtual-network/service-tags-overview.md)keresztül rendszeresen közzéteszi a rendszer. A megfelelő működés biztosítása érdekében a IoT-eszközöknek kimenő kapcsolattal kell rendelkezniük a *AzureIoTHub* szolgáltatás címkéjén felsorolt előtagok címeihez. A IoT-alkalmazási szolgáltatásoknak Ezenfelül kimenő kapcsolattal kell rendelkezniük a *EventHub* szolgáltatás címkéjén felsorolt előtagok címeihez.


## <a name="best-practices"></a>Ajánlott eljárások

* Az IoT hub IP-címének előtagjainak módosítása változhat. A módosításokat a rendszer rendszeresen közzéteszi a szolgáltatáson keresztül, mielőtt érvénybe lépnek. Ezért fontos, hogy folyamatokat fejlesszen ki a legújabb szolgáltatási címkék rendszeres beolvasására és használatára. Ez a folyamat automatizálható a [Service Tags Discovery API](../virtual-network/service-tags-overview.md#service-tags-in-on-premises)használatával.
* Használja a *AzureIoTHub. [ régió neve]* címkével azonosíthatja az IoT hub-végpontok által az adott régióban használt IP-előtagokat. Az adatközpontok vész-helyreállítási feladatainak elvégzéséhez, illetve a [regionális feladatátvételhez](iot-hub-ha-dr.md) a IoT hub geo-pair RÉGIÓJÁNAK IP-előtagjaihoz való kapcsolódás is engedélyezett.


## <a name="support-for-ipv6"></a>IPv6-támogatás 

Az IPv6 jelenleg nem támogatott IoT Hubon.