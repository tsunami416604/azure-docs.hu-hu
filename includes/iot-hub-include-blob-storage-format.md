---
title: fájlbefoglalás
description: fájlbefoglalás
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 27a254b664e2035cc66109f7a473a3e9ef4d730e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "73612082"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Az adattárakat a blob Storage-ba lehet írni az [Apache Avro](https://avro.apache.org/) formátumban, amely az alapértelmezett vagy a JSON (előzetes verzió). 
>    
> A JSON-formátum kódolásának lehetősége minden olyan régióban előzetes verzióban érhető el, amelyben IoT Hub elérhető, kivéve az USA keleti régiója, az USA nyugati régiója és Nyugat-Európa. A kódolás formátuma csak akkor állítható be, amikor a blob Storage-végpont konfigurálva van. A már beállított végpontok formátuma nem módosítható. JSON-kódolás használatakor a contentType JSON-ra és a contentEncoding UTF-8 értékre kell állítania az üzenetrendszer tulajdonságai között. 
>
> A blob Storage-végpontok használatával kapcsolatos részletesebb információkért tekintse [meg az Útválasztás a Storage](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage)szolgáltatáshoz című témakör útmutatását.
>