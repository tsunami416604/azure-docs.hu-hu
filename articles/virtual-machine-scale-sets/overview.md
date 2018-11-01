---
title: Azure virtuálisgép-méretezési csoportok – Áttekintés | Microsoft Docs
description: Tudnivalók az Azure virtuálisgép-méretezési csoportokról és az alkalmazások automatikus méretezéséről
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 03/27/2018
ms.author: manayar
ms.openlocfilehash: 23c04f85c994a8b300ffec23b4660d3d431ef69b
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740419"
---
# <a name="what-are-virtual-machine-scale-sets"></a>Mik a virtuálisgép-méretezési csoportok?
Az Azure-beli virtuálisgép-méretezési csoportok segítségével azonos, elosztott terhelésű virtuális gépek csoportját hozhatja létre és kezelheti. A virtuálisgép-példányok száma automatikusan növelhető vagy csökkenthető a pillanatnyi igényeknek megfelelően vagy egy meghatározott ütemezés szerint. A méretezési csoportok biztosítják az alkalmazások magas rendelkezésre állását, és lehetővé teszik nagy számú virtuális gép központi felügyeletét, konfigurálását és frissítését. A virtuálisgép-méretezési csoportokkal nagyobb léptékű szolgáltatások építhetők ki a nagy számítási igényű, „big data” típusú és tárolóalapú számítási feladatokhoz.


## <a name="why-use-virtual-machine-scale-sets"></a>Miért érdemes virtuálisgép-méretezési csoportokat használni?
A redundancia és a jobb teljesítmény biztosítása érdekében az alkalmazások általában több példány között vannak szétosztva. Az ügyfelek az alkalmazást egy terheléselosztón keresztül érhetik el, amely elosztja a kérelmeket az alkalmazás példányai között. Ha egy alkalmazáspéldány karbantartásra vagy frissítésre szorul, az ügyfelek egy másik elérhető példányhoz irányíthatók. A megnövekedett ügyféligények kielégítéséhez szükség lehet az alkalmazást futtató alkalmazáspéldányok számának növelésére.

Az Azure-beli virtuálisgép-méretezési csoportok biztosítják a szükséges felügyeleti képességeket a több virtuális gépen futó alkalmazásokhoz, az [erőforrások automatikus méretezéséhez](virtual-machine-scale-sets-autoscale-overview.md) és a forgalom terheléselosztásához. A méretezési csoportok legfontosabb előnyei a következők:

- **Több virtuális gép egyszerű létrehozása és kezelése**
    - Ha egy alkalmazás sok virtuális gépen fut, fontos fenntartani egy állandó, a környezet egészében érvényesülő konfigurációt. Az alkalmazás megbízható teljesítménye érdekében fontos, hogy minden virtuális gépen ugyanaz legyen a gép mérete, a lemezkonfiguráció és az alkalmazás telepített verziója.
    - A méretezési csoportok használatakor a rendszer az összes virtuálisgép-példányt egyazon alapul szolgáló operációsrendszer-képből és konfigurációból hozza létre. Ez a megközelítés lehetővé teszi több száz virtuális gép egyszerű kezelését további konfigurációs feladatok és hálózatkezelés nélkül.
    - A méretezési csoportok az alapszintű, 4 rétegű forgalomelosztáshoz az [Azure Load Balancer](../load-balancer/load-balancer-overview.md), a speciális, 7 rétegű forgalomelosztáshoz pedig az [Azure Application Gateway](../application-gateway/application-gateway-introduction.md) használatát támogatják.

- **Magas rendelkezésre állás és rugalmas alkalmazások**
    - A méretezési csoportok segítségével az alkalmazások több példányban futtathatók. Ha valamelyik virtuálisgép-példány problémába ütközik, az ügyfelek továbbra is elérhetik az alkalmazást valamelyik másik példányon keresztül, minimális megszakítással.
    - A rendelkezésre állás fokozása érdekében a [rendelkezésre állási zónák](../availability-zones/az-overview.md) használatával a méretezési csoportokhoz tartozó virtuálisgép-példányok automatikusan eloszthatók egyetlen adatközponton belül, vagy több adatközpont között.

- **Az alkalmazások automatikus, az erőforrásigényekhez illeszkedő méretezhetősége**
    - Az alkalmazásokkal szemben támasztott ügyféligények a nap vagy a hét folyamán megváltozhatnak. A méretezési csoportok képesek automatikusan növelni a virtuálisgép-példányok számát, ha az igény nő, illetve csökkenteni, ha visszaesik.
    - Az automatikus méretezés segít minimális szinten tartani az alkalmazást futtató virtuálisgép-példányok számát alacsony igényszint esetén, és elfogadható teljesítményt biztosít az ügyfeleknek további VM-példányok hozzáadásával, amikor igénynövekedés tapasztalható. Ez a képesség segít a költségek csökkentésében, és hatékony módon, csakis szükség esetén hoz létre Azure-erőforrásokat.

- **Működtetés nagy léptékben**
    - A méretezési csoportok akár 1000 virtuálisgép-példányt is tartalmazhatnak. Ha saját VM-rendszerképeit hozza létre és tölti fel, legfeljebb 300 VM-példánya lehet.
    - Az éles környezetben végzett számítási feladatokhoz a legjobb teljesítmény érdekében használja az [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) és a [Premium Storage](../virtual-machines/windows/premium-storage.md) szolgáltatást.


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Mi a különbség a virtuális gépek és a méretezési csoportok között?
A méretezési csoportokat virtuális gépek alkotják. A méretezési csoportok biztosítják az alkalmazások futtatásához és méretezéséhez szükséges felügyeleti és automatizálási rétegeket. Helyettük manuálisan is létrehozhat és felügyelhet önálló virtuális gépeket, illetve meglévő eszközök integrálásával is kialakíthat egy hasonló szintű automatizációt. A következő táblázat összefoglalja a méretezési csoportok előnyeit több virtuálisgép-példány manuális felügyeletével szemben.

| Forgatókönyv                           | Virtuális gépek manuális csoportja                                                                    | Virtuálisgép-méretezési csoport |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| További virtuálisgép-példányok hozzáadása        | A létrehozás, a konfigurálás és a megfelelőség biztosítása manuális folyamatokkal történik                             | Automatikus létrehozás központi konfigurációból |
| Forgalom kiegyensúlyozása és elosztása | Manuálisan kell létrehozni és konfigurálni egy Azure-terheléselosztót vagy alkalmazásátjárót      | Automatikus létrehozás és integrálás egy Azure-terheléselosztóval vagy alkalmazásátjáróval |
| Magas rendelkezésre állás és redundancia   | Manuálisan kell létrehozni a rendelkezésre állási csoportot, illetve elosztani és nyomon követni a virtuális gépeket a rendelkezésre állási zónákban | A virtuálisgép-példányok elosztása a rendelkezésre állási zónák vagy rendelkezésre állási készletek között automatikusan történik |
| Virtuális gépek méretezése                     | Manuális monitorozás és Azure Automation                                                 | Automatikus méretezés a gazdagép metrikái, a vendég metrikái, az Application Insights vagy ütemezés alapján |

A méretezési csoportok nem járnak többletköltségekkel. Csak a mögöttes számítási erőforrások, például a virtuálisgép-példányok, a terheléselosztó vagy a felügyelt lemezes tárolók használatáért kell fizetni. A felügyeleti és automatizálási szolgáltatások, például az automatikus méretezés és a redundancia, nem járnak többletköltségekkel a virtuális gépek használatán túl.


## <a name="next-steps"></a>További lépések
Első lépésként hozza létre első virtuálisgép-méretezési csoportját az Azure Portalon.

> [!div class="nextstepaction"]
> [Méretezési csoport létrehozása az Azure Portalon](quick-create-portal.md)
