---
title: A Data Lake Store biztonsági áttekintése |} A Microsoft Docs
description: Megismerheti, hogyan történik az Azure Data Lake Store a biztonságosabb big data store
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
ms.openlocfilehash: 3b836859dd2af45e84d0f53db06d2c7ab2828253
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057085"
---
# <a name="security-in-azure-data-lake-store"></a>Az Azure Data Lake Store biztonság
Sok vállalat is kihasználhatja a big data-analitika, amelyekkel az intelligens döntések üzleti elemzések készítése. Egy szervezet rendelkezhet egy összetett és szabályozott környezettel, és az egyre növekvő számú különböző felhasználók. A vállalatok számára, győződjön meg arról, hogy kritikus fontosságú üzleti adatokat tárolja biztonságosabban, és egyéni felhasználók számára a hozzáférést a megfelelő szintű, elengedhetetlen, hogy. Az Azure Data Lake Store célja biztonsági követelményeknek. Ez a cikk a Data Lake Store, biztonsági funkcióinak bemutatása többek között:

* Hitelesítés
* Engedélyezés
* A hálózatok elkülönítéséhez
* Adatvédelem
* Naplózás

## <a name="authentication-and-identity-management"></a>Hitelesítés és az identitáskezeléshez
Hitelesítés az a folyamat, mely szerint a felhasználó identitásának ellenőrzése, amikor a felhasználó használja a Data Lake Store vagy bármely szolgáltatás, amely csatlakozik a Data Lake Store. Data Lake Store használja az identitáskezeléshez és hitelesítéshez, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), egy átfogó identitás- és hozzáférés kezelési felhőalapú megoldás, amely egyszerűbbé teszi a felhasználók és csoportok kezelését.

Lehet, hogy minden Azure-előfizetés társítva az Azure Active Directory példányát. Csak a felhasználók és az Azure Active Directory szolgáltatásban definiált szolgáltatásidentitások elérheti a Data Lake Store-fiók az Azure Portalon, a parancssori eszközök vagy ügyfélalkalmazások keresztül a szervezet az Azure Data Lake használatával hoz létre Store SDK-t. Az Azure Active Directoryval, a központi hozzáférés-vezérlési mechanizmus fő előnyei a következők:

* Identitás-életciklus-felügyelete egyszerűbb. Egy felhasználó vagy szolgáltatás (a szolgáltatásnév-identitás) identitásának gyorsan létrehozhatók és gyorsan visszavont egyszerűen törlése vagy letiltja a fiókot a címtárban.
* Többtényezős hitelesítés [A multi-factor authentication](../active-directory/authentication/multi-factor-authentication.md) egy további biztonsági réteget nyújt a felhasználói bejelentkezéseket és tranzakciókat.
* A hitelesítés egy szabványos protokoll nevű nyílt, például az OAuth vagy OpenID keresztül bármilyen ügyféltől.
* Összevonás az enterprise directory services és a felhőalapú identitás-szolgáltatóktól.

## <a name="authorization-and-access-control"></a>Engedélyezési és hozzáférés-vezérlés
Miután az Azure Active Directoryval hitelesíti a felhasználót, hogy a felhasználó hozzáférhet az Azure Data Lake Store, a engedélyeztetési vezérlőit hozzáférési engedélyek a Data Lake Store. Data Lake Store fiókhoz kapcsolódó és a kapcsolódó adatok tevékenységek engedélyezési elkülöníti a következő módon:

* [Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) fiókok kezelése az Azure által biztosított (RBAC)
* POSIX ACL a tároló adatainak elérése

### <a name="rbac-for-account-management"></a>RBAC használata a fiókok kezelése
Négy alapvető szerepkörök határozzák meg a Data Lake Store alapértelmezett. A szerepkörök lehetővé teszik a különböző műveletek a Data Lake Store-fiók az Azure portal, PowerShell-parancsmagok és a REST API-kon keresztül. A tulajdonos és közreműködő szerepkört a fiókhoz hajthat végre számos felügyeleti feladatot. Az Olvasó szerepkör rendelhet a felhasználók, akik csak a fiók felügyeleti adatok megtekintéséhez.

![RBAC-szerepkörök](./media/data-lake-store-security-overview/rbac-roles.png "RBAC-szerepkörök")

Vegye figyelembe, hogy fiókkezeléshez hozzárendelve, bár egyes szerepkörök adatokhoz való hozzáférés befolyásolja. Hozzáférés-vezérlési listák segítségével szabályozza a hozzáférést egy felhasználó a fájlrendszerben végrehajtható műveleteket kell. Az alábbi táblázat a management rights és az adatok hozzáférési jogosultságokat az alapértelmezett szerepköröket összegzését jeleníti meg.

| Szerepkörök | A rights Management | Adat-hozzáférési jogosultságok | Magyarázat |
| --- | --- | --- | --- |
| Nincs hozzárendelt szerepkör |None |ACL szabályozzák. |A felhasználó nem használható az Azure portal vagy Azure PowerShell-parancsmagok keresse meg a Data Lake Store. A felhasználó csak a parancssori eszközöket használhatja. |
| Tulajdonos |Összes |Összes |A tulajdonosi szerepkör felügyelő. Ez a szerepkör mindent felügyelhetnek, és az adatok teljes hozzáféréssel rendelkezik. |
| Olvasó |Csak olvasás |ACL szabályozzák. |Az Olvasó szerepkörhöz, aki mindent megtekinthet kapcsolatos fiókok kezelése, például, hogy melyik felhasználó melyik szerepkör van rendelve. Az Olvasó szerepkör nem végezze el a módosításokat. |
| Közreműködő |Mindenhol, kivéve a szerepkörök hozzáadása és eltávolítása |ACL szabályozzák. |A közreműködő szerepkört egy olyan fiók, például a központi telepítések és létrehozása és a riasztások kezelése egyes funkcióit kezelheti. A közreműködő szerepkört nem adhat hozzá, és távolítsa el a szerepköröket. |
| Felhasználói hozzáférés rendszergazdája |Hozzáadhat és eltávolíthat a szerepkörök |ACL szabályozzák. |A felhasználói hozzáférés rendszergazdája szerepkör kezelheti a felhasználói fiókokat a hozzáférést. |

Útmutatásért lásd: [felhasználók vagy biztonsági csoportok hozzárendelése a Data Lake Store-fiókok](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Hozzáférés-vezérlési listák használata a fájlrendszerek műveletek
Data Lake Store egy olyan hierarchikus fájlrendszer például a Hadoop elosztott fájlrendszer (HDFS), és támogatja [POSIX ACL-ek](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Azt szabályozza, Olvasás (r), írás (w) és végrehajtás (x)-erőforrások a tulajdonos szerepkör, a tulajdonosok csoport és más felhasználók és csoportok számára. Data Lake Store, az ACL-ek is engedélyezhető a legfelső szintű mappához, az almappák és az egyes fájlokon. További információkat a hozzáférés-vezérlési listák Data Lake Store-környezetben való működéséről a következő témakörben talál: [Hozzáférés-vezérlés a Data Lake Store-ban](data-lake-store-access-control.md).

Azt javasoljuk, hogy megadhat ACL-ek több felhasználó használatával [biztonsági csoportok](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Felhasználók hozzáadása az egy biztonsági csoportot, és rendelje hozzá egy fájlhoz vagy mappához tartozó ACL-ek biztonsági csoporthoz. Ez akkor hasznos, ha lehetővé szeretné tenni hozzárendelt engedélyeket, mert a hozzárendelt engedélyekhez 28 bejegyzések maximális. Azure Active Directory biztonsági csoportok használatával a Data Lake Store tárolt adatai nagyobb védelemben részesüljenek kapcsolatos további információkért lásd: [felhasználók vagy biztonsági csoport hozzárendelése ACL-ként az Azure Data Lake Store fájlrendszer](data-lake-store-secure-data.md#filepermissions).

![Hozzáférési engedélyek listában](./media/data-lake-store-security-overview/adl.acl.2.png "hozzáférési engedélyek listázása")

## <a name="network-isolation"></a>A hálózatok elkülönítéséhez
Az Ön hálózati szintű hozzáférés-vezérlését könnyíti a Data Lake Store. Tűzfalak létrehozása és a egy IP-címtartományt definiálása a megbízható ügyfeleket. Az IP-címtartomány csak a definiált tartományon belüli IP-cím rendelkező ügyfelek csatlakozhat a Data Lake Store.

![Tűzfalbeállítások és IP-hozzáférési](./media/data-lake-store-security-overview/firewall-ip-access.png "tűzfalbeállítások és IP-cím")

## <a name="data-protection"></a>Adatvédelem
Az Azure Data Lake Store védi az adatok teljes életciklusa során. Az adatok továbbításához a Data Lake Store az iparági szabványnak megfelelő Transport Layer Security (TLS 1.2) protokollt használja a hálózaton keresztül védi az adatait.

![Data Lake Store a titkosítási](./media/data-lake-store-security-overview/adls-encryption.png "titkosítást a Data Lake Store")

A Data Lake Store biztosítja a fiókban tárolt adatok titkosítását. Dönthet úgy, hogy titkosítja az adatokat, vagy választhatja a titkosítás nélküli lehetőséget. Amennyiben a titkosításhoz, Data Lake Store-ban tárolt adatok a rendszer titkosítja, mielőtt állandó adathordozón tárolná. Ebben az esetben a Data Lake Store automatikusan titkosítja az adatokat, és mindig visszafejti az adatokat lekérés, előtt, így teljes mértékben átlátható az ügyfélnek, az adatok elérése. Nem történik kód változás az ügyféloldalon kell adatok titkosítása/visszafejtése.

Kulcskezelés, a Data Lake Store kétféle módon biztosítja a titkosítási főkulcsok (MEK), az adatokat a Data Lake Store-ban tárolt visszafejtés szükséges kezeléséhez. Vagy engedélyezheti a Data Lake Store a MEK kezeléséről, vagy is megtartja a MEK az Azure Key Vault-fiók használatával tulajdonjogát. Kulcskezelés módját közben az egy Data Lake Store-fiók létrehozása során adja meg. További információkat a titkosítással kapcsolatos konfigurációról a következő témakörben talál: [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Tevékenység és a diagnosztikai naplók
Tevékenység vagy a diagnosztikai naplók függően e Ön által keresett naplók fiók felügyelettel kapcsolatos tevékenységek és adatokkal kapcsolatos tevékenységeket is használhatja.

* Fiók felügyeleti kapcsolatos tevékenységeket az Azure Resource Manager API-k használatával, és az Azure Portalon keresztül tevékenységeket tartalmazó naplók illesztett.
* Adatokkal kapcsolatos tevékenységeket WebHDFS REST API-k, és az Azure Portalon keresztül a diagnosztikai naplók illesztett.

### <a name="activity-log"></a>Tevékenységnapló
Való megfelelés a szabályozásoknak, egy szervezet lehet szükség megfelelő naplók fiók felügyeleti tevékenységek, ha és elemezhetik a meghatározott incidenseket. Data Lake Store rendelkezik beépített figyelés, és az összes fiók felügyeleti tevékenységeket naplózza.

A fiók a felügyeleti napló ellenőrzését megtekintheti, és válassza ki a naplózni kívánt oszlopokat. A Tevékenységnaplók az Azure Storage is exportálhat.

![Tevékenységnapló](./media/data-lake-store-security-overview/activity-logs.png "tevékenységnapló")

További információ a vizsgálati naplók: [megtekintése az erőforrásokon végzett műveletek naplózásához tevékenységi naplóit](../azure-resource-manager/resource-group-audit.md).

### <a name="diagnostics-logs"></a>Diagnosztikai naplók
Adatok hozzáférés naplózása és az Azure Portal diagnosztikai naplózás engedélyezése és a egy Azure Blob storage-fiókot, egy eseményközpontba, vagy a Log Analytics naplók küldése.

![Diagnosztikai naplók](./media/data-lake-store-security-overview/diagnostic-logs.png "diagnosztikai naplók")

Az Azure Data Lake Store diagnosztikai naplóinak való használatáról további információkért lásd: [Data Lake Store diagnosztikai naplóinak elérése](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Összegzés
Nagyvállalati ügyfeleink kereslet egy data analytics felhőalapú platform, amely biztonságos és könnyen használható. Az Azure Data Lake Store tervezett segítségünkre ezeknek a követelményeknek, az Identitáskezelés és a hitelesítést az Azure Active Directory-integráció, ACL-alapú hitelesítést, a hálózatok elkülönítéséhez, az átvitel során, míg az inaktív adatok titkosítását, és a naplózási.

Ha meg szeretné tekinteni a új szolgáltatások a Data Lake Store, küldjön nekünk visszajelzést a [Data Lake Store UserVoice fórumot](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Store áttekintése](data-lake-store-overview.md)
* [Data Lake Store használatának első lépései](data-lake-store-get-started-portal.md)
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)

