---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81539564"
---
Mostantól [privát végpontokat](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) is használhat az adatok biztonságos biztonsági mentésére a virtuális hálózaton belüli kiszolgálókról a Recovery Services-tárolóba. A privát végpont egy IP-címet használ a tár VNET. A virtuális hálózaton belüli erőforrásai és a tároló közötti hálózati forgalom a virtuális hálózatra és a Microsoft gerinc hálózatán található privát kapcsolatra is áthalad. Ezzel kiküszöbölhető a nyilvános internetről való kitettség. Az Azure-beli virtuális gépeken futó SQL-és SAP HANA-adatbázisok biztonsági mentésére és visszaállítására privát végpontok használhatók. A MARS-ügynököt használó helyszíni kiszolgálókhoz is használható.

Az Azure virtuális gép biztonsági mentése nem igényel internetkapcsolatot, ezért nincs szükség privát végpontokra a hálózat elkülönítésének engedélyezéséhez.

>[!NOTE]
> Ez a funkció jelenleg korlátozott rendelkezésre állású, és az USA nyugati középső régiójában, az USA déli középső régiójában, az USA 2. nyugati régiójában és az USA keleti régiójában érhető el (a további Azure-régiók elérhetősége). Kérjük, töltse ki [ezt a kérdőívet](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) , azbackupnetsec@microsoft.com és küldjön nekünk e-mailt, ha szeretne privát végpontokat használni a Azure Backuphoz. A funkció használatának lehetősége a Azure Backup szolgáltatás jóváhagyását képezi.
