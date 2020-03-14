---
title: Biztonság és adatvédelem
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search megfelel a SOC 2, a HIPAA és más minősítéseknek. A kapcsolat és az adattitkosítás, a hitelesítés és az identitás hozzáférése a felhasználók és a csoport biztonsági azonosítói alapján a szűrési kifejezésekben.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 44d5edd7b5808b6c212a832dd95de7a9cb4b7c08
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282834"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Biztonság és adatvédelem az Azure Cognitive Search

Az átfogó biztonsági funkciók és hozzáférés-vezérlések az Azure Cognitive Searchba vannak építve, így biztosítva, hogy a privát tartalmak így is maradnak. Ez a cikk az Azure Cognitive Search beépített biztonsági szolgáltatásokat és szabványoknak megfelelő megfelelőséget sorolja fel.

Az Azure Cognitive Search biztonsági architektúrája felöleli a fizikai biztonságot, a titkosított átvitelt, a titkosított tárolást és a platformra vonatkozó szabványoknak való megfelelőséget. Működés közben az Azure Cognitive Search csak a hitelesített kérelmeket fogadja el. A biztonsági szűrők segítségével felhasználónkénti hozzáférés-vezérlést is hozzáadhat a tartalomhoz. Ez a cikk az egyes rétegeken a biztonságot érinti, de elsősorban arra összpontosít, hogy az adatok és a műveletek hogyan biztonságosak az Azure Cognitive Searchban.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Szabványok megfelelősége: ISO 27001, SOC 2, HIPAA

Az Azure Cognitive Search a következő szabványoknak megfelelő minősítéssel rendelkezik, ahogy azt a [2018. júniusában bejelentette](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SoC 2 Type 2 megfelelőség](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) A teljes jelentéshez nyissa meg az [Azure-t és a Azure Government SoC 2 Type II jelentést](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Egészségbiztosítási hordozhatóság és elszámoltathatósági szabály (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR 11. rész)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS 1. szint](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Ausztráliai IRAP, nem besorolt DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

A szabványok megfelelősége az általánosan elérhető funkciókra vonatkozik. Az előzetes verziójú funkciók hitelesítése akkor történik meg, amikor az általános rendelkezésre állásra vált, és nem használhatók szigorú szabványokra vonatkozó követelményekkel rendelkező megoldásokban. A megfelelőségi minősítést a [Microsoft Azure megfelelőség](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) és a [megbízhatósági központ](https://www.microsoft.com/en-us/trustcenter)áttekintése ismerteti. 

## <a name="encrypted-transmission-and-storage"></a>Titkosított átvitel és tárolás

A titkosítás a teljes indexelési folyamat során kiterjed: a kapcsolatokból, a továbbításon keresztül, illetve az Azure Cognitive Searchban tárolt indexelt adatokkal.

| Biztonsági réteg | Leírás |
|----------------|-------------|
| Titkosítás átvitel közben <br>(HTTPS/SSL/TLS) | Az Azure Cognitive Search a 443-es HTTPS-portot figyeli. A platformon az Azure-szolgáltatásokkal létesített kapcsolatok titkosítva vannak. <br/><br/>Az összes ügyfél és szolgáltatás közötti Azure Cognitive Search-interakció SSL/TLS 1,2-kompatibilis.  Ügyeljen arra, hogy az TLS 1.2-es verzióját használja az SSL-kapcsolatokhoz a szolgáltatáshoz.|
| Titkosítás inaktív állapotban <br>Microsoft által felügyelt kulcsok | A titkosítás teljes mértékben az indexelési folyamatba kerül, és nem befolyásolja az indexelési idő – befejezés vagy az index méretének mérését. Automatikusan megtörténik az összes indexelésnél, beleértve az olyan index növekményes frissítését is, amely nem teljesen titkosított (január 2018. előtt jött létre).<br><br>Belsőleg a titkosítás az [Azure Storage Service Encryptionon](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)alapul, és 256 bites AES- [titkosítást](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)használ.<br><br> A titkosítás az Azure Cognitive Search-ban, a Microsoft által belsőleg felügyelt tanúsítványokkal és titkosítási kulcsokkal, valamint univerzálisan alkalmazható. A titkosítás be-és kikapcsolható, kezelheti vagy helyettesítheti a saját kulcsait, vagy megtekintheti a portál titkosítási beállításait vagy programozott módon.<br><br>A inaktív adatok titkosítása 2018. január 24-én jelent meg, és minden szolgáltatási szinten érvényes, beleértve az ingyenes szintet is minden régióban. A teljes titkosításhoz az adott dátum előtt létrehozott indexeket el kell dobni, és újból létre kell hozni a titkosítás megkezdése érdekében. Ellenkező esetben csak a január 24 után hozzáadott új adatforgalom titkosítva van.|
| Titkosítás inaktív állapotban <br>Felhasználó által kezelt kulcsok | Az ügyfél által felügyelt kulcsokkal történő titkosítás mostantól általánosan elérhető a január 2019-on vagy azt követően létrehozott keresési szolgáltatásokhoz. Ingyenes (megosztott) szolgáltatásokban nem támogatott.<br><br>Az Azure Cognitive Search indexek és a szinonimák leképezései mostantól titkosítva lehetnek a Azure Key Vaultban az ügyfél kulcsok felügyelt kulcsaival. További információ: [titkosítási kulcsok kezelése az Azure Cognitive Searchban](search-security-manage-encryption-keys.md).<br><br>Ez a funkció nem helyettesíti az alapértelmezett titkosítást a nyugalmi állapotban, hanem az alkalmazáson kívül is alkalmazza.<br><br>A funkció engedélyezése növeli az index méretét és csökkenti a lekérdezési teljesítményt. Az eddigi megfigyelések alapján a lekérdezési időpontokban 30%-60%-os növekedés várható, bár a tényleges teljesítmény az index definíciója és a lekérdezések típusaitól függően változhat. A teljesítményre gyakorolt hatás miatt javasoljuk, hogy ezt a funkciót csak olyan indexeken engedélyezze, amelyekhez valóban szükség van.

## <a name="azure-wide-user-access-controls"></a>Azure-szintű felhasználói hozzáférés-vezérlés

Számos biztonsági mechanizmus érhető el az Azure-ban, és így automatikusan elérhetővé válik a létrehozott Azure Cognitive Search-erőforrások számára.

+ [Zárolások az előfizetés vagy az erőforrás szintjén a törlés megakadályozása érdekében](../azure-resource-manager/management/lock-resources.md)
+ [Szerepköralapú Access Control (RBAC) az információkhoz és a felügyeleti műveletekhez való hozzáférés szabályozásához](../role-based-access-control/overview.md)

Minden Azure-szolgáltatás támogatja a szerepköralapú hozzáférés-vezérlést (RBAC), amely az összes szolgáltatásban konzisztens hozzáférési szinteket biztosít. A bizalmas adatok (például a rendszergazdai kulcs) megtekintése például a tulajdonosi és a közreműködő szerepkörökre korlátozódik, míg a szolgáltatás állapotának megtekintése bármely szerepkör tagjai számára elérhető. A RBAC tulajdonosi, közreműködői és olvasói szerepköröket biztosít. Alapértelmezés szerint az összes szolgáltatás-rendszergazda a tulajdonos szerepkör tagja.

<a name="service-access-and-authentication"></a>

## <a name="service-access-and-authentication"></a>Szolgáltatás-hozzáférés és-hitelesítés

Míg az Azure Cognitive Search örökli az Azure platform biztonsági védelmét, a saját kulcs-alapú hitelesítést is biztosít. Az API-Key egy véletlenszerűen generált számokból és betűkből álló karakterlánc. A kulcs típusa (rendszergazda vagy lekérdezés) határozza meg a hozzáférési szintet. Az érvényes kulcs beküldése igazolja, hogy a kérelem megbízható entitásból származik. 

A keresési szolgáltatáshoz két különböző típusú kulcs van engedélyezve:

* Rendszergazdai hozzáférés (a szolgáltatásra vonatkozó bármilyen írási és olvasási művelethez érvényes)
* Lekérdezési hozzáférés (csak olvasási műveletekhez, például lekérdezésekhez, egy index dokumentumainak gyűjteményéhez érvényes)

Az *adminisztrátori kulcsok* a szolgáltatás kiépített állapotában jönnek létre. Két rendszergazdai kulcs van kijelölve *elsődlegesként* és *másodlagosként* , hogy azok egyenesek maradjanak, de valójában felcserélhetők. Minden szolgáltatásnak két rendszergazdai kulcsa van, így a szolgáltatáshoz való hozzáférés elvesztése nélkül végezheti el a bevezetést. Az Azure biztonsági eljárásainak rendszeres időközönként újra [létrehozhatja az adminisztrátori kulcsot](search-security-api-keys.md#regenerate-admin-keys) , de a felügyeleti kulcsok teljes számát nem lehet hozzáadni. Keresési szolgáltatásokban legfeljebb két rendszergazdai kulcs lehet.

A *lekérdezési kulcsok* szükség szerint jönnek létre, és a lekérdezéseket kiállító ügyfélalkalmazások számára lettek kialakítva. Akár 50 lekérdezési kulcsot is létrehozhat. Az alkalmazás kódjában megadhatja a keresési URL-címet és egy lekérdezési API-kulcsot, amely lehetővé teszi a csak olvasási hozzáférést egy adott index dokumentum-gyűjteményéhez. Együtt, a végpont, a csak olvasási hozzáféréssel rendelkező API-kulcs és a célként megadott index határozza meg a kapcsolat hatókörét és hozzáférési szintjét az ügyfélalkalmazás alapján.

Minden kérelem esetében hitelesítésre van szükség, amelyben minden kérelem egy kötelező kulcsból, egy műveletből és egy objektumból áll. Ha együtt van összekapcsolva, a két jogosultsági szint (teljes vagy csak olvasható) és a környezet (például egy index lekérdezési művelete) elegendő a szolgáltatási műveletek teljes spektrumú biztonságának biztosításához. A kulcsokkal kapcsolatos további információkért lásd: [API-kulcsok létrehozása és kezelése](search-security-api-keys.md).

## <a name="index-access"></a>Indexelési hozzáférés

Az Azure Cognitive Searchban az egyes indexek nem biztonságos objektumok. Ehelyett az indexhez való hozzáférést a szolgáltatási réteg (olvasási vagy írási hozzáférés), valamint egy művelet kontextusa határozza meg.

A végfelhasználói hozzáféréshez a lekérdezési kérelmeket a lekérdezési kulcs használatával lehet összeszervezni, amely minden kérés írásvédett, és tartalmazza az alkalmazás által használt adott indexet. Egy lekérdezési kérelemben nincs olyan fogalom, amely az indexek összekapcsolását vagy egyszerre több index elérését teszi elérhetővé, így az összes kérelem egyetlen indexet céloz meg definíció szerint. Ennek megfelelően a lekérdezési kérelem (egy kulcs plusz egy célként megadott index) kialakítása határozza meg a biztonsági határt.

Az indexekhez való rendszergazdai és fejlesztői hozzáférés nem különbözik: a szolgáltatás által felügyelt objektumok létrehozásához, törléséhez és frissítéséhez egyaránt írási hozzáférésre van szükség. A szolgáltatáshoz tartozó rendszergazdai kulccsal bárki megtekintheti, módosíthatja vagy törölheti ugyanazon szolgáltatás bármelyik indexét. Az indexek véletlen vagy rosszindulatú törlésével szembeni védelem érdekében a belső verziókövetés a kód eszközeire a nemkívánatos indexek törlésének vagy módosításának megfordítására szolgáló megoldás. Az Azure Cognitive Search a rendelkezésre állás biztosítása érdekében feladatátvételt hajt végre a fürtön belül, de nem tárolja és nem hajtja végre az indexek létrehozásához vagy betöltéséhez használt tulajdonosi kódot.

Az index szintjén biztonsági határokat igénylő bérlős megoldások esetében az ilyen megoldások jellemzően olyan középső szintet tartalmaznak, amelyet az ügyfelek az indexek elkülönítésének kezelésére használnak. További információ a több-bérlős használati esetről: [tervezési minták a több-bérlős SaaS-alkalmazásokhoz és az Azure Cognitive Search](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access"></a>Rendszergazdai hozzáférés

A [szerepköralapú hozzáférés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) határozza meg, hogy rendelkezik-e hozzáféréssel a szolgáltatáshoz és annak tartalmához. Ha Ön egy Azure Cognitive Search-szolgáltatás tulajdonosa vagy közreműködője, a portálon vagy a PowerShell az **. Search** modul használatával hozhat létre, frissíthet vagy törölhet objektumokat a szolgáltatásban. Használhatja az [Azure Cognitive Search felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)is.

## <a name="user-access"></a>Felhasználói hozzáférés

Alapértelmezés szerint az indexhez való felhasználói hozzáférést a lekérdezési kérelem hozzáférési kulcsa határozza meg. A legtöbb fejlesztő a [*lekérdezési kulcsokat*](search-security-api-keys.md) az ügyféloldali keresési kérelmekhez hozza létre és rendeli hozzá. A lekérdezési kulcs olvasási hozzáférést biztosít az indexen belüli összes tartalomhoz.

Ha a tartalom részletes, felhasználónkénti vezérlését igényli, biztonsági szűrőket készíthet a lekérdezésekhez, és visszaküldheti az adott biztonsági identitáshoz társított dokumentumokat. Az előre definiált szerepkörök és szerepkör-hozzárendelések helyett az identitás-alapú hozzáférés-vezérlés olyan *szűrőként* van megvalósítva, amely identitások alapján metszi a dokumentumok és tartalmak keresési eredményeit. Az alábbi táblázat két módszert ismertet a jogosulatlan tartalom keresési eredményeinek kivágására.

| A módszer | Leírás |
|----------|-------------|
|[Biztonsági körülvágás identitás-szűrők alapján](search-security-trimming-for-azure-search.md)  | Dokumentálja a felhasználói identitás hozzáférés-vezérlésének megvalósításához szükséges alapszintű munkafolyamatot. Ismerteti a biztonsági azonosítók indexbe való hozzáadását, majd a tiltott tartalom eredményének kivágására szolgáló mező szűrését ismerteti. |
|[Biztonsági kivágás Azure Active Directory identitások alapján](search-security-trimming-for-azure-search-with-aad.md)  | Ez a cikk az előző cikkben található, amely a Azure Active Directory (HRE) identitások beolvasásának lépéseit ismerteti az Azure Cloud platform egyik [ingyenes szolgáltatásával](https://azure.microsoft.com/free/) . |

## <a name="table-permissioned-operations"></a>Tábla: engedélyezett műveletek

Az alábbi táblázat összefoglalja az Azure Cognitive Searchban engedélyezett műveleteket, és egy adott művelethez hozzáférő kulcs feloldja azokat.

| Művelet | Engedélyek |
|-----------|-------------------------|
| Szolgáltatás létrehozása | Azure-előfizetés tulajdonosa|
| Szolgáltatás méretezése | Rendszergazdai kulcs, RBAC tulajdonos vagy közreműködő az erőforráson  |
| Szolgáltatás törlése | Rendszergazdai kulcs, RBAC tulajdonos vagy közreműködő az erőforráson |
| Objektumok létrehozása, módosítása és törlése a szolgáltatásban: <br>Indexek és összetevők (beleértve az analizátor-definíciókat, a pontozási profilokat, a CORS lehetőségeket), az indexelő, az adatforrások, a szinonimák, a javaslatok. | Rendszergazdai kulcs, RBAC tulajdonos vagy közreműködő az erőforráson  |
| Index lekérdezése | Rendszergazdai vagy lekérdezési kulcs (a RBAC nem alkalmazható) |
| A rendszerinformációk lekérdezése, például statisztikai adatok, Darabszámok és az objektumok listája. | Rendszergazdai kulcs, RBAC az erőforráson (tulajdonos, közreműködő, olvasó) |
| Rendszergazdai kulcsok kezelése | Rendszergazdai kulcs, RBAC tulajdonos vagy közreműködő az erőforráson. |
| Lekérdezési kulcsok kezelése |  Rendszergazdai kulcs, RBAC tulajdonos vagy közreműködő az erőforráson.  |

## <a name="physical-security"></a>Fizikai biztonság

A Microsoft adatközpontok piacvezető fizikai biztonságot biztosítanak, és a szabványoknak és előírásoknak való széleskörű portfólióval rendelkeznek. További információért nyissa meg a [globális adatközpontok](https://www.microsoft.com/cloud-platform/global-datacenters) lapot, vagy tekintse meg az adatközpont biztonságáról szóló rövid videót.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Lásd még

+ [A .NET használatának első lépései (bemutatjuk, hogy egy rendszergazdai kulcs használatával hozzon létre egy indexet)](search-create-index-dotnet.md)
+ [Ismerkedés a REST szolgáltatással (a bemutatja, hogyan hozhat létre indexet egy rendszergazdai kulccsal)](search-create-index-rest-api.md)
+ [Identitás-alapú hozzáférés-vezérlés az Azure Cognitive Search szűrők használatával](search-security-trimming-for-azure-search.md)
+ [Active Directory identitás-alapú hozzáférés-vezérlés az Azure Cognitive Search szűrőkkel](search-security-trimming-for-azure-search-with-aad.md)
+ [Szűrők az Azure Cognitive Search](search-filters.md)