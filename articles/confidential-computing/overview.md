---
title: Az Azure bizalmas számítástechnika áttekintése
description: Az Azure bizalmas (ACC) számítástechnika áttekintése
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: b52af8f41f32c224fd7c4fa0f5c539b23d231f49
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149304"
---
# <a name="confidential-computing-on-azure"></a>Bizalmas számítástechnika az Azure-ban

Az Azure bizalmas számítástechnikai szolgáltatása lehetővé teszi a bizalmas adatok elkülönítését a felhőben történő feldolgozás során. Számos iparág bizalmas számítástechnikai adatok védelmére használja fel az adataikat. Ezek a számítási feladatok a következők:

- Pénzügyi adatbiztonság
- A beteg adatainak védelme
- Gépi tanulási folyamatok futtatása bizalmas adatokon
- Algoritmusok végrehajtása több forrásból származó titkosított adatkészleteken


## <a name="overview"></a>Áttekintés
<p><p>

> [!VIDEO https://www.youtube.com/embed/Qu6sP0XDMU8]

Tudjuk, hogy fontos a Felhőbeli adatvédelme. Halljuk az Ön problémáit. Íme néhány kérdés, miszerint ügyfeleinknek lehet a felhőbe áthelyezniük a bizalmas számítási feladatokat: 

- Hogyan győződjön meg arról, hogy a Microsoft nem fér hozzá a nem titkosított adatforgalomhoz?
- Hogyan a vállalaton belül a rendszerjogosultságú rendszergazdák biztonsági fenyegetésének megelőzése?
- Milyen módon lehet megakadályozni, hogy harmadik felek hozzáférjenek a bizalmas ügyféladatok eléréséhez?

A Microsoft Azure segítségével csökkentheti a támadási felületet, így erősebb adatvédelmet nyerhet. Az Azure már számos olyan eszközt kínál, amelyekkel a különböző modelleken keresztül biztosíthatja az [**adatok**](../security/fundamentals/encryption-atrest.md) védelmét, például az ügyféloldali titkosítással és a kiszolgálóoldali titkosítással. Az Azure emellett a biztonságos protokollok, például a TLS [**és a HTTPS protokollon keresztül is**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) titkosítja az adatátviteli mechanizmusokat. Ez az oldal az adattitkosítás harmadik szakaszát mutatja be – a **használatban**lévő adattitkosítást.


## <a name="introduction-to-confidential-computing"></a>A bizalmas számítástechnika bemutatása<a id="intro to acc"></a>

A bizalmas számítástechnika a bizalmas számítástechnikai [konzorcium](https://confidentialcomputing.io/) (CCC) által meghatározott iparági kifejezés, amely a bizalmas számítástechnika bevezetésének meghatározására és felgyorsítására dedikált alap. A bizalmas számítástechnika a számítások végrehajtásakor használt adatok védelme. A számítások egy hardveres megbízható végrehajtási környezetben (TEE) történnek.

A TEE olyan környezet, amely csak a jóváhagyott kód végrehajtását kényszeríti ki. A TEE-ben lévő összes adattal nem lehet beolvasni vagy módosítani az adott környezeten kívüli kódokat.

### <a name="enclaves-and-trusted-execution-environments"></a>Enklávék és megbízható végrehajtási környezetek

A bizalmas számítástechnikai környezetekben a pólókat gyakran *enklávéknak* vagy *biztonságos enklávéknak*nevezzük. Az enklávék a hardver processzorának és memóriájának biztonságos részei. Az enklávéban még egy hibakeresővel sem lehet megtekinteni az adatelemeket vagy a kódokat. Ha a nem megbízható kód megkísérli módosítani a tartalmat az enklávé memóriájában, a környezet le lesz tiltva, és a rendszer megtagadja a műveleteket.

Az alkalmazások fejlesztése során a [szoftvereszközök](#oe-sdk) segítségével a kód és az adatközpontban tárolt adatvédelmek részeinek védelmét is elvégezheti. Ezek az eszközök biztosítják, hogy a kód és az adatai nem tekinthetők meg és nem módosíthatók a megbízható környezeten kívüli személyek számára. 

Alapvetően az enklávét fekete dobozként kell meggondolni. A mezőbe a titkosított kódot és az adatmezőket helyezheti el. A mező kívülről nem láthat semmit. Az enklávénak egy kulcsot kell adnia az adatvisszafejtéshez, majd újra fel kell dolgoznia és titkosítania kell az adatközpontot az enklávéból való kiküldésük előtt.

### <a name="attestation"></a>Igazolási

Meg kell kérnie a megbízható környezet biztonságos ellenőrzésének és érvényesítésének megszerzését. Ez az ellenőrzés az igazolási folyamat. 

Az igazolás lehetővé teszi, hogy a függő entitás nagyobb megbízhatóságot biztosítson, hogy a szoftverük (1) egy enklávéban fut, és (2) az enklávé naprakész és biztonságos. Az enklávé például arra kéri a mögöttes hardvert, hogy olyan hitelesítő adatokat állítson elő, amely igazolja, hogy az enklávé létezik a platformon. A jelentést ezután egy második enklávé is megadhatja, amely ellenőrzi, hogy a jelentés ugyanazon a platformon lett létrehozva.

Az igazolást olyan biztonságos igazolási szolgáltatás használatával kell megvalósítani, amely kompatibilis a rendszerszoftverrel és a szilíciummal. Az [Intel tanúsítvány-és kiépítési szolgáltatásai](https://software.intel.com/sgx/attestation-services) kompatibilisek az Azure bizalmas számítástechnikai virtuális gépekkel.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Az Azure használata felhőalapú, bizalmas számítástechnikai rendszerekhez<a id="cc-on-azure"></a>

Az Azure bizalmas számítástechnikai részlege lehetővé teszi, hogy a virtualizált környezetekben a bizalmas számítástechnikai funkciókat használja. Mostantól eszközöket, szoftvereket és felhőalapú infrastruktúrát is használhat a biztonságos hardverre való kiépítéshez. 

### <a name="virtual-machines"></a>Virtuális gépek

Az Azure az első olyan felhőalapú szolgáltató, amely a virtualizált környezetekben nyújt bizalmas számítástechnikai szolgáltatásokat. Olyan virtuális gépeket fejlesztettünk ki, amelyek absztrakt rétegként működnek a hardver és az alkalmazás között. A számítási feladatokat nagy méretekben, a redundancia és a rendelkezésre állási lehetőségek használatával futtathatja.  

#### <a name="intel-sgx-enabled-virtual-machines"></a>Intel SGX ENKLÁVÉHOZ-kompatibilis Virtual Machines

Az Azure bizalmas számítástechnikai virtuális gépeken a CPU hardverének egy része az alkalmazás kódjának és adatainak egy részére van fenntartva. Ez a korlátozott rész az enklávé. 

![VM-modell](media/overview/hardware-backed-enclave.png)

Az Azure bizalmas számítástechnikai infrastruktúrája jelenleg a virtuális gépek (VM-EK) speciális SKU-jának részét alkotja. Ezek a virtuális gépek Intel processzorokon futnak a szoftverfrissítési Gárda (Intel SGX ENKLÁVÉHOZ) révén. Az [Intel SGX enklávéhoz](https://intel.com/sgx) az a komponens, amely lehetővé teszi a bizalmas számítástechnikai szolgáltatással való fokozott védelmet. 

Napjainkban az Azure az Intel SGX ENKLÁVÉHOZ technológiára épülő [DCsv2-sorozatot](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) kínálja a hardveres enklávé létrehozásához. Létrehozhat biztonságos enklávé-alapú alkalmazásokat az DCsv2-sorozatú virtuális gépeken való futtatáshoz az alkalmazásadatok és a használatban lévő kódok védelme érdekében. 

[További](virtual-machine-solutions.md) információ az Azure bizalmas számítástechnikai virtuális gépek hardveres megbízható enklávékkal történő üzembe helyezéséről.

## <a name="application-development"></a>Alkalmazásfejlesztés<a id="application-development"></a>

Az enklávék és az elszigetelt környezetek hatékonyságának kihasználásához a bizalmas számítástechnikai eszközöket támogató eszközöket kell használnia. Az enklávé-alkalmazások fejlesztését számos eszköz segíti. Használhatja például a következő nyílt forráskódú keretrendszereket: 

- [Az Open enklávé szoftverfejlesztői készlete (SDK)](https://github.com/openenclave/openenclave)
- [A bizalmas konzorciumi keretrendszer (CCF)](https://github.com/Microsoft/CCF)

### <a name="overview"></a>Áttekintés

Az enklávékkal létrehozott alkalmazások kétféleképpen vannak particionálva:
1. "Nem megbízható" összetevő (a gazdagép)
1. "Megbízható" összetevő (az enklávé)

**A gazdagép** nem megbízható környezetben futó enklávé-alkalmazás. A gazdagépen található kód nem fér hozzá az enklávéba betöltött kódhoz. 

**Az enklávé** , ahol a kód és az adatsorozat a póló implementációjában fut. A biztonságos számítások az enklávéban történnek, hogy biztosítható legyen a titkok és a bizalmas adatok védelme. 

Az enklávé-alkalmazások fejlesztésének megkezdése előtt meg kell határoznia, hogy milyen programkódra és adatvédelemre van szükség. A megbízható összetevőbe helyezett kód el van különítve az alkalmazás többi részétől. Miután az enklávé inicializálása megtörtént, és a kód betöltődik a memóriába, a kód nem olvasható be és nem módosítható külső védett környezetből.

### <a name="open-enclave-software-development-kit-oe-sdk"></a>Open enklávé szoftverfejlesztői készlet (OE SDK)<a id="oe-sdk"></a>

Ha egy enklávéban futó kódot szeretne írni, használja a szolgáltató által támogatott függvénytárat vagy keretrendszert. Az [Open ENKLÁVÉ SDK](https://github.com/openenclave/openenclave) (OE SDK) egy nyílt forráskódú SDK, amely lehetővé teszi a különböző bizalmas számítástechnikai hardverek absztrakt használatát. 

Az OE SDK egyetlen absztrakciós rétegként van felépítve bármely CSP hardverén. Az OE SDK az Azure-beli bizalmas számítástechnikai virtuális gépeken is használható a enklávékban található alkalmazások létrehozásához és futtatásához.

## <a name="next-steps"></a>További lépések

Helyezzen üzembe egy DCsv2-sorozatú virtuális gépet, és telepítse az OE SDK-t.

> [!div class="nextstepaction"]
> [Bizalmas számítástechnikai virtuális gép üzembe helyezése az Azure Marketplace-en](quick-create-marketplace.md)