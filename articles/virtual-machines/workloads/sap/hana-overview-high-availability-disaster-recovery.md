---
title: Az Azure-ban (nagyméretű példányok) SAP HANA magas rendelkezésre állás és vészhelyreállítás helyreállítási |} A Microsoft Docs
description: Magas rendelkezésre állást és az Azure-ban (nagyméretű példányok) SAP Hana vész-helyreállítási terv létrehozása
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4d60f6752bf369e875c350823f76854408fcb806
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58000591"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA nagyméretű példányok magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban 

>[!IMPORTANT]
>Ez a dokumentáció a SAP HANA-felügyelet dokumentációja és az SAP-megjegyzések helyettesítése. Valószínű, hogy az olvasó rendelkezik egy szilárd ismeretekkel és szaktudását a SAP HANA-felügyelet és a műveletek, különösen a biztonsági mentés, visszaállítás, magas rendelkezésre állású és vész-helyreállítási témaköröket.

Fontos lépéseket és folyamatokat a környezetben, és a HANA-verziók és kiadások tett gyakorolják. Bizonyos folyamatok, az ebben a dokumentációban leírt általános végiglépkedéshez egyszerűsítettek, és nem jelentenek a részletes lépéseket a végleges művelet szakkönyveket felhasználhatók. Ha azt szeretné, a konfigurációk művelet szakkönyveket létrehozásához, teszteléséhez és gyakorolja a folyamatok és az adott konfigurációkhoz kapcsolódó folyamatok dokumentálása szüksége. 


Magas rendelkezésre állás és vészhelyreállítás (DR) az alapvető fontosságú SAP HANA az Azure-ban (nagyméretű példányok) kiszolgálón futó rendkívül fontos jellemzőit. Fontos, SAP, a rendszerintegrátor vagy a Microsoft megfelelően tervezhet és a megfelelő magas rendelkezésre állású és vész-helyreállítási stratégia megvalósításához dolgozhat. Fontos is érdemes figyelembe venni a helyreállításipont-célkitűzés (RPO) és a helyreállítási időre vonatkozó célkitűzést, amelyek adott környezetre jellemző.

A Microsoft támogatja a HANA nagyméretű példányok néhány SAP HANA magas rendelkezésre állású funkcióit. Ilyen képességek:

- **Tárreplikáció**: Adatok replikálása egy másik HANA nagyméretű szolgáltatáspéldányban egy másik Azure-régióban a storage rendszer riasztásészlelési képességére. SAP HANA működése független ezt a módszert. Ez a funkció az alapértelmezett vész helyreállítási mechanizmusa el a HANA nagyméretű példányok.
- **HANA-rendszerreplikálást**: A [az összes adat az SAP Hana-beli replikációs](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) egy különálló SAP HANA rendszerbe. A helyreállítási időre vonatkozó célkitűzés rendszeres időközönként adatok a replikáció révén minimálisra csökken. SAP HANA aszinkron, a szinkron a memóriában, és szinkron módot támogat. A szinkron módban csak szolgál SAP HANA rendszereit, amelyek az ugyanabban az adatközpontban vagy kevesebb, mint 100 km-re vannak. A jelenlegi kialakítás nagyméretű HANA-példány stampgyűjteményt, a HANA-rendszerreplikálást magas rendelkezésre állás érdekében csak egy régión belül használható. HANA-rendszerreplikálást szükséges külső fordított proxy vagy útválasztási összetevő vész-helyreállítási konfigurációk be egy másik Azure-régióban. 
- **Gazdagép automatikus feladatátvétele**: Egy SAP Hana, amely a HANA-rendszerreplikálást alternatív helyi kiesésekor helyszíni helyreállító megoldással. A fő csomópont elérhetetlenné válik, ha konfigurál egy vagy több készenléti SAP HANA-csomópont kibővített módban, és az SAP HANA automatikusan átadja a feladatokat a készenléti csomópont.

SAP HANA az Azure-ban (nagyméretű példányok) négy geopolitikai területeken (Egyesült Államok, Ausztrália, Európában és japán) két Azure-régióban érhető el. Egy geopolitikai területen két olyan régióban, amelyek nagyméretű HANA-példány stampek külön dedikált hálózati Kapcsolatcsoportok kapcsolódnak. Ezek használhatók a pillanatképek tárolási replikálásához vész-helyreállítási mód megadásának. A replikáció nem jön létre alapértelmezés szerint, de a megfelelő az ügyfelek, akik order vészhelyreállítási funkciók. Tárreplikáció szolgáltatás nagyméretű HANA-példányok pillanatképeit tároló használata függ. Nem alkalmas a Vészhelyreállítási régióban, amely egy másik geopolitikai területén, egy Azure-régió kiválasztásához. 

Az alábbi táblázat a jelenleg támogatott magas rendelkezésre állás és vészhelyreállítás helyreállítási módok és kombinációit:

| Támogatja a HANA nagyméretű példányok forgatókönyv | Magas rendelkezésre állási lehetőségek | Vész-helyreállítási lehetőség | Megjegyzések |
| --- | --- | --- | --- |
| Egyetlen csomópont | Nem érhető el. | Dedikált DR-telepítés.<br /> Multipurpose DR-telepítés. | |
| Gazdagép automatikus feladatátvétele: Horizontális felskálázás (a vagy készenléti nélkül)<br /> például 1 + 1 | A készenléti véve az aktív szerepkör-lehetséges.<br /> HANA azt szabályozza, hogy a szerepkör-kapcsoló. | Dedikált DR-telepítés.<br /> Multipurpose DR-telepítés.<br /> DR-szinkronizálás segítségével tárreplikációt. | HANA kötet csoportok csatlakoztatott összes csomópontjának.<br /> DR-helynek kell lennie a csomópontok azonos számú. |
| HANA-rendszerreplikálást | Elsődleges vagy másodlagos telepítés lehetséges.<br /> Másodlagos abban az esetben feladatátvétel elsődleges szerepkörre helyezi át.<br /> HANA-rendszerreplikálást és az operációs rendszer szabályozza a feladatátvételt. | Dedikált DR-telepítés.<br /> Multipurpose DR-telepítés.<br /> DR-szinkronizálás segítségével tárreplikációt.<br /> A HANA-rendszerreplikálást DR még nem lehet külső gyártóktól származó összetevőket nélkül. | Minden egyes csomópont csatolt kötetek külön készlete.<br /> Az élesben használt helyet a másodlagos másodpéldány csak lemezkötetek a DR-helyre replikálja.<br /> Egy olyan kötetek, szükséges a DR-helyen. | 

Egy dedikált Vészhelyreállítási telepítő nem, ahol a DR-webhely a nagyméretű HANA-példány egység nem használatos a futó számítási feladatok vagy nem éles rendszerek. Az egység passzív, és csak akkor, ha a vészhelyreállítási feladatátvétel végrehajtása üzemel. Ez a beállítás viszont nem sok ügyfél előnyben részesített választást.

Tekintse meg [HLI támogatott forgatókönyvek](hana-supported-scenario.md) tárolási elrendezést, és ethernet részletekkel az architektúrához.

> [!NOTE]
> [Az SAP HANA MCOD központi telepítések](https://launchpad.support.sap.com/#/notes/1681092) (egy egység több HANA példányok) a táblázatban felsorolt forgatókönyvek munkahelyi átfedő a magas rendelkezésre ÁLLÁS és Vészhelyreállítás módszerek. Kivétel ez alól egy támasztja alapuló automatikus feladatátvételi fürtöt a HANA-Rendszerreplikálást használatát. Ilyen esetben csak az egység egy HANA-példány támogatja. A [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) telepítések esetén csak nem a storage-alapú magas rendelkezésre ÁLLÁS és Vészhelyreállítás metódusai Ha egynél több bérlőhöz van telepítve. Egyetlen bérlő számára üzembe helyezett, a felsorolt összes módszerek érvényesek.  

A többcélú DR-telepítés, ahol a nagyméretű HANA-példány egység DR a helyszínen fut, nem éles számítási feladatok. Vészhelyzet esetén állítsa le a nem éles rendszer csatlakoztassa a storage replikált (új) kötetet készletek, és indítsa el a termelési HANA-példány. A legtöbb ügyfeleink, akik a nagyméretű HANA-példány vészhelyreállítási funkciók használja ezt a konfigurációt. 


További információ az SAP HANA magas rendelkezésre állás a következő cikkekben SAP találja meg: 

- [Az SAP HANA magas rendelkezésre állású tanulmány](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Az SAP HANA-felügyeleti útmutató](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Az SAP HANA-Rendszerreplikálást SAP HANA Academy-videó](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP támogatási Megjegyzés #1999880 – az SAP HANA Rendszerreplikáció – gyakori kérdések](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP támogatási Megjegyzés #2165547 – SAP HANA biztonsági mentése és visszaállítása az SAP HANA rendszer replikációs környezeten belül](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP támogatási Megjegyzés #1984882 – SAP HANA-Rendszerreplikálást segítségével a minimális/üzemszünet hardver Exchange-hez](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>A hálózatokkal kapcsolatos szempontok vész-helyreállítási a nagyméretű HANA-példányok

Nagyméretű HANA-példányokhoz vész-helyreállítási funkciójának előnyeit, hálózati kapcsolat a két Azure-régiók és tervezési kell. Az Azure ExpressRoute-kapcsolatcsoport kapcsolatok a helyszíni rendszerből a fő Azure-régióban, és a egy másik kapcsolatcsoport kapcsolat a helyszíni és a vészhelyreállítási régióban van szüksége. Ezt a mértéket egy olyan helyzetet, ahol probléma van egy Azure-régióban, beleértve a Microsoft Enterprise peremhálózati útválasztóhoz (MSEE) helyét ismerteti.

Egy második mérték csatlakoztathat, minden Azure virtuális hálózatok, amelyek az Azure-ban (nagyméretű példányok) SAP Hana-val csatlakoznak egy adott régióban az ExpressRoute-kapcsolatcsoport, amely kapcsolódik a HANA nagyméretű példányok más régióban található. Ez *közötti kapcsolódás*, egy Azure-on futó szolgáltatások régió 1 virtuális hálózat kapcsolódhat nagyméretű HANA-példány egységek régió 2 és fordítva. Ez a mérték címek egy esetet, amelyben az MSEE-helyek csak az egyik, amely kapcsolódik a helyszíni hellyel, az Azure-ral offline állapotba kerül.

A következő ábra mutatja be egy rugalmas konfigurációs vész-helyreállítási esetek:

![Vész-helyreállítási optimális konfigurációt](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>A nagyméretű HANA-példányokhoz tárreplikáció vész-helyreállítási egyéb követelmények

A vészhelyreállítás telepítője a HANA nagyméretű példányok az előző követelmények mellett a következőket kell tennie:

- SAP HANA ORDER akkora, mint az éles üzemben futó SKU-k az Azure-ban (nagyméretű példányok) termékváltozatokban, és telepítse őket a vészhelyreállítási régióban lévő. Az aktuális felhasználói telepítések ezek a példányok nem éles HANA-példányok futtatásához használt. Ezek a konfigurációk nevezzük *többcélú DR mátrixok*.   
- A rendelés a DR webhelyen további tárterületet az SAP HANA-szeretné helyreállítani a vész-helyreállítási helyként az Azure-ban (nagyméretű példányok) SKU-k mindegyikéhez. További tárterület vásárlása lehetővé teszi a tárolási köteteket foglal le. A köteteket, amelyek a cél az éles Azure-régió, a vészhelyreállítást az Azure-régióban a storage replikációs foglalhat le.
- Abban az esetben, ahol elsődleges HSR-telepítő rendelkezik, és, konfigurálja a storage-alapú replikálás a DR-helyre, akkor kell tárterület-vásárlási lehetőségekről a DR-helyen, mind az elsődleges és másodlagos csomópontot adatok replikálását a DR-helyre.

  **Következő lépések**
- Tekintse meg [biztonsági mentési és visszaállítási](hana-backup-restore.md).













