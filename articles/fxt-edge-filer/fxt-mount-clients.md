---
title: Az ügyfelek a Microsoft Azure FXT Edge Filer fürthöz csatlakoztatni
description: NFS-ügyfélgépek hogyan csatlakoztathatja az Azure FXT Edge Filer hibrid tárolási gyorsítótár
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: b48d8d74843947c3e40dc80234560b0147be6eea
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542962"
---
# <a name="tutorial-mount-the-cluster"></a>Oktatóanyag: A fürt csatlakoztatása

Ez az oktatóanyag bemutatja, hogyan csatlakoztathatja az NFS-ügyfelek az Azure FXT Edge Filer fürthöz. Az ügyfelek csatlakoztassa a háttér-tárolási hozzáadásakor hozzárendelt virtuális névtér elérési utakat. 

Az oktatóanyagban azzal foglalkozunk: 

> [!div class="checklist"]
> * Stratégiák terheléselosztási ügyfelek elosztását a ügyfél felé néző IP-címek tartománya
> * Hogyan hozható létre egy csatlakoztatási elérési útját a ügyfél felé néző IP cím és a névtér elágazás
> * Melyik argumentumok a mount parancs használata

Ez az oktatóanyag befejezéséhez körülbelül 45 percet vesz igénybe.

## <a name="steps-to-mount-the-cluster"></a>A fürt csatlakoztatására lépések

Kövesse az alábbi lépéseket az ügyfélgépek a Azure FXT Edge Filer fürthöz való kapcsolódáshoz.

1. Annak eldöntése, hogyan kell a fürt csomópontok közötti terheléselosztás ügyfél forgalmát. Olvasási [egyenleg ügyfélterhelés](#balance-client-load), az alábbi részleteket. 
1. Azonosítsa a fürt IP-cím és a szinkronizációs pont elérési csatlakoztatásához.
1. Határozza meg, hogy az ügyfél irányultságú a csatlakoztatási elérési útját.
1. A probléma a [mount parancs](#use-recommended-mount-command-options), megfelelő argumentumokkal.

## <a name="balance-client-load"></a>Egyenleg ügyfél betöltése

Annak érdekében, eloszthatja a kérelmeket ügyfél a fürt összes csomópontja között, az ügyfelek az ügyfél által használt IP-címek teljes tartományához kell csatlakoztatja. Többféleképpen is ez a feladat automatizálását.

Ciklikus időszeletelési DNS terheléselosztási a fürt kapcsolatos további információkért olvassa el a [DNS konfigurálása az Azure FXT Edge Filer fürt](fxt-configure-network.md#configure-dns-for-load-balancing). Ez a módszer megmaradjanak kell egy DNS-kiszolgáló, amelynek az alábbi cikkek nem ismertetése.

Egy egyszerűbb módszert a kis méretű telepítések, hogy a parancsfájl a tartomány teljes IP-címek hozzárendelése ügyfél csatlakoztatási időpontban. 

Más terheléselosztási módszerek nagy vagy összetett rendszereket megfelelő lehet. Nézze át a Microsoft helyi képviselőjéhez, vagy nyisson egy támogatási kérést segítséget. (Az azure Load Balancer jelenleg *nem támogatott* az Azure FXT Edge Filer.)

## <a name="create-the-mount-command"></a>A mount parancs létrehozása 

Az ügyfélről a ``mount`` parancsot a virtuális kiszolgáló (vserver) leképezi a helyi fájlrendszer a Azure FXT Edge Filer fürtön elérési útra. 

A formátum ``mount <FXT cluster path> <local path> {options}``

Nincsenek a mount parancs három elemek: 

* fürt elérési út – IP-címére és névtér szinkronizációs pont elérési alább ismertetett kombinációja
* helyi elérési út – a elérési utat az ügyfélen 
* csatlakoztatási beállítások parancs - (felsorolt [mount parancs beállításai ajánlott](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>A fürt elérési utat hoz létre

A fürt elérési út a vserver kombinációját *IP-cím* plusz elérési útját egy *névtér csatlakozási*. A névtér szinkronizációs pont a virtuális elérési utat, ha definiálva, [hozzáadva a tárolórendszer](fxt-add-storage.md#create-a-junction).

Például, ha a használt ``/fxt/files`` a névtér elérési útjaként az ügyfelek csatlakoztatni szeretné *IP_cím*: / fxt vagy fájlokat a helyi csatlakoztatási ponton. 

![A névtér elérési út mezőbe fájlokkal/avere / "Új szinkronizációs pont hozzáadása" párbeszédpanelen](media/fxt-mount/fxt-junction-example.png)

Az IP-cím a megadott a vserver az ügyfél által használt IP-címek egyike. A számos ügyfél által használt IP-címek a Vezérlőpult fürt két helyen található:

* **VServers** táblára (fülre irányítópult) – 

  ![Irányítópult lap a Vezérlőpult a kiválasztott a graph és az IP-cím szakasz alatti adatok táblázatban VServer lap bekarikázott](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **Ügyfél felől elérhető hálózati** beállítások lap – 

  ![Beállítások > VServer > a táblázat egy adott vserver címtartomány szakaszában körüli kör az ügyfél felől elérhető hálózati konfigurációs lapja](media/fxt-mount/fxt-ip-addresses-settings.png)

Kombinálja az IP-cím és a névterek elérési kialakításához a csatlakoztatási parancsot a fürt elérési útját. 

Példa ügyfél mount parancs: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>A helyi elérési utat hoz létre

A mount parancs a helyi elérési útja szerint strukturálhatja. Beállíthat bármilyen elérési út struktúrát választhat a virtuális névtér részeként. A névtér és a helyi elérési útja, amely egy kényelmes megoldás az ügyfél munkafolyamatát tervezése. 

Az ügyfél által használt névtér kapcsolatos további információkért olvassa el a fürt beállítási útmutató [névtér áttekintés](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html).

Mellett az elérési utak közé tartozik a [csatlakoztatási parancs beállítások](#use-recommended-mount-command-options) alább leírt, amikor az egyes ügyfelek.

### <a name="use-recommended-mount-command-options"></a>Az ajánlott mount parancs beállítások használata

Ahhoz, hogy egy zökkenőmentes ügyfél csatlakoztatási, adja át ezeket a beállításokat és argumentumok a mount parancs: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Kötelező beállítások | |
--- | --- 
``hard`` | Helyreállítható csatlakoztatása az Azure FXT Edge Filer fürthöz társítva alkalmazáshibák és adatvesztés lehetséges. 
``proto=netid`` | Ez a lehetőség támogatja a megfelelő NFS hálózati hibák kezelésére.
``mountproto=netid`` | Ezt a beállítást támogatja a csatlakoztatási műveletek megfelelő hálózati hibák kezelésére.
``retry=n`` | Állítsa be ``retry=30`` átmeneti csatlakoztatási hibák elkerülése érdekében. (Az előtérben történő csatlakoztatása egy másik értéket javasolt.)

| Előnyben részesített beállításai  | |
--- | --- 
``nointr``            | Ha az ügyfelek a régebbi operációs rendszer kernelt (előtt április 2008) támogatja ezt a beállítást használja, használja azt. A "megszakítás" beállítást az alapértelmezett érték.

## <a name="next-steps"></a>További lépések

Miután csatlakoztatta az ügyfelek, tesztelheti a munkafolyamatot, és a fürt használatának első lépései.

Ha az adatok áthelyezése egy új felhőalapú core filer, figyelembe kell betöltési előnyeit, a gyorsítótár struktúra párhuzamos adatok használatával. Néhány stratégiát ismertetett [adatok áthelyezése a fürt vFXT](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest). (Avere vFXT az Azure-hoz az egy felhőalapú megoldás, amely nagyon hasonlít a Azure FXT Edge Filer gyorsítótárazási technológiát használ.)

Olvasási [figyelő Azure FXT Edge Filer hardverállapot](fxt-monitor.md) Ha bármilyen hardveres hibaelhárításra van szüksége. 
