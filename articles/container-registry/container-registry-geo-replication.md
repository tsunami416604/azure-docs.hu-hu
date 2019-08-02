---
title: Az Azure Container Registry földrajzi replikálása
description: Ismerkedjen meg a Geo-replikált Azure Container-jegyzékek létrehozásával és kezelésével.
services: container-registry
author: stevelas
manager: gwallace
ms.service: container-registry
ms.topic: overview
ms.date: 05/24/2019
ms.author: stevelas
ms.openlocfilehash: e17b70843fcda1d183de1b81a98da53138835340
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309594"
---
# <a name="geo-replication-in-azure-container-registry"></a>Georeplikáció az Azure Container Registryben

Azok a vállalatok, amelyek helyi jelenlétet vagy gyors biztonsági mentést kívánnak, több Azure-régióból is futtathatják a szolgáltatásokat. Ajánlott eljárásként helyezzen üzembe egy tároló-beállításjegyzéket minden olyan régióban, ahol a lemezképek futnak, és lehetővé teszi a hálózati bezárást, amely lehetővé teszi a gyors és megbízható képrétegbeli adatátvitelt. A Geo-replikáció lehetővé teszi, hogy az Azure Container Registry egyetlen regisztrációs adatbázisként működjön, több régiót is kínál több főkiszolgálós regionális nyilvántartással. 

A földrajzilag replikált beállításjegyzék a következő előnyöket biztosítja:

* Egyetlen beállításjegyzék-/képfájl-vagy-címke neve több régióban is használható
* Hálózati Bezárás a beállításjegyzékből a regionális telepítések között
* Nincs további kimenő forgalomra vonatkozó díj, mivel a lemezképek egy helyi, replikált beállításjegyzékből származnak, amely ugyanabban a régióban található, mint a tároló-gazdagép
* A beállításjegyzék egyetlen felügyelete több régióban

> [!NOTE]
> Ha több Azure Container registryben kell karbantartani a tároló lemezképeit, Azure Container Registry is támogatja a lemezképek [importálását](container-registry-import-images.md). Egy DevOps-munkafolyamatban például a rendszerképeket importálhatja egy fejlesztői beállításjegyzékből egy éles beállításjegyzékbe anélkül, hogy a Docker-parancsokat kellene használnia.
>

## <a name="example-use-case"></a>Példa használati esetre
A contoso nyilvános jelenlétű webhelyet futtat az Egyesült Államok, Kanada és Európa területén. Ha ezeket a piacokat helyi és hálózati tartalommal szeretné kiszolgálni, a contoso az USA nyugati régiójában, az USA keleti régiójában, a közép-Kanadában és Nyugat-Európában található [Azure Kubernetes Service](/azure/aks/) (ak) fürtöket futtatja. A Docker-rendszerképként üzembe helyezett webhely-alkalmazás ugyanazt a kódot és képet használja az összes régióban. Az adott régióhoz tartozó tartalmat a rendszer beolvassa egy adatbázisból, amely az egyes régiókban egyedi módon van kiépítve. Minden regionális központi telepítés egyedi konfigurációval rendelkezik a helyi adatbázishoz hasonló erőforrásokhoz.

A fejlesztői csapat Seattle WA-ben található, amely az USA nyugati régiójának adatközpontját használja.

![Több beállításjegyzékbe való továbbítás](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Több beállításjegyzékbe való továbbítás*

A földrajzi replikálási funkciók használata előtt a contoso az USA nyugati régiójában volt az USA-beli regisztrációs adatbázis, amely a Nyugat-Európában további beállításjegyzéket tartalmaz. A különböző régiók kiszolgálása érdekében a fejlesztői csapat két különböző beállításjegyzékbe küldi a lemezképeket.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Több beállításjegyzékből való húzás](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Több beállításjegyzékből való húzás*

Több beállításjegyzék tipikus kihívásai többek között a következők:

* Az USA keleti régiója, az USA nyugati régiója és a kanadai középső klaszterek mind lekérik az USA nyugati régiójában.
* A fejlesztői csapatnak képeket kell leküldenie az USA nyugati régiójában és a Nyugat-európai nyilvántartásba.
* A fejlesztői csapatnak minden regionális központi telepítést konfigurálnia kell, és meg kell őriznie a helyi beállításjegyzékre hivatkozó képnevekkel.
* Minden régióhoz konfigurálni kell a beállításjegyzék-hozzáférést.

## <a name="benefits-of-geo-replication"></a>A Geo-replikáció előnyei

![Kihúzás földrajzilag replikált beállításjegyzékből](media/container-registry-geo-replication/after-geo-replicate-pull.png)

A Azure Container Registry geo-replikációs funkciójának használatával ezek az előnyök megvalósulnak:

* Egyetlen beállításjegyzék kezelése minden régióban:`contoso.azurecr.io`
* A rendszerkép központi telepítésének egyetlen konfigurációját kezelheti, mivel az összes régió ugyanazt a képurl-címet használta:`contoso.azurecr.io/public/products/web:1.2`
* Leküldés egyetlen beállításjegyzékbe, míg az ACR kezeli a Geo-replikálást. A regionális webhookok konfigurálásával értesítéseket kaphat az eseményekről bizonyos replikákban. [](container-registry-webhook.md)

## <a name="configure-geo-replication"></a>Aktív georeplikáció konfigurálása

A földrajzi replikálás konfigurálása olyan egyszerű, mintha a térképen a régiók elemre kattintana. A Geo-replikációt az Azure CLI-ben az az [ACR Replication](/cli/azure/acr/replication) parancsokkal is kezelheti.

A Geo-replikáció csak a [prémium szintű kibocsátásiegység-forgalmi jegyzékek](container-registry-skus.md) egyik funkciója. Ha a beállításjegyzék még nem prémium szintű, az alapszintű és a standard csomagról prémiumra válthat a [Azure Portalban](https://portal.azure.com):

![SKU-i váltás a Azure Portalban](media/container-registry-skus/update-registry-sku.png)

A prémium szintű beállításjegyzék geo-replikációjának konfigurálásához jelentkezzen be a Azure Portalba https://portal.azure.com a következő címen:.

Navigáljon a Azure Container Registryhoz, és **** válassza a replikálások lehetőséget:

![Replikációk az Azure Portal tárolójegyzékeinek felhasználói felületén](media/container-registry-geo-replication/registry-services.png)

Megjelenik egy Térkép, amely az összes aktuális Azure-régiót megjeleníti:

 ![Régiótérkép az Azure Portalon](media/container-registry-geo-replication/registry-geo-map.png)

* A kék hatszögek az aktuális replikákat jelölik
* A zöld hatszögek a lehetséges replika régiókat jelölik
* A szürke hatszögek a replikáláshoz még nem elérhető Azure-régiókat jelölik

A replika konfigurálásához válasszon ki egy zöld hatszöget, majd válassza a **Létrehozás**lehetőséget:

 ![Replikáció létrehozásának felhasználói felülete az Azure Portalon](media/container-registry-geo-replication/create-replication.png)

További replikák konfigurálásához válassza a többi régió zöld hatszögét, majd kattintson a **Létrehozás**gombra.

Az ACR megkezdi a lemezképek szinkronizálását a konfigurált replikák között. Ha elkészült, a portál *készen áll*. A portálon lévő replika állapota nem frissül automatikusan. A frissítés gomb használatával tekintse meg a frissített állapotot.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>A földrajzilag replikált beállításjegyzék használatának szempontjai

* A földrajzilag replikált beállításjegyzék minden régiója független a beállítás után. Azure Container Registry SLA-kat az egyes földrajzilag replikált régiókban alkalmazza.
* Amikor leküldi vagy lekéri a képeket egy földrajzilag replikált beállításjegyzékből, az Azure Traffic Manager a háttérben elküldi a kérést az Önhöz legközelebb eső régióban található beállításjegyzékbe.
* Miután leküldte a rendszerképet vagy a címkét a legközelebbi régióra, időbe telik, amíg a Azure Container Registry replikálja a jegyzékeket és a rétegeket a többi, Ön által választott régióba. A nagyobb méretű képek replikálásához hosszabb időt is igénybe kell venni, mint a kisebbek. A rendszer a képeket és címkéket szinkronizálja a replikációs régiók között egy végleges konzisztencia-modellel.
* A földrajzilag replikált beállításjegyzék leküldéses frissítéseitől függő munkafolyamatok kezeléséhez javasoljuk, hogy a [](container-registry-webhook.md) webhookokat úgy konfigurálja, hogy válaszoljanak a leküldéses eseményekre. A regionális webhookok a földrajzilag replikált beállításjegyzékben állíthatók be, hogy nyomon kövessék a leküldéses eseményeket a földrajzilag replikált régiókban.


## <a name="geo-replication-pricing"></a>Geo-replikáció díjszabása

A Geo-replikáció a Azure Container Registry [Premium SKU](container-registry-skus.md) egyik funkciója. Ha a beállításjegyzéket a kívánt régióba replikálja, az egyes régiók esetében prémium szintű regisztrációs díjat kell fizetnie.

Az előző példában a contoso két regisztrációs adatbázisba konszolidált egyet, és replikákat ad hozzá az USA keleti régiója, Közép-Kanada és Nyugat-Európa között. A contoso havonta négy alkalommal fizet, további konfiguráció vagy felügyelet nélkül. Minden régió most lekéri a lemezképeket helyileg, a teljesítmény javítása, a megbízhatóság és az USA nyugati régiója és az USA keleti régiója között elérkező költségek nélkül.

## <a name="next-steps"></a>További lépések

Tekintse meg a három részből álló oktatóanyag-sorozatot, a [geo-replikációt Azure Container Registryban](container-registry-tutorial-prepare-registry.md). Végigvezeti a földrajzilag replikált beállításjegyzék létrehozásán, a tároló kialakításán, majd egyetlen `docker push` paranccsal, több regionális Web Apps a tárolók példányain való üzembe helyezésével.

> [!div class="nextstepaction"]
> [Geo-replikálás Azure Container Registry](container-registry-tutorial-prepare-registry.md)
