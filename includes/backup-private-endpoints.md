---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: b8941bb4fa77cb0008cb7271681e972bc21d6588
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83659548"
---
Mostantól [privát végpontokat](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) is használhat az adatok biztonságos biztonsági mentésére a virtuális hálózaton belüli kiszolgálókról a Recovery Services-tárolóba. A privát végpont egy IP-címet használ a tár VNET. A virtuális hálózaton belüli erőforrásai és a tároló közötti hálózati forgalom a virtuális hálózatra és a Microsoft gerinc hálózatán található privát kapcsolatra is áthalad. Ezzel kiküszöbölhető a nyilvános internetről való kitettség. Az Azure-beli virtuális gépeken futó SQL-és SAP HANA-adatbázisok biztonsági mentésére és visszaállítására privát végpontok használhatók. A MARS-ügynököt használó helyszíni kiszolgálókhoz is használható.

Az Azure virtuális gép biztonsági mentése nem igényel internetkapcsolatot, ezért nincs szükség privát végpontokra a hálózat elkülönítésének engedélyezéséhez.

További információ a Azure Backup privát végpontokról [itt](https://docs.microsoft.com/azure/backup/private-endpoints)olvasható.
