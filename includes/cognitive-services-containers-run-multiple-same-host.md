---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 02/21/2019
ms.openlocfilehash: e80feac7dbf16652cc2e2a6176ed8b2c8c48e35b
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753777"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Több tároló futtatása ugyanazon a gazdagépen

Ha a futtatni kívánt több tároló elérhetővé tett porttal, győződjön meg arról, egy másik portot a tárolók futtatásához. Például futtassa az első tároló 5000-es és a második tároló-as porton 5001.

Cserélje le a `<container-registry>` és `<container-name>` az értékeket a tárolók használata. Ezeket nem kell ugyanazt a tárolót kell. A Face tároló és a LUIS-tároló a gazdagépen futó együtt használhat, vagy beállíthatja, hogy a futtató több Face tárolókat. 

Futtassa az első tároló 5000-es porton. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Futtassa a második tárolót 5001 porton.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Minden ezt követő tároló lehet egy másik porton. 
