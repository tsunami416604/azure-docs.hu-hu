---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 9911b1c92bdca6c0cdf064ea484cfb603e659467
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712453"
---
Ha egy HTTP-proxy konfigurálása, hogy a kimenő kérelmek van szüksége, használja a két argumentumot:

| Name (Név) | Adattípus | Leírás |
|--|--|--|
|HTTP_PROXY|Karakterlánc|a proxy szeretne használni, például `http://proxy:8888`<br><proxy-url>|
|HTTP_PROXY_CREDS|Karakterlánc|a felhasználónév: jelszó például a proxy hitelesítése szükséges hitelesítő adatokat.|
|`<proxy-user>`|sztring|A felhasználó a proxy.|
|`proxy-password`|Karakterlánc|A társított jelszót `<proxy-user>` a proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```