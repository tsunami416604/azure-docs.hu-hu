---
title: Ügyfelek csatlakoztatása a Microsoft Azure FXT Edge Filer-fürtre
description: Hogyan csatlakoztathatják az NFS-ügyfélgépek az Azure FXT Edge Filer hibrid tárolási gyorsítótárát?
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 43223db298e4ad170ea6d0687a342b3aee35500e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130767"
---
# <a name="tutorial-mount-the-cluster"></a>Oktatóanyag: A fürt csatlakoztatása

Ez az oktatóanyag bemutatja, hogyan csatlakoztathatja az NFS-ügyfeleket az Azure FXT Edge Filer-fürthöz. Az ügyfelek a háttértároló hozzáadásakor hozzárendelt virtuális névtérelérési utakat csatolják.

Ez a bemutató a következőket tanítja:

> [!div class="checklist"]
> * Stratégiák a terheléselosztási ügyfelek számára az ügyféloldali IP-címek tartományában
> * Csatlakoztatási útvonal összeállítása ügyféloldali IP-címből és névtércsomópontból
> * A csatlakoztatási parancsban használandó argumentumok

Ez az oktatóanyag körülbelül 45 percet vesz igénybe.

## <a name="steps-to-mount-the-cluster"></a>A fürt csatlakoztatásának lépései

Az alábbi lépésekkel csatlakoztathatja az ügyfélgépeket az Azure FXT Edge Filer-fürthöz.

1. Döntse el, hogyan töltheti el az ügyfélforgalmat a fürtcsomópontok között. Olvassa [el egyenleg ügyfél terhelés](#balance-client-load), az alábbiakban, a részletekért.
1. Azonosítsa a csatlakoztatni a fürt IP-címét és a csatlakozási útvonalat.
1. Határozza meg a csatlakoztatás ügyfélfelé néző elérési útját.
1. Adja ki a [csatlakoztatási parancsot](#use-recommended-mount-command-options)a megfelelő argumentumokkal.

## <a name="balance-client-load"></a>Ügyfélterhelés elosztása

Az ügyfélkérelmek nek a fürt összes csomópontja közötti egyensúlyának érdekében az ügyfeleket az ügyféloldali IP-címek teljes tartományához kell csatlakoztatnia. A feladat számos módon automatizálható.

Ha többet szeretne megtudni a fürt ciklikus multiplexelésű DNS-terheléselosztásáról, olvassa el [a DNS konfigurálása az Azure FXT Edge Filer fürthöz című olvasni.](fxt-configure-network.md#configure-dns-for-load-balancing) A módszer használatához dns-kiszolgálót kell fenntartania, amelyet ezek a cikkek nem ismertenek.

A kis telepítések egyszerűbb módszere egy parancsfájl használata az IP-címek hozzárendeléséhez a tartományban az ügyfélcsatlakoztatási időben.

Más terheléselosztási módszerek is megfelelőek lehetnek nagy vagy bonyolult rendszerek hez. Forduljon a Microsoft képviselőjéhez, vagy nyisson meg egy [támogatási kérelmet.](fxt-support-ticket.md) (Az Azure Load Balancer jelenleg *nem támogatott* az Azure FXT Edge Filer.)

## <a name="create-the-mount-command"></a>A csatlakoztatási parancs létrehozása

Az ügyfélről ``mount`` a parancs leképezi a virtuális kiszolgálót (vserver) az Azure FXT Edge Filer-fürtön a helyi fájlrendszer elérési útvonalához.

A formátum``mount <FXT cluster path> <local path> {options}``

A csatlakoztatási parancsnak három eleme van:

* fürt elérési útja - az alábbiakban ismertetett IP-cím és névtér-csomópont elérési útja
* helyi elérési út - az ügyfél elérési útja
* csatlakoztatási parancs beállításai - (az [Ajánlott csatlakoztatási parancs beállításainak használata)](#use-recommended-mount-command-options)című listában szerepel.

### <a name="create-the-cluster-path"></a>A fürt elérési útjának létrehozása

A fürt elérési útja a vserver *IP-címének* és a *névtércsomópont*elérési útjának kombinációja. A névtércsomópont egy virtuális elérési út, amelyet [a tárolórendszer hozzáadásakor](fxt-add-storage.md#create-a-junction)adott meg.

Ha például a ``/fxt/files`` névtér elérési útjaként használt, az ügyfelek *IP_address*:/fxt/files-t csatlakoztatnának a helyi csatlakoztatási ponthoz.

!["Új csomópont hozzáadása" párbeszédpanel a /avere/files kapcsolóval a névtér elérési útja mezőben](media/fxt-mount/fxt-junction-example.png)

Az IP-cím a vserverhez definiált ügyféloldali IP-címek egyike. Az ügyféloldali IP-k tartományát két helyen találja a fürt vezérlőpultján:

* **VServers** tábla (Dashboard lap) -

  ![A Vezérlőpult irányítópultja a grafikon alatti adattáblázatban kijelölt VServer lapmal, az IP-cím szakasz bekarikázva](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **Ügyfél hálózati** beállítások lap -

  ![A VServer > ügyfél hálózati konfigurációs lapja > egy körrel a táblázat Címtartomány szakasza körül egy adott virtuális kiszolgálóhoz](media/fxt-mount/fxt-ip-addresses-settings.png)

Az IP-cím és a névtér elérési útja egyesítésével alakítsa ki a csatlakoztatási parancs fürtelérési útját.

Példa ügyfélcsatlakoztatási parancsra:``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>A helyi elérési út létrehozása

A csatlakoztatási parancs helyi elérési útja önön múlik. A virtuális névtér részeként bármilyen kívánt görbestruktúrát beállíthat. Tervezzen olyan névteret és helyi elérési utat, amely megfelel az ügyfél-munkafolyamatnak.

Az ügyféloldali névtérről a Fürtkonfigurációs útmutató névtéráttekintése című témakörben olvashat [bővebben.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)

Az elérési utakon kívül adja meg az alábbiakban ismertetett [csatlakoztatási parancsbeállításokat](#use-recommended-mount-command-options) az egyes ügyfelek csatlakoztatásakor.

### <a name="use-recommended-mount-command-options"></a>Az ajánlott csatlakoztatási parancs beállításainak használata

A zökkenőmentes ügyfélcsatlakoztatás biztosításához adja át ezeket a beállításokat és argumentumokat a csatlakoztatási parancsban:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Szükséges beállítások | |
--- | ---
``hard`` | Az Azure FXT Edge Filer-fürt höz való ideiglenes csatlakoztatások alkalmazáshibákkal és esetleges adatvesztéssel vannak társítva.
``proto=netid`` | Ez a beállítás támogatja az NFS hálózati hibák megfelelő kezelését.
``mountproto=netid`` | Ez a beállítás támogatja a hálózati hibák megfelelő kezelését a csatlakoztatási műveletekhez.
``retry=n`` | Állítsa ``retry=30`` be, hogy elkerülje az átmeneti csatlakoztatási hibákat. (Az előtér-tartókban más érték ajánlott.)

| Elsődleges beállítások  | |
--- | ---
``nointr``            | Ha az ügyfelek régebbi operációs rendszermagokat használnak (2008 áprilisa előtt), amelyek támogatják ezt a beállítást, használja azt. Az "intr" beállítás az alapértelmezett beállítás.

## <a name="next-steps"></a>További lépések

Miután csatlakoztatta az ügyfeleket, tesztelheti a munkafolyamatot, és elkezdheti a fürtöt.

Ha adatokat kell áthelyezni egy új felhőmag-fájlkezelőbe, használja ki a gyorsítótár szerkezetét párhuzamos adatbetöltés használatával. Egyes stratégiák leírása az [Adatok áthelyezése vFXT-fürtbe](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest)című részben található. (Az Avere vFXT for Azure egy felhőalapú termék, amely az Azure FXT Edge Filer-hoz nagyon hasonló gyorsítótárazási technológiát használ.)

Olvassa [el az Azure FXT Edge Filer hardverállapotának figyelése,](fxt-monitor.md) ha hardverproblémákelhárítására van szüksége.
