---
title: Regisztrációs adatbázis georeplikálása
description: Első lépések létrehozása és kezelése a georeplikált Azure-tároló beállításjegyzék, amely lehetővé teszi a beállításjegyzék több régióban több főkiszolgálós regionális replikák.
author: stevelas
ms.topic: article
ms.date: 08/16/2019
ms.author: stevelas
ms.openlocfilehash: d238de30e458261a11c941c03ac127c732ca8d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456440"
---
# <a name="geo-replication-in-azure-container-registry"></a>Georeplikáció az Azure Container Registry szolgáltatásban

Azok a vállalatok, amelyek helyi jelenlétet vagy gyorsbiztonsági mentést szeretnének, úgy döntenek, hogy több Azure-régióból is futtatnak szolgáltatásokat. Az ajánlott eljárás az, ha mindegyik olyan régióban elhelyez egy tárolóregisztrációs adatbázist, amelyben rendszerképeket futtat, így hálózatközeli műveletek futtathatók, ami lehetővé teszi a rendszerképek gyors és megbízható átvitelét. A georeplikáció segítségével az Azure Container Registry egyetlen regisztrációs adatbázisként futtatható, amely több régiót szolgál ki több főkiszolgálós regionális regisztrációs adatbázisokkal. 

A georeplikált regisztrációs adatbázis a következő előnyöket nyújtja:

* Több régióban is ugyanazok az adatbázis-/rendszerkép-/címkenevek használhatók
* A regisztrációs adatbázisok elérése hálózatközeli a regionálisan üzemelő példányokból
* Nincsenek további forgalmi díjak, mivel a rendszerképek lekérése egy, a tárológazdagéppel egy régióban lévő helyi, replikált regisztrációs adatbázisból történik
* A több régióra kiterjedő regisztrációs adatbázisok egy helyen felügyelhetők

> [!NOTE]
> Ha a tárolórendszerképek példányait több Azure-tároló beállításjegyzékében is karban kell tartania, az Azure Container Registry támogatja a [lemezképek importálását](container-registry-import-images.md)is. Például egy DevOps-munkafolyamatban importálhat egy lemezképet egy fejlesztési beállításjegyzékből egy termelési-forgalmi jegyzékbe, docker-parancsok használata nélkül.
>

## <a name="example-use-case"></a>Példa használati eset
A Contoso egy nyilvános jelenléti webhelyet üzemeltet az Egyesült Államokban, Kanadában és Európában. Ezeknek a piacoknak a helyi és hálózati szoros tartalommal való kiszolgálásához a Contoso az [Azure Kubernetes-szolgáltatás](/azure/aks/) (AKS) fürtjeit futtatja az USA nyugati részén, az USA keleti részén, Kanadában, Közép- és Nyugat-Európában. A Docker-rendszerképként üzembe helyezett webhelyalkalmazás ugyanazt a kódot és lemezképet használja az összes régióban. Az adott régióban helyi tartalmat egy adatbázisból olvassa be, amely az egyes régiókban egyedileg van kiépítve. Minden regionális központi telepítés egyedi konfigurációval rendelkezik az erőforrásokhoz, például a helyi adatbázishoz.

A fejlesztőcsapat seattle-i wa-i területen található, kihasználva az USA nyugati adatközpontját.

![Rámenős több kibocsátásijegyzékre](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Rámenős több kibocsátásijegyzékre*

A georeplikációs szolgáltatások használata előtt a Contoso usa-beli rendszerleíró adatbázissal rendelkezett az USA nyugati részén, egy további beállításjegyzékkel Nyugat-Európában. A különböző régiók kiszolgálása érdekében a fejlesztőcsapat két különböző beállításjegyzékbe tolta a képeket.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Lehúzás több kibocsátásijegyzékből](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Lehúzás több kibocsátásijegyzékből*

A több kibocsátásijegyzék tipikus kihívásai a következők:

* Az USA keleti régiója, az USA nyugati régiója és Kanada központi fürtjei mind lekérik az USA nyugati részének rendszerleíró adatbázisából, és kimenő díjakat vonnak be, mivel ezek a távoli tárolók mindegyike lekéri a képeket az USA nyugati részéről adatközpontokból.
* A fejlesztőcsapatnak képeket kell leadnia az USA nyugati és nyugat-európai nyilvántartásaiba.
* A fejlesztőcsapatnak konfigurálnia és karbantartania kell az egyes regionális központi telepítéseket a helyi beállításjegyzékre hivatkozó lemezképnevekkel.
* A beállításjegyzék-hozzáférést minden régióhoz konfigurálni kell.

## <a name="benefits-of-geo-replication"></a>A georeplikáció előnyei

![Lehúzás georeplikált rendszerleíró adatbázisból](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Az Azure Container Registry georeplikációs szolgáltatásának használatával ezek az előnyök valósulnak meg:

* Egyetlen beállításjegyzék kezelése az összes régióban:`contoso.azurecr.io`
* A lemezkép-telepítések egyetlen konfigurációjának kezelése, mivel az összes régió ugyanazt a lemezkép-URL-címet használta:`contoso.azurecr.io/public/products/web:1.2`
* Egyetlen beállításjegyzékbe, míg az ACR kezeli a georeplikációt. Konfigurálhatja a regionális [webhookokat, hogy értesítse](container-registry-webhook.md) az adott replikák ban lévő eseményeket.

## <a name="configure-geo-replication"></a>Aktív georeplikáció konfigurálása

A georeplikáció konfigurálása olyan egyszerű, mint a régiókra kattintani a térképen. A georeplikációt olyan eszközökkel is kezelheti, mint az [az acr replikációs](/cli/azure/acr/replication) parancsok az Azure CLI-ben, vagy telepíthet egy, az [Azure Resource Manager-sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication)rendelkező, georeplikációra engedélyezett beállításjegyzéket.

A georeplikáció csak a [prémium szintű beállításjegyzékek egyik szolgáltatása.](container-registry-skus.md) Ha a beállításjegyzék még nem prémium, az [Azure Portalon](https://portal.azure.com)átválthat Alapszintű és Standard verzióról Prémium verzióra:

![A sk-ek váltása az Azure Portalon](media/container-registry-skus/update-registry-sku.png)

A prémium szintű beállításjegyzék georeplikációjának konfigurálásához https://portal.azure.comjelentkezzen be az Azure Portalra a .

Nyissa meg az Azure Container Registry webhelyét, és válassza **a Replikációk**lehetőséget:

![Replikációk az Azure Portal tárolójegyzékeinek felhasználói felületén](media/container-registry-geo-replication/registry-services.png)

Megjelenik egy térkép, amely az összes aktuális Azure-régiót megjeleníti:

 ![Régiótérkép az Azure Portalon](media/container-registry-geo-replication/registry-geo-map.png)

* A kék hatszögek az aktuális replikákat jelölik
* A zöld hatszögek a lehetséges replikarégiókat jelölik
* A szürke hatszögek olyan Azure-régiókat jelölnek, amelyek még nem érhetők el a replikációhoz

Kópia konfigurálásához jelöljön ki egy zöld hatszöget, majd válassza a **Létrehozás**lehetőséget:

 ![Replikáció létrehozásának felhasználói felülete az Azure Portalon](media/container-registry-geo-replication/create-replication.png)

További kópiák konfigurálásához jelölje ki a zöld hatszöget más régiókhoz, majd kattintson a **Létrehozás gombra.**

Az ACR megkezdi a lemezképek szinkronizálását a konfigurált replikák között. Ha elkészült, a portál tükrözi *Ready*. A portál replikaállapota nem frissül automatikusan. A frissítés gombbal megtekintheti a frissített állapotot.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>A georeplikált rendszerleíró adatbázis használatával kapcsolatos szempontok

* A georeplikált beállításjegyzék minden egyes régiója a beállítás után független. Az Azure Container Registry SLA-k minden georeplikált régióra vonatkoznak.
* Amikor leküldéses vagy lekéréses lemezképek egy georeplikált beállításjegyzékből, az Azure Traffic Manager a háttérben elküldi a kérelmet a beállításjegyzék ben található az Önhöz legközelebb eső régióban.
* Miután leküldéses egy lemezkép vagy címke frissítése a legközelebbi régióba, némi időt vesz igénybe az Azure Container Registry replikálni a jegyzékek és a rétegek a fennmaradó régiókban, amely et felkért. A nagyobb képek replikálása hosszabb időt vesz igénybe, mint a kisebbeké. A rendszer a rendszer a rendszer egy végleges konzisztenciamodellel szinkronizálja a képeket és a címkéket a replikációs régiók között.
* A georeplikált leküldéses frissítésektől függő munkafolyamatok kezeléséhez azt javasoljuk, hogy konfigurálja a [webhookokat](container-registry-webhook.md) a leküldéses események reválaszolására. Regionális webhookokat állíthat be egy georeplikált beállításjegyzékben a leküldéses események nyomon követéséhez a georeplikált régiók között.

## <a name="delete-a-replica"></a>Replika törlése

Miután beállított egy kópiát a beállításjegyzékhez, bármikor törölheti, ha már nincs rá szükség. Töröljön egy replika az Azure Portalon vagy más eszközök, például az [az acr replikáció törlése](/cli/azure/acr/replication#az-acr-replication-delete) parancsot az Azure CLI használatával.

Replika törlése az Azure Portalon:

1. Nyissa meg az Azure Container Registry webhelyét, és válassza **a Replikációlehetőséget.**
1. Jelölje ki a kópia nevét, majd kattintson a **Törlés gombra.** Erősítse meg, hogy törölni szeretné a replikát.

> [!NOTE]
> A rendszerleíró adatbázis replikája nem törölhető a rendszerleíró adatbázis *saját régiójában,* azaz azon a helyen, ahol létrehozta a rendszerleíró adatbázist. Az otthoni kópiát csak úgy törölheti, hogy magát a rendszerleíró adatbázist törli.

## <a name="geo-replication-pricing"></a>Georeplikációs árképzés

A georeplikáció az Azure Container Registry [prémium termékváltozatának](container-registry-skus.md) egyik szolgáltatása. Amikor replikálja a rendszerleíró adatbázist a kívánt régiókba, minden régióban prémium beállításjegyzék-díjat kell fizetnie.

Az előző példában a Contoso két jegyzéket konszolidált egyre, replikákat adva az USA keleti, kanadai közép- és nyugat-európai replikákhoz. A Contoso havonta négyszer prémium díjat fizetne, további konfiguráció és felügyelet nélkül. Minden régió most húzza a képeket helyben, a teljesítmény javítása, a megbízhatóság nélkül hálózati kimenő díjak nyugat-amerikai Kanadába és az USA keleti régiója.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Leküldéses műveletek georeplikált regisztikátasokkal kapcsolatos hibák elhárítása
 
Előfordulhat, hogy egy Docker-ügyfél, amely leküldéses egy lemezképet egy georeplikált beállításjegyzékbe, előfordulhat, hogy nem minden lemezképréteget és annak jegyzékfájlját egyetlen replikált régióba. Ez azért fordulhat elő, mert az Azure Traffic Manager a rendszerleíró adatbázis-kérelmeket a hálózat legközelebbi replikált beállításjegyzékébe irányítja. Ha a rendszerleíró adatbázis két *közeli* replikációs területtel rendelkezik, a lemezképrétegek és a jegyzékfájl terjeszthető a két hely között, és a leküldéses művelet sikertelen lesz, amikor a jegyzék et érvényesíti. A probléma oka az, hogy egyes Linux-állomásokon a rendszerleíró adatbázis DNS-neve megoldódott. Ez a probléma nem fordul elő a Windows rendszeren, amely ügyféloldali DNS-gyorsítótárat biztosít.
 
Ha ez a probléma jelentkezik, az egyik megoldás az `dnsmasq` ügyféloldali DNS-gyorsítótár alkalmazása, például a Linux-állomáson. Ez segít biztosítani, hogy a rendszerleíró adatbázis neve következetesen fel legyen oldva. Ha az Azure-ban linuxos virtuális gépet használ a rendszerleíró adatbázisba való leküldéses hez, olvassa el a [Linux-alapú virtuális gépek DNS-névfeloldási beállításai az Azure-ban](../virtual-machines/linux/azure-dns.md)című témakörben található beállításokat.

A DNS-feloldás optimalizálásához a legközelebbi replika leküldésekor lemezképek, konfigurálja a georeplikált beállításjegyzék ugyanabban az Azure-régiókban, mint a leküldéses műveletek forrása, vagy a legközelebbi régióban, ha az Azure-on kívül dolgozik.

## <a name="next-steps"></a>További lépések

Tekintse meg a három részes [oktatóanyag-sorozat, georeplikáció az Azure Container Registry](container-registry-tutorial-prepare-registry.md). Végigvezeti a georeplikált beállításjegyzék létrehozásában, egy tároló létrehozásában, majd egyetlen `docker push` paranccsal üzembe helyezése több regionális webalkalmazások tárolók példányok.

> [!div class="nextstepaction"]
> [Georeplikáció az Azure Container Registry szolgáltatásban](container-registry-tutorial-prepare-registry.md)
