---
title: Az SAP HANA magas rendelkezésre állása és vészhelyreállítása az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: Magas rendelkezésre állás létrehozása és az SAP HANA vészutáni helyreállításának megtervezése az Azure-ban (nagy példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0585c1251ba18e1390f3eee28a989edee6eb8591
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616935"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>AZ SAP HANA nagy példányai magas rendelkezésre állású és vészhelyreállítást jelentenek az Azure-ban 

>[!IMPORTANT]
>Ez a dokumentáció nem helyettesíti az SAP HANA felügyeleti dokumentációt vagy az SAP Notes.This documentation is no replacement of the SAP HANA administration documentation or SAP Notes. Várható, hogy az olvasó az SAP HANA felügyeletének és műveleteinek alapos megértésével és szakértelmével rendelkezik, különösen a biztonsági mentés, a visszaállítás, a magas rendelkezésre állás és a vészhelyreállítás témaköreivel.

Fontos, hogy gyakorolja a környezetben és a HANA verziók és kiadások során végrehajtott lépéseket és folyamatokat. Az ebben a dokumentációban leírt egyes folyamatok egyszerűbbek a jobb általános megértés érdekében, és nem használhatók részletes lépésekként az esetleges üzemeltetési kézikönyvekhez. Ha műveleti kézikönyveket szeretne létrehozni a konfigurációkhoz, tesztelnie és gyakorolnia kell a folyamatokat, és dokumentálnia kell az adott konfigurációkhoz kapcsolódó folyamatokat. 


A magas rendelkezésre állás és a vészhelyreállítás (DR) az Azure (Nagy példányok) kiszolgálón az alapvető fontosságú SAP HANA futtatásának alapvető szempontjai. Fontos, hogy működjön együtt az SAP, a rendszerintegrátor, vagy a Microsoft megfelelően megtervezheti és valósítsa meg a megfelelő magas rendelkezésre állású és vész-helyreállítási stratégiák. Azt is fontos, hogy fontolja meg a helyreállítási pont célja (RPO) és a helyreállítási idő cél, amelyek kifejezetten a környezetre.

A Microsoft támogatja néhány SAP HANA magas rendelkezésre állású képességek HANA nagy példányok. Ilyen képességek:

- **Tárolási replikáció:** A tárolórendszer képes replikálni az összes adatot egy másik Azure-régióban lévő hana nagy példánybélyegzőre. Az SAP HANA ettől a módszertől függetlenül működik. Ez a funkció a HANA nagy példányok alapértelmezett vész-helyreállítási mechanizmusa.
- **HANA rendszer replikációja:** Az [SAP HANA-ban lévő összes adat replikációja](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) egy külön SAP HANA-rendszerre. A helyreállítási idő célkitűzés minimálisra csökken az adatok rendszeres időközönként. Az SAP HANA támogatja az aszinkron, szinkron memórián belüli és szinkron módokat. Szinkron mód csak az SAP HANA-rendszerek, amelyek ugyanabban az adatközpontban, vagy kevesebb, mint 100 km-re egymástól. A HANA nagy példánybélyegzők jelenlegi kialakításával a HANA rendszer replikációja csak egy régión belül használható magas rendelkezésre állásra. HANA rendszer replikációja igényel egy külső proxy vagy útválasztási összetevő vész-helyreállítási konfigurációk egy másik Azure-régióban. 
- **Automatikus feladatátvétel gazdagépe:** Az SAP HANA helyi hiba-helyreállítási megoldás, amely a HANA rendszer replikációjának alternatívája. Ha a fő csomópont elérhetetlenné válik, egy vagy több készenléti SAP HANA-csomópontot konfigurál horizontális felskálázási módban, és az SAP HANA automatikusan átadja a rendszeradékat egy készenléti csomópontnak.

Sap HANA az Azure-ban (nagy példányok) kínálnak két Azure-régióknégy geopolitikai területeken (USA, Ausztrália, Európa és Japán). Egy geopolitikai területen belül, amely hana nagy példány bélyegzők dedikált hálózati áramkörök csatlakozik a külön dedikált hálózati áramkörök két régióban. Ezek a tárolási pillanatképek replikálására szolgálnak vész-helyreállítási módszerek biztosítása érdekében. A replikáció alapértelmezés szerint nincs létrehozva, de a vész-helyreállítási funkciókat megrendelő ügyfelek számára van beállítva. A tárolási replikáció a HANA nagy példányok tárolási pillanatképeinek használatától függ. Nem lehet azure-régiót kiválasztani vészrégióként, amely egy másik geopolitikai területen található. 

Az alábbi táblázat a jelenleg támogatott magas rendelkezésre állási és vész-helyreállítási módszereket és kombinációkat mutatja be:

| Hana nagy példányok által támogatott forgatókönyv | Magas rendelkezésre állási lehetőség | Vész-helyreállítási lehetőség | Megjegyzések |
| --- | --- | --- | --- |
| Egyetlen csomópont | Nem érhető el. | Dedikált DR beállítás.<br /> Többcélú DR beállítás. | |
| Állomás automatikus feladatátvétele: Horizontális felskálázás (készenléti állapottal vagy anélkül)<br /> beleértve az 1+1-et | Lehetséges, ha a készenléti szerepkör aktív szerepet vállal.<br /> Hana szabályozza a szerepkör-kapcsolót. | Dedikált DR beállítás.<br /> Többcélú DR beállítás.<br /> VÉSZ-szinkronizálás a tároló replikációjával. | Hana kötetkészletek kapcsolódnak az összes csomóponthoz.<br /> A VÉSZ-helynek azonos számú csomódval kell rendelkeznie. |
| HANA rendszer replikációja | Elsődleges vagy másodlagos beállítással lehetséges.<br /> Másodlagos áthelyezése az elsődleges szerepkörbe egy feladatátvételi esetben.<br /> HANA rendszerreplikáció és operációs rendszer vezérlésének feladatátvétele. | Dedikált DR beállítás.<br /> Többcélú DR beállítás.<br /> VÉSZ-szinkronizálás a tároló replikációjával.<br /> A DR a HANA rendszer replikációjának használatával még nem lehetséges külső összetevők nélkül. | Minden csomóponthoz külön lemezkötet-készlet található.<br /> Csak a másodlagos replika lemezkötetei a termelési helyen replikálódnak a VÉSZ-helyre.<br /> A VÉSZ-helyen egy kötetkészlet szükséges. | 

Egy dedikált VÉSZ-beállítás, ahol a HANA nagy példány egység a VÉSZ-hely nem használható más számítási feladatok vagy nem éles rendszer futtatásához. Az egység passzív, és csak akkor van telepítve, ha egy katasztrófa feladatátvétel végrehajtása. Bár ez a beállítás nem ajánlott választás sok ügyfél számára.

Tekintse [hli támogatott forgatókönyvek](hana-supported-scenario.md) tanulni tárolási elrendezés és ethernet részleteket az architektúra.

> [!NOTE]
> [SAP HANA MCOD-telepítések](https://launchpad.support.sap.com/#/notes/1681092) (több HANA-példányok egy egységen) átfedési forgatókönyvek a táblázatban felsorolt HA és DR metódusok. Kivételt képez a HANA rendszerreplikáció használata a pacemakeren alapuló automatikus feladatátvételi fürttel. Egy ilyen eset egységenként csak egy HANA-példányt támogat. [SAP HANA MDC-telepítések](https://launchpad.support.sap.com/#/notes/2096000) esetén csak a nem tárolóalapú HA és VÉSZ metódusok működnek, ha egynél több bérlő van telepítve. Ha egy bérlő telepítve van, az összes felsorolt metódus érvényes.  

A többcélú VÉSZ-beállítás, ahol a HANA nagy példány egység a VÉSZ-hely fut egy nem éles számítási feladatok. Katasztrófa esetén állítsa le a nem éles rendszert, csatlakoztassa a tárolóreplikált (további) kötetkészleteket, majd indítsa el az éles HANA-példányt. A hana nagy példány vész-helyreállítási funkcióját használó legtöbb ügyfél ezt a konfigurációt használja. 


Az SAP HANA magas rendelkezésre állásáról a következő SAP-cikkekben talál további információt: 

- [SAP HANA magas rendelkezésre állású tanulmány](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA felügyeleti útmutató](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA Academy – videó az SAP HANA rendszer replikációjáról](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP támogatási megjegyzés #1999880 – gyakori kérdések az SAP HANA rendszer replikációjával](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP támogatási megjegyzés #2165547 – SAP HANA biztonsági másolat és visszaállítás az SAP HANA rendszerreplikációs környezetben](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP támogatási megjegyzés #1984882 – SAP HANA rendszerreplikáció használata hardveres cseréhez minimális/nulla állásidővel](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>A HANA nagy példányokkal történő vész-helyreállítási hálózati szempontok

A HANA nagy példányok vész-helyreállítási funkcióinak kihasználásához meg kell terveznie a hálózati kapcsolatot a két Azure-régióhoz. Szüksége van egy Azure ExpressRoute-kapcsolat a helyszíni a fő Azure-régióban, és egy másik kapcsolat a helyszíni vész-helyreállítási régióban. Ez a mérték egy olyan helyzetet fed le, amelyben probléma van egy Azure-régióban, beleértve a Microsoft Enterprise Edge router (MSEE) helyét.

Második intézkedésként csatlakoztathatja az összes Azure virtuális hálózatok, amelyek csatlakoznak az SAP HANA az Azure-ban (nagy példányok) az egyik régióban egy ExpressRoute-áramkör, amely összeköti a HANA nagy példányok a másik régióban. Ezzel a *keresztcsatlakozással*az 1-es régióban lévő Azure virtuális hálózaton futó szolgáltatások csatlakozhatnak a HANA 2- es régióban lévő HANA nagypéldány-egységekhez, és fordítva. Ez a mérték egy olyan esetet orvosolni fog, amelyben az Azure-ral a helyszíni helyhez csatlakozó MSEE-helyek közül csak az egyik offline állapotba kerül.

Az alábbi ábra a vész-helyreállítási esetek rugalmas konfigurációját mutatja be:

![Optimális konfiguráció a vészhelyreállításhoz](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Egyéb követelmények a HANA nagy példányok tárolási replikációjával a vészhelyreállításhoz

A HANA nagy példányokkal rendelkező vész-helyreállítási beállítások előző követelményein kívül a következőket kell tennie:

- Rendelje nedV HANA az Azure-beli (nagy példányok) SK-k az éles sk-ek azonos méretű, és üzembe helyezi őket a vész-helyreállítási régióban. Az aktuális ügyfél-telepítések, ezek a példányok nem éles HANA-példányok futtatásához használható. Ezeket a konfigurációkat *többcélú VÉSZ-beállításoknak nevezzük.*   
- Rendeljen további tárhelyet a VÉSZ-ALAPÚ helyen az SAP HANA minden egyes Azure-beli (nagy példány) ska,amely a vész-helyreállítási helyen szeretne helyreállítani. További tárhely vásárlása lehetővé teszi a tárolókötetek lefoglalását. Lefoglalhatja azokat a köteteket, amelyek az éles Azure-régióból a tárolási replikáció célcélpontjai a vész-helyreállítási Azure-régióba.
- Abban az esetben, ha a HSR beállítása az elsődleges, és a rendszer a tárolási alapú replikáció a VÉSZ-helyre, meg kell vásárolnia további tároló a VÉSZ-helyen, így az elsődleges és másodlagos csomópontok adatait replikálja a VÉSZ-helyre.

  **További lépések**
- Lásd [a biztonsági mentést és a visszaállítást](hana-backup-restore.md).













