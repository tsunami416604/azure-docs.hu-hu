---
title: Az Azure igazolásának áttekintése
description: Az Microsoft Azure igazolásának áttekintése, amely a megbízható végrehajtási környezetek (pólók) igazolására szolgáló megoldás
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: a84308ba06a38cea475fcb1bae022da16424a731
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032996"
---
# <a name="microsoft-azure-attestation-preview"></a>Microsoft Azure Attestation (előzetes verzió)

Microsoft Azure igazolás (előzetes verzió) a megbízható végrehajtási környezetek (pólók) igazolására szolgáló megoldás, mint például az [Intel® Software Guard Extensions](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (SGX enklávéhoz) enklávék és a [virtualizálás-alapú biztonsági](/windows-hardware/design/device-experiences/oem-vbs) (vbs) enklávék. Az enklávé igazolása olyan folyamat, amely ellenőrzi, hogy az enklávé biztonságos és megbízható-e.

Az igazolás egy folyamat, amely azt mutatja be, hogy a szoftver bináris fájljai megfelelően lettek-e létrehozva egy megbízható platformon. A távoli függő entitások így biztosak lehetnek abban, hogy csak az ilyen jellegű szoftverek megbízható hardveren futnak. Az Azure-igazolás egy egységesített ügyfél-szolgáltatás és keretrendszer az igazoláshoz.

Az Azure-igazolás lehetővé teszi az olyan élvonalbeli biztonsági paradigmák használatát, mint az [Azure bizalmas számítástechnika](../confidential-computing/overview.md) és az intelligens peremhálózat védelme. Ügyfeleinknek lehetősége van arra, hogy egymástól függetlenül ellenőrizzék a gép helyét, a virtuális gép (VM) helyzetét a gépen, valamint azt a környezetet, amelyben az enklávék futnak a virtuális gépen. Az Azure igazolása ezeket és számos további ügyfél-kérelmet is felruház.

Az Azure igazolása befogadja a számítási entitásokból származó bizonyítékokat, bekapcsolja őket egy készletbe, érvényesíti azokat a konfigurálható házirendekkel, és kriptográfiai igazolásokat állít elő a jogcímbarát alkalmazásokhoz (például a függő entitásokhoz és a naplózási hatóságokhoz).

## <a name="use-cases"></a>Használati esetek

Az Azure igazolása átfogó igazolási szolgáltatásokat nyújt több környezethez és megkülönböztető használati esetekhez.

### <a name="sgx-attestation"></a>SGX ENKLÁVÉHOZ igazolása

A SGX ENKLÁVÉHOZ olyan hardveres elkülönítésre utal, amely bizonyos Intel CPU-modellekben támogatott. A SGX ENKLÁVÉHOZ lehetővé teszi, hogy a kód a SGX ENKLÁVÉHOZ enklávés néven ismert, megtisztított rekeszekben fusson. A hozzáférés-és a memória-engedélyeket a hardver kezeli, így biztosítva a megfelelő elkülönítést biztosító minimális támadási felületet.

Az ügyfélalkalmazások úgy is kiállíthatók, hogy kihasználják a SGX ENKLÁVÉHOZ enklávék előnyeit azáltal, hogy a biztonság szempontjából kényes feladatokat delegálnak az adott enklávékban. Az ilyen alkalmazások ezt követően az Azure-igazolás használatával rutinszerűen hozhatnak létre megbízhatóságot az enklávéban, és képesek hozzáférni a bizalmas adatokhoz.

### <a name="vbs-attestation"></a>VBS-igazolás

A VBS egy szoftveres architektúra az enklávé memória-védelméhez a Hyper-V alapján. Megakadályozza a gazdagépek rendszergazdai kódját, valamint a helyi és a Felhőbeli szolgáltatás-rendszergazdák számára az adatok elérését egy VBS enklávéban, vagy annak végrehajtását.

A SGX ENKLÁVÉHOZ technológiához hasonlóan az Azure-igazolás is támogatja a VBS enklávék érvényesítését a konfigurált szabályzatok alapján, valamint az érvényesség igazolására szolgáló minősítési nyilatkozat kiállítását.

### <a name="open-enclave"></a>Nyitott enklávé
Az [Open enklávé](https://openenclave.io/sdk/) (OE) olyan kódtárak gyűjteménye, amelyek egyetlen egységes enclaving absztrakciót hoznak létre a fejlesztők számára a Tee-alapú alkalmazások létrehozásához. Univerzális biztonságos alkalmazás-modellt kínál, amely a platform sajátosságait is lekicsinyíti. A Microsoft fontos lépésként tekinti át a demokratizálása-alapú enklávé-technológiákat, például a SGX ENKLÁVÉHOZ, és növeli az Azure-ban való felvételét.

Az OE az enklávé-tanúsítványok ellenőrzésére vonatkozó konkrét követelményeket Szabványosít. Ez megfelel az OE-nek, amely az Azure-igazolások kiválóan illeszkedő tanúsítvány-felhasználója.

## <a name="azure-attestation-can-run-in-a-tee"></a>Az Azure-igazolás egy PÓLÓban is futtatható

Az Azure-igazolás kritikus fontosságú a bizalmas számítástechnikai forgatókönyvek esetében, mivel az a következő műveleteket végzi el:

- Ellenőrzi, hogy érvényes-e az enklávé bizonyítéka.
- Kiértékeli az enklávét a felhasználó által meghatározott házirend alapján.
- Kezeli és tárolja a bérlőre vonatkozó házirendeket.
- Létrehoz és aláír egy jogkivonatot, amelyet a függő entitások a enklávéval való interakcióra használnak.

Az Azure-igazolás két különböző típusú környezetben futtatható:
- Az Azure-igazolás egy SGX ENKLÁVÉHOZ-kompatibilis PÓLÓn fut.
- Az Azure-igazolás nem a PÓLÓn fut.

Az Azure igazolási ügyfelei kifejezték azt a követelményt, hogy a Microsoft működésbe kerüljön a megbízható számítástechnikai bázis (TCB) alapján. Ezzel megakadályozhatja, hogy a Microsoft entitások, például a virtuálisgép-rendszergazdák, a gazda-rendszergazdák és a Microsoft-fejlesztők módosíthassák az igazolási kérelmeket, a szabályzatokat és az Azure-tanúsítványok kiállított jogkivonatait. Az Azure-igazolás a PÓLÓban is futtatható, ahol az Azure-igazolások, például az árajánlatok érvényesítése, a jogkivonat-létrehozás és a jogkivonat-aláírás funkcióit egy SGX ENKLÁVÉHOZ enklávéba helyezi át a rendszer.

## <a name="why-use-azure-attestation"></a>Miért érdemes az Azure-igazolást használni?

Az Azure-igazolás a legjobb választás a pólók igazolására, mivel az a következő előnyöket kínálja: 

- Egységes keretrendszer több pólók, például a SGX ENKLÁVÉHOZ enklávék és a VBS enklávék igazolásához
- Több-bérlős szolgáltatás, amely lehetővé teszi az egyéni igazolási szolgáltatók és házirendek konfigurálását a jogkivonat-generálás korlátozására
- Olyan alapértelmezett szolgáltatót kínál, amely a felhasználóktól való konfiguráció nélkül képes tanúsítani
- A SGX ENKLÁVÉHOZ enklávéban való használat közben védi az adatvédelmet
- Olyan, magasan elérhető szolgáltatás, amely szolgáltatói szerződés kínál (SLA)

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>Üzletmenet-folytonosság és vész-helyreállítási (BCDR) támogatás

Az [üzletmenet folytonossága és](/azure/best-practices-availability-paired-regions) a vész-helyreállítási szolgáltatás (BCDR) lehetővé teszi az Azure-igazolást, hogy enyhítse a jelentős rendelkezésre állási problémákkal vagy egy adott régióban észlelt katasztrófákkal kapcsolatos szolgáltatások megszakadását.

A BCDR által jelenleg támogatott régiók alább találhatók
- USA 2. keleti régiója – az USA középső régiója> párosítva.
- USA középső régiója => az USA 2. keleti régiójában.

A két régióban üzembe helyezett fürtök normál körülmények között egymástól függetlenül működnek. Az egyik régió hibája vagy kimaradása esetén a következő történik:

- Az Azure igazolási BCDR zökkenőmentes feladatátvételt tesz lehetővé, amelyben az ügyfeleknek nem kell további lépéseket tenniük a helyreállításhoz
- A régióhoz tartozó [Azure Traffic Manager](../traffic-manager/index.yml) felismeri az állapot-mintavételt, és átváltja a végpontot a párosított régióra.
- A meglévő kapcsolatok nem fognak működni, és belső kiszolgálóhiba-vagy időtúllépési problémákat fognak kapni
- A vezérlési sík összes művelete le lesz tiltva. Az ügyfelek nem tudnak tanúsítvány-előállítók létrehozására és a szabályzatok frissítésére az elsődleges régióban
- Az összes adatsík művelet, beleértve a tanúsító hívásokat, továbbra is az elsődleges régióban fog működni

## <a name="next-steps"></a>Következő lépések
- Ismerje meg az [Azure igazolásának alapfogalmait](basic-concepts.md)
- [Igazolási szabályzat létrehozása és aláírása](author-sign-policy.md)
- [Az Azure-igazolás beállítása a PowerShell használatával](quickstart-powershell.md)

