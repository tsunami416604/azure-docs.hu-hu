---
title: Azure Blob Storage-beli adatkezelés és-keresés a blob-Indextel (előzetes verzió)
description: Megtudhatja, hogyan használhatja a blob-indexelési címkéket a blob-objektumok felderítéséhez, kezeléséhez és lekérdezéséhez.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 5b41609ec2b7cc9880fb22a76b9e3b40c315bc3c
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499874"
---
# <a name="manage-and-find-data-on-azure-blob-storage-with-blob-index-preview"></a>Azure Blob Storage-beli adatkezelés és-keresés a blob-Indextel (előzetes verzió)

Mivel az adatkészletek nagyobb és nagyobb méretűek, az adattengerek egy adott objektumának megkeresése nehéz és bosszantó lehet. A blob indexe adatkezelési és felderítési képességeket biztosít a kulcs-érték index címke attribútumaival, amelyek lehetővé teszik, hogy egyetlen tárolón vagy a Storage-fiókban lévő összes tárolón belül kategorizálja és megkeresse az objektumokat. Később, az adatváltozások követelményeinek megfelelően az objektumok dinamikusan újrakategorizálható az index-címkék frissítésével, miközben az aktuális tároló szervezetük továbbra is helyben marad. A blob-index használatával egyszerűsítheti a fejlesztést azáltal, hogy a blob-és a társított index-attribútumokat ugyanazon a szolgáltatáson összesíti. lehetővé teszi, hogy hatékony és méretezhető alkalmazásokat hozzon létre a natív funkciók használatával. 

A blob index a következőket teszi lehetővé:

- A Blobok dinamikusan kategorizálása a kulcs-érték indexelési címkék használatával az adatkezeléshez
- Adott címkézett Blobok gyors keresése egyetlen tárolóban vagy egy teljes Storage-fiókban
- Feltételes viselkedés megadása a blob API-k számára az index címkék kiértékelése alapján
- Indexelő címkék használata speciális vezérlőkhöz a blob platform szolgáltatásaihoz, például az [életciklus-kezeléshez](storage-lifecycle-management-concepts.md)

Vegye figyelembe azt a forgatókönyvet, ahol több millió blob található a Storage-fiókban, amelyet számos különböző alkalmazás írt és elért. Egyetlen projekt összes kapcsolódó adatát szeretné megkeresni, de nem biztos benne, hogy mi van a hatókörben, mivel az adatok több tárolóban is elterjedhetnek, különböző blob elnevezési konvenciókkal. Azonban biztos abban, hogy az alkalmazások az összes olyan címkét feltöltik az összes adattal, amely a megfelelő projekten és a leíráson alapul. Ahelyett, hogy több millió blobot keres, és nem hasonlítja össze a neveket és a tulajdonságokat, egyszerűen használhatja `Project = Contoso` felderítési feltételként. A blob index a teljes Storage-fiókban lévő összes tárolót szűri, így gyorsan megkeresheti és visszaküldheti a 50-es Blobok készletét `Project = Contoso` . 

A blob index használatára vonatkozó példákkal kapcsolatban lásd: [blob index használata az adatkezeléshez és az adatkereséshez](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>BLOB-indexek címkéi és adatkezelés

A tároló és a blob neve előtag egydimenziós kategorizálás a tárolt adatai számára. A blob index mostantól lehetővé teszi a többdimenziós kategorizálást az alkalmazott attribútum címkékkel rendelkező összes [blob-adattípushoz (blokk, Hozzáfűzés vagy lap)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) . Ez a többdimenziós kategorizálás natív módon indexelve van, és elérhetővé teszi az adatait gyors lekérdezéséhez és kereséséhez.

Vegye figyelembe a következő öt blobot a Storage-fiókban:
>
> container1/transaction.csv  
> container2/campaign.docx  
> fényképek/bannerphoto.png  
> Archívum/befejezett/2019review.pdf  
> naplók/2020/01/01/logfile.txt  
>

Ezek a Blobok jelenleg a tároló/virtuális mappa vagy a blob nevének előtagja alapján vannak elválasztva. A blob index használatával az alábbi öt blobon beállíthatja az index címke attribútumát, hogy azok együtt legyenek `Project = Contoso` kategorizálva az aktuális előtag-szervezetük megtartása mellett. Így nem kell áthelyeznie az adatok áthelyezését azáltal, hogy lehetővé teszi az adatok szűrését és keresését a Storage platform többdimenziós indexének használatával.

## <a name="setting-blob-index-tags"></a>BLOB-index címkék beállítása

A blob index címkéi olyan kulcs-érték attribútumok, amelyek alkalmazhatók a Storage-fiókjában lévő új vagy meglévő objektumokra is. A feltöltési folyamat során megadhatja az index címkéit a PutBlob, a PutBlockList vagy a CopyBlob művelettel, valamint az opcionális x-MS-Tags fejléc használatával. Ha már rendelkezik Blobokkal a Storage-fiókban, meghívhatja a SetBlobTags egy formázott XML-dokumentummal, amely a kérelem törzsében adja meg a blob index címke attribútumait. 

Vegye figyelembe a következő példákkal megadható címkéket

A blobon egyetlen címkét alkalmazhat, amely leírja, hogy mikor végzett az adatai feldolgozásával.
>
> "processedDate" = "2020-01-01"
>
A blobon több címkét is alkalmazhat, hogy az adatai jobban meglegyenek.
>
> "Projekt" = "contoso"  
> "Besorolt" = "true"  
> "Status" = "feldolgozatlan"  
> "Priority" = "01" 
>

A meglévő index címke attribútumainak módosításához először le kell kérnie a meglévő címke attribútumait, módosítania kell a címke attribútumait, és cserélnie kell a SetBlobTags művelettel. Ha az összes index címkét el szeretné távolítani a blobból, hívja meg a SetBlobTags műveletet, és ne adja meg a címke attribútumait. Mivel a blob-index címkéi a blob-adattartalomhoz tartozó alerőforrások, a SetBlobTags nem módosítja a mögöttes tartalmakat, és nem módosítja a blob utolsó módosításának idejét vagy eTag (Entity címke). A jelenlegi alapblobok és a korábbi verziók indexelési címkéi is létrehozhatók vagy módosíthatók. a pillanatképek vagy a törölt Blobok címkéi azonban nem módosíthatók. 

A következő korlátozások vonatkoznak a blob index címkékre:
- Minden blob legfeljebb 10 blob-index címkével rendelkezhet
- A címke kulcsainak 1 – 128 karakter közé kell esnie
- A címke értékének 0 és 256 karakter közöttinek kell lennie
- A kulcsok és értékek címkézése megkülönbözteti a kis-és nagybetűket
- A kulcsok és az értékek címkézése csak a karakterlánc-adattípusokat támogatja; a rendszer karakterláncként menti a számokat, a dátumot, az időpontokat és a speciális karaktereket.
- A kulcsok és értékek címkézésének meg kell felelnie a következő elnevezési szabályoknak:
  - Alfanumerikus karakterek: a-z, A-Z, 0-9
  - Speciális karakterek: szóköz, plusz, mínusz, pont, kettőspont, egyenlő, aláhúzás, továbbítási perjel

## <a name="getting-and-listing-blob-index-tags"></a>BLOB-index címkék beolvasása és listázása

A blob-indexek címkéi a blob-adatok mellett alerőforrásként vannak tárolva, és az alapul szolgáló blob-adatoktól függetlenül kérhetők le. A beállítás után a blob-index címkéi egy adott blobhoz azonnal lekérhető és ellenőrizhető a GetBlobTags művelettel. A ListBlobs művelet a (z `include:tags` ) paraméterrel együtt a tárolóban lévő összes blobot is visszaküldi az alkalmazott blob-index címkékkel. 

A legalább 1 blob index címkével rendelkező Blobok esetében a rendszer az x-MS-tag-Count értéket adja vissza a ListBlobs, a GetBlob és a GetBlobProperties műveletekben, amelyek a blob-index címkéi számát jelzik.

## <a name="finding-data-using-blob-index-tags"></a>Az adatkeresés a blob-index címkékkel

Az indexelési motor a blob-index címkéi alapján állítja be a kulcs/érték attribútumokat egy többdimenziós indexbe. Amíg az index-címkék már léteznek a blobon, és azonnal beolvashatók, eltarthat egy ideig, amíg a blob-index frissül a frissített index címke attribútumaival. A blob-index frissítése után már kihasználhatja a blob Storage által kínált natív lekérdezési és felderítési funkciókat.

A FindBlobsByTags művelet lehetővé teszi olyan Blobok szűrt visszatérési készletének beolvasását, amelyek indexelési címkéi megegyeznek egy adott blob-index lekérdezési kifejezésével. A blob index támogatja a Storage-fiókban lévő összes tároló szűrését, vagy a szűrést egyetlen tárolóra is szűkítheti. Mivel az összes blob index címke kulcsa és értéke karakterlánc, a támogatott viszonyítási operátorok egy lexikográfiai rendezést használnak az index címke értékein.

A blob index szűrésére a következő feltételek érvényesek:
-   A címke kulcsai idézőjelek közé kell, hogy legyenek (")
-   A címkézési értékeket és a tárolók nevét szimpla idézőjelek közé kell foglalni (')
-   A @ karakter csak egy adott tároló nevében (pl. @container = "ContainerName") való szűrésre engedélyezett.
- A szűrők lexikográfiai-rendezéssel lesznek alkalmazva a sztringeken
-   Ugyanazon a kulcson azonos egyoldalas tartománybeli műveletek érvénytelenek (azaz "Rank" > "10" és "Rank" >= "15")
- Ha a REST használatával hoz létre egy szűrési kifejezést, a karaktereknek URI-kódolással kell rendelkezniük.

Az alábbi táblázatban a FindBlobsByTags összes érvényes operátora látható:

|  Operátor  |  Leírás  | Példa |
|------------|---------------|---------|
|     =      |     Egyenlő     | "Status" = "folyamatban" | 
|     >      |  Nagyobb |  "Date" > "2018-06-18" |
|     >=     |  Nagyobb vagy egyenlő mint | "Prioritás" >= "5" | 
|     <      |  Kisebb    | "Age" < "32" |
|     <=     |  Kisebb vagy egyenlő  | "Cég" <= "contoso" |
|    AND     |  Logikai és  | "Rang" >= "010" és "Rank" < "100" |
| @container |  Hatókör egy adott tárolóra   | @container= "videofiles" és "status" = "kész" |

> [!NOTE]
> A címkék beállításakor és lekérdezése során Ismerje meg a lexicographical megrendelését.
> - A számok a betűk előtt vannak rendezve. A számok az első számjegy alapján vannak rendezve.
> - A nagybetűket a kisbetűs karakterek előtt rendezi a rendszer.
> - A szimbólumok nem szabványosak. Egyes szimbólumok a numerikus értékek előtt vannak rendezve. A többi szimbólum a levelek előtt vagy után van rendezve.
>

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Feltételes blob-műveletek a blob-index címkékkel
Az 2019-10-10-as és újabb REST-verziók esetében a legtöbb [blob Service API](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs) mostantól támogatja a feltételes fejlécet, az x-MS-if-Tags-t, így a művelet csak akkor lesz sikeres, ha a megadott blob-index feltétele teljesül. Ha a feltétel nem teljesül, a rendszer lekérdezi `error 412: The condition specified using HTTP conditional header(s) is not met` .

Előfordulhat, hogy az x-MS-if-Tags fejléc kombinálva van a többi meglévő HTTP feltételes fejléctel (IF-Match, If-None-Match stb.).  Ha egy kérelemben több feltételes fejléc van megadva, akkor a művelet sikerességéhez minden esetben igaz értéket kell kiértékelni.  A feltételes fejlécek hatékonyan kombinálhatók a logikai és a szolgáltatással. 

Az alábbi táblázatban a feltételes műveletek összes érvényes operátora látható:

|  Operátor  |  Leírás  | Példa |
|------------|---------------|---------|
|     =      |     Egyenlő     | "Status" = "folyamatban" |
|     <>     |   Nem egyenlő   | "Állapot"  <>  "kész"  | 
|     >      |  Nagyobb |  "Date" > "2018-06-18" |
|     >=     |  Nagyobb vagy egyenlő mint | "Prioritás" >= "5" | 
|     <      |  Kisebb    | "Age" < "32" |
|     <=     |  Kisebb vagy egyenlő  | "Cég" <= "contoso" |
|    AND     |  Logikai és  | "Rang" >= "010" és "Rank" < "100" |
|     VAGY     |  Logikai vagy   | "Status" = "kész" vagy "priority" >= "05" |

> [!NOTE]
> Két további operátor létezik, nem egyenlő és logikai vagy, amelyek a blob művelet feltételes x-MS-if-Tags fejlécében engedélyezettek, de nem léteznek a FindBlobsByTags műveletben.

## <a name="platform-integrations-with-blob-index-tags"></a>Platform-integrációk a blob index címkékkel

A blob-indexek címkéi nem csupán a blob-adataik kategorizálását, kezelését és keresését teszik lehetővé, hanem más Blob service szolgáltatásokkal, például az [életciklus-kezeléssel](storage-lifecycle-management-concepts.md)való integrációt is biztosítanak. 

### <a name="lifecycle-management"></a>Életciklus-kezelés

Az új blobIndexMatch használata az életciklus-kezelésben, áthelyezheti az adatait a hűvösebb rétegekre, vagy törölheti az adatait a blobokra alkalmazott indexelési címkék alapján. Ez lehetővé teszi, hogy részletesebb legyen a szabályokban, és csak akkor helyezze át vagy törölje az adatokat, ha megfelelnek a megadott címkék feltételeinek.

A blob-indexeket beállíthatja úgy, hogy egy életciklus-szabályban beállított önálló szűrőként alkalmazza a címkézett adatműveleteket. Vagy kombinálhatja az előtag-egyezést és a blob-index egyezését, hogy azok megfeleljenek az egyes adatkészleteknek. Ha több szűrőt alkalmaz egy életciklus-szabályra, az egy logikai és egy művelet, amely szerint a művelet csak akkor érvényes, ha az összes szűrési feltétel egyezik. 

A következő minta életciklus-kezelési szabály a "videofiles" tárolóban lévő blobok és a rétegek Blobok archiválására vonatkozik, ha az adatok megfelelnek a blob-index címkéi feltételeinek ```"Status" = 'Processed' AND "Source" == 'RAW'``` .

# <a name="portal"></a>[Portál](#tab/azure-portal)
![A blob-index egyezési szabálya a Azure Portal életciklus-felügyeletére vonatkozó példa](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)
   
# <a name="json"></a>[JSON](#tab/json)
```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```
---

## <a name="permissions-and-authorization"></a>Engedélyek és engedélyezés

Az alábbi módszerek egyikével engedélyezheti a blob-indexhez való hozzáférést:

- Szerepköralapú hozzáférés-vezérlés (RBAC) használatával engedélyeket adhat egy Azure Active Directory (Azure AD) rendszerbiztonsági tag számára. A Microsoft az Azure AD használatát javasolja a kiváló biztonság és a könnyű használat érdekében. További információ az Azure AD és a blob-műveletek használatáról: a Blobok [és várólisták hozzáférésének engedélyezése Azure Active Directory használatával](../common/storage-auth-aad.md).
- Közös hozzáférésű aláírás (SAS) használatával a blob-indexhez való hozzáférés delegálására. A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../common/storage-sas-overview.md).
- A fiók-hozzáférési kulcsok használatával engedélyezze a megosztott kulccsal rendelkező műveleteket. További információ: [Engedélyezés megosztott kulccsal](/rest/api/storageservices/authorize-with-shared-key).

A blob index címkéi a blob-adatforrások. Előfordulhat, hogy a Blobok olvasásához vagy írásához szükséges engedélyekkel rendelkező felhasználó vagy SAS-token nem fér hozzá a blob-index címkéhez. 

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés 
A [HRE-identitást](../common/storage-auth-aad.md) használó hívók a következő engedélyeket kaphatják meg a blob-index címkén való működéshez. 

|   BLOB-műveletek   |  RBAC művelet   |
|---------------------|----------------|
| Blobok keresése címkék alapján  | Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/Filter/művelet |
| BLOB-címkék beállítása         | Microsoft. Storage/storageAccounts/blobServices/tárolók/Blobok/címkék/írás | 
| BLOB-címkék beolvasása         | Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/címkék/olvasás |

Az alapul szolgáló blob-adatoktól elkülönítve további engedélyek szükségesek a címkéken való működéshez. A Storage blob adatközreműködői szerepkör mindhárom engedély közül mindhárom engedélyt megkapja. A Storage blob-Adatolvasó csak a Címkék keresése és a blob-címkék engedélyeinek lekérdezése után kapja meg az egyetlen keresési blobot.

### <a name="sas-permissions"></a>SAS-engedélyek 
A [közös hozzáférésű aláírást (SAS)](../common/storage-sas-overview.md) használó hívók hatókörön belüli engedélyeket kaphatnak a blob-címkéken való működéshez.
#### <a name="blob-sas"></a>BLOB SAS
A következő engedélyek adhatók meg egy Blob service SAS-ben a blob-indexhez való hozzáférés engedélyezéséhez. A blob olvasási és írási engedélyei önmagában nem elégek ahhoz, hogy lehetővé tegyék az indexelési címkék olvasását vagy írását.

|  Engedély  |  URI-szimbólum  | Engedélyezett műveletek |
|--------------|--------------|--------------------|
|  Címkék indexelése  |      t         | BLOB-indexekhez tartozó címkék beolvasása és beállítása blobokhoz |

#### <a name="container-sas"></a>Tároló SAS
A következő engedélyek engedélyezhetők a Container Service SAS-ben a blob-címkék szűrésének engedélyezéséhez.  A blob List engedély nem elegendő ahhoz, hogy lehetővé tegye a Blobok indexelési címkékkel való szűrését.

|  Engedély  |  URI-szimbólum  | Engedélyezett műveletek |
|--------------|--------------|--------------------|
| Címkék indexelése     |      f       | Blobok keresése blob-indextel címkékkel | 

## <a name="choosing-between-metadata-and-blob-index-tags"></a>A metaadatok és a blob-index címkék közötti választás 
A blob-index címkéi és metaadatai egyaránt lehetővé teszik a felhasználó által definiált kulcs/érték tulajdonságok tetszőleges számú tárolását egy blob-erőforrás mellett. Mindkettő lekérhető és beállítható közvetlenül a blob tartalmának visszaküldése vagy módosítása nélkül. A metaadatok és az indexelési címkék egyaránt használhatók.

Azonban csak a blob index címkéi automatikusan indexelve lesznek, és a natív blob szolgáltatás lekérdezi őket. A metaadatokat nem lehet natív módon indexelni és lekérdezni, hacsak nem használ külön szolgáltatást, például [Azure Search](../../search/search-blob-ai-integration.md). A blob index címkéi további engedélyekkel rendelkeznek a mögöttes blob-adatoktól elkülönítve olvasásra/szűrésre és írásra is. A metaadatok ugyanazokat az engedélyeket használják, mint a blob, és a rendszer HTTP-fejlécként adja vissza a GetBlob vagy a GetBlobProperties műveletekben. A blob index címkéi a [Microsoft által felügyelt kulcs](../common/storage-service-encryption.md) használatával titkosítva vannak, míg a metaadatokat a rendszer a blob-adatokhoz megadott titkosítási kulcs használatával titkosítja nyugalmi állapotban. 

Az alábbi táblázat a metaadatok és a blob-index címkék közötti különbségeket foglalja össze:

|              |   Metaadatok   |   BLOB-index Címkék  |
|--------------|--------------|--------------------|
| **Korlátok**         | Nincs számszerű korlát; összesen 8 KB; kis-és nagybetűk megkülönböztetése | 10 címke/blob Max; 768 bájt/címke; kis-és nagybetűk |
| **Frissítések**      | Archiválási szinten nem engedélyezett; A SetBlobMetadata lecseréli az összes meglévő metaadatot; A SetBlobMetadata megváltoztatja a blob utolsó módosításának időpontját. | Minden hozzáférési szinten engedélyezett; A SetBlobTags lecseréli az összes meglévő címkét; A SetBlobTags nem módosítja a blob utolsó módosításának időpontját. |
| **Storage**        | A blob-adattal együtt tárolva |  Alárendelt erőforrás a blob-adatforráshoz | 
| **Indexelés & lekérdezés** | N/A natív módon; külön szolgáltatást kell használnia, például Azure Search | Igen, a blob Storage-ba beépített natív indexelési és lekérdezési képességek |
| **Titkosítás** | Titkosítva, a blob-adatokhoz használt titkosítási kulccsal |  Microsoft által felügyelt titkosítási kulccsal inaktív állapotban titkosított |
| **Díjszabás**   | A metaadatok mérete a blob tárolási költségei közé tartozik. |    Fix Cost index címkével | 
| **Fejléc válasza** | A GetBlob és a GetBlobProperties fejlécként visszaadott metaadatok | TagCount visszaadott GetBlob vagy GetBlobProperties; Csak a GetBlobTags és a ListBlobs által visszaadott Címkék |
| **Engedélyek**  |    A blob-adatokra vonatkozó olvasási vagy írási engedélyek kiterjeszthetők a metaadatokra |    A címkék olvasásához/szűréséhez vagy írásához további engedélyek szükségesek |

## <a name="pricing"></a>Díjszabás
A blob index díjszabása jelenleg nyilvános előzetes verzióban érhető el, és az általános elérhetőségre változhat. Az ügyfelek a Storage-fiókban lévő blob-indexek teljes száma után számítanak fel díjat a hónap átlaga alapján. Az indexelési motornak nem kell fizetnie. A SetBlobTags, GetBlobTags és FindBlobsByTags vonatkozó kéréseket a megfelelő működési típusok alapján számítjuk fel. [További információért lásd a blob díjszabásának blokkolása](https://azure.microsoft.com/pricing/details/storage/blobs/)című témakört.

## <a name="regional-availability-and-storage-account-support"></a>Regionális rendelkezésre állási és Storage-fiókok támogatása

A blob index jelenleg csak általános célú v2 (GPv2) fiókokban érhető el, amelyeken a hierarchikus névtér (HNS) le van tiltva. A általános célú-(GPV1-) fiókok nem támogatottak, de bármely GPv1-fiókot GPv2-fiókra frissíthet. A Storage-fiókokkal kapcsolatos további információkért lásd: az [Azure Storage-fiók áttekintése](../common/storage-account-overview.md).

Nyilvános előzetes verzióban a blob index jelenleg csak a következő régiókban érhető el:
- Közép-Kanada
- Kelet-Kanada
- Közép-Franciaország
- Dél-Franciaország

Első lépésként tekintse meg a [blob index felhasználása az adatkezeléshez és az adatkereséshez](storage-blob-index-how-to.md)című témakört.

> [!IMPORTANT]
> Tekintse meg a jelen cikk feltételek című szakaszát. Az előzetes verzióra való regisztráláshoz tekintse meg a jelen cikk előfizetés regisztrálása című szakaszát. Regisztrálnia kell az előfizetését, mielőtt a blob-indexet használni tudja a Storage-fiókokban.

### <a name="register-your-subscription-preview"></a>Előfizetés regisztrálása (előzetes verzió)
Mivel a blob-index csak nyilvános előzetes verzióban érhető el, regisztrálnia kell az előfizetést, mielőtt használni tudja a funkciót. Kérelem elküldéséhez futtassa a következő PowerShell-vagy CLI-parancsokat.

#### <a name="register-by-using-powershell"></a>Regisztrálás a PowerShell használatával
```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Regisztrálás az Azure CLI használatával
```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues-preview"></a>Feltételek és ismert problémák (előzetes verzió)
Ez a szakasz a blob index aktuális nyilvános előzetes verziójának ismert problémáit és feltételeit ismerteti. A legtöbb előzetes verzióhoz hasonlóan ez a funkció nem használható éles számítási feladatokhoz, amíg a viselkedés megváltozhat.

-   Előzetes verzióként regisztrálnia kell az előfizetését, mielőtt a Storage-fiókhoz az előzetes verziójú régiókban lévő blob-indexet használhassa.
-   Előzetes verzióban jelenleg csak GPv2-fiókok támogatottak. A blob, a BlockBlobStorage és a HNS enabled DataLake Gen2-fiókok jelenleg nem támogatottak a blob indexben. A GPv1-fiókok nem támogatottak.
-   Az oldal-Blobok indexelési címkékkel való feltöltése jelenleg nem őrzi meg a címkéket. Az oldal blobjának feltöltése után be kell állítania a címkéket.
-   Ha a szűrés egyetlen tárolóra van korlátozva, a @container csak akkor adható át, ha a szűrő kifejezésben szereplő összes indexelő címke egyenlőségi ellenőrzés (kulcs = érték). 
-   A és a (z) és a (z) és a (z) és a (z) a 100 < (z) és a (z) és a (z) és a (z) és a (z) fel> tétellel rendelkező
-   A verziószámozás és a blob-index jelenleg nem támogatott. A blob-index címkéi megmaradnak a verzióknál, de jelenleg nem jutnak el a blob index motorhoz.
-   A fiók feladatátvétele jelenleg nem támogatott. Előfordulhat, hogy a blob-index nem frissül megfelelően a feladatátvétel után.
-   Az életciklus-kezelés jelenleg csak az esélyegyenlőségi ellenőrzéseket támogatja a blob-index egyeztetésével.
-   A CopyBlob nem másol blob-index címkéket a forrás blobból az új cél blobba. A másolási művelet során megadhatja a cél blobra alkalmazni kívánt címkéket. 
- A CopyBlob (aszinkron másolás) egy másik Storage-fiókból, a célként megadott blobon alkalmazott címkék használatával jelenleg a blob indexelő motorja nem adja vissza a blobot és annak címkéit a szűrő készletében. A CopyBlob URL-címről (szinkronizálási másolat) a közbenső időpontban ajánlott használni.
-   A címkék megmaradnak a pillanatképek létrehozásán; a pillanatképek előléptetése azonban jelenleg nem támogatott, és egy üres kódelemet eredményezhet.

## <a name="faq"></a>GYIK

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>A blob-index segíthet a saját blobokban lévő tartalmak szűrésében és lekérdezésében? 
Nem, a blob index címkéi segítenek megtalálni a keresett blobokat. Ha a blobokon belül kell keresnie, használja a lekérdezés gyorsítását vagy a Azure Search.

### <a name="are-there-any-special-considerations-regarding-blob-index-tag-values"></a>Vannak olyan különleges megfontolások, amelyek a blob-indexek címkéi értékeire vonatkoznak?
A blob index címkéi csak a karakterlánc-adattípusokat támogatják, a lekérdezés pedig az eredményeket a lexicographical rendeléssel adja vissza. A számok esetében ajánlott nulla pad a számot használni. A dátum-és időpontok esetében ajánlott ISO 8601-kompatibilis formátumban tárolni.

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>A blob-indexek címkéi és a hozzájuk kapcsolódó Azure Resource Manager Címkék?
Nem, Azure Resource Manager címkék segítenek megszervezni a vezérlési sík erőforrásait, például az előfizetéseket, az erőforráscsoportokat és a Storage-fiókokat. A blob index címkéi az objektumok kezelését és felderítését biztosítják az adatsík erőforrásain, például a Storage-fiókban található blobokon.

## <a name="next-steps"></a>További lépések

A blob index kihasználása például a [blob index használata az adatkezeléshez és az adatkereséshez](storage-blob-index-how-to.md)című témakörben olvasható.

Ismerje meg az [életciklus-kezelést](storage-lifecycle-management-concepts.md) , és állítson be egy szabályt a blob-index egyezésével.

