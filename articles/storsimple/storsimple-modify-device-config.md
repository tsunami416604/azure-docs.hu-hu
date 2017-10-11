---
title: "A StorSimple eszköz konfigurációjának módosítása |} Microsoft Docs"
description: "Ismerteti a StorSimple Manager szolgáltatás segítségével konfigurálja újra a StorSimple eszköz, amely már telepítve van."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: bb679264-8d46-429c-9ef7-630dc3b4a423
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/19/2017
ms.author: v-sharos
ms.openlocfilehash: 5bfbeb038dbedae2bf77016abbc19458c3dc22c9
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>A StorSimple Manager szolgáltatással a StorSimple eszköz konfigurációjának módosítása
## <a name="overview"></a>Áttekintés
A klasszikus Azure portálon **konfigurálása** lap tartalmaz olyan módon konfigurálhatja újra a StorSimple eszközön a StorSimple Manager szolgáltatás által kezelt eszköz paramétereket. Ez az oktatóanyag azt ismerteti, hogyan használhatja a **konfigurálása** lap a következő eszközszintű feladatok végezhetők el:

* Eszköz beállításainak módosítása 
* Idő beállításainak módosítása 
* DNS-beállításainak módosítása 
* Hálózati illesztők módosítása
* A felcserélendő vagy IP-cím ismételt hozzárendelése

## <a name="modify-device-settings"></a>Eszköz beállításainak módosítása
Az eszköz beállítások közé tartozik az eszköz és az eszköz leírása rövid nevét.

> [!NOTE] 
> Az eszköz neve a klasszikus Azure portálon nem módosítható. Az eszköz átnevezése nem támogatott.

A StorSimple eszköz, amely a StorSimple Manager szolgáltatás csatlakozik hozzá van rendelve egy alapértelmezett nevet. Az alapértelmezett név általában tükrözi az eszköz sorozatszámát. Például egy alapértelmezett eszköz neve 15 karakternél hosszabb, mint 8600-SHX0991003G44HT, például azt jelzi, a következő:

* **8600** – azt jelzi, az eszköz típusa.
* **SHX** – azt jelzi, a gyártási helyet.
* **0991003** -azt jelzi, a termék.
* **G44HT**– az utolsó 5 számjegy eggyel növekszik, egyedi sorozatszámokat létrehozásához. Az egymást követő beállítása nem lehet.

Egy eszköz leírást is megadhat. Egy eszköz leírása általában segít azonosítani a tulajdonos és az eszköz a fizikai helye. A Leírás mezőben 256-nál kevesebb karaktert tartalmazhat.

## <a name="modify-time-settings"></a>Idő beállításainak módosítása
Az eszköz hitelesítéséhez, a felhő tárolási szolgáltató kell szinkronizálja az időt. A legördülő listából válassza ki az időzónát, és adjon meg legfeljebb két Network Time Protocol (NTP) kiszolgálót. Az elsődleges NTP-kiszolgáló szükséges, és van megadva, ha használatával a Windows PowerShell-lel konfigurálja az eszközt. Megadhatja, hogy a Windows Server alapértelmezett **time.windows.com** az NTP-kiszolgálóként. Megtekintheti az elsődleges NTP-kiszolgálókonfiguráció a klasszikus Azure portálon keresztül, de a Windows PowerShell-felületet segítségével módosíthatja.

A másodlagos NTP-kiszolgáló konfigurálása nem kötelező. A klasszikus portál segítségével másodlagos NTP-kiszolgáló konfigurálása. 

Az NTP-kiszolgáló beállításakor győződjön meg arról, hogy a hálózat engedélyezi-e a NTP-adatforgalmat az adatközpontban a az internethez. Egy nyilvános NTP-kiszolgáló megadása esetén győződjön meg arról, hogy a hálózati tűzfalak és egyéb biztonsági eszközök legyenek konfigurálva ahhoz felé és felől a külső hálózati utazik NTP-adatforgalmat. Ha nem engedélyezett a kétirányú NTP-adatforgalmat, egy belső NTP-kiszolgáló (egy Windows rendszerbeli tartományvezérlőnek biztosítja ezt a funkciót) kell használnia. Ha az eszköz nem próbálta szinkronizálni az időt, akkor nem lehet képes kommunikálni a felhő tárolási szolgáltatót.

Nyilvános NTP-kiszolgálók listájának megtekintéséhez keresse fel a [NTP-kiszolgáló Web](http://support.ntp.org/bin/view/Servers/WebHome). 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Mi történik, ha az eszköz telepítve van egy másik időzónába?
Ha az eszközt másik időzónában van telepítve, az eszköz időzóna módosíthatja. Fényében, hogy a biztonsági mentési házirendek az eszköz időzónát használja, a biztonsági mentési házirendek automatikusan új időzónájának megfelelően módosítsa. Meg kell adni a felhasználói beavatkozás nélkül.

## <a name="modify-dns-settings"></a>DNS-beállításainak módosítása
DNS-kiszolgáló használatos, amikor az eszköz kísérel meg kommunikálni a tárolási felhőszolgáltatást. A magas rendelkezésre állás érdekében meg kell az elsődleges, mind a másodlagos DNS-kiszolgálók konfigurálása az eszközök kezdeti telepítése során. Konfigurálja újra az elsődleges DNS-kiszolgáló, szüksége lesz a Windows PowerShell felületet a StorSimple eszköz használatára.

Ha módosítani szeretné a másodlagos DNS-kiszolgáló, használhatja a klasszikus Azure portálon.

## <a name="modify-network-interfaces"></a>Hálózati illesztők módosítása
Az eszköz hat eszköz hálózati adapterrel rendelkezik, négyet 1 gbe-s, illetve amelyek két 10 GbE. Ezek a kapcsolatok Data 0 – 5 adatok tartalma. 0, az 1, adatok 4 és adatok 5 esetén 1 gbe-s, mivel a DATA 2 és a DATA 3 10 GbE hálózati adapterrel.

Konfigurálása **hálózati kapcsolati beállítások** az egyes használni a felületet. Magas rendelkezésre állás biztosítása érdekében javasoljuk, hogy legalább két iSCSI és a felhőalapú kapcsolatain két az eszközön. Azt javasoljuk, de nem szükséges, hogy a nem használt csatolók le kell tiltani.

Amikor konfigurálja a hálózati illesztők bármelyikét, konfigurálnia kell egy virtuális IP-cím (VIP).

DATA 0 alapértelmezés szerint felhő engedélyezve. DATA 0 konfigurálásakor is szükségesek két fix IP-címek, egyet a tartományvezérlők konfigurálása. A fix IP-címek közvetlenül a eszközvezérlők eléréséhez használható, és akkor hasznos, ha a frissítések telepítése az eszközre, vagy amikor hozzáfér a tartományvezérlők hibaelhárítás céljából.

A StorSimple 8000 Series Update 1, 0 adatok útválasztási metrika értéke a legkisebb; Ezért ha az eszközt a StorSimple 8000 Series Update 1 fut, a felhőalapú forgalom az DATA 0 keresztül történik. Jegyezze fel a ez Ha egynél több felhő-kompatibilis hálózati adaptert a StorSimple eszköz.

> [!NOTE]
> A vezérlőhöz tartozó fix IP-címek az eszköz frissítéseinek karbantartásához használatosak. Ezért a fix IP-knek irányíthatóknak kell lenniük, és csatlakoztatva kell lenniük az internethez.
> 
> 

Mindegyik hálózati interfész jelennek meg a következő paraméterekkel:

* **Sebesség** – nem a felhasználó által konfigurálható paraméter. 0, az 1, adatok 4 és adatok 5 esetén mindig 1 gbe-s, mivel a DATA 2 és a DATA 3 10 GbE felületek.
  
  > [!NOTE]
  > Sebesség és a kétirányú mindig automatikus-egyeztetését. Jumbo-keretek támogatása nem támogatottak.
  > 
  > 
* **Csatoló állapota** – illesztőfelület engedélyezhetők és letilthatók. Ha engedélyezve van, az eszköz megkísérli a felületen. Azt javasoljuk, hogy csak a csatlakozik a hálózathoz és használt csatolók engedélyezve legyen. Minden nem használt csatolók letiltása.
* **Illesztőfelület-típuson** – Ez a paraméter lehetővé teszi az iSCSI forgalom és a felhő tárolási forgalom elkülönítésére. Ez a paraméter a következők egyike lehet:
  
  * **Engedélyezett felhő** – Ha engedélyezve van, az eszköz kommunikáljon a felhő Ez az interfész fogja használni.
  * **az iSCSI engedélyezve** – Ha engedélyezve van, az eszköz Ez az interfész használja az iSCSI-gazdagép kommunikálni.
    
    Azt javasoljuk, hogy az iSCSI forgalom és a felhő tárolási forgalom elkülönítésére. Ne feledje, ha a gazdagép az eszköz azonos alhálózaton belül, nem kell rendelni egy átjáró; azonban ha a gazdagép egy másik alhálózat, mint az eszköz, szüksége lesz egy átjáró hozzárendelni.
* **IP-cím** – Ez lehet IPv4 vagy IPv6-alapú vagy mindkettőt. Az IPv4 és IPv6 címcsaládra eszköz hálózati csatolók esetében támogatottak. IPv4-alapú használata esetén adja meg egy 32-bit-es IP-cím (*xxx.xxx.xxx.xxx*) pont decimális jelöléssel. IPv6 használatakor egyszerűen adjon meg egy 4 számjegyből álló előtagot, és a 128 bites cím jön létre automatikusan az eszköz hálózati illesztőt ezt az előtagot.
* **Alhálózati** – Ez az alhálózati maszk hivatkozik, és be van állítva, a Windows PowerShell felületén keresztül.
* **Átjáró** – Ez a csomópontot, amely nem azonos IP-címterület (alhálózat) belül kommunikálni kísérletek során ez a felület használandó alapértelmezett átjáróként. Az alapértelmezett átjáró kell ugyanazt a címtartományt (alhálózat) az interfésszel IP-cím, alhálózati maszk alapján.
* **Statikus IP-cím** – Ez a mező akkor válik elérhetővé, csak, amíg a DATA 0 konfigurálnia felületet. A műveletek, például a frissítések vagy az eszköz hibaelhárítása szükség lehet az eszköz vezérlő történő közvetlen csatlakoztatásának. A rögzített IP-címet az aktív és az eszközön a passzív vezérlő eléréséhez használható.

A klasszikus Azure portálon keresztül újrakonfigurálhatja az vezérlő 0 és a vezérlő 1.

> [!NOTE]
> * Ahhoz, hogy a megfelelő műveletet, ellenőrizze a illesztőjének sebessége és a kétirányú eszköz csatolóhoz csatlakoztatott kapcsolón. Kapcsoló felületek vagy egyeztetni kell, vagy Gigabit Ethernet konfigurálni (1000 MB/s) és a kétirányú lehet. Alacsony sebességen vagy fél duplex működő felületek teljesítményproblémák eredményez.
> * Megszakításait és az állásidő minimalizálása érdekében azt javasoljuk, hogy engedélyezi-e portfast egyes kapcsoló portot, amelyet az eszköz iSCSI hálózati illesztő csatlakozik. Ezzel biztosíthatja, hogy hálózati kapcsolat létesíthető gyorsan feladatátvétel esetén.
> 
> 

## <a name="swap-or-reassign-ips"></a>A felcserélendő vagy IP-cím ismételt hozzárendelése
Jelenleg Ha a tartományvezérlő hálózati csatolóhoz (ugyanarra az eszközre vagy egy másik eszköz a hálózat) által hozzárendelt virtuális IP-címhez, amely használatban van, majd a tartományvezérlő hajt végre feladatátvételt. Ezért kell követni a megfelelő eljárást, ha az eszköz hálózati adapter van csere a virtuális IP-címek, mert ismétlődő IP-helyzet hoz létre.

A következő lépésekkel vagy ismételt hozzárendelése a virtuális IP-címek a hálózati adapterek bármelyikéhez:

#### <a name="to-reassign-ips"></a>IP-címek hozzárendelésének
1. Kapcsolja ki a két felülethez IP-címet.
2. Az IP-címek törlődik, miután az új IP-címek kiosztása a megfelelő felületek.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [az MPIO konfigurálása a StorSimple eszköz](storsimple-configure-mpio-windows-server.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

