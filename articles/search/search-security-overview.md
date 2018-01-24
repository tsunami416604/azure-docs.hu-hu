---
title: "Adatok és a műveletek az Azure Search biztonságos |} Microsoft Docs"
description: "Az Azure Search biztonsági SOC 2 megfelelőségi, titkosítás, hitelesítés és identitás hozzáférés a felhasználó és az Azure keresési szűrők csoport biztonsági azonosítói alapul."
services: search
documentationcenter: 
author: HeidiSteen
manager: cgronlun
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/19/2018
ms.author: heidist
ms.openlocfilehash: c3aa4883e33b1f3494f8502fe7f8b12f7d64a72f
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="security-and-controlled-access-in-azure-search"></a>Biztonság és ellenőrzött hozzáférés az Azure Search

Az Azure Search van [SOC 2 szabványnak megfelelő](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports), egy átfogó biztonsági architektúra feszítőfa fizikai biztonsági, titkosított adatátvitel, titkosított tárolási és platform kiterjedő szoftver védelmet. Azure Search működés közben csak hitelesített kéréseket fogad el. Másik lehetőségként adhat hozzá felhasználói hozzáférés-vezérlést a tartalomhoz. Ez a cikk a minden egyes rétegben biztonsági koppint, de elsősorban arra irányul, hogy milyen adatok és a műveletek biztosított az Azure Search.

![Biztonsági rétegek blokk ábrája](media/search-security-overview/azsearch-security-diagram.png)

## <a name="physical-security"></a>Fizikai biztonság

A Microsoft azon adatközpontjainak iparágvezető fizikai biztonságot, és egy nagy mennyiségű portfóliót szabványok és a megfelelő. További tudnivalókért keresse fel a [globális adatközpontok](https://www.microsoft.com/cloud-platform/global-datacenters) oldalon, vagy tekintse meg egy rövid videót a center security adatokon.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]

## <a name="encrypted-transmission-and-storage"></a>Titkosított átvitel, illetve tárolás

Titkosítási bővíti a teljes indexelése folyamat során: a kapcsolatok, átviteli keresztül, és le az Azure Search tárolt indexelt adatokat.

| Biztonsági réteg | Leírás |
|----------------|-------------|
| Az átvitel során titkosítás | Az Azure Search 443-as HTTPS-porton figyel. A platformon Azure-szolgáltatásokhoz való kapcsolatok vannak titkosítva. |
| Titkosítás inaktív állapotban | Titkosítási teljesen internalized indexelési folyamat, nincs mérhető hatással indexelési idő befejezési vagy index mérete. Az automatikusan megtörténik az indexelő minden, a növekményes frissítések olyan indexet, amely nem teljesen titkosítottak (január 2018 előtt létrehozott) is.<br><br>Belsőleg, titkosítás alapul [Azure Storage szolgáltatás titkosítási](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), 256 bites [AES titkosítási](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).|
| [SOC 2 megfelelőségi](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) | Összes keresőszolgáltatást teljesen AICPA SOC 2 szabványnak megfelelő, az Azure Search biztosít minden adatközpontokban. Tekintse át a teljes jelentés, keresse fel [Azure - és Azure Government SOC 2 II jelentés](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). |

Titkosítási tanúsítványok és titkosítási kulcsok Microsoft belsőleg kezeli, és egységesen alkalmazza az Azure Search belső. Nem titkosítás engedélyezése vagy letiltása, kezelése vagy helyettesítse a saját kulcsok és a portálon vagy programozottan titkosítási beállítások megtekintéséhez. 

Titkosítását a 2018 január 24 jelentették, és minden szolgáltatásrétegeiben használt funkciókkal, beleértve a megosztott (ingyenes) szolgáltatásokat, minden régióban vonatkozik. Teljes titkosítás indexek ezen időpont előtt létre kell dobni, és az újonnan létrehozott sorrendben történik a titkosítás. Ellenkező esetben csak az új után január 24 adattitkosítás.

## <a name="azure-wide-logical-security"></a>Logikai Azure kiterjedő olyan biztonságot

Több biztonsági mechanizmus áll, az Azure-verem keresztül elérhető, és így automatikusan elérhető, az Azure Search erőforrások hoz létre.

+ [Az előfizetés vagy az erőforrás törlésének megakadályozása szintet zárolásainak](../azure-resource-manager/resource-group-lock-resources.md)
+ [Szerepköralapú hozzáférés-vezérlés (RBAC) történő hozzáférés szabályozása érdekében információkat és felügyeleti műveletek](../active-directory/role-based-access-control-what-is.md)

Az összes Azure-szolgáltatások beállítása hozzáférési szintek következetesen összes szolgáltatásban a szerepköralapú hozzáférés-vezérlést (RBAC) támogatja. Például bizalmas adatokat, például az adminisztrációs kulcsot megtekintése nem használható a tulajdonos és közreműködő szerepkörökhöz, mivel a szolgáltatás állapotának megtekintése a szerepkör tagjai számára érhető el. Az RBAC biztosít a tulajdonos, közreműködő és olvasó szerepkört. Alapértelmezés szerint az összes szolgáltatás-rendszergazdák tagjai a tulajdonosi szerepkört.

## <a name="service-access-and-authentication"></a>Hozzáférés és a hitelesítés

Azure Search örökli a biztonsági óvintézkedéseket, az Azure platform, amíg a saját kulcs alapú hitelesítés is tartalmazza. (A rendszergazda vagy a lekérdezés) kulcs típusa meghatározza, hogy a hozzáférési szintet. Érvényes kulcs benyújtása akkor tekinthető igazolása a kérelem olyan megbízható entitás származik. 

Hitelesítés kéréseknek, ahol minden kérelmet kötelező kulcs, egy műveletet, és áll egy objektum szükséges. Összeláncolt, ha a két jogosultsági szintek (teljes vagy csak olvasható) és a környezet elegendőek a szolgáltatási műveletek teljes-számos biztonsági biztosít. 

|Kulcs|Leírás|Korlátok|  
|---------|-----------------|------------|  
|Adminisztratív körzet|Teljes körű jogosultságot biztosít az összes művelethez, beleértve a szolgáltatás kezelése létrehozása, és törölje az indexek, az indexelők és az adatforrások.<br /><br /> Két felügyeleti **api-kulcsokat**nevezik, *elsődleges* és *másodlagos* kulcsok a portálon jönnek létre, ha a szolgáltatás jön létre, és az igény szerinti külön-külön újragenerálása . A két kulcs lehetővé teszi egy kulcs váltása a második kulcs használatakor a szolgáltatás folyamatos elérésére.<br /><br /> Adminisztrációs kulcsok csak HTTP-kérelmek fejléceinek vannak megadva. Az egy URL-cím nem helyezhető el egy adminisztrációs api-kulcsot.|Legfeljebb 2-szolgáltatás|  
|Lekérdezés|Csak olvasható hozzáférést biztosítanak az indexekhez és dokumentumokhoz, és általában a keresési kérelmeket kibocsátó ügyfélalkalmazások számára.<br /><br /> Lekérdezési kulcsok igény szerint jönnek létre. Létrehozhat őket manuálisan a portálon vagy programozottan keresztül a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Lekérdezési kulcsok a keresés, a javaslat, vagy a keresési művelet HTTP-kérelem fejléc adható meg. Másik lehetőségként átadhatók egy lekérdezési kulcsot paraméterként egy URL-címen. Attól függően, hogy az ügyfélalkalmazás formulates a kérelmet akkor valószínűleg egyszerűbb felelt meg a kulcsot a lekérdezési paramétert:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01&api-key=A8DA81E03F809FE166ADDB183E9ED84D`|50-szolgáltatás|  

 Nincs vizuálisan, egy adminisztrációs kulcsot vagy a lekérdezési kulcs közötti különbség. Mindkét kulcsai 32 véletlenszerűen álló karakterláncok generált alfanumerikus karakter. Ha elveszti nyomon milyen kulcs van megadva az alkalmazásban, akkor [ellenőrizze a portálon értékek](https://portal.azure.com) , vagy használja a [REST API](https://docs.microsoft.com/rest/api/searchmanagement/) adhatja vissza az értéket és a kulcs típusa.  

> [!NOTE]  
>  A gyenge biztonsági eljárás felelt meg a bizalmas adatokat, mint egy `api-key` kérési URI-ban. Emiatt Azure Search csak fogad egy lekérdezési kulcsot egy `api-key` a lekérdezésben karakterlánc, és kerülje ennek során, kivéve, ha az index tartalmát nyilvánosan elérhetőnek kell lennie. Általános szabályként elmondható, sikeres javasoljuk a `api-key` fejléc szerint.  

### <a name="how-to-find-the-access-keys-for-your-service"></a>Hol találhatók a szolgáltatás hozzáférési kulcsainak listázása

Ezt úgy szerezheti be a portál vagy a tárelérési kulcsok a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/). További információkért lásd: [kulcsok kezelése](search-manage.md#manage-api-keys).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Lista a [keresési szolgáltatások](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az előfizetéséhez.
3. Válassza ki a szolgáltatást, és keresse meg a szolgáltatás lapján **beállítások** >**kulcsok** rendszergazda és a lekérdezési kulcsok megtekintéséhez.

![Portállapon, beállítások, a kulcsok szakasz](media/search-security-overview/settings-keys.png)

## <a name="index-access"></a>Index hozzáférés

Az Azure Search egyedi index nincs egy biztonságos objektum. Ehelyett az index a hozzáférést a szolgáltatási rétegben (olvasási vagy írási hozzáférése), egy művelet keretében együtt határozza meg.

Végfelhasználói hozzáférése esetén a lekérdezésekre vonatkozó kérelmek szerkezeti is az alkalmazás egy lekérdezési kulcsot, amely minden kérést, csak olvasható, és tartalmazza a megadott index az alkalmazás által használt protokoll használatával kapcsolódó levelezőprogramokkal. A lekérdezési kérelemben nincs való csatlakozás indexeket, vagy több index egyidejűleg eléréséhez az összes kérelmet a célzott egyetlen index definícióját. A lekérdezés kérelem magát (a kulcs és egy egyetlen céllal index) szerkezete, a biztonsági határ határozza meg.

Indexek rendszergazdai és fejlesztői hozzáférést magánháztartás: mindkét létrehozása, törlése és a szolgáltatás által kezelt objektumokat frissíteni írási hozzáférésre van szüksége. Bárki, aki egy adminisztrációs kulcsot a szolgáltatáshoz való olvasása, módosítása és törlése ugyanazt a szolgáltatást az index. A védelem indexek rosszindulatú vagy véletlen törlése ellen a belső fejlesztésű verziókövetésének kód eszközök felcserélni egy nemkívánatos index törlésére, valamint a módosítás a remedy. Az Azure Search rendelkezésre állásának biztosításához a fürtön belüli feladatátvétel rendelkezik, de nem tárolja, és nem hajtható végre a saját fejlesztésű kódot létrehozni vagy betölteni indexek használt.

A több-bérlős megoldások biztonsági határokat a index szinten igénylő az ilyen megoldások általában tartalmaznak egy középső réteg, mely ügyfelek index elkülönítési kezelésére használható. További információ a több-bérlős használati eset: [kialakítási minták a több-bérlős SaaS-alkalmazásokhoz és az Azure Search](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access-from-client-apps"></a>Ügyfél alkalmazásokból való rendszergazdai hozzáférés

Az Azure Search felügyeleti REST API, az Azure erőforrás-kezelő bővítménye, és közösen használja annak függőségeit. Így az Active Directory előfeltétele az Azure Search szolgáltatás adminisztrációs. Ügyfélkód felügyeleti kérelmeinek hitelesíteni kell az Azure Active Directoryval, mielőtt a kérelem eléri a Resource Manager.

Adatok kéréseket a meghatározott az Azure Search szolgáltatás végpontjának, például a Create Index (Azure Search szolgáltatás REST API) vagy dokumentumok keresése (Azure Search szolgáltatás REST API), a kérelem fejlécében api-kulcsot használni.

Ha az alkalmazás kódjában kezeli a szolgáltatás felügyeleti műveleteket, valamint az adatok műveleteket végez a keresési indexek vagy dokumentumok, a kódban megvalósítása két hitelesítési módszer: a natív Azure Search és az Active Directory-hitelesítés hozzáférési kulcs Erőforrás-kezelő által igényelt módszert. 

További információ az Azure Search kérelmet szerkezetének kialakítása: [Azure Search szolgáltatás REST](https://docs.microsoft.com/rest/api/searchservice/). Az erőforrás-kezelő hitelesítési követelményeivel kapcsolatos további információkért lásd: [használható erőforrás-kezelő hitelesítési API access előfizetésekhez](../azure-resource-manager/resource-manager-api-authentication.md).

## <a name="user-access-to-index-content"></a>Index tartalomhoz való hozzáférés

Felhasználói hozzáférés index tartalmát a lekérdezéseket, egy adott biztonsági azonosítóhoz tartozó dokumentumok vissza olyan biztonsági szűröket keresztül valósul meg. Előre definiált szerepkörök és a szerepkör-hozzárendelések helyett azonosító-alapú hozzáférés-vezérlés szűrőként megvalósítása, hogy levágja találatok dokumentumokat és identitások alapján. A következő táblázat ismerteti a két megközelítés használatos levágási keresési eredmények jogosulatlan tartalom.

| Approach | Leírás |
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
| Lekérdezési kulcsok kezelése |  Adminisztrációs kulcsot, RBAC tulajdonosa vagy Közreműködője az erőforráson. Az RBAC-olvasó lekérdezési kulcsok megtekintéséhez. |


## <a name="see-also"></a>Lásd még

+ [Első lépések a .NET (mutatja be, egy adminisztrációs kulcsot használ az index létrehozása)](search-create-index-dotnet.md)
+ [Első lépések REST (mutatja be, egy adminisztrációs kulcsot használ az index létrehozása)](search-create-index-rest-api.md)
+ [Azure Search szűrőkkel azonosító-alapú hozzáférés-vezérlés](search-security-trimming-for-azure-search.md)
+ [Active Directory azonosító-alapú hozzáférés-vezérlés Azure Search-szűrők használata](search-security-trimming-for-azure-search-with-aad.md)
+ [Az Azure Search szűrők](search-filters.md)