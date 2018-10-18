---
title: Használati API kapcsolatos gyakori kérdések |} A Microsoft Docs
description: Azure Stack-mérőszámok, összehasonlító Azure usage API-t, a használati idő és a jelzett idő, hibakódok listáját.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 362ee7e84229c421f69ff0db0c8cfb040b105c97
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394512"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Gyakori kérdések az Azure Stack-használati API

Ebben a cikkben megválaszolunk néhány – gyakori kérdések az Azure Stack-használati API.

## <a name="what-meter-ids-can-i-see"></a>Milyen mérőszám azonosítók felszámított díj?
A következő erőforrás-szolgáltatók a jelentett használat:

### <a name="network"></a>Network (Hálózat)
  
**Mérőszám azonosítója**: F271A8A388C44D93956A063E1D2FA80B  
**Fogyasztásmérő neve**: statikus IP-címek használata  
**Egység**: IP-címek  
**Megjegyzések**: használt száma az IP-címek. Ha napi részletességgel API használatának felhívja, mérőszám órák száma szorozva IP-címet adja vissza.  
  
**Mérőszám azonosítója**: 9E2739BA86744796B465F64674B822BA  
**Fogyasztásmérő neve**: dinamikus IP-címek használata  
**Egység**: IP-címek  
**Megjegyzések**: használt száma az IP-címek. Ha napi részletességgel API használatának felhívja, mérőszám órák száma szorozva IP-címet adja vissza.  
  
### <a name="storage"></a>Storage
  
**Mérőszám azonosítója**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Fogyasztásmérő neve**: TableCapacity  
**Egység**: GB\*óra  
**Megjegyzések**: teljes tábla által felhasznált kapacitás.  
  
**Mérőszám azonosítója**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Fogyasztásmérő neve**: PageBlobCapacity  
**Egység**: GB\*óra  
**Megjegyzések**: teljes lapblobok által felhasznált kapacitás.  
  
**Mérőszám azonosítója**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Fogyasztásmérő neve**: QueueCapacity  
**Egység**: GB\*óra  
**Megjegyzések**: teljes várólista által felhasznált kapacitás.  
  
**Mérőszám azonosítója**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Fogyasztásmérő neve**: BlockBlobCapacity  
**Egység**: GB\*óra  
**Megjegyzések**: teljes kapacitás használja fel a blokkblobok használatát támogatják.  
  
**Mérőszám azonosítója**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Fogyasztásmérő neve**: TableTransactions  
**Egység**: kérelmek száma a 10 000's  
**Megjegyzések**: tábla szolgáltatáskérések (in 10,000s).  
  
**Mérőszám azonosítója**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Fogyasztásmérő neve**: TableDataTransIn  
**Egység**: bejövő adatok GB-ban  
**Megjegyzések**: Table service beérkező adatok GB-ban.  
  
**Mérőszám azonosítója**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Fogyasztásmérő neve**: TableDataTransOut  
**Egység**: GB kimenő adatforgalom  
**Megjegyzések**: Table service kimenő adatforgalom GB-ban  
  
**Mérőszám azonosítója**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Fogyasztásmérő neve**: BlobTransactions  
**Egység**: 10, 000's kérelmek száma  
**Megjegyzések**: szolgáltatáskérések Blob (in 10,000s).  
  
**Mérőszám azonosítója**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Fogyasztásmérő neve**: BlobDataTransIn  
**Egység**: bejövő adatok GB-ban  
**Megjegyzések**: Blob service a beérkező adatok GB-ban.  
  
**Mérőszám azonosítója**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Fogyasztásmérő neve**: BlobDataTransOut  
**Egység**: GB kimenő adatforgalom  
**Megjegyzések**: Blob service a kimenő adatforgalom GB-ban.  
  
**Mérőszám azonosítója**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Fogyasztásmérő neve**: QueueTransactions  
**Egység**: 10, 000's kérelmek száma  
**Megjegyzések**: szolgáltatáskérések Queue (in 10,000s).  
  
**Mérőszám azonosítója**: E518E809-E369-4A45-9274-2017B29FFF25  
**Fogyasztásmérő neve**: QueueDataTransIn  
**Egység**: bejövő adatok GB-ban  
**Megjegyzések**: Queue szolgáltatás beérkező adatok GB-ban.  
  
**Mérőszám azonosítója**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Fogyasztásmérő neve**: QueueDataTransOut  
**Egység**: GB kimenő adatforgalom  
**Megjegyzések**: Queue szolgáltatás a kimenő adatforgalom GB-ban  

### <a name="compute"></a>Compute 
  
**Mérőszám azonosítója**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Fogyasztásmérő neve**: Alap VM-méret órák  
**Egység**: virtuális processzor-üzemóra  
**Megjegyzések**: a virtuális magok számának szorzata az óra, a virtuális gépen.  
  
**Mérőszám azonosítója**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Fogyasztásmérő neve**: Windows VM-méret órák  
**Egység**: virtuális processzor-üzemóra  
**Megjegyzések**: a virtuális magok számának szorzata óra, a virtuális gépen.  
  
**Mérőszám azonosítója**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Fogyasztásmérő neve**: virtuális gép mérete óra  
**Egység**: VM-órák  
**Megjegyzések**: kiinduló és a Windows virtuális gép rögzíti. Nem módosíthatja a magok.  
  
### <a name="managed-disks"></a>Felügyelt lemezek

**Mérőszám azonosítója**: 5d76e09f-4567-452a-94cc-7d1f097761f0   
**Fogyasztásmérő neve**: S4   
**Egység**: a lemezek száma\*óra   
**Megjegyzések**: standard szintű felügyelt lemez – 32 GB 

**Mérőszám azonosítója**: dc9fc6a9-0782-432a-b8dc-978130457494   
**Fogyasztásmérő neve**: S6   
**Egység**: a lemezek száma\*óra   
**Megjegyzések**: standard szintű felügyelt lemez – 64 GB-os 

**Mérőszám azonosítója**: e5572fce-9f58-49d7-840c-b168c0f01fff   
**Fogyasztásmérő neve**: S10   
**Egység**: a lemezek száma\*óra   
**Megjegyzések**: standard szintű felügyelt lemez – 128 GB-os 

**Mérőszám azonosítója**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**Fogyasztásmérő neve**: S15   
**Egység**: a lemezek száma\*óra   
**Megjegyzések**: standard szintű felügyelt lemez – 256 GB 

**Mérőszám azonosítója**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**Fogyasztásmérő neve**: S20   
**Egység**: a lemezek száma\*óra      
**Megjegyzések**: standard szintű felügyelt lemez – 512 GB 

**Mérőszám azonosítója**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**Fogyasztásmérő neve**: S30   
**Egység**: a lemezek száma\*óra   
**Megjegyzések**: standard szintű felügyelt lemez – 1024 GB 

**Mérőszám azonosítója**: d9aac1eb-a5d1-42f2-b617-9e3ea94fed88   
**Fogyasztásmérő neve**: S40   
**Egység**: a lemezek száma\*óra   
**Megjegyzések**: standard szintű felügyelt lemez – 2048 GB 

**Mérőszám azonosítója**: a54899dd-458e-4a40-9abd-f57cafd936a7   
**Fogyasztásmérő neve**: S50   
**Egység**: a lemezek száma\*óra   
**Megjegyzések**: standard szintű felügyelt lemez – 4096 GB 

**Mérőszám azonosítója**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**Fogyasztásmérő neve**: P4   
**Egység**: a lemezek száma\*óra   
**Megjegyzések**: prémium szintű felügyelt lemez – 32 GB 

**Mérőszám azonosítója**: 518b412b-1927-4f25-985f-4aea24e55c4f   
**Fogyasztásmérő neve**: P6   
**Egység**: a lemezek száma\*óra   
**Megjegyzések**: prémium szintű felügyelt lemez – 64 GB-os 

**Mérőszám azonosítója**: 5cfb1fed-0902-49e3-8217-9add946fd624   
**Fogyasztásmérő neve**: P10   
**Egység**: a lemezek száma\*óra   
**Megjegyzések**: prémium szintű felügyelt lemez – 128 GB-os  

**Mérőszám azonosítója**: 8de91c94-f740-4d9a-b665-bd5974fa08d4   
**Fogyasztásmérő neve**: P15  
**Egység**: a lemezek száma\*óra   
**Megjegyzések**: prémium szintű felügyelt lemez – 256 GB 

**Mérőszám azonosítója**: c7e7839c-293b-4761-ae4c-848eda91130b   
**Fogyasztásmérő neve**: P20   
**Egység**: a lemezek száma\*óra   
**Megjegyzések**: prémium szintű felügyelt lemez – 512 GB 

**Mérőszám azonosítója**: 9f502103-adf4-4488-b494-456c95d23a9f   
**Fogyasztásmérő neve**: P30   
**Egység**: a lemezek száma\*óra   
**Megjegyzések**: prémium szintű felügyelt lemez – 1024 GB 

**Mérőszám azonosítója**: 043757fc-049f-4e8b-8379-45bb203c36b1   
**Fogyasztásmérő neve**: P40   
**Egység**: a lemezek száma\*óra    
**Megjegyzések**: prémium szintű felügyelt lemez – 2048 GB 

**Mérőszám azonosítója**: c0342c6f-810b-4942-85d3-6eaa561b6570   
**Fogyasztásmérő neve**: P50   
**Egység**: a lemezek száma\*óra   
**Megjegyzések**: prémium szintű felügyelt lemez – 4096 GB 

**Mérőszám azonosítója**: 8a409390-1913-40ae-917b-08d0f16f3c38   
**Fogyasztásmérő neve**: ActualStandardDiskSize   
**Egység**: bájt\*óra      
**Megjegyzések**: A standard szintű felügyelt lemez lemezen tényleges méret  

**Mérőszám azonosítója**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**Fogyasztásmérő neve**: ActualPremiumDiskSize   
**Egység**: bájt\*óra      
**Megjegyzések**: A tényleges méret a lemez prémium szintű felügyelt lemez 

**Mérőszám azonosítója**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Fogyasztásmérő neve**: ActualStandardSnapshotSize   
**Egység**: bájt\*óra   
**Megjegyzések**: a standard szintű felügyelt pillanatképek a lemez a tényleges méret.  

**Mérőszám azonosítója**: 95b0c03f-8a82-4524-8961-ccfbf575f536   
**Fogyasztásmérő neve**: ActualPremiumSnapshotSize   
**Egység**: bájt\*óra   
**Megjegyzések**: A tényleges méret felügyelt prémium szintű lemez.   

### <a name="sql-rp"></a>SQL-RP
  
**Mérőszám azonosítója**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Fogyasztásmérő neve**: DatabaseSizeHourSqlMeter  
**Egység**: MB\*óra  
**Megjegyzések**: teljes adatbázis-kapacitás létrehozása. Napi részletességgel-használati API hívásakor, mérőszám MB, szorozva az órák számát adja vissza.  
  
### <a name="mysql-rp"></a>MySql-RP   
  
**Mérőszám azonosítója**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Fogyasztásmérő neve**: DatabaseSizeHourMySqlMeter  
**Egység**: MB\*óra  
**Megjegyzések**: teljes adatbázis-kapacitás létrehozása. Napi részletességgel-használati API hívásakor, mérőszám MB, szorozva az órák számát adja vissza.    
### <a name="key-vault"></a>Key Vault   
  
**Mérőszám azonosítója**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Fogyasztásmérő neve**: Key Vault-tranzakciókra  
**Egység**: kérelmek száma a 10 000's  
**Megjegyzések**: a Key Vault-adatsík által fogadott száma a REST API-kérelmek.  
  
**Mérőszám azonosítója**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Fogyasztásmérő neve**: speciális kulcsok tranzakciók  
**Egység**: 10 ezer tranzakció  
**Megjegyzések**: RSA/4 K, 3 K ECC-kulcsok tranzakciókat. (előzetes verzió).  
  
### <a name="app-service"></a>App Service   
  
**Mérőszám azonosítója**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Fogyasztásmérő neve**: App Service-ben  
**Egység**: virtuális processzor-üzemóra  
**Megjegyzések**: app Service-ben futtatásához használt virtuális magok számát. Megjegyzés: A Microsoft használatával Ez a mérőszám az App Service az Azure Stacken díja szerint számítjuk fel. Felhőszolgáltatók segítségével az App Service (alább) mérőszámok használat számítása a bérlők.  
  
**Mérőszám azonosítója**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Fogyasztásmérő neve**: Függvénykérést  
**Egység**: 10 kérést  
**Megjegyzések**: (/ 10 végrehajtás) kért végrehajtások száma. Végrehajtásnak számít minden alkalommal, amikor egy függvényt egy esemény bekövetkeztekor futtat, illetve van egy kötés által kiváltott.  
  
**Mérőszám azonosítója**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Fogyasztásmérő neve**: számítási Functions -  
**Egység**: GB-s  
**Megjegyzések**: gigabájtmásodpercben (GB/s) mért erőforrás-használat. **Erőforrás-használat megfigyelt** szorzata átlagos memória méretét GB-ban a ideje (MS), a függvény végrehajtásához szükséges. Függvény által felhasznált memória mérjük a végrehajtási időt a legközelebbi 128 MB, legfeljebb 1 536 MB maximális memória mérete legfeljebb kerekítési legfeljebb a legközelebbi 1 ms. A minimális végrehajtási idő és a egy adott függvény végrehajtása a memória, 100 ms és 128 mb jelölik.  
  
**Mérőszám azonosítója**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Fogyasztásmérő neve**: megosztott App Service óra  
**Egység**: 1 óra  
**Megjegyzések**: App Service-csomag szegmensek óra használat. Tarifacsomagok díjszabása.  
  
**Mérőszám azonosítója**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Fogyasztásmérő neve**: ingyenes App Service-óra  
**Egység**: 1 óra  
**Megjegyzések**: Per óra ingyenes App Service-csomag használatáért. Tarifacsomagok díjszabása.  
  
**Mérőszám azonosítója**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Fogyasztásmérő neve**: kis méretű standard szintű App Service óra  
**Egység**: 1 óra  
**Megjegyzések**: kiszámítása alapján méretét és a példányok számát.  
  
**Mérőszám azonosítója**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Fogyasztásmérő neve**: közepes méretű standard szintű App Service óra  
**Egység**: 1 óra  
**Megjegyzések**: kiszámítása alapján méretét és a példányok számát.  
  
**Mérőszám azonosítója**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Fogyasztásmérő neve**: nagy méretű standard szintű App Service óra  
**Egység**: 1 óra  
**Megjegyzések**: kiszámítása alapján méretét és a példányok számát.  
  
### <a name="custom-worker-tiers"></a>Egyéni feldolgozói rétegek   
  
**Mérőszám azonosítója**: *egyéni feldolgozói rétegek*  
**Fogyasztásmérő neve**: egyéni feldolgozói rétegek  
**Egység**: óra  
**Megjegyzések**: Determinisztikus mérőszám azonosítója Termékváltozat és egyéni feldolgozói réteg neve alapján jön létre. Ez a mérőszám azonosítója egy egyedülálló megoldás minden egyes egyéni feldolgozói réteg.  
  
**Mérőszám azonosítója**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Fogyasztásmérő neve**: SNI SSL  
**Egység**: egy SNI SSL-kötés  
**Megjegyzések**: App Service kétféle SSL-kapcsolatot támogat: kiszolgálónév jelzése (SNI) és az IP-cím alapú SSL kapcsolatot. Az SNI-alapú SSL a modern böngészőkben használható, az IP-alapú SSL mindegyikben.  
  
**Mérőszám azonosítója**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Fogyasztásmérő neve**: IP SSL  
**Egység**: / IP-cím alapú SSL-kötés  
**Megjegyzések**: App Service kétféle SSL-kapcsolatot támogat: kiszolgálónév jelzése (SNI) és az IP-cím alapú SSL kapcsolatot. Az SNI-alapú SSL a modern böngészőkben használható, az IP-alapú SSL mindegyikben.  
  
**Mérőszám azonosítója**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Fogyasztásmérő neve**: webes folyamat  
**Egység**:  
**Megjegyzések**: aktív webhelyenként óránként számítja ki.  
  
**Mérőszám azonosítója**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Fogyasztásmérő neve**: külső kimenő adatforgalmat  
**Egység**: GB  
**Megjegyzések**: adott válasz bájtjai + kimenő kérelmek teljes bájt + a teljes bejövő FTP kérelem adott válasz bájtjai + teljes bejövő webes üzembe helyezése a kérelemre adott válasz bájtjai bejövő kérelmek száma összesen.  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsdocsmicrosoftcomazurebillingbilling-usage-rate-card-overviewazure-resource-usage-api-preview-currently-in-public-preview"></a>Az API-k hasonlítsa össze az Azure Stack használati módja a [Azure-használati API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) (jelenleg nyilvános előzetes verzióban)?
* A bérlői használati API-t az Azure API, egy kivétellel összhangban: a *showDetails* jelző jelenleg nem támogatott az Azure Stackben.
* A szolgáltatói használati API-t csak azokra az Azure Stack.
* Jelenleg a [RateCard API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview) , amely elérhető az Azure-ban nem érhető el az Azure Stackben.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Mi a különbség a használati és jelentett idő között?
Használati jelentések két fő időértékek rendelkezik:

* **Jelentett idő**. Ha a használati esemény a használati rendszer megadott idő
* **Használati idő**. Az idő, amikor az Azure Stack erőforrás használatának

Használati és jelentett idő értékek eltérést észlelt egy adott használati esemény jelenhet meg. A késleltetés, amíg minden környezetben több órára is lehet.

Jelenleg, lekérdezheti, ha kizárólag a *jelentett idő*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Mit jelentenek a használati API hibakódok?
| **HTTP-állapotkód:** | **Hibakód:** | **Leírás** |
| --- | --- | --- |
| / 400 – Hibás kérelem |*NoApiVersion* |A *api-version* lekérdezési paraméter hiányzik. |
| / 400 – Hibás kérelem |*InvalidProperty* |Tulajdonság hiányzik vagy érvénytelen értékkel rendelkezik. Az üzenet a válasz törzsében hibakód azonosítja a hiányzó tulajdonság. |
| / 400 – Hibás kérelem |*RequestEndTimeIsInFuture* |Az érték *ReportedEndTime* a jövőben van. Értékek a későbbiekben nem engedélyezettek ehhez az argumentumhoz. |
| / 400 – Hibás kérelem |*SubscriberIdIsNotDirectTenant* |A szolgáltató API-hívás egy előfizetés-azonosító, amely nem egy érvényes bérlő a hívó használatban van. |
| / 400 – Hibás kérelem |*SubscriptionIdMissingInRequest* |A hívó az előfizetés-azonosító nincs megadva. |
| / 400 – Hibás kérelem |*InvalidAggregationGranularity* |A kért egy érvénytelen összesítési pontossággal. Érvényes értékek: naponta, és óránként. |
| 503 |*ServiceUnavailable* |Egy újrapróbálkozást lehetővé tevő hiba történt, mert a szolgáltatás foglalt, vagy a hívás szabályozás alatt áll. |

## <a name="next-steps"></a>További lépések
[A számlázás a vásárlók és a költséghelyi elszámolás az Azure Stackben](azure-stack-billing-and-chargeback.md)

[Szolgáltatói erőforrás-használati API](azure-stack-provider-resource-api.md)

[Bérlői erőforrás-használati API](azure-stack-tenant-resource-usage-api.md)
