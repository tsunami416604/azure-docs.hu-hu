---
title: Az Azure Data Lake Storage Gen1 biztonságának áttekintése | Microsoft dokumentumok
description: Ismerje meg, hogy az Azure Data Lake Storage Gen1 hogyan biztonságosabb big data tároló
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ebd5b2ac-c5cc-46d4-9cfd-1a1ee70024c2
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 4e640aa1cb02174c935c0f7c1d61ab2fca5ea046
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974576"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Biztonság az Azure Data Lake Storage gen1-ben
Számos vállalat kihasználja a big data-elemzés előnyeit az üzleti elemzések hez, hogy intelligens döntéseket hozhassanak. Egy szervezet összetett és szabályozott környezettel rendelkezhet, és egyre több különböző felhasználó vala. A vállalat számára létfontosságú, hogy a kritikus fontosságú üzleti adatok biztonságosabb anamtizálva, az egyes felhasználók számára biztosított megfelelő hozzáférési szinttel legyen ek. Az Azure Data Lake Storage Gen1 célja, hogy segítsen megfelelni ezeknek a biztonsági követelményeknek. Ebben a cikkben ismerje meg a Data Lake Storage Gen1 biztonsági képességeit, többek között a következőket:

* Hitelesítés
* Engedélyezés
* Hálózati elkülönítés
* Adatvédelem
* Naplózás

## <a name="authentication-and-identity-management"></a>Hitelesítés és identitáskezelés
A hitelesítés az a folyamat, amelynek során a felhasználó identitását ellenőrzi, ha a felhasználó kommunikál a Data Lake Storage Gen1 vagy bármely szolgáltatás, amely csatlakozik a Data Lake Storage Gen1. Az identitáskezelés és -hitelesítés érdekében a Data Lake Storage Gen1 az [Azure Active Directoryt](../active-directory/fundamentals/active-directory-whatis.md)használja, amely egy átfogó identitás- és hozzáférés-kezelési felhőmegoldás, amely leegyszerűsíti a felhasználók és csoportok kezelését.

Minden Azure-előfizetés társítható az Azure Active Directory egy példányához. Csak az Azure Active Directory szolgáltatásban definiált felhasználók és szolgáltatásidentitások férhetnek hozzá a Data Lake Storage Gen1 fiókhoz az Azure Portalon, a parancssori eszközökön vagy a szervezet által a Data Lake használatával építkező ügyfélalkalmazásokon keresztül. Tároló Gen1 SDK. Az Azure Active Directory központosított hozzáférés-vezérlési mechanizmusként való használatának fő előnyei a következők:

* Egyszerűsített identitáséletciklus-kezelés. A felhasználó vagy a szolgáltatás (egyszerű szolgáltatásidentitás) identitása gyorsan létrehozható és visszavonható a fiók egyszerű törlésével vagy letiltásával a címtárban.
* Többtényezős hitelesítés. [A többtényezős hitelesítés](../active-directory/authentication/multi-factor-authentication.md) további biztonsági réteget biztosít a felhasználói bejelentkezések és tranzakciók számára.
* Hitelesítés bármely ügyféltől egy szabványos nyílt protokollon keresztül, például OAuth vagy OpenID.
* Összevonás vállalati címtárszolgáltatásokkal és felhőalapú identitásszolgáltatókkal.

## <a name="authorization-and-access-control"></a>Engedélyezés és hozzáférés-vezérlés
Miután az Azure Active Directory hitelesíti a felhasználót, hogy a felhasználó hozzáférhessen a Data Lake Storage Gen1-hez, az engedélyezés szabályozza a Data Lake Storage Gen1 hozzáférési engedélyeit. A Data Lake Storage Gen1 a következő módon választja el a fiókkal kapcsolatos és az adatokkal kapcsolatos tevékenységek engedélyezését:

* Az Azure által [fiókkezeléshez biztosított szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC)
* POSIX ACL az adatok eléréséhez az üzletben

### <a name="rbac-for-account-management"></a>RBAC a fiókkezeléshez
Alapértelmezés szerint négy alapvető szerepkör van definiálva a Data Lake Storage Gen1-hez. A szerepkörök különböző műveleteket engedélyeznek egy Data Lake Storage Gen1-fiókon az Azure Portalon, a PowerShell-parancsmagokon és a REST API-kon keresztül. A tulajdonosi és közreműködői szerepkörök számos felügyeleti funkciót végezhetnek a fiókban. Az Olvasó szerepkört olyan felhasználókhoz rendelheti, akik csak a fiókkezelési adatokat tekintik meg.

![RBAC-szerepkörök](./media/data-lake-store-security-overview/rbac-roles.png "RBAC-szerepkörök")

Vegye figyelembe, hogy bár a szerepkörök fiókkezeléshez vannak rendelve, egyes szerepkörök hatással vannak az adatokhoz való hozzáférésre. A fájlrendszeren végrehajtható műveletekhez való hozzáférés szabályozásához a hozzáférés szabályozásához a hozzáférés szabályozásához a hozzáférés szükséges. Az alábbi táblázat az alapértelmezett szerepkörök kezelési jogainak és adathozzáférési jogainak összegzését mutatja be.

| Szerepkörök | Kezelési jogok | Adatelérési jogok | Magyarázat |
| --- | --- | --- | --- |
| Nincs hozzárendelve szerepkör |None |Az ACL által szabályozott |A felhasználó nem használhatja az Azure Portalon vagy az Azure PowerShell-parancsmagokkal a Data Lake Storage Gen1 tallózására. A felhasználó csak parancssori eszközöket használhat. |
| Tulajdonos |Összes |Összes |A tulajdonos szerepkör egy rendszergazda. Ez a szerepkör mindent kezelhet, és teljes hozzáféréssel rendelkezik az adatokhoz. |
| Olvasó |Csak olvasható |Az ACL által szabályozott |Az Olvasó szerepkör megtekintheti a fiókkezeléssel kapcsolatos mindent, például azt, hogy melyik felhasználó melyik szerepkörhöz van rendelve. Az Olvasó szerepkör nem hajt végre módosításokat. |
| Közreműködő |Az összes, kivéve a szerepkörök hozzáadását és eltávolítását |Az ACL által szabályozott |A közreműködői szerepkör kezelheti a fiók bizonyos aspektusait, például a központi telepítéseket, valamint a riasztások létrehozását és kezelését. A közreműködői szerepkör nem tud szerepköröket hozzáadni vagy eltávolítani. |
| Felhasználói hozzáférés rendszergazdája |Szerepkörök hozzáadása és eltávolítása |Az ACL által szabályozott |A Felhasználói hozzáférés rendszergazdája szerepkör kezelheti a fiókokhoz való felhasználói hozzáférést. |

További információt a [Felhasználók vagy biztonsági csoportok hozzárendelése a Data Lake Storage Gen1 fiókokhoz (Felhasználók vagy biztonsági csoportok hozzárendelése 1. fiókhoz) témakörben talál.](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts)

### <a name="using-acls-for-operations-on-file-systems"></a>AcL-k használata a fájlrendszereken végzett műveletekhez
A Data Lake Storage Gen1 egy hierarchikus fájlrendszer, mint a Hadoop Distributed File System (HDFS), és támogatja [a POSIX ACL-eket.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists) Szabályozza az olvasási (r), írási (w) és végrehajtási (x) engedélyeket a Tulajdonos szerepkör, a Tulajdonosok csoport, valamint más felhasználók és csoportok számára. A Data Lake Storage Gen1-ben az ACL-ek engedélyezhetők a gyökérmappában, az almappákban és az egyes fájlokon. Az ACL-ek működéséről a Data Lake Storage Gen1 környezetében további információt a [Hozzáférés-vezérlés a Data Lake Storage Gen1 programban című témakörben talál.](data-lake-store-access-control.md)

Azt javasoljuk, hogy biztonsági [csoportok](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)használatával adjon meg több felhasználószámára ACL-okat. Felhasználók hozzáadása egy biztonsági csoporthoz, majd egy fájl vagy mappa ACL-jainak hozzárendelése az adott biztonsági csoporthoz. Ez akkor hasznos, ha hozzárendelt engedélyeket szeretne megadni, mivel a hozzárendelt engedélyekhez legfeljebb 28 bejegyzés lehet. A Data Lake Storage Gen1 szolgáltatásban tárolt adatok Azure Active Directory biztonsági csoportjainak használatával történő hatékonyabb védelméről a [Felhasználók vagy biztonsági csoportok hozzárendelése a CL-ként a Data Lake Storage Gen1 fájlrendszerhez](data-lake-store-secure-data.md#filepermissions)című témakörben talál további információt.

![Hozzáférési engedélyek listázása](./media/data-lake-store-security-overview/adl.acl.2.png "Hozzáférési engedélyek listázása")

## <a name="network-isolation"></a>Hálózati elkülönítés
A Data Lake Storage Gen1 segítségével hálózati szinten szabályozhatja az adattárhoz való hozzáférést. Tűzfalakat hozhat létre, és ip-címtartományt határozhat meg a megbízható ügyfelek számára. IP-címtartomány esetén csak a megadott tartományon belüli IP-címmel rendelkező ügyfelek csatlakozhatnak a Data Lake Storage Gen1-hez.

![Tűzfal-beállítások és IP-hozzáférés](./media/data-lake-store-security-overview/firewall-ip-access.png "Tűzfal beállításai és IP-címe")

## <a name="data-protection"></a>Adatvédelem
A Data Lake Storage Gen1 az adatok teljes életciklusa során védelmet nyújt. Az átvitel alatt álló adatok esetében a Data Lake Storage Gen1 az iparági szabványnak megfelelő Transport Layer Security (TLS 1.2) protokollt használja a hálózaton keresztüli adatok védelmére.

![Titkosítás a Data Lake Storage Gen1-ben](./media/data-lake-store-security-overview/adls-encryption.png "Titkosítás a Data Lake Storage Gen1-ben")

A Data Lake Storage Gen1 titkosítást is biztosít a fiókban tárolt adatokhoz. Dönthet úgy, hogy titkosítja az adatokat, vagy választhatja a titkosítás nélküli lehetőséget. Ha engedélyezi a titkosítást, a Data Lake Storage Gen1-ben tárolt adatok titkosítva vannak az állandó adathordozón való tárolás előtt. Ebben az esetben a Data Lake Storage Gen1 automatikusan titkosítja az adatokat a megpersistnivaló előtt, és visszafejti az adatokat a lekérés előtt, így teljesen átlátható az adatokhoz hozzáférő ügyfél számára. Az ügyféloldalon nincs szükség kódmódosításra az adatok titkosításához/visszafejtéséhez.

A kulcskezeléshez a Data Lake Storage Gen1 két módot biztosít a fő titkosítási kulcsok (MEKs) kezelésére, amelyek a Data Lake Storage Gen1-ben tárolt adatok visszafejtéséhez szükségesek. A Data Lake Storage Gen1 kezelheti a MEK-ket, vagy dönthet úgy, hogy az Azure Key Vault-fiókjával megtartja a MEK-k tulajdonjogát. A Data Lake Storage Gen1 fiók létrehozásakor adja meg a kulcskezelés módját. A titkosítással kapcsolatos konfigurációk biztosításáról az [Azure Data Lake Storage Gen1 használatának első lépései az Azure Portal használatával című témakörben](data-lake-store-get-started-portal.md)talál további információt.

## <a name="activity-and-diagnostic-logs"></a>Tevékenység és diagnosztikai naplók
Tevékenység- vagy diagnosztikai naplókat használhat attól függően, hogy fiókkezelési tevékenységekhez vagy adatokkal kapcsolatos tevékenységekhez keres-e naplókat.

* A fiókkezeléssel kapcsolatos tevékenységek az Azure Resource Manager API-kat használják, és a tevékenységnaplókon keresztül kerülnek felszínre az Azure Portalon.
* Az adatokkal kapcsolatos tevékenységek webHDFS REST API-kat használnak, és diagnosztikai naplókon keresztül kerülnek felszínre az Azure Portalon.

### <a name="activity-log"></a>Tevékenységnapló
A szabályozásoknak való megfelelés érdekében a szervezet nek megfelelő naplózási nyomvonalat kell végeznie a számlakezelési tevékenységekről, ha meghatározott incidensekbe kell ásnia. A Data Lake Storage Gen1 beépített figyeléssel rendelkezik, és naplózza az összes fiókkezelési tevékenységet.

A fiókkezelési naplózási naplók esetében tekintse meg és válassza ki a naplózni kívánt oszlopokat. A tevékenységnaplókat az Azure Storage-ba is exportálhatja.

![Tevékenységnapló](./media/data-lake-store-security-overview/activity-logs.png "Tevékenységnapló")

A tevékenységnaplók kal kapcsolatos munkáról az [Erőforrásokkal kapcsolatos műveletek naplózásához szükséges tevékenységnaplók megtekintése című](../azure-resource-manager/management/view-activity-logs.md)témakörben talál további információt.

### <a name="diagnostics-logs"></a>Diagnosztikai naplók
Engedélyezheti az adatok eléréséhez naplózásés diagnosztikai naplózás az Azure Portalon, és küldje el a naplókat egy Azure Blob storage-fiók, egy eseményközpont vagy az Azure Monitor naplók.

![Diagnosztikai naplók](./media/data-lake-store-security-overview/diagnostic-logs.png "Diagnosztikai naplók")

A data lake storage gen1 diagnosztikai naplókkal való együttműködésről a [Data Lake Storage Gen1 diagnosztikai naplóinak elérése című témakörben talál](data-lake-store-diagnostic-logs.md)további információt.

## <a name="summary"></a>Összefoglalás
A nagyvállalati ügyfelek biztonságos és könnyen használható adatelemzési felhőplatformot igényelnek. A Data Lake Storage Gen1 célja, hogy az Azure Active Directory-integráció, az ACL-alapú engedélyezés, a hálózati elkülönítés, az átvitel és nyugalom alatt tárolt adattitkosítás, valamint a naplózás révén segítsen kezelni ezeket a követelményeket.

Ha új funkciókat szeretne látni a Data Lake Storage Gen1-ben, küldje el visszajelzését a [Data Lake Storage Gen1 UserVoice fórumon.](https://feedback.azure.com/forums/327234-data-lake)

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake storage gen1 áttekintése](data-lake-store-overview.md)
* [Ismerkedés a Data Lake Storage Gen1 szolgáltatással](data-lake-store-get-started-portal.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
