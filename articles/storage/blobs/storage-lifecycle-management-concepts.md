---
title: Az Azure Storage életciklusának kezelése
description: Ismerje meg, hogyan hozhat létre életciklus-szabályzati szabályokat az elévülési adatok gyakori elérésű és az archív szintekről való áttéréshez.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 238c12baf55b525a24107a727d09588ef06a6bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598306"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Az Azure Blob Storage-életciklus felügyelete

Az adatkészletek egyedi életciklussal rendelkeznek. Az életciklus korai szakaszában az emberek gyakran férnek hozzá bizonyos adatokhoz. De a hozzáférés iránti igény drasztikusan csökken, ahogy az adatok öregszik. Egyes adatok tétlenmarad a felhőben, és ritkán érhető el, ha tárolt. Egyes adatok napokkal vagy hónapokkal a létrehozás után járnak le, míg más adatkészletek aktívan olvashatók és módosulnak a teljes élettartamuk során. Az Azure Blob-tárolási életciklus-kezelés gazdag, szabályalapú szabályzatot kínál a GPv2- és Blob storage-fiókokhoz. A szabályzat segítségével átválthatja az adatokat a megfelelő hozzáférési szintekre, vagy lejár az adatok életciklusának végén.

Az életciklus-kezelési szabályzat lehetővé teszi:

- A teljesítmény és a költség optimalizálása érdekében áttűnési blobok egy hűvösebb tárolási szintre (forró -hűtés, gyors archiválás vagy gyors archiválás)
- Blobok törlése életciklusuk végén
- A tárfiók szintjén naponta egyszer futtatandó szabályok meghatározása
- Szabályok alkalmazása tárolókra vagy blobok egy részhalmazára (előtagok használata szűrőkként)

Fontolja meg egy olyan forgatókönyv, amelyben az adatok kap gyakori hozzáférést az életciklus korai szakaszában, de csak alkalmanként két hét után. Az első hónap után az adatkészlet ritkán érhető el. Ebben a forgatókönyvben a gyakori tárolás a korai szakaszban a legjobb. A hűvös tárolás a legmegfelelőbb az alkalmi hozzáféréshez. Archív tárolás a legjobb szint lehetőség után az adatok korosztály több mint egy hónap. A tárolási szintek nek az adatok korához viszonyított módosításával megtervezheti az igényeinek legkevésbé költséges tárolási lehetőségeket. Az átmenet eléréséhez életciklus-kezelési szabályzatszabályok állnak rendelkezésre az elévülési adatok hűvösebb szintekre való áthelyezéséhez.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>Tárfiók támogatása

Az életciklus-kezelési szabályzat általános célú v2 (GPv2) fiókokkal, Blob storage-fiókokkal és prémium szintű blokkblob-tárfiókokkal érhető el. Az Azure Portalon frissítheti a meglévő általános célú (GPv1) fiókot GPv2-fiókra. A tárfiókokról az [Azure storage-fiók áttekintése című témakörben olvashat bővebben.](../common/storage-account-overview.md)  

## <a name="pricing"></a>Díjszabás

Az életciklus-kezelési funkció ingyenes. Az ügyfelek nek a [listablobok](https://docs.microsoft.com/rest/api/storageservices/list-blobs) és a [Blobtier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API-hívások beállítása rendszeres működési költségét kell fizetnie. A törlési művelet ingyenes. A díjszabásról a [Blobok tiltási díjszabása](https://azure.microsoft.com/pricing/details/storage/blobs/)című témakörben talál további információt.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az életciklus-kezelési funkció minden Azure-régióban elérhető.

## <a name="add-or-remove-a-policy"></a>Házirend hozzáadása vagy eltávolítása

Házirendet az alábbi módszerek kel vehet fel, szerkeszthet vagy távolíthat el:

* [Azure-portál](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST API-k](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

A házirend teljes egészében olvasható vagy írható. A részleges frissítések nem támogatottak. 

> [!NOTE]
> Ha engedélyezi a tárfiók tűzfalszabályait, előfordulhat, hogy az életciklus-kezelési kérelmek le vannak tiltva. Ezeket a kérelmeket feloldhatja, ha kivételeket biztosít a megbízható Microsoft-szolgáltatásokhoz. További információt a [Tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)című rész Kivételek című szakaszában talál.

Ez a cikk bemutatja, hogyan kezelheti a szabályzatot a portál és a PowerShell-metódusok használatával.  

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az Azure Portalon keresztül kétféleképpen adhat hozzá szabályzatot. 

* [Az Azure Portal listanézete](#azure-portal-list-view)
* [Az Azure Portal kódnézete](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Az Azure Portal listanézete

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Az Azure Portalon keresse meg és válassza ki a tárfiókot. 

3. A **Blob Service csoportban**válassza **az életciklus-kezelés** lehetőséget a szabályok megtekintéséhez vagy módosításához.

4. Válassza a **Lista nézet** lapot.

5. Válassza **a Szabály hozzáadása lehetőséget,** majd töltse ki a **Műveletkészlet** űrlapmezőit. A következő példában a blobok átkerülnek a hűvös tárolóba, ha 30 napig nem módosultak.

   ![Életciklus-kezelési művelet készletlapja az Azure Portalon](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Válassza **a Szűrőkészlet** lehetőséget a választható szűrő hozzáadásához. Ezután válassza a **Tallózás** gombot, ha meg szeretne adni egy tárolót és mappát a szűréshez.

   ![Életciklus-kezelés szűrőkészletlapja az Azure Portalon](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. A házirend-beállítások áttekintéséhez válassza a **Véleményezés + hozzáadás** lehetőséget.

9. Az új házirend hozzáadásához válassza a **Hozzáadás** lehetőséget.

#### <a name="azure-portal-code-view"></a>Az Azure Portal kódnézete
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Az Azure Portalon keresse meg és válassza ki a tárfiókot.

3. A **Blob Service csoportban**válassza **az életciklus-kezelés** lehetőséget a szabályzat megtekintéséhez vagy módosításához.

4. A következő JSON egy példa egy olyan házirendre, amely beilleszthető a **Kódnézet** lapra.

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

6. Erről a JSON-példáról további információt a [Szabályzat](#policy) és [szabályok](#rules) című szakaszban talál.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A következő PowerShell-parancsfájl segítségével hozzáadhat egy szabályzatot a tárfiókhoz. A `$rgname` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a tárfiók nevével.

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

# <a name="template"></a>[Sablon](#tab/template)

Az azure Resource Manager-sablonok használatával megadhatja az életciklus-kezelést. Az alábbiakban egy RA-GRS GPv2 tárfiók életciklus-kezelési házirenddel üzembe helyezéséhez egy mintasablont olvashat.

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

---

## <a name="policy"></a>Szabályzat

Az életciklus-kezelési házirend egy JSON-dokumentumban lévő szabályok gyűjteménye:

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
| `rules`        | Szabályobjektumok tömbje | A házirendben legalább egy szabály szükséges. Egy házirendben legfeljebb 100 szabályt határozhat meg.|

A házirenden belüli minden szabálynak több paramétere van:

| Paraméter neve | Paraméter típusa | Megjegyzések | Kötelező |
|----------------|----------------|-------|----------|
| `name`         | Sztring |A szabálynév legfeljebb 256 alfanumerikus karaktert tartalmazhat. A szabály neve nem i.  Egyedinek kell lennie egy házirenden belül. | True (Igaz) |
| `enabled`      | Logikai | Egy választható logikai érték, amely lehetővé teszi egy szabály ideiglenes letiltását. Az alapértelmezett érték akkor igaz, ha nincs beállítva. | False (Hamis) | 
| `type`         | Felsoracska érték | Az aktuális érvényes `Lifecycle`típus a. | True (Igaz) |
| `definition`   | Az életciklus-szabályt meghatározó objektum | Minden definíció egy szűrőkészletből és egy műveletkészletből áll. | True (Igaz) |

## <a name="rules"></a>Szabályok

Minden szabálydefiníció tartalmaz egy szűrőkészletet és egy műveletkészletet. A [szűrőkészlet](#rule-filters) korlátozza a szabályműveleteket a tárolón vagy objektumneveken belüli objektumok bizonyos készletére. A [műveletkészlet](#rule-actions) alkalmazza a réteget, vagy törli a műveleteket a szűrt objektumkészletre.

### <a name="sample-rule"></a>Mintaszabály

A következő mintaszabály szűri a fiókot, `container1` hogy `foo`futtassa a műveleteket a benne lévő objektumokon, és kezdje a alkalmazással.  

>[!NOTE]
>Az életciklus-kezelés csak a blokkblob-típust támogatja.  

- Rétegblob a ritka elérésű szinthez 30 nappal az utolsó módosítás után
- Rétegblob az archiválási szinthez 90 nappal az utolsó módosítás után
- Blob törlése 2555 nap (hét év) az utolsó módosítás után
- Blob-pillanatképek törlése 90 nappal a pillanatkép létrehozása után

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

### <a name="rule-filters"></a>Szabályszűrők

A szűrők a szabályműveleteket a tárfiókon belüli blobok egy részhalmazára korlátozzák. Ha egynél több szűrő van `AND` definiálva, egy logikai szűrő fut az összes szűrőn.

A szűrők a következők:

| Szűrő neve | Szűrő típusa | Megjegyzések | Kötelező |
|-------------|-------------|-------|-------------|
| blobTypes   | Előre definiált felsoraértékek tömbje. | A jelenlegi kiadás `blockBlob`támogatja . | Igen |
| előtagMatch | Az előtagok karakterláncainak tömbje. Minden szabály legfeljebb 10 előtagot definiálhat. Az előtag karakterláncának tárolónévvel kell kezdődnie. Ha például egy szabály összes blobjának `https://myaccount.blob.core.windows.net/container1/foo/...` megfelelően szeretné megegyezni, a Match előtag a `container1/foo`. | Ha nem adja meg a prefixMatch, a szabály a tárfiókon belüli összes blobra vonatkozik.  | Nem |

### <a name="rule-actions"></a>Szabályműveletek

A műveletek a szűrt blobokra vonatkoznak, ha a futtatási feltétel teljesül.

Az életciklus-kezelés támogatja a blobok rétegezését és törlését, valamint a blob-pillanatképek törlését. Definiáljon legalább egy műveletet minden egyes szabályhoz a blobokon vagy a blobpillanatképeken.

| Műveletek        | Alap blob                                   | Pillanatkép      |
|---------------|---------------------------------------------|---------------|
| tierToCool között    | Támogatási blobok jelenleg a gyakori elérésű szinten         | Nem támogatott |
| tierToArchive (réteg– Archívum | Támogatási blobok jelenleg a gyakori vagy ritka elérésű szinten | Nem támogatott |
| delete        | Támogatott                                   | Támogatott     |

>[!NOTE]
>Ha egynél több műveletet definiál ugyanazon a blobon, az életciklus-kezelés a legköltségesebb műveletet alkalmazza a blobra. Például a `delete` cselekvés olcsóbb, `tierToArchive`mint a cselekvés . A `tierToArchive` cselekvés olcsóbb, `tierToCool`mint a cselekvés.

A futtatási feltételek az életkoron alapulnak. Az alapblobok az utolsó módosított időt használják az életkor nyomon követésére, a blob-pillanatképek pedig a pillanatkép létrehozásának idejét használják az életkor nyomon követéséhez.

| Művelet futtatási feltétele             | Feltétel értéke                          | Leírás                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | Egész érték, amely az életkort jelzi napokban | Az alapblob-műveletek feltétele     |
| daysAfterCreationGreaterThan     | Egész érték, amely az életkort jelzi napokban | A blob pillanatkép-műveletek feltétele |

## <a name="examples"></a>Példák

Az alábbi példák bemutatják, hogyan lehet kezelni a gyakori forgatókönyveket az életciklus-házirend szabályokkal.

### <a name="move-aging-data-to-a-cooler-tier"></a>Az elévülési adatok áthelyezése hűvösebb szintre

Ez a példa bemutatja, hogyan `container1/foo` lehet `container2/bar`áttűnési blokk blobok előtaggal vagy. A házirend áttűnések blobok, amelyek nem módosították a több mint 30 nap a tárhely hűtésére, és a blobok nem módosított 90 nap az archív szintre:

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

### <a name="archive-data-after-ingest"></a>Adatok archiválása betöltés után

Egyes adatok tétlenmarad a felhőben, és ritkán, ha valaha is elérhető egyszer tárolt. A következő életciklus-szabályzat úgy van konfigurálva, hogy archiválja az adatokat röviddel a feldolgozás után. Ez a példa áttűnések blokk `archivecontainer` blobok a tárolón belüli tárfiókban egy archív rétegbe. Az átmenet úgy valósul meg, hogy a blobokra 0 nappal az utolsó módosítás után lép:

> [!NOTE] 
> A hatékonyabb hatékonyság érdekében ajánlott a blobok feltöltése közvetlenül az archív szint. Használhatja az x-ms-acess-tier fejléc [putblob](https://docs.microsoft.com/rest/api/storageservices/put-blob) vagy [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) REST 2018-11-09-es és újabb vagy a legújabb blob storage ügyfélkönyvtárak. 

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

### <a name="expire-data-based-on-age"></a>Lejárati adatok életkor alapján

Egyes adatok várhatóan napokkal vagy hónapokkal a létrehozás után járnak le. Az életciklus-kezelési házirendet beállíthatja úgy, hogy az adatok az adatok kora alapján történő törlésével lejárjon. A következő példa egy szabályzatot mutat be, amely törli az összes 365 napnál régebbi blokkblobokat.

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

### <a name="delete-old-snapshots"></a>Régi pillanatképek törlése

A teljes élettartama során rendszeresen módosított és elérhető adatok esetében a pillanatképek et gyakran használják az adatok régebbi verzióinak nyomon követésére. Létrehozhat egy szabályzatot, amely törli a régi pillanatképeket a pillanatkép kora alapján. A pillanatkép korát a pillanatkép létrehozási idejének kiértékelése határozza meg. Ez a házirend-szabály törli a `activedata` blokkblob-pillanatképeket a tárolóban, amelyek 90 vagy régebbiek a pillanatkép létrehozása után.

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

**Létrehoztam egy új házirendet, miért nem futnak azonnal a műveletek?**  
A platform naponta egyszer futtatja az életciklus-szabályzatot. A házirend konfigurálása után egyes műveletek első futtatása akár 24 órát is igénybe vehet.  

**Ha frissítek egy meglévő szabályzatot, mennyi ideig tart a műveletek futtatása?**  
A frissített szabályzat életbe léptetése akár 24 órát is igénybe vesz. A házirend érvénybe lépése után akár 24 órát is igénybe vehet a műveletek futtatása. Ezért a szakpolitikai intézkedések végrehajtásához akár 48 óra is igénybe vehet.   

**Manuálisan rehidratáltam egy archivált blobot, hogyan akadályozhatom meg, hogy ideiglenesen visszakerüljön az archív szintre?**  
Amikor egy blob egyik hozzáférési szintről a másikra kerül áthelyezésre, az utolsó módosítási idő nem változik. Ha manuálisan rehidratálja az archivált blobot a gyakori elérésű szintre, az életciklus-kezelési motor visszahelyezi az archiválási szintre. Tiltsa le a blobot ideiglenesen érintő szabályt, hogy megakadályozza az archiválását. Engedélyezze újra a szabályt, amikor a blob biztonságosan áthelyezhető az archív szintre. A blob ot is másolhatja egy másik helyre, ha tartósan a gyakori vagy ritka elérésű szintben kell maradnia.

## <a name="next-steps"></a>További lépések

További információ az adatok véletlen törlés utáni helyreállításáról:

- [Az Azure Storage-blobok helyreállítható törlése](../blobs/storage-blob-soft-delete.md)
