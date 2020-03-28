---
title: 'Oktatóanyag: Tárhely hozzáadása egy Azure FXT Edge Filer-fürthöz'
description: A háttérrendszerű tárolás és az ügyfélfelé néző pszeudonévtér konfigurálása az Azure FXT Edge Filer számára
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 3f736942627d088e3a639f89bef5438714c2608b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239216"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Oktatóanyag: Háttértároló hozzáadása és a virtuális névtér konfigurálása 

Ez az oktatóanyag bemutatja, hogyan adhat hozzá háttértárolót a gyorsítótárhoz, és hogyan állíthatja be az ügyfélfelé néző virtuális fájlrendszert. 

A fürt háttértároló rendszerekhez csatlakozik, hogy hozzáférjen az adatügyfelek kéréséhez, és a módosításokat tartósabban tárolja, mint a gyorsítótárban. 

A névtér az ügyfélfelé néző pszeudo fájlrendszer, amely lehetővé teszi a háttértároló kontakarékonként történő cseréjét az ügyféloldali munkafolyamatok módosítása nélkül. 

Ebben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
> * Háttéralapú tárhely hozzáadása az Azure FXT Edge Filer-fürthöz 
> * Az ügyfélfelé néző elérési út meghatározása a tároláshoz

## <a name="about-back-end-storage"></a>Háttéralapú tárolás –

Az Azure FXT Edge Filer-fürt *egy alapvető filer-definíciót* használ a háttértároló rendszer és az FXT-fürt összekapcsolására.

Az Azure FXT Edge Filer számos népszerű NAS hardverrendszerrel kompatibilis, és üres tárolókat használhat az Azure Blobból vagy más felhőalapú tárhelyből. 

A felhőtároló-tárolóknak hozzáadva üresnek kell lenniük, hogy az FXT operációs rendszer teljes mértékben kezelhesse a felhőtároló köteten lévő összes adatot. A meglévő adatokat áthelyezheti a felhőtárolóba, miután hozzáadja a tárolót a fürthöz core filerként.

A Vezérlőpulton alapvető fájlkezelőt adhat a rendszerhez.

> [!NOTE]
> 
> Ha Amazon AWS vagy Google Cloud tárhelyet szeretne használni, telepítenie kell egy FlashCloud<sup>TM</sup> funkciólicencet. Kérjen licenckulcsot a Microsoft képviselőjéhez, majd kövesse a [szolgáltatáslicencek hozzáadásához vagy eltávolításához](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses)szükséges korábbi konfigurációs útmutató utasításait.
> 
> Az Azure Blob storage támogatása az Azure FXT Edge Filer szoftverlicencrésze. 

Az alapvető fájlkezelők hozzáadásáról a fürtkonfigurációs útmutató alábbi szakaszait olvashatja:

* Ha többet szeretne megtudni az alapvető filer kiválasztásáról és előkészítéséről, olvassa el [a Munka a Core Filers-szel](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* A részletes előfeltételeket és a lépésenkénti utasításokat az alábbi cikkekben találja:

  * [Új NAS Core Filer hozzáadása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Új Cloud Core filer hozzáadása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Miután hozzáadott egy core filer-t, frissítheti a beállításait a Core Filer Details beállítások lapon.

## <a name="add-a-core-filer"></a>Core filer hozzáadása

Definiáljon egy alapvető fájlkezelőt a **Core Filer** > **Manage Core Filers** beállítások lapján található **Létrehozás** gombra kattintva.

![Kattintson a létrehozás gombra a Core Filers kezelése lapon található alapvető filerek listája felett](media/fxt-cluster-config/create-core-filer-button.png)

Az **Új core filer hozzáadása** varázsló végigvezeti a háttértárolóra mutató központi fájlkezelő létrehozásának folyamatán. A fürtkonfigurációs útmutató lépésről lépésre ismerteti a folyamatot, amely különbözik az NFS/NAS-tárhely és a felhőalapú tárolás esetében (a fenti hivatkozások). 

Az altevékenységek a következők:

* Adja meg az alapfájltípust (NAS vagy felhő)

  ![A hardverEs NAS új core filer varázsló első lapja. A "cloud core filer" opció le van tiltva, és hibaüzenetet jelenít meg a hiányzó licencről.](media/fxt-cluster-config/new-nas-1.png)

* Állítsa be a fő fájlkezelő nevét. Válasszon olyan nevet, amely segít a fürtrendszergazdáinak megérteni, hogy melyik tárolórendszert képviseli.

* NAS core filers esetén adja meg a teljesen minősített tartománynevet (FQDN) vagy AZ IP-címet. FQDN ajánlott minden alapvető filers, és szükséges smb hozzáférést.

* Gyorsítótár-házirend kiválasztása – A varázsló második oldala felsorolja az új fő fájlkezelő rendelkezésre álló gyorsítótár-házirendjeit. További részletekért olvassa el [a fürtkonfigurációs útmutató gyorsítótár-házirendek szakaszát.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) 

  ![A hardveres NAS új core filer varázsló második lapja; a Gyorsítótár-házirend legördülő menü meg van nyitva, amely számos letiltott lehetőséget és három érvényes gyorsítótár-házirend-beállítást jelenít meg (bypass, olvasási gyorsítótár, olvasási/írási gyorsítótárazás).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* A felhőalapú tároláshoz többek között meg kell adnia a felhőszolgáltatást és a hozzáférési hitelesítő adatokat. További részletekért olvassa el a [felhőszolgáltatás és](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) a protokoll című részt a fürtkonfigurációs útmutatóban.

  ![Felhőalapú alapvető filer információk az Új core filer varázslóban](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Ha már hozzáadott felhőhozzáférési hitelesítő adatokat ehhez a fürthöz, azok megjelennek a listában. A **fürtfelhő** > hitelesítő adatainak beállításai lapon frissítheti és adja hozzá a hitelesítő**adatokat.** 

A varázsló összes szükséges beállításának kitöltése után kattintson a **Fájlkezelő hozzáadása** gombra a módosítás elküldéséhez.

Néhány pillanat múlva a tárolórendszer megjelenik a **Dashboard** alapvető filers listáján, és elérhető a core filer beállítási oldalakon.

![core filer "Flurry-NAS" a Core Filers beállítások kezelése lapon, a filer részletek nézet kibontva](media/fxt-cluster-config/core-filer-in-manage-page.png)

A képernyőkép központi filer-jének hiányzik egy vserver. A központi fájlkezelőt egy vserverhez kell kapcsolnia, és létre kell hoznia egy csomópontot, hogy az ügyfelek hozzáférhessenek a tárolóhoz. Ezeket a lépéseket [a Névtér konfigurálása](#configure-the-namespace)című részben ismertetjük.

## <a name="configure-the-namespace"></a>A névtér konfigurálása

Az Azure FXT Edge Filer-fürt létrehoz egy virtuális fájlrendszert, a *fürt névterét,* amely leegyszerűsíti az ügyfél hozzáférést a különböző háttérrendszereken tárolt adatokhoz. Mivel az ügyfelek virtuális elérési út használatával kérnek fájlokat, a tárolórendszerek az ügyfélmunkafolyamat módosítása nélkül is hozzáadhatók vagy cserélhetők. 

A fürtnévtér lehetővé teszi a felhő- és NAS-tárolórendszerek hasonló fájlstruktúrában történő bemutatását is. 

A fürt v-kiszolgálói fenntartják a névteret és a tartalmat az ügyfelek számára. A fürt névtere két lépésből áll: 

1. Vserver létrehozása 
1. A háttértároló rendszerek és az ügyfélfelé néző fájlrendszer elérési útjai közötti csomópontok beállítása 

### <a name="create-a-vserver"></a>Vserver létrehozása

A VServers virtuális fájlkiszolgálók, amelyek az ügyfél és a fürt alapvető fájlkezelői közötti adatáramlást szabályozzák:

* VServers ügyféloldali IP-címeket üzemeltet
* A VServers létrehozza a névteret, és olyan csomópontokat határoz meg, amelyek az ügyfélfelé néző virtuális könyvtárstruktúrát a háttértárolók exportálásához
* A VServers fájlhozzáférés-vezérlést kényszerít, beleértve az alapvető fájlkezelő exportálási házirendeket és a felhasználói hitelesítési rendszereket
* A VServers smb-infrastruktúrát biztosít

A fürt vserver konfigurálásának megkezdése előtt olvassa el a csatolt dokumentációt, és a névtér és a vkiszolgálók megértéséhez forduljon a Microsoft képviselőjéhez. VlAN-ok használata esetén [hozza létre őket](fxt-configure-network.md#adjust-network-settings) a vserver létrehozása előtt. 

A fürtkonfigurációs útmutató ezen szakaszai segítenek megismerkedni az FXT vserver és a globális névtér szolgáltatásaival:

* [Vservers létrehozása és használata](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Globális névtér használata](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [VServer létrehozása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Legalább egy virtuális kiszolgálóra van szükség a fürthöz. 

Új virtuális kiszolgáló létrehozásához a következő információkra van szükség:

* A vserver hez beállított név

* A vserver által kezelt ügyféloldali IP-címek tartománya

  A vserver létrehozásakor egyetlen összefüggő IP-címtartományt kell megadnia. Később további címeket adhat hozzá az **Ügyfél hálózati** beállítások lapon.

* Ha a hálózat VLAN-okkal rendelkezik, amelyeket a VLAN-hoz kell használni a vserver

Új v-kiszolgáló létrehozásához használja a **VServer** > **VServers-kezelés** beállításait. További részletekért olvassa [el A VServer létrehozása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) a fürt konfigurációs útmutatójában. 

![előugró ablak új vserver létrehozásához](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Csomópont létrehozása

A *csomópont* leképezi a háttér-tárolási útvonalat az ügyfél által látható névtérhez.

Ezzel a rendszerrel egyszerűsítheti az ügyfélcsatlakoztatási pontokban használt elérési utat, és zökkenőmentesen skálázhatja a kapacitást, mivel egy virtuális elérési út több magos fájlkezelőből is képes tárolni.

![Új csomópont hozzáadása varázsló lap kitöltött beállításokkal](media/fxt-cluster-config/add-junction-full.png)

A névtércsomópont létrehozásáról a Fürtkonfigurációs útmutató [ **VServer-névtércímű** > ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) dokumentumában talál teljes körű információt.

![A VServer > névtér beállítási lapja, amely egy csomópont részleteit tartalmazza](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Exportálási szabályok konfigurálása

Miután rendelkezik egy vserver és egy core filer, testre kell szabnia az exportálási szabályok és az exportálási szabályzatok, amelyek szabályozzák, hogy az ügyfelek hogyan férhetnek hozzá a fájlokat a core filer export.

Először a **VServer** > **exportálási szabályok** lapon új szabályokat vehet fel, módosíthatja az alapértelmezett házirendet, vagy létrehozhat saját egyéni exportálási házirendet.

Másodszor, a **VServer** > **exportszabályzatok** lapon alkalmazhatja a testreszabott házirendet a fő fájlkezelő exportálására, amikor az adott virtuális kiszolgálón keresztül érhető el.

A részletekért olvassa el a fürtkonfigurációs útmutató cikkét, [amely szabályozza a core filer-exportáláshoz való hozzáférést.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html)


## <a name="next-steps"></a>További lépések

A tároló hozzáadása és az ügyfélfelé néző névtér konfigurálása után fejezze be a fürt kezdeti beállítását: 

> [!div class="nextstepaction"]
> [A fürt hálózati beállításainak konfigurálása](fxt-configure-network.md)
