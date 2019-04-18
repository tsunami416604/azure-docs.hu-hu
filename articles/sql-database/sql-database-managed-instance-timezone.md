---
title: Azure SQL Database Managed Instance Time Zone | Microsoft Docs"
description: Ismerje meg az Azure SQL Database felügyelt példányain időzóna tulajdonságairól
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 04/10/2019
ms.openlocfilehash: 23314e97051da95ab164baeab6e9d089f486351a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489690"
---
# <a name="time-zone-in-azure-sql-database-managed-instance-preview"></a>Az Azure SQL Database felügyelt példány (előzetes verzió) időzóna

Noha az egyezményes világidő (UTC) használatával felhőalapú megoldások az adatréteg számára ajánlott gyakorlat, az Azure SQL Database felügyelt példányain kínál időzóna többféle, ha a meglévő alkalmazásokat, amelyek dátum és idő értékét tárolja, és hívás dátuma és egy adott időzóna implicit kontextusában idő együttműködik.

T-SQL-funkciók, például [GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) , illetve CLR-beli kódot, tekintse át az adott időzóna szolgáltatásiszint-példányon állítsa. SQL-ügynök feladatot is hajtsa végre az ütemezés a példány időzónájának megfelelően.

  >[!NOTE]
  > Felügyelt példány egyetlen központi telepítési lehetőség az Azure SQL Database, amely támogatja az időzóna-beállítását. Egyéb telepítési lehetőségekért mindig kövesse (UTC).
Használat [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) egyetlen vagy készletezett SQL-adatbázisokban ilyenkor nem UTC időzóna szerint dátum és idő adatok értelmezése.

## <a name="supported-time-zones"></a>Támogatott időzónák

A felügyelt példány az alapul szolgáló operációs rendszer támogatott időzónák készletét öröklődnek, és rendszeresen frissül új definíciókat, és változásainak és a meglévő.

A támogatott időzónák neveinek listáját keresztül közzétett a [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) rendszernézet.

## <a name="setting-time-zone"></a>A beállítás időzóna

Felügyelt példány időzónát beállítható csak a példány létrehozása során. Az alapértelmezett időzóna az egyezményes világidő (UTC).

  >[!NOTE]
  > Meglévő felügyelt példány az adott időzóna nem lehet módosítani.

### <a name="setting-the-time-zone-through-azure-portal"></a>Az Azure Portalon keresztül időzóna beállítása

Az új példány paraméterek megadása, miközben időzónát kijelölhet a listában, a támogatott időzónák:
  
![Időzóna beállítása során példány létrehozása](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Adja meg az időzóna azonosítója tulajdonságot a [Resource Manager-sablon](https://aka.ms/sql-mi-create-arm-posh) példány létrehozása során az időzóna beállítása.

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

Ez a cikk végén található időzóna azonosítója tulajdonság támogatott értékei listája.

Ha nincs megadva, a rendszer időzóna UTC-re állítja.

## <a name="checking-the-time-zone-of-instance"></a>Az időzóna-példány ellenőrzése

[CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) függvény az időzóna-példány egy megjelenített nevét adja vissza.

## <a name="cross-feature-considerations"></a>Kereszt-funkciók kapcsolatos megfontolások

### <a name="restore-and-import"></a>Visszaállítás és importálása

Visszaállítás biztonsági másolatból, vagy adatokat importálhat a felügyelt példány egy példányt, vagy a kiszolgáló más időzónában beállításokkal. Azonban ügyeljen arra, hogy ehhez kellő körültekintéssel járjon el, és elemezheti az alkalmazás viselkedése, és az eredményeket a lekérdezéseket és jelentéseket, ugyanúgy, mint a különböző időzóna-beállítások két SQL Server-példányok közötti adatátvitel során.

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

Időponthoz visszaállítás végrehajtásakor történő helyreállításához szükséges időt a rendszer értelmezi UTC időnek nyári időszámítás és az esetleges változások miatt a félreérthetőség elkerülése érdekében.

### <a name="auto-failover-groups"></a>Automatikus feladatátvételi csoportok

Feladatátvétel az elsődleges és másodlagos példány között ugyanabban az időzónában használatával csoport nincs érvényes, de erősen ajánlott.
  >[!IMPORTANT]
  > Bár vannak forgatókönyvek esetén másik időzónában kellene földrajzilag másodlagos példányt használja a csak olvasási méretezhető, vegye figyelembe, hogy a másodlagos példány manuális vagy automatikus feladatátvétel esetén sem megtartja annak eredeti időzóna.

## <a name="limitations"></a>Korlátozások

- A meglévő felügyelt példány időzóna nem lehet módosítani.
- Az SQL Agent-feladatok elindítása külső folyamatok időzóna-példány nem veszik figyelembe.
- Felügyelt példány natív felhasználói [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance) PowerShell-parancsmag nem támogatási megadásának időzónájában paraméter még. PowerShell-burkoló használata, [Resource Manager-sablon](https://aka.ms/sql-mi-create-arm-posh) helyette.
- CLI-paranccsal [létrehozása az sql buszpéldány](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-create) még nem támogatja időzóna-paraméter.

## <a name="list-of-supported-time-zones"></a>Támogatott időzónákat

| **Időzóna-Azonosítóhoz** | **Időzóna megjelenített neve** |
| --- | --- |
| Dátumvonali téli idő | (UTC-12:00) Nemzetközi dátumválasztó vonal - nyugat |
| UTC-11 | (UTC-11:00) Egyezményes világidő-11 |
| Aleuti téli idő | (UTC-10:00) Aleut-szigetek |
| Hawaii téli idő | (UTC-10:00) Hawaii |
| Marquises-szigeteki téli idő | (UTC-09:30) Marquesas-szigetek |
| Alaszkai téli idő | (UTC-09:00) Alaszka |
| UTC-09 | (UTC-09:00) Egyezményes világidő-09 |
| Csendes-óceáni téli idő (Mexikó) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Egyezményes világidő-08 |
| Csendes-óceáni téli idő | (UTC-08:00) Csendes-óceáni idő (Egyesült Államok és Kanada) |
| Amerikai hegyi téli idő | (UTC-07:00) Arizona |
| Hegyi téli idő (Mexikó) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Hegyi téli idő | (UTC-07:00) Hegyi idő (Egyesült Államok és Kanada) |
| Közép-amerikai téli idő | (UTC-06:00) Közép-Amerika |
| Amerikai középső téli idő | (UTC-06:00) Amerikai középidő (Egyesült Államok és Kanada) |
| Húsvét-szigeti téli idő | (UTC-06:00) Húsvét-sziget |
| Téli középidő (Mexikó) | (UTC-06:00) Guadalajara, Mexikóváros, Monterrey |
| Kanadai téli középidő | (UTC-06:00) Saskatchewan |
| Dél-amerikai csendes-óceáni téli idő | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Keleti téli idő (Mexikó) | (UTC-05:00) Chetumal |
| Keleti téli idő | (UTC-05:00) Keleti idő (Egyesült Államok és Kanada) |
| Haiti téli idő | (UTC-05:00) Haiti |
| Kubai téli idő | (UTC-05:00) Havanna |
| Amerikai keleti téli idő | (UTC-05:00) Indiana (kelet) |
| Turks- és Caicos-szigetek téli idő | (UTC-05:00) Turks- és Caicos-szigetek |
| Paraguayi téli idő | (UTC-04:00) Asuncion |
| Atlanti-óceáni téli idő | (UTC-04:00) Atlanti-óceáni idő (Kanada) |
| Venezuelai téli idő | (UTC-04:00) Caracas |
| Közép-brazíliai idő | (UTC-04:00) Cuiaba |
| Dél-amerikai nyugati téli idő | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Csendes-óceáni dél-amerikai téli idő | (UTC-04:00) Santiago |
| Új-foundlandi téli idő | (UTC-03:30) Newfoundland |
| Tocantinsi nyári idő | (UTC-03:00) Araguaina |
| E. Dél-amerikai téli idő | (UTC-03:00) Brasilia |
| Dél-amerikai keleti téli idő | (UTC-03:00) Cayenne, Fortaleza |
| Argentin téli idő | (UTC-03:00) Buenos Aires város |
| Grönlandi téli idő | (UTC-03:00) Grönland |
| Montevideoi téli idő | (UTC-03:00) Montevideo |
| Magallanesi téli idő | (UTC-03:00) Punta Arenas |
| Saint-Pierre-i téli idő | (UTC-03:00) Saint-Pierre és Miquelon |
| Bahiai téli idő | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Egyezményes világidő-02 |
| Közép-atlanti téli idő | (UTC-02:00) Közép-atlanti időzóna – régi |
| Azori-szigeteki téli idő | (UTC-01:00) Azori-szigetek |
| Zöld-foki-szigeteki téli idő | (UTC-01:00) Cabo Verde |
| UTC | (UTC) Egyezményes világidő |
| Greenwichi téli idő | (UTC+00:00) Dublin, Edinburgh, Lisszabon, London |
| Greenwichi téli idő | (UTC+00:00) Monrovia, Reykjavik |
| W. Európai téli idő | (UTC+01:00) Amszterdam, Berlin, Bern, Róma, Stockholm, Bécs |
| Közép-európai téli idő | (UTC+01:00) Belgrád, Pozsony, Budapest, Ljubljana, Prága |
| Francia téli idő | (UTC+01:00) Brüsszel, Koppenhága, Madrid, Párizs |
| Marokkói téli idő | (UTC+01:00) Casablanca |
| Sao Tome-i téli idő | (UTC+01:00) Sao Tome |
| Közép-európai téli idő | (UTC+01:00) Szarajevó, Szkopje, Varsó, Zágráb |
| W. Közép-afrikai téli idő | (UTC+01:00) Nyugat-Közép-Afrika |
| Jordániai téli időszámítás | (UTC+02:00) Amman |
| Athéni téli idő | (UTC+02:00) Athén, Bukarest |
| Közel-keleti téli időszámítás | (UTC+02:00) Bejrút |
| Egyiptomi téli idő | (UTC+02:00) Kairó |
| E. Európai téli idő | (UTC+02:00) Chisinau |
| Szíriai téli idő | (UTC+02:00) Damaszkusz |
| Nyugati téli idő | (UTC+02:00) Gáza, Hebron |
| Dél-afrikai téli idő | (UTC+02:00) Harare, Pretoria |
| Finn, lett, észt téli idő | (UTC+02:00) Helsinki, Kijev, Riga, Szófia, Tallinn, Vilnius |
| Izraeli normál idő | (UTC+02:00) Jeruzsálem |
| Kalinyingrádi téli idő | (UTC+02:00) Kalinyingrád |
| Szudáni téli idő | (UTC+02:00) Kartúm |
| Líbiai téli idő | (UTC+02:00) Tripoli |
| Namíbiai téli idő | (UTC+02:00) Windhoek |
| Bagdadi téli idő | (UTC+03:00) Bagdad |
| Törökországi téli idő | (UTC+03:00) Isztambul |
| Arab téli idő | (UTC+03:00) Kuvait, Riyadh |
| Belarusz téli idő | (UTC+03:00) Minszk |
| Orosz téli idő | (UTC+03:00) Moszkva, Szentpétervár |
| E. Afrikai téli idő | (UTC+03:00) Nairobi |
| Iráni téli idő | (UTC+03:30) Teherán |
| Arábiai téli idő | (UTC+04:00) Abu Dhabi, Muscat |
| Asztraháni téli idő | (UTC+04:00) Asztrahán, Uljanovszk |
| Azerbajdzsáni téli időszámítás | (UTC+04:00) Baku |
| Russia Time Zone 3 | (UTC+04:00) Izsevszk, Szamara |
| Mauritiusi téli idő | (UTC+04:00) Port Louis |
| Szaratovi téli idő | (UTC+04:00) Szaratov |
| Grúz téli idő | (UTC+04:00) Tbiliszi |
| Volgográdi téli idő | (UTC+04:00) Volgográd |
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
| Omszki téli idő | (UTC+06:00) Omszk |
| Myanmari téli idő | (UTC+06:30) Yangon (Rangun) |
| Délkelet-ázsiai téli idő | (UTC+07:00) Bangkok, Hanoi, Jakarta |
| Altaji téli idő | (UTC+07:00) Barnaul, Gorno-Altajszk |
| W. Mongolia Standard Time | (UTC+07:00) Hovd |
| North Asia Standard Time | (UTC+07:00) Krasznojarszk |
| N. Közép-ázsiai téli idő | (UTC+07:00) Novoszibirszk |
| Tomszki téli idő | (UTC+07:00) Tomszk |
| Kínai téli idő | (UTC+08:00) Peking, Chongqing, Hongkong (KKT), Urumqi |
| Észak-ázsiai keleti téli idő | (UTC+08:00) Irkutszk |
| Szingapúri téli idő | (UTC+08:00) Kuala Lumpur, Szingapúr |
| W. Ausztráliai téli idő | (UTC+08:00) Perth |
| Tajpeji téli idő | (UTC+08:00) Taipei |
| Ulánbátori téli idő | (UTC+08:00) Ulánbátor |
| Ausztrál középnyugati téli idő | (UTC+08:45) Eucla |
| Transzbajkáli téli idő | (UTC+09:00) Csita |
| Tokiói téli idő | (UTC+09:00) Oszaka, Szapporo, Tokió |
| Észak-koreai téli idő | (UTC+09:00) Phenjan |
| Koreai téli idő | (UTC+09:00) Szöul |
| Jakutszki téli idő | (UTC+09:00) Jakutszk |
| CEN. Ausztráliai téli idő | (UTC+09:30) Adelaide |
| Ausztráliai közép téli idő | (UTC+09:30) Darwin |
| E. Ausztráliai téli idő | (UTC+10:00) Brisbane |
| Ausztráliai keleti téli idő | (UTC+10:00) Canberra, Melbourne, Sydney |
| Nyugat-csendes-óceáni téli idő | (UTC+10:00) Guam, Port Moresby |
| Tasmániai téli idő | (UTC+10:00) Hobart |
| Vlagyivosztoki téli idő | (UTC+10:00) Vlagyivosztok |
| Lord Howe-szigeti téli idő | (UTC+10:30) Lord Howe-sziget |
| Bougainville-szigeti téli idő | (UTC+11:00) Bougainville sziget |
| Russia Time Zone 10 | (UTC+11:00) Csokurdah |
| Magadáni téli idő | (UTC+11:00) Magadan |
| Norfolk-szigeti téli idő | (UTC+11:00) Norfolk-sziget |
| Szahalini téli idő | (UTC+11:00) Szahalin |
| Közép-csendes-óceáni téli idő | (UTC+11:00) Salamon-szigetek, Új-Kaledónia |
| 11. orosz időzóna | (UTC+12:00) Anadir, Petropavlovszk-Kamcsatszkij |
| Új-zélandi téli idő | (UTC+12:00) Auckland, Wellington |
| UTC+12 | (UTC+12:00) Egyezményes világidő+12 |
| Fidzsi-szigeteki téli idő | (UTC+12:00) Fidzsi |
| Kamcsatkai téli idő | (UTC+12:00) Petropavlovszk-Kamcsatszkij - régi |
| Chatham-szigeteki téli idő | (UTC+12:45) Chatham-szigetek |
| UTC+13 | (UTC+13:00) Egyezményes világidő+13 |
| Tongai téli idő | (UTC+13:00) Nuku'alofa |
| Szamoai téli idő | (UTC+13:00) Szamoa |
| Line-szigetek téli idő | (UTC+14:00) Kiritimati-sziget |

## <a name="see-also"></a>Lásd még

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)