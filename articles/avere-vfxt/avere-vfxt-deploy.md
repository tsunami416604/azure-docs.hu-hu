---
title: Avere vFXT üzembe az Azure-hoz
description: Az Azure-ban a Avere vFXT fürt telepítése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: f6d847e9042341f47a06fde0f9aa4a70f2549a07
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512152"
---
# <a name="deploy-the-vfxt-cluster"></a>A vFXT-fürt üzembe helyezése

Ez az eljárás végigvezeti az Azure piactéren elérhető a telepítési varázsló használatával. A varázsló automatikusan üzembe helyezi a fürtöt az Azure Resource Manager-sablon használatával. Után adja meg a paramétereket a képernyőn, és kattintson a **létrehozás**, az Azure automatikusan elvégzi a következő lépéseket: 

* Hozza létre a fürt vezérlőt, amely egy egyszerű virtuális gép, amely tartalmazza a szoftver telepítéséhez és a fürt kezeléséhez szükséges.
* Állítsa be az erőforráscsoport és a virtuális hálózati infrastruktúrát, beleértve az új elemek létrehozása, ha szükséges.
* Csomópont virtuális gépek a fürt létrehozásához, és konfigurálja őket az Avere fürtként.
* Ha a rendszer kéri, hozzon létre egy új Azure Blob-tárolót, és azt konfigurálja a fürt alapvető filer.

Ez a dokumentum utasításait követve, miután egy virtuális hálózat, alhálózat, egy tartományvezérlő és egy vFXT fürtöt az alábbi ábrán látható módon fog rendelkezni:

![virtuális hálózat nem kötelező a blob storage és a egy három tartalmazó alhálózat tartalmazó bemutató ábra. Ez vFXT csomópontok/vFXT fürt és a egy virtuális gép címkével rendelkező fürt vezérlő címkével ellátott virtuális gépek vannak csoportosítva.](media/avere-vfxt-deployment.png)

A fürt létrehozását követően kell [hozzon létre egy storage-végpont](#create-a-storage-endpoint-if-using-azure-blob) a virtuális hálózaton, ha a Blob storage használatával. 

Mielőtt az létrehozása sablon használatával ellenőrizze, rendelkezik az Előfeltételek címezni:  

1. [Új előfizetés](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Előfizetés tulajdonosi engedélyekkel](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [A vFXT fürt kvóta](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Egyéni hozzáférési szerepkörök](avere-vfxt-prereqs.md#create-access-roles) -létre kell hoznia egy szerepköralapú hozzáférés-vezérlési szerepkör hozzárendelése a fürtcsomópontok. Lehetősége van is létre kell hoznia egy egyéni hozzáférés szerepkört a fürt vezérlő, de a felhasználók többsége az alapértelmezett tulajdonosi szerepkört, amely megfelelő vezérlő jogosultságot ad az egy erőforráscsoport-tulajdonosnak vesz igénybe. Olvasási [beépített szerepkörök az Azure-erőforrások](../role-based-access-control/built-in-roles.md#owner) további részleteket talál.

Fürt telepítés lépéseit, valamint a tervezési kapcsolatos további információkért olvassa el a [megtervezése a Avere vFXT rendszer](avere-vfxt-deploy-plan.md) és [üzembe helyezés – áttekintés](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Az Azure a Avere vFXT létrehozása

A létrehozás sablon az Azure Portal eléréséhez Avere keresése és kiválasztása "Avere vFXT ARM üzembe helyezési". 

![Az Azure portal a kiemelt kenyér megjelenítő böngészőablakban morzsalékok "Új > Marketplace > minden". A lapon a minden, a keresőmezőbe a "avere" és a második eredmény rendelkezik "Avere vFXT ARM üzembe helyezési" szemlélteti az jelöljön ki red.](media/avere-vfxt-template-choose.png)

Kattintson a részletek a Avere vFXT ARM üzembe helyezési oldalon elolvasásával **létrehozás** megkezdéséhez. 

![A központi telepítési sablon bemutató első oldalán az Azure Marketplace-en](media/avere-vfxt-deploy-first.png)

A sablon oszlik négy lépést – két információgyűjtés oldalát, és a érvényesítése és a megerősítés lépéseket. 

* Az oldalon a fürt vezérlő virtuális gép beállításainak összpontosít. 
* Két lap összegyűjti a paramétereket a fürt és a kapcsolódó erőforrásokhoz, mint az alhálózatok és a tároló létrehozásához. 
* Oldal három összefoglalja azokat a beállításokat, és érvényesíti a konfigurációt. 
* Lap négy szoftverfrissítési feltételek és kikötések ismerteti, és lehetővé teszi, hogy a Fürtlétrehozási folyamat elindításához. 

## <a name="page-one-parameters---cluster-controller-information"></a>Egy paraméter - fürt adatokat lapon

Az első oldalán a központi telepítési sablont a fürt vezérlő adatokat gyűjt. 

![Első oldalán a központi telepítési sablon](media/avere-vfxt-deploy-1.png)

Töltse ki a következő információkat:

* **Fürt vezérlőnév** -állítsa a fürt vezérlő virtuális gép nevét.

* **Vezérlő felhasználónév** – töltse ki a legfelső szintű felhasználónevet, a fürt vezérlő virtuális Gépet. 

* **Hitelesítési típus** – válassza a jelszó vagy SSH nyilvános kulcsos hitelesítés, a vezérlő való kapcsolódáshoz. Az SSH nyilvános kulcs módszer javasolt; olvassa el [hogyan hozhat létre és használhat SSH-kulcsokat](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) Ha segítségre van szüksége.

* **Jelszó** vagy **nyilvános SSH-kulcs** – attól függően, a kiválasztott hitelesítési típus, meg kell adnia egy nyilvános RSA-kulcs vagy egy jelszót, a következő mezőket. Ezt a hitelesítő adatot használnak a korábban megadott felhasználónév.

* **Avere fürt létrehozása a szerepkör-azonosító** -használja ezt a mezőt a hozzáférés-vezérlő szerepkör a fürt vezérlő megadásához. Az alapértelmezett érték: a beépített szerepkör [tulajdonosa](../role-based-access-control/built-in-roles.md#owner). A fürt erőforráscsoport tulajdonosi jogosultságokkal a fürt vezérlő korlátozódnak. 

  A globálisan egyedi azonosító, a szerepkör kell használnia. Az alapértelmezett érték (tulajdonos) a GUID 8e3af657-a8ff – 443-as c-a75c-2fe8c4bcb635. A GUID egy egyéni szerepkör, használja ezt a parancsot: 

  ```azurecli
  az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
  ```

* **Előfizetés** – válassza ki az előfizetést a Avere vFXT. 

* **Erőforráscsoport** – válassza ki az erőforráscsoportot a Avere vFXT fürthöz, vagy kattintson az "Új létrehozása", és adjon meg egy új erőforráscsoport-nevet. 

* **Hely** – válassza ki a fürt és -erőforrások Azure helyét.

Kattintson a **OK** befejezésekor. 

> [!NOTE]
> Ha azt szeretné, hogy a fürt tartományvezérlőt, a nyilvános IP-címet beállítani, hozzon létre egy új virtuális hálózatot a fürthöz a meglévő hálózat kiválasztása helyett. Ez a beállítás van két oldalon.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Oldal két paraméter – vFXT fürt adatai

A második oldalán a központi telepítési sablont a fürt mérete, a csomópont típusa, a gyorsítótár mérete és a storage paraméterek, más beállítások megadását teszi lehetővé. 

![A központi telepítési sablont második lapján](media/avere-vfxt-deploy-2.png)

* **Avere vFXT fürtcsomópontok számának** – válassza ki a fürtben található csomópontok számát. Legalább három csomóponttal, a maximális tizenkét. 

* **Fürt felügyeleti jelszó** -fürt felügyeleti a jelszavát. Ez a jelszó felhasználónév együtt használható ```admin``` való bejelentkezéshez a fürt Vezérlőpult-beállítások konfigurálása és a fürt monitorozására.

* **Műveletek fürtszerepkör Avere** – adja meg a fürt csomópontjai a hozzáférés-vezérlő szerepkör nevét. Ez az előfeltétel-ellenőrzési lépésben létrehozott egyéni szerepkör. 

  A példában leírt [hozza létre a fürt csomópont hozzáférés szerepkört](avere-vfxt-prereqs.md#create-the-cluster-node-access-role) menti a fájlt az ```avere-operator.json``` és a megfelelő szerepkör neve ```avere-operator```.

* **Avere vFXT fürtnév** – adjon meg egy egyedi nevet a fürt. 

* **Méret** – adja meg a fürt csomópontjainak létrehozásakor a virtuális gép típusát. 

* **Gyorsítótár mérete csomópontonként** – a fürt gyorsítótár megoszlik a fürtcsomópontok között, így a gyorsítótár teljes mérete a Avere vFXT fürtön a gyorsítótár méretét a csomópontok számát megszorozva csomópontonként lesz. 

  Az ajánlott konfiguráció, hogy 1 TB-os csomópontonként, ha használja Standard_D16s_v3 fürtcsomópontokat, valamint 4 TB-os használhat csomópontonként, ha Standard_E32s_v3 csomópontok használatával.

* **Virtuális hálózat** – válassza ki a helyet adhat a fürtöt egy meglévő vnetet, vagy hozzon létre egy új virtuális hálózat határozza meg. 

  > [!NOTE]
  > Ha létrehoz egy új virtuális hálózat, a fürt vezérlő lesz nyilvános IP-cím, hogy az új magánhálózati elérheti. Ha úgy dönt, hogy egy meglévő vnetet, a fürt vezérlő nélkül nyilvános IP-címmel van konfigurálva. 
  > 
  > A fürt vezérlőn egy nyilvánosan látható IP-címet biztosít egyszerűbb hozzáférést a vFXT fürthöz, de kisebb biztonsági kockázatot. 
  >  * A fürt vezérlőn nyilvános IP-cím lehetővé teszi a Avere vFXT fürtöt a saját alhálózatán kívülről kapcsolódni a jump-gazdagépként használja.
  >  * Nincs beállítva egy nyilvános IP-címet a vezérlőn, ha egy másik jump host, egy VPN-kapcsolat vagy ExpressRoute kell használnia, aki a fürtöt. Hozzon létre például a vezérlő, amely már rendelkezik egy VPN-kapcsolat konfigurálása virtuális hálózat része.
  >  * Ha egy vezérlő hoz létre egy nyilvános IP-címmel, a tartományvezérlő virtuális gép egy hálózati biztonsági csoporttal kell védeni. Alapértelmezés szerint a Avere vFXT az Azure-beli egy hálózati biztonsági csoportot hoz létre, és korlátozza a bejövő hozzáférést csak a 22-es nyilvános IP-címekkel rendelkező vezérlők portot. További védelmet biztosíthat a rendszer zárolja a forrás az IP-címek – való hozzáférés, kapcsolatok engedélyezése csak a fürt hozzáféréshez használni kívánt gépeket.

* **Alhálózat** – válasszon egy alhálózatot a meglévő virtuális hálózatból, vagy hozzon létre egy újat. 

* **Blob storage használata** -válassza **igaz** hozzon létre egy új Azure Blob-tárolót, és konfigurálja az új Avere vFXT fürthöz háttér-tárolóként. Ez a beállítás is ugyanazt az erőforráscsoportot, a fürtön belül egy új tárfiókot hoz létre. 

  Adja meg a mezőben **hamis** Ha nem szeretné, hogy hozzon létre egy új tárolót. Ebben az esetben kell csatolni, és a fürt létrehozását követően a tároló konfigurálása. Olvasási [konfigurálta a tárterületet](avere-vfxt-add-storage.md) útmutatást. 

* **Storage-fiók** – Ha egy új Azure Blob-tároló létrehozása, adjon meg egy nevet az új tárfiókot. 

## <a name="validation-and-purchase"></a>Érvényesítési és megvásárlása

Három lap lehetővé teszi a konfiguráció összegzése, és érvényesíti a paramétereket. Miután az érvényesítés sikeres, kattintson a **OK** gombra a folytatáshoz. 

![A központi telepítési sablon - ellenőrzési harmadik oldalán](media/avere-vfxt-deploy-3.png)

Négy lapon kattintson a **létrehozás** gombra kattintva fogadja el a feltételeket, és hozzon létre a Avere vFXT az Azure-fürtben. 

![A központi telepítési sablon – használati feltételeket, a negyedik oldal létrehozása gomb](media/avere-vfxt-deploy-4.png)

Fürt üzembe helyezése a 15-20 percet vesz igénybe.

## <a name="gather-template-output"></a>Gyűjtsön kimeneteket a sablon

Befejeztével a Avere vFXT sablon létrehozása a fürt, az új fürt néhány fontos információt jelenít meg. 

> [!TIP]
> Ellenőrizze, hogy a felügyeleti IP-cím másolása a sablon kimeneti. Ez a cím a fürt felügyeletéhez szükséges.

Ezek az információk megkereséséhez kövesse ezt az eljárást:

1. Nyissa meg az erőforráscsoport, a fürt vezérlő.

1. A bal oldali menüjében kattintson **központi telepítések**, majd **microsoft-avere.vfxt-sablon**.

   ![Erőforráscsoport portáloldalán a bal és a egy táblát a központi telepítés nevét megjelenítő microsoft-avere.vfxt-sablon a kiválasztott központi telepítések](media/avere-vfxt-outputs-deployments.png)

1. A bal oldali menüjében kattintson **kimenetek**. Az értékek másolásához a mezőkben. 

   ![lap ábrázoló SSHSTRING, RESOURCE_GROUP, hely, NETWORK_RESOURCE_GROUP, hálózat, ALHÁLÓZAT, SUBNET_ID, VSERVER_IPs és MGMT_IP jobb oldalán a címkék mezőkben szereplő értékek kimenete](media/avere-vfxt-outputs-values.png)


## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>Hozzon létre egy storage-végponthoz (Ha használja az Azure Blob)

Ha a a háttér-adatokat tároló Azure Blob storage használ, létre kell hoznia egy tárolási végpontot a virtuális hálózaton. Ez [szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) helyett átirányítására a virtuális hálózaton kívül helyi tartja az Azure Blob-forgalmat.

1. A portálon kattintson **virtuális hálózatok** a bal oldalon.
1. Válassza ki a vezérlőhöz tartozó virtuális hálózat. 
1. Kattintson a **Szolgáltatásvégpontokat** a bal oldalon.
1. Kattintson a **Hozzáadás** tetején.
1. Hagyja meg a szolgáltatás ``Microsoft.Storage`` és a vezérlő alhálózat választása.
1. Kattintson a lap alján, **Hozzáadás**.

  ![Jegyzetekkel lépéseit a szolgáltatásvégpont létrehozása az Azure portal képernyőképe](media/avere-vfxt-service-endpoint.png)

## <a name="next-step"></a>Következő lépés

Most, hogy a fürt fut, és tudja, a felügyeleti IP-címére, [kapcsolódni a fürt konfigurálása eszköz](avere-vfxt-cluster-gui.md) támogatás engedélyezéséhez adja hozzá az tárolási, ha szükséges, és más fürtbeállítások testreszabása.
