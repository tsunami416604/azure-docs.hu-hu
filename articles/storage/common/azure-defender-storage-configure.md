---
title: Az Azure Defender beállítása a Storage szolgáltatáshoz
titleSuffix: Azure Storage
description: Konfigurálja az Azure Defender for Storage-t a fiókban észlelt rendellenességek észlelése érdekében, és értesítse a fiókjához való hozzáférésre potenciálisan ártalmas kísérleteket.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: d2ad302042b277cf29b3a7b22af88b662686b3fd
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994910"
---
# <a name="configure-azure-defender-for-storage"></a>Az Azure Defender beállítása a Storage szolgáltatáshoz

Az Azure Defender for Storage egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi, hogy biztonsági szakértő vagy biztonsági figyelő rendszerek kezelése nélkül foglalkozzon a fenyegetésekkel.

A biztonsági riasztások akkor lépnek életbe, ha a tevékenységben anomáliák vannak. Ezek a biztonsági riasztások integrálva vannak [Azure Security Centerekkel](https://azure.microsoft.com/services/security-center/), és e-mailben is elküldjük az előfizetés-rendszergazdáknak, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálásával és javításával kapcsolatos ajánlásokkal együtt.

A szolgáltatás betölti az olvasási, írási és törlési kérelmek erőforrás-naplóit a blob Storage-ba, illetve Azure Files (előzetes verzió) a veszélyforrások észleléséhez. Az Azure Defender által indított riasztások vizsgálatához Storage Analytics naplózás használatával tekintheti meg a kapcsolódó tárolási tevékenységeket. További információ: a **naplózás konfigurálása** a [Storage-fiók figyelése a Azure Portalban](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Rendelkezésre állás

A Storage-hoz készült Azure Defender jelenleg blob Storage, Azure Files (előzetes verzió) és Azure Data Lake Storage Gen2 (előzetes verzió) esetén érhető el. Az Azure Defendert támogató fióktípus például az általános célú v2, a blobok és a blob Storage-fiókok. Az Azure Defender for Storage a nyilvános felhőkben és az USA kormányzati felhőkben is elérhető, de nem más szuverén vagy Azure Government Felhőbeli régiókban.

Az Azure Blob Storage API-kat és a Data Lake Storage API-kat használó Data Lake Storage támogatási tranzakciókat engedélyező hierarchikus névtereket tartalmazó fiókok. Az Azure-fájlmegosztás támogatja a tranzakciókat az SMB protokollon keresztül.

A díjszabással kapcsolatos részletekért, beleértve az ingyenes 30 napos próbaverziót is, tekintse meg a [Azure Security Center díjszabási oldalát](https://azure.microsoft.com/pricing/details/security-center/).

Az alábbi lista összefoglalja az Azure Defender for Storage szolgáltatás elérhetőségét:

- Kiadás állapota:
  - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (általánosan elérhető)
  - [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (az előzetes verzió támogatja az SMB-és Rest-tranzakciókat)
  - Azure Data Lake Storage Gen2 (előzetes verzió)
- Felhők<br>
    ✔ Kereskedelmi felhők<br>
    ✔ US Gov<br>
    ✘ China gov, egyéb gov

## <a name="set-up-azure-defender"></a>Az Azure Defender beállítása

Az Azure Defendert a következő szakaszokban ismertetett módon állíthatja be a Storage szolgáltatáshoz.

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Amikor előfizet a Azure Security Center Standard szintjére, az Azure Defender automatikusan beállítja az összes Storage-fiókját. Az Azure Defender a következő módon engedélyezhető vagy tiltható le a Storage-fiókok számára egy adott előfizetésben:

1. A [Azure Portal](https://portal.azure.com) **Azure Security Center** elindítása.
1. A főmenü **felügyelet**területén válassza a **díjszabás & beállítások**lehetőséget.
1. Válassza ki azt az előfizetést, amelyhez engedélyezni vagy letiltani szeretné az Azure Defendert.
1. Válassza az **Azure Defender** lehetőséget, hogy engedélyezze az Azure Defendert az előfizetéshez.
1. Az **Azure Defender-csomag kiválasztása erőforrás típusa**területen keresse meg a **tárolási** sort, és válassza az **engedélyezve** lehetőséget a **terv** oszlopban.
1. Mentse a módosításokat.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Képernyőfelvétel: az Azure Defender engedélyezése a Storage-ban Security Center":::

Az Azure Defender mostantól engedélyezve van az előfizetésben található összes Storage-fiókhoz.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Indítsa el a [Azure Portal](https://portal.azure.com/).
1. Nyissa meg a tárfiókot. A **Beállítások**területen válassza a **fokozott biztonság**lehetőséget.
1. Válassza **Az Azure Defender engedélyezése a tárolóhoz**lehetőséget.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Az Azure Defender Azure Storage-fiókhoz való engedélyezését bemutató képernyőkép":::

Az Azure Defender mostantól engedélyezve van ehhez a Storage-fiókhoz.

### <a name="template"></a>[Sablon](#tab/template)

Egy Azure Resource Manager sablon használatával üzembe helyezhet egy Azure Storage-fiókot az Azure Defenderrel. További információ: Storage- [fiók komplex veszélyforrások elleni védelemmel](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Egy Azure Policy használatával engedélyezheti az Azure Defender számára a Storage-fiókokat egy adott előfizetés vagy erőforráscsoport alatt.

1. Indítsa el az Azure **Policy-fogalommeghatározások** lapot.
1. Keresse meg az **Azure Defender üzembe helyezése a Storage-fiókokra** vonatkozó házirendet.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Házirend alkalmazása az Azure Defender Storage-fiókok engedélyezéséhez":::

1. Válasszon ki egy Azure-előfizetést vagy erőforráscsoportot.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Válassza ki az előfizetést vagy az erőforráscsoportot a házirend hatóköréhez ":::

1. Rendelje hozzá a szabályzatot.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Házirend kiosztása az Azure Defender tárterületének engedélyezéséhez":::

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Használjon REST API-parancsokat egy adott Storage-fiók Azure Defender-beállításának létrehozásához, frissítéséhez vagy beszerzéséhez.

- [Komplex veszélyforrások elleni védelem – létrehozás](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
- [Komplex veszélyforrások elleni védelem – Get](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Használja a következő PowerShell-parancsmagokat:

- [Komplex veszélyforrások elleni védelem engedélyezése](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
- [Komplex veszélyforrások elleni védelem](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
- [A komplex veszélyforrások elleni védelem letiltása](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Biztonsági rendellenességek megismerése

Ha a tárolási tevékenységek rendellenességeket tapasztalnak, e-mailben értesítést kap a gyanús biztonsági eseményről. Az esemény részletei a következők:

- A rendellenesség természete
- A tárfiók neve
- Az esemény időpontja
- A tárolási típus
- A lehetséges okok
- A vizsgálat lépései
- A szervizelés lépései

Az e-mail emellett tartalmazza a lehetséges okokat és az ajánlott műveleteket is a lehetséges fenyegetések kivizsgálásához és enyhítéséhez.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Azure Defender tárolási riasztási e-mail":::

Az aktuális biztonsági riasztásokat a Azure Security Center [biztonsági riasztások csempéről](../../security-center/security-center-managing-and-responding-alerts.md)tekintheti meg és kezelheti. Ha egy adott riasztásra kattint, a rendszer részletesen ismerteti az aktuális fenyegetés kivizsgálásával és a jövőbeli fenyegetésekkel kapcsolatos műveleteket.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Azure Defender tárolási riasztás":::

## <a name="security-alerts"></a>Biztonsági riasztások

A riasztásokat szokatlan és potenciálisan ártalmas kísérletek generálják a Storage-fiókok eléréséhez vagy kiaknázásához. Az Azure Storage-hoz kapcsolódó riasztások listáját az [Azure Storage-riasztások](../../security-center/alerts-reference.md#alerts-azurestorage)című témakörben tekintheti meg.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Storage-fiókok naplóiról](/rest/api/storageservices/About-Storage-Analytics-Logging)
- További információ a [Azure Security Center](../../security-center/security-center-intro.md)
