---
title: A StorSimple 8000 sorozatú eszköz konfigurációjának módosítása |} A Microsoft Docs
description: Ismerteti a StorSimple-Eszközkezelő szolgáltatás segítségével konfigurálja újra a StorSimple-eszköz, amely már telepítve lett.
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
ms.openlocfilehash: 727cf523f2a505729377f36738657fc5489134a7
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025990"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>A StorSimple-Eszközkezelő szolgáltatás segítségével a StorSimple-eszköz konfigurációjának módosítása

## <a name="overview"></a>Áttekintés

Az Azure Portalon **eszközbeállítások** című rész a **beállítások** panel, amely a StorSimple eszközön, amely a StorSimple-Eszközkezelő szolgáltatás által kezelt újrakonfigurálhatja az összes eszköz paramétert tartalmaz . Ez az oktatóanyag azt ismerteti, hogyan használhatja a **beállítások** paneljein végezze el a következő eszközszintű feladatokat:

* Módosítsa az eszköz rövid neve
* Eszközbeállítások idő módosítása
* Másodlagos DNS hozzárendelése
* Hálózati adapter módosítása
* Felcserélés vagy IP-cím ismételt hozzárendelése

## <a name="modify-device-friendly-name"></a>Módosítsa az eszköz rövid neve

Az Azure portal használatával módosíthatja az eszköz nevét, és rendelje hozzá egy egyedi rövid nevet a választott. Használja a **általános beállítások** panel az eszközön, az eszköz rövid nevének módosításához. A rövid név bármilyen karaktert tartalmazhat, és a egy legfeljebb 64 karakter hosszú lehet.

> [!NOTE] 
> Csak akkor módosíthatja az eszköz nevét, az Azure Portalon, az eszköz beállításának befejezése előtt. A minimális Eszközbeállítás végrehajtása után az eszköz neve nem módosítható.

![Eszköz neve az általános beállítások](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

A StorSimple-eszköz, amely a StorSimple-Eszközkezelő szolgáltatás csatlakozik hozzá van rendelve egy alapértelmezett nevet. Az alapértelmezett név általában tükrözi az eszköz sorozatszámát. Ha például egy alapértelmezett eszköz neve 15 karakternél hosszabb, 8600-SHX0991003G44HT, például azt jelzi, hogy a következő:

* **8600-as** – azt jelzi, hogy az eszköz típusa.
* **SHX** – azt jelzi, hogy a gyártási hely.
* **0991003** -azt jelzi, hogy egy bizonyos termékben.
* **G44HT**-hozhat létre egyedi sorozatszáma eggyel növeli az utolsó 5 számjegy. Ez egy szekvenciális beállítása nem lehet.

## <a name="modify-device-description"></a>Eszköz leírásának módosítása

Használja a **általános beállítások** panel az eszköz leírásának módosítása az eszközön.

![Az általános beállítások eszköz leírása](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Egy eszköz leírása általában segít azonosítani a tulajdonos és az eszközök fizikai helyét. A Leírás mezőben a 256-nál kevesebb karaktert tartalmazhat.

## <a name="modify-time-settings"></a>Idő beállításainak módosítása

Az eszköz szinkronizálnia kell az időt ahhoz, hogy a tárolási szolgáltató a hitelesítéshez. Használja a **általános beállítások** panelen módosíthatja az eszköz beállításokat az eszközön.

![Az általános beállítások eszköz leírása](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 A legördülő listából válassza ki az időzónát. Legfeljebb két Network Time Protocol (NTP) kiszolgálók is megadhatja:

 - **Elsődleges NTP-kiszolgáló** – a konfigurációs szükség, és van megadva a Windows PowerShell storsimple-höz készült használhatja az eszköz konfigurálása. Megadhatja, hogy a Windows Server alapértelmezett **time.windows.com** az NTP-kiszolgálóval. Az elsődleges NTP-kiszolgálókonfiguráció az Azure Portalon is megtekintheti, de a Windows PowerShell felületét a módosításhoz kell használnia. Használja a `Set-HcsNTPClientServerAddress` parancsmag használatával módosíthatja az eszköz elsődleges NTP-kiszolgálót. További információkért nyissa meg a synxtax [Set-HcsNTPClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) parancsmagot.

- **Másodlagos NTP-kiszolgáló** – a konfigurálása nem kötelező. A portál használatával egy másodlagos NTP-kiszolgálót konfigurálja.

Az NTP-kiszolgáló konfigurálásakor győződjön meg arról, hogy a hálózat lehetővé teszi, hogy az NTP-adatforgalmat az Adatközpont az internethez. Nyilvános NTP-kiszolgáló megadásakor győződjön meg arról, hogy a hálózati tűzfal és egyéb biztonsági eszközök engedélyezésére vannak konfigurálva NTP forgalom áramlását felé és felől a külső hálózattal. Kétirányú NTP forgalom pedig nem engedélyezett, ha egy belső NTP-kiszolgáló (Windows tartományvezérlő biztosítja ezt a funkciót) kell használnia. Ha az eszköz ideje nem lehet szinkronizálni, azt nem lehet képes kommunikálni a felhős társzolgáltatóhoz.

Nyilvános NTP-kiszolgálók listájának megtekintéséhez nyissa meg a [NTP-kiszolgálók webes](http://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Mi történik, ha az eszköz egy másik időzónában van üzembe helyezve?

Ha az eszköz egy másik időzónában van telepítve, az eszköz időzónáját módosítja. Tekintettel arra, hogy a biztonsági mentési szabályzatok használata az eszköz időzónáját, a biztonsági mentési szabályzatok automatikusan módosítsa az új időzónának megfelelően. Nincs szükség felhasználói beavatkozásra.

## <a name="modify-dns-settings"></a>DNS-beállításainak módosítása

Az eszköz próbál meg kommunikálni a tárolási szolgáltató használt DNS-kiszolgáló. Használja a **hálózati beállítások** panel és a konfigurált DNS-beállítások módosítását az eszközön. 

![A hálózati beállításokat a DNS-beállítások](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

A magas rendelkezésre állás érdekében szükségesek az elsődleges, mind a másodlagos DNS-kiszolgálók konfigurálása a kezdeti üzembe helyezése során.

**Elsődleges DNS-kiszolgáló** – a storsimple-höz készült Windows PowerShell segítségével először adja meg a elsődleges DNS-kiszolgáló a kezdeti beállítás során. Az elsődleges DNS-kiszolgáló csak a Windows PowerShell felületéről újra létrehozhatja. Használja a `Set-HcsDNSClientServerAddress` parancsmag használatával módosíthatja az eszköz elsődleges DNS-kiszolgálót. További információkért nyissa meg a synxtax [Set-HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) parancsmagot.

**Másodlagos DNS-kiszolgáló** – az a másodlagos DNS-kiszolgáló módosítani, használja a `Set-HcsDNSClientServerAddress` parancsmagot az eszköz a Windows PowerShell felületén, vagy **hálózati beállítások** panel az Azure Portalon a StorSimple-eszköz.

A másodlagos DNS-kiszolgáló Azure Portalon módosíthatja, hajtsa végre az alábbi lépéseket.

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Eszközök listájából válassza ki, és kattintson az eszköz.

2. Az a **beállítások** panelen lépjen a **eszközbeállítások > hálózati**. Megnyílik a **hálózati beállítások** panelen. Kattintson a **DNS-beállítások** csempére. Módosítsa a másodlagos DNS IP-címét.

    ![Másodlagos DNS-kiszolgáló IP adderss módosítása](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. A parancssávon kattintson **mentése** és amikor a rendszer megerősítést kér, kattintson a **OK**.

    ![Mentés és a módosítások megerősítése](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Hálózati adapter módosítása

Az eszköz hat eszköz hálózati adapterrel rendelkezik, négy, amelyek 1 GbE, és amelyek közül kettő 10 GbE. Ezeket az adaptereket, az adatok 0 – 5 adatok vannak ellátva. DATA 0, 1 adatok, adatok 4 és DATA 5 rendszer 1 GbE, mivel a DATA 2 és DATA 3 10 GbE hálózati adapterek.

Használja a **hálózati beállítások** paneljén konfigurálhatja az egyes használt csatolók közül.

![Hálózati adapterek keresztül a hálózati beállítások konfigurálása](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Magas rendelkezésre állás biztosítása érdekében azt javasoljuk, hogy legalább két iSCSI és a két felhőalapú kapcsolatain az eszközön. Javasolt, de nem szükséges, hogy a használaton kívüli adaptereket kell-e letiltva.

Mindegyik hálózati interfész jelennek meg a következő paraméterekkel:

* **Sebesség** – nem felhasználó által konfigurálható paramétert. DATA 0, 1 adatok, adatok 4 és DATA 5 mindig 1 GbE, mivel a DATA 2 és DATA 3 10 GbE-adapterek.
  
  > [!NOTE]
  > Sebesség és a kétirányú mindig automatikusan-egyeztetését. Nem támogatja a Jumbo-keretek támogatása.
  
* **Csatoló állapota** – felületet is engedélyezhető vagy letiltható. Ha engedélyezve van, az eszköz meg fogja próbálni a felületén. Azt javasoljuk, hogy csak azok az interfészek csatlakozik a hálózathoz és használt engedélyezhető. Tiltsa le minden olyan felületek, amelyek nem használnak.
* **Typ rozhraní** – Ez a paraméter lehetővé teszi, hogy elkülönítse a felhőalapú tárolási forgalom iSCSI forgalmát. Ezt a paramétert a következők egyike lehet:
  
  * **Felhőbarát** – Ha engedélyezve van, az eszköz kommunikáljon a felhő Ez az interfész fogja használni.
  * **az iSCSI-kompatibilis** – Ha engedélyezve van, az eszköz ezen a kapcsolaton fogja használni az iSCSI-gazdagép kommunikálni.
    
    Azt javasoljuk, hogy a felhőalapú tárolási forgalom adatforgalma iSCSI elkülönítése. Azt is vegye figyelembe, ha a gazdagép-eszközként ugyanabban az alhálózatban, nem kell rendelni egy átjáró; azonban ha a gazdagép egy másik alhálózatban, mint az eszköz, meg kell rendelnie egy átjárót.
* **IP-cím** – a hálózati adapterek konfigurálásakor konfigurálnia kell egy virtuális IP-cím (VIP). Ez lehet IPv4 vagy IPv6-alapú vagy mindkettőt. Az IPv4- és IPv6-alapú címcsaládra eszköz hálózati adapterek használata támogatott. Az IPv4 használata esetén adja meg egy 32-bit-es IP-cím (*xxx.xxx.xxx.xxx*) pont-decimális jelöléssel. IPv6 használatakor a egyszerűen adjon meg egy 4 számjegyű előtagot, és a egy 128 bites címet automatikusan az eszköz hálózati adapter adott előtag alapján létrejön.
* **Alhálózat** – Ez az alhálózati maszk hivatkozik, és a Windows PowerShell-felületen keresztül történik.
* **Átjáró** – Ez az az alapértelmezett átjáró, amikor az megpróbál kommunikálni a csomópontok, amelyet nem az azonos IP-címtér (alhálózat) Ez az interfész által használandó. Az alapértelmezett átjáró kell lennie az azonos címtérben (alhálózat) az interfésszel IP-cím, az alhálózati maszk határoz meg.
* **Rögzített IP-cím** – Ez a mező akkor válik elérhetővé, csak a DATA 0 konfigurálása közben felületet. A műveletek, például frissítések vagy az eszköz hibaelhárítása szükség lehet az eszközvezérlő való közvetlen csatlakozáshoz. Mind az aktív és az eszközön a passzív vezérlő rögzített IP-cím használható.

> [!NOTE]
> * Ahhoz, hogy a megfelelő műveletet, ellenőrizze a illesztőjének sebessége és a kapcsolóhoz csatlakozó egyes eszköz felületén a kétirányú. Kapcsoló illesztőkre vagy egyeztetni kell, vagy Gigabit Ethernet konfigurálni (1000 MB/s), és teljes. Sebességű vagy fél duplex módban működő adapterek teljesítményproblémák eredményez.
> * Megszakításait és az állásidő minimalizálása érdekében azt javasoljuk, hogy engedélyezze a portfast az egyes kapcsoló portot, amelyet az eszköz iSCSI hálózati adapter csatlakozik. Ez biztosítja, hogy hálózati is lehet kapcsolódni a gyors feladatátvétel esetén.

### <a name="configure-data-0"></a>Az adatok 0 konfigurálása

DATA 0, felhőszolgáltatásokat használó alapértelmezés szerint. DATA 0 konfigurálásakor is szükségesek két fix IP-címét, egy az egyes tartományvezérlők konfigurálása. A fix IP-címek is használhatók a eszközvezérlők a közvetlen elérés érdekében, és hasznosak lehetnek az eszközön, a szemétgyűjtés megfelelő működéséhez frissítések telepítésekor, vagy amikor hozzáfér a tartományvezérlők problémájának hibaelhárítására használjuk.

A rögzített IP-vezérlők használatával a DATA 0 beállításai panel újra létrehozhatja.

![Hálózati adapter – adat 0 konfigurálása](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> A vezérlőhöz tartozó fix IP-címek szolgálnak az eszköz frissítéseinek karbantartásához és a terület-visszaigénylést algoritmusok (szemétgyűjtés) megfelelően működjön. Ezért a fix IP-knek irányíthatóknak kell lenniük, és csatlakoztatva kell lenniük az internethez.

### <a name="configure-data-1---data-5"></a>1 – 5 adatok adatok konfigurálása

Az adatok, 1 – adatok 5 hálózati adaptereket, konfigurálhatja a hálózati beállításokat az alábbi képernyőképen látható módon:

![Hálózati adapterek adatok 1 – 5 adatok konfigurálása](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Felcserélés vagy IP-cím ismételt hozzárendelése

Jelenleg Ha bármely hálózati adaptert a tartományvezérlő (által ugyanazon az eszközön vagy egy másik eszköz a hálózatban) hozzárendelt virtuális IP-címhez, amely használatban van, majd a vezérlő feladatátvételt hajt végre. Ha cserélhetők fel, vagy egy eszköz hálózati adapter virtuális IP-cím ismételt hozzárendelése, akkor létrehozhat egy duplikált IP helyzet egy megfelelő eljárást kell követnie.

A következő lépésekkel cseréje vagy újbóli hozzárendelése a virtuális IP-cím a hálózati adapterek valamelyikét:

#### <a name="to-reassign-ips"></a>IP-cím ismételt hozzárendelése

1. Törölje mindkét felületek IP-címét.
2. Az IP-címek törlődik, miután az új IP-címek hozzárendelése a megfelelő adaptereket.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [az MPIO konfigurálása a StorSimple eszköz](storsimple-8000-configure-mpio-windows-server.md).
* Ismerje meg, hogyan [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

