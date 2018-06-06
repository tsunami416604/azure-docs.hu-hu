---
title: Az Azure Advisor magas rendelkezésre állású javaslatok |} Microsoft Docs
description: Azure Advisor használata az Azure-környezetekhez magas rendelkezésre állásának javítása érdekében.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 23764b476f01c30b1755c507a0cfa5ead27be91e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736551"
---
# <a name="advisor-high-availability-recommendations"></a>Magas rendelkezésre állású javaslatokat biztosít

Az Azure Advisor segítségével győződjön meg arról, és az üzleti szempontból kritikus fontosságú alkalmazások folytonosságának javításához. Advisor a magas rendelkezésre állású javaslatok beszerezheti a **magas rendelkezésre állású** az Advisor irányítópult.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Virtuális gép a hibatűrés érdekében

Az alkalmazás redundanciájának garantálása érdekében javasoljuk, hogy a virtuális gépeket legalább kettesével foglalja rendelkezésre állási csoportokba. Az Advisor azonosítja a virtuális gépek, amelyek nem részei egy rendelkezésre állási csoportot és helyezi át a rendelkezésre állási csoportok javasolja. Ez a konfiguráció biztosítja, hogy vagy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép elérhető, és megfelel-e a virtuális gép az Azure SLA-t. Lehetősége van a rendelkezésre állási készlet a virtuális gép létrehozásához, vagy a virtuális gép hozzáadása egy meglévő rendelkezésre állási csoportot.

> [!NOTE]
> Ha egy rendelkezésre állási csoport létrehozása mellett dönt, hozzá kell adnia legalább egy további virtuális gép bele. Azt javasoljuk, hogy Ön csoporthoz két vagy több virtuális gép rendelkezésre állási beállítása annak érdekében, hogy legalább egy gép érhető kimaradás során.

## <a name="ensure-availability-set-fault-tolerance"></a>Ellenőrizze a rendelkezésre állási csoport hibatűrés 

Az alkalmazás redundanciájának garantálása érdekében javasoljuk, hogy a virtuális gépeket legalább kettesével foglalja rendelkezésre állási csoportokba. Az Advisor tartalmazhat egyetlen virtuális gép rendelkezésre állási készleteket azonosítja, és azt javasolja, hogy egy vagy több virtuális gépek hozzáadása. Ez a konfiguráció biztosítja, hogy vagy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép elérhető, és megfelel-e a virtuális gép az Azure SLA-t. Kiválaszthatja a virtuális gép létrehozásához, vagy egy meglévő virtuális gép hozzáadása a rendelkezésre állási csoport.  

## <a name="ensure-application-gateway-fault-tolerance"></a>Ellenőrizze az alkalmazás átjáró hibatűrés
Kritikus fontosságú alkalmazások alkalmazás-átjárók vannak kapcsolva, az üzletmenet folytonosságának biztosításához, Advisor azonosítja alkalmazás példányai, amelyek nincsenek beállítva a hibatűrés érdekében, és javaslatot tesz, a szervizelési műveletek is. Az Advisor azonosítja közepes vagy nagy Egypéldányos alkalmazás átjárók, és hozzáadja legalább egy további példányt javasol. Egy vagy több instance kis alkalmazásátjárót azonosítja és a közepes vagy nagy termékváltozatok történő javasolja. Ezek a műveletek ellenőrizze, hogy az átjáró alkalmazáspéldányok megfelelnek a jelenlegi SLA ezekhez az erőforrásokhoz tartozó javasol.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>A teljesítmény és a virtuális gépek lemezeit megbízhatóságának javítása

Az Advisor azonosítja a virtuális gépek a standard lemezek és frissítése a premium lemezek javasolja.
 
Prémium szintű Storage nagy teljesítményű, alacsony késésű lemez I/O-igényes munkaterhelések futó virtuális gépek támogatása nyújt. Prémium szintű storage-fiókok használó virtuálisgép-lemezek SSD-meghajtót (SSD) adatait tárolják. A legjobb teljesítmény érdekében az alkalmazáshoz azt javasoljuk, hogy az áttelepített a prémium szintű Storage magas IOPS igénylő virtuális gép lemezei. 

A lemezek nem igényelnek magas iops értéket, ha szabványos Storage fenntartásuk korlátozhatja költségeket. Standard szintű tárolást virtuálisgép-lemez adatokat tárolja a (merevlemezes HDD) meghajtók SSD-k helyett. Ha szeretné, telepítse át a virtuális gépek lemezeit a prémium szintű lemezekhez. A legtöbb virtuális gép termékváltozatok Premium lemezek támogatottak. Azonban néhány esetben, ha szeretné használni a premium lemezek esetén szükség lehet a virtuális gép verziófrissítése termékváltozatok is.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>A virtuális gép adatainak véletlen törlés elleni védelme
Virtuális gép biztonsági mentése beállításával biztosítja az üzleti szempontból kritikus fontosságú adatok rendelkezésre állását, és véletlen törlés vagy -sérülés elleni védelmet nyújt.  Az Advisor azonosítja a virtuális gépek, ahol a biztonsági mentés nem engedélyezett, és azt a biztonsági mentés engedélyezését javasolja. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Ellenőrizze, hogy Azure-felhőbe szakértők eléréséhez szükség esetén
Olyan üzleti szempontból kritikus fontosságú alkalmazások és szolgáltatások futtatásakor fontos hozzáférjenek a technikai támogatási szolgálathoz, amikor szükséges. Az Advisor azonosítja a potenciális üzleti szempontból kritikus fontosságú előfizetések, amelyek nem rendelkeznek a támogatási csomag szerepel a technikai támogatási szolgálathoz, és javasolja, hogy telepítse olyan beállítás, amely magában foglalja a technikai támogatási szolgálathoz.

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Magas rendelkezésre állású javaslatok az Advisor elérése

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2.  Az Advisor irányítópultján kattintson a **magas rendelkezésre állású** fülre.

## <a name="next-steps"></a>További lépések

Advisor-javaslatokra kapcsolatos további információkért lásd:
* [Bevezetés az Azure Advisor](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Költség javaslatokat biztosít](advisor-performance-recommendations.md)
* [Teljesítmény javaslatokat biztosít](advisor-performance-recommendations.md)
* [Biztonsági javaslatokat biztosít](advisor-security-recommendations.md)

