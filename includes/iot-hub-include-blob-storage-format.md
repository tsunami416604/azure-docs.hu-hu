---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 27a254b664e2035cc66109f7a473a3e9ef4d730e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73612082"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Az adatok az [Apache Avro](https://avro.apache.org/) formátumban, az alapértelmezett vagy json (előzetes verzió) a blobstorage-ba írhatók. 
>    
> A JSON-formátum kódolásának képessége előzetes verzióban érhető el minden olyan régióban, ahol az IoT Hub elérhető, kivéve az USA keleti régióját, az USA nyugati régióját és Nyugat-Európát. A kódolási formátum csak a blob storage-végpont konfigurálásakor állítható be. A formátum nem módosítható olyan végponthoz, amely már be van állítva. JSON-kódolás használatakor a contentType-ot JSON-ra, a contentEncoding-t pedig UTF-8-ra kell állítania az üzenetrendszer tulajdonságaiközött. 
>
> A blobstorage-végpont használatával kapcsolatos további információkért tekintse meg [a tárolóba történő továbbításra vonatkozó útmutatást.](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage)
>