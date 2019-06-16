---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: fe1b4699a300831294c26b103d322fb83ad87d3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66116681"
---
Ha egy HTTP-proxy konfigurálása, hogy a kimenő kérelmek van szüksége, használja a két argumentumot:

| Name (Név) | Adattípus | Leírás |
|--|--|--|
|HTTP_PROXY|string|a proxy szeretne használni, például `http://proxy:8888`<br><proxy-url>|
|HTTP_PROXY_CREDS|string|a felhasználónév: jelszó például a proxy hitelesítése szükséges hitelesítő adatokat.|
|`<proxy-user>`|string|A felhasználó a proxy.|
|`proxy-password`|string|A társított jelszót `<proxy-user>` a proxy.|
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