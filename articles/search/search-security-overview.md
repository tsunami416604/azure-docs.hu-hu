---
title: Biztonság és adatvédelem
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search megfelel az SOC 2, HIPAA és egyéb minősítések. Kapcsolat- és adattitkosítás, -hitelesítés és -identitás-hozzáférés a szűrőkifejezésekben lévő felhasználói és csoportbiztonsági azonosítókon keresztül.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 6de6f23fe9564b28a5d436ac00999dbb3e9183e1
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548965"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Biztonság és adatvédelem az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive Search átfogó biztonsági funkciókkal és hozzáférés-vezérlésekkel biztosíthatja, hogy a privát tartalmak így is maradhassanak. Ez a cikk felsorolja az Azure Cognitive Search beépített biztonsági funkciókat és szabványoknak való megfelelést.

Az Azure Cognitive Search biztonsági architektúrája a fizikai biztonságot, a titkosított átviteleket, a titkosított tárolást és a platformszintű szabványoknak való megfelelést öleli fel. Működési szempontból az Azure Cognitive Search csak hitelesített kérelmeket fogad el. Szükség esetén biztonsági szűrőkkel is hozzáadhat felhasználónkénti hozzáférés-vezérlést a tartalomhoz. Ez a cikk az egyes rétegek biztonságára összpontosít, de elsősorban arra összpontosít, hogy az adatok és a műveletek hogyan vannak biztosítva az Azure Cognitive Search szolgáltatásban.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Szabványoknak való megfelelés: ISO 27001, SOC 2, HIPAA

Az Azure Cognitive Search a [2018 júniusában bejelentett](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/)szabványoknak megfelelően tanúsított:

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 2-es típusú megfelelőség](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) A teljes jelentésért látogasson el az [Azure – és az Azure Government SOC 2 Type II report című képernyőre.](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports) 
+ [Egészségbiztosítási hordozhatóságról és elszámoltathatóságról szóló törvény (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR 11. rész)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST (HITRUST)](https://en.wikipedia.org/wiki/HITRUST)
+ [1. szintű PCI DSS](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Ausztrália IRAP Be nem sorolt DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

A szabványoknak való megfelelés az általánosan elérhető funkciókra vonatkozik. Az előzetes verziójú funkciók akkor tanúsíthatók, amikor áttérnek az általános rendelkezésre állásra, és nem használhatók szigorú szabványokkal rendelkező megoldásokban. A megfelelőségi tanúsítványt [a Microsoft Azure-megfelelőség áttekintése](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) és az [Adatvédelmi központ dokumentálja.](https://www.microsoft.com/en-us/trustcenter) 

## <a name="encrypted-transmission-and-storage"></a>Titkosított átvitel és tárolás

A titkosítás a teljes indexelési folyamatra kiterjed: a kapcsolatoktól az átvitelen keresztül és az Azure Cognitive Search-ben tárolt indexelt adatokig.

| Biztonsági réteg | Leírás |
|----------------|-------------|
| Titkosítás az átvitel során <br>(HTTPS/SSL/TLS) | Az Azure Cognitive Search a 443-as HTTPS-porton figyel. A platformon keresztül az Azure-szolgáltatásokhoz való kapcsolatok titkosítva vannak. <br/><br/>Minden ügyfél-szolgáltatás Azure Cognitive Search interakciók SSL/TLS 1.2 képes.  Ügyeljen arra, hogy a TLSv1.2-t használja a szolgáltatáshoz való SSL-kapcsolatokhoz.|
| Titkosítás inaktív állapotban <br>Microsoft által kezelt kulcsok | A titkosítás teljes mértékben internalizált az indexelési folyamatban, mérhető hatással van az indexelési idő befejezéséhez vagy az index méretéhez. Ez automatikusan történik minden indexelés, beleértve a növekményes frissítéseket egy index, amely nem teljesen titkosított (2018 januárja előtt létrehozott).<br><br>Belsőleg a titkosítás az [Azure Storage Service Encryption szolgáltatáson](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)alapul, 256 bites [AES titkosítással.](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)<br><br> A titkosítás az Azure Cognitive Search belső, a Microsoft által belsőleg felügyelt és általánosan alkalmazott tanúsítványokkal és titkosítási kulcsokkal. Nem kapcsolhatja be és ki a titkosítást, kezelheti vagy helyettesítheti saját kulcsait, illetve nem tekintheti meg a titkosítási beállításokat a portálon vagy programozott módon.<br><br>2018. január 24-én jelentették be az inaktív titkosítást, és minden régióban minden szolgáltatási szintre vonatkozik, beleértve az ingyenes szintet is. A teljes titkosításhoz az adott dátum előtt létrehozott indexeket el kell dobni, és újra kell építeni a titkosítás hoz. Ellenkező esetben csak a január 24.-e után hozzáadott új adatok lesznek titkosítva.|
| Titkosítás inaktív állapotban <br>Felhasználó által kezelt kulcsok | Az ügyfél által kezelt kulcsokkal végzett titkosítás mostantól általánosan elérhető a 2019 januárjában vagy azt követően létrehozott keresési szolgáltatások esetében. Ingyenes (megosztott) szolgáltatások nem támogatottak.<br><br>Az Azure Cognitive Search-indexek és szinonimatérképek mostantól titkosíthatók az Azure Key Vaultban az ügyfél által felügyelt kulcsokkal. További információ: [Titkosítási kulcsok kezelése az Azure Cognitive Search szolgáltatásban.](search-security-manage-encryption-keys.md)<br><br>Ez a szolgáltatás nem helyettesíti az alapértelmezett titkosítást inaktív, hanem alkalmazni mellett is.<br><br>A szolgáltatás engedélyezése növeli az index méretét és csökkenti a lekérdezés teljesítményét. Az eddigi megfigyelések alapján 30–60%-os növekedésre számíthat a lekérdezési időkben, bár a tényleges teljesítmény az indexdefiníciótól és a lekérdezések típusától függően változhat. A teljesítményre gyakorolt hatás miatt azt javasoljuk, hogy csak olyan indexeken engedélyezze ezt a funkciót, amelyekvalóban igénylik.

## <a name="azure-wide-user-access-controls"></a>Az egész Azure-ra kiterjedő felhasználói hozzáférés-vezérlés

Számos biztonsági mechanizmus érhető el az Azure-ban, és így automatikusan elérhető az Azure Cognitive Search által létrehozott erőforrásokhoz.

+ [Zárolásaz előfizetés vagy erőforrás szintjén a törlés megakadályozása érdekében](../azure-resource-manager/management/lock-resources.md)
+ [Szerepköralapú hozzáférés-vezérlés (RBAC) az információkhoz és felügyeleti műveletekhez való hozzáférés szabályozásához](../role-based-access-control/overview.md)

Minden Azure-szolgáltatás támogatja a szerepköralapú hozzáférés-vezérlést (RBAC) a hozzáférési szintek összesen az összes szolgáltatás ban való egységes beállításához. Például a bizalmas adatok, például a rendszergazdai kulcs megtekintése a tulajdonosi és közreműködői szerepkörökre korlátozódik. A szolgáltatás állapotának megtekintése azonban bármely szerepkör tagjai számára elérhető. Az RBAC tulajdonosi, közreműködői és olvasói szerepköröket biztosít. Alapértelmezés szerint minden szolgáltatás-rendszergazda a Tulajdonos szerepkör tagja.

<a name="service-access-and-authentication"></a>

## <a name="endpoint-access"></a>Végpont-hozzáférés

### <a name="public-access"></a>Nyilvános hozzáférés

Az Azure Cognitive Search örökli az Azure platform biztonsági védelmét, és saját kulcsalapú hitelesítést biztosít. Az api-kulcs véletlenszerűen generált számokból és betűkből álló karakterlánc. A kulcs típusa (rendszergazda vagy lekérdezés) határozza meg a hozzáférés szintjét. Egy érvényes kulcs beküldése bizonyítéknak minősül, hogy a kérelem megbízható entitástól származik. 

A keresési szolgáltatáshoz kétféle kulcs hat szintje van:

* Rendszergazdai hozzáférés (a szolgáltatással szembeni írási és írási műveletekre érvényes)
* Lekérdezési hozzáférés (írásvédett műveletekre, például lekérdezésekre érvényes egy index dokumentumgyűjteményén)

*A rendszergazdai kulcsok* a szolgáltatás kiépítésekor jönnek létre. Két rendszergazdai kulcsok, kijelölt *elsődleges* és *másodlagos* tartani őket egyenes, de valójában ezek felcserélhetők. Minden szolgáltatás két rendszergazdai kulccsal rendelkezik, így az egyiket a szolgáltatáshoz való hozzáférés elvesztése nélkül átgörgetheti. A felügyeleti kulcs rendszeres időközönként [újragenerálható](search-security-api-keys.md#regenerate-admin-keys) az Azure biztonsági ajánlott eljárásai szerint, de nem adhat hozzá a teljes rendszergazdai kulcs száma. Keresési szolgáltatásonként legfeljebb két rendszergazdai kulcs létezik.

*A lekérdezési kulcsok* szükség szerint jönnek létre, és a lekérdezéseket kibocsátó ügyfélalkalmazásokhoz készültek. Legfeljebb 50 lekérdezési kulcsot hozhat létre. Az alkalmazáskódban megadhatja a keresési URL-címet és egy lekérdezési API-kulcsot, hogy csak olvasható hozzáférést biztosítson egy adott index dokumentumgyűjteményéhez. A végpont, az írásvédett hozzáférés api-kulcsa és a célindex együttesen határozzák meg az ügyfélalkalmazásból származó kapcsolat hatókörét és hozzáférési szintjét.

Hitelesítés szükséges minden kéréshez, ahol minden kérelem egy kötelező kulcsból, egy műveletből és egy objektumból áll. Ha össze van láncolva, a két jogosultsági szint (teljes vagy csak olvasható) és a környezet (például egy indexen lévő lekérdezési művelet) elegendő a szolgáltatási műveletek teljes spektrumú biztonságának biztosításához. A kulcsokról további információt az [API-kulcsok létrehozása és kezelése című témakörben talál.](search-security-api-keys.md)

### <a name="restricted-access"></a>Korlátozott hozzáférés

Ha rendelkezik közszolgáltatással, és korlátozni szeretné a szolgáltatás használatát, használhatja az IP-korlátozási szabályt a Felügyeleti REST API-verzióban: 2020-03-13, [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule). Az IpRule lehetővé teszi a szolgáltatáshoz való hozzáférés korlátozását azáltal, hogy egyedileg vagy egy tartományban azonosítja a keresési szolgáltatáshoz hozzáférést. 

### <a name="private-access"></a>Privát hozzáférés

Az Azure Cognitive Search [privát végpontjai](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) lehetővé teszik, hogy a virtuális hálózaton lévő ügyfél biztonságosan hozzáférjen a keresési indexben lévő adatokhoz [egy privát kapcsolaton](https://docs.microsoft.com/azure/private-link/private-link-overview)keresztül. A privát végpont a keresési szolgáltatás virtuális hálózati címteréből származó IP-címet használ. Az ügyfél és a keresési szolgáltatás közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinchálózatán lévő privát kapcsolaton, így kiküszöböli a nyilvános internetről érkező expozíciót.

[Az Azure Virtual Network (VNet)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) lehetővé teszi a biztonságos kommunikációt az erőforrások között, a helyszíni hálózattal és az internettel. 

## <a name="index-access"></a>Index hozzáférés

Az Azure Cognitive Search, az egyes index nem biztonságos objektum. Ehelyett az indexhez való hozzáférés a szolgáltatási rétegen (olvasási vagy írási hozzáférés) van meghatározva, egy művelet környezetével együtt.

A végfelhasználói hozzáférés, a lekérdezési kérelmek et strukturálhatja egy lekérdezési kulcs használatával, amely minden kérelmet csak olvasható, és tartalmazza az alkalmazás által használt index. A lekérdezési kérelemben nincs fogalma az indexek összekötése vagy több index egyidejű elérése, így minden kérelem definíció szerint egyetlen indexet céloz meg. Mint ilyen, a lekérdezési kérelem létrehozása maga (egy kulcs plusz egy célindex) határozza meg a biztonsági határt.

Az indexekhez való rendszergazdai és fejlesztői hozzáférés nem differenciált: mindkettőnek írási hozzáféréssel kell rendelkeznie a szolgáltatás által kezelt objektumok létrehozásához, törléséhez és frissítéséhez. A szolgáltatáshoz rendszergazdai kulccsal rendelkező bárki elolvashatja, módosíthatja vagy törölheti az ugyanabban a szolgáltatásban lévő indexeket. Az indexek véletlen vagy rosszindulatú törlése elleni védelem érdekében a kódeszközök házon belüli forrásellenőrzése a nem kívánt indextörlés vagy -módosítás visszafordításának megoldása. Az Azure Cognitive Search feladatátvételt hajtott végre a fürtön belül a rendelkezésre állás biztosítása érdekében, de nem tárolja vagy hajtja végre az indexek létrehozásához vagy betöltéséhez használt saját kódot.

Az indexszintű biztonsági határokat igénylő többbérlős megoldások esetében ezek a megoldások általában egy középső réteget tartalmaznak, amelyet az ügyfelek az indexelkülönítés kezelésére használnak. A több-bérlős használati esetről további információt a [több-bérlős SaaS-alkalmazások tervezési mintái és az Azure Cognitive Search című témakörben talál.](search-modeling-multitenant-saas-applications.md)

## <a name="authentication"></a>Hitelesítés

### <a name="admin-access"></a>Rendszergazdai hozzáférés

[A szerepköralapú hozzáférés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) határozza meg, hogy rendelkezik-e hozzáféréssel a szolgáltatás és annak tartalma feletti vezérlőkhöz. Ha egy Azure Cognitive Search szolgáltatás tulajdonosa vagy közreműködője, használhatja a portált vagy a PowerShell **Az.Search** modult objektumok létrehozásához, frissítéséhez vagy törléséhez a szolgáltatásban. Használhatja az [Azure Cognitive Search Management REST REST API-t](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)is.

### <a name="user-access"></a>Felhasználói hozzáférés

Alapértelmezés szerint az indexhez való felhasználói hozzáférést a lekérdezési kérelem hozzáférési kulcsa határozza meg. A legtöbb fejlesztő [*lekérdezési kulcsokat*](search-security-api-keys.md) hoz létre és rendel hozzá az ügyféloldali keresési kérelmekhez. A lekérdezési kulcs olvasási hozzáférést biztosít az indexen belüli összes tartalomhoz.

Ha részletes, felhasználónkénti szabályozásra van szüksége a tartalom felett, biztonsági szűrőket hozhat létre a lekérdezésekre, és visszaadhat egy adott biztonsági identitáshoz társított dokumentumokat. Az előre definiált szerepkörök és szerepkör-hozzárendelések helyett az identitásalapú hozzáférés-vezérlés *szűrőként* van megvalósítva, amely az identitások alapján vágja le a dokumentumok és tartalmak keresési eredményeit. Az alábbi táblázat két módszert ismertet a jogosulatlan tartalom keresési eredményeinek vágására.

| Módszer | Leírás |
|----------|-------------|
|[Biztonsági vágás identitásszűrők alapján](search-security-trimming-for-azure-search.md)  | Dokumentálja a felhasználói identitáshozzáférés-vezérlés megvalósításának alapvető munkafolyamatát. Ez magában foglalja a biztonsági azonosítók hozzáadása egy index, majd ismerteti szűrés, hogy a mező a tiltott tartalom eredményeinek vágása. |
|[Az Azure Active Directory-identitásokon alapuló biztonsági vágás](search-security-trimming-for-azure-search-with-aad.md)  | Ez a cikk kibontja az előző cikket, amely lépéseket biztosít az azure Active Directory (AAD) identitások lekéréséhez, amely az Azure felhőplatform egyik [ingyenes szolgáltatása.](https://azure.microsoft.com/free/) |

## <a name="table-permissioned-operations"></a>Tábla: Engedéllyel rendelkező műveletek

Az alábbi táblázat összefoglalja az Azure Cognitive Search által engedélyezett műveleteket, és azt, hogy melyik kulcs oldja fel egy adott művelet elérését.

| Művelet | Engedélyek |
|-----------|-------------------------|
| Szolgáltatás létrehozása | Az Azure-előfizetés tulajdonosa|
| Szolgáltatás méretezése | Felügyeleti kulcs, RBAC-tulajdonos vagy közreműködő az erőforráson  |
| Szolgáltatás törlése | Felügyeleti kulcs, RBAC-tulajdonos vagy közreműködő az erőforráson |
| Objektumok létrehozása, módosítása, törlése a szolgáltatáson: <br>Indexek és összetevő-alkatrészek (beleértve az analizátor definícióit, pontozási profilokat, CORS-beállításokat), indexelők, adatforrások, szinonimák, szuggesztív ek. | Felügyeleti kulcs, RBAC-tulajdonos vagy közreműködő az erőforráson  |
| Index lekérdezése | Rendszergazda vagy lekérdezési kulcs (Az RBAC nem alkalmazható) |
| Rendszeradatok lekérdezése, például statisztikák, számlálók és objektumlisták visszaadása. | Rendszergazdai kulcs, RBAC az erőforráson (tulajdonos, közreműködő, olvasó) |
| Felügyeleti kulcsok kezelése | Felügyeleti kulcs, RBAC-tulajdonos vagy közreműködő az erőforráson. |
| Lekérdezési kulcsok kezelése |  Felügyeleti kulcs, RBAC-tulajdonos vagy közreműködő az erőforráson.  |

## <a name="physical-security"></a>Fizikai biztonság

A Microsoft adatközpontjai iparágvezető fizikai biztonságot nyújtanak, és megfelelnek a szabványok és előírások széles portfóliójának. További információért lépjen a [Globális adatközpontok](https://www.microsoft.com/cloud-platform/global-datacenters) oldalra, vagy tekintse meg az adatközpontok biztonságáról szóló rövid videót.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Lásd még

+ [Első lépések .NET (bemutatja egy rendszergazdai kulcs használatával index létrehozásához)](search-create-index-dotnet.md)
+ [Rest használatának első lépései (bemutatja, hogy rendszergazdai kulccsal hoz létre indexet)](search-create-index-rest-api.md)
+ [Identitásalapú hozzáférés-vezérlés az Azure Cognitive Search szűrőivel](search-security-trimming-for-azure-search.md)
+ [Active Directory identitásalapú hozzáférés-vezérlés az Azure Cognitive Search szűrőivel](search-security-trimming-for-azure-search-with-aad.md)
+ [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)