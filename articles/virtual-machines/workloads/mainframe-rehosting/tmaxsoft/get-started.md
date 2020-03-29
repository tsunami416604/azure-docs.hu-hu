---
title: Ismerkedés a TmaxSoft OpenFrame-el az Azure virtuális gépeken
description: Az IBM z/OS nagyszámítógépes számítási feladatainak újraüzemeltetése TmaxSoft OpenFrame környezet használatával azure-beli virtuális gépeken.Rehost your IBM z/OS mainframe workloads using TmaxSoft OpenFrame environment on Azure Virtual Machines (VMs).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61485427"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Ismerkedés a TmaxSoft OpenFrame-el az Azure-ban

A TmaxSoft OpenFrame segítségével vigye át meglévő nagyszámítógépes eszközeit a Microsoft Azure-ba. Ez a népszerű újraüzemeltetési megoldás emulációs környezetet hoz létre az Azure-ban, amely lehetővé teszi az alkalmazások gyors áttelepítését. Nincs szükség újraformázásra.

## <a name="openframe-rehosting-environment"></a>OpenFrame rehosting környezet

OpenFrame-környezet beállítása az Azure-ban fejlesztési, bemutatók, tesztelési vagy éles számítási feladatok hoz. Ahogy az alábbi ábra is mutatja, az OpenFrame több összetevőt is tartalmaz, amelyek az Azure-beli nagyszámítógépes emulációs környezetet hoznak létre. Az OpenFrame online szolgáltatásai például lecserélik a nagyszámítógépes köztes szoftvereket, például az IBM Ügyfélinformáció-ellenőrző Rendszert (CICS). Az OpenFrame Batch a TJES összetevőjével felváltja az IBM nagyszámítógép feladatbeviteli alrendszerét (JES). 

![OpenFrame újrafogadási folyamat](media/openframe-01.png)

> [!NOTE]
> Az OpenFrame-környezet Azure-beli futtatásához érvényes terméklicenccel vagy a TmaxSoft próbalicencével kell rendelkeznie.

## <a name="openframe-components"></a>OpenFrame-összetevők

A következő összetevők az Azure OpenFrame-környezetének részét képezik:

- **Áttelepítési eszközök,** beleértve az OFMiner, egy megoldás, amely elemzi a nagyszámítógépek eszközök, majd áttelepíti azokat az Azure-ba.
- **Fordítók**, beleértve az OFCOBOL-t, egy fordítót, amely a nagyszámítógép COBOL programjait értelmezi; OFPLI, amely értelmezi a mainframe PL / I programok; és ofasm, egy fordító, amely értelmezi a mainframe assembler programokat.
- **Az előtér-összetevők,** beleértve a Java Enterprise User Solution (JEUS), a Java Enterprise Edition 6.OFGW tanúsítvánnyal rendelkező webalkalmazás-kiszolgálót és a 3270-es figyelőt tartalmazó OpenFrame átjáró-összetevőt.
- **Alkalmazási** környezet. Az OpenFrame Base a teljes rendszert kezelő köztes szoftver. Az OpenFrame Server Type C (OSC) felváltja a nagyszámítógép köztes szoftverét és az IBM CICS-t.
- **Relációs adatbázis**, például Tibero (látható), Oracle Database, Microsoft SQL Server, IBM Db2 vagy MySQL. Az OpenFrame-alkalmazások odbc protokollt használnak az adatbázissal való kommunikációhoz.
- **Biztonság** a TACF-en keresztül, egy szolgáltatásmodul, amely szabályozza a felhasználók hozzáférését a rendszerekhez és erőforrásokhoz. 
- **Az OFManager** egy olyan megoldás, amely az OpenFrame működési és felügyeleti funkcióit biztosítja a webes környezetben.

![OpenFrame architektúra](media/openframe-02.png)

## <a name="next-steps"></a>További lépések

- [A TmaxSoft OpenFrame telepítése az Azure-ra](./install-openframe-azure.md)
