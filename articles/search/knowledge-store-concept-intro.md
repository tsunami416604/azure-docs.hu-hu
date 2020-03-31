---
title: Bevezetés a tudástárolóba (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: A bővített dokumentumokat az Azure Storage-ba küldheti, ahol megtekintheti, átformálhatja és felhasználhatja a bővített dokumentumokat az Azure Cognitive Search szolgáltatásban és más alkalmazásokban. Ez a funkció nyilvános előzetes verzióban érhető el.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0ad780c04954c09ddfd432b3c7de3dc65f0841bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942995"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Bevezetés a tudástárolókba az Azure Cognitive Search szolgáltatásban

> [!IMPORTANT] 
> A tudástároló jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) A [REST API 2019-05-06-preview verziója](search-api-preview.md) előnézeti funkciókat biztosít. Jelenleg korlátozott a portál támogatása, és nincs .NET SDK-támogatás.

A tudástároló az Azure Cognitive Search olyan szolgáltatása, amely független elemzés vagy későbbi feldolgozás esetén megőrzi [az AI-dúsítási folyamat](cognitive-search-concept-intro.md) kimenetét. A *bővített dokumentum* egy folyamat kimenete, amely olyan tartalomból jön létre, amelyet AI-folyamatokkal kinyert, strukturált és elemzett. Egy szabványos AI-folyamatban a bővített dokumentumok átmenetiek, csak indexelés során használatosak, majd elvetik őket. A tudástárolóval a bővített dokumentumok megőrződnek. 

Ha a múltban kognitív képességeket használt, már tudja, hogy a *skillsets* áthelyezi a dokumentumot a dúsítások sorozatán keresztül. Az eredmény lehet egy keresési index, vagy (új ebben az előzetes verzióban) előrejelzések egy tudástárolóban. A két kimenet, a keresési index és a tudástároló ugyanannak a folyamatnak a termékei; ugyanabból a bemenetből származnak, de olyan kimenetet eredményeznek, amely strukturált, tárolt és nagyon eltérő módon használható.

Fizikailag a tudástároló az [Azure Storage,](https://docs.microsoft.com/azure/storage/common/storage-account-overview)vagy az Azure Table storage, az Azure Blob storage vagy mindkettő. Az Azure Storage-hoz csatlakoztatható bármely eszköz vagy folyamat felhasználhatja a tudástároló tartalmát.

![Tudástároló a folyamatábrán](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Tudástároló a folyamatábrán")

## <a name="benefits-of-knowledge-store"></a>A tudástároló előnyei

A tudástároló strukturálható, környezetet és tényleges tartalmat biztosít – strukturálatlan és félig strukturált adatfájlokból, például blobokból, elemzésen átesett képfájlokból vagy akár strukturált adatokból, új űrlapokká alakítva. Egy [lépésről lépésre forgatókönyv](knowledge-store-create-rest.md)ben láthatja első kézből, hogy egy sűrű JSON-dokumentum van felosztva alstruktúrák, új struktúrákká alakítva, és egyébként elérhetővé downstream folyamatok, például a gépi tanulás és az adatelemzési számítási feladatok.

Bár hasznos látni, hogy az AI-dúsítási folyamat mire képes, a tudástároló ban rejlő valódi potenciál az adatok átformálásának lehetősége. Előfordulhat, hogy egy alapvető skillset, majd iterálni rajta, hogy adjunk növekvő szintű struktúra, amely ezután kombinálni új struktúrák, fogyó eszközök más alkalmazásokban kívül az Azure Cognitive Search.

Számba vétele, az előnyök a tudás tárolására a következők:

+ A bővített dokumentumokat a keresésen [kívüli elemzési és jelentéskészítő eszközökben](#tools-and-apps) használhatja fel. A Power BI power queryvel lenyűgöző választás, de az Azure Storage-hoz csatlakozni képes bármely eszköz vagy alkalmazás lehívhat egy ön által létrehozott tudástárolóból.

+ Finomítsa az AI-indexelési folyamatot hibakeresés közben, és a skillset-definíciók. A tudástároló egy ai-indexelési folyamat egy skillset definíciójának termékét jeleníti meg. Ezeket az eredményeket használhatja egy jobb skillset megtervezéséhez, mert pontosan láthatja, hogy néznek ki a dúsítások. A [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) használatával az Azure Storage egy tudástároló tartalmát tekintheti meg.

+ Alakítsa az adatokat új űrlapokká. Az átalakításkodik skillsets, de a lényeg az, hogy a skillset most már ezt a képességet. Az Azure Cognitive Search [Shaper-készsége](cognitive-search-skill-shaper.md) ki lett bővítve a feladat befogadására. Az átformálás lehetővé teszi olyan vetület definiálását, amely igazodik az adatok tervezett felhasználásához, miközben megőrzi a kapcsolatokat.

> [!Note]
> Új a ai gazdagodás és a kognitív képességek? Az Azure Cognitive Search integrálja a Cognitive Services Vision és Language funkciók kivonat, és gazdagíthatja a forrásadatok optikai karakterfelismerés (OCR) képfájlok, entitásfelismerés és a kulcskifejezések kinyerése szöveges fájlokból, és így tovább. További információ: [AI dúsítása az Azure Cognitive Search.](cognitive-search-concept-intro.md)

## <a name="physical-storage"></a>Fizikai tárolás

A tudástároló fizikai kifejeződése `projections` a Skillset `knowledgeStore` definíciójának elemén keresztül van kifejezve. A vetület úgy határozza meg a kimenet szerkezetét, hogy az megegyezik a tervezett felhasználással.

A kivetítések táblázatként, objektumként vagy fájlként tagolhatók.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

Az ebben a struktúrában megadott vetülettípus határozza meg a tudástároló által használt tároló típusát.

+ A táblatároló a `tables`definiáláskor használatos. Táblázatvetés definiálása, ha táblázatos jelentési struktúrákra van szüksége az analitikus eszközökbemenetekhez, vagy adatkeretekként más adattárakba exportálhat. A bővített `tables` dokumentumok részhalmazának vagy keresztmetszetének leéséhez több is megadható. Ugyanazon a vetítési csoporton belül a táblakapcsolatok megmaradnak, így mindegyikkel dolgozhat.

+ A blobtároló a `objects` vagy `files`a definiálásakor használatos. A fizikai ábrázolása `object` egy hierarchikus JSON-struktúra, amely egy bővített dokumentumot képvisel. A `file` egy dokumentumból kinyert, a Blob storage-ba átvitt kép.

Egyetlen vetítési `tables`objektum egy `objects`, `files`, , és számos esetben elegendő lehet egy vetítés létrehozása. 

Azonban lehetőség van több vetületkészlet `table` - `object` - `file` létrehozására, és ezt megteheti, ha különböző adatkapcsolatokat szeretne. Egy készleten belül az adatok kapcsolódnak, feltéve, hogy ezek a kapcsolatok léteznek, és észlelhetők. Ha további készleteket hoz létre, az egyes csoportokban lévő dokumentumok soha nem kapcsolódnak egymáshoz. Több vetítési csoport használatára is példa lehet, ha ugyanazokat az adatokat szeretné előre jeltetni az online rendszerrel való használatra, és egy adott módon kell ábrázolni, akkor ugyanazokat az adatokat is ki szeretné jeleníteni egy olyan adatelemzési folyamatban, amely megjelenik Másképp.

## <a name="requirements"></a>Követelmények 

[Az Azure Storage](https://docs.microsoft.com/azure/storage/) szükséges. Ez biztosítja a fizikai tárolás. Használhatja a Blob storage, Table storage vagy mindkettő. Blob storage érintetlen bővített dokumentumok, általában akkor, amikor a kimenet megy az alsóbb rétegbeli folyamatok. A táblázattárolás a bővített dokumentumok szeleteihez használható, amelyeket gyakran használnak elemzésre és jelentéskészítésre.

[Skillset](cognitive-search-working-with-skillsets.md) van szükség. Tartalmazza a `knowledgeStore` definíciót, és meghatározza a bővített dokumentum szerkezetét és összetételét. Nem hozhat létre tudástárolót üres skillset használatával. Legalább egy képességgel kell rendelkeznie egy skillsetben.

[Indexelő](search-indexer-overview.md) szükséges. A skillset egy indexelő által meghívott, amely a végrehajtást. Indexelők jönnek a saját követelmények és attribútumok. E tulajdonságok közül több közvetlen hatással van a tudástárolóra:

+ Az indexelők [nek egy támogatott Azure-adatforrásra](search-indexer-overview.md#supported-data-sources) van szükségük (a tudástárolót végső soron létrehozó folyamat az Azure-beli támogatott forrásból származó adatok lekérése). 

+ Az indexelők keresési indexet igényelnek. Az indexelő megköveteli, hogy indexsémát adjon meg, még akkor is, ha soha nem tervezi használni. A minimális index egy karakterláncmezővel rendelkezik, amelyet kulcsként jelölnek meg.

+ Az indexelők opcionális mezőleképezéseket biztosítanak, amelyek a forrásmezők célmezőhöz való aliasaként szolgálnak. Ha az alapértelmezett mezőleképezést módosítani kell (más nevet vagy típust kell használni), létrehozhat egy [mezőleképezést](search-indexer-field-mappings.md) az indexelőn belül. A tudástároló kimenete esetén a cél lehet egy mező egy blob objektumban vagy táblában.

+ Indexelők ütemezések és egyéb tulajdonságok, például a különböző adatforrások által biztosított változásészlelési mechanizmusok, is alkalmazható a tudástárolóban. A tartalom frissítéséhez például rendszeres időközönként [ütemezheti](search-howto-schedule-indexers.md) a dúsítást. 

## <a name="how-to-create-a-knowledge-store"></a>Hogyan hozzunk létre egy tudás tárolására

Tudástár létrehozásához használja a portált vagy`api-version=2019-05-06-Preview`az előzetes REST API - ).

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Az **Adatok importálása** varázsló tudástároló létrehozásának lehetőségeit tartalmazza. A kezdeti feltáráshoz [hozza létre az első tudástárolót négy lépésben.](knowledge-store-connect-power-bi.md)

1. Jelöljön ki egy támogatott adatforrást.

1. Adja meg a dúsítást: erőforrás csatolása, képzettségek kiválasztása és tudástár megadása. 

1. Indexséma létrehozása. A varázsló megköveteli, és következtetni az egyik az Ön számára.

1. Futtassa a varázslót. A kibontás, a dúsítás és a tárolás ebben az utolsó lépésben történik.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>A Szakértelem-készlet létrehozása és az előzetes REST API használata

Az `knowledgeStore` a egy [skillset-en](cognitive-search-working-with-skillsets.md)belül van meghatározva , amelyet viszont egy [indexelő](search-indexer-overview.md)hív meg . A bővítés során az Azure Cognitive Search létrehoz egy helyet az Azure Storage-fiókban, és a bővített dokumentumokat blobként vagy táblákba vetíti ki, a konfigurációtól függően.

Jelenleg az előzetes REST API az egyetlen mechanizmus, amellyel programozott módon hozhat létre tudástárolót. Egy egyszerű módja annak, hogy tárja [fel, hogy hozza létre az első tudás tárolására segítségével Postman és a REST API.](knowledge-store-create-rest.md)

Az előzetes verziójú szolgáltatás referenciatartalma a cikk [API-referenciaszakaszában](#kstore-rest-api) található. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Hogyan lehet csatlakozni az eszközökhöz és az alkalmazásokhoz

Miután a dúsítások léteznek a tárolóban, az Azure Blobhoz vagy a Table storage-hoz csatlakozó eszközök vagy technológiák felhasználhatók a tartalom feltárására, elemzésére vagy felhasználására. A következő lista a kezdet:

+ [A Storage Explorer](knowledge-store-view-storage-explorer.md) a bővített dokumentumszerkezet és -tartalom megtekintéséhez. Tekintsük ezt a tudástároló tartalmának megtekintésére szolgáló alapeszköznek.

+ [Power BI](knowledge-store-connect-power-bi.md) jelentéskészítéshez és elemzéshez. 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) további manipuláció.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API-referencia

REST API-verzió `2019-05-06-Preview` tudástárolására további definíciók révén skillsets. A hivatkozás mellett lásd: [Tudástár létrehozása postán az](knowledge-store-create-rest.md) API-k hívásának részleteiért.

+ [Skillset létrehozása (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [Skillset frissítése (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>További lépések

A tudástároló a bővített dokumentumok megőrzését kínálja, hasznos egy skillset tervezésekor, vagy új struktúrák és tartalmak létrehozása az Azure Storage-fiók elérésére képes ügyfélalkalmazások számára.

A bővített dokumentumok létrehozásának legegyszerűbb módja [a portálon keresztül](knowledge-store-create-portal.md)történik, de használhatja a Postman és a REST API-t is, ami hasznosabb, ha betekintést szeretne kapni az objektumok létrehozásának és hivatkozásának módjába.

> [!div class="nextstepaction"]
> [Tudástár létrehozása postás és rest használatával](knowledge-store-create-rest.md)

Ha többet szeretne megtudni az előrejelzésekről, a képességekről és arról, hogyan [definiálja őket egy skillsetben](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Előrejelzések egy tudástárban](knowledge-store-projection-overview.md)

A speciális vetítések fogalmait lefedő oktatóanyag, mint a szeletelés, a szövegközi formázás és a kapcsolatok, kezdje [a tudástárolóban lévő kivetítések meghatározásával](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Vetületek definiálása tudástárolóban](knowledge-store-projections-examples.md)