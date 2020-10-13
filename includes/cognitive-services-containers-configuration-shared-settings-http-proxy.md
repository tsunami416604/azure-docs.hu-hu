---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: ce4cc68826b39b5707549afc799d2d214e8876c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88639180"
---
Ha HTTP-proxyt kell konfigurálnia a kimenő kérelmek végrehajtásához, használja a következő két argumentumot:

| Name (Név) | Adattípus | Leírás |
|--|--|--|
|HTTPS_PROXY|sztring|A használandó proxy, például: `https://proxy:8888`<br>`<proxy-url>`|
|HTTPS_PROXY_CREDS|sztring|A proxyn való hitelesítéshez szükséges hitelesítő adatok, például Felhasználónév: jelszó.|
|`<proxy-user>`|sztring|A proxy felhasználója.|
|`<proxy-password>`|sztring|A `<proxy-user>` proxyhoz tartozó jelszó.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTPS_PROXY=<proxy-url> \
HTTPS_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
