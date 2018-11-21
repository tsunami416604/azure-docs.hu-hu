---
title: A cloud Cruiser és a Microsoft Azure Billing API-integráció |} A Microsoft Docs
description: A Microsoft Azure Billing fiókpartner Cloud Cruiser, az az Azure számlázási API-k integrálása a terméket a személyre biztosít egyedülálló megközelítésével.  Ez különösen hasznos az Azure-ban és a Cloud Cruiser ügyfelek, amelyek segítségével/történt a Cloud Cruiser a Microsoft Azure Pack szeretne.
services: ''
documentationcenter: ''
author: tonguyen
manager: mumami
editor: ''
tags: billing
ms.assetid: b65128cf-5d4d-4cbd-b81e-d3dceab44271
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/09/2017
ms.author: erikre
ms.openlocfilehash: 79582e59d9ad9396acf29d6e35d640edcb20dca3
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275955"
---
# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>A cloud Cruiser és a Microsoft Azure Billing API-integráció
Ez a cikk ismerteti, hogy az új Microsoft Azure Billing API-kkal gyűjtött adatokat is a Cloud Cruiser a munkafolyamat költség szimulációt és elemzést.

## <a name="azure-ratecard-api"></a>Az Azure RateCard API
A RateCard API-t az Azure-ból arány információkat biztosít. Miután hitelesítése a megfelelő hitelesítő adatokkal, lekérdezheti, ha az API-t az elérhető szolgáltatásokkal kapcsolatos metaadatok gyűjtése az Azure-ban együtt a díjait az ajánlat azonosítója.

A következő mintát választ van az API-val megjelenítése az árak az a0 (Windows) példány:

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>A cloud Cruiser a felületet Azure RateCard API
Cloud Cruiser használhatja a RateCard API-adatokat különböző módon. Ebben a cikkben bemutatjuk, hogyan használható, hogy az IaaS számítási feladatok költsége szimulációt és elemzést.

Ezt a használati esetet bemutatása érdekében imagine több fut a Microsoft Azure Pack (WAP)-példányok számítási feladatok. A célja, hogy szimulálja a azonos számítási feladatok Azure-ban, és költségeinek ilyen migrálási állapotát. Ebben a szimulációban létrehozásához a következők két fő feladatot kell elvégezni:

1. **Importálás és a folyamat a szolgáltatás által gyűjtött információk a RateCard API-ból.** Ez a feladat a munkafüzetek, ahol a RateCard API kinyerése átalakíthatók és közzétett egy új díjkonstrukció is történik. Az új díjkonstrukció megbecsülni az Azure díjszabása a szimulációk használják.
2. **WAP-szolgáltatások és az Azure IaaS-szolgáltatások normalizálása.** Alapértelmezés szerint a WAP-szolgáltatások alapulnak az egyes erőforrásokra (CPU, memória mérete, lemez méretét, stb.) a amíg az Azure szolgáltatások alapján példány mérete (A0, A1, A2, stb.). Az első tevékenység Cloud Cruiser ETL motor által végrehajtott, nevű munkafüzetek, ahol ezeket az erőforrásokat is telepíthet a megfelel az Azure-beli példány szolgáltatások példányméretek is.

### <a name="import-data-from-the-ratecard-api"></a>Adatokat importálhat a RateCard API
Cloud Cruiser munkafüzetekkel automatikus módszere a gyűjtenek és dolgoznak fel adatokat a RateCard API.  ETL (kinyerési, átalakítási, betöltési) munkafüzetek lehetővé teszik a gyűjtemény, átalakítás és a Cloud Cruiser adatbázisba adatok közzététel konfigurálása.

Minden munkafüzet rendelkezhet egy vagy több gyűjteményt, kiegészíteni vagy kiegészítheti a használati adatok különböző forrásokból származó adatok korrelációját engedélyezi. A következő két képernyőfelvételek bemutatják, hogyan hozzon létre egy új *gyűjtemény* egy meglévő munkafüzetet, és az adatok importálása a *gyűjtemény* az RateCard API-val:

![1. ábra – egy új gyűjtemény létrehozása][1]

![2. ábra – adatok importálása az új gyűjteményből][2]

Az adatok importálása a munkafüzetbe, után hozhat létre több lépéseket és átalakítási folyamatokat, és az adatok módosításához. Ebben a példában azt csak olyan infrastruktúra--szolgáltatásként (IaaS) az Adatátalakítási lépéseket használatával távolítsa el a felesleges sort vagy rekordok érdeklik, mivel kapcsolatos szolgáltatásokhoz, amelyek IaaS nem.

Az alábbi képernyőfelvételen a RateCard API-ból gyűjtött adatok feldolgozásához használt Adatátalakítási lépéseket:

![3. ábra – Adatátalakítási lépéseket RateCard API-ból összegyűjtött adatok feldolgozása][3]

### <a name="defining-new-services-and-rate-plans"></a>Új szolgáltatások és a sebesség-csomagok
A Cloud Cruiser szolgáltatások definiálása különböző módja van. A lehetőségek egyike a szolgáltatások importálásához a használati adatok. Ezt a módszert gyakran használják, ha a nyilvános felhőkkel, ahol a szolgáltató által már definiálva vannak a szolgáltatások használata.

Díjszabás vagy árak, amelyek alkalmazhatók a különböző szolgáltatások, a hatékony dátumok vagy ügyfélcsoportot, többek között a sebesség megtervezése. Díjcsomagokról a Cloud Cruiser a is használható a szimuláció vagy "Mi lenne ha" forgatókönyvek megismeréséhez, hogyan a szolgáltatások változásai hatással lehet egy számítási feladat teljes költsége hozhat létre.

Ebben a példában a szolgáltatás adatokat használjuk a RateCard API-tól adjon meg új szolgáltatások a Cloud Cruiser. Egy új arány csomag létrehozása a Cloud Cruiser ugyanúgy, használhatjuk a szolgáltatások díjait.

Az átalakítási folyamat végén egy új lépés létrehozása és közzététele a a RateCard API-tól az adatok az új szolgáltatásokról és díjak szerint lehetőség.

![4. ábra – új szolgáltatások és a díjak a RateCard API-tól az adatok közzététele][4]

### <a name="verify-azure-services-and-rates"></a>Ellenőrizze az Azure-szolgáltatások és díjszabás
Miután közzétette a szolgáltatások és a díjszabás, ellenőrizheti a szolgáltatások listája az importált Cloud Cruiser *szolgáltatások* lapon:

![5. ábra – az új szolgáltatások ellenőrzése][5]

Az a *csomagok aránya* lapon, az úgynevezett "AzureSimulation" együtt a díjszabás a RateCard API importálása új díjkonstrukció ellenőrizheti.

![6. ábra – az új csomag arány és a kapcsolódó díjakat ellenőrzése][6]

### <a name="normalize-wap-and-azure-services"></a>WAP és az Azure-szolgáltatások normalizálása
Alapértelmezés szerint a WAP biztosítja a használati adatok alapján a számítási, memória és hálózati erőforrások használatát. A Cloud Cruiser, megadhatja az-alapú szolgáltatások közvetlenül a foglalási vagy a díjköteles használat ezen erőforrások közül. Ha például egy alapszintű arány beállításához a CPU-használat óránként, vagy a példányhoz lefoglalt memória GB díja szerint számítjuk fel.

Ebben a példában hasonlítsa össze az árakat WAP és az Azure között azt összesítenie kell az erőforrás-használat a WAP csomagjaiból, amely az Azure-szolgáltatások majd leképezhető be. Az átalakítás a munkafüzetek egyszerűen hajtható végre:

![7. ábra – szolgáltatások optimalizálására WAP adatok átalakítása][7]

Az utolsó lépés: a munkafüzetet, hogy az adatok közzététele a Cloud Cruiser adatbázisba. Ezzel a lépéssel a használati adatok van most kötegelve (amely az Azure-szolgáltatások leképezése) szolgáltatásba, és vannak kötve, az alapértelmezett érvényes díjak létrehozásához.

A munkafüzet a Befejezés után automatizálható az adatok feldolgozása az ütemező egy feladatot ad hozzá, és a gyakoriságát és időpontját a munkafüzet futtatásához megadásával.

![8. ábra – a munkafüzet ütemezése][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Jelentéseket hozhat létre a számítási költségek szimuláció elemzéshez
Miután a használati gyűjt, és a költségek töltődnek be a Cloud Cruiser adatbázisba, a Cloud Cruiser Insights modul használatával a számítási költségek szimuláció, amely szeretnénk létrehozni.

Annak érdekében, hogy ez a forgatókönyv bemutatása érdekében létrehoztuk a következő jelentés:

![A Cost összehasonlítása][9]

A felső diagramon költség összehasonlítása szolgáltatások, a munkaterhelés minden egyes adott szolgáltatás fut a WAP (sötétkék) és az Azure (világoskék) közötti ára összehasonlítása jeleníti meg.

Az alsó diagram megjeleníti a ugyanazokat az adatokat, de szervezeti egység szerinti bontásban. A költségeket a számítási feladatok futtatásához a WAP és az Azure a különbség a kettő együtt minden részleg számára a megtakarítások (zöld) sáv jelenik meg.

## <a name="azure-usage-api"></a>Azure-használati API
### <a name="introduction"></a>Bevezetés
A Microsoft nemrég bevezetett az Azure használati API, lehetővé téve az előfizetők használatával követésével fontos információkhoz juthat a fogyasztás használati adatok programozott módon kérhetők le. Cloud Cruiser ügyfelek kihasználhatják a gazdagabb adatkészlet az API-n keresztül érhető el.

Cloud Cruiser az integrációt a használati API többféle módon is használhat. A granularitási (óránkénti használati adatokat), és az API-n keresztül elérhető erőforrás metaadatait biztosít a rugalmas Költséghelyi visszacsatolást és modellek támogatásához szükséges adatkészlet. 

Ebben az oktatóanyagban azt egy példa a Cloud Cruiser milyen előnyökkel jár az API használati információt jelenthet. Pontosabban azt fogja hozzon létre egy erőforráscsoportot az Azure-ban, a fiók struktúra címkék hozzárendelése, majd folyamatát írják le, lekérése és feldolgozása, a Cloud Cruiser eszközcímke-információ.

A végső célja, hogy az alábbihoz hasonló jelentéseket hozhat létre, és képes lesz elemezni a költségek és a fiók struktúra, a címkék által a használatalapú kell.

![10. ábra – címkék lebontások jelentés][10]

### <a name="microsoft-azure-tags"></a>A címkék a Microsoft Azure
Az Azure-használati API-n keresztül elérhető adatok nemcsak felhasználási adatokat, de az erőforrás metaadatának többek között a hozzá társított címkéket is tartalmaz. Az erőforrások rendszerezéséhez, de annak érdekében, hogy érvényes egyszerű módot biztosítanak a címkéket, kell ahhoz, hogy:

* A címkék helyesen vonatkoznak az erőforrásokhoz való üzembe helyezés
* A címkék megfelelően szolgálnak a költséghelyi visszacsatolási és költséghelyi elszámolás folyamatot használatát a munkahelyi fiók struktúra kötése.

Ezek a követelmények mindkét kihívást jelenthet, különösen akkor, ha az üzembe helyezése vagy díjszabási oldalán manuális folyamat. Helytelenül beírt, helytelen vagy hiányzó még címkék olyan közös panaszok ügyfeleink, ha címkékkel és a hibák is megnehezítik életre díjszabási oldalán.

Az új Azure-használati API-val Cloud Cruiser is erőforrás-címkézési vonatkozó lekéréses, és a egy kifinomult ETL eszköz munkafüzetek nevű, a gyakori címkézési hibák kijavításához. Reguláris kifejezések és a megfelelési adatok átalakítása, keresztül Cloud Cruiser helytelenül címkézett erőforrások azonosításához, és alkalmazza a megfelelő címkéket, a helyes hozzárendelését a fogyasztó erőforrások biztosítása.

Díjszabási oldalán a Cloud Cruiser automatizálja a költséghelyi visszacsatolási és költséghelyi elszámolás folyamatát, és a eszközcímke-információ segítségével lehessen vonni a megfelelő végfelhasználói kereskedelem (részleg, részleg, projekt, stb.) használatát. Ezt az automatizálást hatalmas fokozása biztosít, és a egy egységes és naplózható díjszabási folyamat gondoskodhat.

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>A címkék a Microsoft Azure-erőforráscsoport létrehozása
Ez az oktatóanyag első lépéseként a hozzon létre egy erőforráscsoportot az Azure Portalon, majd hozzon létre új címkéket az erőforrásokhoz való társításához. Ebben a példában azt hoz létre a következő címkékkel: részleg, környezet tulajdonosa, projekt.

Az alábbi képernyőképen a társított címkékkel rendelkező erőforráscsoporthoz mintáját szemlélteti.

![11. ábra - erőforráscsoportot a társított címkéket az Azure Portalon][11]

A következő lépés, hogy a használat API átemelje az adatokat Cloud Cruiser. A használati API jelenleg megválaszolhatók JSON formátumban. Íme egy példa a beolvasott adatok:

    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Adatokat importálhat a használati API Cloud Cruiser
Cloud Cruiser munkafüzetekkel automatikus módszere a gyűjtenek és dolgoznak fel adatokat a használati API. Az ETL (kinyerési, átalakítási, betöltési) munkafüzetet lehetővé teszi a gyűjtemény, az átalakítás és az adatok közzétételét a Cloud Cruiser adatbázisba konfigurálását.

Minden munkafüzet rendelkezhet egy vagy több gyűjteményt. Ez lehetővé teszi kiegészíteni vagy kiegészítheti a használati adatok különböző forrásokból származó adatok korrelációját. Ebben a példában az Azure-sablon munkafüzet létrehozunk egy új lapra (*UsageAPI)* és állítsa be egy új *gyűjtemény* információk importálásához a Usage API-t.

![3. ábra – a UsageAPI táblázatba importált API használati adatok][12]

Figyelje meg, hogy ez a munkafüzet már rendelkezik-e más lapok szolgáltatások importálása az Azure-ból (*ImportServices*), és feldolgozza a használati adatokat a számlázási API-ból (*publishdata metódusban a következőnél*).

Ezután feltölti használjuk a Usage API-t a *UsageAPI* táblázatot, és vesse össze az adatokat a használati adatokat a számlázási API-a a *publishdata metódusban a következőnél* lap.

### <a name="processing-the-tag-information-from-the-usage-api"></a>Feldolgozás az eszközcímke-információ a használati API
Az adatok importálása a munkafüzetbe, után hozunk létre az Adatátalakítási lépéseket a *UsageAPI* lapra annak érdekében, hogy időben feldolgozni az információkat az API-ból. A címkék kinyerését egyetlen mezőt, majd hozza létre a mezők a velük (részleg, projekt, tulajdonos és környezet) használatával a "JSON split" processzor első lépéseként.

![4. ábra – hozzon létre új mezőket a eszközcímke-információ][13]

Figyelje meg, hogy a "Hálózat" szolgáltatás hiányzik a eszközcímke-információ (sárga keretet), de azt is ellenőrizze, hogy az azonos erőforráscsoportban megnézzük a *ResourceGroupName* mező. A címkéket, a többi erőforrást az erőforráscsoportból van, mivel használhatjuk ezeket az információkat címkéjét szeretné alkalmazni a hiányzó a folyamat későbbi részében ehhez az erőforráshoz.

A következő lépés az, hogy hozzon létre egy keresési táblázat társítása az információkat a címkéket a *ResourceGroupName*. A keresési táblázatban az eszközcímke-információ a használati adatokat feldúsítani a következő lépésben szolgál.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>A használati adatokat ad hozzá a eszközcímke-információ
Most azt is ugorhat a *publishdata metódusban a következőnél* lap, amely feldolgozza a használati adatokat a számlázási API-ból, és adja hozzá a címkék kinyert mezőket. Ez a folyamat végzi el az előző lépésben létrehozott keresési tábla használatával a *ResourceGroupName* kulcsa a kereséseket.

![5. ábra – a fiók struktúra, a keresések az adatok feltöltése][14]

Figyelje meg, hogy alkalmazta a megfelelő fiókot struktúra mezőket a "Hálózat" szolgáltatás, a probléma kijavítása a hiányzó címkékkel. Azt is feltölti a fiók struktúra mezőket az erőforrások eltérő a cél erőforráscsoport az "Egyéb", annak érdekében, hogy megkülönböztessük őket a jelentésekben.

Most már csak hozzá kell adnunk a egy lépéssel a használati adatok közzététele. Ezzel a lépéssel az egyes szolgáltatásokhoz, a sebesség-csomagban meghatározott blokkblobdíjak alkalmazható a használatra vonatkozó információ, és az eredményül kapott díját kell betölteni az adatbázisba.

A legjobb része, hogy csak kell lépnie a folyamat során egyszer. A munkafüzet befejezése után adja hozzá az ütemező egyszerűen, és óránként vagy naponta fusson az ütemezett időpontban. Akkor annyit új jelentések létrehozásakor vagy testreszabása a meglévőket, annak érdekében, hogy a sem értelmezhető elemzési beszerezni a felhőbeli használati adatok elemzése.

### <a name="next-steps"></a>További lépések
* A Cloud Cruiser munkafüzetek és a jelentések létrehozásával kapcsolatos részletes útmutatásért tekintse meg Cloud Cruiser online [dokumentáció](http://docs.cloudcruiser.com/) (érvényes bejelentkezés szükséges).  További információ a Cloud Cruiser Forduljon [ info@cloudcruiser.com ](mailto:info@cloudcruiser.com).
* Lásd: [betekintést nyerhet a Microsoft Azure erőforrás-használat](billing-usage-rate-card-overview.md) az Azure erőforrás-használat és RateCard API-k áttekintése.
* Tekintse meg a [Azure Billing – REST API-referencia](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) mindkét API-k további információ az Azure Resource Manager által nyújtott API-k készlete részét képező.
* Ha szeretné, hogy közvetlenül a mintakódot, tekintse meg a Microsoft Azure Billing API-Kódminták a [Azure-Kódminták](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>További információ
* Az Azure Resource Managerrel kapcsolatos további tudnivalókért lásd: a [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md) cikk.

<!--Image references-->

[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "1. ábra – egy új gyűjtemény létrehozása"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "2. ábra – adatok importálása az új gyűjteményből"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "3. ábra – Adatátalakítási lépéseket RateCard API-ból összegyűjtött adatok feldolgozása"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "4. ábra – új szolgáltatások és a díjak a RateCard API-tól az adatok közzététele"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "5. ábra – az új szolgáltatások ellenőrzése"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "6. ábra – az új csomag arány és a kapcsolódó díjakat ellenőrzése"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "7. ábra – szolgáltatások optimalizálására WAP adatok átalakítása"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "8. ábra – a munkafüzet ütemezése"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "9. ábra – a számítási költségek összehasonlító forgatókönyvhöz mintát tartalmazó jelentés"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "10. ábra – címkék lebontások jelentés"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "11. ábra - erőforráscsoportot a társított címkéket az Azure Portalon"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "12. ábra – a UsageAPI táblázatba importált API használati adatok"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "13. ábra – hozzon létre új mezőket a eszközcímke-információ"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "14. ábra – a fiók struktúra, a keresések az adatok feltöltése"
