---
title: Az Azure Storage-életciklus kezelése
description: Megtudhatja, hogyan hozhat létre életciklus szabályok átmenet elévülési adatok gyors elérésűről lassú elérésű és archív tárolási szint.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 11/04/2018
ms.author: yzheng
ms.subservice: common
ms.openlocfilehash: dd74cac3000f6a280d5b8faa858c2143d17a7e55
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247799"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Az Azure Blob storage (előzetes verzió) életciklusának kezelése

Adatkészletek egyedi életciklusokkal rendelkeznek. Korai szakaszában az életciklus férhetnek egyes adatok milyen gyakran. De szükségük van a hozzáférésre, az adatok életkorának csökken. Bizonyos adatok tétlen marad, a felhőben, és a ritkán elért tárolt. Míg egyéb adatkészletekhez aktívan beolvasása és módosítanak teljes az élettartam néhány adatot napok vagy hónappal a létrehozás után lejár. Az Azure Blob storage életciklus-felügyelet (előzetes verzió) egy gazdag, szabályalapú házirend GPv2- és Blob storage-fiókok kínál. A szabályzat segítségével az adatok a megfelelő hozzáférési szinten való áttérés, vagy az adatok életciklus végén lejár.

Az életciklus-kezelési szabályzat lehetővé teszi:

- Áttérés a blobokon át a célszintű (gyakori, ritka elérésű, az archív tárolási szint ritkáról gyakori elérésű vagy ritka elérésű, archív) tárolási szintek optimalizálható a teljesítmény és költség
- Azok életciklusának végén található blobok törlése
- A storage-fiók szintjén naponta egyszer futtatni szabályok definiálása
- Tárolók és blobok (használatával előtagok szűrők) egy részét a szabályok alkalmazása

Fontolja meg a forgatókönyv, ahol egy adatkészlet a gyakori hozzáférést kap a korai fázisában – az életciklus során azonban majd csak alkalmanként két hét után. Az első hónapon túli ritkán hozzáfért az adatkészlet. Ebben a forgatókönyvben a gyakori elérésű tárolási a legcélszerűbb kezdeti szakaszában. Ritkán használt adatok tárolási alkalmi hozzáférést igénylő leginkább megfelelő, és az archive storage a legjobb szint lehetőség az adatok életkorának után több mint egy hónapja. Tárolási rétegek adatok korát tekintetben fokozottabban tervezhet a legkevésbé költséges tárolási lehetőség az igényeinek. Az átállás eléréséhez életciklus felügyeletiházirend-szabályok elévülési adatok áthelyezése az alacsonyabb szinten érhető el.

## <a name="storage-account-support"></a>Storage-fiókok támogatása

Az életciklus-kezelési szabályzat érhető el mindkét General Purpose v2 (GPv2-) fiókok és a Blob storage-fiókok. Az Azure Portalon frissítheti meglévő általános célú (GPv1-) fiók egy egyszerű, egykattintásos folyamat keresztül GPv2-fiókra. További információ a tárfiókokról: [Az Azure-tárfiókok áttekintése](../common/storage-account-overview.md).  

## <a name="pricing"></a>Díjszabás 

Az életciklus felügyeleti funkció ingyenesen elérhető előzetes verzióban érhető el. A normál művelet költsége díjszabásának a [Blobok listázása](https://docs.microsoft.com/rest/api/storageservices/list-blobs) és [Blobszint beállítása](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API-hívások. Díjszabással kapcsolatos további információkért lásd: [Blokkbob-díjszabás](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="register-for-preview"></a>Regisztráljon az előzetes verzió 
Nyilvános előzetes verzióban érhető el a regisztrációhoz kell igényelnie a szolgáltatás az előfizetéshez regisztrálnia. Kérelmek általában két héten belül hagyja jóvá. A jóváhagyást követően az összes meglévő és új GPv2- vagy Blob storage-fiókok az alábbi régiókban a szolgáltatás tartalmazza: 2. nyugati RÉGIÓJA, USA nyugati középső RÉGIÓJA, USA 2. keleti régiójában és Nyugat-Európa. Előzetes verzió csak a blokkblob típusú támogatja. Csakúgy, mint a legtöbb előzetes verziók, ne használja ezt a szolgáltatást éles számítási feladatokra addig általánosan elérhető

Kérést szeretne beküldeni, a következő parancsokat PowerShell vagy parancssori felület.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A kérelem küldése:

```powershell
Register-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
A regisztrációs jóváhagyási állapot a következő paranccsal ellenőrizheti:
```powershell
Get-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
A jóváhagyási és a megfelelő regisztrációs kap a *regisztrált* állapotba, ha a korábbi kérések elküldését.

### <a name="azure-cli"></a>Azure CLI

A kérelem küldése: 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
A regisztrációs jóváhagyási állapot a következő paranccsal ellenőrizheti:
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
A jóváhagyási és a megfelelő regisztrációs kap a *regisztrált* állapotba, ha a korábbi kérések elküldését.


## <a name="add-or-remove-a-policy"></a>Adja hozzá, vagy távolítsa el a szabályzat 

Hozzáadása, szerkesztése vagy eltávolítani egy házirendet az Azure portal használatával [PowerShell](https://www.powershellgallery.com/packages/Az.Storage), [Azure CLI-vel](https://docs.microsoft.com/cli/azure/ext/storage-preview/storage/account/management-policy?view=azure-cli-latest#ext-storage-preview-az-storage-account-management-policy-create), [REST API-k](https://docs.microsoft.com/rest/api/storagerp/managementpolicies/createorupdate), vagy az ügyféleszközök elől az alábbi nyelveken: [.NET ](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby](https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki **összes erőforrás** , majd a storage-fiókjában.

3. Válassza ki **életciklus-felügyelet (előzetes verzió)** Blob Service megtekinteni vagy módosítani a szabályzat szerint csoportosítva.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

### <a name="azure-cli"></a>Azure CLI

```
az account set --subscription "[subscriptionName]”
az extension add --name storage-preview
az storage account management-policy show --resource-group [resourceGroupName] --account-name [accountName]
```

> [!NOTE]
Ha engedélyezi a tűzfalszabályok a tárfiók, lifecycle management kérelmek blokkolhatja. Ezek a kérelmek azáltal, hogy kivételek blokkolásának feloldásához. További információkért tekintse meg a kivételek szakaszában [tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policy"></a>Szabályzat

Életciklus-kezelési szabályzat szabályok a JSON-dokumentumok gyűjteménye:

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "rule1",
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```


Egy házirend két paraméter szükséges:

| Paraméter neve | Paraméter típusa | Megjegyzések |
|----------------|----------------|-------|
| version        | Egy karakterlánc kifejezve `x.x` | Az előzetes verzió verziószáma 0,5. |
| szabályok          | A szabály objektumokból álló tömb | Legalább egy szabályt az egyes szabályzatok szükséges. Az előzetes időszakban legfeljebb 4 szabályok szabályzatonként is megadhat. |

A házirend minden egyes szabály három paraméter szükséges:

| Paraméter neve | Paraméter típusa | Megjegyzések |
|----------------|----------------|-------|
| Name (Név)           | Karakterlánc | Egy szabály neve tetszőleges kombinációját alfanumerikus karaktereket tartalmazhat. Szabály neve a kis-és nagybetűket. Egy házirend egyedinek kell lennie. |
| type           | Enum érték | Előzetes verzió érvényes értéke `Lifecycle`. |
| definíció     | Egy objektum, amely meghatározza az életciklus-szabály | Minden egyes definíció épül fel egy szűrő és egy műveletet. |

## <a name="rules"></a>Szabályok

Minden egyes szabály definíciója tartalmaz egy szűrő és egy műveletet. A [set szűrése](#rule-filters) szabály műveletek a tárolóban lévő objektumok meghatározott készlete, vagy ha objektumok nevét. A [művelet set](#rule-actions) vonatkozik a réteg vagy törlési műveleteket az objektumok szűrt készlete.

### <a name="sample-rule"></a>Mintául szolgáló szabály
A következő példa szabály szűri a fiókot kizárólag a műveletek végrehajtásához `container1/foo`. Futtassa a következő műveleteket a létező belüli objektumok `container1` **és** kezdődik `foo`: 

- Réteg blob ritka elérésű szint utolsó módosítása után 30 nappal
- Réteg blob az archív szint utolsó módosítása után 90 napig
- Utolsó módosítás után 2,555 nap (hét évig) blob törlése
- Pillanatkép létrehozása után 90 napig törölni a blobpillanatképeket

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "ruleFoo", 
      "type": "Lifecycle", 
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}

```

### <a name="rule-filters"></a>A szabály szűrők

Szűrők Szabályműveletek a tárfiókban található blobok egy részhalmazára korlátozza. Ha egynél több szűrő van megadva, a logikai `AND` futtat az összes szűrőt.

Az előzetes időszakban érvényes szűrők a következők:

| Szűrő neve | Szűrő típusa | Megjegyzések | Megadása kötelező |
|-------------|-------------|-------|-------------|
| blobTypes   | Előre definiált enum értékek tömbje. | Az előzetes kiadás támogat `blockBlob`. | Igen |
| prefixMatch | Meg kell egyeznie az előtagok karakterláncok tömbje. Előtag-karakterláncra tárolónévnek kell kezdődnie. Például, ha az összes BLOB alapján egyeztetni kívánt "https://myaccount.blob.core.windows.net/container1/foo/..." a szabály a prefixMatch van `container1/foo`. | Ha nem ad meg prefixMatch, a szabályok érvényesek lesznek a fiókban lévő összes BLOB. | Nem |

### <a name="rule-actions"></a>A szabály műveletek

A végrehajtási feltétel teljesülése esetén a szűrt blobok műveletek lépnek.

Előzetes verzióban elérhető az életciklus-felügyelet támogatja a rétegezést, és a blobok törlése és a blob pillanatképeinek törlését. Adja meg legalább egy műveletet az egyes szabályokhoz blobokat vagy pillanatképeket.

| Műveletek        | Alap Blob                                   | Pillanatkép      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Támogatja a gyakori elérésű szint jelenleg a blobokhoz         | Nem támogatott |
| tierToArchive | Támogatja a gyors vagy lassú elérésű szint jelenleg a blobokhoz | Nem támogatott |
| delete        | Támogatott                                   | Támogatott     |

>[!NOTE] 
Ha egynél több művelet ugyanennek a blobnak a definiálja, életciklus-felügyelet a legkevésbé költséges művelet a blob vonatkozik. Ha például a művelet `delete` művelet olcsóbb `tierToArchive`. A művelet `tierToArchive` művelet olcsóbb `tierToCool`.

Előzetes verzióban elérhető a művelet-végrehajtási feltételek kora alapulnak. Alap blobok az utolsó módosítás időpontja segítségével nyomon követheti a életkor és pillanatképek használatát a pillanatkép-létrehozás ideje blob-kor nyomon követéséhez.

| A művelet végrehajtási állapot | A feltétel értéke | Leírás |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Egész érték, amely jelzi a kora napokban | Az alap blob műveletekhez érvényes feltétel |
| daysAfterCreationGreaterThan     | Egész érték, amely jelzi a kora napokban | Blob pillanatkép műveletek esetében érvényes feltétel | 

## <a name="examples"></a>Példák
Az alábbi példák bemutatják, hogyan lehet gyakori helyzetek meg életciklus-szabályzat előírásainak.

### <a name="move-aging-data-to-a-cooler-tier"></a>Egy írásként elévülési adatok áthelyezése

Ez a példa bemutatja, hogyan előtaggal van ellátva a blokkblobok használatát támogatják az átmenet `container1/foo` vagy `container2/bar`. A szabályzat értékre vált, a blobokat, amelyekre több mint 30 nap elérésű tárolási szintre való még nem lett módosítva, és a blobok, az archív szinten 90 nap során nem módosított:

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "agingRule", 
      "type": "Lifecycle", 
      "definition": 
        {
          "filters": {
            "blobTypes": [ "blockBlob" ],
            "prefixMatch": [ "container1/foo", "container2/bar" ]
          },
          "actions": {
            "baseBlob": {
              "tierToCool": { "daysAfterModificationGreaterThan": 30 },
              "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
            }
          }
        }      
    }
  ]
}
```

### <a name="archive-data-at-ingest"></a>Betöltés adatok archiválása 

Néhány adatot tétlen marad, a felhőben és a ritkán, ha követően tárolja. Archiválhatja ezeket az adatokat, azonnal beolvasás után. A következő életciklus-szabályzat van konfigurálva, a betöltés adatok archiválása. Ebben a példában átmenetek letiltása a tárfiók tárolóban lévő blobok `archivecontainer` azonnal, az archív tárolási szintről. Az azonnali átállás kapcsolódással ható blobok 0 nap után az utolsó módosítás időpontja:

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "archiveRule", 
      "type": "Lifecycle", 
      "definition": 
        {
          "filters": {
            "blobTypes": [ "blockBlob" ],
            "prefixMatch": [ "archivecontainer" ]
          },
          "actions": {
            "baseBlob": { 
                "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
            }
          }
        }      
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Hamarosan lejár kora alapján

Bizonyos adatok várhatóan napok vagy akár hónapokat, csökkentheti a költségeket vagy kormányzati követelményeinek létrehozása után lejár. Konfigurálhatja egy életciklus-kezelési szabályzat által adatok életkor alapján törlés adatok lejár. Az alábbi példa bemutatja egy szabályzatot, amely törli az összes blokkblobok (a nem megadott előtag) 365 napnál régebbi.

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "expirationRule", 
      "type": "Lifecycle", 
      "definition": 
        {
          "filters": {
            "blobTypes": [ "blockBlob" ]
          },
          "actions": {
            "baseBlob": {
              "delete": { "daysAfterModificationGreaterThan": 365 }
            }
          }
        }      
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>Korábbi pillanatképek törlése

Módosítva, és rendszeresen élettartama során elért adatok esetén pillanatképek gyakran használják az adatok régebbi verzióit nyomon követéséhez. Létrehozhat egy szabályzatot, amely törli a régi pillanatkép pillanatkép életkor alapján. A pillanatkép-kor kiértékelése a pillanatkép-létrehozás ideje határozza meg. Ezt a törlések letiltása a tárolóban lévő blob-pillanatfelvételek házirendszabályt `activedata` , amelyek a 90 napos vagy régebbi pillanatkép létrehozása után.

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "snapshotRule", 
      "type": "Lifecycle", 
      "definition": 
        {
          "filters": {
            "blobTypes": [ "blockBlob" ],
            "prefixMatch": [ "activedata" ]
          },
          "actions": {            
            "snapshot": {
              "delete": { "daysAfterCreationGreaterThan": 90 }
            }
          }
        }      
    }
  ]
}
```
## <a name="faq---i-created-a-new-policy-why-are-the-actions-not-run-immediately"></a>Gyakori kérdések – hoztam létre egy új házirendet, miért van a műveleteket nem azonnali futtatása? 

A platform naponta egyszer fut, az életciklus-szabályzat. Ha egyszer már megadta egy új szabályzatot, indításához és futtatásához bizonyos műveletek (például a rétegezést és törlése) esetében akár 24 óráig is eltarthat.  

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan adatok véletlen törlés utáni helyreállításhoz:

- [Helyreállítható Törlés az Azure Storage-BLOB ](../blobs/storage-blob-soft-delete.md)
