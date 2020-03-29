---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68320487"
---
Ha http-proxyt kell konfigurálnia a kimenő kérelmek hez, használja az alábbi két argumentumot:

| Név | Adattípus | Leírás |
|--|--|--|
|HTTP_PROXY|sztring|A proxy használata, például,`http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|sztring|A proxyn keresztüli hitelesítéshez szükséges hitelesítő adatok, például felhasználónév:jelszó.|
|`<proxy-user>`|sztring|A proxy felhasználója.|
|`<proxy-password>`|sztring|A proxyhoz `<proxy-user>` társított jelszó.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
