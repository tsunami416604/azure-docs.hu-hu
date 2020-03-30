---
title: A StorSimple 8000 sorozatú eszközkonfiguráció módosítása | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy a StorSimple Eszközkezelő szolgáltatás használatával újrakonfigurálható egy már telepített StorSimple-eszköz.
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
ms.openlocfilehash: 774f5a73a5fc30352698c0af0c279fbbe488c480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267689"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>A StorSimple Eszközkezelő szolgáltatás használata a StorSimple eszköz konfigurációjának módosításához

## <a name="overview"></a>Áttekintés

Az Azure Portal **Eszközbeállítások** szakasz a **Beállítások** panel tartalmazza az összes eszköz paramétereket, amelyek újrakonfigurálhatók a StorSimple-eszköz, amely egy StorSimple Eszközkezelő szolgáltatás által felügyelt. Ez az oktatóanyag bemutatja, hogyan **használhatja** a Beállítások panelt a következő eszközszintű feladatok végrehajtására:

* Eszközbarát név módosítása
* Eszközidő-beállításainak módosítása
* Másodlagos DNS hozzárendelése
* Hálózati adapterek módosítása
* IP-k cseréje vagy újbóli hozzárendelése

## <a name="modify-device-friendly-name"></a>Eszközbarát név módosítása

Az Azure Portalsegítségével módosíthatja az eszköz nevét, és hozzárendelheti egy egyedi rövid nevet, amelyet választott. Az **eszköz általános beállítási** paneljén módosíthatja az eszköz rövid nevét. A rövid név bármilyen karaktert tartalmazhat, és legfeljebb 64 karakter hosszú lehet.

> [!NOTE] 
> Az eszköz nevét csak az Azure Portalon módosíthatja, mielőtt az eszköz beállítása befejeződött. A minimális eszközbeállítás befejezése után nem módosíthatja az eszköz nevét.

![Eszköz neve az Általános beállításokban](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

A StorSimple eszköz, amely csatlakozik a StorSimple Device Manager szolgáltatáshoz van rendelve egy alapértelmezett nevet. Az alapértelmezett név általában az eszköz sorozatszámát tükrözi. Egy 15 karakter hosszú alapértelmezett eszköznév( például 8600-SHX0991003G44HT) például a következőket jelzi:

* **8600** – Az eszköz modelljét jelzi.
* **SHX** – a gyártási helyet jelzi.
* **0991003** - Egy adott terméket jelöl.
* **G44HT**– Az utolsó 5 számjegy egyedi sorozatszámok létrehozásához növekszik. Lehet, hogy ez nem szekvenciális készlet.

## <a name="modify-device-description"></a>Eszköz leírásának módosítása

Az eszköz leírásának módosításához használja az általános **beállítások** panelt az eszközön.

![Az eszköz leírása az Általános beállításokban](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Az eszköz leírása általában segít azonosítani a tulajdonost és az eszköz fizikai helyét. A megnevezésmező 256 karakternél rövidebb lehet.

## <a name="modify-time-settings"></a>Időbeállítások módosítása

Az eszköznek szinkronizálnia kell az időt ahhoz, hogy hitelesíthesse magát a felhőalapú tárhelyszolgáltatónál. Az eszköz időbeállításainak módosításához használja az általános **beállítások** panelt az eszközön.

![Az eszköz leírása az Általános beállításokban](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Válassza ki az időzónát a legördülő listából. Legfeljebb két NTP-kiszolgálót adhat meg:

 - **Elsődleges NTP-kiszolgáló** – A konfiguráció szükséges, és meg van adva, ha a Windows PowerShell for StorSimple az eszköz konfigurálásához. Az alapértelmezett Windows Server **time.windows.com** ntp-kiszolgálóként is megadhatja. Megtekintheti az elsődleges NTP-kiszolgáló konfigurációját az Azure Portalon keresztül, de a Windows PowerShell-felületet kell használnia annak módosításához. A `Set-HcsNTPClientServerAddress` parancsmag segítségével módosítsa az eszköz elsődleges NTP-kiszolgálóját. További információt a [Set-HcsNTPClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) parancsmag szintaxisa című lapban talál.

- **Másodlagos NTP-kiszolgáló** – A konfiguráció megadása nem kötelező. A portál segítségével konfigurálhat egy másodlagos NTP-kiszolgálót.

Az NTP-kiszolgáló konfigurálásakor győződjön meg arról, hogy a hálózat lehetővé teszi, hogy az NTP-forgalom az adatközpontból az internetre továbbítson. Nyilvános NTP-kiszolgáló megadásakor győződjön meg arról, hogy a hálózati tűzfalak és más biztonsági eszközök úgy vannak beállítva, hogy az NTP-forgalom a külső hálózatra és a külső hálózatról is bejárható legyen. Ha a kétirányú NTP-forgalom nem engedélyezett, belső NTP-kiszolgálót kell használnia (ezt a funkciót Windows-tartományvezérlő biztosítja). Ha az eszköz nem tudja szinkronizálni az időt, előfordulhat, hogy nem tud kommunikálni a felhőalapú tárhelyszolgáltatóval.

A nyilvános NTP-kiszolgálók listájának megtekintéséhez nyissa meg az [NTP-kiszolgálók webwebhelyét.](https://support.ntp.org/bin/view/Servers/WebHome)

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Mi történik, ha az eszköz egy másik időzónában van telepítve?

Ha az eszköz egy másik időzónában van telepítve, az eszköz időzónája megváltozik. Mivel az összes biztonsági mentési házirend az eszköz időzónáját használja, a biztonsági mentési házirendek automatikusan módosulnak az új időzónának megfelelően. Felhasználói beavatkozásra nincs szükség.

## <a name="modify-dns-settings"></a>DNS-beállítások módosítása

A DNS-kiszolgáló akkor használatos, amikor az eszköz kommunikálni próbál a felhőalapú tárhelyszolgáltatóval. Az eszköz **Hálózati beállítások** paneljén megtekintheti és módosíthatja a konfigurált DNS-beállításokat. 

![DNS-beállítások a Hálózati beállítások ban](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

A magas rendelkezésre állás érdekében az elsődleges és a másodlagos DNS-kiszolgálókat is konfigurálnia kell a kezdeti eszköztelepítés során.

**Elsődleges DNS-kiszolgáló** – A Windows PowerShell for StorSimple segítségével adja meg először az elsődleges DNS-kiszolgálót a kezdeti telepítés során. Az elsődleges DNS-kiszolgálócsak a Windows PowerShell-felületen keresztül konfigurálható újra. A `Set-HcsDNSClientServerAddress` parancsmag segítségével módosítsa az eszköz elsődleges DNS-kiszolgálóját. További információt a [Set-HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) parancsmag szintaxisa című lapban talál.

**Másodlagos DNS-kiszolgáló** – A másodlagos DNS-kiszolgáló módosításához használja a `Set-HcsDNSClientServerAddress` parancsmalapot az eszköz Windows PowerShell-felületén vagy a StorSimple-eszköz hálózati **beállítások** paneljén az Azure Portalon.

A másodlagos DNS-kiszolgáló módosítása az Azure Portalon hajtsa végre a következő lépéseket.

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Az eszközök listájában jelölje ki és kattintson az eszközre.

2. A **Beállítások** panelen nyissa meg **az Eszközbeállítások > Hálózat**lehetőséget. Ezzel megnyitja a **Hálózati beállítások** panelt. Kattintson **a DNS-beállítások** csempére. Módosítsa a másodlagos DNS-kiszolgáló IP-címét.

    ![Másodlagos DNS-kiszolgáló IP-címének módosítása](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. A parancssávon kattintson a **Mentés** gombra, és amikor megerősítést kér, kattintson az **OK**gombra.

    ![Módosítások mentése és megerősítése](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Hálózati adapterek módosítása

A készülék hat eszközhálózati interfésszel rendelkezik, amelyek közül négy 1 GbE, kettő pedig 10 GbE. Ezek a felületek A DATA 0 – DATA 5 címkével vannak ellátva. A DATA 0, DATA 1, DATA 4 és DATA 5 1 GbE, míg a DATA 2 és a DATA 3 10 GbE hálózati interfész.

A **Hálózati beállítások** panelen konfigurálhatja a használandó összeköttetéseket.

![Hálózati adapterek konfigurálása hálózati beállításokkal](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

A magas rendelkezésre állás biztosítása érdekében azt javasoljuk, hogy legalább két iSCSI-kapcsolattal és két felhőalapú kapcsolattal rendelkezzen az eszközön. Javasoljuk, de nem szükséges a nem használt összeköttetések letiltása.

Minden hálózati adapterhez a következő paraméterek jelennek meg:

* **Sebesség** – Nem felhasználó által konfigurálható paraméter. A DATA 0, DATA 1, DATA 4 és DATA 5 mindig 1 GbE, míg a DATA 2 és a DATA 3 10 GbE interfész.
  
  > [!NOTE]
  > A sebesség és a kétoldalas nyomtatás mindig automatikusan egyeztetésre került. Jumbo keretek nem támogatottak.
  
* **Kapcsolat állapota** – Egy kapcsolat engedélyezhető vagy letiltható. Ha engedélyezve van, az eszköz megpróbálja használni a kapcsolatot. Azt javasoljuk, hogy csak a hálózathoz csatlakoztatott és használt összeköttetések legyenek engedélyezve. Tiltsa le a nem használt felületeket.
* **Kapcsolat típusa** – Ez a paraméter lehetővé teszi az iSCSI-forgalom elkülönítését a felhőalapú tárolási forgalomtól. Ez a paraméter a következők egyike lehet:
  
  * **Felhő engedélyezve** – ha engedélyezve van, az eszköz fogja használni ezt a felületet kommunikálni a felhő.
  * **iSCSI engedélyezve** – ha engedélyezve van, az eszköz ezt az összeköttetést fogja használni az iSCSI-állomással való kommunikációhoz.
    
    Azt javasoljuk, hogy különítse el az iSCSI-forgalmat a felhőbeli tárolási forgalomtól. Ha a gazdagép ugyanazon az alhálózaton belül van, mint az eszköz, nem kell átjárót hozzárendelnie; ha azonban a gazdagép az eszköztől eltérő alhálózatban van, átjárót kell hozzárendelnie.
* **IP-cím** – A hálózati adapterek konfigurálásakor virtuális IP-címet (VIP) kell konfigurálnia. Ez lehet IPv4 vagy IPv6 vagy mindkettő. Az eszköz hálózati csatolói mind az IPv4, mind az IPv6-címcsaládok támogatottak. Az IPv4 használataesetén adjon meg egy 32 bites IP-címet (*xxx.xxx.xxx.xxx)* pont-decimális jelöléssel. Az IPv6 használatakor egyszerűen adja meg a 4 jegyű előtagot, és az adott előtag alapján automatikusan létrehoz egy 128 bites címet az eszköz hálózati illesztőjéhez.
* **Alhálózat** – Ez az alhálózati maszkra vonatkozik, és a Windows PowerShell-felületen keresztül van konfigurálva.
* **Átjáró** – Ez az alapértelmezett átjáró, amelyet ez a kapcsolat használ, amikor olyan csomópontokkal próbál kommunikálni, amelyek nem ugyanazon az IP-címterületen (alhálózaton) vannak. Az alapértelmezett átjárónak ugyanabban a címtérben (alhálózatban) kell lennie, mint az alhálózati maszk által meghatározott ip-címnek.
* **Rögzített IP-cím** – Ez a mező csak a DATA 0 kapcsolat konfigurálása közben érhető el. Az olyan műveletekhez, mint például a frissítések vagy az eszköz hibaelhárítása, előfordulhat, hogy közvetlenül az eszközvezérlőhöz kell csatlakoznia. A rögzített IP-cím az eszköz aktív és passzív vezérlőjének elérésére is használható.

> [!NOTE]
> * A megfelelő működés biztosítása érdekében ellenőrizze a kapcsolat sebességét és a kétoldalas nyomtatást azon a kapcsolón, amelyhez az egyes eszközillesztők csatlakoztatva vannak. A kapcsolóösszeköttetéseknek vagy egyeztetniük kell a Gigabit Ethernet (1000 Mbps) kapcsolattal, vagy konfigurálniuk kell őket, és teljes kétirányúak kell lenniük. A lassabb vagy váltakozó kétirányú interfészek teljesítményproblémákat eredményeznek.
> * A fennakadások és az állásidő minimalizálása érdekében azt javasoljuk, hogy engedélyezze a portfast ot minden olyan kapcsolóporton, amelyhez az eszköz iSCSI hálózati illesztője csatlakozni fog. Ez biztosítja, hogy a hálózati kapcsolat gyorsan létre, ha a feladatátvétel.

### <a name="configure-data-0"></a>DATA 0 konfigurálása

A DATA 0 alapértelmezés szerint felhőalapú. A DATA 0 konfigurálásakor két rögzített IP-címet is be kell állítania, mindegyik vezérlőhöz egyet. Ezek a rögzített IP-címek az eszközvezérlők közvetlen elérésére használhatók, és akkor hasznosak, ha frissítéseket telepít az eszközre, a szemétgyűjtés megfelelő működéséhez, vagy a vezérlők hibaelhárítás céljából történő eléréséhez.

A rögzített IP-vezérlőket a DATA 0 beállítások panelen keresztül konfigurálhatja újra.

![Hálózati adapter konfigurálása - DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> A vezérlő rögzített IP-címei az eszköz frissítéseinek karbantartására és a helyvisszanyerési algoritmusok (szemétgyűjtés) megfelelő működésére szolgálnak. Ezért a fix IP-knek irányíthatóknak kell lenniük, és csatlakoztatva kell lenniük az internethez.

### <a name="configure-data-1---data-5"></a>DATA 1 konfigurálása - DATA 5

A DATA 1 - DATA 5 hálózati interfészek esetében az összes hálózati beállítást az alábbi képernyőképen látható módon állíthatja be:

![Hálózati interfészek konfigurálása DATA 1 - DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>IP-k cseréje vagy újbóli hozzárendelése

Jelenleg, ha a vezérlő bármely hálózati illesztője olyan VIRTUÁLIS IP-címhez van rendelve, amely használatban van (ugyanaz az eszköz vagy a hálózat egy másik eszköze), akkor a vezérlő feladatátvételt kap. Ha ip-címeket cserél vagy rendel át egy eszköz hálózati illesztőjéhez, a megfelelő eljárást kell követnie, mivel ismétlődő IP-helyzetet hozhat létre.

Hajtsa végre az alábbi lépéseket a VIP-k cseréjéhez vagy hozzárendeléséhez bármelyik hálózati adapterhez:

#### <a name="to-reassign-ips"></a>IP-k újbóli hozzárendelése

1. Törölje mindkét kapcsolat IP-címét.
2. Az IP-címek törlése után rendelje hozzá az új IP-címeket a megfelelő összeköttetésekhez.

## <a name="next-steps"></a>További lépések

* További információ a Rról, hogyan [állíthat be többpiót a StorSimple eszközhöz.](storsimple-8000-configure-mpio-windows-server.md)
* Ismerje meg, hogyan [használhatja a StorSimple Eszközkezelő szolgáltatást a StorSimple-eszköz felügyeletére.](storsimple-8000-manager-service-administration.md)

