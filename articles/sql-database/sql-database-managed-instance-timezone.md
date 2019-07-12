---
title: Az Azure SQL Database felügyelt példányain időzónák |} A Microsoft Docs"
description: További tudnivalók az Azure SQL Database felügyelt példányain időzóna tulajdonságairól
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 07/05/2019
ms.openlocfilehash: 05ec49c98c5bcfe40346550f5570c03a8fb3f881
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657995"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Az Azure SQL Database felügyelt példányába időzónák

Egyezményes világidő (UTC) a felhőalapú megoldások adatrétegének ajánlott időzónáját. Az Azure SQL Database felügyelt példánya is kínál időzónák többféle dátum és idő értékét tárolja, és a egy adott időzóna implicit kontextusában a date és time függvények hívása már meglévő alkalmazások igényeinek.

T-SQL-funkciók, például [GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) , illetve CLR-beli kódot, tekintse át az adott időzóna szolgáltatásiszint-példányon állítsa. Az SQL Server-ügynök feladatot is hajtsa végre az ütemezéseket a példány időzónájának megfelelően.

  >[!NOTE]
  > Felügyelt példány egyetlen központi telepítési lehetőség az Azure SQL Database, amely támogatja az időzóna-beállítását. Egyéb telepítési lehetőségekért mindig kövesse (UTC).
Használat [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) egyetlen vagy készletezett SQL-adatbázisokban ha értelmezhető dátum és idő információ nem UTC időzónában van szüksége.

## <a name="supported-time-zones"></a>Támogatott időzónák

Támogatott időzónákban egy készletét az alapul szolgáló operációs rendszer a felügyelt példány öröklődik. Új definíciókat, és változásainak és a meglévő rendszeresen frissítjük.

[Nyári időszámítás/időzóna módosítása a házirend](https://aka.ms/time) előre a 2010-ről előzményadatok pontossága garantálja.

A támogatott időzónák neveinek listáját keresztül közzétett a [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) rendszernézet.

## <a name="set-a-time-zone"></a>Időzóna beállítása

A felügyelt példány időzónát beállítható csak a példány létrehozása során. Az alapértelmezett időzóna UTC.

  >[!NOTE]
  > Meglévő felügyelt példány az adott időzóna nem lehet módosítani.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Állítsa be az időzónát, az Azure Portalon keresztül

Paraméterek egy új példányt ad meg, amikor támogatott időzónák listájából válassza ki az időzónát.
  
![Időzóna beállítása során példány létrehozása](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Adja meg a időzóna azonosítója tulajdonságot a [Resource Manager-sablon](https://aka.ms/sql-mi-create-arm-posh) példány létrehozása során az időzóna beállítása.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

Az időzóna azonosítója tulajdonság támogatott értékei listáját van ez a cikk végén található.

Ha nincs megadva, az adott időzóna UTC-re van állítva.

## <a name="check-the-time-zone-of-an-instance"></a>Ellenőrizze az időzóna-példány

A [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) függvény az időzóna-példány egy megjelenített nevét adja vissza.

## <a name="cross-feature-considerations"></a>Kereszt-funkciók kapcsolatos megfontolások

### <a name="restore-and-import"></a>Visszaállítás és importálása

Visszaállítás biztonsági másolatból, vagy adatokat importálhat egy felügyelt példányra egy példányt, vagy a kiszolgáló más időzónában beállításokkal. Ellenőrizze, hogy ehhez kellő körültekintéssel járjon el. Elemezheti az alkalmazás viselkedése és az eredményeket a lekérdezéseket és jelentéseket, csakúgy, mint két másik időzónában beállításokkal rendelkező SQL Server-példányok közötti adatátvitel esetén.

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

<del>Egy időponthoz visszaállítást hajt végre, amikor a helyreállításához szükséges időt UTC idő kerül értelmezésre. Ezzel a beállítással elkerülheti a kétértelműséget, nyári időszámítás és a hozzá tartozó esetleges módosítások miatt.<del>

 >[!WARNING]
  > Jelenlegi működése nem megfelelően a fenti nyilatkozat, és a felügyelt példány forrás, ahol az adatbázis automatikus biztonsági mentések megnyílik az időzónájának megfelelően értelmezi a helyreállításához szükséges időt. Ez a viselkedés értelmezése pont a megadott idő, UTC-idő javítása dolgozunk. Lásd: [ismert problémák](sql-database-managed-instance-timezone.md#known-issues) további részletekért.

### <a name="auto-failover-groups"></a>Automatikus feladatátvételi csoportok

Ugyanabban az időzónában használatával között egy elsődleges és másodlagos példány a feladatátvételi csoportban nincs kényszerítve, de javasoljuk, hogy azt.

  >[!WARNING]
  > Javasoljuk, hogy az elsődleges és másodlagos példány egy feladatátvételi csoportot használjon ugyanabban az időzónában. Egyes ritka esetekben miatt ugyanabban az időzónában tartja az elsődleges és másodlagos példányok között nincs kényszerítve. Fontos tudni, hogy manuális vagy automatikus feladatátvétel esetén a másodlagos példány megőrzik az eredeti időzóna.

## <a name="limitations"></a>Korlátozások

- A meglévő felügyelt példány az adott időzóna nem lehet módosítani.
- Külső folyamatok, az SQL Server Agent-feladatok elindítása nem veszik figyelembe az adott időzóna-példány.

## <a name="known-issues"></a>Ismert problémák

Amikor időponthoz visszaállítás (PITR) művelet végrehajtása, időzóna beállítása a következő felügyelt példányt, megnyílik az adatbázis automatikus biztonsági mentések, annak ellenére, hogy PITR portáloldalán javasolja, hogy az idő (UTC) kerül értelmezésre megfelelően értelmezi a történő helyreállításához szükséges időt.

Példa:

Tegyük fel azt a példányt, megnyílik az automatikus biztonsági mentést rendelkezik keleti téli idő (UTC-5) időzónában.
Időponthoz visszaállítási portál oldalán arra utalnak, hogy vissza választ az idő UTC-idő:

![PITR a portál használatával helyi idő](media/sql-database-managed-instance-timezone/02-pitr-with-nonutc-timezone.png)

Azonban keleti téli idő ténylegesen értelmezi a helyreállításához szükséges időt, és ebben a példában adatbázis lesz visszaállítva, 9 Órakor keleti téli idő, és nem az UTC-idő állapothoz.

Ha szeretne időponthoz visszaállítása adott időpontra való UTC időben, először a forrás-példány időzónájában egyenértékű idő kiszámítása, és az időpont a portálon vagy a PowerShell és CLI-példaszkript használja.

## <a name="list-of-supported-time-zones"></a>Támogatott időzónákat

| **Időzóna-Azonosítóhoz** | **Időzóna megjelenített neve** |
| --- | --- |
| Dátumvonali téli idő | (UTC-12:00) Nemzetközi dátumválasztó vonal - nyugat |
| UTC-11 | (UTC-11:00) Egyezményes világidő-11 |
| Aleuti téli idő | (UTC-10:00) Aleut-szigetek |
| Hawaii téli idő | (UTC-10:00) Hawaii |
| Marquesas téli idő | (UTC-09:30) Marquesas-szigetek |
| Alaszkai téli idő | (UTC-09:00) Alaszka |
| UTC-09 | (UTC-09:00) Egyezményes világidő-09 |
| Csendes-óceáni téli idő (Mexikó) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Egyezményes világidő-08 |
| Csendes-óceáni téli idő | (UTC-08:00) Csendes-óceáni idő (Egyesült Államok és Kanada) |
| Amerikai hegyi téli idő | (UTC-07:00) Arizona |
| Hegyi téli idő (Mexikó) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Hegyi téli idő | (UTC-07:00) Hegyi idő (Egyesült Államok és Kanada) |
| Közép-amerikai téli idő | (UTC-06:00) Közép-Amerika |
| Téli középidő | (UTC-06:00) Amerikai középidő (Egyesült Államok és Kanada) |
| Húsvét-szigeti téli idő | (UTC-06:00) Húsvét-sziget |
| Téli középidő (Mexikó) | (UTC-06:00) Guadalajara, Mexikóváros, Monterrey |
| Kanadai téli középidő | (UTC-06:00) Saskatchewan |
| Dél-amerikai csendes-óceáni téli idő | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Keleti téli idő (Mexikó) | (UTC-05:00) Chetumal |
| Keleti téli idő | (UTC-05:00) Keleti idő (Egyesült Államok és Kanada) |
| Haiti téli idő | (UTC-05:00) Haiti |
| Cuba Standard Time | (UTC-05:00) Havanna |
| Amerikai keleti téli idő | (UTC-05:00) Indiana (kelet) |
| Turks- és Caicos-szigetek téli idő | (UTC-05:00) Turks- és Caicos-szigetek |
| Paraguayi téli idő | (UTC-04:00) Asuncion |
| Atlanti-óceáni téli idő | (UTC-04:00) Atlanti-óceáni idő (Kanada) |
| Venezuela Standard Time | (UTC-04:00) Caracas |
| Közép-brazíliai idő | (UTC-04:00) Cuiaba |
| Dél-amerikai nyugati téli idő | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Csendes-Óceáni téli idő | (UTC-04:00) Santiago |
| Új-foundlandi téli idő | (UTC-03:30) Newfoundland |
| Tocantins Standard Time | (UTC-03:00) Araguaina |
| E. Dél-amerikai téli idő | (UTC-03:00) Brasilia |
| Dél-amerikai keleti téli idő | (UTC-03:00) Cayenne, Fortaleza |
| Argentina Standard Time | (UTC-03:00) Buenos Aires város |
| Grönlandi téli idő | (UTC-03:00) Grönland |
| Montevideoi téli idő | (UTC-03:00) Montevideo |
| Magallanes téli idő | (UTC-03:00) Punta Arenas |
| Saint Pierre téli idő | (UTC-03:00) Saint-Pierre és Miquelon |
| Bahiai téli idő | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Egyezményes világidő-02 |
| Közép-atlanti téli idő | (UTC-02:00) Közép-atlanti időzóna – régi |
| Azori-szigetek téli idő | (UTC-01:00) Azori-szigetek |
| Zöld-foki-szigeteki téli idő | (UTC-01:00) Cabo Verde |
| UTC | (UTC) Egyezményes világidő |
| Greenwichi téli idő | (UTC+00:00) Dublin, Edinburgh, Lisszabon, London |
| Greenwichi téli idő | (UTC+00:00) Monrovia, Reykjavik |
| W. Európai téli idő | (UTC+01:00) Amszterdam, Berlin, Bern, Róma, Stockholm, Bécs |
| Közép-európai téli idő | (UTC+01:00) Belgrád, Pozsony, Budapest, Ljubljana, Prága |
| Romance Standard Time | (UTC+01:00) Brüsszel, Koppenhága, Madrid, Párizs |
| Marokkói téli idő | (UTC + 01:00) Casablanca |
| Sao Tome Standard Time | (UTC + 01:00) Sao Tome |
| Közép-európai téli idő | (UTC+01:00) Szarajevó, Szkopje, Varsó, Zágráb |
| W. Közép-afrikai téli idő | (UTC+01:00) Nyugat-Közép-Afrika |
| Jordániai téli időszámítás | (UTC+02:00) Amman |
| ATHÉNI téli idő | (UTC+02:00) Athén, Bukarest |
| Közel-keleti téli idő | (UTC+02:00) Bejrút |
| Egyiptomi téli idő | (UTC+02:00) Kairó |
| E. Európai téli idő | (UTC+02:00) Chisinau |
| Szíriai téli idő | (UTC+02:00) Damaszkusz |
| Nyugati téli idő | (UTC+02:00) Gáza, Hebron |
| Dél-afrikai téli idő | (UTC+02:00) Harare, Pretoria |
| FÁJL téli idő | (UTC+02:00) Helsinki, Kijev, Riga, Szófia, Tallinn, Vilnius |
| Izraeli normál idő | (UTC+02:00) Jeruzsálem |
| Kalinyingrádi téli idő | (UTC+02:00) Kalinyingrád |
| Szudáni téli idő | (UTC + 02:00) Kartúm |
| Libya Standard Time | (UTC+02:00) Tripoli |
| Namibia Standard Time | (UTC + 02:00) Windhoek |
| Bagdadi téli idő | (UTC+03:00) Bagdad |
| Törökországi téli idő | (UTC + 03:00) Isztambul |
| Arab téli idő | (UTC+03:00) Kuvait, Riyadh |
| Belarusz téli idő | (UTC+03:00) Minszk |
| Orosz téli idő | (UTC + 03:00) Moszkva, Szentpétervár |
| E. Afrikai téli idő | (UTC+03:00) Nairobi |
| Iráni téli idő | (UTC+03:30) Teherán |
| Arábiai téli idő | (UTC+04:00) Abu Dhabi, Muscat |
| Asztraháni téli idő | (UTC+04:00) Asztrahán, Uljanovszk |
| Azerbajdzsáni téli időszámítás | (UTC+04:00) Baku |
| Russia Time Zone 3 | (UTC+04:00) Izsevszk, Szamara |
| Mauritius Standard Time | (UTC+04:00) Port Louis |
| Saratov Standard Time | (UTC + 04:00) Szaratovi |
| Grúz téli idő | (UTC+04:00) Tbiliszi |
| Volgográd téli idő | (UTC + 04:00) Volgográd |
| Kaukázusi téli idő | (UTC+04:00) Jereván |
| Afganisztáni téli idő | (UTC+04:30) Kabul |
| Nyugat-ázsiai téli idő | (UTC+05:00) Asgabat, Taskent |
| Jekatyerinburgi téli idő | (UTC+05:00) Jekatyerinburg |
| Pakisztáni téli idő | (UTC+05:00) Iszlámábád, Karachi |
| Indiai téli idő | (UTC+05:30) Chennai, Kolkata, Mumbai, Új-Delhi |
| Srí Lanka-i téli idő | (UTC+05:30) Sri Jayawardenepura |
| Nepáli téli idő | (UTC+05:45) Katmandu |
| Közép-ázsiai téli idő | (UTC+06:00) Asztana |
| Bangladesi téli idő | (UTC+06:00) Dhaka |
| Omsk Standard Time | (UTC + 06:00) Omszk |
| Myanmari téli idő | (UTC+06:30) Yangon (Rangun) |
| SE Asia Standard Time | (UTC+07:00) Bangkok, Hanoi, Jakarta |
| Altaji téli idő | (UTC+07:00) Barnaul, Gorno-Altajszk |
| W. Mongolia Standard Time | (UTC+07:00) Hovd |
| North Asia Standard Time | (UTC+07:00) Krasznojarszk |
| N. Közép-ázsiai téli idő | (UTC + 07:00) Novoszibirszk |
| Tomsk Standard Time | (UTC+07:00) Tomszk |
| Kínai téli idő | (UTC+08:00) Peking, Chongqing, Hongkong (KKT), Urumqi |
| Észak-ázsiai keleti téli idő | (UTC+08:00) Irkutszk |
| Szingapúri téli idő | (UTC+08:00) Kuala Lumpur, Szingapúr |
| W. Ausztráliai téli idő | (UTC+08:00) Perth |
| Tajpeji téli idő | (UTC+08:00) Taipei |
| Ulánbátori téli idő | (UTC+08:00) Ulánbátor |
| Ausztrál Középnyugati téli idő | (UTC+08:45) Eucla |
| Transzbajkáli téli idő | (UTC+09:00) Csita |
| Tokiói téli idő | (UTC+09:00) Oszaka, Szapporo, Tokió |
| Észak-koreai téli idő | (UTC + 09:00) Phenjan |
| Korea Standard Time | (UTC+09:00) Szöul |
| Jakutszki téli idő | (UTC+09:00) Jakutszk |
| CEN. Ausztráliai téli idő | (UTC+09:30) Adelaide |
| Ausztráliai közép téli idő | (UTC+09:30) Darwin |
| E. Ausztráliai téli idő | (UTC+10:00) Brisbane |
| Ausztráliai keleti téli idő | (UTC+10:00) Canberra, Melbourne, Sydney |
| Nyugat-csendes-óceáni téli idő | (UTC+10:00) Guam, Port Moresby |
| Tasmániai téli idő | (UTC+10:00) Hobart |
| Vlagyivosztoki téli idő | (UTC+10:00) Vlagyivosztok |
| Lord Howe téli idő | (UTC+10:30) Lord Howe-sziget |
| Bougainville téli idő | (UTC+11:00) Bougainville sziget |
| Russia Time Zone 10 | (UTC+11:00) Csokurdah |
| Magadáni téli idő | (UTC+11:00) Magadan |
| Norfolk téli idő | (UTC+11:00) Norfolk-sziget |
| Szahalini téli idő | (UTC+11:00) Szahalin |
| Közép-csendes-óceáni téli idő | (UTC+11:00) Salamon-szigetek, Új-Kaledónia |
| 11. orosz időzóna | (UTC+12:00) Anadir, Petropavlovszk-Kamcsatszkij |
| Új-zélandi téli idő | (UTC+12:00) Auckland, Wellington |
| UTC+12 | (UTC+12:00) Egyezményes világidő+12 |
| Fidzsi-szigeteki téli idő | (UTC+12:00) Fidzsi |
| Kamchatka Standard Time | (UTC+12:00) Petropavlovszk-Kamcsatszkij - régi |
| Chatham-szigeteki téli idő | (UTC+12:45) Chatham-szigetek |
| UTC+13 | (UTC + 13:00) Egyezményes világidő + 13 |
| Tonga Standard Time | (UTC+13:00) Nuku'alofa |
| Szamoai téli idő | (UTC+13:00) Szamoa |
| Line-szigetek téli idő | (UTC+14:00) Kiritimati-sziget |

## <a name="see-also"></a>Lásd még 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
