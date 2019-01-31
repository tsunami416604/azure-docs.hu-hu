---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: 98a6eb024e723e0225711adaccf385a2790e5bc8
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302784"
---
Ha egy HTTP-proxy konfigurálása, hogy a kimenő kérelmek van szüksége, használja a két argumentumot:

| Name (Név) | Adattípus | Leírás |
|--|--|--|
|HTTP_PROXY|sztring|a proxy szeretne használni, például http://proxy:8888|
|HTTP_PROXY_CREDS|sztring|a felhasználónév: jelszó például a proxy hitelesítése szükséges hitelesítő adatokat.|

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=http://190.169.1.6:3128 \
HTTP_PROXY_CREDS=jerry:123456 \
Logging:Disk:LogLevel=Debug Logging:Disk:Format=json
```