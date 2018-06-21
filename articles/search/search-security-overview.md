---
title: Az Azure Search biztonsági és az adatok adatvédelmi |} Microsoft Docs
description: Az Azure Search megfelel SOC 2, a HIPAA vagy a más minősítései közül. Felhasználó és csoport biztonsági azonosítója az Azure Search-en keresztüli kapcsolat és az adatok titkosítás, hitelesítés és identitás hozzáférés szűrők.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: heidist
ms.openlocfilehash: 888f7c3ced0ef48cff222bffdbf0f278fa5f42b3
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285729"
---
# <a name="security-and-data-privacy-in-azure-search"></a>Az Azure Search biztonsági és az adatok adatvédelmi

Átfogó biztonsági szolgáltatásokat és a hozzáférés-vezérlés az Azure Search bizalmas tartalom marad, így biztosításához beépített. Ez a cikk az Azure Search beépített biztonsági szolgáltatásait és szabványok megfelelőségi enumerálása.

Azure Search-biztonsági architektúrája a fizikai biztonsági, titkosított adatátvitel, titkosított tárolási és platform kiterjedő szabványoknak való megfelelés is. Azure Search működés közben csak hitelesített kéréseket fogad el. Szükség esetén a biztonsági szűrők felhasználói hozzáférés-vezérlést a tartalom is hozzáadhat. Ez a cikk a minden egyes rétegben biztonsági koppint, de elsősorban arra irányul, hogy milyen adatok és a műveletek biztosított az Azure Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Szabványoknak való megfelelés: ISO 27001, SOC 2 HIPAA

Szabványoknak való megfelelés részleges listáját tartalmazza SOC 2 típus 2 és a HIPAA általánosan elérhető szolgáltatások. Előzetes verziójú funkciók általánosan rendelkezésre álló részeként hitelesített, és nem használható a konkrét szabványokkal követelményeknek megfelelő megoldások. Megfelelőségi hitelesítő részletes ismertetését lásd: [áttekintése a Microsoft Azure megfelelőségi](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) és a [biztonsági és adatkezelési központ](https://www.microsoft.com/en-us/trustcenter). 

A következő szabványok hitelesítő lett [. június 2018 meghirdetik](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/).

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 típus 2 megfelelőségi](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) nyissa meg a teljes jelentés [Azure - és Azure Government SOC 2 II jelentés](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Egészségügyi biztosítás hordozhatóságáról és elszámolási kötelezettségéről szóló törvény (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR 11. rész)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [1. szintű PCI DSS](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Ausztrália IRAP nem besorolt DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

## <a name="encrypted-transmission-and-storage"></a>Titkosított átvitel, illetve tárolás

Titkosítási bővíti a teljes indexelése folyamat során: a kapcsolatok, átviteli keresztül, és le az Azure Search tárolt indexelt adatokat.

| Biztonsági réteg | Leírás |
|----------------|-------------|
| Az átvitel során titkosítás | Az Azure Search 443-as HTTPS-porton figyel. A platformon Azure-szolgáltatásokhoz való kapcsolatok vannak titkosítva. |
| Titkosítás inaktív állapotban | Titkosítási teljesen internalized indexelési folyamat, nincs mérhető hatással indexelési idő befejezési vagy index mérete. Az automatikusan megtörténik az indexelő minden, a növekményes frissítések olyan indexet, amely nem teljesen titkosítottak (január 2018 előtt létrehozott) is.<br><br>Belsőleg, titkosítás alapul [Azure Storage szolgáltatás titkosítási](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), 256 bites [AES titkosítási](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).|

Titkosítási tanúsítványok és titkosítási kulcsok Microsoft belsőleg kezeli, és egységesen alkalmazza az Azure Search belső. Nem titkosítás engedélyezése vagy letiltása, kezelése vagy helyettesítse a saját kulcsok és a portálon vagy programozottan titkosítási beállítások megtekintéséhez. 

Titkosítását a 2018 január 24 jelentették, és minden szolgáltatásrétegeiben használt funkciókkal, beleértve a megosztott (ingyenes) szolgáltatásokat, minden régióban vonatkozik. Teljes titkosítás indexek ezen időpont előtt létre kell dobni, és az újonnan létrehozott sorrendben történik a titkosítás. Ellenkező esetben csak az új után január 24 adattitkosítás.

## <a name="azure-wide-user-access-controls"></a>Azure-felhasználói hozzáférés-vezérlést

Több biztonsági mechanizmus áll rendelkezésre Azure kiterjedő, és így automatikusan elérhető, az Azure Search erőforrások létrehozása.

+ [Az előfizetés vagy az erőforrás törlésének megakadályozása szintet zárolásainak](../azure-resource-manager/resource-group-lock-resources.md)
+ [Szerepköralapú hozzáférés-vezérlés (RBAC) történő hozzáférés szabályozása érdekében információkat és felügyeleti műveletek](../role-based-access-control/overview.md)

Az összes Azure-szolgáltatások beállítása hozzáférési szintek következetesen összes szolgáltatásban a szerepköralapú hozzáférés-vezérlést (RBAC) támogatja. Például bizalmas adatokat, például az adminisztrációs kulcsot megtekintése nem használható a tulajdonos és közreműködő szerepkörökhöz, mivel a szolgáltatás állapotának megtekintése a szerepkör tagjai számára érhető el. Az RBAC biztosít a tulajdonos, közreműködő és olvasó szerepkört. Alapértelmezés szerint az összes szolgáltatás-rendszergazdák tagjai a tulajdonosi szerepkört.

## <a name="service-access-and-authentication"></a>Hozzáférés és a hitelesítés

Azure Search örökli a biztonsági óvintézkedéseket, az Azure platform, amíg a saját kulcs alapú hitelesítés is tartalmazza. Api-kulcsát: véletlenszerűen generált számok és betűk álló karakterlánc. (A rendszergazda vagy a lekérdezés) kulcs típusa meghatározza, hogy a hozzáférési szintet. Érvényes kulcs benyújtása akkor tekinthető igazolása a kérelem olyan megbízható entitás származik. A keresési szolgáltatás eléréséhez használt kulcsok két típusát:

* A rendszergazda (érvényes a szolgáltatás bármely olvasási és írási művelet)
* A lekérdezés (a csak olvasási műveletek, például a lekérdezések írásában, az index használható)

Adminisztrációs kulcsok jönnek létre, ha a szolgáltatás ki van építve. Két felügyeleti kulcsok, mint a kijelölt *elsődleges* és *másodlagos* leegyszerűsítheti rögtön, de valójában azok felcserélhetők. Minden szolgáltatás van két adminisztrációs kulcsok, hogy lehet vonni egy a szolgáltatáshoz való hozzáférés elvesztése nélkül. Vagy rendszergazdai kulcs helyreállíthatók, de nem tudja felvenni a rendszergazda teljes száma. Nincs legfeljebb két adminisztrációs kulcsok érhető el keresési szolgáltatásonként.

Lekérdezési kulcsok igény szerint jönnek létre, és közvetlenül hívó keresési ügyfélalkalmazások készültek. Legfeljebb 50 lekérdezési kulcsokat hozhat létre. Az alkalmazás kódjában adja meg a keresési URL-cím és a lekérdezési api-kulcsot a szolgáltatás a csak olvasható hozzáférést. Az alkalmazás kódjában is adja meg az index, amelyet az alkalmazás. A végpont, csak olvasási hozzáféréssel egy api-kulcs és a cél index együtt, a hatókör és a hozzáférési szint, a kapcsolat az ügyfélalkalmazás megadása.

Hitelesítés kéréseknek, ahol minden kérelmet kötelező kulcs, egy műveletet, és áll egy objektum szükséges. Összeláncolt, ha a két jogosultsági szintek (teljes vagy csak olvasható) és a környezetben (például a lekérdezési műveletet indexen) elegendőek a szolgáltatási műveletek teljes-számos biztonsági biztosít. Kulcsokkal kapcsolatos további információkért lásd: [létrehozása és api-kulcsok kezelése](search-security-api-keys.md).

## <a name="index-access"></a>Index hozzáférés

Az Azure Search egyedi index nincs egy biztonságos objektum. Ehelyett az index a hozzáférést a szolgáltatási rétegben (olvasási vagy írási hozzáférése), egy művelet keretében együtt határozza meg.

A végfelhasználói hozzáférése lekérdezési kérelmek csatlakozni egy lekérdezési kulcsot, amely minden kérést, csak olvasható, és a megadott index az alkalmazás által használt bele segítségével is struktúra. A lekérdezési kérelemben nincs való csatlakozás indexeket, vagy több index egyidejűleg eléréséhez az összes kérelmet a célzott egyetlen index definícióját. A lekérdezés kérelem magát (a kulcs és egy egyetlen céllal index) konstrukció, a biztonsági határ határozza meg.

Indexek rendszergazdai és fejlesztői hozzáférést magánháztartás: mindkét létrehozása, törlése és a szolgáltatás által kezelt objektumokat frissíteni írási hozzáférésre van szüksége. Bárki, aki egy adminisztrációs kulcsot a szolgáltatáshoz való olvasása, módosítása és törlése ugyanazt a szolgáltatást az index. A védelem indexek rosszindulatú vagy véletlen törlése ellen a belső fejlesztésű verziókövetésének kód eszközök felcserélni egy nemkívánatos index törlésére, valamint a módosítás a remedy. Az Azure Search rendelkezésre állásának biztosításához a fürtön belüli feladatátvétel rendelkezik, de nem tárolja, és nem hajtható végre a saját fejlesztésű kódot létrehozni vagy betölteni indexek használt.

A több-bérlős megoldások biztonsági határokat a index szinten igénylő az ilyen megoldások általában tartalmaznak egy középső réteg, mely ügyfelek index elkülönítési kezelésére használható. További információ a több-bérlős használati eset: [kialakítási minták a több-bérlős SaaS-alkalmazásokhoz és az Azure Search](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access-from-client-apps"></a>Ügyfél alkalmazásokból való rendszergazdai hozzáférés

Az Azure Search felügyeleti REST API, az Azure erőforrás-kezelő bővítménye, és közösen használja annak függőségeit. Így az Active Directory előfeltétele az Azure Search szolgáltatás adminisztrációs. Ügyfélkód felügyeleti kérelmeinek hitelesíteni kell az Azure Active Directoryval, mielőtt a kérelem eléri a Resource Manager.

Adatok kéréseket a meghatározott az Azure Search szolgáltatás végpontjának, például a Create Index (Azure Search szolgáltatás REST API) vagy dokumentumok keresése (Azure Search szolgáltatás REST API), a kérelem fejlécében api-kulcsot használni.

Ha az alkalmazás kódjában kezeli a szolgáltatás felügyeleti műveleteket, valamint az adatok műveleteket végez a keresési indexek vagy dokumentumok, a kódban megvalósítása két hitelesítési módszer: a natív Azure Search és az Active Directory-hitelesítés hozzáférési kulcs Erőforrás-kezelő által igényelt módszert. 

További információ az Azure Search kérelmet szerkezetének kialakítása: [Azure Search szolgáltatás REST](https://docs.microsoft.com/rest/api/searchservice/). Az erőforrás-kezelő hitelesítési követelményeivel kapcsolatos további információkért lásd: [használható erőforrás-kezelő hitelesítési API access előfizetésekhez](../azure-resource-manager/resource-manager-api-authentication.md).

## <a name="user-access-to-index-content"></a>Index tartalomhoz való hozzáférés

Felhasználói hozzáférés index tartalmát a lekérdezéseket, egy adott biztonsági azonosítóhoz tartozó dokumentumok vissza olyan biztonsági szűröket keresztül valósul meg. Előre definiált szerepkörök és a szerepkör-hozzárendelések helyett azonosító-alapú hozzáférés-vezérlés szűrőként megvalósítása, hogy levágja találatok dokumentumokat és identitások alapján. A következő táblázat ismerteti a két megközelítés használatos levágási keresési eredmények jogosulatlan tartalom.

| Módszer | Leírás |
|----------|-------------|
|[Biztonsági elrejtés identitás szűrők alapján](search-security-trimming-for-azure-search.md)  | A felhasználói identitás hozzáférés-vezérlés implementálása a következő alapvető munkafolyamattal dokumentumokat. Indexek felvenni a biztonsági azonosítók foglalkozik, és ezután bemutatja, hogy a mező lehet levágni a tiltott tartalom eredmények szűrése. |
|[Azure Active Directory identitások alapuló biztonsági tisztítás](search-security-trimming-for-azure-search-with-aad.md)  | Ez a cikk kibővíti az előző cikkben, lépéseket biztosít az identitások az Azure Active Directory (AAD), egy a [szolgáltatások szabad](https://azure.microsoft.com/free/) az Azure felhőalapú platform. |

## <a name="table-permissioned-operations"></a>Tábla: Permissioned műveletek

A következő táblázat összefoglalja az Azure Search engedélyezett műveletek és mely kulcs feloldja a hozzáférést egy adott művelethez.

| Művelet | Engedélyek |
|-----------|-------------------------|
| Szolgáltatás létrehozása | Azure-előfizetés tulajdonosa|
| A szolgáltatások méretezéséhez | Adminisztrációs kulcsot, RBAC tulajdonosának vagy Közreműködőjének az erőforrás  |
| A szolgáltatás törlése | Adminisztrációs kulcsot, RBAC tulajdonosának vagy Közreműködőjének az erőforrás |
| Hozzon létre, módosítsa, törölje annak objektumát a szolgáltatásban: <br>Indexek és összetevői (többek között a következőket analyzer definíciókat, pontozási profilok, a CORS-beállítások), indexelők, adatforrások, szinonimák, javaslattevők. | Adminisztrációs kulcsot, RBAC tulajdonosának vagy Közreműködőjének az erőforrás  |
| Az index lekérdezése | Rendszergazda vagy a lekérdezési kulcs (a Szerepalapú nem alkalmazható) |
| Rendszer információkat, például visszaadó statisztika, számok és objektumok listájának lekérdezése. | Adminisztrációs kulcsot, az erőforrás-(tulajdonos, közreműködő, olvasó) RBAC |
| Adminisztrációs kulcsok kezelése | Adminisztrációs kulcsot, RBAC tulajdonosa vagy Közreműködője az erőforráson. |
| Lekérdezési kulcsok kezelése |  Adminisztrációs kulcsot, RBAC tulajdonosa vagy Közreműködője az erőforráson.  |

## <a name="physical-security"></a>Fizikai biztonság

A Microsoft azon adatközpontjainak iparágvezető fizikai biztonságot, és egy nagy mennyiségű portfóliót szabványok és a megfelelő. További tudnivalókért keresse fel a [globális adatközpontok](https://www.microsoft.com/cloud-platform/global-datacenters) oldalon, vagy tekintse meg egy rövid videót a center security adatokon.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Lásd még

+ [Első lépések a .NET (mutatja be, egy adminisztrációs kulcsot használ az index létrehozása)](search-create-index-dotnet.md)
+ [Első lépések REST (mutatja be, egy adminisztrációs kulcsot használ az index létrehozása)](search-create-index-rest-api.md)
+ [Azure Search szűrőkkel azonosító-alapú hozzáférés-vezérlés](search-security-trimming-for-azure-search.md)
+ [Active Directory azonosító-alapú hozzáférés-vezérlés Azure Search-szűrők használata](search-security-trimming-for-azure-search-with-aad.md)
+ [Az Azure Search szűrők](search-filters.md)