---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 02/21/2019
ms.openlocfilehash: 820ea4c401d560d4cf1c937d2efd7d7bde579a91
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675738"
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
docker run --rm -it -p 5001:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Minden ezt követő tároló lehet egy másik porton. 