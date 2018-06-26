---
title: Az Azure Storage-életciklus felügyelete
description: Megtudhatja, hogyan létesíthet életciklus házirendszabályok átmenet againg adatokat ritkán kapcsolatos és archiválási rétegek a gyakran használt adatok.
services: storage
author: yzheng-msft
manager: jwillis
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 04/30/2018
ms.author: yzheng
ms.openlocfilehash: 9721935f005bbd9a5dc261fe801ecc14744b004f
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752792"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Az Azure Blob Storage-életciklus (előzetes verzió) kezelése

Adatkészletek egyedi életciklusának rendelkezik. Néhány adatokhoz korai gyakran a életciklusának, de a kell, az adatok életkorának drasztikusan csökken. Egyes adatokat tétlennek lennie a felhőben, és ritkán érhető el egyszer tárolja. Néhány adat napok vagy hónapok létrehozása után lejár, amíg más adatkészletek aktívan olvasni és módosítani az élettartama során. Az Azure Blob Storage életciklusának kezelésére (előzetes verzió) biztosít egy gazdag, szabályalapú házirendet, amely az adatokat, hogy a legjobb hozzáférési szint nyerjenek és adatok életciklus végén lejár.

Életciklus-kezelési házirend segítségével:

- Áttérés a blobok hűtőre tárolási réteghez (gyakran használt adatok Cool, interaktív archívumhoz, vagy archív Cool) optimalizálható a teljesítmény- és költség
- A életciklusának végén blobok törlése
- Meghatározhat szabályokat a tárolási fiók szintjén naponta egyszer futtatását (támogatott GPv2 és a Blob storage-fiókok)
- Szabályok alkalmazása-tárolók és blobok (előtagok használatával szűrőként) egy részét

Vegye figyelembe az adatok, amelyek gyakran az életciklus korai szakaszában érhető el, csak szükség van két hét után időnként és ritkán érhető el egy hónap után, vagy azon kívül. Ebben az esetben a gyakran használt adatok esetén ajánlott a korai szakaszában ritkán használt adatok tárolási legmegfelelőbb alkalmi hozzáférést, pedig archivált adatok a legjobb réteg lehetőség az adatok életkorának után egy hónap alatt. A tárolási rétegek adatok korát tekintetben beállításával tervezhet a legkisebb költségű tárolási lehetőségek az igényeinek. Ez a változás eléréséhez életciklus-kezelési házirendek korosodási adatok áthelyezése hűtőre rétegek számára elérhetők lesznek.

## <a name="storage-account-support"></a>Tárolási fiók támogatása

Életciklus-kezelési házirend érhető el a mindkét általános célú v2 (GPv2) fiók és a Blob Storage-fiók. Átválthat a meglévő általános célú (GPv1) fiók egy egyszerű egy kattintással folyamat az Azure portálon keresztül GPv2 fiókra. További információk: [Az Azure Storage-fiók beállításai](../common/storage-account-options.md).  

## <a name="pricing"></a>Díjszabás 

Életciklusát szolgáltatása Preview ingyenesen elérhető. Ügyfelek rendszeres művelet költsége van szó, a [lista Blobok](https://docs.microsoft.com/rest/api/storageservices/list-blobs) és [Blob szint beállítása](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API-hívásokat. Lásd: [Blokkblob árképzési](https://azure.microsoft.com/pricing/details/storage/blobs/) tudhat meg többet az árképzés.

## <a name="register-for-preview"></a>Az előzetes regisztrálása 
Nyilvános előzetes verziójában regisztrálásához szüksége lesz a szolgáltatás előfizetésének regisztrálása kérelmet küldeni. A kérelem jóváhagyása (néhány napban), után meglévő és új GPv2 vagy a Blob Storage-fiókkal sem 2 USA nyugati régiója és nyugati középső Régiójában lesz a szolgáltatás engedélyezve van. Előzetes csak a blokkblob esetén támogatott. A legtöbb előzetes, ez a funkció nem használható termelési számítási feladatokhoz GA eléréséig

A kérelem elküldéséhez a következő parancsokat PowerShell vagy a parancssori felület.

### <a name="powershell"></a>PowerShell

Kérelmet küldeni:

```powershell
Register-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
A regisztrációs jóváhagyási állapotot a következő paranccsal ellenőrizheti:
```powershell
Get-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Ha a szolgáltatás jóváhagyott és megfelelően regisztrálva, a "Regisztrált" állapotba kell kapnia.

### <a name="cli-20"></a>CLI 2.0

Kérelmet küldeni: 
```cli
az feature register –-namespace Microsoft.Storage –-name DLM
```
A regisztrációs jóváhagyási állapotot a következő paranccsal ellenőrizheti:
```cli
-az feature show –-namespace Microsoft.Storage –-name DLM
```
Ha a szolgáltatás jóváhagyott és megfelelően regisztrálva, a "Regisztrált" állapotba kell kapnia. 


## <a name="add-or-remove-policies"></a>Szabályok hozzáadása vagy eltávolítása 

Hozzáadásához, szerkesztéséhez, vagy távolítsa el a szabályzatot, az Azure-portált használja, [PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.3-preview), REST API-k vagy az ügyfél eszközök a következő nyelveken: [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [ NODE.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby]( https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A tárfiókjának eléréséhez válassza az Összes erőforrás lehetőséget, majd válassza ki a tárfiókját.

3. Kattintson a beállítások panelen **életciklus-felügyeletének** Blob szolgáltatás megtekintéséhez és/vagy módosítsa a házirendek alapján csoportosítva.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

> [!NOTE]
Ha engedélyezte a tárfiók tűzfalszabályok, életciklus felügyeleti kérések blokkolhatja a. Tiltást kivételek biztosítása. További információkért tekintse meg a kivételek szakasz [tűzfalak és a virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policies"></a>Házirendek

A életciklus-kezelési házirendek gyűjteményei, egy JSON-dokumentum szabályok:

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
| verzió:        | Egy karakterlánc-értékben `x.x` | A kép verziószáma 0,5 |
| szabályok          | Egy szabály objektumokból álló tömb | Minden házirendben szükséges legalább egy szabályt. Előzetes megadhatja a házirend legfeljebb 4 szabálynál. |

A szabályban szükséges paraméterek a következők:

| Paraméter neve | Paraméter típusa | Megjegyzések |
|----------------|----------------|-------|
| Name (Név)           | Sztring | A szabály neve tetszőleges kombinációját alfanumerikus karaktereket tartalmazhat. Szabályának neve, a kis-és nagybetűket. Egy házirend belül egyedinek kell lennie. |
| type           | Enum érték | Az érvényes érték az előzetes `Lifecycle` |
| definíció     | Egy objektum, amely meghatározza az életciklus-szabály | Minden egyes definition egy szűrési és egy művelet készletre a épül fel. |

## <a name="rules"></a>Szabályok

Minden egyes szabályát leíró definíció beolvasása tartalmaz egy szűrési és egy művelet készletre. A következő minta szabály módosítja a réteg a kiinduló blokkblobokhoz előtaggal rendelkező `foo`. A házirendben ezek a szabályok vannak meghatározva:

- Réteg blob cool storage 30 nap után utolsó módosítás
- Archivált adatok blob réteg 90 nap után utolsó módosítás
- Törölni a blobot 2,555 napig (7 év) után utolsó módosítás
- Törölje a blob-pillanatképeket számított 90 napon belül pillanatkép létrehozása

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
          "prefixMatch": [ "foo" ]
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

Szűrők korlátozzák a szabály műveletek részhalmazára blobok a tárfiókon belül. Ha több szűrőt vannak meghatározva, a logikai `AND` összes szűrőt hajtja végre.

Előzetes érvényes szűrők a következők:

| Szűrő neve | Szűrő típusa | Megjegyzések | Szükséges |
|-------------|-------------|-------|-------------|
| blobTypes   | Előre definiált számbavételi értékek tömbjét. | Az előzetes kiadásban csak `blockBlob` esetén támogatott. | Igen |
| prefixMatch | Előtagok egyeznie kell a karakterláncokból álló tömb. | Ha az nincs definiálva, ez a szabály vonatkozik a fiókban az összes BLOB. | Nem |

### <a name="rule-actions"></a>A szabály műveletek

A végrehajtási feltétel teljesülésekor a szűrt blobok műveletek érvényesek.

A képen életciklus-felügyeletének támogatja, és adatdeduplikációval blob törlése és a blob pillanatképek törlését. Minden szabálynak rendelkeznie kell legalább egy műveletet blobok vagy blob pillanatképek definiálva.

| Műveletek        | Alap Blob                                   | Pillanatkép      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Támogatja a blob jelenleg a gyakran használt adatok réteg         | Nem támogatott |
| tierToArchive | Támogatja a blob jelenleg a gyakran használt adatok vagy a ritkán használt adatok réteg | Nem támogatott |
| delete        | Támogatott                                   | Támogatott     |

>[!NOTE] 
Ha egynél több művelet van definiálva azonos blobot, életciklus-felügyeletének vonatkozik a legkisebb költségű művelet a blob. (pl. művelet `delete` művelet olcsóbbak `tierToArchive`. A művelet `tierToArchive` művelet olcsóbbak `tierToCool`.)

A képen a művelet végrehajtási feltételek kora alapulnak. Alap blob használ utolsó módosítás időpontja nyomon követhető a korszűrő és blob-pillanatképek használ pillanatkép létrehozásának ideje korszűrő követésére.

| A művelet végrehajtási állapot | Az állapot értéke | Leírás |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Napban korát jelző egész szám | Alap blob műveletek érvényes feltételét |
| daysAfterCreationGreaterThan     | Napban korát jelző egész szám | A blob pillanatkép műveletek érvényes feltételét | 

## <a name="examples"></a>Példák
Az alábbi példák bemutatják, hogyan kezelje a életciklus szabályzatnak gyakori forgatókönyvek.

### <a name="move-aging-data-to-a-cooler-tier"></a>Helyezze át a korosodási adatok hűtőre réteghez

A következő példa bemutatja, hogyan előtagként blokkblobokat nyerjenek `foo` vagy `bar`. A házirend a blobok, amelyek még nem lett módosítva a ritkán használt adatok több mint 30 napban, és az archív réteghez 90 napban nem módosított blobok átmenetek:

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
            "prefixMatch": [ "foo", "bar" ]
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

### <a name="archive-data-at-ingest"></a>A bemeneti adatok archiválása 

Egyes adatok pedig inaktívan a felhőben maradnak, és a tárolást követően csak nagyon ritkán használják őket, ha használják őket egyáltalán. Ezeket az adatokat a legcélszerűbb azonnal, amennyiben van keresztül a szervezetbe archiválja. A következő életciklusra vonatkozó szabályzata bemeneti adatok archiválására van konfigurálva. Ez a példa átmenetek blokkblobokat előtagja a tárfiók a `archive` azonnal be egy archív réteget. Az azonnali átmenet ható blobok 0 nap után utolsó módosítási időpontjának úgy érhető el:

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
            "prefixMatch": [ "archive" ]
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

### <a name="expire-data-based-on-age"></a>Adatok korát alapuló lejár

Egyes adatokat várt napok vagy hónapok csökkentheti a költségeket vagy kormányzati szabályoknak létrehozása után lejár. A életciklus-kezelési házirendek beállítható adatok lejár által adatok korát alapján törlését. A következő példa bemutatja, hogy törli az összes blokkoló blobokon (előtaggal nincs megadva) házirend 365 napnál régebbi.

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

### <a name="delete-old-snapshots"></a>Törölje a régi pillanatképeket

Módosította, és teljes élettartama alatt rendszeresen elért adatok esetén pillanatképek gyakran segítségével nyomon követhető a adatok régebbi verzióit. Létrehozhat olyan házirendet, amely törli a régi pillanatképek pillanatkép életkor alapján. A pillanatkép-kor határozza meg a pillanatkép létrehozása idő kiértékelése. A törlések blokkolása előtaggal rendelkező blob pillanatképek házirendszabály `activeData` , amelyek a 90 napos vagy régebbi pillanatkép létrehozása után.

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
            "prefixMatch": [ "activeData" ]
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

Megtudhatja, hogyan szeretne adatokat helyreállítani a véletlen törlés után:

- [Az Azure Storage blobs világos törlése ](../blobs/storage-blob-soft-delete.md)
