---
title: Az OMS szolgáltatáshoz Linux-alkalmazás teljesítményének gyűjtése |} Microsoft Docs
description: Ez a cikk részletes adatokat biztosít a teljesítményszámlálók adatainak összegyűjtése MySQL és az Apache HTTP Server Linux OMS-ügynök konfigurálása.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: bfb9b62a8965fa1f7daf62d814665ca23491cc04
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="collect-performance-counters-for-linux-applications-in-log-analytics"></a>Linux Log Analytics-alkalmazások a teljesítményszámlálók adatainak összegyűjtése 
Ez a cikk részletesen konfigurálásához a [Linux OMS-ügynököt](https://github.com/Microsoft/OMS-Agent-for-Linux) adott alkalmazásokra vonatkozó teljesítményszámlálók adatainak összegyűjtése.  A cikk tartalmazza azokat a következők:  

- [MySQL](#MySQL)
- [Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Ha MySQL vagy MariaDB Server észlel a számítógépen, ha az OMS-ügynök telepítve van, egy Teljesítményfigyelő MySQL-kiszolgálóhoz tartozó szolgáltató automatikusan települ. Ez a szolgáltató teljesítménystatisztikáit teszi közzé a helyi MySQL/MariaDB kiszolgálóhoz kapcsolódik. MySQL felhasználói hitelesítő adatokat kell konfigurálni, hogy a szolgáltató férhetnek hozzá a MySQL-kiszolgáló.

### <a name="configure-mysql-credentials"></a>MySQL hitelesítő adatok beállítása
A MySQL OMI szolgáltató egy előre beállított MySQL felhasználói igényel, és MySQL klienskódtárak segítségével telepíteni ahhoz, hogy a teljesítmény- és a MySQL-példányból állapotadatok lekérdezése.  Ezeket a hitelesítő adatokat a Linux-ügynök tárolt hitelesítési fájlban tárolódnak.  A hitelesítési fájl határozza meg, milyen bind-cím és port a MySQL-példány figyeli, és mi gyűjtse össze a metrikák használni kívánt hitelesítő adatait.  

Linux a MySQL OMI az OMS-ügynök telepítése során szolgáltató MySQL my.cnf konfigurációs fájlok (alapértelmezett hely) bind-címet és portot, és részben állítsa be a MySQL OMI hitelesítési fájlt.

A MySQL hitelesítési fájlt tárolja a `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Hitelesítési fájlformátum
Az alábbiakban látható a MySQL OMI hitelesítési fájl formátuma

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

A bejegyzés a hitelesítés-fájl a következő táblázat ismerteti.

| Tulajdonság | Leírás |
|:--|:--|
| Port | A MySQL-példány figyeli a jelenlegi portot jelenti. 0-s port határozza meg, hogy a következő tulajdonságok alapértelmezett példányát használja. |
| A kötés-cím| Aktuális MySQL bind-cím. |
| felhasználónév| MySQL-felhasználó a figyelheti a MySQL server-példány használja. |
| A Base64 kódolású jelszó| A Base64 kódolású MySQL figyelési felhasználó jelszavát. |
| Automatikus frissítés| Megadja, hogy megkeresheti a módosításokat a my.cnf fájlban, és felülírja a MySQL OMI hitelesítési fájlt, a MySQL OMI szolgáltató frissítésekor. |

### <a name="default-instance"></a>Alapértelmezett példány
A MySQL OMI hitelesítési fájlt határozhat meg egy alapértelmezett példány és a port számát több MySQL példányt egy Linux gazdagép könnyebb kezelése.  Az alapértelmezett példány helyén 0-s port példány. Minden további példányokat öröklik tulajdonsága nincs beállítva az alapértelmezett példányból, kivéve, ha ezek különböző értékeket megadni. Például ha porton "3308" MySQL példány kerül hozzáadásra, az alapértelmezett példány bind-cím, a felhasználónév és a Base64-kódolású jelszó használandó próbálja és a figyelést a következő 3308 példány figyelése. Ha 3308-példányhoz van kötve egy másik címet, és használja a MySQL felhasználónév és jelszó-pár csak a bind-cím van szükség, és a többi tulajdonság öröklik.

A következő táblázatban példa példány beállításokkal rendelkezik. 

| Leírás | Fájl |
|:--|:--|
| Alapértelmezett példány és -port 3308 példány. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Alapértelmezett példány és -példány port 3308 és más felhasználónevet és jelszót. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL OMI hitelesítési fájl Program
Egy MySQL OMI hitelesítési fájl programot, amely használható a MySQL OMI hitelesítési fájl szerkesztése az MySQL OMI-szolgáltató telepítésének részét képező van. A hitelesítési fájl program a következő helyen található.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> A hitelesítőadat-fájlt a omsagent fiókkal olvashatónak kell lennie. A mycimprovauth parancsot futtató omsgent ajánlott.

Az alábbi táblázat részletesen szintaxis mycimprovauth használatához.

| Művelet | Példa | Leírás
|:--|:--|:--|
| automatikus frissítés *false vagy true* | mycimprovauth autoupdate hamis | A készlet-e a hitelesítés fájl automatikusan frissíti a indítsa újra, vagy frissíteni. |
| alapértelmezett *bind-cím felhasználónév-jelszó* | mycimprovauth alapértelmezett 127.0.0.1 legfelső szintű pwd | Azt állítja be az alapértelmezett példányt a MySQL OMI hitelesítési fájlt.<br>A jelszó mező egyszerű szövegként kell megadni – – a MySQL OMI hitelesítési fájlban jelszó Base 64 kódolású. |
| Törlés *alapértelmezett vagy port_num* | mycimprovauth 3308 | A megadott példány törlése, vagy alapértelmezett, vagy portszámot. |
| súgó | mycimprov Súgó | Parancsok használatához felsorolása. |
| Nyomtatás | nyomtatási mycimprov | Kinyomtatása egy könnyen áttekinthető MySQL OMI hitelesítési fájlt. |
| Frissítse a port_num *bind-cím felhasználónév-jelszó* | mycimprov frissítés 3307 127.0.0.1 legfelső szintű pwd | A megadott példány frissít, vagy hozzáadja a példány, ha nem létezik. |

A következő Példaparancsok egy alapértelmezett felhasználói fiókot a MySQL-kiszolgáló localhost határozza meg.  A jelszó mező egyszerű szövegként kell megadni – –, a jelszót a MySQL OMI hitelesítési fájlban Base 64 kódolású

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>A MySQL-teljesítményszámlálók szükséges adatbázis-engedélyek
A MySQL-felhasználó a következő lekérdezések MySQL Server teljesítményadatok való hozzáférés szükséges. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


A MySQL-felhasználó is a következő alapértelmezett táblák VÁLASSZA hozzáférést igényel.

- entitástulajdonos
- a MySQL. 

Ezek a jogosultságok a következő grant-parancsok futtatásával kaphatja meg.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> A hozzáférési jogot a MySQL figyelési felhasználó a támogatást nyújtó felhasználó a "GRANT option" jogosultsággal, valamint az engedély jogosultsággal kell rendelkeznie.

### <a name="define-performance-counters"></a>Adja meg a teljesítményszámlálók

Ha megfelelően konfigurált Linux szeretnék adatokat küldeni a Naplóelemzési OMS-ügynököt, konfigurálnia kell a gyűjtendő kérelemteljesítmény-számlálókat.  Az eljárás a [Naplóelemzési a Windows és Linux teljesítmény adatforrások](log-analytics-data-sources-windows-events.md) az alábbi táblázat a számlálókat.

| Objektumnév | Számláló neve |
|:--|:--|
| MySQL-adatbázis | Szabad lemezterület (bájt) |
| MySQL-adatbázis | Táblák |
| MySQL-kiszolgáló | Megszakadt a kapcsolat Pct |
| MySQL-kiszolgáló | Kapcsolat használata Pct |
| MySQL-kiszolgáló | Lemezterület-használat (bájt) |
| MySQL-kiszolgáló | A tábla teljes vizsgálat Pct |
| MySQL-kiszolgáló | InnoDB pufferkészlet találati Pct |
| MySQL-kiszolgáló | InnoDB puffer készlet használata százalékos |
| MySQL-kiszolgáló | InnoDB puffer készlet használata százalékos |
| MySQL-kiszolgáló | A Pct kulcs gyorsítótár-találat |
| MySQL-kiszolgáló | Kulcs gyorsítótárában használata Pct |
| MySQL-kiszolgáló | Kulcs gyorsítótárában írási Pct |
| MySQL-kiszolgáló | Lekérdezés-gyorsítótárának találati Pct |
| MySQL-kiszolgáló | Lekérdezés gyorsítótár szilva Pct |
| MySQL-kiszolgáló | Lekérdezés gyorsítótár használata Pct |
| MySQL-kiszolgáló | A Pct tábla gyorsítótár-találat |
| MySQL-kiszolgáló | Tábla gyorsítótár használata Pct |
| MySQL-kiszolgáló | Tábla zárolási versenyt Pct |

## <a name="apache-http-server"></a>Apache HTTP Server 
Apache HTTP Server észlel a számítógépen a omsagent csomag telepítésekor, ha egy teljesítményfigyelés-szolgáltató az Apache HTTP Server automatikusan települ. Ez a szolgáltató egy Apache modul, amely töltse be az Apache HTTP Server teljesítményadatokat eléréséhez támaszkodik. A modul tölthetők be a következő paranccsal:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Az Apache figyelőmodult el, futtassa a következő parancsot:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Adja meg a teljesítményszámlálók

Ha megfelelően konfigurált Linux szeretnék adatokat küldeni a Naplóelemzési OMS-ügynököt, konfigurálnia kell a gyűjtendő kérelemteljesítmény-számlálókat.  Az eljárás a [Naplóelemzési a Windows és Linux teljesítmény adatforrások](log-analytics-data-sources-windows-events.md) az alábbi táblázat a számlálókat.

| Objektumnév | Számláló neve |
|:--|:--|
| Apache HTTP Server | Foglalt munkavállalók |
| Apache HTTP Server | Üresjárati munkavállalók |
| Apache HTTP Server | A PCT foglalt munkavállalók |
| Apache HTTP Server | Teljes Pct Processzor |
| Apache virtuális állomás | Hibák száma percenként - ügyfél |
| Apache virtuális állomás | Hibák száma percenként - kiszolgáló |
| Apache virtuális állomás | Kérelmenként KB |
| Apache virtuális állomás | KB kérelmek / másodperc |
| Apache virtuális állomás | Kérelmek / másodperc |



## <a name="next-steps"></a>További lépések
* [A teljesítményszámlálók adatainak összegyűjtése](log-analytics-data-sources-performance-counters.md) a Linux-ügynököt.
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) az adatforrások és a megoldások gyűjtött adatok elemzésére. 
