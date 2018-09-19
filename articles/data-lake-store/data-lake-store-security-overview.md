---
title: Az Azure Data Lake Storage Gen1 biztonságának áttekintése |} A Microsoft Docs
description: Megismerheti, hogyan történik az Azure Data Lake Storage Gen1 a biztonságosabb big data store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebd5b2ac-c5cc-46d4-9cfd-1a1ee70024c2
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 4da387abe24318a29472c11dffa7aac67192408c
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46297423"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Biztonság az Azure Data Lake Storage Gen1
Sok vállalat is kihasználhatja a big data-analitika, amelyekkel az intelligens döntések üzleti elemzések készítése. Egy szervezet rendelkezhet egy összetett és szabályozott környezettel, és az egyre növekvő számú különböző felhasználók. A vállalatok számára, győződjön meg arról, hogy kritikus fontosságú üzleti adatokat tárolja biztonságosabban, és egyéni felhasználók számára a hozzáférést a megfelelő szintű, elengedhetetlen, hogy. Az Azure Data Lake Storage Gen1 célja biztonsági követelményeknek. Ez a cikk többek között a Data Lake Storage Gen1 biztonsági funkcióinak bemutatása:

* Hitelesítés
* Engedélyezés
* A hálózatok elkülönítéséhez
* Adatvédelem
* Naplózás

## <a name="authentication-and-identity-management"></a>Hitelesítés és az identitáskezeléshez
Hitelesítés az a folyamat, amellyel a felhasználó identitásának ellenőrzése, amikor a felhasználó használja a Data Lake Storage Gen1 vagy minden olyan szolgáltatás, amely a Data Lake Storage Gen1 csatlakozik. Data Lake Storage Gen1 használja az identitáskezeléshez és hitelesítéshez, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), egy átfogó identitás- és hozzáférés kezelési felhőalapú megoldás, amely egyszerűbbé teszi a felhasználók és csoportok kezelését.

Lehet, hogy minden Azure-előfizetés társítva az Azure Active Directory példányát. Csak a felhasználók és az Azure Active Directory szolgáltatásban definiált szolgáltatásidentitások elérheti a Data Lake Storage Gen1 fiók az Azure Portalon, a parancssori eszközök vagy ügyfélalkalmazások keresztül a szervezet a Data Lake használatával hoz létre Tárolási Gen1 SDK-t. Az Azure Active Directoryval, a központi hozzáférés-vezérlési mechanizmus fő előnyei a következők:

* Identitás-életciklus-felügyelete egyszerűbb. Egy felhasználó vagy szolgáltatás (a szolgáltatásnév-identitás) identitásának gyorsan létrehozhatók és gyorsan visszavont egyszerűen törlése vagy letiltja a fiókot a címtárban.
* Többtényezős hitelesítés [A multi-factor authentication](../active-directory/authentication/multi-factor-authentication.md) egy további biztonsági réteget nyújt a felhasználói bejelentkezéseket és tranzakciókat.
* A hitelesítés egy szabványos protokoll nevű nyílt, például az OAuth vagy OpenID keresztül bármilyen ügyféltől.
* Összevonás az enterprise directory services és a felhőalapú identitás-szolgáltatóktól.

## <a name="authorization-and-access-control"></a>Engedélyezési és hozzáférés-vezérlés
Miután az Azure Active Directoryval hitelesíti a felhasználót, hogy a felhasználó hozzáférhessen a Data Lake Storage Gen1, a engedélyeztetési vezérlőit hozzáférési engedélyek a Data Lake Storage Gen1. Data Lake Storage Gen1 fiókhoz kapcsolódó és a kapcsolódó adatok tevékenységek engedélyezési elkülöníti a következő módon:

* [Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) fiókok kezelése az Azure által biztosított (RBAC)
* POSIX ACL a tároló adatainak elérése

### <a name="rbac-for-account-management"></a>RBAC használata a fiókok kezelése
Négy alapvető szerepkörök alapértelmezés szerint a Data Lake Storage Gen1 vannak definiálva. A szerepkörök lehetővé teszik a különböző műveletek a Data Lake Storage Gen1-fiók az Azure portal, PowerShell-parancsmagok és a REST API-kon keresztül. A tulajdonos és közreműködő szerepkört a fiókhoz hajthat végre számos felügyeleti feladatot. Az Olvasó szerepkör rendelhet a felhasználók, akik csak a fiók felügyeleti adatok megtekintéséhez.

![RBAC-szerepkörök](./media/data-lake-store-security-overview/rbac-roles.png "RBAC-szerepkörök")

Vegye figyelembe, hogy fiókkezeléshez hozzárendelve, bár egyes szerepkörök adatokhoz való hozzáférés befolyásolja. Hozzáférés-vezérlési listák segítségével szabályozza a hozzáférést egy felhasználó a fájlrendszerben végrehajtható műveleteket kell. Az alábbi táblázat a management rights és az adatok hozzáférési jogosultságokat az alapértelmezett szerepköröket összegzését jeleníti meg.

| Szerepkörök | A rights Management | Adat-hozzáférési jogosultságok | Magyarázat |
| --- | --- | --- | --- |
| Nincs hozzárendelt szerepkör |None |ACL szabályozzák. |A felhasználó nem használható az Azure portal vagy Azure PowerShell-parancsmagok keresse meg a Data Lake Storage Gen1. A felhasználó csak a parancssori eszközöket használhatja. |
| Tulajdonos |Összes |Összes |A tulajdonosi szerepkör felügyelő. Ez a szerepkör mindent felügyelhetnek, és az adatok teljes hozzáféréssel rendelkezik. |
| Olvasó |Csak olvasás |ACL szabályozzák. |Az Olvasó szerepkörhöz, aki mindent megtekinthet kapcsolatos fiókok kezelése, például, hogy melyik felhasználó melyik szerepkör van rendelve. Az Olvasó szerepkör nem végezze el a módosításokat. |
| Közreműködő |Mindenhol, kivéve a szerepkörök hozzáadása és eltávolítása |ACL szabályozzák. |A közreműködő szerepkört egy olyan fiók, például a központi telepítések és létrehozása és a riasztások kezelése egyes funkcióit kezelheti. A közreműködő szerepkört nem adhat hozzá, és távolítsa el a szerepköröket. |
| Felhasználói hozzáférés rendszergazdája |Hozzáadhat és eltávolíthat a szerepkörök |ACL szabályozzák. |A felhasználói hozzáférés rendszergazdája szerepkör kezelheti a felhasználói fiókokat a hozzáférést. |

Útmutatásért lásd: [felhasználók vagy biztonsági csoportok hozzárendelése a Data Lake Storage Gen1 fiókokhoz](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Hozzáférés-vezérlési listák használata a fájlrendszerek műveletek
Data Lake Storage Gen1 hierarchikus fájlrendszer például a Hadoop elosztott fájlrendszer (HDFS), és támogatja [POSIX ACL-ek](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Azt szabályozza, Olvasás (r), írás (w) és végrehajtás (x)-erőforrások a tulajdonos szerepkör, a tulajdonosok csoport és más felhasználók és csoportok számára. A Data Lake Storage Gen1 hozzáférés-vezérlési listák is engedélyezhető a legfelső szintű mappához, az almappák és az egyes fájlokon. A hozzáférés-vezérlési listák Data Lake Storage Gen1 összefüggésben működéséről további információkért lásd: [hozzáférés-vezérlés a Data Lake Storage Gen1](data-lake-store-access-control.md).

Azt javasoljuk, hogy megadhat ACL-ek több felhasználó használatával [biztonsági csoportok](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Felhasználók hozzáadása az egy biztonsági csoportot, és rendelje hozzá egy fájlhoz vagy mappához tartozó ACL-ek biztonsági csoporthoz. Ez akkor hasznos, ha lehetővé szeretné tenni hozzárendelt engedélyeket, mert a hozzárendelt engedélyekhez 28 bejegyzések maximális. Azure Active Directory biztonsági csoportok használatával a Data Lake Storage Gen1 tárolt adatai nagyobb védelemben részesüljenek kapcsolatos további információkért lásd: [felhasználók vagy biztonsági csoport hozzárendelése ACL-ként a Data Lake Storage Gen1 fájlrendszer](data-lake-store-secure-data.md#filepermissions).

![Hozzáférési engedélyek listában](./media/data-lake-store-security-overview/adl.acl.2.png "hozzáférési engedélyek listázása")

## <a name="network-isolation"></a>A hálózatok elkülönítéséhez
Data Lake Storage Gen1 segítségével az Ön hálózati szintű hozzáférés-vezérlését könnyíti meg. Tűzfalak létrehozása és a egy IP-címtartományt definiálása a megbízható ügyfeleket. Csak a definiált tartományon belüli IP-cím rendelkező ügyfelek IP-címtartományt, Data Lake Storage Gen1 csatlakozhat.

![Tűzfalbeállítások és IP-hozzáférési](./media/data-lake-store-security-overview/firewall-ip-access.png "tűzfalbeállítások és IP-cím")

## <a name="data-protection"></a>Adatvédelem
Data Lake Storage Gen1 védi az adatok teljes életciklusa során. Az adatok továbbításához a Data Lake Storage Gen1 az iparági szabványnak megfelelő Transport Layer Security (TLS 1.2) protokollt használja a hálózaton keresztül védi az adatait.

![A Data Lake Storage Gen1 titkosítási](./media/data-lake-store-security-overview/adls-encryption.png "titkosítást a Data Lake Storage Gen1")

Data Lake Storage Gen1 is biztosít a fiókban tárolt adatok titkosítását. Dönthet úgy, hogy titkosítja az adatokat, vagy választhatja a titkosítás nélküli lehetőséget. Amennyiben a titkosításhoz, a Data Lake Storage Gen1 tárolt adatok a rendszer titkosítja, mielőtt állandó adathordozón tárolná. Ebben az esetben a Data Lake Storage Gen1 automatikusan titkosítja az adatokat, és mindig visszafejti az adatokat lekérés, előtt, így teljes mértékben átlátható az ügyfélnek, az adatok elérése. Nem történik kód változás az ügyféloldalon kell adatok titkosítása/visszafejtése.

Kulcskezelés, a Data Lake Storage Gen1 kétféle módon biztosítja a titkosítási főkulcsok (MEK), amelyek szükségesek a visszafejtés, a Data Lake Storage Gen1 tárolt adatokat kezeléséhez. Vagy engedélyezheti a Data Lake Storage Gen1 kezeléséről a MEK, vagy is megtartja a MEK az Azure Key Vault-fiók használatával tulajdonjogát. Kulcskezelés módját egy Data Lake Storage Gen1 fiók létrehozásakor adja meg. További információk a titkosítással kapcsolatos konfigurációk megadására: [Azure Data Lake Storage Gen1 használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Tevékenység és a diagnosztikai naplók
Tevékenység vagy a diagnosztikai naplók függően e Ön által keresett naplók fiók felügyelettel kapcsolatos tevékenységek és adatokkal kapcsolatos tevékenységeket is használhatja.

* Fiók felügyeleti kapcsolatos tevékenységeket az Azure Resource Manager API-k használatával, és az Azure Portalon keresztül tevékenységeket tartalmazó naplók illesztett.
* Adatokkal kapcsolatos tevékenységeket WebHDFS REST API-k, és az Azure Portalon keresztül a diagnosztikai naplók illesztett.

### <a name="activity-log"></a>Tevékenységnapló
Való megfelelés a szabályozásoknak, egy szervezet lehet szükség megfelelő naplók fiók felügyeleti tevékenységek, ha és elemezhetik a meghatározott incidenseket. Data Lake Storage Gen1 rendelkezik beépített figyelés, és az összes fiók felügyeleti tevékenységeket naplózza.

A fiók a felügyeleti napló ellenőrzését megtekintheti, és válassza ki a naplózni kívánt oszlopokat. A Tevékenységnaplók az Azure Storage is exportálhat.

![Tevékenységnapló](./media/data-lake-store-security-overview/activity-logs.png "tevékenységnapló")

További információ a vizsgálati naplók: [megtekintése az erőforrásokon végzett műveletek naplózásához tevékenységi naplóit](../azure-resource-manager/resource-group-audit.md).

### <a name="diagnostics-logs"></a>Diagnosztikai naplók
Adatok hozzáférés naplózása és az Azure Portal diagnosztikai naplózás engedélyezése és a egy Azure Blob storage-fiókot, egy eseményközpontba, vagy a Log Analytics naplók küldése.

![Diagnosztikai naplók](./media/data-lake-store-security-overview/diagnostic-logs.png "diagnosztikai naplók")

A diagnosztikai naplók a Data Lake Storage Gen1 további információkért lásd: [diagnosztikai naplóinak elérése a Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Összegzés
Nagyvállalati ügyfeleink kereslet egy data analytics felhőalapú platform, amely biztonságos és könnyen használható. Data Lake Storage Gen1 megtervezett segítségünkre ezeknek a követelményeknek, az Identitáskezelés és a hitelesítést az Azure Active Directory-integráció, ACL-alapú hitelesítést, a hálózatok elkülönítéséhez, az átvitel során, míg az inaktív adatok titkosítását, és a naplózási.

Ha meg szeretné tekinteni a új szolgáltatások a Data Lake Storage Gen1, küldjön nekünk visszajelzést a [Data Lake Storage Gen1 UserVoice fórumot](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
* [Ismerkedés a Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)

