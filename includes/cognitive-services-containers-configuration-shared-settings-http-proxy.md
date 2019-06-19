---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: fe1b4699a300831294c26b103d322fb83ad87d3b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179389"
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