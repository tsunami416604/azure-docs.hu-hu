---
title: Adatgyűjtő készülék Azure áttelepítése |} Microsoft Docs
description: A gyűjtő készüléket és konfigurálásának áttekintése.
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: ea2367a6e1facfbe6a36cb145e258491a1c99517
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="collector-appliance"></a>Adatgyűjtő-készülék

[Az Azure áttelepítése](migrate-overview.md) értékelésére a helyszíni munkaterhelések Azure való áttelepítésre. Ez a cikk a gyűjtő készülék használatával kapcsolatos információkat nyújt.



## <a name="overview"></a>Áttekintés

Az Azure-áttelepítése gyűjtő egy egyszerűsített készülék felderítéséhez a helyszíni vCenter környezetben használható. A készülék deríti fel a helyszíni VMware rendszerű gépek, és a rájuk vonatkozó metaadatok küld az Azure áttelepítése szolgáltatás.

A gyűjtő készülék valójában egy OVF, amely az Azure áttelepítése projekt letölthető. 4 mag, 8 GB RAM és 80 GB egy lemeznek VMware virtuális gépek elindítja. A készülék operációs rendszer Windows Server 2012 R2 (64 bites).

A lépéseket követve hozhat létre a gyűjtő ide - [a gyűjtő virtuális gép létrehozása](tutorial-assessment-vmware.md#create-the-collector-vm).

## <a name="collector-communication-diagram"></a>Adatgyűjtő kommunikációs diagramja

![Adatgyűjtő kommunikációs diagramja](./media/tutorial-assessment-vmware/portdiagram.PNG)


| Összetevő      | Kommunikációs cél   | Szükséges port                            | Ok                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| Gyűjtő      | Azure Migrate szolgáltatás | 443-as TCP                                  | Gyűjtő képes kommunikálni a szolgáltatás, az SSL-port 443-as porton keresztül kell lennie. |
| Gyűjtő      | vCenter Server        | Alapértelmezett: 443                             | Kell, hogy a gyűjtő képes kommunikálni a vCenter-kiszolgáló. Alapértelmezés szerint csatlakozik a 443-as vCenter. Ha egy másik porton figyel a vCenter, ezt a portot, a gyűjtő kimenő port elérhetőnek kell lennie |
| Gyűjtő      | RDP|   | TCP 3389 | Ahhoz, hogy a gyűjtő géppé RDP tudja |





## <a name="collector-pre-requisites"></a>Adatgyűjtő-Előfeltételek

A gyűjtő kell átadni néhány előfeltételt ellenőrzi, hogy csatlakozzon az Azure áttelepítése szolgáltatáshoz és a felderített adatokat feltölteni. Ez a cikk ellenőrzi, hogy az egyes az előfeltételeket, és megérteni, hogy miért szükség.

### <a name="internet-connectivity"></a>Internetkapcsolat

A gyűjtő készüléknek meg kell kapcsolódnia kell az internethez, hogy küldje el a felderített számítógépek. A gép kapcsolódhat az internethez, az alábbi két módszer egyikével.

1. Beállíthatja, hogy a gyűjtő közvetlen internetkapcsolattal kell rendelkeznie.
2. Beállíthatja, hogy a gyűjtő proxykiszolgálón keresztül csatlakozni.
    * Ha a proxykiszolgálóhoz hitelesítés szükséges, a felhasználónév és jelszó megadhatja a a kapcsolatbeállításokat.
    * Az IP cím vagy FQDN a proxykiszolgáló az űrlap http://IPaddress vagy http://FQDN kell lennie. Csak a http-proxy használata támogatott.

> [!NOTE]
> A gyűjtő által a HTTPS-alapú proxykiszolgálókat nem támogatottak.

#### <a name="whitelisting-urls-for-internet-connection"></a>Engedélyezett URL-címek internetkapcsolat

Az előfeltétel-ellenőrzés sikeres, ha a gyűjtő csatlakozni tud-e az interneten keresztül a megadott beállításokat. A kapcsolat ellenőrzése a következő táblázat az URL-címek listáját csatlakozva van hitelesítve. Ha bármilyen URL-alapú tűzfal proxy segítségével szabályozhatja a kimenő kapcsolat, mindenképp érdemes engedélyezett URL-címeket az alábbi szükséges:

**URL-cím** | **Cél**  
--- | ---
*.portal.azure.com | Szükséges ellenőrizze a kapcsolatot az Azure-szolgáltatás, és érvényesítésére időszinkronizálást ad ki.

Az ellenőrzés emellett is megpróbálja ellenőrizni a kapcsolatot a következő URL-, de nem sikertelen a jelölőnégyzetet, ha nem érhető el. Az alábbi URL-engedélyezési lista konfigurálása nem kötelező, de kell venni a manuális módszerrel csökkenthető a előfeltétel-ellenőrzést.

**URL-cím** | **Cél**  | **Mi történik, ha nem engedélyezett**
--- | --- | ---
*.oneget.org:443 | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul. | PowerCLI telepítése nem sikerül. A modul manuális telepítése.
*.windows.net:443 | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul. | PowerCLI telepítése nem sikerül. A modul manuális telepítése.
*.windowsazure.com:443 | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul. | PowerCLI telepítése nem sikerül. A modul manuális telepítése.
*.powershellgallery.com:443 | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul. | PowerCLI telepítése nem sikerül. A modul manuális telepítése.
*.msecnd.net:443 | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul. | PowerCLI telepítése nem sikerül. A modul manuális telepítése.
*.visualstudio.com:443 | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul. | PowerCLI telepítése nem sikerül. A modul manuális telepítése.

### <a name="time-is-in-sync-with-the-internet-server"></a>Az internetes kiszolgáló szinkronban-e idő

A gyűjtő győződjön meg arról, a rendszer hitelesíti a kéréseket a internetes kiszolgálót szinkronban kell lennie. A portal.azure.com URL-címet kell lennie a gyűjtő elérhető, hogy a idő érvényesíteni lehessen. Ha a gép nincs szinkronban, az idő az adatgyűjtő virtuális gépen a jelenlegi időpontnál, az alábbiak szerint megfelelően módosítani szeretné:

1. Nyisson meg egy rendszergazdai parancssort, a virtuális Gépen.
1. Ellenőrizze az időzóna, futtassa a w32tm /tz.
1. Szinkronizálja az időt, futtassa a w32tm/resync.

### <a name="collector-service-should-be-running"></a>Gyűjtő szolgáltatásának kell futnia.

Az Azure áttelepítése gyűjtő szolgáltatásának kell futnia a számítógépen. A szolgáltatás automatikusan elindul, amikor a gép elindul. Ha a szolgáltatás nem fut, megkezdheti a *Azure áttelepítése adatgyűjtő* szolgáltatás Vezérlőpulton. A gyűjtő szolgáltatás felelős a vCenter-kiszolgáló csatlakozik, a gép metaadatok és a teljesítmény adatok gyűjtéséhez és a szolgáltatásnak.

### <a name="vmware-powercli-65"></a>VMware PowerCLI 6.5 

A VMware PowerCLI powershell-modul telepítve kell lennie, hogy a gyűjtő képes kommunikálni a vCenter-kiszolgáló és a lekérdezés megadása a gép összes adatát és a teljesítményadatokat. A powershell-modul automatikusan letölti és telepíti a előfeltétel-ellenőrzés. Automatikus letöltés szükséges néhány URL-címek szerepel az engedélyezési listán, vagy ha ez vagy meg kell adnia elérhet által engedélyezett, vagy manuálisan telepíteni a modult.

A modul telepítése manuálisan az alábbi lépéseket követve:

1. Adatgyűjtő internetkapcsolat nélkül a PowerCli telepítéséhez kövesse a lépéseket, a megadott [Ez a hivatkozás](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html) .
2. Egy másik számítógépre, amelyen internet-hozzáféréssel rendelkező, a PowerShell-modul telepítése után másolja a fájlokat VMware.*, hogy a gép az adatgyűjtő géphez.
3. Indítsa újra az előfeltétel-ellenőrzést, és győződjön meg arról, hogy telepítve van-e a PowerCLI.

## <a name="connecting-to-vcenter-server"></a>Csatlakozik a vCenter-kiszolgáló

A gyűjtő kell a vCenter-kiszolgálóhoz csatlakozni és tudja lekérdezni a virtuális gépet, a metaadatok és a teljesítményszámlálók. Ezeket az adatokat a projekt értékelését kiszámításához használt.

1. A vCenter Server eléréséhez az alábbi táblázatban megadott engedélyekkel rendelkező egy csak olvasható fiók használható a felderítés végrehajtásakor. 

    |Tevékenység  |Szükséges szerepkör/fiók  |Engedélyek  |
    |---------|---------|---------|
    |Rendszerre épülő készüléknek gyűjtőészlelést.    | Legalább egy írásvédett felhasználó van szüksége        |Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható         |

2. Csak a megadott vCenter fiók számára elérhető adatközpontok elérhetők a felderítéshez.
3. Meg kell adnia a vCenter FQDN vagy IP-címet a vCenter-kiszolgálóhoz való csatlakozáshoz. Alapértelmezés szerint az csatlakoznak a 443-as porton keresztül. Ha konfigurálta a vCenter a figyelést a port számát, adja meg azt a kiszolgáló címét, a képernyő IPAddress:Port_Number vagy FQDN:Port_Number részeként.
4. A statisztika vcenter Server kell beállítás 3. szint telepítés megkezdése előtt. Ha a szintje alacsonyabb, mint 3, befejeződik, a felderítés, de a tárolási és hálózati teljesítményadatok nem gyűjthetők. A kiértékelés mérete ajánlásai ebben az esetben alapjául Processzor- és teljesítményadatokat, és a lemezek és a hálózati adapterek csak konfigurációs adatokat. [További](./concepts-collector.md) milyen adatokat gyűjt, és milyen hatással van az értékelés.
5. A gyűjtő rendelkeznie kell egy hálózati sor a láthatáron a vCenter-kiszolgálóhoz.

> [!NOTE]
> Csak vCenter Server 5.5, 6.0 és 6.5 hivatalosan támogatott verzióival.

> [!IMPORTANT]
> Azt javasoljuk, hogy a számlálók megfelelően vannak összegyűjtött legnagyobb közös szintjének (3) a statisztika szint beállítása. Ha vCenter egy alacsonyabb szinten adja meg, csak néhány számlálók gyűjtik be teljesen, és a többi értéke 0. Az értékelés majd előfordulhat, hogy megjelenítése nem teljes adatokat. 

### <a name="selecting-the-scope-for-discovery"></a>A felderítési hatókörének megadása kiválasztása

Miután csatlakozott a vCenter, kiválaszthatja a hatókör felderítéséhez. A hatókör kiválasztása deríti fel a megadott vCenter szoftverleltár elérési úton található összes virtuális gépet.

1. A hatókör lehet egy adatközpontban, a mappa vagy ESXi-állomáson. 
2. Egyszerre csak egy hatókör jelölhet ki. Válassza ki további virtuális gépek, végezze el az egyik felderítés, és indítsa újra a felderítési folyamatot az új hatókör.
3. Jelölhet ki, amelynek hatókör *1000-nél kevesebb virtuális gépek*. Ha egy hatókör, amely 1000-nél több virtuális gépe van, a hatókör ossza fel kisebb egységekből mappák létrehozásával szeretné. Ezt követően kell futtassa a független kisebb mappa.

## <a name="specify-migration-project"></a>Adja meg az áttelepítési projekt

Miután a helyszíni vCenter van csatlakoztatva, és egy hatókör lett megadva, mostantól megadhatja a felderítés és az értékelés használandó igénylő áttelepítési projekt részleteit. Adja meg a Projektazonosítónak és a kulcsot, és csatlakozzon.

## <a name="start-discovery-and-view-collection-progress"></a>Indítsa el a felderítés és a nézet gyűjtemény folyamatban

Után elindul a felderítést, a vCenter virtuális gépek felderítése, és a metaadatok és a teljesítmény adatküldést a kiszolgálóra. A folyamat állapota is tájékoztatást a következő azonosítókat:

1. Adatgyűjtő-azonosító: Egyedi Azonosítót arra, hogy az adatgyűjtő géphez. Ez az azonosító nem változtatja meg az adott számítógépen különféle felderítések között. Ezt az Azonosítót hibák esetén is használhatja, ha a probléma jelentéskészítés a Microsoft Support.
2. Munkamenet-azonosító: A futó feladatot egyedi Azonosítóját. A felderítési feladat befejezése után is hivatkoznak ugyanahhoz a munkamenet-azonosító a portálon. Ezt az Azonosítót minden feladat változik. Hibák, esetén is tud jelentéseket a Microsoft Support ezt az Azonosítót.

### <a name="what-data-is-collected"></a>Összegyűjtött adatok?

A feladat felderíti a következő statikus metaadatait a kiválasztott virtuális gépek. 

1. Virtuális gép megjelenített neve (a vCenter)
2. A virtuális gép szoftverleltár elérési útvonala (gazdagép vagy mappa a vCenter)
3. IP-cím
4. MAC-cím
5. Magok, a lemezek, a hálózati adapterek száma
6. RAM mérete
7. És a virtuális gép, lemez és az alábbi táblázatban szereplő hálózati teljesítményszámlálók.

A következő táblázat a teljesítményszámlálók gyűjtése, valamint is felsorolja az értékelési eredmények, amelyek érintettek, ha a számláló nem gyűjtött a program.

|A számláló                                  |Szint    |Eszközönkénti szint  |Értékelés gyakorolt hatás                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|CPU.Usage.average                        | 1       |NA                |Ajánlott Virtuálisgép-méretet és költség                    |
|mem.usage.average                        | 1       |NA                |Ajánlott Virtuálisgép-méretet és költség                    |
|virtualDisk.read.average                 | 2       |2                 |Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet         |
|virtualDisk.write.average                | 2       |2                 |Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet         |
|NET.Received.average                     | 2       |3                 |Virtuális gép mérete és a hálózati költség                        |
|NET.transmitted.average                  | 2       |3                 |Virtuális gép mérete és a hálózati költség                        |

> [!WARNING]
> Ha csak magasabb szintű statisztika, tart naponta a teljesítményszámlálók létrehozásához. Igen azt javasoljuk, hogy a felderítés egy nap után.

### <a name="time-required-to-complete-the-collection"></a>A gyűjtemény befejezéséhez szükséges idő

A gyűjtő csak felderítésére szolgál az adatokról, és elküldi a projektet. A projekt a felderített adatokat jelenik meg a portálon, és értékelés készítése előtt további időt vehet igénybe.

A kijelölt hatókörben lévő virtuális gépek száma alapján, az legfeljebb 15 percet vesz igénybe a statikus metaadatok küldésére a projektet. A statikus metaadatok a portálon elérhetővé válik, lásd a gépet a portálon, és csoportok létrehozása. Egy értékelési nem hozható létre, amíg a feladat befejeződött, és a projekt feldolgozta-e az adatokat. Egyszer a feladatot a gyűjtő befejeződött, is igénybe vehet, legfeljebb egy órával a teljesítményadatokat a portálon elérhető legyen a kijelölt hatókörben lévő virtuális gépek száma alapján.

## <a name="locking-down-the-collector-appliance"></a>A gyűjtő készülék zárolása
Azt javasoljuk, hogy fut a folyamatos Windows-frissítéseket az adatgyűjtő-készüléken. Ha a gyűjtő 45 napig nem frissül, a gyűjtő indul el automatikusan – a gép leállítása. Ha a felderítés fut, a gép nem ki lesz kapcsolva, akkor is, ha a 45 nap időszak lejárt. Indítsa el a felderítési feladat befejeződik, a számítógép ki lesz kapcsolva. Ha a gyűjtő legfeljebb 45 napig használ, azt javasoljuk, hogy a gép frissült, minden alkalommal futó Windows Update.

Javasoljuk továbbá az alábbi lépéseket a készülék biztonságos
1. Ne ossza és rendszergazdai jelszavakat feljegyezte jogosulatlan felekkel.
2. Állítsa le a készülék, ha nincsenek használatban.
3. A készülék a biztonságos hálózati helyezze el.
4. Ha az áttelepítés munkaelem befejeződött, törölje a készülék-példányt. Győződjön meg arról, a lemez (VMDKs) fájlok biztonsági mentése is törli, a lemezek esetleg gyorsítótárazott azokat a vCenter hitelesítőadat.

## <a name="how-to-upgrade-collector"></a>Gyűjtő frissítése

A gyűjtő a petesejtek ismét letöltése nélkül frissítheti a legújabb verzióra.

1. Töltse le a legújabb [frissítési csomag](https://aka.ms/migrate/col/latestupgrade).
2. Győződjön meg arról, hogy a letöltött gyorsjavítás biztonságos, nyissa meg a rendszergazdai parancsablakot, és a ZIP-fájl a kivonat létrehozásához a következő parancsot. A generált kivonatoló meg kell felelnie a kettős kereszttel említett szemben az adott verzió:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    
    (például használati C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.5.zip SHA256)
3. A zip-fájl másolása az Azure adatgyűjtő virtuális gép áttelepítése (adatgyűjtő készülék).
4. Kattintson a jobb gombbal a zip-fájl, és válassza ki az összes kibontása.
5. Kattintson a jobb gombbal a Setup.ps1, és válassza a Futtatás a PowerShell segítségével, és kövesse a képernyőn a frissítés telepítéséhez.

### <a name="list-of-updates"></a>Frissítések listája

#### <a name="upgrade-to-version-1095"></a>Frissítés 1.0.9.5 verzióról

Frissítés verzió 1.0.9.5 letöltési [csomag](https://aka.ms/migrate/col/upgrade_9_5)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | d969ebf3bdacc3952df0310d8891ffdf
SHA1 | f96cc428eaa49d597eb77e51721dec600af19d53
SHA256 | 07c03abaac686faca1e82aef8b80e8ad8eca39067f1f80b4038967be1dc86fa1

## <a name="next-steps"></a>További lépések

[Helyszíni VMware virtuális gépek értékelés beállítása](tutorial-assessment-vmware.md)
