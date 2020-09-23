---
title: Bizalmas számítástechnikai virtuális gépek az Azure-ban
description: Ismerje meg az Intel SGX ENKLÁVÉHOZ hardverét, hogy lehetővé tegye a bizalmas számítástechnikai számítási feladatokat.
services: virtual-machines
author: JenCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/3/2020
ms.author: JenCook
ms.openlocfilehash: a7e3ade66aa4ebf7584e03b75f85c48b44537d97
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995857"
---
# <a name="azure-confidential-computing-virtual-machines-vms-overview"></a>Azure bizalmas számítástechnikai virtuális gépek (VM) – áttekintés


Az Azure az első olyan felhőalapú szolgáltató, amely a virtualizált környezetekben nyújt bizalmas számítástechnikai szolgáltatásokat. Olyan virtuális gépeket fejlesztettünk ki, amelyek absztrakt rétegként működnek a hardver és az alkalmazás között. A számítási feladatokat nagy méretekben, a redundancia és a rendelkezésre állási lehetőségek használatával futtathatja.  

## <a name="intel-sgx-enabled-virtual-machines"></a>Intel SGX ENKLÁVÉHOZ-kompatibilis Virtual Machines

Az Azure bizalmas számítástechnikai virtuális gépeken a CPU hardverének egy része az alkalmazás kódjának és adatainak egy részére van fenntartva. Ez a korlátozott rész az enklávé. 

![VM-modell](media/overview/hardware-backed-enclave.png)

Az Azure bizalmas számítástechnikai infrastruktúrája jelenleg a virtuális gépek (VM-EK) speciális SKU-jának részét alkotja. Ezek a virtuális gépek az Intel-processzorokon futnak a szoftveres Guard bővítménnyel (Intel SGX ENKLÁVÉHOZ). Az [Intel SGX enklávéhoz](https://intel.com/sgx) az a komponens, amely lehetővé teszi a bizalmas számítástechnikai szolgáltatással való fokozott védelmet. 

Napjainkban az Azure az Intel SGX ENKLÁVÉHOZ technológiára épülő [DCsv2-sorozatot](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) kínálja a hardveres enklávé létrehozásához. Létrehozhat biztonságos enklávé-alapú alkalmazásokat az DCsv2-sorozatú virtuális gépeken való futtatáshoz az alkalmazásadatok és a használatban lévő kódok védelme érdekében. 

[További](virtual-machine-solutions.md) információ az Azure bizalmas számítástechnikai virtuális gépek hardveres megbízható enklávékkal történő üzembe helyezéséről.

## <a name="enclaves"></a>Enklávék

Az enklávék a hardver processzorának és memóriájának biztonságos részei. Az enklávéban még egy hibakeresővel sem lehet megtekinteni az adatelemeket vagy a kódokat. Ha a nem megbízható kód megkísérli módosítani a tartalmat az enklávé memóriájában, a környezet le lesz tiltva, és a rendszer megtagadja a műveleteket.

Alapvetően úgy gondolja, hogy egy enklávé biztonságos mező. A mezőbe a titkosított kódot és az adatmezőket helyezheti el. A mező kívülről nem láthat semmit. Az enklávénak egy kulcsot kell adnia az adatvisszafejtéshez, majd újra fel kell dolgoznia és titkosítania kell az adatközpontot az enklávéból való kiküldésük előtt.

Az egyes enklávék számára a titkosított oldal gyorsítótára (EPC) mérete határozza meg, hogy az egyes enklávék mennyi memóriát tárolhatnak. A nagyobb DCsv2 virtuális gépek több EPC memóriával rendelkeznek. Olvassa el a [DCsv2-specifikációk](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) lapot a virtuális gép maximális számaként.



### <a name="developing-applications-to-run-inside-enclaves"></a>Alkalmazások fejlesztése a enklávékban való futtatáshoz
Az alkalmazások fejlesztése során a [szoftvereszközök](application-development.md) segítségével a kód és az adatközpontban tárolt adatvédelmek részeinek védelmét is elvégezheti. Ezek az eszközök biztosítják, hogy a kód és az adatai nem tekinthetők meg és nem módosíthatók a megbízható környezeten kívüli személyek számára. 

## <a name="next-steps"></a>Következő lépések
- A megoldások Azure bizalmas számítástechnikai virtuális gépeken való üzembe helyezésére vonatkozó [ajánlott eljárások beolvasása](virtual-machine-solutions.md) .
- [DCsv2-sorozatú virtuális gép üzembe helyezése](quick-create-portal.md)
- [Enklávé-kompatibilis alkalmazás fejlesztése](application-development.md) az OE SDK használatával