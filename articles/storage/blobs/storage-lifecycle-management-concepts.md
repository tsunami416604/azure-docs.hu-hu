---
title: Az Azure Storage életciklusának kezelése
description: Megtudhatja, hogyan hozhat létre életciklus-szabályzatokat az adatok gyors és lassú elérésű és archív szintjeire való áttéréséhez.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: e4d961603ab0ade1bb175161fffd7f085a1f644b
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934085"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Az Azure Blob Storage életciklusának kezelése

Az adatkészletek egyedi életciklusokkal rendelkeznek. Az életciklus korai szakaszában a felhasználók gyakran férnek hozzá bizonyos adatszolgáltatásokhoz. A hozzáférés azonban drasztikusan csökken, mint az adatvesztés. Egyes adatforgalom üresjáratban marad a felhőben, és a tárolása ritkán történik meg. Néhány adat a létrehozás után nappal vagy hónapokban lejár, míg más adathalmazok aktívan olvashatók és módosulnak az élettartamuk során. Az Azure Blob Storage életciklus-kezelése a GPv2 és a blob Storage-fiókok részletes, szabályon alapuló szabályzatát kínálja. A szabályzat segítségével átválthatja az adatait a megfelelő hozzáférési rétegekbe, vagy lejárhat az adatéletciklus végén.

Az életciklus-kezelési házirend a következőket teszi lehetővé:

- A Blobok átállítása a hűvösebb tárolási szintre (gyors elérésű, gyors archiválásra vagy az archiválásra) a teljesítmény és a költséghatékonyság optimalizálása érdekében
- Blobok törlése életciklusuk végén
- A Storage-fiók szintjén naponta egyszer futtatandó szabályok definiálása
- Szabályok alkalmazása a tárolók vagy a Blobok egy részhalmaza számára (előtag használata szűrőkkel)

Vegyünk például egy olyan forgatókönyvet, amelyben az adatmennyiség az életciklus korai szakaszában, de csak két hét múlva alkalmanként válik elérhetővé. Az első hónapban a rendszer ritkán fér hozzá az adatkészlethez. Ebben az esetben a gyors tárolás a korai fázisokban a legjobb. A ritka elérésű tárolás a legmegfelelőbb az alkalmi hozzáféréshez. Az Archive Storage a legjobb lehetőség az adatvesztést követően a hónapban. Ha a tárolási rétegek mennyiségét az adatmennyiség tekintetében állítja be, az igényeinek leginkább megfelelő tárolási lehetőségeket is megtervezheti. Az áttérés eléréséhez az életciklus-kezelési házirend szabályai elérhetők az adatvesztéshez a hűvösebb rétegekbe.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>A Storage-fiók támogatása

Az életciklus-kezelési házirend általános célú v2 (GPv2) fiókokkal, blob Storage-fiókokkal és prémium szintű blokk blob Storage-fiókokkal érhető el. A Azure Portal egy meglévő általános célú (GPv1-) fiókot frissíthet egy GPv2-fiókra. További információ a tárfiókokról: [Az Azure-tárfiókok áttekintése](../common/storage-account-overview.md).  

## <a name="pricing"></a>Díjszabás

Az életciklus-kezelési szolgáltatás díjmentes. Az ügyfelek a [List Blobok](https://docs.microsoft.com/rest/api/storageservices/list-blobs) szokásos üzemeltetési költségeit és a [blob-rétegek](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API-hívásait terhelik. A törlési művelet ingyenes. A díjszabással kapcsolatos további információkért lásd a [Blobok díjszabásának blokkolása](https://azure.microsoft.com/pricing/details/storage/blobs/)című témakört.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az életciklus-kezelési funkció az összes Azure-régióban elérhető.

## <a name="add-or-remove-a-policy"></a>Szabályzat hozzáadása vagy eltávolítása

A szabályzatokat a következő módszerek bármelyikével adhatja hozzá, szerkesztheti vagy távolíthatja el:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST API-k](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Ez a cikk bemutatja, hogyan kezelheti a szabályzatokat a portál és a PowerShell-metódusok használatával.  

> [!NOTE]
> Ha engedélyezi a tűzfalszabályok beállításait a Storage-fiókjához, előfordulhat, hogy az életciklus-kezelési kérelmek le lesznek tiltva. Ezeket a kéréseket feloldja a megbízható Microsoft-szolgáltatások kivételének biztosításával. További információt a [tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)című témakör kivételek című szakaszában talál.

### <a name="azure-portal"></a>Azure Portal

Két módon adhat hozzá házirendet a Azure Portalon keresztül. 

* [Azure Portal listanézet](#azure-portal-list-view)
* [Azure Portal kód nézet](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Azure Portal listanézet

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza a **minden erőforrás** lehetőséget, majd válassza ki a Storage-fiókját.

3. A **blob Service**területen válassza az **életciklus-kezelés** lehetőséget a szabályok megtekintéséhez vagy módosításához.

4. Válassza a **listanézet** lapot.

5. Válassza a **szabály hozzáadása** lehetőséget, majd töltse ki a **műveleti set** Form mezőket. A következő példában a blobokat a rendszer a lassú tárterületre helyezi át, ha 30 napig nem módosították őket.

   ![Életciklus-kezelési műveleti készlet lapja Azure Portal](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Válassza a **szűrő beállítása** lehetőséget egy opcionális szűrő hozzáadásához. Ezután a **Tallózás** gombra kattintva megadhatja a szűrni kívánt tárolót és mappát.

   ![Életciklus-felügyeleti szűrő beállított lapja Azure Portal](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. A házirend-beállítások áttekintéséhez válassza a **felülvizsgálat + Hozzáadás** elemet.

9. Az új szabályzat hozzáadásához válassza a **Hozzáadás** lehetőséget.

#### <a name="azure-portal-code-view"></a>Azure Portal kód nézet
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza a **minden erőforrás** lehetőséget, majd válassza ki a Storage-fiókját.

3. A **blob szolgáltatás**területen válassza az **életciklus-kezelés** lehetőséget a szabályzat megtekintéséhez vagy módosításához.

4. A következő JSON-példa egy olyan házirendre mutat, amely beilleszthető a **Code View (kód nézet** ) lapra.

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

5. Kattintson a **Mentés** gombra.

6. A JSON-példával kapcsolatos további információkért tekintse [](#policy) meg a szabályzatok és [szabályok](#rules) szakaszt.

### <a name="powershell"></a>PowerShell

A következő PowerShell-szkripttel adhat hozzá szabályzatot a Storage-fiókhoz. A `$rgname` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a Storage-fiók nevével.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

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

## <a name="azure-resource-manager-template-with-lifecycle-management-policy"></a>Azure Resource Manager-sablon életciklus-kezelési házirenddel

Az életciklus-kezelést megadhatja Azure Resource Manager sablonok használatával. Itt látható egy sablon az RA-GRS GPv2 Storage-fiók életciklus-kezelési házirenddel való üzembe helyezéséhez.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

## <a name="policy"></a>Szabályzat

Az életciklus-kezelési szabályzat egy JSON-dokumentum szabályainak gyűjteménye:

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

A szabályzatok a szabályok gyűjteményei:

| Paraméternév | Paraméter típusa | Megjegyzések |
|----------------|----------------|-------|
| `rules`        | Szabály objektumainak tömbje | Egy házirendben legalább egy szabályra van szükség. Egy házirendben legfeljebb 100 szabályt adhat meg.|

A szabályzaton belüli szabályok több paraméterrel rendelkeznek:

| Paraméternév | Paraméter típusa | Megjegyzések | Kötelező |
|----------------|----------------|-------|----------|
| `name`         | Sztring |A szabály neve legfeljebb 256 alfanumerikus karaktert tartalmazhat. A szabály neve megkülönbözteti a kis-és nagybetűket.  Egy szabályzaton belül egyedinek kell lennie. | True |
| `enabled`      | Logikai | Egy nem kötelező logikai érték, amely lehetővé teszi egy szabály ideiglenes letiltását. Az alapértelmezett érték igaz, ha nincs beállítva. | False (Hamis) | 
| `type`         | Enumerálási érték | A jelenlegi érvényes típus `Lifecycle`:. | True |
| `definition`   | Az életciklus-szabályt meghatározó objektum | Mindegyik definíció egy szűrő készletből és egy műveleti készletből áll. | True |

## <a name="rules"></a>Szabályok

Mindegyik szabály definíciója tartalmaz egy szűrőt és egy műveleti készletet. A [szűrési készlet](#rule-filters) korlátozza a szabályok műveleteit egy adott objektumra a tárolón vagy az objektumok nevein belül. A [művelet beállítja](#rule-actions) a szintet vagy a DELETE műveletet a szűrt objektumokra.

### <a name="sample-rule"></a>Minta szabály

A következő minta szabály úgy szűri a fiókot, hogy a műveleteit a `container1` -ben és a- `foo`ben létező objektumokon futtassa.  

- Rétegbeli blob – a legutolsó módosítás után 30 nappal a lehűtési szintig
- Szintű blob az archiválási szintre 90 nappal az utolsó módosítás után
- A blob 2 555 nap (hét év) törlése az utolsó módosítás után
- BLOB-Pillanatképek törlése 90 nappal a pillanatkép létrehozása után

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

### <a name="rule-filters"></a>Szabályok szűrői

A szűrő korlátozza a szabályok műveleteit a Blobok egy részhalmazára a Storage-fiókon belül. Ha egynél több szűrő van definiálva, az összes `AND` szűrőn egy logikai fut.

A szűrők a következők:

| Szűrő neve | Szűrő típusa | Megjegyzések | Kötelező |
|-------------|-------------|-------|-------------|
| blobTypes   | Előre definiált enumerálási értékek tömbje. | A jelenlegi kiadás támogatja `blockBlob`. | Igen |
| prefixMatch | Karakterláncok tömbje az előtagok megfeleltetéséhez. Mindegyik szabály legfeljebb 10 előtagot tud definiálni. Egy előtag-karakterláncnak a tároló nevével kell kezdődnie. Ha például egy szabályhoz tartozó `https://myaccount.blob.core.windows.net/container1/foo/...` összes blobot szeretné egyeztetni, a `container1/foo`prefixMatch a következő:. | Ha nem határoz meg prefixMatch, a szabály a Storage-fiókban lévő összes blobra vonatkozik.  | Nem |

### <a name="rule-actions"></a>Szabály műveletei

Ha a futtatási feltétel teljesül, a rendszer a szűrt blobokra alkalmazza a műveleteket.

Az életciklus-kezelés támogatja a Blobok kiszervezését és törlését, valamint a blob-Pillanatképek törlését. Adjon meg legalább egy műveletet a Blobok vagy blob-Pillanatképek minden szabályához.

| Action        | Alap blob                                   | Pillanatkép      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Jelenleg a gyors elérésű szinten támogatott Blobok támogatása         | Nem támogatott |
| tierToArchive | Jelenleg a gyors vagy a lassú elérésű szinten támogatja a blobokat | Nem támogatott |
| törlés        | Támogatott                                   | Támogatott     |

>[!NOTE]
>Ha ugyanazon a blobon több műveletet is definiál, az életciklus-kezelés a legkevesebb költséges műveletet alkalmazza a blobra. Például a művelet `delete` olcsóbb a műveletnél `tierToArchive`. A `tierToArchive` művelet olcsóbb a műveletnél `tierToCool`.

A futtatási feltételek életkoron alapulnak. Az alapblobok az utolsó módosítás idejét használják a kor nyomon követéséhez, a blob-Pillanatképek pedig a pillanatkép létrehozásának idejét használják a kor nyomon követéséhez.

| Művelet futtatási feltétele             | Feltétel értéke                          | Leírás                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | Egész számú érték, amely a kora napokat jelzi | Az alap blob-műveletek feltétele     |
| daysAfterCreationGreaterThan     | Egész számú érték, amely a kora napokat jelzi | A blob-Pillanatképek műveleteinek feltétele |

## <a name="examples"></a>Példák

Az alábbi példák bemutatják, hogyan lehet kezelni a gyakori forgatókönyveket életciklus-szabályzatokkal.

### <a name="move-aging-data-to-a-cooler-tier"></a>Az adatvesztést a hűvösebb szintjére helyezheti át

Ez a példa azt mutatja be, hogyan lehet áttérni `container1/foo` a `container2/bar`blokk Blobok előre rögzített vagy. A házirend olyan blobokat vált át, amelyek több mint 30 nap alatt nem lettek módosítva a lassú tároláshoz, és a Blobok 90 nap alatt nem módosultak az archiválási szintre:

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

### <a name="archive-data-at-ingest"></a>Adatok archiválása betöltéskor

Egyes adatforgalom üresjáratban marad a felhőben, és ritkán, ha még egyszer is hozzáfér a tárolóhoz. A következő életciklus-házirend úgy van konfigurálva, hogy a betöltés után archiválja az adatok archiválását. Ez a példa a tárolóban lévő Storage-fiókban `archivecontainer` lévő Blobok archiválási szintre való átváltását eredményezi. Az áttérést az utolsó módosítás időpontja után 0 nappal a Blobok alapján hajtja végre a rendszer:

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

### <a name="expire-data-based-on-age"></a>Az életkor alapján lejár az adatvesztés

A létrehozás után bizonyos adatértékek várhatóan lejárnak a napokban vagy hónapokban. Az életciklus-kezelési házirendet beállíthatja úgy, hogy az adatkor alapján törléssel lejárjon az adatvesztés. Az alábbi példa egy olyan házirendet mutat be, amely a 365 napnál régebbi összes blokk blobot törli.

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

### <a name="delete-old-snapshots"></a>Régi Pillanatképek törlése

A gyakran használt és a teljes élettartamon keresztül elért adatmennyiségek esetében a pillanatképeket gyakran a régebbi verziók nyomon követésére használják. Létrehozhat egy szabályzatot, amely a régi pillanatképeket törli a pillanatképek kora alapján. A pillanatképek korát a pillanatkép létrehozásának időpontjának kiértékelésével határozzuk meg. Ez a házirend-szabály törli a pillanatkép- `activedata` létrehozás után 90 napos vagy régebbi tárolóban lévő blob-pillanatképeket.

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

## <a name="faq"></a>GYIK

**Létrehoztam egy új szabályzatot, miért nem azonnal futnak a műveletek?**  
A platform naponta egyszer futtatja az életciklus-szabályzatot. Miután konfigurálta a házirendet, akár 24 órát is igénybe vehet, hogy egyes műveletek első alkalommal fussanak.  

**Manuálisan dolgozom fel az archivált blobokat, hogyan lehet megakadályozni, hogy átmenetileg visszakerüljön az archiválási szintre?**  
Ha egy blobot egy hozzáférési rétegből egy másikba helyez át, az utolsó módosítás időpontja nem változik. Ha az archivált blobokat manuálisan rehidratálja a gyors szintre, az életciklus-kezelő motor vissza fogja helyezni az archiválási szintre. Tiltsa le az ezt a blobot érintő szabályt ideiglenesen annak megakadályozása érdekében, hogy az archiválható legyen. Másolja a blobot egy másik helyre, ha a gyors elérésű rétegben kell maradni. Engedélyezze újra a szabályt, ha a blob biztonságosan visszahelyezhető az archiválási szintre. 


## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan állíthatja helyre az adatokat a véletlen törlés után:

- [Az Azure Storage-blobok helyreállítható törlése](../blobs/storage-blob-soft-delete.md)
