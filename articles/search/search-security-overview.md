---
title: Biztonság és az adatvédelem – Azure Search
description: Az Azure Search egy megfelelő, a SOC 2, a HIPAA és egyéb tanúsítványok. Kapcsolat és az titkosítással, hitelesítéssel és identitás való hozzáférés felhasználó és csoport biztonsági azonosítói az Azure Search segítségével szűri.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c0f824e2be0215192ca4ca1a722e814cbf299b7a
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342422"
---
# <a name="security-and-data-privacy-in-azure-search"></a>Biztonsági és az adatvédelem az Azure Search szolgáltatásban

Átfogó biztonsági és hozzáférés-vezérlés beépített annak érdekében, hogy bizalmas tartalom marad, így az Azure Search szolgáltatásba. Ez a cikk az Azure Search szolgáltatásba beépített biztonsági funkciókat és szabványok megfelelőségi enumerálása.

Az Azure Search biztonsági architektúra kiterjedő fizikai biztonság, titkosított adatátvitel, titkosított tárolást és platform kiterjedő szabványoknak való megfelelés. Azure Search, csak hitelesített kéréseket fogad el. Szükség esetén – a biztonsági szűrők felhasználónkénti hozzáférés-vezérlést a tartalomhoz is hozzáadhat. Ez a cikk a biztonság minden egyes rétegben koppint, de elsősorban arra összpontosít, hogy milyen adatok és a műveletek biztonságosak az Azure Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Szabványoknak való megfelelés: ISO 27001, SOC 2, HIPAA

Az Azure Search minősítéssel az alábbi előírásoknak, mint [2018 június bejelentett](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 Type 2 megfelelőség](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) lépjen a teljes jelentés [Azure - és az Azure Government SOC 2 típusú II jelentés](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Health Insurance Portability and Accountability Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Part 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [1. szintű PCI DSS](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Ausztrál IRAP nem besorolt DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

Általánosan elérhető szolgáltatásainak szabványoknak való megfelelés vonatkozik. Előzetes verziójú funkciók az általánosan elérhető az átmenet, és nem használható az olyan megoldások szigorú szabványokat követelményekkel rendelkező hitelesített. Megfelelőségi tanúsítvány leírása itt található [áttekintése a Microsoft Azure-megfelelőségi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) és a [biztonsági és adatkezelési központ](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Titkosított átvitelét és tárolását

Titkosítási terjeszti ki a teljes indexelési folyamat során: a kapcsolatok átvitel keresztül, és lefelé az indexelt, az Azure Search szolgáltatásban tárolt adatok.

| Biztonsági réteg | Leírás |
|----------------|-------------|
| Titkosítás az átvitel során <br>(HTTPS/SSL/TLS) | Az Azure Search a 443-as HTTPS-portot figyeli. A platform közötti kapcsolatok az Azure-szolgáltatások vannak titkosítva. <br/><br/>Az összes ügyfél – szolgáltatás Azure Search interakciók SSL/TLS 1.2-es képes.  Ügyeljen arra, TLSv1.2 az SSL-kapcsolatok a szolgáltatáshoz.|
| Titkosítás inaktív állapotban | Titkosítási teljes internalized, az indexelő a folyamatban, az indexelési idő befejezésig való vagy index mérete nincs mérhető hatással. Azt automatikusan történik az összes indexelő, a növekményes frissítéseket az indexbe, amely nem teljes mértékben titkosított (2018. január előtt létrehozott) is.<br><br>Belsőleg, titkosítási alapján [Azure Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), 256 bites [AES-titkosítás](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).|

Titkosítás a belső, az Azure Search, a tanúsítványok és titkosítási kulcsok belső célokra a Microsoft által felügyelt, és egységesen érvényesek. Nem titkosítás engedélyezése vagy letiltása, kezelése vagy helyettesítse be a saját maga, vagy megtekintheti a titkosítási beállítások a portálon vagy programozott módon. 

Titkosítás inaktív állapotban 2018. január 24 mutattuk be, és megosztott (ingyenes) szolgáltatások, beleértve az összes régióban, minden szolgáltatási csomagokra vonatkozik. Teljes titkosítás, az adott dátum előtt létrehozott indexek kell dobni, és az újonnan létrehozott ahhoz, hogy a titkosítás olyankor történjen. Ellenkező esetben csak az új adatok 24. január után hozzá is titkosítva van.

## <a name="azure-wide-user-access-controls"></a>Azure – felhasználói hozzáférés-vezérlés

Több biztonsági mechanizmus áll rendelkezésre álló Azure kiterjedő, és így automatikusan rendelkezésre álló, az Azure Search-erőforrások létrehozása.

+ [Az előfizetés vagy a Törlés megakadályozása erőforrás szintjén zárolások](../azure-resource-manager/resource-group-lock-resources.md)
+ [Szerepköralapú hozzáférés-vezérlés (RBAC) információk és a felügyeleti műveletekhez való hozzáférés szabályozásához](../role-based-access-control/overview.md)

Minden Azure-szolgáltatás támogatja a szerepköralapú hozzáférés-vezérlést (RBAC) állítja a hozzáférési szintet konzisztens az összes szolgáltatásban. Például bizalmas adatokat, például az adminisztrációs kulcsot megtekintése korlátozódik, a tulajdonos és közreműködő szerepköröket, mivel a szolgáltatás állapotának megtekintését minden szerepkör tagjai számára érhető el. Az RBAC a tulajdonos, közreműködő és olvasó szerepkört biztosít. Alapértelmezés szerint az összes szolgáltatás-rendszergazdák a tulajdonos szerepkör tagjai.

## <a name="service-access-and-authentication"></a>Szolgáltatás-hozzáférés és hitelesítés

Bár az Azure Search örökli a biztonsági ellenőrzése az Azure platform, a saját kulcs alapú hitelesítés is tartalmazza. Api-kulcsát: véletlenszerűen generált számokból és betűkből álló karakterlánc. (A rendszergazda vagy a lekérdezés) kulcs típusa határozza meg a hozzáférési szintet. Érvényes kulcs benyújtása számít, koncepció a kérés egy megbízható entitás származik. 

Nincsenek a keresési szolgáltatás, kétféle típusú kulcsok által engedélyezett hozzáférési két szintet:

* Rendszergazdai hozzáférés (érvényes a szolgáltatás minden olvasási és írási művelet)
* Lekérdezési hozzáférési jogosultsággal (például a lekérdezéseket az index csak olvasható műveletekhez érvényes)

*Az adminisztrációs kulcsok* jön létre, amikor a szolgáltatást annak üzembe helyezése. Nincsenek kijelölt két adminisztrációs kulcsot *elsődleges* és *másodlagos* Újévi ugrik, de valójában azok felcserélhetők. Minden szolgáltatásnak van két adminisztrációs kulcsot, így lehet vonni az egyik a szolgáltatáshoz való hozzáférés elvesztése nélkül. Létrehozhatja vagy rendszergazdai kulcs, de a teljes rendszergazdai kulcsok száma nem lehet hozzáadni. Nincs legfeljebb két adminisztrációs kulcsot a keresési szolgáltatás esetében.

*Lekérdezési kulcsokkal* igény szerint jönnek létre, és úgy tervezték, az ügyfélalkalmazások, amelyek keresési közvetlenül hívják. Legfeljebb 50 lekérdezési kulcsok hozhat létre. Az alkalmazás kódjában adja meg a keresési URL-CÍMÉT és a egy lekérdezési api-kulcs a szolgáltatás csak olvasható hozzáférést. Az alkalmazás kódja is megadja az indexet, az alkalmazása által használt. A végpont, a csak olvasási hozzáféréssel az api-kulcsát és a egy célindex együtt, a kapcsolat az ügyfélalkalmazás hatókörrel és a hozzáférési szintjének meghatározását.

Hitelesítés minden kérelemnél, ahol minden egyes kérés áll, amelyek kötelező kulcs, a művelet és a egy objektum szükséges. Összeláncolt, ha a két jogosultsági szintek (teljes vagy csak olvasható) és a környezetben (például egy lekérdezési művelet indexen) elegendőek a teljes körű biztonsági megoldásai a szolgáltatási műveletek. Kulcsokkal kapcsolatos további információkért lásd: [létrehozása és kezelése az api-kulcsainak](search-security-api-keys.md).

## <a name="index-access"></a>Index hozzáférés

Az Azure Search szolgáltatásban az egyedi index nem egy biztonságos objektum. Ehelyett az index a hozzáférést, a szolgáltatási réteg (olvasási vagy írási hozzáféréssel), és a egy művelet keretében határozza meg.

Végfelhasználói hozzáférés hogyan strukturálhatja a lekérdezésekre vonatkozó kérelmek érdemesebb egy lekérdezési kulcsot, amely bármilyen kérést, csak olvasható, és az egyedi index, az alkalmazás által használt tartalmazzák. Egy lekérdezési kérés esetén, és nincs való csatlakozás indexeket, vagy egyszerre eléréséhez a több index, így az összes kérelmet a célzott egyetlen index definíciója. Mint ilyen a lekérdezési kérelem magát (a kulcs és a egy egyetlen célindex) konstrukció határozza meg a biztonsági határként.

Rendszergazdai és fejlesztői hozzáférést indexek magánháztartás: mindkét létrehozása, törlése és a szolgáltatás által kezelt objektumokat frissíteni írási hozzáférésre van szükségük. Bárki a szolgáltatáshoz egy adminisztrációs kulcsot olvasása, módosítása vagy törlése bármely indexen, egyazon szolgáltatáson belül. Indexek szándékos vagy rosszindulatú törlés elleni védelemre a belső fejlesztésű verziókövetését kód eszközök a megoldás a geokód egy nemkívánatos index törlését vagy módosítását. Az Azure Search rendelkezik a rendelkezésre állás biztosítása érdekében a fürtön belüli feladatátvétel, de nem tárolja, és hajtsa végre a szellemi tulajdont képező kód létrehozásához, vagy az indexek betöltése.

A több-bérlős megoldásokat igénylő biztonsági határokat a index szintjén az ilyen megoldások általában tartalmazza a középső réteg, mely ügyfelek index elkülönítési kezelésére használhatja. További információ a több-bérlős használati eset: [tervezési minták több-bérlős SaaS-alkalmazások és az Azure Search az](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access-from-client-apps"></a>Rendszergazdai hozzáférés az ügyfélalkalmazások

Az Azure Search felügyeleti REST API kiterjesztése az Azure Resource Manager és a fájlmegosztások annak függőségeit. Mint ilyen az Active Directory az Azure Search szolgáltatás felügyeletének előfeltétele. Az összes felügyeleti kérések ügyfél-code-ból az Azure Active Directoryval, mielőtt a kérelem eléri az erőforrás-kezelő kell hitelesíteni.

Index létrehozása (Azure Search szolgáltatás REST API) vagy a dokumentumok keresése (Azure Search szolgáltatás REST API), például az Azure Search szolgáltatási végpont adatok kérelmeket a kérelem fejlécében api-kulcsát használja.

Ha az alkalmazás kódjában kezeli a szolgáltatás felügyeleti műveletek, valamint a keresési indexek vagy dokumentumok műveletekhez, a programkódban implementálni két hitelesítési módszer: a natív Azure Search és az Active Directory-hitelesítés hozzáférési kulcs Resource Manager által igényelt módszert. 

Információ az Azure Search egy kérelem strukturálásáról: [Azure Search szolgáltatás REST](https://docs.microsoft.com/rest/api/searchservice/). Resource Manager-hitelesítés követelményeiről további információkért lásd: [használható erőforrás-kezelő hitelesítési API az előfizetések hozzáféréséhez](../azure-resource-manager/resource-manager-api-authentication.md).

## <a name="user-access-to-index-content"></a>Index tartalomhoz való hozzáférés

Alapértelmezés szerint felhasználói hozzáférést az index határozza meg a hozzáférési kulcsot a lekérdezési kérésre. A legtöbb fejlesztő létrehozása és hozzárendelése [ *lekérdezési kulcsokkal* ](search-security-api-keys.md) a ügyféloldali keresési kéréseket. Lekérdezési kulcs olvasási hozzáférést biztosít az indexen belüli összes tartalom.

Ha részletes van szüksége, felhasználónkénti szabályozhatóbbá tartalmat, létrehozhatja a biztonsági szűrők, a lekérdezések egy adott biztonsági identitáshoz tartozó dokumentumok visszaadása. Előre definiált szerepkörök és szerepkör-hozzárendeléseket, helyett identitásalapú hozzáférés-vezérlés megvalósítása a *szűrő* az identitások, hogy Trim találatok dokumentumok és a tartalom alapján. A következő táblázat ismerteti a tartalom jogosulatlan levágási keresési eredmények két módszer.

| A módszer | Leírás |
|----------|-------------|
|[Biztonsági elrejtés identitás szűrők alapján.](search-security-trimming-for-azure-search.md)  | A felhasználói identitás hozzáférés-vezérlés megvalósításához alapvető munkafolyamat dokumentumok. Az index hozzáadása a biztonsági azonosítók ismerteti, és ezután bemutatja ezt a mezőt, és a záró szóközöket tiltott tartalom eredmények szűrése. |
|[Az Azure Active Directory-identitások alapján biztonsági elrejtés](search-security-trimming-for-azure-search-with-aad.md)  | Ez a cikk a következő lépéseket biztosít az identitásokat az Azure Active Directory (AAD), egy előző cikket kibővíti a [ingyenes szolgáltatások](https://azure.microsoft.com/free/) az Azure-felhőplatformon. |

## <a name="table-permissioned-operations"></a>Tábla: Permissioned műveletek

A következő táblázat összefoglalja az Azure Search engedélyezett műveletek, valamint melyik kulcsot lehetővé teszi a hozzáférést egy adott művelethez.

| Művelet | Engedélyek |
|-----------|-------------------------|
| Szolgáltatás létrehozása | Azure-előfizetés tulajdonosa|
| A szolgáltatások méretezése | Adminisztrációs kulcsot, az RBAC tulajdonosi vagy közreműködői az erőforráson  |
| A szolgáltatás törlése | Adminisztrációs kulcsot, az RBAC tulajdonosi vagy közreműködői az erőforráson |
| Létrehozása, módosítása, a szolgáltatás objektumok törlése: <br>Indexek és összetevőit (beleértve analyzer definíciók, pontozási profilok, CORS-beállítások), az indexelők, adatforrásokat, szinonimák, javaslattevőket. | Adminisztrációs kulcsot, az RBAC tulajdonosi vagy közreműködői az erőforráson  |
| Index lekérdezése | Rendszergazda vagy a lekérdezési kulcs (RBAC nem alkalmazható) |
| Rendszer-információkat, például a statisztikákat, számát és listák az objektumok visszaadó lekérdezés. | Adminisztrációs kulcsot az erőforráson (tulajdonos, közreműködő, olvasó) RBAC |
| Az adminisztrációs kulcsok kezelése | Adminisztrációs kulcsot, az RBAC tulajdonosi vagy közreműködői az erőforráson. |
| Lekérdezési kulcsok kezelése |  Adminisztrációs kulcsot, az RBAC tulajdonosi vagy közreműködői az erőforráson.  |

## <a name="physical-security"></a>Fizikai biztonság

Microsoft-adatközpontok iparágvezető fizikai biztonságot, és a egy kiterjedt legszigorúbb szabványoknak és előírásoknak megfelelő. További tudnivalókért keresse fel a [globális adatközpontok](https://www.microsoft.com/cloud-platform/global-datacenters) oldalon, vagy tekintse meg a rövid videót a center security az adatokat.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Lásd még

+ [Első lépései a .NET (használatát mutatja be egy adminisztrációs kulcsot index létrehozása)](search-create-index-dotnet.md)
+ [Első lépései a REST (használatát mutatja be egy adminisztrációs kulcsot index létrehozása)](search-create-index-rest-api.md)
+ [Identitásalapú hozzáférés-vezérlés az Azure Search-szűrők használata](search-security-trimming-for-azure-search.md)
+ [Az Active Directory azonosító-alapú hozzáférés-vezérlés az Azure Search-szűrők használata](search-security-trimming-for-azure-search-with-aad.md)
+ [Szűrők az Azure Search szolgáltatásban](search-filters.md)