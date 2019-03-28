---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: 94e95864d8bac2d6dc0ff690a2a8f53bd2db5a40
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522519"
---
## <a name="validate-container-is-running"></a>Érvényesítése tároló fut-e 

Nincsenek a többféle módon ellenőrizze a tároló fut: 

|Kérés|Cél|
|--|--|
|`http://localhost:5000/`|A tároló kezdőlapja biztosít.|
|`http://localhost:5000/status`|A kért az GET, a tároló ellenőrzése fut anélkül, hogy ez egy végpont lekérdezés. A Kubernetes esetében használható [liveness és a készültségi vizsgálatok](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|A tároló a végpontok dokumentáció teljes készletét nyújtja, valamint egy `Try it now` funkció. Ez a funkció lehetővé teszi, hogy a beállítások megadása HTML webes űrlapon, és győződjön meg arról, a lekérdezés, kód írása nélkül. Ha a lekérdezés visszatér, például a CURL-parancs megadott bemutatják a HTTP-fejlécek és törzs szükséges formátumban. |

![Tároló kezdőlapja](./media/cognitive-services-containers-api-documentation/container-webpage.png)
