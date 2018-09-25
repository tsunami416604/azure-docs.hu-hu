---
title: Az Azure Storage-életciklus kezelése
description: Megtudhatja, hogyan hozhat létre életciklus szabályok átmenet againg adatokat a gyakori elérésű, ritka elérésű és az archív szint.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 04/30/2018
ms.author: yzheng
ms.component: common
ms.openlocfilehash: 25e6fba6ac8aa34c0c30fd61f5fe297b94720439
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983667"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Az Azure Blob Storage (előzetes verzió) életciklusának kezelése

Adatkészletek egyedi életciklusokkal rendelkeznek. Bizonyos adatok gyakran korai érhető el a életciklusának, de szükségük van a hozzáférésre, az adatok életkorának drasztikusan csökken. Bizonyos adatok tétlennek lennie a felhőben, és a ritkán elért tárolt. Míg egyéb adatkészletekhez aktívan beolvasása és módosítanak teljes az élettartam néhány adatot napok vagy hónapok létrehozása után lejár. Az Azure Blob Storage lifecycle management (előzetes verzió) egy olyan interaktív és szabályalapú szabályzatot, amely való áttérés a legjobb hozzáférési szintet az adatok és a életciklus végén található adatok lejárnak kínál.

Életciklus-kezelési szabályzat segítségével:

- Áttérés a célszintű (gyakori elérésű, archív, gyakori vagy ritka elérésű, archív) tárolási szinten a blobok optimalizálható a teljesítmény és költség
- Azok életciklusának végén található blobok törlése
- Definiáljon szabályokat a naponta egyszer hajtható végre, a storage-fiók szintjén (támogatja a GPv2- és Blob storage-fiókok)
- Tárolók és blobok (használatával előtagok szűrők) egy részét a szabályok alkalmazása

Fontolja meg egy adatkészletet, amelyek gyakran életciklusának korai szakaszában érhető el, csak szükség van két hét után időnként és ritkán hozzáfért a letelik az egy hónap és más alkalmazásokhoz. Ebben a forgatókönyvben a gyakori elérésű tárolási ajánlott korai szakaszában, ritkán használt adatok az alkalmi hozzáférést igénylő leginkább megfelelő, és az archive storage a lehetőség ajánlott réteg az adatok életkorának után több mint egy hónapja. Tárolási rétegek adatok korát tekintetben fokozottabban tervezhet a legkevésbé költséges tárolási lehetőség az igényeinek. Az átállás eléréséhez életciklus-kezelési házirendek elévülési adatok áthelyezése az alacsonyabb rétegek érhető el.

## <a name="storage-account-support"></a>Storage-fiókok támogatása

Életciklus-kezelési szabályzat érhető el mindkét General Purpose v2 (GPv2-) fiók és a Blob Storage-fiók. Átválthat a meglévő általános célú (GPv1-) fiók segítségével az Azure Portalon egyetlen kattintással jelentkezhetnek GPv2-fiókra. Storage-fiókokkal kapcsolatos további információkért lásd: [az Azure storage-fiók áttekintése](../common/storage-account-overview.md) további.  

## <a name="pricing"></a>Díjszabás 

Életciklus-kezelési funkció ingyenesen elérhető előzetes verzióban. A normál művelet költsége díjszabásának a [Blobok listázása](https://docs.microsoft.com/rest/api/storageservices/list-blobs) és [Blobszint beállítása](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API-hívások. Lásd: [Blokkbob-díjszabás](https://azure.microsoft.com/pricing/details/storage/blobs/) , a díjszabással kapcsolatos további tudnivalókért.

## <a name="register-for-preview"></a>Regisztráljon az előzetes verzió 
Nyilvános előzetes verzióban érhető el a regisztrációhoz kell igényelnie a szolgáltatás az előfizetéshez regisztrálnia. A kérelem jóváhagyása (néhány napban), bármely meglévő és új GPv2- vagy Blob Storage-fiók a 2. nyugati RÉGIÓJA, USA Középnyugati Régiójában és Nyugat-Európa után a szolgáltatás engedélyezve van. Az előzetes verzióban csak a blokkblobok használata támogatott. Csakúgy, mint a legtöbb előzetes verziók, ez a funkció nem használható éles számítási feladatokra addig általánosan elérhető

Kérést szeretne beküldeni, a következő parancsokat PowerShell vagy parancssori felület.

### <a name="powershell"></a>PowerShell

A kérelem küldése:

```powershell
Register-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
A regisztrációs jóváhagyási állapot a következő paranccsal ellenőrizheti:
```powershell
Get-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Ha a funkció engedélyezett, és megfelelően regisztrálva, meg kell kapnia a "Regisztrálva" állapot.

### <a name="azure-cli"></a>Azure CLI

A kérelem küldése: 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
A regisztrációs jóváhagyási állapot a következő paranccsal ellenőrizheti:
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
Ha a funkció engedélyezett, és megfelelően regisztrálva, meg kell kapnia a "Regisztrálva" állapot. 


## <a name="add-or-remove-policies"></a>Szabályok hozzáadása vagy eltávolítása 

Hozzáadása, szerkesztése vagy eltávolítani egy házirendet az Azure portal használatával [PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.3-preview), [REST API-k](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/createorupdatemanagementpolicies), vagy az ügyféleszközök elől az alábbi nyelveken: [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby](   https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A tárfiókjának eléréséhez válassza az Összes erőforrás lehetőséget, majd válassza ki a tárfiókját.

3. A beállítások panelen kattintson a **életciklus-felügyelet** alatta a Blob szolgáltatás megtekintéséhez és/vagy szabályzatainak módosításához.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

> [!NOTE]
Ha engedélyezi a tűzfalszabályok a tárfiók, lifecycle management kérelmek blokkolhatja. Tiltást kivételek biztosít. További információkért tekintse meg a kivételek részben egy [tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policies"></a>Házirendek

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


A házirendben a két paraméter szükség:

| Paraméter neve | Paraméter típusa | Megjegyzések |
|----------------|----------------|-------|
| verzió:        | Egy karakterlánc kifejezve `x.x` | Az előzetes verzió verziószáma 0,5 |
| szabályok          | A szabály objektumokból álló tömb | Legalább egy szabályt az egyes szabályzatok szükséges. Az előzetes időszakban legfeljebb 4 szabályok szabályzatonként is megadhat. |

A szabályok szükséges paraméterek a következők:

| Paraméter neve | Paraméter típusa | Megjegyzések |
|----------------|----------------|-------|
| Name (Név)           | Sztring | A szabály nevét tetszőleges kombinációját alfanumerikus karaktereket tartalmazhat. Szabály neve a kis-és nagybetűket. Egy házirend egyedinek kell lennie. |
| type           | Enum érték | Előzetes verzió érvényes értéke `Lifecycle` |
| definíció     | Egy objektum, amely meghatározza az életciklus-szabály | Minden egyes definíció szűrőkészletek és a egy művelet set épül fel. |

## <a name="rules"></a>Szabályok

Minden egyes szabály definíciója tartalmaz egy szűrő és egy műveletet. A következő példa szabály módosítja előtaggal rendelkező alapszintű blokkblobok esetében a réteg `container1/foo`. A házirendben ezek a szabályok vannak meghatározva:

- Réteg blob ritka elérésű tárolási utolsó módosítás után 30 napig
- Blob az archív tárolási szint utolsó módosítás után 90 nappal
- Utolsó módosítás után 2,555 napig (7 évben) blob törlése
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

Szűrők Szabályműveletek a tárfiókban található blobok egy részhalmazára korlátozza. Ha több szűrő van megadva, a logikai `AND` történik az összes szűrőt.

Az előzetes időszakban érvényes szűrők a következők:

| Szűrő neve | Szűrő típusa | Megjegyzések | Megadása kötelező |
|-------------|-------------|-------|-------------|
| blobTypes   | Előre definiált enum értékek tömbje. | Az előzetes kiadás csak `blockBlob` használata támogatott. | Igen |
| prefixMatch | Meg kell egyeznie az előtagok karakterláncok tömbje. Előtag-karakterláncra tárolónévnek kell kezdődnie. Például, ha a blobok, az összes "https://myaccount.blob.core.windows.net/mycontainer/mydir/..." megfelelő egy szabályhoz, az előtag, az "mycontainer/könyvtárnév". | Ha ez nincs beállítva, ez a szabály vonatkozik a fiókban lévő összes BLOB. | Nem |

### <a name="rule-actions"></a>A szabály műveletek

A végrehajtási feltétel teljesülése esetén a szűrt blobok műveletek lépnek.

Előzetes verzióban elérhető a életciklus-felügyelet rétegezést, és a BLOB törlésének és a blob pillanatképeinek törlését támogatja. Minden szabály meghatározva a blobokat vagy pillanatképeket legalább egy művelettel kell rendelkeznie.

| Műveletek        | Alap Blob                                   | Pillanatkép      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Támogatja a gyakori elérésű szint jelenleg a blobokhoz         | Nem támogatott |
| tierToArchive | Támogatja a blobok jelenleg a gyakori vagy ritka elérésű szint: | Nem támogatott |
| delete        | Támogatott                                   | Támogatott     |

>[!NOTE] 
Ha egynél több művelet van megadva a ugyanennek a blobnak, életciklus-felügyelet a legkevésbé költséges művelet a blob vonatkozik. (pl. művelet `delete` művelet olcsóbb `tierToArchive`. A művelet `tierToArchive` művelet olcsóbb `tierToCool`.)

Előzetes verzióban elérhető a művelet-végrehajtási feltételek kora alapulnak. Alap blob használ utolsó módosítás időpontja kora követheti, és a blob-pillanatképeket használ pillanatkép létrehozásának idejét kora nyomon követéséhez.

| A művelet végrehajtási állapot | A feltétel értéke | Leírás |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Egész érték, amely jelzi a kora napokban | Az alap blob műveletekhez érvényes feltétel |
| daysAfterCreationGreaterThan     | Egész érték, amely jelzi a kora napokban | Blob pillanatkép műveletek esetében érvényes feltétel | 

## <a name="examples"></a>Példák
Az alábbi példák bemutatják, hogyan lehet gyakori helyzetek meg életciklus-szabályzat előírásainak.

### <a name="move-aging-data-to-a-cooler-tier"></a>Egy írásként elévülési adatok áthelyezése

Az alábbi példa bemutatja, hogyan előtaggal van ellátva a blokkblobok használatát támogatják az átmenet `container1/foo` vagy `container2/bar`. A szabályzat értékre vált, a blobokat, amelyekre több mint 30 nap elérésű tárolási szintre való még nem lett módosítva, és a blobok, az archív szinten 90 nap során nem módosított:

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

Egyes adatok pedig inaktívan a felhőben maradnak, és a tárolást követően csak nagyon ritkán használják őket, ha használják őket egyáltalán. Ezeket az adatokat a legcélszerűbb archiválja a azonnal, miután az adatbetöltés sebességétől. A következő életciklus-szabályzat van konfigurálva, a betöltés adatok archiválása. Ebben a példában átmenetek letiltása a tárfiók tárolóban lévő blobok `archivecontainer` azonnal, az archív tárolási szintről. Az azonnali átállás kapcsolódással ható blobok 0 nap után az utolsó módosítás időpontja:

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

Bizonyos adatok várhatóan napok vagy akár hónapokat, csökkentheti a költségeket vagy hatósági szabályozás alá eső eleget a létrehozása után lejár. Életciklus-kezelési szabályzat állítható lejár az adatok által adatok életkor alapján törlése. Az alábbi példa bemutatja egy szabályzatot, amely törli az összes blokkblobok (a nem megadott előtag) 365 napnál régebbi.

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

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan adatok véletlen törlés utáni helyreállításhoz:

- [Helyreállítható Törlés az Azure Storage-BLOB ](../blobs/storage-blob-soft-delete.md)
