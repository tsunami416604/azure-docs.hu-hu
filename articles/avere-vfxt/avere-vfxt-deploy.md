---
title: Avere-vFXT üzembe helyezése az Azure-ban
description: A avere vFXT-fürt üzembe helyezésének lépései az Azure-ban
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: rohogue
ms.openlocfilehash: 6ddf950bf2d138a94675ee394109a0d227ea206b
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255450"
---
# <a name="deploy-the-vfxt-cluster"></a>A vFXT-fürt üzembe helyezése

Ez az eljárás végigvezeti az Azure piactéren elérhető központi telepítési varázsló használatával. A varázsló Azure Resource Manager sablon használatával automatikusan telepíti a fürtöt. Miután megtörtént a paraméterek megadása az űrlapon, majd a **Létrehozás**gombra kattint, az Azure automatikusan végrehajtja a következő lépéseket:

* Létrehozza a fürtöt, amely egy alapszintű virtuális gép, amely tartalmazza a fürt üzembe helyezéséhez és kezeléséhez szükséges szoftvert.
* Beállítja az erőforráscsoportot és a virtuális hálózati infrastruktúrát, beleértve az új elemek létrehozását is.
* Létrehozza a fürtcsomópont virtuális gépeket, és konfigurálja őket avere-fürtként.
* Ha a rendszer kéri, egy új Azure BLOB-tárolót hoz létre, és a fürt Core Filer-ként konfigurálja azt.

A jelen dokumentumban szereplő utasítások követése után egy virtuális hálózattal, egy alhálózattal, egy vezérlővel és egy vFXT-fürttel fog rendelkezni, ahogy az a következő ábrán is látható. Ez az ábra a választható Azure Blob Core Filer-t mutatja be, amely tartalmaz egy új blob Storage-tárolót (új Storage-fiókban, nem jelenik meg) és egy szolgáltatási végpontot a Microsoft Storage-ban az alhálózaton belül. 

![a avere-fürt összetevőivel három koncentrikus téglalapot ábrázoló diagram. A külső négyszög "erőforráscsoport" címkével rendelkezik, és a "blob Storage (nem kötelező)" címkével ellátott hatszög szerepel. A következő téglalap a "Virtual Network: 10.0.0.0/16" címkével van ellátva, és nem tartalmaz egyedi összetevőket. A legbelső téglalap az "alhálózat: 10.0.0.0/24" címkével rendelkezik, és tartalmaz egy "fürtcsomópont" címkével ellátott virtuális gépet, egy "vFXT-csomópontok (vFXT-fürt)" címkével ellátott három virtuális gép, valamint egy "szolgáltatási végpont" nevű hatszöget. Van egy nyíl, amely összeköti a szolgáltatási végpontot (amely az alhálózaton belül van) és a blob Storage-t (amely kívül esik az alhálózaton és a vnet, az erőforráscsoporthoz). A nyíl az alhálózat és a virtuális hálózat határain halad át.](media/avere-vfxt-deployment.png)  

A létrehozási sablon használata előtt győződjön meg arról, hogy az alábbi előfeltételeket tárgyalta:  

1. [Új előfizetés](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Előfizetés tulajdonosának engedélyei](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Kvóta a vFXT-fürthöz](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Storage Service-végpont (ha szükséges)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) – szükséges a meglévő virtuális hálózattal való üzembe helyezéshez és a blob Storage létrehozásához.

További információ a fürtök üzembe helyezésének lépéseiről és tervezéséről: [a avere-vFXT rendszerének](avere-vfxt-deploy-plan.md) és [telepítésének áttekintése](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Az Azure-hoz készült avere-vFXT létrehozása

Nyissa meg a Azure Portal létrehozási sablonját a avere keresésével, és válassza a "avere vFXT for Azure ARM sablon" lehetőséget. 

![A Azure Portalt tartalmazó böngészőablakban láthatók a "New > Marketplace > all" című rész. A minden oldalon a keresőmezőbe a "avere" kifejezés szerepel, a második pedig a "avere vFXT for Azure ARM Template" kifejezést piros színnel kiemelve.](media/avere-vfxt-template-choose.png)

Miután elolvasta a részleteket a avere vFXT for Azure ARM-sablon lapon, kattintson a **Létrehozás** elemre a kezdéshez. 

![Az Azure Marketplace az üzembe helyezési sablon első oldalával, amely a következőt jeleníti meg](media/avere-vfxt-deploy-first.png)

A sablon négy lépésből áll – két információ gyűjtése lapokra, valamint az érvényesítési és a megerősítési lépésekre. 

* Az oldalon az egyik a fürthöz tartozó virtuális gép beállításaira koncentrál. 
* A két oldal a fürt és a kapcsolódó erőforrások, például alhálózatok és tárolók létrehozására vonatkozó paramétereket gyűjt. 
* A három oldal összegzi a beállításokat, és érvényesíti a konfigurációt. 
* A négy oldal a szoftverek használati feltételeit és kikötéseit ismerteti, és lehetővé teszi a fürt létrehozási folyamatának elindítását. 

## <a name="page-one-parameters---cluster-controller-information"></a>Az oldal egy paramétere – a fürt vezérlője információi

A központi telepítési sablon első lapja a fürtre vonatkozó adatokat gyűjti. 

![A központi telepítési sablon első lapja](media/avere-vfxt-deploy-1.png)

Adja meg a következő információkat:

* **Tartományvezérlő neve** – adja meg a fürt TARTOMÁNYVEZÉRLŐJE virtuális gép nevét.

* **Vezérlő felhasználóneve** – töltse ki a fürt tartományvezérlőjének virtuális géphez tartozó legfelső szintű felhasználónevet. 

* **Hitelesítés típusa** – válassza a jelszó vagy az SSH nyilvános kulcsú hitelesítés lehetőséget a vezérlőhöz való csatlakozáshoz. A nyilvános SSH-kulcs módszere ajánlott; Ha segítségre van szüksége, olvassa el, [hogyan hozhat létre és HASZNÁLHAT SSH-kulcsokat](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) .

* **Jelszó** vagy **nyilvános SSH-kulcs** – a kiválasztott hitelesítési típustól függően meg kell adnia egy RSA nyilvános kulcsot vagy jelszót a következő mezőkben. Ezt a hitelesítő adatot a korábban megadott felhasználónévvel kell használni.

* **Előfizetés** – válassza ki az előfizetést a avere vFXT. 

* **Erőforráscsoport** – válasszon ki egy meglévő üres erőforráscsoportot a avere vFXT-fürthöz, vagy kattintson az "új létrehozása" elemre, és adjon meg egy új erőforráscsoport-nevet. 

* **Hely** – válassza ki a fürt és az erőforrások Azure-beli helyét.

Ha elkészült, kattintson **az OK gombra** . 

> [!NOTE]
> Ha azt szeretné, hogy a tartományvezérlő nyilvános IP-címmel rendelkezzen, hozzon létre egy új virtuális hálózatot a fürthöz egy meglévő hálózat kiválasztása helyett. Ez a beállítás a két oldalon található.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Oldal két paraméter – vFXT-fürt adatai

A telepítési sablon második lapja lehetővé teszi a fürt méretének, a csomópont típusának, a gyorsítótár méretének és a tárolási paraméterek beállítását más beállítások között. 

![A központi telepítési sablon második lapja](media/avere-vfxt-deploy-2.png)

* **Avere vFXT-csomópontok** száma – válassza ki a fürtben használni kívánt csomópontok számát. A minimum három csomópont, a maximum pedig tizenkét. 

* **Fürtfelügyelő jelszava** – hozza létre a fürt felügyeletének jelszavát. Ez a jelszó a ```admin``` felhasználónévvel fog bejelentkezni a fürt Vezérlőpultján a fürt figyeléséhez és a beállítások konfigurálásához.

* **Avere vFXT-fürt neve** – adjon egyedi nevet a fürtnek. 

* **Méret** – ez a szakasz a fürtcsomópontok esetében használt virtuálisgép-típust jeleníti meg. Bár csak egy ajánlott lehetőség van, a **méret módosítása** hivatkozás egy olyan táblázatot nyit meg, amely tartalmazza a példány típusát és a díjszabási számológépre mutató hivatkozást.  

* **Gyorsítótár mérete/csomópont** – a fürt gyorsítótára a fürtcsomópontok között oszlik el, így a avere vFXT-fürt teljes mérete a csomópontok gyorsítótárának mérete, a csomópontok számával megszorozva. 

  Az ajánlott konfiguráció a Standard_E32s_v3-csomópontok 4 TB-os csomópontjának használata.

* **Virtuális hálózat** – adjon meg egy új vnet a fürt kiválasztásához, vagy válasszon ki egy meglévő vnet, amely megfelel a [avere vFXT-rendszer megtervezése](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure)című témakörben ismertetett előfeltételeknek. 

  > [!NOTE]
  > Ha új vnet hoz létre, a tartományvezérlő nyilvános IP-címmel fog rendelkezni, hogy hozzáférhessen az új magánhálózathoz. Ha meglévő vnet választ, a tartományvezérlő nyilvános IP-cím nélkül van konfigurálva. 
  > 
  > A fürt egy nyilvánosan látható IP-címe könnyebb hozzáférést biztosít a vFXT-fürthöz, de kis biztonsági kockázatot hoz létre. 
  >  * A fürt vezérlőjének nyilvános IP-címe lehetővé teszi, hogy Jump hostként használja a avere vFXT-fürthöz a privát alhálózaton kívülről való kapcsolódáshoz.
  >  * Ha nem állít be nyilvános IP-címet a vezérlőn, egy másik Jump hostt, VPN-kapcsolatot vagy ExpressRoute kell használnia a fürt eléréséhez. Hozzon létre például egy olyan virtuális hálózaton belüli vezérlőt, amely már rendelkezik konfigurált VPN-kapcsolattal.
  >  * Ha nyilvános IP-címmel rendelkező vezérlőt hoz létre, akkor a vezérlő virtuális gépet egy hálózati biztonsági csoporttal kell védelemmel ellátnia. Alapértelmezés szerint a avere vFXT for Azure üzembe helyezése egy hálózati biztonsági csoportot hoz létre, és a bejövő hozzáférést csak a 22-es portra korlátozza a nyilvános IP-címekkel rendelkező vezérlők esetében. A rendszer további védettségét úgy biztosíthatja, hogy leállítja az IP-címek tartományához való hozzáférést, azaz csak a fürt eléréséhez használni kívánt gépek kapcsolatait engedélyezi.

  Az üzembe helyezési sablon az új vnet az Azure Blob Storage-hoz tartozó Storage Service-végponttal, a hálózati hozzáférés-vezérlés pedig a fürt alhálózatán lévő IP-címekhez zárolva konfigurálja. 

* **Alhálózat** – válasszon egy alhálózatot a meglévő virtuális hálózatból, vagy hozzon létre újat. 

* **Blob Storage létrehozása és használata** – válassza az **igaz** lehetőséget egy új Azure Blob-tároló létrehozásához, és konfigurálja háttérbeli tárolóként az új avere vFXT-fürthöz. Ezzel a beállítással egy új Storage-fiókot is létrehoz a fürt és a fürt alhálózatán belül egy Microsoft Storage szolgáltatásbeli végponton belül. 
  
  Ha egy meglévő virtuális hálózatot ad meg, akkor a fürt létrehozása előtt rendelkeznie kell egy Storage Service-végponttal. (További információért olvassa el [a avere vFXT-rendszer megtervezése](avere-vfxt-deploy-plan.md)című témakört.)

  Ha nem szeretne új tárolót létrehozni, állítsa be a mezőt **hamis** értékre. Ebben az esetben a fürt létrehozása után csatolnia és konfigurálnia kell a tárolót. Útmutatásért olvassa el a [tároló konfigurálása](avere-vfxt-add-storage.md) című témakört. 

* **(Új) Storage-fiók** – ha új Azure BLOB-tárolót hoz létre, adja meg az új Storage-fiók nevét. 

## <a name="validation-and-purchase"></a>Ellenőrzés és vásárlás

A három oldal összegzi a konfigurációt, és érvényesíti a paramétereket. Az érvényesítés sikeressége után kattintson az **OK** gombra a folytatáshoz. 

![A központi telepítési sablon harmadik lapja – ellenőrzés](media/avere-vfxt-deploy-3.png)

A 4. oldalon adja meg a szükséges kapcsolattartási adatokat, majd kattintson a **Létrehozás** gombra a feltételek elfogadásához és az Azure-fürthöz tartozó avere-vFXT létrehozásához. 

![A központi telepítési sablon negyedik lapja – feltételek és kikötések, létrehozás gomb](media/avere-vfxt-deploy-4.png)

A fürt üzembe helyezése 15-20 percet vesz igénybe.

## <a name="gather-template-output"></a>Sablon kimenetének összegyűjtése

Ha a avere vFXT-sablon befejezi a fürt létrehozását, az az új fürthöz tartozó fontos adatokat jeleníti meg. 

> [!TIP]
> Ügyeljen arra, hogy a felügyeleti IP-címet a sablon kimenetéről másolja. Ehhez a címnek kell lennie a fürt felügyeletéhez.

Az információk megkereséséhez kövesse az alábbi eljárást:

1. Nyissa meg a fürthöz tartozó erőforráscsoportot.

1. A bal oldalon kattintson a **központi telepítések**, majd a **Microsoft-avere. vfxt-template**elemre.

   ![Erőforráscsoport-portál lap a bal oldalon és a Microsoft-avere. vfxt-templateben kiválasztott központi telepítések a központi telepítési név alatt lévő táblázatban](media/avere-vfxt-outputs-deployments.png)

1. A bal oldalon kattintson a **kimenetek**elemre. Másolja az értékeket az egyes mezőkbe. 

   ![kimenetek lap, amely az SSHSTRING, a RESOURCE_GROUP, a LOCATION, a NETWORK_RESOURCE_GROUP, a NETWORK, az SUBNET, a SUBNET_ID, a VSERVER_IPs és a MGMT_IP értékeket jeleníti meg a címkéktől jobbra található mezőkben.](media/avere-vfxt-outputs-values.png)

## <a name="next-step"></a>Következő lépés

Most, hogy a fürt fut, és ismeri a felügyeleti IP-címét, [csatlakozhat a fürt konfigurációs eszközéhez](avere-vfxt-cluster-gui.md) a támogatás engedélyezéséhez, szükség esetén a tárterület hozzáadásához és a fürt egyéb beállításainak testreszabásához.
