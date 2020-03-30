---
title: Linux-alkalmazások teljesítményének gyűjtése az Azure Monitorban | Microsoft dokumentumok
description: Ez a cikk a Log Analytics-ügynök Linuxra konfigurálásának részleteit tartalmazza a MySQL és az Apache HTTP Server teljesítményszámlálóinak gyűjtéséhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2017
ms.openlocfilehash: 2fd148dbb85a4fd60fe63d4fb73128bf92dea1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670559"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Linux-alkalmazások teljesítményszámlálóinak gyűjtése az Azure Monitorban 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Ez a cikk a [Log Analytics-ügynök Linuxra](https://github.com/Microsoft/OMS-Agent-for-Linux) konfigurálása részletesen ismerteti az adott alkalmazások teljesítményszámlálóinak az Azure Monitorba történő gyűjtéséhez.  Az alkalmazások ebben a cikkben a következők:  

- [MySQL](#mysql)
- [Apache HTTP-kiszolgáló](#apache-http-server)

## <a name="mysql"></a>MySQL
Ha a Log Analytics ügynök telepítésekor a rendszer mySQL Server vagy MariaDB Server rendszert észlel a számítógépen, a Rendszer automatikusan telepíti a MySQL Server teljesítményfigyelő szolgáltatóját. Ez a szolgáltató csatlakozik a helyi MySQL/MariaDB kiszolgálóhoz a teljesítménystatisztikák megjelenítése érdekében. A MySQL felhasználói hitelesítő adatait úgy kell konfigurálni, hogy a szolgáltató hozzáférhessen a MySQL Server kiszolgálóhoz.

### <a name="configure-mysql-credentials"></a>MySQL hitelesítő adatok konfigurálása
A MySQL OMI szolgáltató nak előre konfigurált MySQL-felhasználóra van szüksége, és telepítette a MySQL ügyfélkönyvtárakat, hogy lekérdezhesse a teljesítmény- és állapotadatokat a MySQL-példányból.  Ezek a hitelesítő adatok a Linux-ügynökön tárolt hitelesítési fájlban tárolódnak.  A hitelesítési fájl határozza meg, hogy a MySQL-példány milyen kötési címet és portot figyel, és milyen hitelesítő adatokat kell használni a metrikák összegyűjtéséhez.  

A Linux log analytics ügynökének telepítése során a MySQL OMI szolgáltató megvizsgálja a MySQL my.cnf konfigurációs fájlokat (alapértelmezett helyeket) a bind-cím és a port számára, és részben beállítja a MySQL OMI hitelesítési fájlt.

A MySQL hitelesítési fájl `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`a rendszerben tárolódik.


### <a name="authentication-file-format"></a>Hitelesítési fájlformátum
Az alábbiakban a MySQL OMI hitelesítési fájl formátuma látható

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

A hitelesítési fájl bejegyzéseit az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--|:--|
| Port | Azt az aktuális portot jelöli, amelyet a MySQL-példány figyel. A 0-s port azt adja meg, hogy a következő tulajdonságokat használja az alapértelmezett példány. |
| Kötéscíme| Aktuális MySQL bind-cím. |
| felhasználónév| A MySQL felhasználó a MySQL szerverpéldány figyelésére használt. |
| Base64 kódolású jelszó| A Base64-be kódolt MySQL monitorfelhasználó jelszava. |
| Autoupdate| Itt adható meg, hogy a MySQL OMI-szolgáltató frissítésekor újra be kell-e vizsgálni a my.cnf fájl módosításait, és felül kell-e írni a MySQL OMI authentication fájlt. |

### <a name="default-instance"></a>Alapértelmezett példány
A MySQL OMI hitelesítési fájl alapértelmezett példányt és portszámot határozhat meg, hogy megkönnyítse több MySQL-példány kezelését egy Linux-állomáson.  Az alapértelmezett példányt a 0-s porttal rendelkező példány jelöli. Minden további példány örökli az alapértelmezett példánytól beállított tulajdonságokat, kivéve, ha különböző értékeket adnak meg. Ha például a "3308" porton a MySQL-példány figyelője kerül hozzáadásra, az alapértelmezett példány kötési címe, felhasználóneve és Base64 kódolású jelszava a 3308-as példány figyelésének megkísérléséhez lesz használva. Ha a 3308-as példány egy másik címhez van kötve, és ugyanazt a MySQL felhasználónevet és jelszópártot használja, csak a kötési cím szükséges, és a többi tulajdonság öröklődik.

Az alábbi táblázat példapéldány-beállításokat mutat be 

| Leírás | Fájl |
|:--|:--|
| Alapértelmezett példány és példány a 3308-as porttal. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Alapértelmezett példány és példány a 3308-as porttal és a különböző felhasználónévvel és jelszóval. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL OMI hitelesítési fájlprogram
A MySQL OMI szolgáltató telepítéséhez tartozik egy MySQL OMI hitelesítési fájlprogram, amely a MySQL OMI authentication fájl szerkesztésére használható. A hitelesítési fájlprogram a következő helyen található.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> A hitelesítő adatok fájljának az omsagent fióknak olvashatónak kell lennie. A mycimprovauth parancs omsgent néven való futtatása ajánlott.

Az alábbi táblázat a mycimprovauth használatának szintaxisát tartalmazza.

| Művelet | Példa | Leírás
|:--|:--|:--|
| automatikus frissítés *hamis vagy igaz* | mycimprovauth autoupdate hamis | Beállítja, hogy a hitelesítési fájl automatikusan frissüljön-e az újraindításkor vagy a frissítéskor. |
| alapértelmezett *kötési cím felhasználónév-jelszó* | mycimprovauth alapértelmezett 127.0.0.1 gyökér pwd | Beállítja az alapértelmezett példányt a MySQL OMI hitelesítési fájlban.<br>A jelszó mezőt egyszerű szövegként kell beírni - a MySQL OMI hitelesítési fájlban lévő jelszó Base 64 kódolású lesz. |
| *alapértelmezett vagy port_num* törlése | mycimprovauth 3308 között | A megadott példány törlése alapértelmezés szerint vagy portszám szerint. |
| segítség | mycimprov segítség | Kinyomtatja a használandó parancsok listáját. |
| Nyomtatási | mycimprov nyomtatás | Kinyomtat egy könnyen olvasható MySQL OMI hitelesítési fájlt. |
| frissítés port_num *kötési cím felhasználónév jelszavának frissítése* | mycimprov frissítés 3307 127.0.0.1 gyökér pwd | Frissíti a megadott példányt, vagy hozzáadja a példányt, ha nem létezik. |

A következő példaparancsok a Localhost MySQL-kiszolgáló alapértelmezett felhasználói fiókját határozzák meg.  A jelszó mezőt egyszerű szövegként kell beírni - a MySQL OMI hitelesítési fájlban lévő jelszó Base 64 kódolású lesz

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>A MySQL teljesítményszámlálóihoz szükséges adatbázis-engedélyek
A MySQL-felhasználónak hozzáférésre van szüksége a következő lekérdezésekhez a MySQL Server teljesítményadatainak gyűjtéséhez. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


A MySQL-felhasználónak a következő alapértelmezett táblákhoz is szüksége van SELECT-hozzáférésre.

- information_schema
- Mysql. 

Ezek a jogosultságok a következő támogatási parancsok futtatásával adhatók meg.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> A MySQL-figyelő felhasználó engedélyeinek megadásához a megadó felhasználónak rendelkeznie kell a "GRANT opció" jogosultsággal, valamint a megadott jogosultsággal.

### <a name="define-performance-counters"></a>Teljesítményszámlálók definiálása

Miután konfigurálta a Log Analytics-ügynök Linux adatok küldésére az Azure Monitor, konfigurálnia kell a teljesítményszámlálók gyűjtésére.  Használja az eljárást a [Windows és a Linux teljesítményadatforrások az Azure Monitor](data-sources-performance-counters.md) a számlálók az alábbi táblázatban.

| Objektum neve | Számláló neve |
|:--|:--|
| MySQL-adatbázis | Lemezterület bájtban |
| MySQL-adatbázis | Táblák |
| MySQL kiszolgáló | Megszakított kapcsolat pct |
| MySQL kiszolgáló | Kapcsolat használata Pct |
| MySQL kiszolgáló | Lemezterület-használat bájtban |
| MySQL kiszolgáló | Teljes asztal scan Pct |
| MySQL kiszolgáló | InnoDB pufferkészlet találati fájl |
| MySQL kiszolgáló | InnoDB pufferkészlet használata PCT |
| MySQL kiszolgáló | InnoDB pufferkészlet használata PCT |
| MySQL kiszolgáló | Kulcs cache hit pct |
| MySQL kiszolgáló | Kulcsgyorsítótár használata PCT |
| MySQL kiszolgáló | Kulcsgyorsítótár írási pct |
| MySQL kiszolgáló | Lekérdezési gyorsítótár találati fájlja |
| MySQL kiszolgáló | Lekérdezési gyorsítótár aszalt pct-t ad |
| MySQL kiszolgáló | Lekérdezési gyorsítótár használata PCT |
| MySQL kiszolgáló | Tábla gyorsítótár találati fájlja |
| MySQL kiszolgáló | Táblagyorsítótár használata PCT |
| MySQL kiszolgáló | Tábla zárolási versengési pct |

## <a name="apache-http-server"></a>Apache HTTP-kiszolgáló 
Ha az omsagent csomag telepítésekor apache HTTP Server-kiszolgálót észlel a rendszer a számítógépen, az Apache HTTP Server teljesítményfigyelő szolgáltatója automatikusan települ. Ez a szolgáltató egy Apache modulra támaszkodik, amelyet be kell tölteni az Apache HTTP Server kiszolgálóba a teljesítményadatok eléréséhez. A modul a következő paranccsal tölthető be:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Az Apache figyelőmodul kiürítéséhez futtassa a következő parancsot:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Teljesítményszámlálók definiálása

Miután konfigurálta a Log Analytics-ügynök Linux adatok küldésére az Azure Monitor, konfigurálnia kell a teljesítményszámlálók gyűjtésére.  Használja az eljárást a [Windows és a Linux teljesítményadatforrások az Azure Monitor](data-sources-performance-counters.md) a számlálók az alábbi táblázatban.

| Objektum neve | Számláló neve |
|:--|:--|
| Apache HTTP-kiszolgáló | Elfoglalt munkavállalók |
| Apache HTTP-kiszolgáló | Tétlen dolgozók |
| Apache HTTP-kiszolgáló | Pct foglalt munkavállalók |
| Apache HTTP-kiszolgáló | Teljes PCT CPU |
| Apache virtuális gazdagép | Hibák percenként - Ügyfél |
| Apache virtuális gazdagép | Hibák percenként - Kiszolgáló |
| Apache virtuális gazdagép | KB kérésenként |
| Apache virtuális gazdagép | KB-másodperces kérelmek |
| Apache virtuális gazdagép | Kérelmek másodpercenként |



## <a name="next-steps"></a>További lépések
* [Teljesítményszámlálók gyűjtése](data-sources-performance-counters.md) Linux-ügynököktől.
* Ismerje meg a [naplólekérdezéseket](../log-query/log-query-overview.md) az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez. 
