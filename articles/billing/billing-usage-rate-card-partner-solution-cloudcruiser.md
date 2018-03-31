---
title: A felhő Cruiser és számlázási API-integráció a Microsoft Azure |} Microsoft Docs
description: Egy egyedi terv biztosít a Microsoft Azure számlázási partner felhő Cruiser, az Azure számlázási API integrálása a termék tapasztalataikat.  Ez különösen fontos az Azure és a felhő Cruiser ügyfelek, amelyek segítségével/próbált felhő Cruiser a Microsoft Azure Pack érdekelt.
services: ''
documentationcenter: ''
author: tonguyen
manager: tonguyen
editor: ''
tags: billing
ms.assetid: b65128cf-5d4d-4cbd-b81e-d3dceab44271
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/09/2017
ms.author: mobandyo;sirishap;bryanla
ms.openlocfilehash: 8ddb81078e8019284c0481d4ea8d72253d3f0a5a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Felhő Cruiser és a Microsoft Azure számlázási API-integráció
Ez a cikk ismerteti, hogyan az új Microsoft Azure számlázási API-k összegyűjtött adatok használható felhőalapú Cruiser munkafolyamat költség szimuláció és elemzésére.

## <a name="azure-ratecard-api"></a>Az Azure RateCard API
A RateCard API arány információkat nyújt az Azure-ból. A megfelelő hitelesítő adatokkal rendelkező hitelesítés után kérdezhetők le az API-t Azure, az elérhető szolgáltatások metaadatainak gyűjti az együtt a díjszabás társított a kínálnak azonosítóját.

A következő mintát választ tartozik a megjelenítő az árak a A0 az API (Windows) példány:

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

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>A felhőalapú Azure RateCard API felületet Cruiser meg
Felhő Cruiser különböző módokon használhatja a RateCard API információkat. Ebben a cikkben megmutatjuk, hogyan használható IaaS annak szimuláció és elemzési munkaterhelés.

A használati eset bemutatásához, képzelhető el, a munkaterhelés több példánya fut a Microsoft Azure Pack (WAP). A cél, hogy szimulálása a ugyanaz az alkalmazás az Azure-on, és ilyen áttelepítési ezzel járó költségek becslése. A szimuláció létrehozásához a következők két fő feladatok végrehajtását:

1. **Importálás és a folyamat a szolgáltatás adatokat gyűjtött az RateCard API-t.** Ez a feladat a munkafüzetek, ahol a RateCard API kivonatát át legyenek-e, és közzéteszi az új arány terv is történik. Az új csomag arány becsléséhez a Azure árak a szimulációja használják.
2. **WAP szolgáltatások és az Azure-szolgáltatásokat a IaaS optimalizálására.** Alapértelmezés szerint WAP szolgáltatások alapján vannak az egyes erőforrások (Processzor, memória mérete, lemez méretét, stb.) amíg az Azure szolgáltatások alapján példányméretének (A0 A1, a2 méretű, stb.). Ez a feladat első is felhő Cruiser ETL-motor hajtja végre, nevű munkafüzetekhez, ha ezeket az erőforrásokat is telepíthet a példány mérete, Azure-példányokon szolgáltatások hasonló.

### <a name="import-data-from-the-ratecard-api"></a>Adatokat importálhat a RateCard API
Felhő Cruiser munkafüzetek automatikus módszere a gyűjti és feldolgozza a RateCard API adatait adja meg.  ETL (extract-átalakítási-betöltés) munkafüzetek lehetővé teszik a gyűjteményhez, átalakítás és a felhő Cruiser adatbázis közzétételét az adatok konfigurálását.

Minden egyes munkafüzet rendelkezhet egy vagy több gyűjteményt, így kiegészíteni vagy kiegészítheti a használati adatokat különböző forrásokból származó információk összefüggéseket. A következő két képernyőképeket bemutatják, hogyan hozzon létre egy új *gyűjtemény* egy meglévő munkafüzet és az adatok importálásához a *gyűjtemény* a RateCard API:

![1. ábra – egy új gyűjtemény létrehozása][1]

![2. ábra - adatok importálása az új gyűjteményből][2]

Az adatok importálása a munkafüzetbe, után több lépések és átalakítási folyamatok, módosításához és az adatok létrehozásához. Ebben a példában csak dolgozunk infrastruktúra,--szolgáltatás szükségtelen sorok eltávolítása a átalakítása lépéseket is használjuk (IaaS) vagy a rekordok iránt érdeklődik, mivel kapcsolatos szolgáltatások IaaS eltérő.

Az alábbi képernyőfelvételen látható RateCard API gyűjtött adatok feldolgozásához használt átalakítása lépéseket:

![3. ábra - átalakítási lépéseket RateCard API összegyűjtött adatok feldolgozása][3]

### <a name="defining-new-services-and-rate-plans"></a>Tervezi az új szolgáltatásokat és sebesség meghatározása
Szolgáltatások meghatározását a felhő Cruiser különböző módja van. A beállítások egyike a szolgáltatások importálása a használati adatok. Ezt a módszert gyakran használják az nyilvános felhők, ahol a szolgáltató már definiálva vannak a szolgáltatások használatakor.

A sebesség megtervezése olyan díjszabás vagy árak, amelyek különböző szolgáltatásokat, az érvényességi dátumait jeleníti vagy ügyfélcsoportot, többek között alapján lehet alkalmazni. Sebesség terveket is használhatja felhő Cruiser szimuláció vagy "Mi, ha" forgatókönyvek, a szolgáltatások változásainak is hatása a felhasználókra a teljes költség, a munkaterhelés létrehozásához.

Ebben a példában használjuk a szolgáltatás-adatokat a RateCard API az új szolgáltatások felhő Cruiser ad meg. Ugyanúgy használjuk létrehozására egy új arány tervezze meg a felhő Cruiser a díjszabás társított szolgáltatások.

Az átalakítási folyamat végén is hozzon létre egy új lépés, és az adatok a RateCard API az új szolgáltatásokat és a díjszabás is közzé lehet.

![4. ábra – a RateCard API adatok közzététele új szolgáltatásokat és díjszabás][4]

### <a name="verify-azure-services-and-rates"></a>Ellenőrizze az Azure-szolgáltatások és díjszabás
Miután közzétette a szolgáltatások és sebességét, ellenőrizheti a felhő Cruiser importált szolgáltatások listájában *szolgáltatások* lapon:

![5. ábra – a új szolgáltatások ellenőrzése][5]

Az a *arány tervek* lapon, a "AzureSimulation" együtt a díjszabás importálására a RateCard API hívása új arány terv ellenőrizheti.

![6. ábra - az új arány megtervezése és a kapcsolódó díjakat ellenőrzése][6]

### <a name="normalize-wap-and-azure-services"></a>Szabványosíthatja WAP és az Azure-szolgáltatások
Alapértelmezés szerint a WAP biztosítja a használati adatok alapján a számítási, memória és hálózati erőforrások használatát. Felhő Cruiser segítségével megadhatja a alapú szolgáltatások közvetlenül a foglalási vagy mért használatát ezeket az erőforrásokat. Például egy alapszintű sebesség beállítása minden órában CPU-használat, vagy a példány számára lefoglalt GB díjat számítanak.

Ehhez a példához összehasonlításához között WAP és az Azure, igazolnia kell az erőforrás-használata Azure-szolgáltatásokhoz való majd képezhetők kötegbe WAP összesíteni. Ez a transzformáció könnyen megvalósítható a munkafüzeteket:

![7. ábra - szolgáltatások optimalizálására WAP adatok átalakítása][7]

Az utolsó lépés: a munkafüzetet, hogy az adatok közzététele a felhőalapú Cruiser adatbázisba. Ezzel a lépéssel a használati adatok most már kötegelt (amelyek az Azure-szolgáltatások) szolgáltatás, és a költségek létrehozásához alapértelmezett díjszabás kötve.

A munkafüzet végén, az adatok feldolgozása a Feladatütemező ad hozzá egy feladatot, és megadhatja a gyakoriságát és időpontját a munkafüzetek futtatásához automatizálható.

![8. ábra - munkafüzet ütemezése][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Jelentések létrehozása munkaterhelés költség szimuláció elemzés
Miután a használati gyűjt, és díjak be vannak töltve a felhő Cruiser adatbázisba, azt a felhő Cruiser Insights modul hozhat létre a szimuláció, amely azt kívánják költség munkaterhelés.

Ahhoz, hogy ez a forgatókönyv bemutatásához, a következő jelentés létrehozott:

![Költség összehasonlítása][9]

A felső graph költség összehasonlítása szolgáltatások, a munkaterhelés minden egyes konkrét szolgáltatás fut a WAP (sötét kék) és az Azure (könnyű kék) közötti ára összehasonlítása jeleníti meg.

Az alsó grafikon azt ábrázolja, ugyanazokat az adatokat, de részleg által lebontva. Minden részleg futtatni WAP és az Azure, a munkaterhelés és a különbség a kettő között a költségek (zöld) a megtakarítások sáv jelenik meg.

## <a name="azure-usage-api"></a>Az Azure használati API
### <a name="introduction"></a>Bemutatás
A Microsoft nemrég vezette be az Azure használati API, lehetővé téve a használati adatokat betekintést nyerhet a fogyasztás programozott módon le tudja előfizetők. Felhő Cruiser ügyfelek kihasználhatja az API-n keresztül elérhető gazdagabb DataSet.

Felhő Cruiser használhatja az integráció többféle módon használati API-val. A granularitási (óránkénti használati adatai) és erőforrás-metaadatait az API-n keresztül elérhető rugalmas visszajelzés vagy jóváírási modell támogatásához szükséges dataset biztosít. 

Ebben az oktatóanyagban a azt jelent hogyan felhő Cruiser kihasználhassa az API használati adatok egy példát. Pontosabban azt fogja hozzon létre egy erőforráscsoportot az Azure-on, a fiók struktúra címkéket társíthat, majd húzza, és a felhő Cruiser címke adatait feldolgozási folyamatát írják le.

A végső cél, például az alábbi jelentéseket készíthet, és képes lesz elemezni a költségeket és a fogyasztás alapján a fiókot a címkék által kell tennie.

![10. ábra - címkék használatával bontása jelentés][10]

### <a name="microsoft-azure-tags"></a>A Microsoft Azure-címkék
Az Azure-használati API-n keresztül elérhető adatok nem csak fogyasztási adatai, hanem erőforrás metaadatok, többek között a vele társított címkéket tartalmazza. Címkék az erőforrások rendszerezéséhez, de ahhoz, hogy a hatékony könnyű megoldást biztosítson, biztosítania kell, amely:

* Címkék helyesen vonatkoznak az erőforrások kiépítése során
* Címkék megfelelően a szervezeti fiók struktúra használati összekötését használt visszajelzési/jóváírás folyamat.

Ezeket a követelményeket is lehet kihívást, különösen akkor, ha van a kiépítés vagy díjszabási ügyféloldali manuális folyamat. Helytelenül beírt, helytelen, vagy akár hiányzó címkék gyakran panaszkodnak a ügyfelek is, ha a címkékkel, és ezeket a hibákat is megnehezítik élettartama a díjszabási oldalon.

Az új Azure használati API-val felhő Cruiser is erőforrás nyújt információkat le, és egy kifinomult ETL eszköz nevű munkafüzetekhez, javítsa ki a közös címkézési hibákat. Reguláris kifejezések és a megfelelési adatok átalakítása, keresztül felhő Cruiser helytelenül címkézett erőforrások azonosíthatja, és alkalmazza a megfelelő címkéket, a megfelelő társítást a fogyasztó az erőforrások biztosítása.

A díjszabási oldalon felhő Cruiser a visszajelzési/jóváírás folyamat automatizálja, és a címke információ segítségével (részleg, osztály, projekt, stb.) a megfelelő fogyasztónak használati összekötését. Ezt az automatizálást hatalmas fokozása biztosít, és meggyőződni arról, hogy egy egységes és naplózható díjszabási folyamat.

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Erőforráscsoport létrehozása a Microsoft Azure címkékkel
Ez az oktatóanyag első lépése az, hogy hozzon létre egy erőforráscsoportot az Azure portálon, majd hozza létre az erőforrásokhoz való társítása új címkék. Ehhez a példához azt kell létrehozni a következő címkékkel: részleg, környezet tulajdonosa, projekt.

Az alábbi képernyőfelvételen látható egy minta erőforráscsoport a társított címkékkel.

![11. ábra - erőforráscsoportot az Azure-portál társított címkékkel][11]

A következő lépés, hogy a használati API átemelje az adatokat felhő Cruiser. A használati API jelenleg biztosít a válaszok JSON formátumban. Itt látható egy minta beolvasott adat:

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


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Adatokat importálhat a használati API felhő Cruiser
Felhő Cruiser munkafüzetek automatikus módszere a gyűjti és feldolgozza a használati API adatait adja meg. Az ETL (extract-átalakítási-betöltés) munkafüzetek lehetővé teszi a gyűjteményt, átalakítás és a felhő Cruiser adatbázis közzétételét az adatok konfigurálását.

Minden egyes munkafüzet rendelkezhet egy vagy több gyűjteményt. Ez lehetővé teszi, hogy kiegészíti vagy kiegészítheti a használati adatokat különböző forrásokból származó információk összefüggéseket. Az ebben a példában az Azure-sablon alapján munkafüzet létrehozhatunk egy új lapot (*UsageAPI)* , és egy új *gyűjtemény* információkat lehet importálni a használati API.

![3. ábra - használati API adatokat importálni a UsageAPI lap][12]

Figyelje meg, hogy ez a munkafüzet már rendelkezik-e más lapok szolgáltatások importálása az Azure-ból (*ImportServices*), és a számlázási API a felhasználási adatok feldolgozása (*PublishData*).

A következő adatokkal való feltöltéséhez használjuk a használati API a *UsageAPI* lap, és az információt a felhasználási adatokat a számlázási API összefüggéseket a a *PublishData* lap.

### <a name="processing-the-tag-information-from-the-usage-api"></a>A a használati API címkeadatok feldolgozása
Az adatok importálása a munkafüzetbe, után létrehozhatunk átalakítása lépéseit a *UsageAPI* lap sablonokat a feldolgozáshoz az API-t információival. Első lépés, hogy a "JSON vágási" processzor használja a címkék kinyerése egy mező, majd hozzon létre a mezők a őket (részleg, projekt, tulajdonosa és környezet).

![4. ábra – hozzon létre új mezőket a címke információ][13]

Figyelje meg a "Hálózat" szolgáltatás hiányzik a címkeadatok (sárga jelölését), de ellenőrizni tudja, hogy már része a ugyanabban az erőforráscsoportban megnézzük a *ResourceGroupName* mező. A címkék az egyéb erőforrások ezen erőforráscsoportból van, mivel azt ezen információk használatával a hiányzó címkékkel az ennek az erőforrásnak a folyamat későbbi részében.

A következő lépés az, hogy a címkék információival társítása keresési tábla létrehozása a *ResourceGroupName*. A keresési tábla a következő lépésben használt funkciógazdagabbá teheti az adatokkal együtt címkeinformációkat.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>A címke információk hozzáadása a fogyasztási adatokhoz
Most azt is ugorhat a *PublishData* lap, amely feldolgozza a számlázási API felhasználási adatait, majd adja meg a címkék kinyert mezőket. Ez a folyamat végzi el az előző lépésben létrehozott keresési tábla megnézi használatával a *ResourceGroupName* a keresések kulcsként.

![5. ábra – a fiók struktúra, a keresések adataival feltöltése][14]

Figyelje meg, hogy a megfelelő fiók struktúra mezőket a "Hálózat" szolgáltatás alkalmazása megtörtént, a probléma kijavítása a hiányzó címkékkel. Azt is megadni, a fiók struktúra mezők erőforrások "Egyéb", a célként megadott erőforráscsoport nem megkülönböztetéséhez őket a jelentésekre.

Most már csak ellenőriznünk kell a használati adatok közzététele lépés hozzáadása. Ezzel a lépéssel minden egyes definiálva az arány tervezze meg a szolgáltatás megfelelő díjszabás alkalmazható a használatra vonatkozó információ, és az eredményül kapott kell fizetni az adatbázisba betöltése.

A legjobb része, hogy csak akkor kell egyszer végrehajtania ezt a folyamatot. A munkafüzet befejezése után vegye fel a Feladatütemező egyszerűen, és óránként vagy naponta fusson a megadott időpontban. Akkor célszerű csak egy függetlenül attól, hogy új jelentések létrehozásakor, vagy ahhoz, hogy a jelentéssel bíró elemzések lekérése a felhő használati adatok elemzése testreszabása a már meglévőket.

### <a name="next-steps"></a>Következő lépések
* Felhő Cruiser munkafüzeteket és a jelentések létrehozásának részletes utasításokért lásd felhő Cruiser online [dokumentáció](http://docs.cloudcruiser.com/) (érvényes bejelentkezési adatait).  Felhő Cruiser kapcsolatos további információkért forduljon [ info@cloudcruiser.com ](mailto:info@cloudcruiser.com).
* Lásd: [betekintést nyerhet a Microsoft Azure erőforrás-felhasználás](billing-usage-rate-card-overview.md) az Azure erőforrás-használat és RateCard API-k áttekintését.
* Tekintse meg a [Azure számlázási REST API-referencia](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) mindkét API-k bővebben, az Azure Resource Manager által nyújtott API-kat készletét részét képező.
* Ha szeretné jobb alaposabban tanulmányozhatja a mintakódot, tekintse meg a Microsoft Azure számlázási API Kódminták a [Azure mintakódok](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>További tudnivalók
* Az Azure Resource Managerrel kapcsolatos további információkért lásd: a [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md) cikk.

<!--Image references-->

[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "1. ábra – egy új gyűjtemény létrehozása"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "2. ábra - adatok importálása az új gyűjteményből"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "3. ábra - átalakítási lépéseket RateCard API összegyűjtött adatok feldolgozása"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "4. ábra – a RateCard API adatok közzététele új szolgáltatásokat és díjszabás"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "5. ábra – a új szolgáltatások ellenőrzése"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "6. ábra - az új arány megtervezése és a kapcsolódó díjakat ellenőrzése"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "7. ábra - szolgáltatások optimalizálására WAP adatok átalakítása"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "8. ábra - munkafüzet ütemezése"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "9. ábra - mintajelentés a munkaterhelés költség összehasonlítás forgatókönyv esetében"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "10. ábra - címkék használatával bontása jelentés"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "11. ábra - erőforráscsoportot az Azure-portál társított címkékkel"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "12. ábra - használati API adatokat importálni a UsageAPI lap"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "13. ábra – hozzon létre új mezőket a címke információ"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "14. ábra – a fiók struktúra, a keresések adataival feltöltése"
