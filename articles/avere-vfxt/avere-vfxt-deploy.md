---
title: Avere vFXT üzembe az Azure-hoz
description: Az Azure-ban a Avere vFXT fürt telepítése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: eb0f5a4a4219c63334e0a5be3ea4378c3c317bec
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288101"
---
# <a name="deploy-the-vfxt-cluster"></a>A vFXT-fürt üzembe helyezése

A legegyszerűbben úgy vFXT fürt létrehozása az Azure-ban, hogy egy fürt tartományvezérlő használata. A fürt vezérlő egy virtuális Gépet, amely tartalmazza a szükséges szkriptek, a sablonok és a szoftverfrissítési infrastruktúrát hozhat létre és kezelhet a vFXT fürt.

Ezeket a lépéseket egy új vFXT-fürt üzembe helyezésekor tartalmazza:

1. [A fürt vezérlő létrehozása](#create-the-cluster-controller-vm).
1. Ha az Azure Blob storage, [hozzon létre egy storage-végpont](#create-a-storage-endpoint-if-using-azure-blob) a virtuális hálózaton.
1. [Csatlakozás a fürthöz vezérlő](#access-the-controller). A fürt vezérlő virtuális Gépet a többi ezeket a lépéseket kell elvégezni. 
1. [A hozzáférés-szerepkör létrehozásához](#create-the-cluster-node-access-role) a fürtcsomópontok. A prototípust biztosítunk.
1. [A fürt létrehozási parancsfájl testreszabása](#edit-the-deployment-script) típusának megfelelő vFXT fürtöt szeretne létrehozni.
1. [Hajtsa végre a fürt létrehozási parancsprogrammal](#run-the-script).

Fürt telepítés lépéseit, valamint a tervezési kapcsolatos további információkért olvassa el a [megtervezése a Avere vFXT rendszer](avere-vfxt-deploy-plan.md) és [üzembe helyezés – áttekintés](avere-vfxt-deploy-overview.md). 

Ez a dokumentum utasításait követve, miután egy virtuális hálózat, alhálózat, egy tartományvezérlő és egy vFXT fürtöt az alábbi ábrán látható módon fog rendelkezni:

![virtuális hálózat nem kötelező a blob storage és a egy három tartalmazó alhálózat tartalmazó bemutató ábra. Ez vFXT csomópontok/vFXT fürt és a egy virtuális gép címkével rendelkező fürt vezérlő címkével ellátott virtuális gépek vannak csoportosítva.](media/avere-vfxt-deployment.png)

Mielőtt elkezdené, győződjön meg arról, hogy foglalkoztak az Előfeltételek:  

1. [Új előfizetés](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Előfizetés tulajdonosi engedélyekkel](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [A vFXT fürt kvóta](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)

Igény szerint hozhat létre a fürtszerepkör csomópont [előtt](avere-vfxt-pre-role.md) megtehetjük ezt követően egyszerűbb létrehozása a fürt vezérlő, de.

## <a name="create-the-cluster-controller-vm"></a>A fürt vezérlő virtuális gép létrehozása

Az első lépés, ha a virtuális gép létrehozása és konfigurálása a vFXT fürtcsomópontok. 

A fürt vezérlő Linux rendszerű virtuális gép a Avere vFXT fürtszoftver létrehozása és a parancsfájlok előre telepítve. Nem kell jelentős feldolgozási teljesítmény vagy a tárolási terület, így választhat, hogy olcsó beállítások. Ez a virtuális gép időnként a vFXT fürt teljes élettartama során fogja használni.

A fürt vezérlő virtuális gép létrehozása két módszer áll rendelkezésre. Egy [Azure Resource Manager-sablon](#create-controller---arm-template) biztosított [alábbi](#create-controller---arm-template) egyszerűsítése érdekében a folyamat, de Ön is létrehozhat a vezérlőt a [Azure Piactéri lemezképhez](#create-controller---azure-marketplace-image). 

Létrehozhat egy új erőforráscsoportot, a tartományvezérlő létrehozásának részeként.

> [!TIP]
>
> Döntse el, egy nyilvános IP-címet használja a fürt vezérlőn kell-e. Nyilvános IP-címet biztosít egyszerűbb hozzáférést a vFXT fürthöz, de kisebb biztonsági kockázatot.
>
>  * A fürt vezérlőn nyilvános IP-cím lehetővé teszi a Avere vFXT fürtöt a saját alhálózatán kívülről kapcsolódni a jump-gazdagépként használja.
>  * Nincs beállítva egy nyilvános IP-címet a vezérlőn, ha egy másik jump host, egy VPN-kapcsolat vagy ExpressRoute kell használnia, aki a fürtöt. Hozzon létre például a vezérlő, amely rendelkezik egy VPN-kapcsolat konfigurálása virtuális hálózat része.
>  * Ha egy vezérlő hoz létre egy nyilvános IP-címmel, a tartományvezérlő virtuális gép egy hálózati biztonsági csoporttal kell védeni. Hozzáférés engedélyezése csak az internet-hozzáférést biztosítania a 22-es porton keresztül.

### <a name="create-controller---resource-manager-template"></a>Vezérlő – Resource Manager-sablon létrehozása

Vezérlő csomópont létrehozása a Portalról, kattintson az alábbi "Üzembe helyezése az Azure-bA" gombra. Ez az üzembe helyezése sablon létrehozza a virtuális gép által pedig létrehozza és felügyeli a Avere vFXT fürt.

[![gomb vezérlő létrehozása](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fazuredeploy.json)

Adja meg a következő információkat.

Az a **ALAPSZINTŰ** szakaszban:  

* **Előfizetés** a fürt
* **Erőforráscsoport** a fürt 
* **Hely** 

Az a **beállítások** szakaszban:

* E egy új virtuális hálózat létrehozása

  * Ha létrehoz egy új virtuális hálózat, a fürt vezérlő rendeli egy nyilvános IP-címet, hogy elérhetővé válik. A hálózati biztonsági csoport is létrejön a virtuális hálózat, amely korlátozza a bejövő forgalmat csak a 22-es portot.
  * Ha szeretne ExpressRoute vagy VPN használatával csatlakozhat a fürthöz vezérlő, ezt az értéket `false` , és adja meg egy meglévő vnetet a fennmaradó mezőket. A fürt vezérlő fogja használni a virtuális hálózatok közötti hálózati kommunikációhoz. 

* Virtuális hálózati erőforrás-csoport neve és alhálózati név – írja be a meglévő erőforrások nevét (Ha egy meglévő virtuális hálózat használatával), vagy írja be az új neveket, ha egy új virtuális hálózat létrehozása
* **Vezérlő neve** – a vezérlő virtuális gép nevének megadása
* Vezérlő rendszergazdai jogosultságú felhasználónevet – az alapértelmezett érték `azureuser`
* SSH-kulcsot – a nyilvános kulcs beillesztése társítása a rendszergazdai jogosultságú felhasználónevet. Olvasási [hogyan hozhat létre és használhat SSH-kulcsokat](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) Ha segítségre van szüksége.

A **feltételek és kikötések**: 

* Olvassa el a szolgáltatási feltételeket, és jelölje be a jelölőnégyzetet, hogy fogadja el őket. 

  > [!NOTE] 
  > Ha nem egy előfizetés tulajdonosa, rendelkezik egy olyan tulajdonost, fogadja el a feltételeket, a-ben, az alábbi előfeltételként felsorolt lépéseket [elfogadására szoftver előre](avere-vfxt-prereqs.md#accept-software-terms-in-advance). 

Kattintson a **beszerzési** befejezésekor. Az öt vagy hat perccel később a vezérlő csomópont lesz üzembe helyezéséig.

A kimenetek oldalán kell gyűjtenie az adatokat a fürt létrehozásához szükséges. Olvasási [a fürt létrehozásához szükséges adatok](#information-needed-to-create-the-cluster) további.

### <a name="create-controller---azure-marketplace-image"></a>Vezérlő – Azure Marketplace-rendszerkép létrehozása

Keresse meg a vezérlő sablon nevét az Azure piactéren való kereséssel ``Avere``. Válassza ki a **Avere vFXT Azure vezérlő** sablont.

Ha még nem tette meg, fogadja el a feltételeket, és a Piactéri lemezképhez programozott hozzáférés engedélyezése ehhez kattintson a "kíván üzembe helyezni a programozott módon?" alatti hivatkozásra a **létrehozás** gombra.

![Képernyőkép a programozás alapú hozzáférést, amely nem éri a Létrehozás gombra mutató hivatkozás](media/avere-vfxt-deploy-programmatically.png)

Kattintson a **engedélyezése** gombra, és a beállítás mentéséhez.

![Képernyőkép – bemutató kattintásra programozott hozzáférés engedélyezése](media/avere-vfxt-enable-program.png)

A fő lapján, térjen vissza a **Avere vFXT Azure vezérlő** sablonnal, majd kattintson **létrehozás**. 

Az első panelen adja meg, illetve erősítse meg az alapvető beállításokkal:

* **Előfizetés**
* **Erőforráscsoport** (adjon meg egy új nevet, ha szeretne létrehozni egy új csoportot.)
* **A virtuális gép neve** – a vezérlő neve
* **Régió**
* **Rendelkezésre állási beállítások** -redundancia, nem szükséges
* **Kép** -Avere vFXT vezérlő csomópont lemezképével
* **Méret** – hagyja meg az alapértelmezett, vagy válasszon másik költségkímélő típust
* **Rendszergazdai fiók** – a fürt vezérlő való bejelentkezés beállítása: 
  * Válassza a felhasználónév/jelszó vagy SSH nyilvános kulcs (ajánlott).
  
    > [!TIP] 
    > SSH-kulcs használata biztonságosabb. Olvasási [hogyan hozhat létre és használhat SSH-kulcsokat](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) Ha segítségre van szüksége. 
  * Adja meg a felhasználónevet 
  * Illessze be az SSH-kulcs, vagy adja meg, és erősítse meg a jelszót
* **Bejövőport-szabályok** – Ha egy nyilvános IP-cím, port megnyitása a 22-es (SSH) használatával

Kattintson a **tovább** lemez beállításainak megadása:

* **Írja be az operációs rendszer lemezén** -HDD alapértelmezett értéke elegendő
* **Nem felügyelt lemezek használata** – nem szükséges
* **Adatlemezek** – ne használja

Kattintson a **tovább** a hálózati beállításokat:

* **Virtuális hálózat** – válassza ki a vezérlőhöz tartozó virtuális hálózat, vagy adjon meg egy nevet, egy új virtuális hálózat létrehozásához. Ha nem szeretné használni egy nyilvános IP-címet a vezérlő, fontolja meg, keresse meg az expressroute-on vagy a már beállított egy másik hozzáférési módszer rendelkező virtuális hálózaton belül.
* **Alhálózat** -alhálózatát (nem kötelező) a virtuális hálózaton belül. Ha létrehoz egy új virtuális hálózat, létrehozhat egy új alhálózatot egy időben.
* **Nyilvános IP-cím** – Ha azt szeretné, egy nyilvános IP-címet használja, megadhatja azt itt. 
* **Hálózati biztonsági csoport** – ne módosítsa az alapértelmezett (**alapszintű**) 
* **Nyilvános bejövő portok** – Ha egy nyilvános IP-címet használja az Ez a vezérlő használatával engedélyezze a hozzáférést az SSH-forgalmat. 
* **A gyorsított hálózatkezelés** a virtuális gép nem érhető el.

Kattintson a **tovább** felügyeleti beállítások megadása:

* **Rendszerindítási diagnosztika** -váltson **kikapcsolása**
* **Az operációs rendszer Vendég diagnosztikai** -hagyja le van tiltva
* **Diagnosztikai tárfiók** – szükség esetén válasszon vagy adjon meg egy új fiókot diagnosztikai adatainak tárolására.
* **Felügyeltszolgáltatás-identitás** – ezt a lehetőséget, módosítsa **a**, ami létrehoz egy Azure AD egyszerű szolgáltatásnév a fürt vezérlő.
* **Automatikus leállítási** -hagyja 

Ezen a ponton kattinthat **felülvizsgálat + létrehozása** Ha nem szeretné, hogy példány címkék használata. Ellenkező esetben kattintson a **tovább** kétszer, amellyel kihagyhatja a **Vendég config** lapon és a címkék a lap. Ha nincs elkészült, kattintson **felülvizsgálat + létrehozása**. 

Után ellenőrzi a beállításokat, kattintson a **létrehozás** gombra.  

Létrehozás az öt vagy hat percet vesz igénybe.

## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>Hozzon létre egy storage-végponthoz (Ha használja az Azure Blob)

Ha a a háttér-adatokat tároló Azure Blob storage használ, létre kell hoznia egy tárolási végpontot a virtuális hálózaton. Ez [szolgáltatásvégpont](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) tartja azt az interneten keresztüli útválasztást helyett helyi Azure Blob-forgalmat.

1. A portálon kattintson **virtuális hálózatok** a bal oldalon.
1. Válassza ki a vezérlőhöz tartozó virtuális hálózat. 
1. Kattintson a **Szolgáltatásvégpontokat** a bal oldalon.
1. Kattintson a **Hozzáadás** tetején.
1. Hagyja meg a szolgáltatás ``Microsoft.Storage`` és a vezérlő alhálózat választása.
1. Kattintson a lap alján, **Hozzáadás**.

  ![Jegyzetekkel lépéseit a szolgáltatásvégpont létrehozása az Azure portal képernyőképe](media/avere-vfxt-service-endpoint.png)

## <a name="information-needed-to-create-the-cluster"></a>A fürt létrehozásához szükséges információk

Miután létrehozta a fürt vezérlő, ellenőrizze, hogy a következő lépésekhez szükséges információkat. 

A vezérlő való kapcsolódáshoz szükséges adatokat: 

* Vezérlő felhasználónév és SSH-kulcs (vagy jelszó)
* Vezérlő IP-címét vagy más módszerrel, a vezérlő virtuális Géphez való kapcsolódáshoz

A fürt számára szükséges információkat: 

* Erőforráscsoport neve
* Azure-beli hely 
* Virtuális hálózat neve
* Alhálózat neve
* Fürtcsomópont szerepkör neve – Ez a név van beállítva, a szerepkör leírt létrehozásakor [alatt](#create-the-cluster-node-access-role)
* Storage-fiók neve, ha egy Blob-tároló létrehozása

A vezérlő csomópont a Resource Manager-sablon használatával hozta létre, ha az információkat kaphat a [sablonjának kimenete](#find-template-output). 

Ha a vezérlő létrehozásához használt az Azure Marketplace-beli rendszerképét, közvetlenül megadott ezeket az elemeket a legtöbb. 

Keresse meg a hiányzó elemek a tartományvezérlő virtuális gép információkat tartalmazó oldal megnyitásával. Kattintson például **összes erőforrás** , és keresse meg a vezérlő nevét, majd kattintson a vezérlő nevét a részletek megtekintéséhez.

### <a name="find-template-output"></a>Keresse meg a sablon kimeneti

Ezt az információt a Resource Manager sablon kimeneti megkereséséhez kövesse ezt az eljárást:

1. Nyissa meg az erőforráscsoport, a fürt vezérlő.

1. A bal oldali menüjében kattintson **központi telepítések**, majd **Microsoft.Template**.

   ![Erőforráscsoport portál oldalán a központi telepítések kiválasztva a bal oldalon Microsoft.Template bemutató. Ez egy táblázat a központi telepítés neve](media/avere-vfxt-deployment-template.png)

1. A bal oldali menüjében kattintson **kimenetek**. Az értékek másolásához a mezőkben. 

   ![SSHSTRING, RESOURCE_GROUP, hely, hálózat, ALHÁLÓZAT és SUBNET_ID mezők címkéinek jobbra látható a lap kimenete](media/avere-vfxt-template-outputs.png)

## <a name="access-the-controller"></a>A tartományvezérlő hozzáférés

Ehhez a központi telepítés a többi, szeretne csatlakozni a fürthöz vezérlő.

1. A metódus a fürt vezérlő csatlakozni konfigurációtól függ.

   * Kell-e a vezérlő egy nyilvános IP-cím SSH a rendszergazdai jogosultságú felhasználónevet, a tartományvezérlő IP-Címének beállítása (például ``ssh azureuser@40.117.136.91``).
   * Ha a tartományvezérlő nincs nyilvános IP-cím, használja a VPN vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) a virtuális hálózatok közötti kapcsolat.

1. A bejelentkezés után a tartományvezérlőre, amely hitelesíteni futtatásával `az login`. A hitelesítési kód megadására a rendszerhéj megadott másolja, majd egy webes böngésző használatával tölthet [ https://microsoft.com/devicelogin ](https://microsoft.com/devicelogin) és a Microsoft-rendszer a hitelesítéshez. Térjen vissza a rendszerhéj megerősítést.

   ![A megjelenítés, a böngésző hivatkozásra, és a hitelesítési kódot "AZ login" parancs parancssori kimenet](media/avere-vfxt-azlogin.png)

1. Adja hozzá az előfizetés az előfizetési azonosító használatával Ez a parancs futtatásával:  ```az account set --subscription YOUR_SUBSCRIPTION_ID```

## <a name="create-the-cluster-node-access-role"></a>A fürt csomópont hozzáférés szerepkör létrehozása

> [!NOTE] 
> Ha Ön nem egy előfizetés tulajdonosa, és a szerepkör még nem hozott létre, kövesse az alábbi lépéseket, vagy az eljárással az előfizetés tulajdonosa van [Avere vFXT fürt modul hozzáférés szerepkör nélkül a tartományvezérlő létrehozása](avere-vfxt-pre-role.md).

[Szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) révén a vFXT fürtcsomópontok engedély szükséges feladatok végrehajtásához.  

Normál vFXT fürt művelet részeként egyéni vFXT elvégzésére van szükség, olvassa el az Azure erőforrás-tulajdonságok, tárhely kezelése és más csomópontok hálózatiadapter-beállítások felügyelete. 

1. A tartományvezérlőn nyissa meg a ``/avere-cluster.json`` fájlt valamelyik szerkesztőben.

   ![a list parancs, és a "vi /avere-cluster.json" megjelenítő konzol](media/avere-vfxt-open-role.png)

1. Szerkessze a fájlt meg előfizetési azonosítóját, és törölje a sor fölé. Mentse a fájlt az ``avere-cluster.json``.

   ![Szövegszerkesztőben az előfizetés-azonosító és a "távolítsa el ezt a sort" törlésre kiválasztott megjelenítő konzol](media/avere-vfxt-edit-role.png)

1. Ez a parancs használatával hozza létre a szerepkört:  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

A szerepkör nevét, továbbítja a fürt létrehozási parancsprogrammal a következő lépésben. 

## <a name="create-nodes-and-configure-the-cluster"></a>Csomópont létrehozása és a fürt konfigurálása

A Avere vFXT fürt létrehozásához, szerkessze a minta parancsprogramok a vezérlő tartalmazza, és ott futtathatja. Mintaszkriptek gyökérkönyvtárában található (`/`) a fürt vezérlőn.

* Ha azt szeretné, hogy hozzon létre egy blobtárolót a Avere vFXT háttér-tárolási rendszert használja, használja a ``create-cloudbacked-cluster`` parancsfájlt.

* Később hozzáadhat tárolási, használja a ``create-minimal-cluster`` parancsfájlt.

> [!TIP]
> Csomópontok hozzáadása és megsemmisítése a vFXT fürt prototípus-parancsfájlok is szerepelnek a `/` könyvtárat a fürt vezérlő virtuális Gépet.

### <a name="edit-the-deployment-script"></a>Az üzembe helyezési parancsfájl szerkesztése

Egy szövegszerkesztővel nyissa meg a minta parancsfájl. A testre szabott parancsfájl mentése más néven az eredeti minta felülírásának elkerülése érdekében érdemes.

Adja meg az értékeket a parancsfájl-változókat.

* Erőforráscsoport neve

  * Ha hálózati vagy tárolási összetevők, amelyek eltérő erőforráscsoportokban vannak, állítsa vissza a változót, és adja meg ezeket a neveket is. 

```python
# Resource groups
# At a minimum specify the resource group.  If the network resources live in a
# different group, specify the network resource group.  Likewise for the storage
# account resource group.
RESOURCE_GROUP=
#NETWORK_RESOURCE_GROUP=
#STORAGE_RESOURCE_GROUP=
```

* Hely neve
* Virtuális hálózat neve
* Alhálózat neve
* Az Azure AD modul szerepkör neve – Ha követte a példában a [hozza létre a fürt csomópont hozzáférés szerepkört](#create-the-cluster-node-access-role), használjon ``avere-cluster``. 
* Tárfiók neve (Ha egy új Blob-tároló létrehozása)
* Fürt neve – a két vFXT fürt ugyanazzal a névvel nem lehet ugyanabban az erőforráscsoportban. 
* Rendszergazdai jelszó – válasszon egy biztonságos jelszót a monitorozási és a fürt felügyeletéhez. Ezt a jelszót a felhasználóhoz rendelt ``admin``. 
* Csomóponttípus-példány – lásd: [vFXT csomópontméretűek](avere-vfxt-deploy-plan.md#vfxt-node-sizes) információ
* Csomópont-gyorsítótár mérete – lásd: [vFXT csomópontméretűek](avere-vfxt-deploy-plan.md#vfxt-node-sizes) információ

Mentse a fájlt, és a kilépési.

### <a name="run-the-script"></a>A szkript futtatása

A parancsfájl futtatásához írja be a filename hozott létre. (Példa: `./create-cloudbacked-cluster-west1`)  

> [!TIP]
> Fontolja meg ezt a parancsot belül futtatja egy [multiplexer terminálon](http://linuxcommand.org/lc3_adv_termmux.php) például `screen` vagy `tmux` abban az esetben a kapcsolat megszakadását.  

A kimenet is a rendszer naplózza `~/vfxt.log`.

Miután a parancsfájl futása befejeződött, a felügyeleti IP-cím másolásához, szükség van az fürt felügyeleti.

![A szkript végén a felügyeleti IP-cím megjelenítése a parancssori kimenet](media/avere-vfxt-mgmt-ip.png)

## <a name="next-step"></a>Következő lépés

Most, hogy a fürt fut, és tudja, a felügyeleti IP-címére, [kapcsolódni a fürt konfigurálása eszköz](avere-vfxt-cluster-gui.md) támogatásának engedélyezése, és adja hozzá a tárolót, ha szükséges.
