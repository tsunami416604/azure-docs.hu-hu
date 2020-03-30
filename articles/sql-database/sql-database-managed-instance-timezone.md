---
title: Felügyelt példány időzónái
description: További információ az Azure SQL Database felügyelt példányának időzóna-sajátosságairól
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 09/03/2019
ms.openlocfilehash: 0dbed3db8e106b9bfe1b48ff2b9bc52840fc4c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256093"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Időzónák az Azure SQL Database felügyelt példányában

Koordinált világidő (UTC) az ajánlott időzóna az adatréteg a felhőalapú megoldások. Az Azure SQL Database felügyelt példány a dátum- és időértékeket, valamint a hívásdátum- és időfüggvényeket egy adott időzóna implicit környezetével rendelkező meglévő alkalmazások igényeinek megfelelő időzónákat is kínál.

A T-SQL függvények, például [a GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) vagy a CLR-kód betartják a példányszinten beállított időzónát. Az SQL Server Agent-feladatok is követik az ütemezéseket a példány időzónájának megfelelően.

  >[!NOTE]
  > A felügyelt példány az Azure SQL Database egyetlen olyan üzembe helyezési lehetősége, amely támogatja az időzóna-beállítást. A többi telepítési lehetőség mindig az UTC-t követi.
Használja [az AT TIME ZONE-t](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) egy- és készletezésű SQL-adatbázisokban, ha a dátum- és időadatokat nem UTC-időzónában kell értelmeznie.

## <a name="supported-time-zones"></a>Támogatott időzónák

A támogatott időzónák készlete a felügyelt példány alapjául szolgáló operációs rendszertől öröklődik. Rendszeresen frissíti, hogy új időzóna-definíciókat kapjon, és tükrözze a meglévők változásait.

[A nyári időszámítás/időzóna módosításai házirend](https://aka.ms/time) garantálja a korábbi pontosságot 2010-től.

A támogatott időzónák neveit tartalmazó lista a [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) rendszernézetben jelenik meg.

## <a name="set-a-time-zone"></a>Időzóna beállítása

A felügyelt példány oktatása csak a példány létrehozásakor állítható be. Az alapértelmezett időzóna az UTC.

  >[!NOTE]
  > Egy meglévő felügyelt példány időzónája nem módosítható.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Az időzóna beállítása az Azure Portalon keresztül

Amikor paramétereket ad meg egy új példányhoz, válasszon ki egy időzónát a támogatott időzónák listájából.
  
![Időzóna beállítása a példány létrehozása kor](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Adja meg az időzóna-azonosító tulajdonságot az [Erőforrás-kezelő sablonban](https://aka.ms/sql-mi-create-arm-posh) az időzóna beállításához a példány létrehozása során.

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

A timezoneId tulajdonság támogatott értékeinek listája a cikk végén található.

Ha nincs megadva, az időzóna UTC értékre van állítva.

## <a name="check-the-time-zone-of-an-instance"></a>Példány időzónájának ellenőrzése

A [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) függvény a példány időzónájának megjelenítendő nevét adja vissza.

## <a name="cross-feature-considerations"></a>Jellemzők ratiszempontjai

### <a name="restore-and-import"></a>Visszaállítás és importálás

A biztonságimásolat-fájlt visszaállíthatja, vagy adatokat importálhat egy felügyelt példányba egy példányból vagy egy különböző időzóna-beállításokkal rendelkező kiszolgálóról. Ügyeljen arra, hogy ezt óvatosan tegye. Elemezze az alkalmazás viselkedését, valamint a lekérdezések és jelentések eredményeit, ugyanúgy, mint amikor adatokat továbbít két SQL Server-példány között különböző időzóna-beállításokkal.

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

Amikor időponthoz ért visszaállítást hajt végre, a visszaállítás iódje UTC-időként lesz értelmezve. Ily módon elkerülhetők a nyári időszámítás és annak lehetséges változásai miatti kétértelműségek.

### <a name="auto-failover-groups"></a>Automatikus feladatátvételi csoportok

Ugyanazt az időzónát használja egy feladatátvételi csoportban egy elsődleges és másodlagos példányban, de erősen javasoljuk.

  >[!WARNING]
  > Javasoljuk, hogy használja ugyanazt az időzónát az elsődleges és másodlagos példány egy feladatátvételi csoportban. Bizonyos ritka használati esetek miatt az elsődleges és másodlagos példányok között az azonos időzónát megtartva nem érvényesül. Fontos megérteni, hogy manuális vagy automatikus feladatátvétel esetén a másodlagos példány megtartja az eredeti időzónát.

## <a name="limitations"></a>Korlátozások

- A meglévő felügyelt példány időzónája nem módosítható.
- Az SQL Server Agent-feladatokból indított külső folyamatok nem figyelik meg a példány időzónáját.

## <a name="list-of-supported-time-zones"></a>A támogatott időzónák listája

| **Időzóna-azonosító** | **Időzóna megjelenítendő neve** |
| --- | --- |
| Dátumvonal-téli idő | (UTC-12:00) Nemzetközi dátumvonal nyugat |
| UTC-11 | (UTC-11:00) Koordinált világidő-11 |
| Aileáti téli idő | (UTC-10:00) Aleut-szigetek |
| Hawaii téli idő | (UTC-10:00) Hawaii |
| Marquesas téli idő | (UTC-09:30) Marquesas-szigetek |
| Alaszkai téli idő | (UTC-09:00) Alaszka |
| UTC-09 | (UTC-09:00) Koordinált világidő-09 |
| Csendes-óceáni téli idő (Mexikó) | (UTC-08:00) Baja Kalifornia |
| UTC-08 | (UTC-08:00) Koordinált világidő-08 |
| Csendes-óceáni téli idő | (UTC-08:00) Csendes-óceáni idő (USA & Kanada) |
| Amerikai hegyi téli idő | (UTC-07:00) Arizona |
| Hegyi téli idő (Mexikó) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Hegyi téli idő | (UTC-07:00) Mountain Time (Amerikai egyesült államokbeli & Kanada) |
| Közép-amerikai téli idő | (UTC-06:00) Közép-Amerika |
| Közép-téli idő | (UTC-06:00) Central Time (Usa & Kanada) |
| Húsvét-szigeti téli idő | (UTC-06:00) Húsvét-sziget |
| Közép-téli idő (Mexikó) | (UTC-06:00) Guadalajara, Mexikóváros, Monterrey |
| Kanada közép-téli idő | (UTC-06:00) Saskatchewan |
| SA csendes-óceáni téli idő | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Keleti téli idő (Mexikó) | (UTC-05:00) Chetumal |
| Keleti téli idő | (UTC-05:00) Keleti idő (US & Kanada) |
| Haiti téli idő | (UTC-05:00) Haiti |
| Kubai téli idő | (UTC-05:00) Havanna |
| Amerikai keleti téli idő | (UTC-05:00) Indiana (kelet) |
| Turks- és caicos-i téli idő | (UTC-05:00) Turks- és Caicos-szigetek |
| Paraguayi téli idő | (UTC-04:00) Asuncion |
| Atlanti-óceáni téli idő | (UTC-04:00) Atlanti-óceáni idő (Kanada) |
| Venezuelai téli idő | (UTC-04:00) Caracas |
| Közép-brazil téli idő | (UTC-04:00) Cuiaba |
| SA nyugati téli idő | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Csendes-óceáni SA téli idő | (UTC-04:00) Santiago |
| Új-fundlandi téli idő | (UTC-03:30) Newfoundland |
| Tocantins téli idő | (UTC-03:00) Araguaina között |
| E. Dél-amerikai téli idő | (UTC-03:00) Brasilia |
| SA keleti téli idő | (UTC-03:00) Cayenne, Amerikai-Szent |
| Argentínai téli idő | (UTC-03:00) Buenos Aires városa |
| Grönlandi téli idő | (UTC-03:00) Grönland |
| Montevideo-i téli idő | (UTC-03:00) Montevideo |
| Magallanes téli idő | (UTC-03:00) Punta Aréna |
| Saint Pierre-i téli idő | (UTC-03:00) Saint Pierre és Miquelon |
| Bahia téli idő | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Koordinált világidő-02 |
| Közép-atlanti téli idő | (UTC-02:00) Közép-atlanti - Régi |
| Azori-szigetek téli idő | (UTC-01:00) Azori-szigetek |
| Zöld-foki-szigeteki téli idő | (UTC-01:00) Cabo Verde az. |
| UTC | (UTC) Egyezményes világidő |
| GMT téli idő | (UTC+00:00) Dublin, Edinburgh, Lisszabon, London |
| Greenwichi téli idő | (UTC+00:00) Monrovia, Reykjavik |
| W. Európai téli idő | (UTC+01:00) Amszterdam, Berlin, Bern, Róma, Stockholm, Bécs |
| Közép-európai téli idő | (UTC+01:00) Belgrád, Pozsony, Budapest, Ljubljana, Prága |
| Romantika Téli idő | (UTC+01:00) Brüsszel, Koppenhága, Madrid, Párizs |
| Marokkói téli idő | (UTC+01:00) Casablanca |
| Sao Tome téli idő | (UTC+01:00) Sao Tome |
| Közép-európai téli idő | (UTC+01:00) Szarajevó, Szkopje, Varsó, Zágráb |
| W. Közép-afrikai téli idő | (UTC+01:00) Nyugat-Közép-Afrika |
| Jordániai téli idő | (UTC+02:00) Amman |
| GTB téli idő | (UTC+02:00) Athén, Bukarest |
| Közel-keleti téli idő | (UTC+02:00) Bejrút |
| Egyiptomi téli idő | (UTC+02:00) Kairó |
| E. Európai téli idő | (UTC+02:00) Kisinyó |
| Szíriai téli idő | (UTC+02:00) Damaszkusz |
| Ciszjordániai téli idő | (UTC+02:00) Gáza, Hebron |
| Dél-afrikai téli idő | (UTC+02:00) Harare, Pretoria |
| FLE téli idő | (UTC+02:00) Helsinki, Kijev, Riga, Szófia, Tallinn, Vilnius |
| Izraeli téli idő | (UTC+02:00) Jeruzsálem |
| Kalinyingrádi téli idő | (UTC+02:00) Kaliningrad |
| Szudáni téli idő | (UTC+02:00) Kartúm |
| Líbiai téli idő | (UTC+02:00) Tripoli |
| Namíbiai téli idő | (UTC+02:00) Windhoek |
| Arab téli idő | (UTC+03:00) Bagdad |
| Törökország idáig | (UTC+03:00) Isztambul |
| Arab téli idő | (UTC+03:00) Kuvait, Rijád |
| Fehéroroszországi téli idő | (UTC+03:00) Minszk |
| Orosz téli idő | (UTC+03:00) Moszkva, Szentpétervár |
| E. Afrikai téli idő | (UTC+03:00) Nairobi |
| Iráni téli idő | (UTC+03:30) Teherán |
| Arab téli idő | (UTC+04:00) Abu-Dzabi, Muskotály |
| Asztrahán téli idő | (UTC+04:00) Asztrahán, Egyesült Királyság |
| Azerbajdzsáni téli idő | (UTC+04:00) Baku |
| Oroszország időzóna 3 | (UTC+04:00) Izhevsk, Szamara |
| Mauritiusi téli idő | (UTC+04:00) Port Louis |
| Szaratov téli idő | (UTC+04:00) Saratov |
| Grúz téli idő | (UTC+04:00) Tbiliszi |
| Volgogradi téli idő | (UTC+04:00) Volgograd |
| Kaukázusi téli idő | (UTC+04:00) Jereván |
| Afganisztán idáig | (UTC+04:30) Kabul |
| Nyugat-ázsiai téli idő | (UTC+05:00) Ashgabat, Taskent |
| Ekaterinburgi téli idő | (UTC+05:00) Ekaterinburg |
| Pakisztáni téli idő | (UTC+05:00) Iszlámábád, Karacsi |
| Indiai téli idő | (UTC+05:30) Chennai, Kolkata, Mumbai, Újdelhi |
| Srí Lanka-i téli idő | (UTC+05:30) Srí Jayawardenepura |
| Nepáli téli idő | (UTC+05:45) Kathmandu |
| Közép-ázsiai téli idő | (UTC+06:00) Astana |
| Bangladesi téli idő | (UTC+06:00) Dhaka |
| Omszk téli idő | (UTC+06:00) Omsk |
| Mianmari téli idő | (UTC+06:30) Yangon (Rangoon) |
| SE Ázsia téli idő | (UTC+07:00) Bangkok, Hanoi, Jakarta |
| Altai téli idő | (UTC+07:00) Barnaul, Gorno-Altaysk |
| W. Mongóliai téli idő | (UTC+07:00) Hovd között |
| Észak-ázsiai téli idő | (UTC+07:00) Krasnoyarsk |
| N. Közép-ázsiai téli idő | (UTC+07:00) Novosibirsk |
| Tomszk téli idő | (UTC+07:00) Tomszk |
| Kínai téli idő | (UTC+08:00) Peking, Chongqing, Hongkong, Urumqi |
| Észak-ázsiai-keleti téli idő | (UTC+08:00) Irkutsk |
| Szingapúri téli idő | (UTC+08:00) Kuala Lumpur, Szingapúr |
| W. Ausztrál téli idő | (UTC+08:00) Perth |
| Tajpej téli idő | (UTC+08:00) Taipei |
| Ulaanbaatar téli idő | (UTC+08:00) Ulánbátor |
| Aus Central W. téli idő | (UTC+08:45) Eucla között |
| Transbaikal téli idő | (UTC+09:00) Csita |
| Tokiói téli idő | (UTC+09:00) Oszaka, Szapporo, Tokió |
| Észak-koreai téli idő | (UTC+09:00) Phenjan |
| Koreai téli idő | (UTC+09:00) Szöul |
| Jakutszki téli idő | (UTC+09:00) Jakutszk |
| Cen. Ausztrál téli idő | (UTC+09:30) Adelaide |
| AUS közép-téli idő | (UTC+09:30) Darwin |
| E. Ausztrál téli idő | (UTC+10:00) Brisbane |
| AUS keleti téli idő | (UTC+10:00) Canberra, Melbourne, Sydney |
| Nyugat-csendes-óceáni téli idő | (UTC+10:00) Guam, Port Moresby |
| Tasmániai téli idő | (UTC+10:00) Hobart |
| Vlagyivosztok téli idő | (UTC+10:00) Vlagyivosztok |
| Lord Howe téli idő | (UTC+10:30) Lord Howe-sziget |
| Bougainville-i téli idő | (UTC+11:00) Bougainville-sziget |
| Oroszország időzóna 10 | (UTC+11:00) Chokurdakh között |
| Magadan-i téli idő | (UTC+11:00) Magadan |
| Norfolki téli idő | (UTC+11:00) Norfolk-sziget |
| Szahalin téli idő | (UTC+11:00) Szahalin |
| Közép-csendes-óceáni téli idő | (UTC+11:00) Solomon Is., Új-Kaledónia |
| Oroszország időzóna 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamcsatszkij |
| Új-zélandi téli idő | (UTC+12:00) Auckland, Wellington |
| UTC+12 | (UTC+12:00) Összehangolt világidő+12 |
| Fidzsi-szigeteki téli idő | (UTC+12:00) Fidzsi |
| Kamchatka téli idő | (UTC+12:00) Petropavlovsk-Kamchatsky - Régi |
| Chatham-szigetek téli idő | (UTC+12:45) Chatham-szigetek |
| UTC+13 | (UTC+13:00) Összehangolt világidő+13 |
| Tonga téli idő | (UTC+13:00) Nuku'alofa között |
| Szamoai téli idő | (UTC+13:00) Szamoa |
| Line-szigetek téli idő | (UTC+14:00) Kiritimati-sziget |

## <a name="see-also"></a>Lásd még 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [IDŐZÓNA (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
