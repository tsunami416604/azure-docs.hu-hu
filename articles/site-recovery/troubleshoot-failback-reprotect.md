---
title: Hibák elhárítása feladat-visszavétel során a helyszínen az Azure-ból, és utána állítsa az Azure-bA |} Microsoft Docs
description: A cikk ismerteti az Azure-ból és védelem-újrabeállítási során vissza a helyszíni hibás gyakori hibáinak elhárítása
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: gauravd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: rajanaki
ms.openlocfilehash: d487c1fcf7fb6444c2b8489df839a6450715ae0a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshoot-errors-reported-during-the-process-of-failback"></a>Feladat-visszavétel során jelentett hibák elhárítása
Feladat-visszavétel lényegében két fő lépést foglal magában. Egy lássa el újból védelemmel az Azure-ból a helyszíni virtuális gépek számára, a második végrehajtaná a feladat-visszavétel az Azure-ból a helyszíni.

## <a name="troubleshoot-errors-when-reprotecting-a-virtual-machine-back-to-on-premises-after-failover"></a>Amikor az újbóli védelméhez vissza a helyszínen, a virtuális gép a feladatátvételt követően hibák elhárítása
Hibák a következők egyike jelenhet meg a virtuális gépek Azure-bA védelem-újrabeállítási végrehajtása közben. Hibaelhárítás, az ismertetett lépésekkel az egyes.


### <a name="error-code-95226"></a>95226. Hibakód:

*Védelem-újrabeállítási sikertelen volt, mert a virtuális gépet az Azure nem tudta elérni a helyi konfigurációs kiszolgálót.*

Ez akkor fordul elő, amikor 
1. Az Azure virtuális gép nem tudta elérni a helyszíni konfigurációs kiszolgáló és ezért sikertelen nem felderítése és a konfigurációs kiszolgáló regisztrálva. 
2. Az InMage Scout szolgáltatás a való kommunikációhoz és a helyszíni konfigurációs kiszolgáló futnia kell az Azure virtuális gépen nem fut a feladatátvétel utáni.

A probléma megoldásához
1. Gondoskodnia kell arról, hogy az Azure virtuális gép hálózati konfigurálva van, úgy, hogy a virtuális gép kommunikálhat a helyszíni konfigurációs kiszolgáló. Ehhez az szükséges, egy a helyszíni adatközpontját a helyek közötti virtuális magánhálózat beállításához, vagy ExpressRoute kapcsolat konfigurálása a magánhálózati társviszony-létesítés az Azure virtuális gép a virtuális hálózaton. 
2. Ha már rendelkezik egy hálózatot, hogy a virtuális gépet az Azure kommunikálhat a helyszíni konfigurációs kiszolgáló, majd jelentkezzen be a virtuális gépet, és ellenőrizze a "InMage Scout alkalmazásszolgáltatás". Ha azt láthatja, hogy nem fut-e az InMage Scout alkalmazásszolgáltatás, majd indítsa el kézzel a szolgáltatást, és győződjön meg arról, hogy a szolgáltatás indítási típusa automatikusra van beállítva.

### <a name="error-code-78052"></a>78052. Hibakód:
Védelem-újrabeállítási a következő hibaüzenettel meghiúsul: *védelmet a virtuális géphez nem lehetett befejezni.*

Ez akkor fordulhat elő két oknál
1. A virtuális gép, amelyek újbóli védelméhez egy Windows Server 2016. Jelenleg ez az operációs rendszer a feladat-visszavétel esetében nem támogatott, de hamarosan támogatott.
2. Már van egy virtuális gép ugyanazzal a névvel, a fő célkiszolgáló, amelyek nem vissza.

A probléma megoldásához kiválaszthatja, hogy a védelem-újrabeállítási hoz létre a gépet egy másik gazdagépet, ahol a nevek nem ütköznek egy másik fő célkiszolgáló egy másik gazdagépen. A fő célkiszolgáló egy másik gazdagépen, ha nem történik a névütközés vMotion is. Ha a meglévő virtuális gép szétszórt gép, csak átnevezheti azt, hogy az új virtuális gép is létrehozása ugyanazon az ESXi-állomáson.

### <a name="error-code-78093"></a>78093. Hibakód:

*A virtuális gép nem fut, lefagyott állapotú, vagy nem érhető el.*

A állítsa vissza a helyszíni virtuális gép keresztül egy sikertelen, a futó Azure virtuális géphez van szükség. Ez azért, hogy a mobilitási szolgáltatás regisztrálja a konfigurációs kiszolgálón a helyi és elindíthatja a replikáló által a folyamat kiszolgálóval való kommunikáció során. Ha a számítógépen helytelen hálózaton vagy nem fut (lefagyott állam vagy -leállítás), majd a konfigurációs kiszolgáló nem érhető el a mobilitási szolgáltatást a virtuális gépen, a védelem-újrabeállítási megkezdéséhez. A virtuális gépet, hogy a kommunikáció hátsó helyszíni elindíthatja indíthatja újra. Az Azure virtuális gép elindítása után a védelem-újrabeállítási feladat újraindításához

### <a name="error-code-8061"></a>8061. Hibakód:

*Az adattároló nem érhető el az ESXi-állomáson.*

Tekintse meg a [fő cél a szükséges előfeltételek](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) és a [datastores támogatja](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) feladat-visszavételhez


## <a name="troubleshoot-errors-when-performing-a-failback-of-an-azure-virtual-machine-back-to-on-premises"></a>A feladat-visszavétel egy Azure virtuális gép vissza a helyszíni végrehajtása során hibák elhárítása
Hibák a következők egyike jelenhet meg az Azure virtuális gép vissza a helyszíni feladat-visszavétel végrehajtása közben. Hibaelhárítás, az ismertetett lépésekkel az egyes.

### <a name="error-code-8038"></a>8038. Hibakód:

*Nem sikerült elindítani a helyszíni virtuális gép a hiba miatt*

Ez akkor fordul elő, ha a helyszíni virtuális gép egy gazdagépen, amelyen nincs elég memória a kiépítése nem válik.

A probléma megoldásához

1. Az ESXi-állomáson több memóriát oszthat meg.
1. a virtuális Gépet egy másik ESXi-állomáson, amely elegendő memóriával a virtuális gép rendelkezik vMotion.