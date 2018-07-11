---
title: Az Azure Migrate collector berendezést |} A Microsoft Docs
description: A gyűjtőberendezés és konfigurálásának áttekintése.
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: 8cc5863489c58a0603d8aa464911031a22dba8f3
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951039"
---
# <a name="collector-appliance"></a>Gyűjtőberendezés

[Az Azure Migrate](migrate-overview.md) felméri a helyszíni számítási feladatokat az Azure-ba való migrálásra. Ez a cikk ismerteti a gyűjtőberendezés használata.



## <a name="overview"></a>Áttekintés

Az Azure Migrate Collector egy egyszerűsített berendezés, amely a helyszíni vCenter-környezet felderítése használható. A berendezés felderíti a helyszíni VMware gépeket, és azokkal kapcsolatos metaadatokat továbbítja az Azure Migrate szolgáltatásnak.

A gyűjtőberendezés egy OVF, amely innen tölthető le: az Azure Migrate-projekt. Elindítja a 4 mag, 8 GB RAM-MAL és 80 GB egy lemezt a VMware virtuális gépeket. A berendezés operációs rendszer Windows Server 2012 R2 (64 bites).

A gyűjtő a lépéseket követve hozhat létre ide - [a gyűjtő virtuális gép létrehozása](tutorial-assessment-vmware.md#create-the-collector-vm).

## <a name="collector-communication-diagram"></a>Gyűjtő kommunikációs diagramja

![Gyűjtő kommunikációs diagramja](./media/tutorial-assessment-vmware/portdiagram.PNG)


| Összetevő      | Kommunikációs cél   | Szükséges port                            | Ok                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| Gyűjtő      | Azure Migrate szolgáltatás | 443-as TCP                                  | Gyűjtő képes kommunikálni a szolgáltatás a 443-as SSL-porton keresztül kell lennie. |
| Gyűjtő      | vCenter Server        | 443-as alapértelmezett                             | Lehet, hogy a gyűjtő képes kommunikálni a vCenter-kiszolgáló. Alapértelmezés szerint csatlakozik a vCenter a 443-as porton. Ha a vcenter-kiszolgáló egy másik porton figyel, ezt a portot a gyűjtő kimenő portként elérhetőnek kell lennie |
| Gyűjtő      | RDP|   | 3389-ES TCP | Az adatgyűjtő géphez RDP tudni kell |





## <a name="collector-pre-requisites"></a>Gyűjtő Előfeltételek

A gyűjtő kell átadni annak biztosítása érdekében képes csatlakozni az Azure Migrate szolgáltatás és a felderített adatokat feltölteni néhány előfeltételek ellenőrzését. Ez a cikk úgy tűnik, minden egyes az előfeltételeket, és miért van szükség.

### <a name="internet-connectivity"></a>Internetkapcsolat

A gyűjtőberendezés csatlakoztatva kell lennie az internethez, hogy küldje el a felderített gépek. A gép csatlakozhat az internethez az alábbi két módszer egyikét.

1. Beállíthatja, hogy a gyűjtő közvetlen internetkapcsolattal kell rendelkeznie.
2. Beállíthatja, hogy a gyűjtő egy proxykiszolgáló-n keresztül csatlakozik.
    * Ha a proxykiszolgáló hitelesítést igényel, a felhasználónevet és jelszót megadhatja a kapcsolati beállításaiban.
    * Az IP cím vagy FQDN a proxykiszolgáló kell lennie a képernyő http://IPaddress vagy http://FQDN. Csak a http-proxy használata támogatott.

> [!NOTE]
> A gyűjtő által a HTTPS-alapú proxykiszolgálók nem támogatottak.

#### <a name="whitelisting-urls-for-internet-connection"></a>Engedélyezési URL-címek internetkapcsolat

Az előfeltételként ellenőrzése akkor sikeres, ha a gyűjtő képes csatlakozni az interneten keresztül a megadott beállításokat. A kapcsolat-ellenőrzés csatlakozik egy URL-címek listája az alábbi táblázatban ismertetett érvényességét. Ha bármely URL-alapú tűzfalproxyt a kimenő kapcsolat szabályozásához, ügyeljen arra, hogy az engedélyezési listára ezeket a szükséges URL-címeket használ:

**URL-cím** | **Cél**  
--- | ---
*.portal.azure.com | Szükséges ellenőrizze a kapcsolatot az Azure-szolgáltatások, és az időszinkronizálás ellenőrzéséhez bocsát ki.

Az ellenőrzés ezenkívül is megkísérli a következő URL-címekkel való kapcsolat ellenőrzésére, de az ellenőrzés nem hiúsul meg, ha nem érhető el. A következő URL-címek engedélyezési lista konfigurálása nem kötelező, de csökkentése érdekében az előfeltételként ellenőrzés manuális lépéseket kell állítania.

**URL-cím** | **Cél**  | **Mi történik, ha ezt nem engedélyezett**
--- | --- | ---
*.oneget.org:443 | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul. | A PowerCLI telepítése nem sikerül. Telepítse manuálisan a modult.
*.windows.net:443 | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul. | A PowerCLI telepítése nem sikerül. Telepítse manuálisan a modult.
*.windowsazure.com:443 | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul. | A PowerCLI telepítése nem sikerül. Telepítse manuálisan a modult.
*. powershellgallery.com:443 | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul. | A PowerCLI telepítése nem sikerül. Telepítse manuálisan a modult.
*.msecnd.net:443 | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul. | A PowerCLI telepítése nem sikerül. Telepítse manuálisan a modult.
*.visualstudio.com:443 | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul. | A PowerCLI telepítése nem sikerül. Telepítse manuálisan a modult.

### <a name="time-is-in-sync-with-the-internet-server"></a>Az internet server szinkronban legyen ideje

A gyűjtő annak érdekében, hogy a rendszer hitelesíti a kérelmeket a szolgáltatás internetes időkiszolgálóval szinkronizálva kell lenniük. A portal.azure.com URL-cím kell a gyűjtő érhető el, hogy az idő érvényesíthető legyen. Ha a gép nincs szinkronizálva, az idő a gyűjtő virtuális gép az aktuális idő, a következő megfelelően módosítani szeretné:

1. Nyisson meg egy rendszergazdai jogú parancssort a virtuális gépen.
1. Ellenőrizze az időzónát, futtassa a w32tm /tz.
1. Az idő szinkronizálása w32tm/resync futtassa.

### <a name="collector-service-should-be-running"></a>Gyűjtő szolgáltatásának kell futnia.

Az Azure Migrate Collector szolgáltatás a számítógépen kell futnia. Ez a szolgáltatás a számítógép indításakor automatikusan elindul. Ha a szolgáltatás nem fut, elkezdheti a *az Azure Migrate Collector* szolgáltatást a Vezérlőpult használatával. A gyűjtő szolgáltatás nem csatlakozik a vCenter-kiszolgáló, a metaadatok és a teljesítmény számítógépadatok gyűjtése, és küldje el a szolgáltatás felelős.

### <a name="vmware-powercli-65"></a>A VMware PowerCLI 6.5

A VMware powercli-t a powershell modul telepítve kell lennie, hogy a gyűjtő képes kommunikálni a vCenter-kiszolgáló és a gép részleteinek és a teljesítményadatok lekérdezése. A powershell-modul automatikus letöltését és telepítését az előfeltételként ellenőrzés részeként. Automatikus letöltése néhány URL-címek szerepel az engedélyezési listán, vagy ha ez vagy meg kell adnia igényel hozzáférést engedélyezésével őket, vagy manuálisan telepíti a modult.

A modul az alábbi lépésekkel manuális telepítése:

1. Telepítse a PowerCli-gyűjtőn internetkapcsolat nélkül, kövesse lépéseket [ezt a hivatkozást](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html) .
2. Egy másik számítógépen, amely internet-hozzáféréssel rendelkező, a PowerShell-modul telepítése után másolja a fájlokat VMware.* arról a gépről a naplógyűjtő gépen.
3. Indítsa újra az Előfeltételek ellenőrzéséről, és győződjön meg arról, hogy a PowerCLI telepítve van-e.

## <a name="connecting-to-vcenter-server"></a>Csatlakozik a vCenter-kiszolgáló

A gyűjtő kell csatlakozni a vCenter-kiszolgáló és a lekérdezésére van a virtuális gépek, a metaadatok és a teljesítményszámlálók. Ezeket az adatokat a projekt által használt értékelés kiszámítása.

1. Szeretne csatlakozni a vCenter-kiszolgáló, az alábbi táblázatban ismertetett engedélyekkel rendelkező egy csak olvasható fiók használható a felderítés.

    |Tevékenység  |Szükséges szerepkör/fiók  |Engedélyek  |
    |---------|---------|---------|
    |Gyűjtő berendezés alapú felderítés    | Legalább egy olvasási jogosultsággal rendelkező felhasználó van szüksége        |Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható         |

2. A felderítés csak a megadott vCenter fiók számára elérhető adatközpontok érhetők el.
3. Meg kell adnia a vcenter-kiszolgáló teljes Tartományneve vagy IP-a vCenter-kiszolgálóhoz való csatlakozáshoz. Alapértelmezés szerint kapcsolatot létesít a 443-as porton keresztül. Ha konfigurálta a vcenter-kiszolgáló egy másik portszámot figyelni, megadhatja a kiszolgáló címét, a képernyő IPAddress:Port_Number vagy FQDN:Port_Number részeként.
4. 3. szintre kell állítani a vCenter Server statisztikai beállításait, telepítés megkezdése előtt. Ha szintje alacsonyabb, mint 3, a felderítés befejeződik, de a tárolási és hálózati teljesítményadatok nem gyűjthetők. Az értékelés méretezési javaslatokat ebben az esetben alapjául Processzor- és teljesítményadatokat, és a lemez és a hálózati adapterek csak konfigurációs adatait. [További információ](./concepts-collector.md) gyűjtött adatokat és milyen hatással van az értékelést.
5. A gyűjtő rendelkeznie kell egy hálózati üzemel a vCenter-kiszolgálóhoz.

> [!NOTE]
> Csak vCenter Server 5.5-ös, 6.0-s és 6.5-ös hivatalosan támogatja.

> [!IMPORTANT]
> Azt javasoljuk, hogy a legnagyobb közös szint (3) a statisztikai szint beállítása úgy, hogy az összes számláló gyűjtött megfelelően. VCenter egy alacsonyabb szinten van, ha csak néhány számlálókat a többi állítsa 0-ra teljesen, előfordulhat, hogy gyűjti. Az értékelés majd előfordulhat, hogy megjelenítése nem teljes adatokat.

### <a name="selecting-the-scope-for-discovery"></a>A felderítési hatókör kiválasztása

Miután csatlakozott a vcenter-kiszolgáló, kiválaszthatja a felderítési hatókör. A hatókör kiválasztása felderíti az összes virtuális gépet a megadott vCenter-készlet elérési útról.

1. A hatókör egy adatközpontban, egy mappát vagy egy ESXi-gazdagép is lehet.
2. Egyszerre csak egy hatókör választhatja. Válassza ki a virtuális gépek, végezze el az egyik felderítés, és indítsa újra a felderítési folyamatot az új hatókört.
3. Csak válassza ki, amelynek hatókör *kisebb, mint 1500 virtuális gépek*.

## <a name="specify-migration-project"></a>Adja meg a migrálási projektet

Miután a helyszíni vcenter-kiszolgáló csatlakoztatva van, és a hatókör meg van adva, mostantól megadhatja a migrálási projekt részletei a felderítés és értékelés használni kíván. Adja meg a projekt Azonosítóját és kulcsát, és csatlakozzon.

## <a name="start-discovery-and-view-collection-progress"></a>Felderítés és a nézet az adatgyűjtési folyamat indítása

Miután elindult a felderítést, a vCenter virtuális gépeket derít, és a metaadatok és a teljesítmény adatokat küld a kiszolgáló a. A folyamat állapota is tájékoztatja, hogy a következő azonosítóval:

1. Gyűjtőazonosító: Egy egyedi azonosítója, amely az adatgyűjtő géphez van megadva. Ez az azonosító nem változik egy adott gép különféle felderítések között. Ezt az Azonosítót hibák esetén is használhatja, ha a probléma Support a jelentéskészítés.
2. Munkamenet-azonosító: Egy egyedi Azonosítót a futó feladatot. Az ugyanazon munkamenet-azonosító a portálon a felderítési feladat befejezése után is vonatkoznak. Ezt az Azonosítót minden feladat változik. Hibák esetén a Microsoft Support jelentheti a ezt az Azonosítót.

### <a name="what-data-is-collected"></a>A gyűjtött adatokat?

A feladat felderíti a kiválasztott virtuális gépek következő statikus metaadatait.

1. Virtuális gép megjelenített neve (a vCenter)
2. Virtuális gép szoftverleltár elérési útvonala (gazdagép/mappa a vCenter)
3. IP-cím
4. MAC-cím
5. Operációs rendszer
5. Magok, lemezek, hálózati adapterek száma
6. Memória mérete, a lemezméretek
7. És a virtuális gép, lemez és az alábbi táblázatban felsorolt hálózati teljesítményszámlálók.

Az alábbi táblázat a gyűjtött, és felsorolja az értékelések eredményeinek, ha egy adott teljesítményszámláló gyűjtése nem érintett teljesítményszámlálók.

|Számláló                                  |Szint    |Az eszközszintű szint  |Értékelés gyakorolt hatás                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|CPU.Usage.average                        | 1       |NA                |Javasolt Virtuálisgép-méretet és költség                    |
|mem.usage.average                        | 1       |NA                |Javasolt Virtuálisgép-méretet és költség                    |
|virtualDisk.read.average                 | 2       |2                 |Lemez mérete, a tárolási költségek és a Virtuálisgép-méret         |
|virtualDisk.write.average                | 2       |2                 |Lemez mérete, a tárolási költségek és a Virtuálisgép-méret         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Lemez mérete, a tárolási költségek és a Virtuálisgép-méret         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Lemez mérete, a tárolási költségek és a Virtuálisgép-méret         |
|NET.Received.average                     | 2       |3                 |Virtuális gép mérete és a hálózati költség                        |
|NET.transmitted.average                  | 2       |3                 |Virtuális gép mérete és a hálózati költség                        |

> [!WARNING]
> Csak statisztikai magasabb szintű állított be, ha tart naponta létrehozni a teljesítményszámlálókat. Ezért javasoljuk, hogy a felderítés futtatásakor egy nap elteltével.

### <a name="time-required-to-complete-the-collection"></a>A gyűjtemény végrehajtásához szükséges idő

A gyűjtő csak a számítógépadatok deríti fel, és elküldi a projekt. A projekt a detektált adatok a portálon megjelenített és értékelését hozza létre megkezdése előtt további időt is igénybe vehet.

A kijelölt hatókörben lévő virtuális gépek száma szerint, legfeljebb 15 percig tart a statikus metaadatokat küldhet a projekt. A statikus metaadatok a portálon érhető el, ha a portálon található gépek listáját, és csoportok létrehozásának megkezdéséhez. Egy értékelés nem hozható létre, amíg a feladat befejeződött, és a projekt feldolgozta-e az adatokat. Miután a feladat befejeződött, a gyűjtő, is igénybe vehet legfeljebb egy órányi a teljesítményadatokat a portálon elérhető legyen a kijelölt hatókörben lévő virtuális gépek száma alapján.

## <a name="locking-down-the-collector-appliance"></a>A gyűjtőberendezés sémákra
Azt javasoljuk, hogy fut a gyűjtőberendezés folyamatos Windows-frissítéseket. Ha a gyűjtő nem frissül, 60 napig, a gyűjtő indul el automatikusan – a gép leállítása. Ha a felderítést fut, a gép nem ki lesz kapcsolva, akkor is, ha a 60 napos időszak lejárt. Indítsa el a felderítési feladat befejeződik, a számítógép ki lesz kapcsolva. Ha a gyűjtő legalább 45 napig használ, javasoljuk a gép frissítés: minden alkalommal futó Windows Update gondoskodik.

Javasoljuk továbbá az alábbi lépéseket a berendezés biztonságossá tétele
1. Ne ossza és rendszergazdai jelszavak feljegyezte jogosulatlan személyekkel.
2. Állítsa le a készülék, amikor nincs használatban.
3. Egy biztonságos hálózaton a készülék helyét.
4. A migrálás munkahelyi befejeződése után törölje a berendezés-példány. Győződjön meg arról, is törölni a lemez háttér-fájlok (vmdk-inak), mivel a lemezek rendelkezhetnek vCenter hitelesítő adatokat, azokat a gyorsítótárba.

## <a name="how-to-upgrade-collector"></a>Gyűjtő frissítése

Az OVA ismét letöltése nélkül frissítheti a gyűjtő a legújabb verzióra.

1. Töltse le a legújabb [verziófrissítő csomagjának](https://aka.ms/migrate/col/upgrade_9_11) (1.0.9.11 verzió).
2. Győződjön meg arról, hogy a letöltött gyorsjavítás biztonságos, nyissa meg a rendszergazdai parancsablakot, és futtassa a következő parancsot a ZIP-fájl kivonatának. A létrehozott kivonatnak egyeznie kell az említett verzió elleni kivonat:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (gyakorlati példa: C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.7.zip SHA256)
3. Másolja a zip-fájlt az Azure Migrate gyűjtő virtuális gép (gyűjtőberendezés).
4. Kattintson a jobb gombbal a zip-fájlt, és válassza ki az összes kibontása.
5. Kattintson a jobb gombbal a Setup.ps1, és válassza a Futtatás a PowerShell-lel, és kövesse a képernyőn a frissítés telepítéséhez.

### <a name="list-of-updates"></a>Frissítések listája

#### <a name="upgrade-to-version-10911"></a>1.0.9.11 verzióra frissítése

Ujjlenyomat-frissítéshez értékek [1.0.9.11 csomag](https://aka.ms/migrate/col/upgrade_9_11)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

#### <a name="upgrade-to-version-1097"></a>1.0.9.7-es verzió frissítése

Ujjlenyomat-frissítéshez értékek [1.0.9.7-es csomag](https://aka.ms/migrate/col/upgrade_9_7)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="next-steps"></a>További lépések

[Értékelés beállítása helyszíni VMware virtuális gépekhez](tutorial-assessment-vmware.md)
