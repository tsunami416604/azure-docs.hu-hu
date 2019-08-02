---
title: Azure SQL Database felügyelt példány időzónái | Microsoft Docs "
description: Tudnivalók Azure SQL Database felügyelt példány időzóna-specifikus jellemzőiről
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 07/05/2019
ms.openlocfilehash: 33c844374d6d2b8e64cde6c7c9633e54a292d95f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567277"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Azure SQL Database felügyelt példány időzónái

Az egyezményes világidő (UTC) a felhőalapú megoldások adatszintjéhez ajánlott időzóna. Azure SQL Database felügyelt példány a dátum-és időértékeket tároló meglévő alkalmazások igényeinek kielégítésére, valamint a dátum-és időfüggvények meghívására is lehetőséget biztosít az adott időzóna implicit környezetében.

A T-SQL függvények, például a [getdate ()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) vagy a CLR-kód betartják a példány szintjén beállított időzónát. SQL Server Agent a feladatok a példány időzónája szerint is követik az ütemtervet.

  >[!NOTE]
  > A felügyelt példány az egyetlen olyan Azure SQL Database üzembe helyezési lehetősége, amely támogatja az időzóna-beállítást. Egyéb üzembe helyezési lehetőségek mindig az UTC szerint járjanak el.
Ha [](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) a dátum-és időadatokat nem UTC időzónában szeretné értelmezni, használja az időzónát egy és készletezett SQL-adatbázisokban.

## <a name="supported-time-zones"></a>Támogatott időzónák

A támogatott időzónák készlete örökölt a felügyelt példány mögöttes operációs rendszertől. Rendszeresen frissül az új Időzóna-definíciók beszerzése és a meglévők változásainak tükrözése.

A [nyári időszámítási idő/időzóna-változások házirendje](https://aka.ms/time) az 2010-ig terjedő korábbi pontosságot biztosítja.

A támogatott időzónák nevét tartalmazó lista a [sys. time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) rendszernézeten keresztül érhető el.

## <a name="set-a-time-zone"></a>Időzóna beállítása

A felügyelt példányok időzónája a példány létrehozásakor állítható be. Az alapértelmezett időzóna UTC.

  >[!NOTE]
  > Egy meglévő felügyelt példány időzónája nem módosítható.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Az időzóna beállítása a Azure Portal

Új példány paramétereinek megadásakor válasszon ki egy időzónát a támogatott időzónák listájából.
  
![Időzóna beállítása a példány létrehozásakor](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Adja meg az timezoneId tulajdonságot a [Resource Manager](https://aka.ms/sql-mi-create-arm-posh) -sablonban az időzóna beállításához a példány létrehozásakor.

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

## <a name="check-the-time-zone-of-an-instance"></a>Példány időzónájának keresése

A [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) függvény a példány időzónájának megjelenített nevét adja vissza.

## <a name="cross-feature-considerations"></a>Szolgáltatások közötti megfontolások

### <a name="restore-and-import"></a>Visszaállítás és importálás

Visszaállíthat egy biztonságimásolat-fájlt, vagy importálhat egy felügyelt példányra egy példányból vagy egy másik időzóna-beállításokkal rendelkező kiszolgálóról. Ügyeljen rá, hogy körültekintően járjon el. Elemezheti az alkalmazások viselkedését és a lekérdezések és jelentések eredményét, ugyanúgy, mint amikor két SQL Server példány közötti adatátvitelt végez különböző időzóna-beállításokkal.

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

<del>Ha időponthoz tartozó visszaállítást végez, a visszaállítási idő UTC-időként lesz értelmezve. Ezzel a beállítással elkerülhetők a nyári időmegtakarítás és a lehetséges változások miatti félreértések.<del>

 >[!WARNING]
  > A jelenlegi viselkedés nem áll összhangban a fenti utasítással, és a visszaállításhoz szükséges idő a forrás felügyelt példányának időzónája szerint van értelmezve, ahol az automatikus adatbázis-biztonsági mentések származnak. Dolgozunk azon, hogy kijavítsuk ezt a viselkedést, hogy az adott időpontot UTC-idő szerint értelmezze. További részletekért tekintse meg az [ismert problémák](sql-database-managed-instance-timezone.md#known-issues) című témakört.

### <a name="auto-failover-groups"></a>Automatikus feladatátvételi csoportok

Egy feladatátvételi csoportban lévő elsődleges és másodlagos példányon azonos időzóna használata nem kényszerített, de erősen ajánlott.

  >[!WARNING]
  > Azt javasoljuk, hogy a feladatátvételi csoportban lévő elsődleges és másodlagos példányhoz ugyanazt az időzónát használja. Néhány ritka forgatókönyv miatt nem kényszeríti ki ugyanazt az időzónát az elsődleges és a másodlagos példányok között. Fontos tisztában lenni azzal, hogy manuális vagy automatikus feladatátvétel esetén a másodlagos példány megtartja az eredeti időzónáját.

## <a name="limitations"></a>Korlátozások

- A meglévő felügyelt példány időzónája nem módosítható.
- A SQL Server Agenti feladatokból indított külső folyamatok nem veszik figyelembe a példány időzónáját.

## <a name="known-issues"></a>Ismert problémák

Az időpontra vonatkozó visszaállítási (PITR) művelet végrehajtásakor a rendszer a visszaállítási időt a felügyelt példányon beállított időzónaként értelmezi, ahol az adatbázis biztonsági másolatai automatikusan létrejönnek, bár a PITR portál lapja azt javasolja, hogy az idő UTC-ként legyen értelmezve.

Példa:

Tegyük fel, hogy az automatikus biztonsági mentések a keleti téli idő (UTC-5) időzóna-készletből származnak.
Az időponthoz tartozó visszaállítási portál lapja azt jelzi, hogy a visszaállításra kiválasztott idő UTC-idő:

![PITR helyi idővel a portál használatával](media/sql-database-managed-instance-timezone/02-pitr-with-nonutc-timezone.png)

A visszaállításhoz szükséges idő azonban a Kelet-téli időpontnak megfelelően van értelmezve, és ebben az adott példában az adatbázis a keleti téli időpontra, és nem UTC-időre lesz visszaállítva.

Ha az UTC időpontjában egy adott időpontra szeretné visszaállítani az időpontot, először a forrás példány időzónájában kell kiszámítani a megfelelő időpontot, és a portálon vagy a PowerShell-ben vagy a CLI-szkriptben használja ezt az időt.

## <a name="list-of-supported-time-zones"></a>Támogatott időzónák listája

| **Időzóna-azonosító** | **Időzóna megjelenítendő neve** |
| --- | --- |
| Dátumvonali téli idő | (UTC-12:00) Nemzetközi dátumválasztó vonal – nyugat |
| UTC-11 | (UTC-11:00) Egyezményes világidő-11 |
| Aleuti téli idő | (UTC-10:00) Aleut-szigetek |
| Hawaii téli idő | (UTC-10:00) Hawaii |
| Marquises-szigeteki téli idő | (UTC-09:30) Marquises-szigetek |
| Alaszkai téli idő | (UTC-09:00) Alaszka |
| UTC-09 | (UTC-09:00) Egyezményes világidő-09 |
| Csendes-óceáni téli idő (Mexikó) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Egyezményes világidő-08 |
| Csendes-óceáni téli idő | (UTC-08:00) Csendes-óceáni idő (Amerikai Egyesült Államok és Kanada) |
| Amerikai hegyi téli idő | (UTC-07:00) Arizona |
| Hegyi téli idő (Mexikó) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Hegyi téli idő | (UTC-07:00) Hegyi idő (Amerikai Egyesült Államok és Kanada) |
| Közép-amerikai téli idő | (UTC-06:00) Közép-Amerika |
| Amerikai középső téli idő | (UTC-06:00) Amerikai középidő (Amerikai Egyesült Államok és Kanada) |
| Húsvét-szigeti téli idő | (UTC-06:00) Húsvét-sziget |
| Téli középidő (Mexikó) | (UTC-06:00) Guadalajara, Mexikóváros, Monterrey |
| Kanadai téli középidő | (UTC-06:00) Saskatchewan |
| Dél-amerikai csendes-óceáni téli idő | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Keleti téli idő (Mexikó) | (UTC-05:00) Chetumal |
| Keleti téli idő | (UTC-05:00) Keleti idő (Amerikai Egyesült Államok és Kanada) |
| Haiti téli idő | (UTC-05:00) Haiti |
| Kubai téli idő | (UTC-05:00) Havanna |
| Amerikai keleti téli idő | (UTC-05:00) Indiana (kelet) |
| Turks-és Caicos-i téli idő | (UTC-05:00) Turks- és Caicos-szigetek |
| Paraguayi téli idő | (UTC-04:00) Asuncion |
| Atlanti-óceáni téli idő | (UTC-04:00) Atlanti-óceáni idő (Kanada) |
| Venezuela Standard Time | (UTC-04:00) Caracas |
| Közép-brazíliai idő | (UTC-04:00) Cuiaba |
| Dél-amerikai nyugati téli idő | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Csendes-óceáni dél-amerikai téli idő | (UTC-04:00) Santiago |
| Új-foundlandi téli idő | (UTC-03:30) Új-Fundland |
| Tocantinsi nyári idő | (UTC-03:00) Araguaina |
| E. Dél-amerikai téli idő | (UTC-03:00) Brazíliaváros |
| Dél-amerikai keleti téli idő | (UTC-03:00) Cayenne, Fortaleza |
| Argentin téli idő | (UTC-03:00) Buenos Aires |
| Grönlandi téli idő | (UTC-03:00) Grönland |
| Montevideoi téli idő | (UTC-03:00) Montevideo |
| Magallanesi téli idő | (UTC-03:00) Punta Arenas |
| Saint-Pierre-i téli idő | (UTC-03:00) Saint-Pierre és Miquelon |
| Bahiai téli idő | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Egyezményes világidő-02 |
| Közép-atlanti téli idő | (UTC-02:00) Közép-atlanti időzóna – régi |
| Azori-szigeteki téli idő | (UTC-01:00) Azori-szigetek |
| Cape Verdei téli idő | (UTC-01:00) Cabo Verde |
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
| Nyugati banki téli idő | (UTC+02:00) Gáza, Hebron |
| Dél-afrikai téli idő | (UTC+02:00) Harare, Pretoria |
| Finn, lett, észt téli idő | (UTC+02:00) Helsinki, Kijev, Riga, Szófia, Tallinn, Vilnius |
| Izraeli téli idő | (UTC+02:00) Jeruzsálem |
| Kalinyingrádi téli idő | (UTC+02:00) Kalinyingrád |
| Szudáni téli idő | (UTC+02:00) Kartúm |
| Líbiai téli idő | (UTC+02:00) Tripoli |
| Namíbiai téli idő | (UTC+02:00) Windhoek |
| Bagdadi téli idő | (UTC+03:00) Bagdad |
| Törökországi téli idő | (UTC+03:00) Isztambul |
| Arab téli idő | (UTC+03:00) Kuvait, Rijád |
| Belarusz téli idő | (UTC+03:00) Minszk |
| Orosz téli idő | (UTC+03:00) Moszkva, Szentpétervár |
| E. Afrikai téli idő | (UTC+03:00) Nairobi |
| Iráni téli idő | (UTC+03:30) Teherán |
| Arábiai téli idő | (UTC+04:00) Abu-Dzabi, Maszkat |
| Asztraháni téli idő | (UTC+04:00) Asztrahán, Uljanovszk |
| Azerbajdzsáni téli időszámítás | (UTC+04:00) Baku |
| Oroszországi idő 3. zóna | (UTC+04:00) Izsevszk, Szamara |
| Mauritiusi téli idő | (UTC+04:00) Port Louis |
| Szaratovi téli idő | (UTC+04:00) Szaratov |
| Grúz téli idő | (UTC+04:00) Tbiliszi |
| Volgográdi téli idő | (UTC+04:00) Volgográd |
| Kaukázusi téli idő | (UTC+04:00) Jereván |
| Afganisztáni téli idő | (UTC+04:30) Kabul |
| Nyugat-ázsiai téli idő | (UTC+05:00) Asgabat, Taskent |
| Jekatyerinburgi téli idő | (UTC+05:00) Jekatyerinburg |
| Pakisztáni téli idő | (UTC+05:00) Iszlámábád, Karacsi |
| Indiai téli idő | (UTC+05:30) Csennai, Kalkutta, Mumbai, Új-Delhi |
| Srí Lanka-i téli idő | (UTC+05:30) Sri Jayawardenepura |
| Nepáli téli idő | (UTC+05:45) Katmandu |
| Közép-ázsiai téli idő | (UTC+06:00) Asztana |
| Bangladesi téli idő | (UTC+06:00) Dakka |
| Omszki téli idő | (UTC+06:00) Omszk |
| Myanmari téli idő | (UTC+06:30) Yangon (Rangun) |
| Délkelet-ázsiai téli idő | (UTC+07:00) Bangkok, Hanoi, Dzsakarta |
| Altaji téli idő | (UTC+07:00) Barnaul, Gorno-Altajszk |
| W. Mongóliai téli idő | (UTC+07:00) Hovd |
| Észak-ázsiai téli idő | (UTC+07:00) Krasznojarszk |
| N. Közép-ázsiai téli idő | (UTC+07:00) Novoszibirszk |
| Tomszki téli idő | (UTC+07:00) Tomszk |
| Kínai téli idő | (UTC+08:00) Peking, Csungking, Hongkong, Urumcsi |
| Észak-ázsiai keleti téli idő | (UTC+08:00) Irkutszk |
| Szingapúri téli idő | (UTC+08:00) Kuala Lumpur, Szingapúr |
| W. Ausztráliai téli idő | (UTC+08:00) Perth |
| Tajpeji téli idő | (UTC+08:00) Tajpej |
| Ulánbátori téli idő | (UTC+08:00) Ulánbátor |
| Ausztrál középnyugati téli idő | (UTC+08:45) Eucla |
| Transzbajkáli téli idő | (UTC+09:00) Csita |
| Tokiói téli idő | (UTC+09:00) Oszaka, Szapporó, Tokió |
| Észak-koreai téli idő | (UTC+09:00) Phenjan |
| Koreai téli idő | (UTC+09:00) Szöul |
| Yakutsk téli idő | (UTC+09:00) Jakutszk |
| CEN. Ausztráliai téli idő | (UTC+09:30) Adelaide |
| Ausztráliai közép téli idő | (UTC+09:30) Darwin |
| E. Ausztráliai téli idő | (UTC+10:00) Brisbane |
| Ausztráliai keleti téli idő | (UTC+10:00) Canberra, Melbourne, Sydney |
| Nyugat-csendes-óceáni téli idő | (UTC+10:00) Guam, Port Moresby |
| Tasmániai téli idő | (UTC+10:00) Hobart |
| Vlagyivosztoki téli idő | (UTC+10:00) Vlagyivosztok |
| Lord Howe-szigeti téli idő | (UTC+10:30) Lord Howe-sziget |
| Bougainville-szigeti téli idő | (UTC+11:00) Bougainville-sziget |
| Orosz időzóna, 10 | (UTC+11:00) Csokurdah |
| Magadáni téli idő | (UTC+11:00) Magadán |
| Norfolk-szigeti téli idő | (UTC+11:00) Norfolk-sziget |
| Szahalini téli idő | (UTC+11:00) Szahalin |
| Közép-csendes-óceáni téli idő | (UTC+11:00) Salamon-szigetek, Új-Kaledónia |
| Orosz időzóna, 11 | (UTC+12:00) Anadir, Petropavlovszk-Kamcsatszkij |
| Új-zélandi téli idő | (UTC+12:00) Auckland, Wellington |
| UTC+12 | (UTC+12:00) Egyezményes világidő+12 |
| Fidzsi-szigeteki téli idő | (UTC+12:00) Fidzsi-szigetek |
| Kamchatka Standard Time | (UTC+12:00) Petropavlovszk-Kamcsatszkij – régi |
| Chatham-szigeteki téli idő | (UTC+12:45) Chatham-szigetek |
| UTC+13 | (UTC+13:00) Egyezményes világidő+13 |
| Tongai téli idő | (UTC+13:00) Nuku'alofa |
| Szamoai téli idő | (UTC+13:00) Szamoa |
| Line-szigetek téli idő | (UTC+14:00) Karácsony-sziget |

## <a name="see-also"></a>Lásd még 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
