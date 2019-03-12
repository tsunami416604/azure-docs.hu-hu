---
title: Egy Azure container registryt GEO-replikálásához
description: Ismerkedés a georeplikált Azure container registryk létrehozásába és kezelésébe.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview
ms.date: 04/10/2018
ms.author: stevelas
ms.openlocfilehash: 2dc314dd1d1e728f03c1d0c660d9339254ddc462
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541859"
---
# <a name="geo-replication-in-azure-container-registry"></a>Georeplikáció az Azure Container Registryben

Egy helyi jelenlét, vagy egy gyakran használt adatok biztonsági mentése, kívánó vállalatok szolgáltatások futtatása több Azure-régió közül válassza ki. Ajánlott eljárásként elhelyezése egy tároló-beállításjegyzéket minden egyes régióban, ahol lemezképek futnak hálózatközeli operations, gyors és megbízható kép réteg adatátvitel engedélyezése lehetővé teszi. Georeplikáció lehetővé teszi egy egyetlen regisztrációs adatbázisként működik, az Azure container registry több régióban folyamatazonosítóval rendelkező több főkiszolgálós regionális beállításjegyzékek.

A georeplikált beállításjegyzékbe a következő előnyöket nyújtja:

* Egyetlen beállításjegyzék/képcímke nevek segítségével több régióban
* Hálózatközeli beállításjegyzék hozzáférést regionálisan üzemelő példányokba
* Nincsenek további forgalmi díjat képként kikerülnek az tárológazda ugyanabban a régióban egy helyi, a replikált beállításjegyzék
* Egyetlen felügyeleti beállításjegyzék több régióban

> [!NOTE]
> Másolatait a tárolórendszerképek az egynél több Azure container registryben van szüksége, ha az Azure Container Registry támogatja a is [kép importálása](container-registry-import-images.md). Például egy DevOps-munkafolyamatban importálhat egy képet fejlesztési beállításjegyzék éles beállításjegyzékhez, anélkül, hogy a Docker-parancsokkal.
>

## <a name="example-use-case"></a>Példa használati esetekhez
Contoso fut, az Egyesült Államok, Kanada és Európában lévő nyilvános jelenlét webhely. E piacokon kiszolgálása a helyi és a hálózatközeli tartalommal, Contoso fut [Azure Kubernetes Service](/azure/aks/) (AKS)-fürtök az USA nyugati RÉGIÓJA, USA keleti RÉGIÓJA, közép-Kanada és Nyugat-Európa. A webhely alkalmazást, egy Docker-rendszerképet telepített a kód és a kép minden régióban használja. Tartalom helyi régióban, veszi át egy adatbázis, amely egyedi az egyes régiókban van kiépítve. Minden egyes regionális központi telepítés rendelkezik az erőforrásokhoz, mint a helyi adatbázis egyedi konfigurációjában.

A fejlesztői csapat található Seattle WA, az USA nyugati RÉGIÓJA adatközpont felhasználásával.

![Több beállításjegyzékek leküldése](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Több beállításjegyzékek leküldése*

Előtt a georeplikációs funkciókat használ, a Contoso egy Egyesült Államokbeli székhelyű beállításjegyzék az USA nyugati RÉGIÓJA, Nyugat-Európában, egy további beállításjegyzék rendelkezett. Ezek a különböző régiók kiszolgálására, a fejlesztői csapat leküldött rendszerképek két különböző beállításjegyzékek.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![A több beállításjegyzékek lekérése](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*A több beállításjegyzékek lekérése*

Tipikus kihívást több beállításjegyzékek, a következők:

* Az összes USA keleti RÉGIÓJA, USA nyugati RÉGIÓJA és közép-Kanada fürtök forgalmi díjat generálnak, a szóban forgó gazdagépek távoli tároló minden egyes rendszerképek lekérése adatközpontok USA nyugati RÉGIÓJA, USA nyugati RÉGIÓJA beállításjegyzékből való lekérése.
* A fejlesztői csapat kell rendszerképek leküldése a beállításjegyzékek USA nyugati Régiójában és Nyugat-Európa.
* A fejlesztői csapat kell konfigurálása és karbantartása egyes regionális központi lemezkép nevű a helyi beállításjegyzékben hivatkozik.
* Beállításjegyzék elérésének kell konfigurálni minden olyan régió esetében.

## <a name="benefits-of-geo-replication"></a>Georeplikáció előnyei

![A georeplikált beállításjegyzékbe való lekérése](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Az Azure Container Registry georeplikációs funkcióját használja, ezeket az előnyöket is felismerte, hogy:

* Regisztrációs adatbázis kezelése minden régióban: `contoso.azurecr.io`
* Egy egyetlen konfigurációja lemezképek központi telepítésére, felügyeletére minden régióban használja ugyanazt a kép URL-címe: `contoso.azurecr.io/public/products/web:1.2`
* Miközben ACR kezeli a georeplikációt, beleértve a regionális webhookok helyi értesítéseket egy egyetlen regisztrációs leküldése

## <a name="configure-geo-replication"></a>Aktív georeplikáció konfigurálása
Georeplikáció konfigurálása az olyan egyszerű, mintha a régiókat a térképen kattint.

Georeplikációs funkciója [prémium szintű beállításjegyzékek](container-registry-skus.md) csak. Ha a beállításjegyzék nem prémium szintű, módosíthatja az alapszintű és standard szintű, a prémium szintű, de a [az Azure portal](https://portal.azure.com):

![Váltás a termékváltozatok között az Azure Portalon](media/container-registry-skus/update-registry-sku.png)

A prémium szintű tárolójegyzékkel georeplikációt konfigurálásához jelentkezzen be az Azure Portalra a https://portal.azure.com.

Keresse meg az Azure Container Registrybe, és válassza ki **replikációk**:

![Replikációk az Azure Portal tárolójegyzékeinek felhasználói felületén](media/container-registry-geo-replication/registry-services.png)

Térkép jelenik meg, melyen az összes aktuális Azure-régióban:

 ![Régiótérkép az Azure Portalon](media/container-registry-geo-replication/registry-geo-map.png)

* Kék hatszögek jelölik az aktuális replika
* Zöld hatszögek képviselő lehetséges replika régiók
* Szürke hatszögek képviselő még nem érhető el a replikáció az Azure-régiók

-Replika konfigurálásához válassza ki a zöld hatszög, majd jelölje ki **létrehozás**:

 ![Replikáció létrehozásának felhasználói felülete az Azure Portalon](media/container-registry-geo-replication/create-replication.png)

További replikák konfigurálásához válassza ki a zöld hatszögek többi régió esetében, majd kattintson **létrehozás**.

ACR megkezdi a lemezképek szinkronizálása a beállított replikákat között. Ha elkészült, a portál tükrözi *készen*. A replika állapota a portálon nem frissül automatikusan. A frissítés gomb segítségével megtekintheti a frissített állapotot.

## <a name="geo-replication-pricing"></a>Georeplikáció – díjszabás

Georeplikációs funkciója a [prémium szintű Termékváltozat](container-registry-skus.md) az Azure Container Registrybe. A kívánt régiókhoz replikálásakor a beállításjegyzék díjak prémium beállításjegyzék díjak mindegyik régióhoz.

Az előző példában az Contoso konszolidált le egy, két beállításjegyzékek replikák hozzáadásával az USA keleti RÉGIÓJA, közép-Kanada és Nyugat-Európa. Contoso / hó, nincs további konfigurációs vagy felügyeleti négyszer prémium kell. Minden egyes régió most lekéri a saját rendszerképek helyi, teljesítmény, a megbízhatóság, Kanada, USA nyugati RÉGIÓJA és USA keleti RÉGIÓJA – hálózati forgalmi díjat nélkül.

## <a name="summary"></a>Összegzés

A georeplikáció kezelheti a regionális adatközpontok globális felhőként. Sok Azure-szolgáltatás használ rendszerképeket, gyors hálózatközeli, miközben egy egyetlen felügyeleti felületben rejlő előnyöket előnyeit, és lekéri a megbízható helyi rendszerképet.

## <a name="next-steps"></a>További lépések

Tekintse meg a háromrészes oktatóanyag-sorozatban [az Azure Container Registry georeplikációs](container-registry-tutorial-prepare-registry.md). A georeplikált beállításjegyzékbe létrehozásához, egy tároló létrehozása és majd telepítése egyetlen végig `docker push` parancs több regionális Web Apps for Containers-példányt.

> [!div class="nextstepaction"]
> [Az Azure Container Registry georeplikációja](container-registry-tutorial-prepare-registry.md)
