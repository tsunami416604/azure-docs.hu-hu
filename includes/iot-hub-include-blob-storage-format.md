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
ms.openlocfilehash: c779147e464a592d45da8a9a2d8e812320dc23e8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179742"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Az adatok a blob storage akár csak írható a [Apache Avro](https://avro.apache.org/) formátumba, ami az alapértelmezett vagy a JSON-ban (előzetes verzió). 
>    
> A szolgáltatás JSON formátumban kódolása előzetes verzióként érhető minden olyan régióban, amely az IoT Hub is elérhető, kivéve az USA keleti RÉGIÓJA, USA nyugati Régiójában és Nyugat-Európa. A kódolási formátum csak beállíthatja az idő a blob storage-végpont konfigurálva van. A formátum nem módosítható egy végpontot, amely már be lett állítva. JSON-kódolás használatakor be kell a contentType JSON-ra, a contentEncoding UTF-8 rendszer üzenet tulajdonságai. 
>
> További részletes információ a blob storage-végpont használatával, lásd: [útmutatás a blob Storage-útválasztás](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-blob-storage).
>