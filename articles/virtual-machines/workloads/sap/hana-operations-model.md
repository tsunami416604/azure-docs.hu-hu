---
title: SAP Hana az Azure-ban (nagyméretű példányok) működési modellt |} A Microsoft Docs
description: SAP Hana az Azure-ban (nagyméretű példányok) működési modellt.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36a648e2d46cce96a8ff663f45ccf45326898a84
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417006"
---
# <a name="operations-model-and-responsibilities"></a>Működési modellt és felelősségek

A szolgáltatás az SAP HANA az Azure-ban (nagyméretű példányok) a megadott Azure IaaS-szolgáltatások igazodik. Kap egy nagyméretű HANA-példány, az SAP HANA-hoz optimalizált telepített operációs rendszer egy példánya. Mint az Azure IaaS virtuális gépeket, műveletek, az operációs rendszer korlátozására, valamilyen más szoftvert telepítenie, HANA telepítése, az operációs rendszer és a HANA operációs és frissítése az operációs rendszer és a HANA feladata a. A Microsoft nem érvényben lévő, az operációs rendszer és a HANA-frissítéseket.

![Az SAP HANA az Azure-ban (nagyméretű példányok) feladatai](./media/hana-overview-architecture/image2-responsibilities.png)

Ahogy az ábrán látható, az SAP HANA az Azure-ban (nagyméretű példányok) egy több-bérlős IaaS ajánlat. Többségében az osztálynak a feladata az operációsrendszer-infrastruktúra határ van. A Microsoft felelős a sor az operációs rendszer alatt a szolgáltatás minden aspektusát. Ön felelős a sor fölé a szolgáltatás minden aspektusát. Az operációs rendszer az Ön felelőssége. Továbbra is lehet alkalmazni a legújabb helyszíni módszerek megfelelőségi, biztonsági, felügyelet, alapja és az operációs rendszer felügyeleti használandó. A rendszerek jelennek meg, mintha azok a hálózaton lévő összes Üdvözlettel.

Ez a szolgáltatás így területek, ahol kell dolgozni a mögöttes infrastruktúra-funkciók használata a legjobb eredmények elérése érdekében a Microsoft az SAP Hana-hoz optimalizáltuk.

Az alábbi lista részletesen az egyes réteget és az Ön feladatkörei biztosítja:

**Hálózatkezelés**: A belső hálózatok számára a futó SAP HANA nagyméretű szolgáltatáspéldányban. A felhasználó felelőssége hozzáférést biztosít a tárolás, a példányhoz (például a horizontális felskálázást és egyéb funkciók), a kapcsolat a fekvő és a kapcsolatokat az Azure-bA az SAP alkalmazásrétegre üzemeltető közötti kapcsolatot a virtuális gépek. Is tartalmaz a WAN-kapcsolatra vész helyreállítási célokra replikáció az Azure adatközpontok között. Minden hálózatból elérhető a bérlői szerint vannak particionálva, és rendelkezik a alkalmazni szolgáltatás-minőségi.

**Tárolási**: A virtualizált particionálni az SAP HANA-kiszolgáló számára szükséges összes kötetet, valamint a pillanatképek tárolási. 

**Kiszolgálók**: A dedikált fizikai kiszolgálók az SAP HANA-adatbázisok futtatását hozzárendelt a bérlők számára. A kiszolgálók típusú I osztályhoz termékváltozatok a hardver emeli ki. Az ilyen kiszolgálók a kiszolgáló konfigurációs van gyűjtött és karbantartása a profilok, amelyek egy másik fizikai hardveren egyetlen fizikai hardverről helyezheti át. Az ilyen (manuális) áttért a profil által végrehajtott műveletek az Azure-szolgáltatás javítása egy kicsit hasonlítható. A kiszolgálók, II. típusú osztály SKU-ja nem érhető el az ilyen képességet.

**SDDC**: A felügyeleti szoftver adatok kezelésére szolgáló szoftver-meghatározott entitásokként erőforrások. A Microsoft lehetővé teszi az adatbáziskészlet erőforrásainak méretezés, a rendelkezésre állás és a teljesítmény javítása érdekében.

**O/S**: Az operációs rendszer választja (SUSE Linux vagy Red Hat Linux) futtató kiszolgálókon. A rendszer a megadott operációsrendszer-lemezképek megadva az egyes Linux-gyártó által a Microsoftnak az SAP HANA futtatásához. A Linux-gyártó az SAP HANA-ra optimalizált adott rendszerkép rendelkező előfizetés kell rendelkeznie. Ön felelős a képek rögzítése az operációs rendszer szállítójához. 

Készenléti feladat átadása a Microsoft által a pontról Ön felelős minden olyan további javítása, a Linux operációs rendszert. További csomagok, amelyek egy SAP HANA sikeres telepítéséhez szükséges lehet, és, amely nem tartalmazza az SAP HANA-ban az adott Linux-gyártó által a javításokat tartalmaz operációsrendszer-lemezképek optimalizált. (További információkért lásd az SAP HANA-telepítési dokumentációt és SAP-megjegyzések) 

Ön felelős operációs rendszer javításait hibásan működik, vagy az operációs rendszer optimalizálás és az illesztőprogramok az adott kiszolgálói hardver viszonyítva. Is Ön felelős biztonsági és funkcionális javítás az operációs rendszer. 

Ön felelőssége a figyelés és a kapacitástervezés is tartalmazza:

- Erőforrás-használat Processzor.
- Memóriahasználatát.
- Kötetek szabad terület, az IOPS és a késleltetés kapcsolódik.
- A hálózati forgalomnak nagyméretű HANA-példány és az SAP alkalmazásrétegre között.

Az alapul szolgáló infrastruktúrát, a nagyméretű HANA-példányt a biztonsági mentés és az operációsrendszer-kötet visszaállítása funkciót biztosít. Ez a funkció használata is a felhasználó felelőssége.

**Közbenső**: Az SAP HANA példányok, elsősorban. Felügyeleti, műveleti és figyelési az Ön felelőssége. A megadott funkció segítségével tárolási pillanatfelvételeket kíván használni a biztonsági mentési és visszaállítási és vész-helyreállítási céllal. Ezek a képességek az infrastruktúra által biztosított. Az Ön feladatkörei is magas rendelkezésre állású és vész-helyreállítási ezekkel a lehetőségekkel kihasználva őket, és a figyelés meghatározni, hogy storage pillanatképek sikeresen végrehajtva tervezése.

**Adatok**: Az SAP HANA által kezelt adatok és az egyéb adatok, például a biztonsági mentések köteteken található fájlt vagy fájlt oszt meg. Az Ön feladatkörei közé tartozik a szabad lemezterület figyelése és kezelése a tartalom a köteteken. Is Ön felelős lemezkötetek és a pillanatképek tárolási, biztonsági mentések sikeres végrehajtásának figyelése. A Microsoft feladata a vész-helyreállítási helyek az adatok replikálása sikeres végrehajtását.

**Alkalmazások:** Az SAP alkalmazás példányai vagy az SAP alkalmazások, ezeknek az alkalmazásoknak az alkalmazásrétegre. Az Ön feladatkörei közé tartozik a központi telepítési, felügyeleti, műveleti és ezen alkalmazások figyelését. Ön felelős hasznosan Processzor-erőforrás-használat, memóriahasználat, az Azure-tárhelyhasználat és hálózati sávszélességet a virtuális hálózatokon belül. Is Ön felelős kapacitás megtervezése a SAP Hana-hoz a virtuális hálózatok erőforrás-használat az Azure-ban (nagyméretű példányok).

**WAN-OK**: A kapcsolatok létrehozása a helyszíni számítási feladatok Azure-környezetek. Minden, a nagyméretű HANA-példány ügyfél számára a csatlakozást az Azure ExpressRoute használata. Ez a kapcsolat nem az SAP HANA az Azure-ban (nagyméretű példányok) megoldás része. Ön felelős a beállítás a kapcsolat.

**Archív**: Előfordulhat, hogy inkább az archív adatok másolatát a saját módszerrel a storage-fiókok. Archiválás szükséges felügyeleti, megfelelőségi, költségeket és műveleteket. Ön felelős létrehozni archívum másolatot és biztonsági másolatokat az Azure és a egy megfelelő módon tárolja őket.

Tekintse meg a [SLA-t, az SAP Hana az Azure-ban (nagyméretű példányok)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Következő lépések**
- Tekintse meg [Azure-beli SAP HANA (nagyméretű példányok) architektúra](hana-architecture.md)
