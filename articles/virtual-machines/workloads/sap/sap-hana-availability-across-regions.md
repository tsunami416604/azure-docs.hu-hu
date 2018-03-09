---
title: "Azure-régiók SAP HANA elérhetőségét |} Microsoft Docs"
description: "Azure virtuális gépeken futó SPA HANA ismerteti rendelkezésre állási szempontok áttekintése"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: msjuergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 052394884f85d527caa88ff6c9464af669f47bb5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-across-azure-regions"></a>Azure-régiók közötti SAP HANA rendelkezésre állása
SAP HANA rendelkezésre állási különböző Azure-régiók közötti forgatókönyvek ebben a cikkben leírt és ismertet. Mivel, külön Azure-régiók rendelkezik-e közöttük nagyobb távolság, ebben a cikkben szereplő különleges szempontok vannak.

## <a name="motivation-to-deploy-across-multiple-azure-regions"></a>Kifejlesztésének központi telepítése a teljes több Azure-régiók
Különböző Azure-régiók nagyobb távolság el egymástól. Függő geopolitikai régió, ez lehet miles több száz vagy akár több ezer miles, például az Amerikai Egyesült Államokban. A különböző Azure-régiók közötti távolság, mert közötti hálózati forgalom a két különböző Azure-régiók lép jelentős hálózati oda késés telepített eszközök. Jelentős elég az SAP-munkaterhelés jellemző két SAP HANA-példányai között szinkron adatcsere kizárása. A másik oldalon hogy gyakran szemben a tényt, hogy nincs-e az elsődleges adatközpont és egy másodlagos adatközpontba közötti távolságot a megadott követelmény természeti katasztrófa esetén szerezze meg a legnagyobb területen biztosítása érdekében. Például, hogy a karibi, és hogy terület találati szeptember és október 2017 hurrikánok. Vagy legalább egy legkisebb távolságot követelményt. A legtöbb esetben ügyfél, a legkisebb távolságot definícióját meg kell keresztül a rendelkezésre állás kialakítása a [Azure-régiókat](https://azure.microsoft.com/regions/). Mivel túl nagy közötti távolság két Azure-régiók HANA szinkron replikáció módját, RTO és a helyreállítási Időkorlát használandó válthatja ki, hogy egy régión belül a rendelkezésre állási konfigurációk telepítése, és egy második további központi telepítéseket, majd kiegészítése a régióban.

Figyelembe kell venni ezeket a beállításokat egy másik szempont a feladatátvétel és az ügyfél átirányítási. A feltételezése, hogy két különböző Azure-régiók mindig SAP HANA-példányok közötti feladatátvétel kézi feladatátvételre-e. Mivel az SAP HANA replikációs replikációs mód értéke aszinkron, lehetőség van, hogy az elsődleges HANA példány véglegesített adatokat nem tette, még a másodlagos HANA-példányra. Ezért az Automatikus feladatátvétel konfigurációk, ahol a replikáció az aszinkron nem fogadható. Még az ellenőrzött kézi feladatátvételre, mint a feladatátvétel a gyakorlatban kell intézkedéseket, győződjön meg arról, hogy az véglegesített adatokat az elsődleges oldalon végzett azt a másodlagos példány más Azure-régiót manuálisan lép érvénybe.
 
Mivel az Azure Vnetekhez, amely vannak telepítve, a második az Azure-régió, a SAP HANA-ügyfelek vagy meg kell változtatnia a konfigurálásukban felhasználhatja vagy módon jobban, amelyre a lépéseket, hogy a névfeloldás módosítani kell a különböző IP-címtartomány kezelhető. Úgy hogy az első a rendszer az ügyfeleket az új másodlagos tartozó IP-címét. Az SAP foglalkozó [ügyfél kapcsolat helyreállítása után felvásárlási](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html) további részleteket állapotba kerül.   

## <a name="simple-availability-between-two-azure-regions"></a>Két Azure-régiók közötti egyszerű rendelkezésre állása
Ebben a forgatókönyvben azt módba nem történő bármely rendelkezésre állási konfiguráció egy helyen. Azonban az igény szerinti szeretné, hogy a munkaterhelés egy katasztrófa esetén és kiszolgálása között. Tipikus esetekben a rendszerek, például, amelyek nem éles rendszerek esetén. Bár, a rendszer le fél napos vagy akár naponta képes elviselni, 48 óra vagy több nem használható a rendszer nem engedélyezi. Annak érdekében, hogy a telepítő kevesebb költséges, végezzen el egy másik, amely akkor is kevésbé fontos adatmegőrzési funkciókkal, mint a cél virtuális gép, vagy a virtuális gép másodlagos régióban kisebb méretet, és úgy, hogy az adatok előzetes betöltése nem. Mivel a feladatátvételi manuális lesz, és vonja maga után, valamint a teljes alkalmazás verem feladatátvételi számos további lépéseket, állítsa le a virtuális gép, méretezze át, és indítsa el újra, a virtuális gép további időre elfogadható.

> [!NOTE]
> Adatok előzetes betöltését a HANA replikációs cél, nélkül is szüksége legalább 64 GB memóriával és, hogy elég memória a sortárindex adatok megtartása a memóriában a cél-példány felett.

![Két virtuális gépek két régió keresztül](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> Ebben a konfigurációban, nem adja meg az RPO = 0, mivel a HANA replikációs mód aszinkron. Ha meg kell adnia az RPO = 0, ez a konfiguráció nincs választott konfigurációs.

A konfigurációban kisebb módosítást konfigurálhatja az adatok előzetes betöltése sikerült. Azonban jellegéből manuális feladatátvétel és a tényt, hogy az alkalmazás rétegek át kell helyezni a második régió, valamint azt nem célszerű előre az adatok betöltésére. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Rendelkezésre állási egyesítése egyetlen régión belül és között régiók 
Rendelkezésre állási belül és között régiók kombinációja alapján is vezeti:

- A helyreállítási Időkorlát követelmény = 0 belül egy Azure-régiót.
- Nem kívánok vagy kiterjedhet globális műveletek vonatkozzon a vállalat által a fő természeti katasztrófa, amely hatással van egy nagyobb területet. Mint az esetben egyes hurrikánok, amely az elmúlt néhány évben a karibi találati által.
- Szabályozások készlete, amelyek egyértelműen túl milyen Azure rendelkezésre állási zónák elsődleges és másodlagos hely közötti távolság igény szerinti biztosíthat.

 
Ebben az esetben, mely SAP hívások konfigurálhat egy [SAP HANA Multitier rendszer replikációs konfiguráció](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) HANA rendszer replikáció. Az architektúra alábbihoz hasonlóan fog kinézni:

![három virtuális gépek két régió keresztül](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Ez a konfiguráció biztosítja az RPO = 0, az elsődleges régióban kis RTO időpontokat, és emellett biztosítja csökkenő növekvő RPO esetén áthelyezésre keresztül a második régióban. A második régióban RTO időpontok függenek e konfigurálnia adatok előzetes betöltését, vagy nem. Sok ügyfél esetben a virtuális Gépet a másodlagos régióban futtatására szolgál egy tesztrendszerhez. Adott kihasználtsága miatt az adatokat nem lehet előre betölteni.

> [!NOTE]
> Mivel a 2. rétegbeli (az elsődleges régióban szinkron replikáció) az 1. rétegbeli fog HANA replikációs a logreplay művelet módot használ, a 2. szint és a réteg 3 (a másodlagos helyre replikálásával) közötti replikációt nem lehet delta_datashipping művelet mód. Részleteit, valamint a működési mód bizonyos korlátozások vonatkoznak az SAP szerint van dokumentálva [SAP HANA replikációs művelet módjainak](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>További lépések
Ha részletes útmutatás a konfiguráció, az Azure-ban, olvassa el a cikkek:

- [A telepítő SAP HANA replikációs az Azure virtuális gépeken](sap-hana-high-availability.md)
- [A SAP, a rendszer replikációval SAP Hana-rész 4 – Azure magas rendelkezésre állás](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
