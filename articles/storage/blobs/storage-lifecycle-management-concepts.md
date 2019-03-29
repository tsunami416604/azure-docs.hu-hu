---
title: Az Azure Storage-életciklus kezelése
description: Megtudhatja, hogyan hozhat létre életciklus szabályok átmenet elévülési adatok gyors elérésűről lassú elérésű és archív tárolási szint.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: conceptual
ms.date: 3/20/2019
ms.author: yzheng
ms.subservice: common
ms.openlocfilehash: df38fd30c1bfba4993e9992783a130262a703370
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579512"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Az Azure Blob storage, életciklus kezelése

Adatkészletek egyedi életciklusokkal rendelkeznek. Korai szakaszában az életciklus férhetnek egyes adatok milyen gyakran. De szükségük van a hozzáférésre, az adatok életkorának csökken. Bizonyos adatok tétlen marad, a felhőben, és a ritkán elért tárolt. Míg egyéb adatkészletekhez aktívan beolvasása és módosítanak teljes az élettartam néhány adatot napok vagy hónappal a létrehozás után lejár. Az Azure Blob storage életciklus-felügyelet a GPv2 és Blob storage-fiókok gazdag, szabályalapú házirendet kínál. A szabályzat segítségével az adatok a megfelelő hozzáférési szinten való áttérés, vagy az adatok életciklus végén lejár.

Az életciklus-kezelési szabályzat lehetővé teszi:

- Áttérés a blobokon át a célszintű (gyakori, ritka elérésű, az archív tárolási szint ritkáról gyakori elérésű vagy ritka elérésű, archív) tárolási szintek optimalizálható a teljesítmény és költség
- Azok életciklusának végén található blobok törlése
- A storage-fiók szintjén naponta egyszer futtatni szabályok definiálása
- Tárolók és blobok (használatával előtagok szűrők) egy részét a szabályok alkalmazása

Fontolja meg a forgatókönyv, ahol egy adatkészlet a gyakori hozzáférést kap a korai fázisában – az életciklus során azonban majd csak alkalmanként két hét után. Az első hónapon túli ritkán hozzáfért az adatkészlet. Ebben a forgatókönyvben a gyakori elérésű tárolási a legcélszerűbb kezdeti szakaszában. Ritkán használt adatok tárolási alkalmi hozzáférést igénylő leginkább megfelelő, és az archive storage a legjobb szint lehetőség az adatok életkorának után több mint egy hónapja. Tárolási rétegek adatok korát tekintetben fokozottabban tervezhet a legkevésbé költséges tárolási lehetőség az igényeinek. Az átállás eléréséhez életciklus felügyeletiházirend-szabályok elévülési adatok áthelyezése az alacsonyabb szinten érhető el.

## <a name="storage-account-support"></a>Storage-fiókok támogatása

Az életciklus-kezelési szabályzat érhető el mindkét General Purpose v2 (GPv2-) fiókok és a Blob storage-fiókok. Az Azure Portalon frissítheti meglévő általános célú (GPv1-) fiók egy egyszerű, egykattintásos folyamat keresztül GPv2-fiókra. További információ a tárfiókokról: [Az Azure-tárfiókok áttekintése](../common/storage-account-overview.md).  

## <a name="pricing"></a>Díjszabás 

Az életciklus felügyeleti funkció ingyenesen elérhető. A normál művelet költsége díjszabásának a [Blobok listázása](https://docs.microsoft.com/rest/api/storageservices/list-blobs) és [Blobszint beállítása](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API-hívások. Törlési művelet díjmentes. Díjszabással kapcsolatos további információkért lásd: [Blokkbob-díjszabás](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás 
Az életciklus szolgáltatást minden nyilvános Azure-régióban érhető el. 


## <a name="add-or-remove-a-policy"></a>Adja hozzá, vagy távolítsa el a szabályzat 

Hozzáadása, szerkesztése vagy eltávolítani egy házirendet az Azure portal használatával [Azure PowerShell-lel](https://github.com/Azure/azure-powershell/releases), az Azure CLI, a REST API-k vagy az ügyféleszközökben. Ez a cikk bemutatja, hogyan házirend kezelése a portálon és a PowerShell-módszer használatával.  

> [!NOTE]
> Ha engedélyezi a tűzfalszabályok a tárfiók, lifecycle management kérelmek blokkolhatja. Ezek a kérelmek azáltal, hogy kivételek blokkolásának feloldásához. További információkért tekintse meg a kivételek szakaszában [tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki **összes erőforrás** , majd a storage-fiókjában.

3. Alatt **Blob Service**válassza **életciklus-felügyelet** megtekintése vagy módosítása a szabályzatban.

### <a name="powershell"></a>PowerShell

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery 

#Create a new action object

$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd 

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy 
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1

```


## <a name="policy"></a>Szabályzat

Életciklus-kezelési szabályzat szabályok a JSON-dokumentumok gyűjteménye:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
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


A házirend szabályok gyűjteménye:

| Paraméter neve | Paraméter típusa | Megjegyzések |
|----------------|----------------|-------|
| szabályok          | A szabály objektumokból álló tömb | Legalább egy szabályt egy házirendet a szükséges. Egy szabályzatban legfeljebb 100 szabályokat definiálhat.|

A házirend minden egyes szabály több paraméterekkel rendelkezik:

| Paraméter neve | Paraméter típusa | Megjegyzések | Szükséges |
|----------------|----------------|-------|----------|
| név           | String |A szabály neve legfeljebb 256 alfanumerikus karaktereket tartalmazhat. Szabály neve a kis-és nagybetűket.  Egy házirend egyedinek kell lennie. | True (Igaz) |
| engedélyezve | Logikai | Egy nem kötelező logikai érték beolvasása, hogy ideiglenes szabály le van tiltva. Alapértelmezett érték: igaz, ha nincs beállítva. | False (Hamis) | 
| type           | Enum érték | Az aktuális érvényes típus `Lifecycle`. | True (Igaz) |
| definíció     | Egy objektum, amely meghatározza az életciklus-szabály | Minden egyes definíció épül fel egy szűrő és egy műveletet. | True (Igaz) |

## <a name="rules"></a>Szabályok

Minden egyes szabály definíciója tartalmaz egy szűrő és egy műveletet. A [set szűrése](#rule-filters) szabály műveletek a tárolóban lévő objektumok meghatározott készlete, vagy ha objektumok nevét. A [művelet set](#rule-actions) vonatkozik a réteg vagy törlési műveleteket az objektumok szűrt készlete.

### <a name="sample-rule"></a>Mintául szolgáló szabály
A következő példa szabály szűri a fiók a műveletek futtatásához objektumon belül létező `container1` **és** kezdődnie `foo`.  

- Réteg blob ritka elérésű szint utolsó módosítása után 30 nappal
- Réteg blob az archív szint utolsó módosítása után 90 napig
- Utolsó módosítás után 2,555 nap (hét évig) blob törlése
- Pillanatkép létrehozása után 90 napig törölni a blobpillanatképeket

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
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

Érvényes szűrők a következők:

| Szűrő neve | Szűrő típusa | Megjegyzések | Kötelező |
|-------------|-------------|-------|-------------|
| blobTypes   | Előre definiált enum értékek tömbje. | A jelenlegi kiadásban támogatja a `blockBlob`. | Igen |
| prefixMatch | Meg kell egyeznie az előtagok karakterláncok tömbje. Minden szabály legfeljebb 10 előtagok adhatja meg. Előtag-karakterláncra tárolónévnek kell kezdődnie. Például, ha az összes BLOB alapján egyeztetni kívánt "https://myaccount.blob.core.windows.net/container1/foo/..." a szabály a prefixMatch van `container1/foo`. | Ha nem ad meg prefixMatch, a szabály vonatkozik a tárfiókban lévő összes BLOB.  | Nem |

### <a name="rule-actions"></a>A szabály műveletek

A futtatási feltétel teljesülése esetén a szűrt blobok műveletek lépnek.

Életciklus-felügyelet rétegezést, és a blobok törlését és a blob pillanatképeinek törlését is támogatja. Adja meg legalább egy műveletet az egyes szabályokhoz blobokat vagy pillanatképeket.

| Műveletek        | Alap Blob                                   | Pillanatkép      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Támogatja a gyakori elérésű szint jelenleg a blobokhoz         | Nem támogatott |
| tierToArchive | Támogatja a gyors vagy lassú elérésű szint jelenleg a blobokhoz | Nem támogatott |
| delete        | Támogatott                                   | Támogatott     |

>[!NOTE] 
>Ha egynél több művelet ugyanennek a blobnak a definiálja, életciklus-felügyelet a legkevésbé költséges művelet a blob vonatkozik. Ha például a művelet `delete` művelet olcsóbb `tierToArchive`. A művelet `tierToArchive` művelet olcsóbb `tierToCool`.

A futtatási feltételek kora alapulnak. Alap blobok az utolsó módosítás időpontja segítségével nyomon követheti a életkor és pillanatképek használatát a pillanatkép-létrehozás ideje blob-kor nyomon követéséhez.

| A művelet futtatása feltétel | A feltétel értéke | Leírás |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Egész érték, amely jelzi a kora napokban | Az alap blob műveletekhez érvényes feltétel |
| daysAfterCreationGreaterThan     | Egész érték, amely jelzi a kora napokban | Blob pillanatkép műveletek esetében érvényes feltétel | 

## <a name="examples"></a>Példák
Az alábbi példák bemutatják, hogyan lehet gyakori helyzetek meg életciklus-szabályzat előírásainak.

### <a name="move-aging-data-to-a-cooler-tier"></a>Egy írásként elévülési adatok áthelyezése

Ez a példa bemutatja, hogyan előtaggal van ellátva a blokkblobok használatát támogatják az átmenet `container1/foo` vagy `container2/bar`. A szabályzat értékre vált, a blobokat, amelyekre több mint 30 nap elérésű tárolási szintre való még nem lett módosítva, és a blobok, az archív szinten 90 nap során nem módosított:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
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

Néhány adatot tétlen marad, a felhőben és a ritkán, ha követően tárolja. A következő életciklus-szabályzat van konfigurálva, az adatok archiválása, miután az adatbetöltés sebességétől. Ebben a példában átmenetek letiltása a tárfiók tárolóban lévő blobok `archivecontainer` be az archív tárolási szintről. Az átállás a blobok eljáró 0 nap utolsó módosítás időpontja után végezhető el:

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
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
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
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
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",      
    "definition": {
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

A platform naponta egyszer fut, az életciklus-szabályzat. Után konfigurálhat egy szabályzatot, az első alkalommal történő futtatása bizonyos műveletek (például a rétegezést és törlése) esetében akár 24 óráig is eltarthat.  

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan adatok véletlen törlés utáni helyreállításhoz:

- [Helyreállítható Törlés az Azure Storage-BLOB](../blobs/storage-blob-soft-delete.md)
