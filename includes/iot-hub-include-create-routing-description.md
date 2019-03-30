---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b640a3cb9382ad72bb48e06c6a7074c96409e2e4
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630927"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Az üzeneteket különböző forrásokhoz fogja irányítani azon tulajdonságok alapján, amelyeket a szimulált eszköz csatolt az üzenethez. Azokat az üzeneteket, amelyekhez nincs egyéni útválasztás beállítva, az alapértelmezett végpontra (üzenetek/események) küldi a rendszer. A következő oktatóanyagban üzenetek küldése az IoT Hub, és megtekintheti őket a más célhelyre irányított.

|érték |Eredmény|
|------|------|
|level="storage" |Írás az Azure Storage-ba.|
|level="critical" |Írás a Service Bus-üzenetsorba. Egy logikai alkalmazás lekéri az üzenetet a sorból, és az Office 365 használatával e-mailben küldi el az üzenetet.|
|alapértelmezett |Jelenítse meg az adatokat a Power BI használatával.|

Az első lépés, hogy az adatokat továbbítja a végpont beállításához. A második lépés, hogy az üzenet útvonalának, amely használja, hogy a végpont beállításához. Miután beállította az Útválasztás, megtekintheti a végpontok és Üzenetútvonalak a portálon.