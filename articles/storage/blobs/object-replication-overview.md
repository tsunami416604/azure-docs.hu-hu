---
title: Objektum-replikáció áttekintése (előzetes verzió)
titleSuffix: Azure Storage
description: Az objektum-replikáció (előzetes verzió) aszinkron módon másolja a blokk blobokat egy forrás Storage-fiók és egy cél fiók között. Az objektumok replikálásával csökkentheti az olvasási kérelmek késését, növelheti a számítási feladatok hatékonyságát, optimalizálhatja az adatterjesztést, és csökkentheti a költségeket.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: abec9811cd407c1fab91cfb60412aabdd969690d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036981"
---
# <a name="object-replication-for-block-blobs-preview"></a>Objektum-replikálás blokk-Blobok számára (előzetes verzió)

Az objektum-replikáció (előzetes verzió) aszinkron módon másolja a blokk blobokat egy forrás Storage-fiók és egy cél fiók között. Az objektumok replikálása által támogatott egyes forgatókönyvek a következők:

- **Minimalizálja a késést.** Az objektumok replikálása csökkentheti az olvasási kérések késését azáltal, hogy lehetővé teszi az ügyfelek számára, hogy a szorosabb fizikai közelségben lévő régióból származó adatok felhasználását használják.
- **A számítási feladatok hatékonyságának növelése.** Az objektumok replikálásával a számítási feladatok különböző régiókban is feldolgozhatják ugyanezeket a blokk-blobokat.
- **Az adateloszlás optimalizálása.** Egyetlen helyen feldolgozhatja vagy elemezheti az adatforrásokat, majd csak az eredményeket replikálhatja további régiókba.
- **A költségek optimalizálása.** Az adatok replikálása után csökkentheti a költségeket az archiválási szintre való áthelyezéssel a életciklus-kezelési házirendek használatával.

Az alábbi ábra azt mutatja be, hogy az objektum-replikáció hogyan replikálja a blokk-blobokat az egyik régióban található forrás-Storage-fiókból két különböző régióban.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Az objektum-replikáció működését bemutató ábra":::

Az objektumok replikálásának konfigurálásáról az objektum- [replikáció konfigurálása (előzetes verzió)](object-replication-configure.md)című cikk nyújt tájékoztatást.

## <a name="object-replication-policies-and-rules"></a>Objektum-replikációs házirendek és szabályok

Az objektumok replikálásának konfigurálásakor létre kell hoznia egy replikációs házirendet, amely megadja a forrás Storage-fiókot és a célként megadott fiókot. A replikációs házirend egy vagy több olyan szabályt tartalmaz, amely a forrás tárolót és a célhelyet adja meg, és jelzi, hogy a rendszer melyik blokk blobokat replikálja a forrás tárolóban.

Az objektumok replikálásának konfigurálása után az Azure Storage rendszeresen ellenőrzi a forrás fiók változási csatornáját, és aszinkron módon replikálja az írási vagy törlési műveleteket a célhelyre. A replikációs késés a replikált blokk blob méretétől függ.

> [!IMPORTANT]
> Mivel a blob-adatblokkokat a rendszer aszinkron módon replikálja, a forrás-és a célcím nem azonnal szinkronizálva van. Jelenleg nincs SLA arra vonatkozóan, hogy mennyi időt vesz igénybe az adatreplikálás a célkiszolgálóra.

### <a name="replications-policies"></a>Replikációs házirendek

Az objektumok replikálásának konfigurálásakor a rendszer az Azure Storage erőforrás-szolgáltatón keresztül létrehoz egy replikációs házirendet a forrás és a cél fiókon is. A replikációs házirendet egy házirend-azonosító azonosítja. A forrás-és a célhelyen lévő házirendnek ugyanazzal a házirend-AZONOSÍTÓval kell rendelkeznie ahhoz, hogy a replikálás megtörténjen.

A Storage-fiók legfeljebb két célobjektum forrásaként használható. A cél fiók legfeljebb két forrásoldali fiókkal rendelkezhet. A forrás- és célfiókok mind lehetnek eltérő régiókban. Külön replikációs házirendeket konfigurálhat az egyes célszámítógépekre történő adatreplikáláshoz.

### <a name="replication-rules"></a>Replikációs szabályok

A replikációs szabályok azt határozzák meg, hogy az Azure Storage hogyan replikálja a blobokat a forrás tárolóból a célként megadott tárolóba. Az egyes replikációs házirendekhez legfeljebb 10 replikációs szabályt adhat meg. Mindegyik szabály egyetlen forrás-és cél tárolót határoz meg, és minden forrás és cél tároló csak egy szabályban használható.

A replikációs szabályok létrehozásakor alapértelmezés szerint csak a forrás-tárolóba felvenni kívánt új blokk-blobokat másolja a rendszer. Azt is megadhatja, hogy az új és a meglévő blokk blobokat is másolja, vagy megadhat egy egyéni másolási hatókört, amely a megadott időpontból létrehozott blokk-blobokat másolja.

Egy vagy több szűrőt is megadhat egy replikációs szabály részeként a blokk Blobok előtag alapján történő szűréséhez. Egy előtag megadásakor a rendszer csak a forrás tárolóban lévő előtaggal egyező blobokat másolja a célhelyre.

A forrás-és a cél tárolóknak is léteznie kell, mielőtt megadhatja őket egy szabályban. A replikációs szabályzat létrehozása után a céltároló csak olvashatóvá válik. A céltárolóba történő írásra tett kísérlet sikertelen lesz a következő hibakóddal: 409 (ütközés). Meghívhatja azonban a [blob szint beállítása](/rest/api/storageservices/set-blob-tier) műveletet a cél tárolóban lévő blobon az archív szintre való áthelyezéshez. Az archiválási szinttel kapcsolatos további információkért lásd [: Azure Blob Storage: gyors, ritka elérésű és archív hozzáférési szintek](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="about-the-preview"></a>Az előzetes verzió ismertetése

Az objektum-replikáció csak az általános célú v2-alapú Storage-fiókok esetében támogatott. Az objektum-replikáció az előzetes verzióban a következő régiókban érhető el:

- Közép-Franciaország
- Kelet-Kanada
- Közép-Kanada
- USA 2. keleti régiója
- USA középső régiója

Az objektum-replikáció használatához a forrás-és a cél fiókoknak is szerepelniük kell ezen régiók egyikében. A fiókok lehetnek két különböző régióban.

Az előzetes verzió ideje alatt a Storage-fiókok közötti replikálás nem jár további költségekkel.

> [!IMPORTANT]
> Az objektum-replikáció előzetes verziója csak nem éles használatra készült. Az üzemi szolgáltatási szintű szerződések (SLA-kat) jelenleg nem érhetők el.

### <a name="prerequisites-for-object-replication"></a>Az objektumreplikáció előfeltételei

Az objektum-replikációhoz a következő Azure Storage-funkciókra van szükség: 
- [Csatorna módosítása](storage-blob-change-feed.md)
- [Verziókezelés](versioning-overview.md)

Az objektumok replikálásának konfigurálása előtt engedélyezze az előfeltételeket. A módosítási csatornát engedélyezni kell a forrásoldali fiókon, és a blob verziószámozását is engedélyezni kell a forrás-és a cél fiókban. A szolgáltatások engedélyezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

- [A módosítási csatorna engedélyezése és letiltása](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [BLOB-verziószámozás engedélyezése és kezelése](versioning-enable.md)

Győződjön meg arról, hogy az engedélyezése előtt regisztrálja az adatmódosítási hírcsatorna és a blob verziószámozási előzetes verzióját.

A módosítási hírcsatorna és a blob verziószámozásának engedélyezése további költségeket eredményezhet. További részletekért tekintse meg az [Azure Storage díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/storage/).

### <a name="register-for-the-preview"></a>Regisztráljon az előzetes verzióra

Az objektum-replikálás előzetes verzióját a PowerShell vagy az Azure CLI használatával lehet regisztrálni. Ha még nem tette meg, győződjön meg arról, hogy a módosítási hírcsatorna és a blob verziószámozási előnézetei is regisztrálva vannak.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az előnézet PowerShell-lel való regisztrálásához futtassa a következő parancsokat:

```powershell
# Register for the object replication preview
Register-AzProviderFeature -FeatureName AllowObjectReplication -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha regisztrálni szeretne az előzetes verzióra az Azure CLI-vel, futtassa a következő parancsokat:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowObjectReplication
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>Regisztráció állapotának bejelölése

A regisztrációs kérések állapotát a PowerShell vagy az Azure CLI használatával tekintheti meg.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A regisztrációs kérések állapotának a PowerShell használatával történő megtekintéséhez futtassa a következő parancsokat:

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowObjectReplication

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A regisztrációs kérések állapotának az Azure CLI használatával történő megtekintéséhez futtassa a következő parancsokat:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowObjectReplication')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="ask-questions-or-provide-feedback"></a>Kérdések feltevése vagy visszajelzés küldése

Ha kérdéseket szeretne feltenni az objektum-replikálás előzetes verziójával kapcsolatban, vagy visszajelzést szeretne küldeni, forduljon a Microsofthoz AzureStorageFeedback@microsoft.com . Az Azure Storage szolgáltatással kapcsolatos ötleteket és javaslatokat mindig az [Azure Storage visszajelzési fóruma](https://feedback.azure.com/forums/217298-storage)fogadja.

## <a name="next-steps"></a>További lépések

- [Objektum-replikáció konfigurálása (előzetes verzió)](object-replication-configure.md)
- [A hírcsatorna-támogatás módosítása az Azure Blob Storage (előzetes verzió)](storage-blob-change-feed.md)
- [BLOB-verziószámozás engedélyezése és kezelése](versioning-enable.md)
