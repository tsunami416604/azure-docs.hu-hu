---
title: Azure-régiók SAP HANA elérhetőségét |} Microsoft Docs
description: Rendelkezésre állási szempontjai SAP HANA több Azure-régiók az Azure virtuális gépeken futó áttekintése.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: edbd1885dd529e4ccd38f2012d56865a2147f64d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30842271"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Azure-régiók közötti SAP HANA-elérhetősége

Ez a cikk ismerteti a különböző Azure-régiók közötti SAP HANA rendelkezésre állási kapcsolatos forgatókönyvek. Azure-régiók közötti távolság, mert a több Azure-régiók SAP HANA rendelkezésre állási beállítása szempontot magában foglalja.

## <a name="why-deploy-across-multiple-azure-regions"></a>Miért telepítése több Azure-régiók között

Azure-régiók gyakran elválasztott nagy távolságra. Attól függően, hogy a geopolitikai régió Azure-régiók közötti távolság lehet miles több száz vagy akár több ezer miles, például az Amerikai Egyesült Államokban. A távolság miatt két különböző Azure-régiók üzembe helyezett eszközök közötti hálózati forgalom jelentős hálózati oda késés felhasználói élmény. A várakozási fontos elég a tipikus SAP munkaterhelések két SAP HANA-példányai között szinkron adatcsere kizárása. 

Másrészről szervezetek sokszor távolság követelmény az elsődleges adatközpont helye és másodlagos adatközpontba között. Távolság követelmény segít a rendelkezésre állást nyújtanak, egy szélesebb körű földrajzi helyen természeti katasztrófa esetén. Például a hurrikánok, amelyek közvetlenül a karibi, és hogy szeptember és október 2017. A szervezeténél esetleg legalább egy legkisebb távolságot követelményt. Az Azure ügyfelek esetén a legkisebb távolságot definition meg kell keresztül a rendelkezésre állás kialakítása a [Azure-régiók](https://azure.microsoft.com/regions/). Két Azure-régiók közötti távolság mérete túl nagy a HANA szinkron replikáció mód használatára, mert RTO-és a helyreállítási Időkorlát válthatja ki, hogy egy régió tartozik a rendelkezésre állási konfigurációk telepítése, és egy második további központi telepítéseket, majd kiegészítése a régióban.

Figyelembe kell venni ebben a forgatókönyvben szerepet játszó másik tényező feladatátvételi és átirányítási ügyfél. A feltételezése, hogy két különböző Azure-régiók mindig SAP HANA-példányok közötti feladatátvétel kézi feladatátvételre-e. Az SAP HANA replikációs replikációs mód értéke aszinkron, mert van egy lehetséges, hogy az elsődleges HANA példány véglegesített adatokat nem még tette a másodlagos HANA-példányra. Ezért automatikus feladatátvétel nem lehet beállítani az konfiguráció aszinkron a replikáció esetén. Még az manuálisan ellenőrzött feladatátvételi, mint a feladatátvétel a gyakorlatban kell annak érdekében, hogy az véglegesített adatokat az elsődleges oldalon végzett azt a másodlagos példány előtt manuálisan más Azure-régiót intézkedéseket.
 
Azure-beli virtuális hálózat egy másik IP-címtartományt használja. A második az Azure-régió az IP-címek vannak telepítve. Igen, vagy módosítani kell a SAP HANA-ügyfél konfigurációjának, vagy lehetőleg kell létrehoznia a névfeloldás megváltoztatásához szükséges lépések. Ezzel a módszerrel a rendszer az ügyfeleket az új másodlagos hely kiszolgáló IP-címre. További információkért tekintse meg az SAP cikket [ügyfél kapcsolat helyreállítása után felvásárlási](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Két Azure-régiók közötti egyszerű rendelkezésre állása

Előfordulhat, hogy nem kívánja bevezetni a rendelkezésre állási konfiguráció egyetlen régión belül, de továbbra is fennáll az igény szerinti kell rendelkeznie a munkaterhelést, ha katasztrófa történik és kiszolgálása között. Ilyen rendszerek jellemző példái azok éles rendszerek. Annak ellenére, hogy a rendszer le fél napos vagy akár naponta fenntartható, a rendszer nem érhető el, 48 óra vagy több nem engedélyezi. Ahhoz, hogy a telepítő kevésbé költséges, még akkor is kevésbé fontos a virtuális gép egy másik rendszer fut. A többi rendszer funkció célhelye. A virtuális Gépet a másodlagos régióban kisebb méretet, és válassza ki az adatok betöltésére nem is. Mivel a feladatátvétel kézi-e, és terjed ki a teljes alkalmazás verem feladatátvételi számos további lépéseket, a további le a virtuális Gépet, méretezze át, és indítsa újra a virtuális gép ideje elfogadható.

> [!NOTE]
> Akkor is, ha a HANA rendszer replikációs cél nem használunk adatok preload, legalább 64 GB memóriát kell. Elegendő memória a sortárindex adatok megtartja az a cél-példány memóriáját 64 GB-os mellett is kell.

![Két virtuális gépek két régió keresztül ábrája](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> Ebben a konfigurációban, nem adja meg az RPO = 0, mert a HANA rendszer replikációs mód aszinkron. Ha meg kell adnia az RPO = 0, ez a konfiguráció nem választott konfigurációját.

Egy kis változást, amely a konfigurációs tehet adatokat beállítása szolgáltatásbetöltés lehet. Azonban feladatátvétel és a tényt, hogy az alkalmazás rétegeket is át kell helyezni a második régió manuális jellegéből, nem ésszerű adatok betöltésére. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Rendelkezésre állás egyetlen régión belül és között régiók egyesítése 

Rendelkezésre állási belül és között régiók kombinációja ezek a tényezők alapján előfordulhat, hogy vezeti:

- A helyreállítási Időkorlát követelményt = 0 belül egy Azure-régiót.
- A szervezet nem hajlandó vagy egy fő természeti katasztrófa, amely hatással van egy nagyobb területet hatással globális műveleteket képes. Ez az eset a néhány, az elmúlt néhány évben a karibi találati hurrikánok esetében.
- Az szabályozások készlete, amelyek egyértelműen túl milyen Azure rendelkezésre állási zónák biztosíthat elsődleges és másodlagos helyek közötti távolság igény szerint.

Ezekben az esetekben, milyen SAP hívások beállíthat egy [SAP HANA többrétegű rendszer replikációs konfiguráció](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) HANA replikációs használatával. Az architektúra néz ki:

![Három virtuális gépek két régió keresztül ábrája](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Ez a konfiguráció biztosítja az RPO = 0, az alacsony RTO, az elsődleges régióban. A konfigurációs decent RPO is biztosít, ha a második régió az áthelyezési van szó. A második régióban RTO időpontok függenek-e az adatok előre feltölti. Sok ügyfél a virtuális gép használja a másodlagos régióban tesztrendszer futtatásához. Az adott használati eset, nem kell előre feltölti az adatokat.

> [!NOTE]
> Mivel használata **logreplay** HANA replikációs a replikáció szint 2 és 3 (a másodlagos helyre replikálásával) réteg között (az elsődleges régióban szinkron replikáció), a 2 1. rétegbeli át a működési mód nem lehet **delta_datashipping** működési mód. Működési mód és bizonyos korlátozások vonatkoznak az adatait, tekintse meg az SAP cikket [SAP HANA replikációs művelet módjainak](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>További lépések

Ezeket a konfigurációkat, az Azure-ban való beállításának lépésenkénti útmutatásért lásd:

- [Az Azure virtuális gépeken SAP HANA-rendszer replikáció beállítása](sap-hana-high-availability.md)
- [Magas rendelkezésre állású SAP Hana replikációs használatával](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
