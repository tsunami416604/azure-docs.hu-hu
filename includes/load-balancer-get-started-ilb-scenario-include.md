---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: f0e575af51f952a80fe42102b033727713c75cf9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60398790"
---
## <a name="configuration-scenario"></a>Konfigurációs forgatókönyv

Ebben a forgatókönyvben belső terheléselosztót hozunk létre egy virtuális hálózaton a következő ábrán látható módon:

![Belső terheléselosztói forgatókönyv](./media/load-balancer-get-started-ilb-scenario-include/figure1.png)

A forgatókönyv konfigurációja a következő:

* Két virtuális gép: **DB1** és **DB2**
* A belső terheléselosztó végpontjai
* Egy belső terheléselosztó
