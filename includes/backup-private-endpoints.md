---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539564"
---
Most már [használhatja a privát végpontok](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) biztonsági másolatot az adatokbiztonságosan a kiszolgálók egy virtuális hálózaton belül a Recovery Services-tároló. A privát végpont a virtuális hálózat címterületéről származó IP-címet használ a tárolóhoz. A virtuális hálózaton belüli erőforrások és a trezor közötti hálózati forgalom a virtuális hálózaton és a Microsoft gerinchálózatán lévő privát kapcsolaton keresztül halad. Ez kiküszöböli a nyilvános internetről származó kitettséget. A privát végpontok az Azure-beli virtuális gépeken futó SQL- és SAP HANA-adatbázisok biztonsági mentéséhez és visszaállításához használhatók. Azt is fel lehet használni a helyszíni kiszolgálók a MARS-ügynök használatával.

Az Azure VM biztonsági mentésnem igényel internetkapcsolatot, és így nem igényel privát végpontokat a hálózati elkülönítés engedélyezéséhez.

>[!NOTE]
> Ez a funkció jelenleg korlátozott anammár elérhető, és az USA nyugati középső régiójában, az USA déli régiójában, az USA nyugati régiójában 2 és USA keleti régiójában érhető el (más Azure-régiókban való elérhetőség). Kérjük, töltse ki ezt azbackupnetsec@microsoft.com a [felmérést,](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) és írjon nekünk, ha szeretné használni a privát végpontok az Azure Backup. A funkció használatának lehetőségét az Azure Backup szolgáltatás jóvá kell hagynia.
