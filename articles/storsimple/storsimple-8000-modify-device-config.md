---
title: Az a StorSimple 8000 series konfigurációjának módosítása |} Microsoft Docs
description: Ismerteti a StorSimple Device Manager szolgáltatás segítségével konfigurálja újra a StorSimple eszköz, amely már telepítve van.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 13ff24c24a881297775fa5f65821e53ceb83c351
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875157"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>A StorSimple Device Manager szolgáltatással a StorSimple eszköz konfigurációjának módosítása

## <a name="overview"></a>Áttekintés

Az Azure-portálon **eszközbeállítások** szakasz a **beállítások** panel tartalmaz minden olyan módon konfigurálhatja újra a StorSimple eszközön a StorSimple eszköz Manager szolgáltatás által kezelt eszköz paraméterek . Ez az oktatóanyag azt ismerteti, hogyan használhatja a **beállítások** panelen a következő eszközszintű feladatok végezhetők el:

* Módosítsa az eszköz rövid neve
* Eszköz idő beállításainak módosítása
* A másodlagos DNS hozzárendelése
* Hálózati illesztők módosítása
* A felcserélendő vagy IP-cím ismételt hozzárendelése

## <a name="modify-device-friendly-name"></a>Módosítsa az eszköz rövid neve

Az Azure portál segítségével módosíthatja az eszköz nevét, és rendelje hozzá egy egyedi nevet az Ön által választott. Használja a **általános beállítások** panel az eszköz rövid neve módosítása az eszközön. A rövid név bármilyen karaktert tartalmazhat, és legfeljebb 64 karakter hosszú lehet.

> [!NOTE] 
> Csak akkor módosíthatja az eszköz neve, az Azure portálon, az eszköz beállításának befejezése előtt. A minimális eszközbeállítások végrehajtása után az eszköz nevét nem módosíthatja.

![Eszköz neve általános beállításai](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

A StorSimple eszköz, amely a StorSimple Device Manager szolgáltatás csatlakozik hozzá van rendelve egy alapértelmezett nevet. Az alapértelmezett név általában tükrözi az eszköz sorozatszámát. Például egy alapértelmezett eszköz neve 15 karakternél hosszabb, mint 8600-SHX0991003G44HT, például azt jelzi, a következő:

* **8600** – azt jelzi, az eszköz típusa.
* **SHX** – azt jelzi, a gyártási helyet.
* **0991003** -azt jelzi, a termék.
* **G44HT**– az utolsó 5 számjegy eggyel növekszik, egyedi sorozatszámokat létrehozásához. Az egymást követő beállítása nem lehet.

## <a name="modify-device-description"></a>Eszköz leírása módosítása

Használja a **általános beállítások** panel az eszköz leírása módosítása az eszközön.

![Az általános beállítások eszköz leírása](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Egy eszköz leírása általában segít azonosítani a tulajdonos és az eszköz a fizikai helye. A Leírás mezőben 256-nál kevesebb karaktert tartalmazhat.

## <a name="modify-time-settings"></a>Idő beállításainak módosítása

Az eszköz hitelesítéséhez, a felhő tárolási szolgáltató kell szinkronizálja az időt. Használja a **általános beállítások** panel módosítása az eszközön beállításokat az eszközön.

![Az általános beállítások eszköz leírása](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 A legördülő listából válassza ki az időzónát. Legfeljebb két Network Time Protocol (NTP) kiszolgálók adhatók meg:

 - **Elsődleges NTP-kiszolgáló** -a konfiguráció szükséges, és van megadva, ha használatával a Windows PowerShell-lel konfigurálja az eszközt. Megadhatja, hogy a Windows Server alapértelmezett **time.windows.com** az NTP-kiszolgálóként. Megtekintheti az elsődleges NTP-kiszolgálókonfiguráció az Azure portálon keresztül, de a Windows PowerShell-felületet segítségével módosíthatja. Használja a `Set-HcsNTPClientServerAddress` parancsmagot az elsődleges NTP-kiszolgáló, az eszköz módosításához. További információkért látogasson el synxtax [Set-HcsNTPClientServerAddress] (https://technet.microsoft.com/library/dn688138.aspx) parancsmag.

- **Másodlagos NTP-kiszolgáló** -a konfigurálása nem kötelező. A portál segítségével másodlagos NTP-kiszolgáló konfigurálása.

Az NTP-kiszolgáló beállításakor győződjön meg arról, hogy a hálózat engedélyezi-e a NTP-adatforgalmat az adatközpontban a az internethez. Egy nyilvános NTP-kiszolgáló megadása esetén győződjön meg arról, hogy a hálózati tűzfalak és egyéb biztonsági eszközök legyenek konfigurálva ahhoz felé és felől a külső hálózati utazik NTP-adatforgalmat. Ha nem engedélyezett a kétirányú NTP-adatforgalmat, egy belső NTP-kiszolgáló (egy Windows rendszerbeli tartományvezérlőnek biztosítja ezt a funkciót) kell használnia. Ha az eszköz nem próbálta szinkronizálni az időt, akkor nem lehet képes kommunikálni a felhő tárolási szolgáltatót.

Nyilvános NTP-kiszolgálók listájának megtekintéséhez keresse fel a [NTP-kiszolgáló Web](http://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Mi történik, ha az eszköz telepítve van egy másik időzónába?

Ha az eszközt másik időzónában van telepítve, az eszköz időzóna módosíthatja. Fényében, hogy a biztonsági mentési házirendek az eszköz időzónát használja, a biztonsági mentési házirendek automatikusan új időzónájának megfelelően módosítsa. Meg kell adni a felhasználói beavatkozás nélkül.

## <a name="modify-dns-settings"></a>DNS-beállításainak módosítása

DNS-kiszolgáló használatos, amikor az eszköz kísérel meg kommunikálni a tárolási felhőszolgáltatást. Használja a **hálózati beállításai** panelen megtekintheti és módosíthatja a konfigurált DNS-beállítások az eszközön. 

![DNS-beállításokat a hálózati beállítások](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

A magas rendelkezésre állás érdekében meg kell az elsődleges, mind a másodlagos DNS-kiszolgálók konfigurálása az eszközök kezdeti telepítése során.

**Elsődleges DNS-kiszolgáló** -először adja meg az elsődleges DNS-kiszolgáló első telepítése során a Windows PowerShell-lel használhatja. Az elsődleges DNS-kiszolgáló csak a Windows PowerShell felületén keresztül olyan módon konfigurálhatja újra. Használja a `Set-HcsDNSClientServerAddress` parancsmagot az elsődleges DNS-kiszolgáló: az eszköz módosításához. További információkért látogasson el a synxtax [Set-HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) parancsmag.

**Másodlagos DNS-kiszolgáló** – való a másodlagos DNS-kiszolgáló módosításához használja a `Set-HcsDNSClientServerAddress` parancsmag a Windows PowerShell felületen, az eszköz vagy **hálózati beállításai** panelen a StorSimple eszközt az Azure portálon található.

A másodlagos DNS-kiszolgáló Azure-portálon módosítja, hajtsa végre az alábbi lépéseket.

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Az eszközök listájában válassza ki, majd kattintson az eszköz.

2. Az a **beállítások** paneljén lépjen **eszközbeállítások > hálózati**. Ezzel megnyílik a **hálózati beállításai** panelen. Kattintson a **DNS-beállítások** csempére. A másodlagos DNS-kiszolgáló IP-cím módosításához.

    ![Másodlagos DNS-kiszolgáló IP adderss módosítása](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. A parancssávon kattintson **mentése** és megerősítést kér, amikor kattintson **OK**.

    ![Mentse és módosításának megerősítése](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Hálózati illesztők módosítása

Az eszköz hat eszköz hálózati adapterrel rendelkezik, négyet 1 gbe-s, illetve amelyek két 10 GbE. Ezek a kapcsolatok Data 0 – 5 adatok tartalma. 0, az 1, adatok 4 és adatok 5 esetén 1 gbe-s, mivel a DATA 2 és a DATA 3 10 GbE hálózati adapterrel.

Használja a **hálózati beállításai** panel minden használandó felületek be.

![Hálózati illesztők keresztül hálózati beállítások konfigurálása](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Magas rendelkezésre állás biztosítása érdekében javasoljuk, hogy legalább két iSCSI és a felhőalapú kapcsolatain két az eszközön. Azt javasoljuk, de nem szükséges, hogy a nem használt csatolók le kell tiltani.

Mindegyik hálózati interfész jelennek meg a következő paraméterekkel:

* **Sebesség** – nem a felhasználó által konfigurálható paraméter. 0, az 1, adatok 4 és adatok 5 esetén mindig 1 gbe-s, mivel a DATA 2 és a DATA 3 10 GbE felületek.
  
  > [!NOTE]
  > Sebesség és a kétirányú mindig automatikus-egyeztetését. Jumbo-keretek támogatása nem támogatottak.
  
* **Csatoló állapota** – illesztőfelület engedélyezhetők és letilthatók. Ha engedélyezve van, az eszköz megkísérli a felületen. Azt javasoljuk, hogy csak a csatlakozik a hálózathoz és használt csatolók engedélyezve legyen. Minden nem használt csatolók letiltása.
* **Illesztőfelület-típuson** – Ez a paraméter lehetővé teszi az iSCSI forgalom és a felhő tárolási forgalom elkülönítésére. Ez a paraméter a következők egyike lehet:
  
  * **Engedélyezett felhő** – Ha engedélyezve van, az eszköz kommunikáljon a felhő Ez az interfész fogja használni.
  * **az iSCSI engedélyezve** – Ha engedélyezve van, az eszköz Ez az interfész használja az iSCSI-gazdagép kommunikálni.
    
    Azt javasoljuk, hogy az iSCSI forgalom és a felhő tárolási forgalom elkülönítésére. Ne feledje, ha a gazdagép az eszköz azonos alhálózaton belül, nem kell rendelni egy átjáró; azonban ha a gazdagép egy másik alhálózat, mint az eszköz, szüksége lesz egy átjáró hozzárendelni.
* **IP-cím** – a hálózati adapterek konfigurálásakor konfigurálnia kell egy virtuális IP-cím (VIP). Ez lehet IPv4 vagy IPv6-alapú vagy mindkettőt. Az IPv4 és IPv6 címcsaládra eszköz hálózati csatolók esetében támogatottak. IPv4-alapú használata esetén adja meg egy 32-bit-es IP-cím (*xxx.xxx.xxx.xxx*) pont decimális jelöléssel. IPv6 használatakor egyszerűen adjon meg egy 4 számjegyből álló előtagot, és a 128 bites cím jön létre automatikusan az eszköz hálózati illesztőt ezt az előtagot.
* **Alhálózati** – Ez az alhálózati maszk hivatkozik, és be van állítva, a Windows PowerShell felületén keresztül.
* **Átjáró** – Ez a csomópontot, amely nem azonos IP-címterület (alhálózat) belül kommunikálni kísérletek során ez a felület használandó alapértelmezett átjáróként. Az alapértelmezett átjáró kell ugyanazt a címtartományt (alhálózat) az interfésszel IP-cím, alhálózati maszk alapján.
* **Statikus IP-cím** – Ez a mező akkor válik elérhetővé, csak, amíg a DATA 0 konfigurálnia felületet. A műveletek, például a frissítések vagy az eszköz hibaelhárítása szükség lehet az eszköz vezérlő történő közvetlen csatlakoztatásának. A rögzített IP-címet az aktív és az eszközön a passzív vezérlő eléréséhez használható.

> [!NOTE]
> * Ahhoz, hogy a megfelelő műveletet, ellenőrizze a illesztőjének sebessége és a kétirányú eszköz csatolóhoz csatlakoztatott kapcsolón. Kapcsoló felületek vagy egyeztetni kell, vagy Gigabit Ethernet konfigurálni (1000 MB/s) és a kétirányú lehet. Alacsony sebességen vagy fél duplex működő felületek teljesítményproblémák eredményez.
> * Megszakításait és az állásidő minimalizálása érdekében azt javasoljuk, hogy engedélyezi-e portfast egyes kapcsoló portot, amelyet az eszköz iSCSI hálózati illesztő csatlakozik. Ezzel biztosíthatja, hogy hálózati kapcsolat létesíthető gyorsan feladatátvétel esetén.

### <a name="configure-data-0"></a>DATA 0 konfigurálása

DATA 0 alapértelmezés szerint felhő engedélyezve. DATA 0 konfigurálásakor is szükségesek két fix IP-címek, egyet a tartományvezérlők konfigurálása. A fix IP-címek közvetlenül a eszközvezérlők eléréséhez használható, és hasznos az eszközön, szemétgyűjtés megfelelően működjön a frissítések telepítésekor, vagy amikor hozzáfér a tartományvezérlők hibaelhárítás céljából.

A rögzített IP-vezérlők használatával a DATA 0 beállítások panel olyan módon konfigurálhatja újra.

![Hálózati adapter - DATA 0 konfigurálása](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> A vezérlő rögzített IP-címek szolgálnak az eszköz frissítéseinek karbantartásához és a terület-visszanyerést algoritmusok (szemétgyűjtés) megfelelően működjön. Ezért a fix IP-knek irányíthatóknak kell lenniük, és csatlakoztatva kell lenniük az internethez.

### <a name="configure-data-1---data-5"></a>1 - 5 adatok adatok konfigurálása

Az adatok 1 - adatok 5 hálózati felületek, konfigurálhatja a hálózati beállításokat az alábbi képernyőfelvételen látható módon:

![Hálózati illesztők DATA 1 - 5 adatok konfigurálása](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>A felcserélendő vagy IP-cím ismételt hozzárendelése

Jelenleg Ha a tartományvezérlő hálózati csatolóhoz (ugyanarra az eszközre vagy egy másik eszköz a hálózat) által hozzárendelt virtuális IP-címhez, amely használatban van, majd a tartományvezérlő hajt végre feladatátvételt. Ha felcserélése vagy ismételt hozzárendelése a virtuális IP-címek egy adott eszköz hálózati csatoló, eljárást kell követni, egy megfelelő, létrehozhat egy ismétlődő IP-helyzetben.

A következő lépésekkel vagy ismételt hozzárendelése a virtuális IP-címek a hálózati adapterek bármelyikéhez:

#### <a name="to-reassign-ips"></a>IP-címek hozzárendelésének

1. Kapcsolja ki a két felülethez IP-címet.
2. Az IP-címek törlődik, miután az új IP-címek kiosztása a megfelelő felületek.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [az MPIO konfigurálása a StorSimple eszköz](storsimple-8000-configure-mpio-windows-server.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

