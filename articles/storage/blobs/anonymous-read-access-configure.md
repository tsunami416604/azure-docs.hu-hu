---
title: Névtelen nyilvános olvasási hozzáférés konfigurálása a tárolók és a Blobok számára
titleSuffix: Azure Storage
description: Megtudhatja, hogyan engedélyezheti vagy tilthatja a névtelen hozzáférést a Storage-fiók blob-adateléréséhez. Állítsa be a tároló nyilvános hozzáférési beállítását, hogy a tárolók és Blobok elérhetők legyenek a névtelen hozzáféréshez.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/23/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: daf4eb4492f723b049dc62a16351e04ffc252337
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289244"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Névtelen nyilvános olvasási hozzáférés konfigurálása a tárolók és a Blobok számára

Az Azure Storage támogatja az opcionális névtelen nyilvános olvasási hozzáférést a tárolók és a Blobok számára. Alapértelmezés szerint a névtelen hozzáférés nem engedélyezett az adataihoz. Ha explicit módon engedélyezi a névtelen hozzáférést, a tárolóra és a blobokra vonatkozó összes kérést engedélyezni kell. Ha a tároló nyilvános hozzáférési szintjének beállítását a névtelen hozzáférés engedélyezésére konfigurálja, az ügyfelek a kérelem engedélyezése nélkül is olvashatják az adott tárolóban tárolt adatolvasást.

> [!WARNING]
> Ha egy tároló nyilvános hozzáférésre van konfigurálva, akkor bármely ügyfél beolvashatja az adott tárolóban lévő összes ügyfelet. A nyilvános hozzáférés potenciális biztonsági kockázatot jelent, így ha a forgatókönyv nem igényli azt, a Microsoft azt javasolja, hogy ne engedélyezze azt a Storage-fiók esetében. További információ: a [Névtelen nyilvános olvasási hozzáférés megakadályozása a tárolók és a Blobok](anonymous-read-access-prevent.md)számára.

Ez a cikk a tárolók és a Blobok névtelen nyilvános olvasási hozzáférésének konfigurálását ismerteti. További információ a blob-adatok ügyfélalkalmazások általi névtelen eléréséről: a [nyilvános tárolók és Blobok névtelenül](anonymous-read-access-client.md)való elérése a .net-tel.

## <a name="about-anonymous-public-read-access"></a>Névtelen nyilvános olvasási hozzáférés

Alapértelmezés szerint az adataihoz való nyilvános hozzáférés mindig tiltott. A nyilvános hozzáférést két külön beállítás befolyásolja:

1. **A Storage-fiókhoz való nyilvános hozzáférés engedélyezése.** Alapértelmezés szerint a Storage-fiókok lehetővé teszik, hogy a felhasználók a megfelelő engedélyekkel engedélyezzék a tárolóhoz való nyilvános hozzáférést. A blob-információk nyilvános hozzáféréshez nem érhetők el, kivéve, ha a felhasználó a további lépésekkel explicit módon konfigurálja a tároló nyilvános hozzáférési beállításait.
1. **Konfigurálja a tároló nyilvános hozzáférési beállításait.** Alapértelmezés szerint a tároló nyilvános hozzáférésének beállítása le van tiltva, ami azt jelenti, hogy a tárolóhoz vagy annak minden kérelméhez engedély szükséges. A megfelelő engedélyekkel rendelkező felhasználók úgy módosíthatják a tároló nyilvános hozzáférési beállításait, hogy csak akkor engedélyezze a névtelen hozzáférést, ha a Storage-fiókhoz névtelen hozzáférés van engedélyezve.

Az alábbi táblázat összefoglalja, hogy mindkét beállítás hogyan befolyásolja a tárolók nyilvános elérését.

| Nyilvános hozzáférés beállítása | A nyilvános hozzáférés le van tiltva egy tárolónál (alapértelmezett beállítás) | A tárolók nyilvános hozzáférése tárolóra van állítva | A tárolók nyilvános hozzáférése Blobra van állítva |
|--|--|--|--|
| A Storage-fiókhoz nem engedélyezett a nyilvános hozzáférés | Nincs nyilvános hozzáférés a Storage-fiókban található egyik tárolóhoz sem. | Nincs nyilvános hozzáférés a Storage-fiókban található egyik tárolóhoz sem. A Storage-fiók beállítása felülbírálja a tároló beállítását. | Nincs nyilvános hozzáférés a Storage-fiókban található egyik tárolóhoz sem. A Storage-fiók beállítása felülbírálja a tároló beállítását. |
| A Storage-fiók számára engedélyezett a nyilvános hozzáférés (alapértelmezett beállítás) | Nincs nyilvános hozzáférés ehhez a tárolóhoz (alapértelmezett konfiguráció). | A nyilvános hozzáférés engedélyezett a tároló és a Blobok számára. | A nyilvános hozzáférés engedélyezve van a tárolóban lévő Blobok számára, a tárolóban azonban nem. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Nyilvános olvasási hozzáférés engedélyezése vagy letiltása egy Storage-fiókhoz

Alapértelmezés szerint a Storage-fiók úgy van konfigurálva, hogy lehetővé tegye a felhasználók számára a megfelelő engedélyeket a tárolóhoz való nyilvános hozzáférés engedélyezéséhez. Ha a nyilvános hozzáférés engedélyezve van, a megfelelő engedélyekkel rendelkező felhasználók módosíthatják a tároló nyilvános hozzáférési beállításait, hogy a névtelen hozzáférést engedélyezzék a tárolóban lévők számára. A blob-információk soha nem érhetők el a nyilvános hozzáféréshez, kivéve, ha a felhasználó a további lépéseket a tároló nyilvános hozzáférési beállításának explicit módon történő konfigurálásához.

Ne feledje, hogy a tárolóhoz való nyilvános hozzáférés alapértelmezés szerint ki van kapcsolva, és explicit módon be kell állítani a névtelen kérelmek engedélyezését. A Storage-fiók beállításaitól függetlenül az adatai soha nem lesznek elérhetők nyilvános hozzáférésre, kivéve, ha egy megfelelő engedélyekkel rendelkező felhasználó ezt a további lépést teszi a nyilvános hozzáférés engedélyezésére a tárolón.

A Storage-fiókhoz való nyilvános hozzáférés letiltása megakadályozza a fiókban lévő összes tárolóhoz és blobhoz való névtelen hozzáférést. Ha a fiókhoz nem engedélyezett a nyilvános hozzáférés, nem lehet konfigurálni a tároló nyilvános hozzáférési beállításait a névtelen hozzáférés engedélyezéséhez. A fokozott biztonság érdekében a Microsoft azt javasolja, hogy a Storage-fiókok számára ne engedélyezze a nyilvános hozzáférést, kivéve, ha a forgatókönyv megköveteli, hogy a felhasználók névtelenül férhessenek hozzá a blob-erőforrásokhoz.

> [!IMPORTANT]
> A Storage-fiókhoz való nyilvános hozzáférés letiltása felülbírálja a Storage-fiókban lévő összes tároló nyilvános hozzáférési beállításait. Ha a Storage-fiókhoz való nyilvános hozzáférés nem engedélyezett, a fiókra vonatkozó jövőbeli névtelen kérelmek sikertelenek lesznek. A beállítás módosítása előtt ügyeljen arra, hogy Ismerje meg, hogy milyen hatással van az ügyfélalkalmazások névtelenül a Storage-fiókban lévő adatokhoz való hozzáférésre. További információ: a [Névtelen nyilvános olvasási hozzáférés megakadályozása a tárolók és a Blobok](anonymous-read-access-prevent.md)számára.

A Storage-fiókhoz való nyilvános hozzáférés engedélyezéséhez vagy letiltásához használja a Azure Portal vagy az Azure CLI-t a fiók **blobPublicAccess** tulajdonságának konfigurálásához. Ez a tulajdonság minden olyan Storage-fiókhoz elérhető, amely a Azure Resource Manager telepítési modellel lett létrehozva. További információ: a [Storage-fiók áttekintése](../common/storage-account-overview.md).

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A Azure Portal a Storage-fiókok nyilvános hozzáférésének engedélyezéséhez vagy letiltásához kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. Keresse meg a **konfigurációs** beállítást a **Beállítások**területen.
1. A **blob nyilvános hozzáférésének** beállítása **engedélyezett** vagy **Letiltva**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="A fiókhoz tartozó nyilvános blob-hozzáférés engedélyezését és letiltását bemutató képernyőkép":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel való nyilvános hozzáférés engedélyezéséhez vagy letiltásához először kérje le a Storage-fiók erőforrás-AZONOSÍTÓját az az [Resource show](/cli/azure/resource#az-resource-show) parancs meghívásával. Ezután hívja meg az az [Resource Update](/cli/azure/resource#az-resource-update) parancsot a Storage-fiók **allowBlobPublicAccess** tulajdonságának beállításához. A nyilvános hozzáférés engedélyezéséhez állítsa a **allowBlobPublicAccess** tulajdonságot True (igaz) értékre. a tiltáshoz állítsa **hamis**értékre.

Az alábbi példa leválasztja a nyilvános blob-hozzáférést a Storage-fiókhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
storage_account_id=$(az resource show \
    --name anonpublicaccess \
    --resource-group storagesamples-rg \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowBlobPublicAccess=false
    ```
```

---

> [!NOTE]
> A Storage-fiókhoz való nyilvános hozzáférés letiltása nem befolyásolja az adott Storage-fiókban üzemeltetett statikus webhelyeket. A **$web** tároló mindig nyilvánosan elérhető.

## <a name="check-whether-public-access-is-allowed-for-a-storage-account"></a>Annak megállapítása, hogy engedélyezett-e a nyilvános hozzáférés a Storage-fiókhoz

A **allowBlobPublicAccess** tulajdonság értékének beszerzésével ellenőrizhető, hogy engedélyezett-e a nyilvános hozzáférés a Storage-fiókhoz. Ha egy nagy számú Storage-fiók esetében szeretné ezt a tulajdonságot egyszerre használni, használja az Azure Resource Graph Explorert.

> [!IMPORTANT]
> A **allowBlobPublicAccess** tulajdonság alapértelmezés szerint nincs beállítva, és nem ad vissza értéket, amíg explicit módon be nem állítja azt. A Storage-fiók lehetővé teszi a nyilvános hozzáférést, ha a tulajdonság értéke **Null** vagy **igaz**.

### <a name="check-whether-public-access-is-allowed-for-a-single-storage-account"></a>Annak megállapítása, hogy engedélyezett-e a nyilvános hozzáférés egyetlen Storage-fiókhoz

Annak megállapításához, hogy a nyilvános hozzáférés engedélyezett-e egyetlen Storage-fiókhoz az Azure CLI használatával, hívja meg az az [Resource show](/cli/azure/resource#az-resource-show) parancsot és a **allowBlobPublicAccess** tulajdonság lekérdezését:

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-whether-public-access-is-allowed-for-a-set-of-storage-accounts"></a>Annak megállapítása, hogy engedélyezett-e a nyilvános hozzáférés a Storage-fiókok készletéhez

Annak megállapításához, hogy a nyilvános hozzáférés engedélyezett-e az optimális teljesítményt biztosító Storage-fiókok között, használhatja az Azure Resource Graph Explorert a Azure Portal. Ha többet szeretne megtudni az Erőforrásgrafikon Explorer használatáról, tekintse meg a gyors útmutató [: az első Resource Graph-lekérdezés futtatása az Azure Resource Graph Explorerben](/azure/governance/resource-graph/first-query-portal).

A következő lekérdezés futtatása az Erőforrásgrafikon Explorerben a Storage-fiókok listáját adja vissza, és megjeleníti az egyes fiókok **allowBlobPublicAccess** tulajdonságának értékét:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>Egy tároló nyilvános hozzáférési szintjének beállítása

Ahhoz, hogy a névtelen felhasználók számára olvasási hozzáférést adjon egy tárolóhoz és a blobokhoz, először engedélyezze a nyilvános hozzáférést a Storage-fiókhoz, majd állítsa be a tároló nyilvános hozzáférési szintjét. Ha a Storage-fiókhoz való nyilvános hozzáférés megtagadva, nem konfigurálhatja a tárolók nyilvános hozzáférését.

Ha a nyilvános hozzáférés engedélyezett a Storage-fiókhoz, a következő engedélyekkel konfigurálhat egy tárolót:

- **Nincs nyilvános olvasási hozzáférés:** A tároló és a hozzá tartozó Blobok csak jogosult kéréssel érhetők el. Ez a beállítás az összes új tároló alapértelmezett értéke.
- **Nyilvános olvasási hozzáférés csak Blobok esetén:** A tárolóban lévő blobokat névtelen kérések is elolvashatják, de a tárolók nem érhetők el névtelenül. A névtelen ügyfelek nem tudják enumerálni a tárolóban lévő blobokat.
- **Nyilvános olvasási hozzáférés a tárolóhoz és a blobokhoz:** A tároló-és blob-adatokat névtelen kérések is elolvashatják, kivéve a tároló engedélyeinek beállításait és a tárolók metaadatait. Az ügyfelek a tárolóban lévő blobokat névtelen kérelem alapján enumerálják, de a tárolóban lévő tárolók nem sorolhatók fel.

Az egyes Blobok nyilvános hozzáférési szintje nem módosítható. A nyilvános hozzáférési szint csak a tároló szintjén állítható be.

A tároló nyilvános hozzáférési szintjének beállításához használja a Azure Portal vagy az Azure CLI-t. Megadhatja a tároló nyilvános hozzáférési szintjét a tároló létrehozásakor, vagy frissítheti a beállítást egy meglévő tárolón.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A Azure Portal egy vagy több meglévő tárolójának nyilvános hozzáférési szintjének frissítéséhez kövesse az alábbi lépéseket:

1. A Azure Portalban navigáljon a Storage-fiók áttekintéséhez.
1. A menü panel **blob Service** területén válassza a **tárolók**lehetőséget.
1. Válassza ki azokat a tárolókat, amelyekhez a nyilvános hozzáférési szintet be szeretné állítani.
1. A **hozzáférési szint módosítása** gomb használatával jelenítse meg a nyilvános hozzáférési beállításokat.
1. Válassza ki a **nyilvános hozzáférési szint** legördülő menüből a kívánt nyilvános hozzáférési szintet, majd az OK gombra kattintva alkalmazza a módosítást a kijelölt tárolók elemre.

    ![A nyilvános hozzáférési szint megadását bemutató képernyőkép a portálon](./media/anonymous-read-access-configure/configure-public-access-container.png)

Ha a Storage-fiókhoz a nyilvános hozzáférés nem engedélyezett, a tároló nyilvános hozzáférési szintje nem állítható be. Ha megkísérli a tároló nyilvános hozzáférési szintjének beállítását, láthatja, hogy a beállítás le van tiltva, mert a fiókhoz a nyilvános hozzáférés nem engedélyezett.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Képernyőfelvétel, amely azt mutatja, hogy a tároló nyilvános hozzáférési szintje le van tiltva, ha a nyilvános hozzáférés nem engedélyezett":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha egy vagy több tárolóhoz az Azure CLI-vel szeretné frissíteni a nyilvános hozzáférési szintet, hívja meg az az [Storage Container set Permission](/cli/azure/storage/container#az-storage-container-set-permission) parancsot. Engedélyezze a műveletet a fiók kulcsa, a kapcsolati karakterlánc vagy a közös hozzáférésű aláírás (SAS) megadásával. A tároló nyilvános hozzáférési szintjét [beállító tároló ACL](/rest/api/storageservices/set-container-acl) -művelete nem támogatja az Azure ad-vel való engedélyezést. További információ: [a blob-és üzenetsor-adatműveletek meghívására vonatkozó engedélyek](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

A következő példa egy tároló nyilvános hozzáférési beállítását állítja be a tárolóhoz és a blobokhoz való névtelen hozzáférés engedélyezéséhez. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key
```

Ha a Storage-fiókhoz a nyilvános hozzáférés nem engedélyezett, a tároló nyilvános hozzáférési szintje nem állítható be. Ha megkísérli a tároló nyilvános hozzáférési szintjének beállítását, akkor hiba történik, ami azt jelzi, hogy a nyilvános hozzáférés nem engedélyezett a Storage-fiókban.

---

## <a name="check-the-container-public-access-setting"></a>A tároló nyilvános hozzáférési beállításának bejelölése

Egy vagy több tároló nyilvános hozzáférési beállításának vizsgálatához használhatja a Azure Portal, a PowerShell, az Azure CLI, az egyik Azure Storage ügyféloldali kódtárat vagy az Azure Storage erőforrás-szolgáltatót. A következő fejezetek néhány példát mutatnak be.  

### <a name="check-the-public-access-setting-for-a-single-container"></a>Egyetlen tároló nyilvános hozzáférésének beállítása

Az Azure CLI-vel rendelkező tárolók nyilvános hozzáférési szintjének lekéréséhez hívja meg az az [Storage Container show Permission](/cli/azure/storage/container#az-storage-container-show-permission) parancsot. Engedélyezze a műveletet a fiók kulcsa, a kapcsolati karakterlánc vagy a közös hozzáférésű aláírás (SAS) megadásával. A tároló nyilvános hozzáférési szintjének [beolvasására szolgáló tároló ACL](/rest/api/storageservices/get-container-acl) -művelete nem támogatja az Azure ad-vel való engedélyezést. További információ: [a blob-és üzenetsor-adatműveletek meghívására vonatkozó engedélyek](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

A következő példa egy tároló nyilvános hozzáférési beállítását olvassa be. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key>
    --auth-mode key
```

### <a name="check-the-public-access-setting-for-a-set-of-containers"></a>A nyilvános hozzáférés beállítása tárolók készletéhez

A tárolók listázásával és a nyilvános hozzáférési beállítások ellenőrzésével ellenőrizhető, hogy egy vagy több Storage-fiók mely tárolókat konfigurálja nyilvános hozzáférésre. Ez a megközelítés praktikus megoldás, ha a Storage-fiók nem tartalmaz nagy mennyiségű tárolót, vagy ha kis számú Storage-fiókban ellenőrzi a beállítást. A teljesítmény azonban csökkenhet, ha nagy mennyiségű tárolót próbál számba venni.

A következő példa a PowerShell használatával kéri le a nyilvános hozzáférési beállítást egy Storage-fiókban lévő összes tárolóhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>További lépések

- [Tárolók és Blobok névtelen nyilvános olvasási hozzáférésének tiltása](anonymous-read-access-prevent.md)
- [Nyilvános tárolók és Blobok elérése névtelenül a .NET-tel](anonymous-read-access-client.md)
- [Hozzáférés engedélyezése az Azure Storage-hoz](../common/storage-auth.md)
