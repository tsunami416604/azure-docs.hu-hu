---
title: Háttér-tároló felvétele a Microsoft Azure FXT Edge Filer fürthöz
description: Háttér-tároló és az ügyfél irányultságú pseudonamespace Azure FXT Edge Filer konfigurálása
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 0a16e654ff92c450438ac91c590b42d22201d015
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450456"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Oktatóanyag: Háttér-tároló hozzáadása és konfigurálása a virtuális névtér 

Ez az oktatóanyag azt ismerteti, a gyorsítótár vissza – edge-tár hozzáadása és az ügyfél által használt virtuális fájlrendszer beállítása. 

A fürthöz csatlakozik a háttér-tárolórendszerek az ügyfelek kérelem eléréséhez, és több véglegesen, mint a módosítások tárolásához a gyorsítótárban. 

Az ügyfél irányultságú pszeudo-fájlrendszer, amely lehetővé teszi, hogy cserélhetők fel, hogy a háttér-tároló nélkül változó ügyféloldali munkafolyamatok névtere. 

Ebben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
> * Háttér-tár hozzáadása az Azure FXT Edge Filer fürtre 
> * A storage ügyféloldali elérési útját definiálása

## <a name="about-back-end-storage"></a>Háttér-storage-ról

Az Azure FXT Edge Filer fürt használja egy *filer alapvető* egy háttér-tárolórendszer összekapcsolása a FXT fürt definíciója.

Azure FXT Edge Filer számos népszerű NAS hardverrendszer kompatibilis, és használhatja az Azure Blob- vagy egyéb felhőalapú tárolási üres tárolók. 

Cloud-storage-tárolók üres, ha hozzáadja a FXT operációs rendszer teljes mértékben kezelhető összes adat a felhőalapú tárolási köteten kell lennie. A meglévő adatokat a tároló felvétele a fürtbe, a core filer követően továbbléphet a felhőalapú tároló.

A Vezérlőpult segítségével egy alapvető filer adja hozzá a rendszerhez.

> [!NOTE]
> 
> Ha azt szeretné, az Amazon AWS vagy a Google Cloud storage használatát, telepítenie kell egy FlashCloud<sup>TM</sup> funkció licenc. A Microsoft képviselőjétől kaphat egy licenckulcsot, és kövesse az utasításokat az útmutató a az örökölt konfiguráció [hozzáadása és eltávolítása a szolgáltatás licencek](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
> 
> Az Azure Blob storage támogatja az Azure FXT Edge Filer-szoftver licencének tartalmazza. 

Core kiemelik hozzáadásával kapcsolatos részletes információkért olvassa el a fürt beállítási útmutató ezen szakasza:

* További információ kiválasztása és előkészítése egy core filer hozzáadni, olvassa el [működik az alapvető kiemelik](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Részletes előfeltételeket és részletes útmutatás olvassa el a következő cikkeket:

  * [Egy új NAS-Core Filer hozzáadása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Egy új felhőalapú Core Filer hozzáadása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Miután hozzáadott egy alapvető filer, frissítheti annak beállításait az alapvető Filer részletes beállítások lapon.

## <a name="add-a-core-filer"></a>Adjon hozzá egy alapvető filer

Egy mag filer megadása gombra kattintva a **létrehozás** gombot a **Core Filer** > **Core kiemelik kezelése** beállítások lapon.

![A Létrehozás gombra alapvető kiemelik a Core kiemelik kezelése lapon az eszközlista feletti](media/fxt-cluster-config/create-core-filer-button.png)

A **hozzáadása új Core Filer** varázsló végigvezeti egy alapvető filer a háttérrendszer tárolási mutató létrehozásának folyamatán. A fürt beállítási útmutató részletes leírását a folyamat, amely nem egyezik, NFS vagy NAS-tárolási és a felhőalapú (hivatkozások olyan fent) rendelkezik. 

Altevékenységek a következők:

* Megadhatja a core filer (NAS vagy felhő)

  ![A hardver NAS új core filer varázsló első oldalán. A "felhő alapvető filer" lehetőség le van tiltva, és információ a licenc hiányzik egy hibaüzenetet jelenít meg.](media/fxt-cluster-config/new-nas-1.png)

* A core filer nevének megadása. Válasszon egy nevet, amely segít a fürt rendszergazdák, mely azt jelöli, hogy tárolórendszer ismertetése.

* NAS core kiemelik adja meg a teljesen minősített tartománynevét (FQDN) vagy IP-címet. Teljes tartománynév javasolt az összes mag kiemelik, és az SMB-hozzáférés szükséges.

* Válasszon ki egy gyorsítótár szabályzatot – az új core filer felsorolja a rendelkezésre álló gyorsítótár-házirendek, a varázsló második oldalára. További információkért olvassa el a [gyorsítótárazzák a fürt beállítási útmutató a szabályzatok szakaszban](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Varázsló második lapján egy hardver NAS új core filer; a gyorsítótár legördülő menü meg nyitva, megjelenítése több le van tiltva, és három érvényes gyorsítótár házirend-beállításokat (megkerülés, olvassa el a gyorsítótárazás, és olvasási/írási gyorsítótárazás).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* A felhőalapú a felhőalapú szolgáltatás és a hozzáférési hitelesítő adatokat, többek között a más paramétereket kell megadnia. További információkért olvassa el a [Cloud service és a protokoll](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) az útmutató a fürt konfiguráció.

  ![A felhő alapvető fontosságú filer információk az új Core Filer varázsló](media/fxt-cluster-config/new-core-filer-cloud3.png) <!-- xxx get an Azure version of this screenshot xxx -->
  
  Ha már hozzáadott ehhez a fürthöz tartozó felhőalapú hozzáférési hitelesítő adatokat, azok szerepelnek a listán. Frissítse és adja hozzá a hitelesítő adatokat a **fürt** > **Felhőhöz tartozó hitelesítő adatok** beállítások lapon. 

Miután kitöltötte az összes a szükséges beállításokat a varázslóban, kattintson a **hozzáadása Filer** gombra kattintva küldje el a módosítást.

Néhány pillanat múlva a tárolórendszer jelenik meg a **irányítópult** kiemelik lista alapvető és alapvető filer beállításlapok keresztül érhetők el.

![Core filer "Flurry-NAS" a Core kiemelik kezelése beállítások lapon a filer részletes nézete a kibontva](media/fxt-cluster-config/core-filer-in-manage-page.png)

Ezen a képernyőfelvételen a core filer hiányzik egy vserver. A core filer összekapcsolása egy vserver kell, és hozzon létre egy szinkronizációs pont úgy, hogy az ügyfelek hozzáférhetnek a storage. Ezeket a lépéseket alább találhatók a [konfigurálása a névtér](#configure-the-namespace).

## <a name="configure-the-namespace"></a>A névtér konfigurálása

Az Azure FXT Edge Filer fürt létrehoz egy virtuális fájlrendszer nevű a *névtér fürt* , amely egyszerűbbé teszi a különböző háttérrendszerekhez tárolt adatokat az ügyfél-hozzáférési. Mivel az ügyfelek kérnek egy virtuális elérési út alapján fájlokat, tárolórendszerek hozzáadhatja, vagy cserélni az ügyfél munkafolyamat módosítása nélkül. 

A fürt névteret is lehetővé teszi a felhő- és NAS-tárolási rendszerek egy hasonló fájlstruktúra szerepelnek. 

A névtér és a tartalom kiszolgálása ügyfeleknek a fürt vservers fenntartása érdekében. A fürt-névterek létrehozásának két lépésből áll: 

1. Hozzon létre egy vserver 
1. Állítsa be a háttér-tárolási rendszerek és az ügyfél irányultságú fájlrendszeri elérési utak között elhelyezni pontokra 

### <a name="create-a-vserver"></a>Hozzon létre egy vserver

VServers olyan virtuális fájlkiszolgálók, az ügyfél és a fürt alapvető kiemelik közti adatfolyamok szabályozó:

* VServers host client-facing IP addresses
* VServers hozza létre a névteret, és elhelyezni pontokra, amelyek az ügyfél által használt virtuális könyvtárstruktúrát export a háttér-tároló definiálása
* VServers fájl hozzáférés-vezérlést, beleértve a core filer exportálási szabályzatokat és a felhasználó hitelesítési rendszerekkel kényszerítése
* VServers SMB infrastruktúrát biztosítanak.

Egy fürt vserver beállítása előtt olvassa el a társított dokumentációt, és tekintse meg a Microsoft helyi képviselőjéhez súgó ismertetése névtérhez és vservers. Ha használ VLAN-okat, [hozza létre őket](fxt-configure-network.md#adjust-network-settings) a vserver létrehozása előtt. 

Ezekben a szakaszokban a fürt konfiguráció útmutató segít Ismerkedjen meg a FXT vserver és globális névtér szolgáltatásokat:

* [Az VServers létrehozásához és használatához](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Egy globális Namespace használatával](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Egy VServer létrehozása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

A fürt legalább egy vserver van szükség. 

Hozzon létre egy új vserver, hogy szüksége van a következő információkat:

* A név a vserver beállítása

* Az ügyfél által használt IP-címek tartományát a vserver fogja kezelni

  A vserver létrehozásakor meg kell adnia egy egyetlen folytonos IP-címek tartományát. Később is hozzáadhat további címek segítségével a **ügyfél felől elérhető hálózati** beállítások lapon.

* Ha a hálózat VLAN-okat, mely a vserver használandó VLAN

Használja a **VServer** > **kezelése VServers** hozhat létre egy új vserver beállítások lapon. Részletekért olvassa el [létrehozása egy VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) az útmutató a fürt konfiguráció. 

![egy új vserver létrehozására vonatkozó előugró ablak](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>A szinkronizációs pont létrehozása

A *csatlakozási* egy háttérrendszer. tárolási elérési útja képez le az ügyfél által látható névtérben.

Ez a rendszer egyszerűsíti az elérési utat az ügyfél csatlakoztatási pontokat használ, és megfelelő, zökkenőmentes méretezése kapacitás, mert egy virtuális elérési út lehetővé teszi több mag kiemelik a storage használhatja.

![Adja hozzá az új szinkronizációs pont varázslólap kitölti a rendszer beállításokkal](media/fxt-cluster-config/add-junction-full.png)

Tekintse meg [ **VServer** > **Namespace** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) az útmutató a fürt konfiguráció névtér elágazás létrehozásával kapcsolatos részleteket.

![A VServer > részleteit megjelenítő az elágazás Namespace beállítások lap](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Exportálási szabályok konfigurálása

Miután egy vserver és a egy alapvető filer, az exportálási szabályok testreszabása, és exportálja a házirendeket szabályozó hogyan ügyfelek hozzáférhetnek a core filer export fájlokat.

Először a **VServer** > **exportálása szabályok** oldal az alapértelmezett házirend módosítása, vagy hozzon létre saját egyéni exportálási szabályzat új szabályokat hozzáadni.

A második, használja a **VServer** > **házirend exportálása** lap a testre szabott házirend alkalmazása a core filer export, amikor adott vserver keresztül érhetők el.

A fürt beállítási útmutató a cikk [hozzáférés szabályozása alapvető Filer export](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) részleteiről.


## <a name="next-steps"></a>További lépések

Tárolóeszközök hozzáadása és konfigurálása az ügyfél által használt névtér, után végezze el a fürt kezdeti beállítása: 

> [!div class="nextstepaction"]
> [A fürt hálózati beállítások konfigurálása](fxt-configure-network.md)
