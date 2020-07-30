---
title: Biztonsági áttekintés
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search megfelel a SOC 2, a HIPAA és más minősítéseknek. A kapcsolat és az adattitkosítás, a hitelesítés és az identitás hozzáférése a felhasználók és a csoport biztonsági azonosítói alapján a szűrési kifejezésekben.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 55ee6e99cdf6d77ea1e78799e016d4c276e85fcd
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423864"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Biztonság az Azure Cognitive Searchban – áttekintés

Ez a cikk az Azure Cognitive Search legfontosabb biztonsági funkcióit ismerteti, amelyek a tartalom és a műveletek védelmére használhatók. 

+ A tárolási rétegben a többhelyes titkosítás a platform szintjén van megadva, de a Cognitive Search "kettős titkosítást" is biztosít azon ügyfelek számára, akik a felhasználók és a Microsoft által felügyelt kulcsok kettős védelmét szeretnék.

+ A bejövő biztonság a keresési szolgáltatási végpontot a biztonsági szintek növekvő szintjén védi: a kérés API-kulcsaitól, a tűzfal bejövő szabályaitól a szolgáltatás teljes körű védelmét biztosító privát végpontok számára a nyilvános internetről.

+ A kimenő biztonság a külső forrásokból származó tartalmat lekérő indexelő eszközökre vonatkozik. A kimenő kérelmek esetében hozzon létre egy felügyelt identitást, hogy a megbízható szolgáltatásban keressen az Azure Storage, az Azure SQL, a Cosmos DB vagy más Azure-adatforrások adataihoz való hozzáférés során. A felügyelt identitás a hitelesítő adatok vagy a hozzáférési kulcsok helyettesítése a kapcsolaton. A kimenő biztonságot ebben a cikkben nem tárgyaljuk. További információ erről a képességről: [Csatlakozás adatforráshoz felügyelt identitás használatával](search-howto-managed-identities-data-sources.md).

Tekintse meg ezt a gyors iramú videót a biztonsági architektúra és az egyes szolgáltatások kategóriáinak áttekintéséhez.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="encrypted-transmissions-and-storage"></a>Titkosított átvitelek és tárolók

A titkosítás az Azure Cognitive Searchban, a kapcsolatok és az átvitelek megkezdésével, a lemezen tárolt tartalom kiterjesztésével. A nyilvános interneten található keresési szolgáltatások esetében az Azure Cognitive Search a 443-es HTTPS-portot figyeli. Az összes ügyfél és szolgáltatás közötti kapcsolat TLS 1,2 titkosítást használ. A korábbi verziók (1,0 vagy 1,1) nem támogatottak.

### <a name="data-encryption-at-rest"></a>Inaktív adatok titkosítása

Az Azure Cognitive Search az index definícióit és tartalmát, az adatforrás-definíciókat, az indexelő definíciókat, a készségkészlet-definíciókat és a szinonimákat is tartalmazza.

A tárolási rétegben az adatai a Microsoft által kezelt kulcsokkal titkosítva vannak a lemezen. A titkosítás be-és kikapcsolása nem lehetséges, vagy a titkosítási beállítások megtekinthetők a portálon, vagy programozott módon. A titkosítás teljes mértékben belső, és az indexelési idő – befejezés vagy az index mérete nem mérhető. Automatikusan megtörténik az összes indexelésnél, beleértve az olyan index növekményes frissítését is, amely nem teljesen titkosított (január 2018. előtt jött létre).

Belsőleg a titkosítás az [Azure Storage Service Encryptionon](../storage/common/storage-service-encryption.md)alapul, és 256 bites AES- [titkosítást](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)használ.

> [!NOTE]
> A inaktív adatok titkosítása 2018. január 24-én jelent meg, és minden szolgáltatási szinten érvényes, beleértve az ingyenes szintet is minden régióban. A teljes titkosításhoz az adott dátum előtt létrehozott indexeket el kell dobni, és újból létre kell hozni a titkosítás megkezdése érdekében. Ellenkező esetben csak a január 24 után hozzáadott új adatforgalom titkosítva van.

### <a name="customer-managed-key-cmk-encryption"></a>Ügyfél által felügyelt kulcs (CMK) titkosítása

Azok az ügyfelek, akik további tárterület-védelemmel szeretnék titkosítani az adataikat és az objektumokat, mielőtt azokat tárolják és titkosítsák a lemezen. Ez a módszer egy felhasználó által birtokolt kulcson alapul, amelyet a Microsofttól függetlenül Azure Key Vault felügyel és tárol. A tartalom titkosítása a lemezre titkosítás előtt "kettős titkosításnak" nevezzük. Az indexek és a szinonimák leképezése jelenleg szelektíven is elvégezhető. További információ: [ügyfél által felügyelt titkosítási kulcsok az Azure Cognitive Searchban](search-security-manage-encryption-keys.md).

> [!NOTE]
> A CMK titkosítás általánosan elérhető a január 2019 után létrehozott keresési szolgáltatásokhoz. Ingyenes (megosztott) szolgáltatásokban nem támogatott. 
>
>A funkció engedélyezése növeli az index méretét és csökkenti a lekérdezési teljesítményt. Az eddigi megfigyelések alapján a lekérdezési időpontokban 30%-60%-os növekedés várható, bár a tényleges teljesítmény az index definíciója és a lekérdezések típusaitól függően változhat. A teljesítményre gyakorolt hatás miatt javasoljuk, hogy ezt a funkciót csak olyan indexeken engedélyezze, amelyekhez valóban szükség van.

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>Bejövő biztonsági és Endpoint Protection

A bejövő biztonsági funkciók a keresési szolgáltatás végpontját a biztonság és a bonyolultság szintjének növelésével védik. Először is minden kérelemhez API-kulcs szükséges a hitelesített hozzáféréshez. Másodszor beállíthatja a tűzfalszabályok számára, hogy meghatározott IP-címekre korlátozza a hozzáférést. A speciális védelem érdekében a harmadik lehetőség az, hogy engedélyezze az Azure privát hivatkozását, hogy a szolgáltatás végpontját minden internetes forgalomból megvédje.

### <a name="public-access-using-api-keys"></a>Nyilvános hozzáférés API-kulcsok használatával

Alapértelmezés szerint a keresési szolgáltatás a nyilvános felhőn keresztül érhető el, kulcs alapú hitelesítéssel, rendszergazdai vagy lekérdezési hozzáféréssel a keresési szolgáltatás végpontja számára. Az API-Key egy véletlenszerűen generált számokból és betűkből álló karakterlánc. A kulcs típusa (rendszergazda vagy lekérdezés) határozza meg a hozzáférési szintet. Az érvényes kulcs beküldése igazolja, hogy a kérelem megbízható entitásból származik. 

A keresési szolgáltatásnak két hozzáférési szintje van, amelyeket a következő API-kulcsok engedélyeznek:

+ Rendszergazdai kulcs (olvasási és írási hozzáférés engedélyezése a Search szolgáltatásban a [create-Read-Update-delete](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) műveletekhez)

+ Lekérdezési kulcs (csak olvasási hozzáférést biztosít az index dokumentumainak gyűjteményéhez)

Az *adminisztrátori kulcsok* a szolgáltatás kiépített állapotában jönnek létre. Két rendszergazdai kulcs van kijelölve *elsődlegesként* és *másodlagosként* , hogy azok egyenesek maradjanak, de valójában felcserélhetők. Minden szolgáltatásnak két rendszergazdai kulcsa van, így a szolgáltatáshoz való hozzáférés elvesztése nélkül végezheti el a bevezetést. Az Azure biztonsági eljárásainak rendszeres időközönként újra [létrehozhatja az adminisztrátori kulcsot](search-security-api-keys.md#regenerate-admin-keys) , de a felügyeleti kulcsok teljes számát nem lehet hozzáadni. Keresési szolgáltatásokban legfeljebb két rendszergazdai kulcs lehet.

A *lekérdezési kulcsok* szükség szerint jönnek létre, és a lekérdezéseket kiállító ügyfélalkalmazások számára lettek kialakítva. Akár 50 lekérdezési kulcsot is létrehozhat. Az alkalmazás kódjában megadhatja a keresési URL-címet és egy lekérdezési API-kulcsot, amely lehetővé teszi a csak olvasási hozzáférést egy adott index dokumentum-gyűjteményéhez. Együtt, a végpont, a csak olvasási hozzáféréssel rendelkező API-kulcs és a célként megadott index határozza meg a kapcsolat hatókörét és hozzáférési szintjét az ügyfélalkalmazás alapján.

Minden kérelem esetében hitelesítésre van szükség, amelyben minden kérelem egy kötelező kulcsból, egy műveletből és egy objektumból áll. Ha együtt van összekapcsolva, a két jogosultsági szint (teljes vagy csak olvasható) és a környezet (például egy index lekérdezési művelete) elegendő a szolgáltatási műveletek teljes spektrumú biztonságának biztosításához. A kulcsokkal kapcsolatos további információkért lásd: [API-kulcsok létrehozása és kezelése](search-security-api-keys.md).

### <a name="ip-restricted-access"></a>IP-korlátozott hozzáférés

A keresési szolgáltatáshoz való hozzáférés további szabályozásához olyan bejövő tűzfalszabályok hozhatók létre, amelyek lehetővé teszik az adott IP-cím vagy IP-címtartomány elérését. Az összes ügyfélkapcsolatot egy engedélyezett IP-címen keresztül kell elvégezni, vagy a kapcsolat megtagadva.

A portál használatával [konfigurálhatja a bejövő hozzáférést](service-configure-firewall.md). 

Másik lehetőségként használhatja a felügyeleti REST API-kat is. Az 2020-03-13-es API-verzió a [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule) paraméterrel lehetővé teszi a szolgáltatáshoz való hozzáférés korlátozását úgy, hogy az IP-címeket, egyenként vagy egy tartományon belül azonosítja, hogy hozzáférést kíván biztosítani a keresési szolgáltatáshoz. 

### <a name="private-endpoint-no-internet-traffic"></a>Magánhálózati végpont (nincs internetes forgalom)

Az Azure Cognitive Search [privát végpontja](../private-link/private-endpoint-overview.md) lehetővé teszi, hogy egy [virtuális hálózaton](../virtual-network/virtual-networks-overview.md) lévő ügyfél biztonságosan hozzáférhessen egy keresési indexben lévő adathoz egy [privát hivatkozáson](../private-link/private-link-overview.md)keresztül. 

A magánhálózati végpont a virtuális hálózati címtartomány IP-címét használja a keresési szolgáltatáshoz való kapcsolódáshoz. Az ügyfél és a keresési szolgáltatás közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinc hálózatán található privát kapcsolaton, ami kiküszöböli a nyilvános internetről való kitettséget. A VNET lehetővé teszi az erőforrások, a helyszíni hálózat és az Internet közötti biztonságos kommunikációt. 

Habár ez a megoldás a legbiztonságosabb, a további szolgáltatások használata további költségeket jelent, ezért ügyeljen arra, hogy egyértelművé váljon a kimerülés előtti előnyök. a költségekkel kapcsolatos további információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/private-link/). Ha többet szeretne megtudni arról, hogy ezek az összetevők hogyan működnek együtt, tekintse meg a cikk tetején található videót. A privát végponti beállítás lefedettsége 5:48-kor kezdődik a videóban. A végpont beállításával kapcsolatos útmutatásért lásd: [privát végpont létrehozása az Azure Cognitive Searchhoz](service-create-private-endpoint.md).

## <a name="index-access"></a>Indexelési hozzáférés

Az Azure Cognitive Searchban az egyes indexek nem biztonságos objektumok. Ehelyett az indexhez való hozzáférést a szolgáltatási réteg határozza meg (olvasási vagy írási hozzáférés a szolgáltatáshoz), valamint egy művelet kontextusával.

A végfelhasználói hozzáféréshez a lekérdezési kérelmeket a lekérdezési kulcs használatával lehet összeszervezni, amely minden kérés írásvédett, és tartalmazza az alkalmazás által használt adott indexet. Egy lekérdezési kérelemben nincs olyan fogalom, amely az indexek összekapcsolását vagy egyszerre több index elérését teszi elérhetővé, így az összes kérelem egyetlen indexet céloz meg definíció szerint. Ennek megfelelően a lekérdezési kérelem (egy kulcs plusz egy célként megadott index) kialakítása határozza meg a biztonsági határt.

Az indexekhez való rendszergazdai és fejlesztői hozzáférés nem különbözik: a szolgáltatás által felügyelt objektumok létrehozásához, törléséhez és frissítéséhez egyaránt írási hozzáférésre van szükség. A szolgáltatáshoz tartozó rendszergazdai kulccsal bárki megtekintheti, módosíthatja vagy törölheti ugyanazon szolgáltatás bármelyik indexét. Az indexek véletlen vagy rosszindulatú törlésével szembeni védelem érdekében a belső verziókövetés a kód eszközeire a nemkívánatos indexek törlésének vagy módosításának megfordítására szolgáló megoldás. Az Azure Cognitive Search a rendelkezésre állás biztosítása érdekében feladatátvételt hajt végre a fürtön belül, de nem tárolja és nem hajtja végre az indexek létrehozásához vagy betöltéséhez használt tulajdonosi kódot.

Az index szintjén biztonsági határokat igénylő bérlős megoldások esetében az ilyen megoldások jellemzően olyan középső szintet tartalmaznak, amelyet az ügyfelek az indexek elkülönítésének kezelésére használnak. További információ a több-bérlős használati esetről: [tervezési minták a több-bérlős SaaS-alkalmazásokhoz és az Azure Cognitive Search](search-modeling-multitenant-saas-applications.md).

## <a name="user-access"></a>Felhasználói hozzáférés

Az index és az egyéb objektumok elérésének módja a kérelemben szereplő API-kulcs típusa alapján történik. A legtöbb fejlesztő a [*lekérdezési kulcsokat*](search-security-api-keys.md) az ügyféloldali keresési kérelmekhez hozza létre és rendeli hozzá. A lekérdezési kulcs csak olvasási hozzáférést biztosít az indexen belüli kereshető tartalmakhoz.

Ha részletes, felhasználónkénti vezérlést igényel a keresési eredmények között, biztonsági szűrőket készíthet a lekérdezésekhez, és visszaküldheti az adott biztonsági identitáshoz társított dokumentumokat. Az előre definiált szerepkörök és szerepkör-hozzárendelések helyett az identitás-alapú hozzáférés-vezérlés olyan *szűrőként* van megvalósítva, amely identitások alapján metszi a dokumentumok és tartalmak keresési eredményeit. Az alábbi táblázat két módszert ismertet a jogosulatlan tartalom keresési eredményeinek kivágására.

| A módszer | Description |
|----------|-------------|
|[Biztonsági körülvágás identitás-szűrők alapján](search-security-trimming-for-azure-search.md)  | Dokumentálja a felhasználói identitás hozzáférés-vezérlésének megvalósításához szükséges alapszintű munkafolyamatot. Ismerteti a biztonsági azonosítók indexbe való hozzáadását, majd a tiltott tartalom eredményének kivágására szolgáló mező szűrését ismerteti. |
|[Biztonsági kivágás Azure Active Directory identitások alapján](search-security-trimming-for-azure-search-with-aad.md)  | Ez a cikk az előző cikkben található, amely a Azure Active Directory (HRE) identitások beolvasásának lépéseit ismerteti az Azure Cloud platform egyik [ingyenes szolgáltatásával](https://azure.microsoft.com/free/) . |

## <a name="administrative-rights"></a>Rendszergazdai jogosultságok

A [szerepköralapú hozzáférés (RBAC)](../role-based-access-control/overview.md) az Azure-erőforrások kiépítésére [Azure Resource Manager](../azure-resource-manager/management/overview.md) épülő engedélyezési rendszer. Az Azure Cognitive Search a Resource Manager használatával létrehozhatja vagy törölheti a szolgáltatást, kezelheti az API-kulcsokat, és méretezheti a szolgáltatást. Az Azure-beli szerepkör-hozzárendelések határozzák meg, hogy kik tudják elvégezni ezeket a feladatokat, függetlenül attól, hogy a [portált](search-manage.md), a [PowerShellt](search-manage-powershell.md)vagy a [felügyeleti REST API-kat](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)használják.

Ezzel szemben a szolgáltatásban üzemeltetett tartalommal kapcsolatos rendszergazdai jogosultságokat, például az indexek létrehozását vagy törlését, az API-kulcsok az [előző szakaszban](#index-access)leírtak szerint.

> [!TIP]
> Az Azure-ra kiterjedő mechanizmusok használatával zárolhatja az előfizetést vagy az erőforrást, így megakadályozhatja a keresési szolgáltatás véletlen vagy jogosulatlan törlését rendszergazdai jogosultságokkal rendelkező felhasználók számára. További információ: [erőforrások zárolása a váratlan törlés megakadályozása érdekében](../azure-resource-manager/management/lock-resources.md).

## <a name="certifications-and-compliance"></a>Minősítések és megfelelőség

Az Azure Cognitive Search a nyilvános felhő és a Azure Government esetében több globális, regionális és iparági szabványnak megfelelő előírásoknak is megfelelt. A teljes listához töltse le a [ **Microsoft Azure megfelelőségi ajánlatokat** ](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) a hivatalos naplózási jelentések oldaláról.

## <a name="see-also"></a>Lásd még

+ [Azure-biztonság – Alapismeretek](../security/fundamentals/index.yml)
+ [Azure-biztonság](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)