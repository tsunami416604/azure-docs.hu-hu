---
title: Biztonsági a Data Lake Store áttekintése |} Microsoft Docs
description: Megértse, hogyan Azure Data Lake Store egy biztonságosabb big Data típusú adatok tárolási
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
ms.openlocfilehash: f4014d304d1e0a7d841e797c3e3bdfe31918c151
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="security-in-azure-data-lake-store"></a>Az Azure Data Lake Store biztonsági
Sok vállalat vannak kihasználja a big data elemzésre szolgáló üzleti elemzéseket felhasználóinál intelligens döntéseket. Egy szervezet előfordulhat, hogy rendelkezik egy összetett és szabályozott környezetben, a különböző felhasználók növekvő számú. Győződjön meg arról, hogy kritikus fontosságú üzleti adatokat tárolja a rendszer biztonsága érdekében együtt a megfelelő szintű hozzáférést biztosít az egyéni felhasználók számára a vállalati létfontosságú. Azure Data Lake Store célja e biztonsági követelményeknek. Ebből a cikkből megtudhatja, Data Lake Store biztonsági képességeivel kapcsolatos többek között:

* Hitelesítés
* Engedélyezés
* Hálózatelkülönítés
* Adatvédelem
* Naplózás

## <a name="authentication-and-identity-management"></a>Hitelesítés és identitás kezelése
Hitelesítés az a folyamat, amellyel a felhasználó identitásának ellenőrzése, amikor a felhasználó kommunikál a Data Lake Store vagy a bármely szolgáltatás, amely kapcsolódik a Data Lake Store. Az Identitáskezelés és a hitelesítéshez, Data Lake Store az [Azure Active Directory](../active-directory/active-directory-whatis.md), egy átfogó identitás- és hozzáférés kezelési felhőalapú megoldás, amely egyszerűbbé teszi a felhasználók és csoportok.

Lehet, hogy minden Azure-előfizetéssel társítva van egy példányát az Azure Active Directory. Csak a felhasználók és a szolgáltatás-identitások az Azure Active Directory szolgáltatásban meghatározott érhető el a Data Lake Store-fiók, az Azure portálon, a parancssori eszközök, vagy ügyfélalkalmazások keresztül a szervezet az Azure Data Lake használatával hoz létre Store SDK. Az Azure Active Directoryt használja a központi hozzáférés-vezérlési mechanizmus főbb előnyei a következők:

* Egyszerűsített identitás életciklusának kezelésére. A felhasználó vagy egy szolgáltatást (egyszerű szolgáltatásidentitás) hozható létre gyorsan és egyszerűen törlése vagy a könyvtárban a fiók letiltása gyorsan visszavonva.
* Többtényezős hitelesítés [A multi-factor authentication](../active-directory/authentication/multi-factor-authentication.md) egy további biztonsági réteget biztosít a felhasználói bejelentkezéseket és tranzakciókat.
* Bármely ügyfél szabványos protokollhoz, például az OAuth vagy OpenID keresztül hitelesítést.
* Összevonási vállalati címtárszolgáltatások és a felhőalapú identitás-szolgáltatóktól.

## <a name="authorization-and-access-control"></a>Engedélyezési és hozzáférés-vezérlés
Azure Active Directory hitelesíti a felhasználót, hogy a felhasználó hozzáférhessen az Azure Data Lake Store, engedélyezési vezérlők férnek hozzá a Data Lake Store engedélyeit. Data Lake Store fiókhoz kapcsolódó és a kapcsolódó adatok tevékenységek engedélyezési elválasztja a következő módon:

* [Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) felhasználóifiók-kezelés az Azure által biztosított (RBAC)
* A tárolóban lévő adatok eléréséhez POSIX ACL

### <a name="rbac-for-account-management"></a>Az RBAC felhasználóifiók-kezelés
Négy alapvető szerepkörök alapértelmezett Data Lake Store vannak definiálva. A szerepkörök lehetővé teszik a különböző műveleteket egy Data Lake Store-fiókot az Azure-portálon, a PowerShell-parancsmagok és a REST API-k használatával. A tulajdonos és közreműködő szerepkört a fiókhoz számos felügyeleti feladatot hajthat végre. Az olvasó szerepkört hozzárendelheti a felhasználók számára csak a fiók felügyeleti adatainak megtekintéséhez.

![Az RBAC-szerepkörök](./media/data-lake-store-security-overview/rbac-roles.png "RBAC-szerepkörök")

Vegye figyelembe, hogy bár szerepkörök fiókkezelés vannak hozzárendelve, az egyes szerepkörök adatokhoz való hozzáférés hatással. Kell használnia a hozzáférés-vezérlési listák való hozzáférést egy felhasználó a fájlrendszerben végrehajtható műveleteket. A következő táblázat a management rights és az alapértelmezett szerepköröket adatok hozzáférési jogait összegzését jeleníti meg.

| Szerepkörök | A rights Management | Adatok hozzáférési jogok | Magyarázat |
| --- | --- | --- | --- |
| Nem hozzárendelt szerepkör |None |Hozzáférés-vezérlési lista által szabályozott |A felhasználó nem használható az Azure portálon vagy az Azure PowerShell-parancsmagok keresse meg a Data Lake Store. A felhasználó csak a parancssori eszközöket használhatja. |
| Tulajdonos |Összes |Összes |A tulajdonosi szerepkört felügyelőt. Ez a szerepkör mindent felügyelhetnek, és az adatok teljes hozzáféréssel rendelkezik. |
| Olvasó |Csak olvasás |Hozzáférés-vezérlési lista által szabályozott |Az olvasó szerepkört mindent megtekinthetnek vonatkozó felhasználóifiók-kezelés, például, hogy melyik felhasználó mely szerepkör van rendelve. Az olvasó szerepkört nem módosíthatja. |
| Közreműködő |Hozzáadás és eltávolítás szerepkört kivéve |Hozzáférés-vezérlési lista által szabályozott |A közreműködői szerepkör kezelheti adatait, például a központi telepítések és létrehozása és a riasztások kezelése az egyes funkcióit. A közreműködői szerepkör nem hozzáadása vagy eltávolítása a szerepkörök. |
| Felhasználói hozzáférés rendszergazdája |Hozzáadása és eltávolítása a szerepkörök |Hozzáférés-vezérlési lista által szabályozott |A felhasználói hozzáférés adminisztrátora szerepkör kezelheti a felhasználói fiókok elérését. |

Útmutatásért lásd: [felhasználók vagy biztonsági csoportok hozzárendelése Data Lake Store-fiók](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Hozzáférés-vezérlési listák segítségével a fájlrendszerek műveleteihez
Data Lake Store hierarchikus fájlrendszer például a Hadoop elosztott fájlrendszerrel (HDFS), és támogatja [POSIX hozzáférés-vezérlési listák](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Azt szabályozza, hogy olvasási (r), (w) írási és végrehajtási (x) erőforrásokra a tulajdonosi szerepkört, a tulajdonosok csoport és más felhasználók és csoportok engedélyeit. A Data Lake Store a hozzáférés-vezérlési listák engedélyezhető a legfelső szintű mappa, almappák és a fájlokat. További információkat a hozzáférés-vezérlési listák Data Lake Store-környezetben való működéséről a következő témakörben talál: [Hozzáférés-vezérlés a Data Lake Store-ban](data-lake-store-access-control.md).

Azt javasoljuk, hogy Ön hozzáférés-vezérlési listák többfelhasználós segítségével meghatározhatók [biztonsági csoportok](../active-directory/active-directory-groups-create-azure-portal.md). Felhasználók hozzáadása az egy biztonsági csoportot, és hozzárendelheti a hozzáférés-vezérlési listákat, a fájl vagy mappa biztonsági csoportba. Ez akkor hasznos, ha lehetővé szeretné tenni hozzárendelt engedélyek, mert a hozzárendelt engedélyek 28 bejegyzések maximális. Azure Active Directory biztonsági csoportok használatával a Data Lake Store-ban tárolt adatok védelmét kapcsolatos további információkért lásd: [felhasználók vagy biztonsági csoport hozzárendelése az Azure Data Lake Store-fájlrendszer hozzáférés-vezérlési listákat,](data-lake-store-secure-data.md#filepermissions).

![Hozzáférési engedélyek listában](./media/data-lake-store-security-overview/adl.acl.2.png "lista hozzáférési engedélyek")

## <a name="network-isolation"></a>Hálózatelkülönítés
A hálózati szintű adattár állítsunk a Data Lake Store. Állítson be tűzfalak, és az IP-címtartományok definiálása a megbízható ügyfeleket. Az IP-címtartományok csak a definiált tartományon belüli IP-cím ügyfelek csatlakozhatnak Data Lake Store.

![Tűzfalbeállítások és IP-hozzáférési](./media/data-lake-store-security-overview/firewall-ip-access.png "tűzfalbeállítások és IP-cím")

## <a name="data-protection"></a>Adatvédelem
Azure Data Lake Store teljes életciklusa során az adatok védelmére. Az adatok az átvitel során a Data Lake Store az iparági szabványoknak megfelelő Transport Layer Security (TLS) protokoll az adatok védelmét a hálózaton keresztül.

![A Data Lake Store titkosítási](./media/data-lake-store-security-overview/adls-encryption.png "titkosítási Data Lake Store-ban")

A Data Lake Store biztosítja a fiókban tárolt adatok titkosítását. Dönthet úgy, hogy titkosítja az adatokat, vagy választhatja a titkosítás nélküli lehetőséget. Ha csatlakozott a titkosításhoz, Data Lake Store-ban tárolt adatok titkosítása előtt állandó adathordozón tárolja. Ebben az esetben a Data Lake Store automatikusan adatok megőrzése előtt titkosítja, és adatok lekérését, előtt visszafejti, hogy teljes mértékben transzparens az ügyfélnek fér hozzá az adatokhoz. Nincs ügyféloldali adatok titkosításához/visszafejtéséhez szükséges kód változás.

Kulcskezelés a Data Lake Store két üzemmódot biztosít a fő titkosítási kulcsok (MEKs), amelyek szükségesek a Data Lake Store-ban tárolt adatokat visszafejtése kezeléséhez. Vagy engedélyezheti a Data Lake Store a MEKs kezeléséhez, vagy válassza a tartsa meg az Azure Key Vault fiókkal MEKs tulajdonjogát. Kulcskezelés módját közben az Data Lake Store-fiók létrehozása közben adja meg. További információkat a titkosítással kapcsolatos konfigurációról a következő témakörben talál: [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Tevékenység és a diagnosztikai naplók
Tevékenység vagy diagnosztikai naplók, attól függően, hogy keres naplókat a fiók-felügyelettel kapcsolatos tevékenységek vagy adatok kapcsolatos tevékenységeket is használhatja.

* Fiók kezelésével kapcsolatos tevékenységeket Azure Resource Manager API-k, és az Azure portálon keresztül tevékenységi naplóit illesztett.
* Tevékenységek kapcsolódó adatok WebHDFS REST API-k, és az Azure portálon keresztül diagnosztikai naplók illesztett.

### <a name="activity-log"></a>Tevékenységnapló
Szabályozásoknak kell megfelelnie, egy szervezet lehet szükség megfelelő elkérése fiók felügyeleti tevékenységek, ha a meghatározott incidenseket a dig. Data Lake Store rendelkezik beépített figyelést, és minden fiók felügyeleti tevékenységeket naplózza.

A fióknak felügyeleti napló ellenőrzését megtekintése, és válassza ki a naplózni kívánt oszlopokat. Is exportálhatók tevékenységi naplóit Azure Storage.

![Tevékenységnapló](./media/data-lake-store-security-overview/activity-logs.png "műveletnapló")

Tevékenységi naplóit munkavégzés további információkért lásd: [tevékenységi naplóit rendszervizsgálati műveleteket az egyes erőforrások megtekintése](../azure-resource-manager/resource-group-audit.md).

### <a name="diagnostics-logs"></a>Diagnosztikai naplók
Adatok hozzáférés naplózása és az Azure portálon diagnosztikai naplózás engedélyezése, és az Azure Blob storage-fiók, az eseményközpontok vagy Naplóelemzési küldeni a naplókat.

![Diagnosztikai naplók](./media/data-lake-store-security-overview/diagnostic-logs.png "diagnosztikai naplók")

Diagnosztikai naplók az Azure Data Lake Store munkavégzés további információkért lásd: [diagnosztikai naplókat a Data Lake Store elérése](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Összegzés
A vállalati ügyfelek igény egy analytics felhőalapú adatplatform, amely biztonságos, könnyen használható. Azure Data Lake Store tervezett segítségével ezeket a követelményeket, az Identitáskezelés és keresztül Azure Active Directory-integráció, ACL-alapú engedélyezési, a hálózati elkülönítés, adattitkosítás átvitel közben, és a hitelesítési és naplózási.

Ha meg szeretné tekinteni a Data Lake Store új funkcióiról, küldjön visszajelzést a [Data Lake Store UserVoice fórum](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Store áttekintése](data-lake-store-overview.md)
* [Bevezetés a Data Lake Store használatába](data-lake-store-get-started-portal.md)
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)

