---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 17a8c9580a8e69213c7f34e8ec889f7e46c6d17d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696064"
---
Ez a szakasz a biztonsági környezeti megkötés (SCC) alkalmazásának módját ismerteti. Az előzetes kiadás esetében ezek a biztonsági korlátozások ellazítására használhatók. 

1. Töltse le az egyéni biztonsági környezet korlátozását (SCC). Használja a következők egyikét: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([Nyers](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` A következő parancs letölti az ív-adathalmazt. YAML:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. SCC létrehozása

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Alkalmazza a SCC-t a szolgáltatási fiókra.

   > [!NOTE]
   > Használja ugyanazt a névteret itt és az `azdata arc dc create` alábbi parancsban. Példa: `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```