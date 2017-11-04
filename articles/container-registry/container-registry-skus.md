---
title: "Azure-tárolót beállításjegyzék termékváltozatok"
description: "A különböző szolgáltatásrétegeiben használt funkciókkal elérhető Azure tároló beállításjegyzék összehasonlítása"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: mmacy
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: stevelas
ms.openlocfilehash: 630bc088fcb6d3c7e5bb3a9713107c3fb6653ec6
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="azure-container-registry-skus"></a>Azure-tárolót beállításjegyzék termékváltozatok

Az Azure tároló beállításjegyzék (ACR) több szolgáltatásszinttel termékváltozatok néven érhető el. Ezek termékváltozatok adja meg, kiszámítható díjszabás és hogyan szeretné használni az Azure-ban a személyes Docker beállításjegyzék számos lehetőség közül választhat. Az egy magasabb szintű SKU biztosít, további teljesítmény és méretezés. Azonban a termékváltozatokat a azonos programozott képességeket biztosítják, a fejlesztő Ismerkedés a Basic, és a beállításjegyzék használati növekedése Standard és Premium átalakítása engedélyezése.

## <a name="basic"></a>Basic
Egy Azure tároló beállításjegyzék megtanulni fejlesztőknek költség optimalizált belépési pont. Alapszintű nyilvántartó azonos programozott funkciókkal rendelkeznek, mint a Standard és Premium (a Azure Active Directory hitelesítési integráció, a lemezkép törlése és a webes hurkok) rendelkezik, azonban van mérete és használati korlátozások.

## <a name="standard"></a>Standard
Standard nyilvántartó, Basic, a megnövekedett tárolási korlátokhoz és átviteli kép ugyanazokat a képességeket kínálnak. Standard nyilvántartó kell a legtöbb éles környezetben igényeinek kielégítéséhez.

## <a name="premium"></a>Prémium
Prémium szintű nyilvántartó magasabb korlátok korlátozásokat, például a tárolási és párhuzamos műveletek engedélyezése a nagy mennyiségű forgatókönyvek szolgálnak. Magasabb kép átviteli sebesség mellett prémium szintű ad funkciók, például [georeplikáció](container-registry-geo-replication.md) egyetlen beállításjegyzékbeli kezeléséhez különféle régiókban, minden egyes telepítéshez hálózati bezárású beállításjegyzékbeli karbantartása.

## <a name="classic"></a>Klasszikus
A klasszikus beállításjegyzék SKU eredeti kiadásának az Azure-ban az Azure-tároló beállításjegyzék szolgáltatás engedélyezve van. Klasszikus nyilvántartó üzemelnek az előfizetéshez, ezáltal megnehezítve a magasabb szintű képességeit, például a nagyobb átviteli sebesség és a georeplikáció biztosításához ACR létrehozó Azure storage-fiók. Korlátozott platformképességei miatt tervezzük érvényteleníthető a jövőben a klasszikus Termékváltozat.

> [!NOTE]
> A klasszikus beállításjegyzék SKU tervezett elavulása elkerülése érdekében javasoljuk összes új nyilvántartó Basic, Standard vagy prémium használja. További információ az létező klasszikus beállításjegyzék konvertálása: [módosítása termékváltozatok](#changing-skus).
>

## <a name="registry-sku-feature-matrix"></a>Beállításjegyzék SKU szolgáltatás mátrix

Az alábbi táblázat részletezi, a szolgáltatások és a Basic, Standard és Premium szolgáltatásszintek határain.

| Szolgáltatás | Basic | Standard | Prémium |
|---|---|---|---|---|
| Storage | 10 giB | 100 giB| 500 giB |
| Percenként ReadOps<sup>1, 2</sup> | 1k | 300-k | 10 000-k |
| Percenként WriteOps<sup>1, 3</sup> | 100 | 500 | 2-k |
| Töltse le a MB/s sávszélesség<sup>1</sup> | 30 | 60 | 100 |
| Töltse fel a MB/s sávszélesség<sup>1</sup> | 10 | 20 | 50 |
| webhook | 2 | 10 | 100 |
| Georeplikáció | N/A | N/A | [Támogatott *(előzetes verzió)*](container-registry-geo-replication.md) |

<sup>1</sup> *ReadOps*, *WriteOps*, és *sávszélesség* minimális hozzávetőlegesek. ACR nagy hangsúlyt fektet használati van szüksége a teljesítmény javítása érdekében.

<sup>2</sup> [docker lekéréses](https://docs.docker.com/registry/spec/api/#pulling-an-image) az eszköz több olvasási műveletek a lemezkép, valamint a jegyzék beolvasása rétegek száma alapján.

<sup>3</sup> [docker leküldéses](https://docs.docker.com/registry/spec/api/#pushing-an-image) az eszköz több írási műveleteket, rétegeket, amelyeket a leküldött értesítést kell száma alapján. A `docker push` tartalmaz *ReadOps* egy meglévő lemezképet jegyzékfájljának beolvasása.

## <a name="manage-registry-size"></a>A rendszerleíró adatbázis mérete kezelése
Minden egyes SKU tárolási korlátai célja, hogy megfelel-e a jellemző forgatókönyv: első lépések a Basic, Standard, a legtöbb éles alkalmazások és a prémium kapacitású teljesítmény és [georeplikáció](container-registry-geo-replication.md). A beállításjegyzék élettartama során kezelje nem használt tartalom rendszeresen törlésével annak méretét.

A tároló beállításjegyzékben található az aktuális használatát, a beállításjegyzék **áttekintése** az Azure-portálon:

![Beállításjegyzék használati adatokat az Azure-portálon](media/container-registry-skus/registry-overview-quotas.png)

A beállításjegyzék méretének törölni kell az Azure portálon adattárak kezelheti.

A **szolgáltatások**, jelölje be **Tárházak**, majd kattintson a jobb gombbal a kívánt törölje, majd válassza ki a tárház **törlése**.

![Törölje a tárházat az Azure-portálon](media/container-registry-skus/delete-repository-portal.png)

## <a name="changing-skus"></a>SKU módosítása

Módosíthatja a beállításjegyzék SKU az Azure portálon.

A beállításjegyzékben **áttekintése** az Azure portálon, válassza ki a **frissítés**, válassza ki egy új **SKU** a Termékváltozat legördülő.

![Frissítés tároló beállításjegyzék SKU Azure-portálon](media/container-registry-skus/update-registry-sku.png)

## <a name="changing-from-classic"></a>A klasszikus módosítása
Ha módosítja egy klasszikus beállításjegyzék alapszintű, a kapcsolódó tárfiók az előfizetésben a lemezképeket tároló kezeli az Azure storage-fiók meglévő Standard vagy prémium, Azure másolatok. A folyamat eltarthat egy ideig.

Az átalakítás során `docker pull` továbbra is működik, azonban `docker push` le van tiltva, amíg átalakítás befejeződik.

Ezt követően a program az előfizetés tárfiók ACR már nem használják.

### <a name="why-change-from-classic-to-basic-standard-or-premium"></a>Miért módosítja a klasszikus Basic, Standard vagy prémium?

Klasszikus nyilvántartó korlátozott szolgáltatásait, elkerülése érdekében javasoljuk, hogy a klasszikus nyilvántartó Basic, Standard vagy prémium rétegek módosítja. A magasabb szintű termékváltozatok a beállításjegyzék mélyebb integrálása az Azure képességeit. Ezek a képességek közé tartoznak:

* Azure Active Directory-integráció, egyéni hitelesítés (lásd: [az acr bejelentkezési](/cli/azure/acr?view=azure-cli-latest#az_acr_login))
* Kép és a címke törlése támogatása
* [Georeplikáció](container-registry-geo-replication.md)
* [Webhook](container-registry-webhook.md)

Az összes, a klasszikus beállításjegyzékbeli függ a storage-fiók adott Azure automatikusan létrehozza az Azure-előfizetéséhez, a beállításjegyzék létrehozásakor. Ezzel szemben a Basic, Standard és Premium termékváltozat előnyeit *tárolási felügyelt*. Ez azt jelenti, hogy Azure a tárterület átlátható módon kezeli az Ön--a képek külön tárfiókot nem jön létre a saját előfizetésének.

Néhány felügyelt tárolási Basic, Standard és Premium nyilvántartó által nyújtott előnyöket nyújtja:

* Tároló lemezképek [titkosítása](../storage/common/storage-service-encryption.md).
* Lemezképek használatával tárolja [georedundáns tárolás](../storage/common/storage-redundancy.md#geo-redundant-storage)modulhoz, biztonsági mentési több területi replikáció a képek.
* Képes [termékváltozatok közötti áthelyezése](#changing-skus), ha úgy dönt, hogy egy magasabb szintű SKU, amely lehetővé teszi, hogy nagyobb átviteli sebességgel. Az egyes SKU ACR is megfeleljenek a átviteli sebesség növelése az igényeinek. Az alapul szolgáló implementáció, hogyan ACR eléri-e a kívánt átviteli kifejezett *leképezés* (által kiválasztásával magasabb termékváltozatok), anélkül, hogy kellene kezelnie végrehajtása részleteit.

## <a name="pricing"></a>Díjszabás

Az egyes az Azure-tároló beállításjegyzék termékváltozatok árakról, lásd: [tároló beállításjegyzék árképzési](https://azure.microsoft.com/pricing/details/container-registry/).

## <a name="next-steps"></a>Következő lépések

**Azure-tárolót beállításjegyzék terv**

Látogasson el a [ACR terv](https://aka.ms/acr/roadmap) a Githubon tájékozódhat a szolgáltatás a közeljövőben megjelenő funkciókról.

**Azure-tárolót beállításjegyzék UserVoice**

Küldje el, majd az új szolgáltatási javaslataikat szavazhatnak [ACR UserVoice](https://feedback.azure.com/forums/903958-azure-container-registry).