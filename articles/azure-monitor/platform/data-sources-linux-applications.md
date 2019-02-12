---
title: Linux-alkalmazások teljesítménye az Azure monitorban gyűjtése |} A Microsoft Docs
description: Ez a cikk részletes adatokat biztosít a teljesítményszámlálók adatainak összegyűjtése, a MySQL és az Apache HTTP Server Linuxhoz készült Log Analytics-ügynök konfigurálása.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 453e66934b93ab4368c4d3816d3db1a4588ae660
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56001328"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Linux-alkalmazások az Azure Monitor teljesítményszámlálók gyűjtése 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Ez a cikk részletesen konfigurálásához a [Linuxhoz készült Log Analytics-ügynök](https://github.com/Microsoft/OMS-Agent-for-Linux) az adott alkalmazásokra vonatkozó teljesítményszámlálók gyűjtése az Azure Monitor szolgáltatásba.  Az ebben a cikkben szereplő alkalmazások a következők:  

- [MySQL](#MySQL)
- [Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
A MySQL-kiszolgáló vagy a MariaDB-kiszolgáló azt észlelte a számítógépen, a Log Analytics-ügynök telepítésekor a rendszer, ha egy Teljesítményfigyelő-szolgáltatója a MySQL-kiszolgáló automatikusan települ. Ez a szolgáltató teljesítménystatisztikáit elérhetővé a helyi MySQL/MariaDB-kiszolgáló csatlakozik. MySQL-felhasználó hitelesítő adatai kell konfigurálni, hogy a szolgáltató férhessenek hozzá a MySQL-kiszolgáló.

### <a name="configure-mysql-credentials"></a>MySQL hitelesítő adatainak konfigurálása
Az OMI a MySQL-szolgáltató egy előre konfigurált MySQL-felhasználó igényel, és telepítve van a MySQL-ügyfélkódtárak annak érdekében, hogy a teljesítmény és a MySQL-példányt az egészségügyi információk lekérdezése.  Ezeket a hitelesítő adatokat, hogy a Linux-ügynök hitelesítési fájlban tárolódnak.  A hitelesítési fájl milyen bind-címet és portot figyel a MySQL-példányt, és milyen metrikákat használandó hitelesítő adatokat adja meg.  

A MySQL OMI Linuxhoz készült Log Analytics-ügynök telepítése során szolgáltató beolvasási MySQL my.cnf konfigurációs fájljait (alapértelmezett helyeket) bind-cím és port, és részben állítsa be a MySQL OMI hitelesítési fájlt.

A MySQL-hitelesítési fájlt tárolja `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Hitelesítési fájlformátumban
Az alábbiakban a a MySQL OMI hitelesítési fájl formátuma

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

A bejegyzések a hitelesítési fájl az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--|:--|
| Port | Az aktuális port figyel a MySQL-példányt jelöli. Port 0 jelzi, hogy a következő tulajdonságok alapértelmezett példány. |
| Bind-Address| Aktuális MySQL bind-cím. |
| felhasználónév| MySQL-felhasználó használata az MySQL server-példány figyeléséhez használt. |
| Base64 kódolású jelszó| A Base64 kódolású figyelési MySQL-felhasználó jelszavát. |
| AutoUpdate| Megadja, hogy a módosításokat a my.cnf fájl ismételt vizsgálata, és felülírja a MySQL OMI hitelesítési fájlt, a MySQL OMI szolgáltató frissítésekor. |

### <a name="default-instance"></a>Alapértelmezett példány
A MySQL OMI hitelesítési fájl definiálhat egy alapértelmezett példány és a port számát, hogy a Linux-gazdagépen egy egyszerűbb több MySQL-példányok felügyeletére.  Az alapértelmezett példány helyén port 0 rendelkező példány. Minden további példányokat adja meg az alapértelmezett példány, ha azok adja meg a különböző értékek tulajdonságok öröklik. Például "3308" porton MySQL-példányt ad hozzá, ha az alapértelmezett példány bind-cím, a felhasználónevet és jelszót Base64-kódolású használandó próbálja ki, és figyelheti a példány 3308 figyel. Ha a példány a 3308 kötve van egy másik címet, és MySQL felhasználónév és jelszó-pár csak a kötés-címre van szükség ahhoz, és a többi tulajdonság öröklődnek.

A következő táblázatban példa példány beállításokkal rendelkezik. 

| Leírás | Fájl |
|:--|:--|
| Alapértelmezett példány és a példány 3308-port. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Alapértelmezett példány és a példány a port 3308 és más felhasználónevet és jelszót. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL OMI hitelesítési fájlt programba
Az OMI a MySQL-szolgáltató telepítésének részét képező van egy MySQL OMI hitelesítési fájlt program, amely a MySQL OMI hitelesítési fájljának szerkesztésével használható. A hitelesítési fájl program a következő helyen található.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> A hitelesítő adatok fájlnak kell lennie a omsagent fiók által is olvasható. A mycimprovauth parancsot futtató omsgent használata javasolt.

Az alábbi táblázat részletesen szintaxis mycimprovauth használatának.

| Művelet | Példa | Leírás
|:--|:--|:--|
| automatikus frissítési *true vagy a False (hamis)* | mycimprovauth autoupdate hamis | Csoportok-e a hitelesítési fájl automatikusan frissül a indítsa újra, vagy frissíteni. |
| alapértelmezett *bind-cím felhasználónév-jelszó* | mycimprovauth alapértelmezett 127.0.0.1 legfelső szintű pwd | Beállítja az alapértelmezett példányt a MySQL OMI hitelesítési fájlt.<br>A jelszó mezőt meg kell adni az egyszerű szöveg – a jelszót a MySQL OMI hitelesítési fájl Base-64 kódolású lesz. |
| Törlés *alapértelmezett vagy port_num* | mycimprovauth 3308 | Törli a megadott példány, vagy alapértelmezés szerint, vagy a port számát. |
| help | mycimprov Súgó | A parancsok használatához felsorolása. |
| Nyomtatás | nyomtatási mycimprov | Kinyomtatása könnyen olvasható MySQL OMI hitelesítési fájlt. |
| update port_num *bind-address username password* | mycimprov frissítés 3307 127.0.0.1 legfelső szintű pwd | Frissíti a megadott példány, vagy a példány ad, ha még nem létezik. |

A következő Példaparancsok egy alapértelmezett felhasználói fiókot a MySQL-kiszolgáló a localhost határozza meg.  A jelszó mezőt meg kell adni az egyszerű szöveg – a jelszót a MySQL OMI hitelesítési fájl Base-64 kódolású lesz.

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>MySQL-teljesítményszámlálók szükséges adatbázis-engedélyek
A MySQL-felhasználót a következő lekérdezéseket a MySQL-kiszolgáló-teljesítményadatok összegyűjtése hozzáférésre van szüksége. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


A MySQL-felhasználót is a következő alapértelmezett táblák VÁLASSZA hozzáférésre van szüksége.

- information_schema
- MySQL. 

Ezeket a jogosultságokat a következő grant-parancsok futtatásával is megadható.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Engedélyek megadása egy MySQL-hez figyelési felhasználó próbáltak felhasználó a "GRANT option" jogosultsággal, valamint az engedély megadása a jogosultsággal kell rendelkeznie.

### <a name="define-performance-counters"></a>Teljesítményszámlálók definiálása

Ha megfelelően konfigurált adatokat küldeni az Azure Monitor Linuxhoz készült Log Analytics-ügynököket, konfigurálnia kell a teljesítményszámlálók adatait szeretné gyűjteni.  Ismertetett eljárással [az Azure monitorban Windows és Linux rendszerű teljesítmény adatforrások](data-sources-performance-counters.md) az az alábbi táblázat a számlálókat.

| Objektumnév | Számláló neve |
|:--|:--|
| MySQL-adatbázis | Lemezterület (bájt) |
| MySQL-adatbázis | Táblák |
| MySQL-kiszolgáló | Megszakadt kapcsolat százalékos aránya |
| MySQL-kiszolgáló | Kapcsolat használat százalékos aránya |
| MySQL-kiszolgáló | Lemezterület-használat (bájt) |
| MySQL-kiszolgáló | A tábla teljes vizsgálat százalékos aránya |
| MySQL-kiszolgáló | InnoDB Pufferkészletben találati százalékos aránya |
| MySQL-kiszolgáló | InnoDB puffer készlet használat százalékos aránya |
| MySQL-kiszolgáló | InnoDB puffer készlet használat százalékos aránya |
| MySQL-kiszolgáló | Kulcs gyorsítótárbeli találatok százalékos aránya |
| MySQL-kiszolgáló | Kulcs gyorsítótár használata százalékos aránya |
| MySQL-kiszolgáló | Kulcs gyorsítótár írási százalékos aránya |
| MySQL-kiszolgáló | Lekérdezés gyorsítótár-találatok Pct |
| MySQL-kiszolgáló | A lekérdezési gyorsítótár szilva Pct |
| MySQL-kiszolgáló | Lekérdezés gyorsítótár használata százalékos aránya |
| MySQL-kiszolgáló | Táblák gyorsítótárának találati százalékos aránya |
| MySQL-kiszolgáló | Tábla gyorsítótár használata százalékos aránya |
| MySQL-kiszolgáló | Tábla zárolási versenyt százalékos aránya |

## <a name="apache-http-server"></a>Apache HTTP Server 
Ha az Apache HTTP Server észlel a számítógépen a omsagent csomag telepítésekor a rendszer, a teljesítményfigyelés-szolgáltató az Apache HTTP Server automatikusan települ. Ez a szolgáltató támaszkodik egy Apache-modul, amely ahhoz, hogy hozzáférhessen a teljesítményadatokat az Apache HTTP Server be kell tölteni. A modul tölthetők be a következő paranccsal:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Távolítsa el a figyelési Apache-modul, futtassa a következő parancsot:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Teljesítményszámlálók definiálása

Ha megfelelően konfigurált adatokat küldeni az Azure Monitor Linuxhoz készült Log Analytics-ügynököket, konfigurálnia kell a teljesítményszámlálók adatait szeretné gyűjteni.  Ismertetett eljárással [az Azure monitorban Windows és Linux rendszerű teljesítmény adatforrások](data-sources-performance-counters.md) az az alábbi táblázat a számlálókat.

| Objektumnév | Számláló neve |
|:--|:--|
| Apache HTTP Server | Foglalt feldolgozók |
| Apache HTTP Server | Inaktív feldolgozók |
| Apache HTTP Server | A PCT foglalt feldolgozók |
| Apache HTTP Server | A Pct CPU összesen |
| Apache Virtual Host | Hibák száma percenként – ügyfél |
| Apache Virtual Host | Hibák száma percenként - kiszolgáló |
| Apache Virtual Host | KB kérelmenként |
| Apache Virtual Host | KB-os kérések másodpercenként |
| Apache Virtual Host | Kérések másodpercenként |



## <a name="next-steps"></a>További lépések
* [Teljesítményszámlálók gyűjtése](data-sources-performance-counters.md) Linux-ügynököktől.
* Ismerje meg [lekérdezések naplózását](../log-query/log-query-overview.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez. 