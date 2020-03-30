---
title: Az SAP HANA műveleti modellje az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: Az SAP HANA műveleti modellje az Azure-ban (nagy példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e147e4a5f104ca4cd1a10a776c907e3f9f1d6128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616973"
---
# <a name="operations-model-and-responsibilities"></a>Működési modell és feladatok

Az SAP HANA az Azure-ban (nagy példányok) nyújtott szolgáltatás igazodik az Azure IaaS-szolgáltatások. Egy HANA nagy példány egy telepített operációs rendszerrel rendelkező, SAP HANA-ra optimalizált példányt kap. Az Azure IaaS virtuális gépekhez ugyanúgy, mint az Azure IaaS virtuális gépek, az operációs rendszer megerősítésével, további szoftverek telepítésével, a HANA telepítésével, az operációs rendszer és a HANA működtetésével, valamint az operációs rendszer és a HANA frissítésével kapcsolatos feladatok többsége az Ön felelőssége. A Microsoft nem kényszeríti az operációs rendszer- és HANA-frissítéseket.

![Az SAP HANA feladatai az Azure-ban (nagy példányok)](./media/hana-overview-architecture/image2-responsibilities.png)

Amint az ábrán látható, az SAP HANA az Azure-ban (nagy példányok) egy több-bérlős IaaS-ajánlat. A legtöbb esetben a felelősség megosztása az operációs rendszer-infrastruktúra határán van. A Microsoft felelős a szolgáltatás minden aspektusáért az operációs rendszer vonala alatt. Ön a felelős a szolgáltatás minden aspektusáért a vonal felett. Az operációs rendszer az Ön felelőssége. Továbbra is használhatja a legfrissebb helyszíni módszereket, amelyeket a megfelelőség, a biztonság, az alkalmazáskezelés, az alap és az operációs rendszer kezelése érdekében alkalmazhat. A rendszerek minden tekintetben úgy jelennek meg, mintha a hálózatban lennének.

Ez a szolgáltatás sap HANA-ra van optimalizálva, így vannak olyan területek, ahol a Microsofttal együtt kell működnie az alapul szolgáló infrastruktúra-képességek használatához a legjobb eredmény érdekében.

Az alábbi lista részletesebben ismerteti az egyes rétegeket és az Ön feladatait:

**Hálózatkezelés:** Az SAP HANA-t futtató nagypéldány-bélyegző összes belső hálózata. Az Ön felelőssége magában foglalja a hozzáférést a tároláshoz, a példányok közötti kapcsolatot (a kibővített és egyéb funkciók), a tájhoz való kapcsolódást és az Azure-hoz való kapcsolódást, ahol az SAP-alkalmazásréteg virtuális gépeken található. Azt is tartalmazza a WAN-kapcsolat az Azure Data Centerek között a vész-helyreállítási replikációs célokat replikáció. Minden hálózat particionált a bérlő, és a szolgáltatás minősége alkalmazva.

**Tárolás:** A virtualizált particionált tároló az SAP HANA-kiszolgálók által szükséges összes kötethez, valamint a pillanatképekhez. 

**Kiszolgálók**: A dedikált fizikai kiszolgálók a bérlőkhöz rendelt SAP HANA DB-k futtatásához. A termék- és szolgáltatáskivonatok I. típusú osztályának kiszolgálói hardveres absztrakt. Az ilyen típusú kiszolgálók esetében a kiszolgáló konfigurációja profilokban történik, amelyek áthelyezhetők az egyik fizikai hardverről a másikra. Egy ilyen (manuális) lépés a profil műveletek egy kicsit összehasonlítható az Azure szolgáltatás gyógyulását. A II.

**SDDC**: Az adatközpontok szoftveráltal definiált entitásként történő kezelésére használt felügyeleti szoftver. Lehetővé teszi a Microsoft számára, hogy erőforrásokat egyesítsen a méretezési, a rendelkezésre állási és a teljesítménybeli okokból.

**O/S**: A kiszolgálókon futó operációs rendszer (SUSE Linux vagy Red Hat Linux). Az operációs rendszer lemezképeit az egyes Linux-szállító biztosította a Microsoftnak az SAP HANA futtatásához. Az adott SAP HANA-optimalizált lemezképhez előfizetéssel kell rendelkeznie a Linux-szállítóval. Ön felelős a képek regisztrálásáért az operációs rendszer szállítójával. 

A Microsoft általi átadási ponttól kezdve Ön felelős a Linux operációs rendszer további javításáért. Ez a javítás további csomagokat tartalmaz, amelyek szükségesek lehetnek a sikeres SAP HANA-telepítéshez, és amelyeket az adott Linux-szállító nem tartalmazott az SAP HANA optimalizált operációs rendszerlemezeiben. (További információt az SAP HANA telepítési dokumentációjában és az SAP Notes-ban talál.) 

Ön felelős az operációs rendszer javításáért az operációs rendszer és illesztőprogramjainak az adott kiszolgálóhardverhez viszonyított hibás működése vagy optimalizálása miatt. Ön is felelős az operációs rendszer biztonságáért vagy funkcionális javításáért. 

Az Ön felelőssége az:

- CPU-erőforrás-felhasználás.
- Memóriafogyasztás.
- Szabad területhez, IOPS-hoz és késéshez kapcsolódó lemezkötetek.
- A HANA nagy példány és az SAP-alkalmazásréteg közötti hálózati kötetforgalom.

A HANA Large Instance mögöttes infrastruktúrája biztosítja az operációs rendszer kötetének biztonsági mentését és visszaállítását. Ennek a funkciónak a használata szintén az Ön felelőssége.

**Köztes:** Az SAP HANA példány, elsősorban. Az adminisztráció, a műveletek és a felügyelet az Ön felelőssége. A megadott funkciók segítségével tárolási pillanatképek biztonsági mentési és visszaállítási és vész-helyreállítási célokra használhatja. Ezeket a képességeket az infrastruktúra biztosítja. A feladatok közé tartozik a magas rendelkezésre állású vagy vész-helyreállítási ezekkel a képességekkel, kihasználva őket, és figyelése annak megállapítására, hogy a tárolási pillanatképek sikeresen végrehajtott.

**Adatok:** Az SAP HANA által kezelt adatok, és egyéb adatok, például a köteteken vagy fájlmegosztásokon található biztonsági mentések. Feladatai közé tartozik a lemezszabad terület figyelése és a kötetek tartalmának kezelése. Emellett a lemezkötetek és a tárolási pillanatképek biztonsági másolatainak sikeres végrehajtásáért is felelős. A vész-helyreállítási helyekre történő adatreplikáció sikeres végrehajtása a Microsoft felelőssége.

**Alkalmazások:** Az SAP-alkalmazáspéldányok, illetve nem SAP-alkalmazások esetében az alkalmazások alkalmazásrétege. Feladatai közé tartozik az alkalmazások üzembe helyezése, felügyelete, műveletei és figyelése. Ön felelős a cpu-erőforrás-felhasználás, a memóriafelhasználás, az Azure Storage-felhasználás és a virtuális hálózatokon belüli hálózati sávszélesség-felhasználás kapacitástervezéséért. Emellett felelős a virtuális hálózatoktól az SAP HANA-ig az Azure-beli (nagy példányok) erőforrás-felhasználás kapacitástervezéséért is.

**WANs:** A helyszíni és azure-üzembe helyezési számítási feladatokhoz létesítő kapcsolatok. A HANA large instance minden ügyfele az Azure ExpressRoute-ot használja a kapcsolathoz. Ez a kapcsolat nem része az SAP HANA az Azure (nagy példányok) megoldás. Ön a felelős a kapcsolat beállításáért.

**Archiválás:** Előfordulhat, hogy inkább archiválja az adatok másolatait saját metódusai használatával a tárfiókokban. Az archiváláshoz irányításra, megfelelőségre, költségekre és műveletekre van szükség. Ön a felelős az archív másolatok és biztonsági mentések létrehozásáért az Azure-ban, és megfelelő módon tárolja azokat.

Tekintse meg az [SAP HANA SLA-t az Azure-ban (nagy példányok)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**További lépések**
- SAP [HANA (nagy példányok) architektúrájának ajánlása az Azure-ban](hana-architecture.md)
