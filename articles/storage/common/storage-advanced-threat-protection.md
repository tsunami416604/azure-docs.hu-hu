---
title: Speciális veszélyforrások elleni védelem konfigurálása
titleSuffix: Azure Storage
description: Konfigurálja a speciális veszélyforrások elleni védelmet az Azure Storage számára a fióktevékenység anomáliáinak észleléséhez, és értesítést kapjon a fiók elérésére irányuló potenciálisan káros kísérletekről.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 4219bb471b92e7ddae72c50403f635498c90080d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251690"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Speciális veszélyforrások elleni védelem konfigurálása az Azure Storage-hoz

Az Azure Storage speciális veszélyforrások elleni védelme egy további biztonsági intelligenciaréteget biztosít, amely észleli a tárfiókok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését anélkül, hogy biztonsági szakértő lenne, vagy biztonsági figyelőrendszereket kezelne.

A biztonsági riasztások akkor aktiválódnak, ha tevékenységi rendellenességek lépnek fel. Ezek a biztonsági riasztások integrálva vannak [az Azure Security Centerrel,](https://azure.microsoft.com/services/security-center/)és e-mailben is elküldésre kerülnek az előfizetés-rendszergazdáknak, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálásával és elhárításával kapcsolatos javaslatokkal.

A szolgáltatás beolvassa az olvasási, írási és törlési kérelmek diagnosztikai naplóit a Blob Storage-ba a fenyegetésészleléshez. A speciális veszélyforrások elleni védelemriasztási riasztások vizsgálatához megtekintheti a kapcsolódó tárolási tevékenységeket a Storage Analytics-naplózás használatával. További információ: **A naplózás konfigurálása** [az Azure Portalon tárfiók figyelése című témakörben.](storage-monitor-storage-account.md#configure-logging)

## <a name="availability"></a>Rendelkezésre állás

Az Azure Storage speciális veszélyforrások elleni védelme jelenleg csak a [Blob Storage számára](https://azure.microsoft.com/services/storage/blobs/)érhető el. 

Ez a szolgáltatás minden nyilvános felhőben és az Egyesült Államok kormányzati felhőiben érhető el, de más szuverén vagy Azure kormányzati felhőrégióban nem.

A díjszabással kapcsolatos részletekért, beleértve az ingyenes 30 napos próbaverziót, tekintse meg az [Azure Security Center díjszabási lapját.](https://azure.microsoft.com/pricing/details/security-center/)


## <a name="set-up-advanced-threat-protection"></a>Speciális veszélyforrások elleni védelem beállítása

A speciális veszélyforrások elleni védelmet a következő szakaszokban ismertetett módszerek bármelyike konfigurálhatja.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Indítsa el az [Azure Portalt.](https://portal.azure.com/)
1. Keresse meg az Azure Storage-fiókját. A **Beállítások csoportban**válassza a **Speciális biztonság**lehetőséget.
1. Válassza a **Beállítások** hivatkozást a speciális biztonsági konfigurációs lapon.
1. Állítsa **a Speciális biztonság** beállítását BE **beállításra.**
1. Az új vagy frissített házirend mentéséhez kattintson a **Mentés** gombra.

    ![Az Azure Storage speciális veszélyforrások elleni védelmének bekapcsolása](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Amikor előfizet a standard szintre az Azure Security Centerben, a speciális veszélyforrások elleni védelem automatikusan be van állítva az összes tárfiókjában. Egy adott előfizetés keretében engedélyezheti vagy letilthatja a részletes veszélyforrások elleni védelmet a tárfiókokhoz az alábbiak szerint:

1. Indítsa el az **Azure Security Centert** az [Azure Portalon.](https://portal.azure.com)
1. A főmenüben kattintson **az & beállítások díjszabása parancsra.**
1. Kattintson arra az előfizetésre, amelyet engedélyezni vagy letiltani szeretne a tárfiókok veszélyforrások elleni védelmében.

    ![Előfizetés kiválasztása](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Kattintson **a Tarifacsomag elemre.**
1. A **Tarifacsomag kiválasztása erőforrástípus szerint** szakasz **Storage-fiókok** sorában kattintson az **Engedélyezve** vagy **a Letiltva gombra.**

    ![Az ATP engedélyezése a Biztonsági központban](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Kattintson a **Mentés** gombra.

### <a name="template"></a>[Sablon](#tab/template)

Azure Resource Manager-sablon használatával üzembe helyezhet egy Azure Storage-fiókot, amelynek engedélyezve van a speciális veszélyforrások elleni védelem. További információ: [Storage fiók speciális veszélyforrások elleni védelemmel.](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Azure-szabályzat használatával speciális veszélyforrások elleni védelem engedélyezése a tárfiókok között egy adott előfizetés vagy erőforráscsoport alatt.

1. Indítsa el az Azure **Policy - Definíciók** lapot.

1. Keresse meg a **központi veszélyforrások elleni védelem központi telepítése a tárfiókok** házirenden.

     ![Keresési szabályzat](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Válasszon ki egy Azure-előfizetést vagy erőforráscsoportot.

    ![Előfizetés vagy csoport kiválasztása](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Rendelje hozzá a házirendet.

    ![Házirend-definíciók lap](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

A Rest API-parancsok használatával hozzon létre, frissítsen vagy szerezzen be egy adott tárfiók speciális veszélyforrások elleni védelmi beállítást.

* [Komplex veszélyforrások elleni védelem - Létrehozás](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Komplex veszélyforrások elleni védelem - Get](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Használja a következő PowerShell-parancsmagokat:

* [Speciális veszélyforrások elleni védelem engedélyezése](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Fejlett veszélyforrások elleni védelem](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Speciális veszélyforrások elleni védelem letiltása](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Biztonsági anomáliák felfedezése

Ha tárolási tevékenység anomáliák fordulnak elő, e-mailben értesítést kap a gyanús biztonsági eseményről. Az esemény részletei a következők:

* Az anomália jellege
* A tárfiók neve
* Az esemény ideje
* A tárolási típus
* A lehetséges okok
* A vizsgálat lépései
* A javítási lépések

Az e-mail is részleteket tartalmaz a lehetséges okok és ajánlott műveletek kivizsgálására és a potenciális fenyegetés enyhítésére.

![Az Azure Storage speciális veszélyforrások elleni riasztási e-mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Az Aktuális biztonsági riasztásokat az Azure Security Center [Biztonsági riasztások csempéjén](../../security-center/security-center-managing-and-responding-alerts.md)tekintheti át és kezelheti. Egy adott riasztásra kattintva részletesen és műveleteket biztosít az aktuális fenyegetés kivizsgálásához és a jövőbeli fenyegetések kezeléséhez.

![Az Azure Storage speciális veszélyforrások elleni riasztási e-mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Biztonsági riasztások

A riasztásokat szokatlan és potenciálisan káros tárfiókok elérésére vagy kihasználó kísérletei generálják. Az Azure Storage riasztásainak listáját az Azure [Security Center Fenyegetésveszélyvédelme az adatszolgáltatások értcímű](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage) **témakör tárolószakaszában** található.

## <a name="next-steps"></a>További lépések

* További információ [az Azure Storage-fiókokban tárolt naplókról](/rest/api/storageservices/About-Storage-Analytics-Logging)
* További információ az [Azure Security Centerről](../../security-center/security-center-intro.md)
