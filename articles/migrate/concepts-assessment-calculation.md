---
title: "Azure áttelepítése Assessment számításokban |} Microsoft Docs"
description: "Értékelési számítások az Azure áttelepítése szolgáltatás áttekintése."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 39a63769-31eb-49f9-9089-4d3e4e88a412
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: f00825ff9a5018e67672ce452f01130f84919e52
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="assessment-calculations"></a>Értékelés számítások

[Az Azure áttelepítése](migrate-overview.md) értékelésére a helyszíni munkaterhelések Azure való áttelepítésre. Ez a cikk értékelése kiszámítási módját ismerteti.



## <a name="overview"></a>Áttekintés

Egy Azure áttelepítése-nek három fázisból áll. Értékelési kezdődik-e egy alkalmasságát elemzés, teljesítmény-alapú méretezési becsléseket követ, és végül a havi költségeket becslése. A gép csak áthelyezése később, ha az előzőre adja át. Például a gép az Azure Alkalmazhatóság ellenőrzése sikertelen, ha meg van jelölve, céljaira nem használható Azure, és a méretezés és a költségszámítás nem számítható ki. 


## <a name="azure-suitability-analysis"></a>Az Azure alkalmasságát elemzés

Az Azure-bA áttelepíteni kívánt gépeknek meg kell felelniük, Azure-követelmények és korlátozások. Például ha egy helyszíni virtuális lemez több mint 4 TB-os, azt nem kell Azure-platformon futó. Az Azure megfelelőség-ellenőrzéseket az alábbi táblázat foglalja össze. 

**Jelölőnégyzet** | **Részletek**
--- | ---
**Indítási típus** | A Vendég operációsrendszer-lemez rendszerindító típusúnak kell lennie, és a BIOS, nem UEFI.
**Magok** | A gépek magok száma meg kell egyeznie (vagy kevesebb, mint) magok (32) maximális száma egy Azure virtuális gép támogatott.<br/><br/> Korábbi teljesítmény érhető el, ha az összehasonlításhoz használt magok Azure áttelepítése tekinti. Ha egy megerősítő tényező assessment beállításaiban van megadva, a rendszer megszorozza túlterhelt magok száma a megerősítő tényező.<br/><br/> Ha nincs teljesítményelőzményei, a megerősítő Authentication alkalmazása nélkül Azure áttelepítéséhez használja a lefoglalt magok.
**Memória** | A memória mérete meg kell egyeznie (vagy kevesebb, mint) egy Azure virtuális gép számára engedélyezett maximális memóriaméretet (448 GB). <br/><br/> Korábbi teljesítmény érhető el, ha az összehasonlításhoz használt memória Azure áttelepítése tekinti. Ha egy megerősítő tényező van megadva, a rendszer megszorozza a használt memória a megerősítő tényező.<br/><br/> Ha nincsenek előzményei a lefoglalt memória használata esetén a megerősítő Authentication alkalmazása nélkül.<br/><br/> 
**Windows Server 2003-2008 R2** | 32 bites és 64 bites támogatása.<br/><br/> Azure csak a legjobb elérhető támogatást nyújt.
**Windows Server 2008 R2 minden szervizcsomag-verzió** | 64 bites támogatás.<br/><br/> Azure teljes támogatást nyújt.
**Windows Server 2012 & összes szervizcsomag-verzió** | 64 bites támogatás.<br/><br/> Azure teljes támogatást nyújt.
**Windows Server 2012 R2 & összes szervizcsomag-verzió** | 64 bites támogatás.<br/><br/> Azure teljes támogatást nyújt.
**Windows Server 2016 & összes szervizcsomag-verzió** | 64 bites támogatás.<br/><br/> Azure teljes támogatást nyújt.
**A Windows ügyfél 7 és újabb verziók** | 64 bites támogatás.<br/><br/> Azure csak a Visual Studio előfizetői támogatást nyújt.
**Linux** | 64 bites támogatás.<br/><br/> Azure teljes mértékben támogatja ezek [operációs rendszerek](../virtual-machines/linux/endorsed-distros.md).
**Tároló lemez** | A lemez mérete lefoglalt kell lennie 4 TB-os (4096 GB) vagy kisebb.<br/><br/> A géphez csatolt lemezek száma 65 vagy kevesebb, beleértve az operációsrendszer-lemezképet kell lennie. 
**Hálózat** | A gép 32 kell lennie, vagy kevesebb hálózati adapter nem csatlakoztatható.


## <a name="performance-based-sizing"></a>Teljesítmény-alapú méretezése

Után a gép Azure alkalmas van megjelölve, Azure áttelepítése felel meg azt a Virtuálisgép-méretet, a következő feltételek alapján, az Azure-ban:

- **Tárolási ellenőrzés**: Azure áttelepítése megkísérli minden a gép az Azure-ban a lemez csatlakoztatott lemez:-Azure áttelepítése szorozza meg az i/o másodpercenként (IOPS) a megerősítő tényezővel. Azt is Többszörösök minden lemez megerősítő tényező (a MB/s) átviteli sebesség. Ez biztosítja, hogy a hatályos IOPS és-átviteli sebességet. Ennek alapján, a lemez standard vagy prémium szintű Azure-ban Azure át rendeli.
    - Ha a szolgáltatás nem találja a szükséges IOPS & átviteli lemezzel, az Azure-jelöli céljaira nem használható gépként.
    - Ha úgy találja, hogy a megfelelő lemezek egy készlete, Azure áttelepítése kiválasztja a meglévők közül, amelyek támogatják a tárolási redundancia módját, valamint a assessment beállításokban megadott helyre.
    - Ha több jogosult lemezek vannak, a másikat a legalacsonyabb költséget választja ki.
- **Tárolási lemez teljesítmény**: [további](../azure-subscription-service-limits.md#storage-limits) kapcsolatos Azure korlátozza a lemez és virtuális gép.
- **Lemez típusa**: Azure áttelepítése támogatja a csak a felügyelt lemezeket.
- **Ellenőrizze a hálózati**: Azure áttelepítése próbál meg megtalálni egy Azure virtuális Gépen, amely képes támogatni a hálózati adapterek száma a helyi számítógépen.
    - Ehhez az szükséges, az aggregátumok / másodperc (MBps) a gép (a kimenő hálózati) kívül az összes hálózati adapter között továbbított adatok, és a megerősítő tényező vonatkozik összesített száma. A szám, ha egy Azure virtuális Gépen, amely képes támogatni a szükséges hálózati teljesítmény kereséséhez használt.
    - Azure áttelepítése veszi a hálózati beállításokat a virtuális gépről, és feltételezi, hogy egy hálózati az adatközponton kívülről kell.
    - Ha hálózati teljesítményadatai nem érhető el, csak a hálózati adapter száma a Virtuálisgép-méretezési tekinthető meg.
- **Jelölőnégyzet számítási**: után tárolási és hálózati követelményei kiszámítása, Azure át úgy ítéli meg számítási követelmények:
    - A teljesítményadatokat a virtuális gép számára érhető el, ha megvizsgálja a túlterhelt maggal és a memória, és a megerősítő tényező vonatkozik. Ez a szám alapján, megkísérli annak az Azure-ban található a megfelelő Virtuálisgép-méretet.
    - Ha található nem megfelelő méretű, a gép van megjelölve céljaira nem használható az Azure-bA.
    - Ha egy megfelelő méretű található, a tárolási és hálózatkezelési számítások Azure áttelepítése vonatkozik. Ezt követően végrehajtja, helyét és árképzési szint beállításait, a végső virtuális gép mérete javaslat.


## <a name="monthly-cost-estimation"></a>Havi költség becslése

Méretezési javaslatok befejezését követően a Azure áttelepítése az áttelepítés utáni számítási és tárolási költségek számítja ki.

- **Költség számítási**: a javasolt Azure Virtuálisgép-méretet használó, Azure telepítse át a számlázási API-t használja havi költségének kiszámítására, a virtuális gép számára. A számítási az operációs rendszer, frissítési garancia, helyét és pénznem beállítások figyelembe vesz igénybe. A költségeket von gépeire, teljes havi számítási költségének kiszámítására.
- **Tárolási költségek**: A havi tárolási költség, a gép összes lemeze havi költségét összesítésével számítható a géphez csatlakozik. Az Azure áttelepítése a teljes havi tárolási költségek a tárolási költségeket az összes gépet összesítésével számítja ki. A számítási jelenleg nem veszi figyelembe assessment beállításaiban megadott ajánlatokat.

Az értékelés beállításaiban megadott pénznemben költségek jelennek meg. 


## <a name="next-steps"></a>Következő lépések

[Helyszíni VMware virtuális gépek értékelés beállítása](tutorial-assessment-vmware.md)
