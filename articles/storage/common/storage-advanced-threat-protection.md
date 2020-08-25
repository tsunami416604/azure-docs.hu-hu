---
title: Komplex veszélyforrások elleni védelem konfigurálása
titleSuffix: Azure Storage
description: Az Azure Storage komplex veszélyforrások elleni védelmének beállításával észlelheti a fiókban észlelt rendellenességeket, és értesítést kaphat a fiókjához való hozzáférésre vonatkozó lehetséges ártalmas kísérletekről.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: e037607d1f86e6df4d3f5b12e29ba8fde447ebc9
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757931"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Komplex veszélyforrások elleni védelem konfigurálása az Azure Storage-hoz

Az Azure Storage komplex veszélyforrások elleni védelme egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi, hogy biztonsági szakértő vagy biztonsági figyelő rendszerek kezelése nélkül foglalkozzon a fenyegetésekkel.

A biztonsági riasztások akkor lépnek életbe, ha a tevékenységben anomáliák vannak. Ezek a biztonsági riasztások integrálva vannak [Azure Security Centerekkel](https://azure.microsoft.com/services/security-center/), és e-mailben is elküldjük az előfizetés-rendszergazdáknak, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálásával és javításával kapcsolatos ajánlásokkal együtt.

A szolgáltatás betölti az olvasási, írási és törlési kérelmek erőforrás-naplóit a blob Storage-ba, illetve Azure Files (előzetes verzió) a veszélyforrások észleléséhez. A komplex veszélyforrások elleni védelemből származó riasztások vizsgálatához a kapcsolódó tárolási tevékenységeket Storage Analytics naplózás használatával tekintheti meg. További információ: a **naplózás konfigurálása** a [Storage-fiók figyelése a Azure Portalban](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Rendelkezésre állás

Az Azure Storage komplex veszélyforrások elleni védelme jelenleg blob Storage, Azure Files (előzetes verzió) és Azure Data Lake Storage Gen2 (előzetes verzió) esetén érhető el. A komplex veszélyforrások elleni védelmet támogató fióktípus például az általános célú v2, a blobok és a blob Storage-fiókok. A komplex veszélyforrások elleni védelem a nyilvános felhőkben és az USA kormányzati felhőkben is elérhető, de nem más szuverén vagy Azure Government Felhőbeli régiókban.

Az Azure Blob Storage API-kat és a Data Lake Storage API-kat használó Data Lake Storage támogatási tranzakciókat engedélyező hierarchikus névtereket tartalmazó fiókok. Az Azure-fájlmegosztás támogatja a tranzakciókat az SMB protokollon keresztül.

A díjszabással kapcsolatos részletekért, beleértve az ingyenes 30 napos próbaverziót is, tekintse meg a [Azure Security Center díjszabási oldalát](https://azure.microsoft.com/pricing/details/security-center/).

Az alábbi lista összefoglalja az Azure Storage komplex veszélyforrások elleni védelmének rendelkezésre állását:

- Kiadás állapota:
  - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (általánosan elérhető)
  - [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (az előzetes verzió támogatja az SMB-és Rest-tranzakciókat)
  - Azure Data Lake Storage Gen2 (előzetes verzió)
- Felhők<br>
    ✔ Kereskedelmi felhők<br>
    ✔ US Gov<br>
    ✘ China gov, egyéb gov

## <a name="set-up-advanced-threat-protection"></a>Komplex veszélyforrások elleni védelem beállítása

A komplex veszélyforrások elleni védelem a következő szakaszokban ismertetett módon állítható be.

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Amikor előfizet a Azure Security Center Standard csomagra, a komplex veszélyforrások elleni védelem automatikusan be lesz állítva az összes Storage-fiókra. A következő módon engedélyezheti vagy letilthatja az összetett veszélyforrások elleni védelmet a Storage-fiókjaihoz egy adott előfizetésben:

1. A [Azure Portal](https://portal.azure.com) **Azure Security Center** elindítása.
1. A főmenüben kattintson a **díjszabás & beállítások**elemre.
1. Kattintson arra az előfizetésre, amelyre engedélyezni vagy le szeretné tiltani a veszélyforrások elleni védelmet a Storage-fiókjaihoz.

    ![Előfizetés kiválasztása](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Kattintson a **díjszabási**csomag elemre.
1. Az **árképzési rétegek kiválasztása erőforrástípus szerint** szakaszban, a Storage- **fiókok** sorban kattintson az **engedélyezve** vagy a **Letiltva**lehetőségre.

    ![ATP engedélyezése Security Center](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Kattintson a **Mentés** gombra.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Indítsa el a [Azure Portal](https://portal.azure.com/).
1. Navigáljon az Azure Storage-fiókjához. A **Beállítások**területen válassza a **fokozott biztonság**lehetőséget.
1. Válassza a **Beállítások** hivatkozást a speciális biztonsági beállítások lapon.
1. A **fokozott biztonság** beállítása **a**következőre:.
1. Az új vagy frissített szabályzat mentéséhez kattintson a **Mentés** gombra.

    ![Az Azure Storage komplex veszélyforrások elleni védelem bekapcsolása](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="template"></a>[Sablon](#tab/template)

Egy Azure Resource Manager sablon használatával üzembe helyezhet egy Azure Storage-fiókot, amelyen engedélyezve van az összetett veszélyforrások védelme. További információ: Storage- [fiók komplex veszélyforrások elleni védelemmel](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Egy Azure Policy használatával engedélyezheti a komplex veszélyforrások elleni védelmet egy adott előfizetés vagy erőforráscsoport alatt lévő Storage-fiókok között.

1. Indítsa el az Azure **Policy-fogalommeghatározások** lapot.

1. Keresse meg a komplex **veszélyforrások elleni védelem telepítése a Storage-fiókokra** vonatkozó házirendet.

     ![Keresési szabályzat](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Válasszon ki egy Azure-előfizetést vagy erőforráscsoportot.

    ![Előfizetés vagy csoport kiválasztása](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Rendelje hozzá a szabályzatot.

    ![Házirend-definíciók lap](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

A REST API-parancsok használatával létrehozhat, frissíthet vagy beszerezhet egy adott Storage-fiók komplex veszélyforrások elleni védelem beállítását.

* [Komplex veszélyforrások elleni védelem – létrehozás](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Komplex veszélyforrások elleni védelem – Get](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Használja a következő PowerShell-parancsmagokat:

* [Komplex veszélyforrások elleni védelem engedélyezése](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Komplex veszélyforrások elleni védelem](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [A komplex veszélyforrások elleni védelem letiltása](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Biztonsági rendellenességek megismerése

Ha a tárolási tevékenységek rendellenességeket tapasztalnak, e-mailben értesítést kap a gyanús biztonsági eseményről. Az esemény részletei a következők:

* A rendellenesség természete
* A tárfiók neve
* Az esemény időpontja
* A tárolási típus
* A lehetséges okok
* A vizsgálat lépései
* A szervizelés lépései

Az e-mail emellett tartalmazza a lehetséges okokat és az ajánlott műveleteket is a lehetséges fenyegetések kivizsgálásához és enyhítéséhez.

![Azure Storage komplex veszélyforrások elleni védelem – riasztási e-mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Az aktuális biztonsági riasztásokat a Azure Security Center [biztonsági riasztások csempéről](../../security-center/security-center-managing-and-responding-alerts.md)tekintheti meg és kezelheti. Ha egy adott riasztásra kattint, a rendszer részletesen ismerteti az aktuális fenyegetés kivizsgálásával és a jövőbeli fenyegetésekkel kapcsolatos műveleteket.

![Azure Storage komplex veszélyforrások elleni védelem – riasztási e-mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Biztonsági riasztások

A riasztásokat szokatlan és potenciálisan ártalmas kísérletek generálják a Storage-fiókok eléréséhez vagy kiaknázásához. Az Azure Storage-hoz kapcsolódó riasztások listáját a [Azure Security Center adatszolgáltatásainak veszélyforrások elleni védelme](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage) **című rész tartalmazza** .

## <a name="next-steps"></a>További lépések

* További információ az [Azure Storage-fiókok naplóiról](/rest/api/storageservices/About-Storage-Analytics-Logging)
* További információ a [Azure Security Center](../../security-center/security-center-intro.md)
