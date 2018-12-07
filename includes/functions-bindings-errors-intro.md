---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c1784111cd2fc2c93b67510f310b9e513cf2b86e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978819"
---
Az Azure Functions [eseményindítók és kötések](../articles/azure-functions/functions-triggers-bindings.md) különböző Azure-szolgáltatásokkal kommunikálni. Amikor ezen szolgáltatások integrálása, előfordulhat, hibák következik be, amely az alapul szolgáló Azure-szolgáltatások az API-k származik. Hibák akkor is előfordulhat, amikor REST vagy a klienskódtáron használatával kommunikálnak a függvénykódban más szolgáltatásaiban próbál. Az adatvesztés elkerülése érdekében, és ellenőrizze, helyes működését a függvények, fontos vagy forrásból hibáinak kezelése.

A következő eseményindítók rendelkezik beépített újrapróbálkozási támogatással:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Az Azure Queue storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Az Azure Service Bus (üzenetsor vagy témakör)](../articles/azure-functions/functions-bindings-service-bus.md)

Alapértelmezés szerint ezek az eseményindítók megismétlődnek legfeljebb öt alkalommal. Az ötödik újrapróbálkozás után ezek az eseményindítók írjon egy üzenetet egy speciális [ártalmas várólista](../articles/azure-functions/functions-bindings-storage-queue.md#trigger---poison-messages).

Az egyéb funkciók eseményindítók nincs nincs beépített újrapróbálkozási függvény végrehajtása során hibák esetén. Kell hiba fordulhat elő, a függvény az eseményindítóra vonatkozó információt az adatvesztés elkerülése érdekében, javasoljuk, hogy használja try-catch blokkok a függvénykódban olvasásra a hibaüzeneteket. Ha hiba történik, az adatait, a függvénynek átadott az eseményindító által speciális "ártalmas" üzenetsorokhoz írni. Ez a megközelítés akkor ugyanarra a számítógépre, amelyet a [Blob storage-eseményindító](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---poison-blobs).

Ezzel a módszerrel, amely képes hibák miatt megszakadt, és ismételje meg őket később egy másik függvény használatával üzenetek feldolgozásával tárolt információk segítségével ártalmas üzenetsorból kiváltó események rögzítheti.  
