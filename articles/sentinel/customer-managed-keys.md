---
title: Ügyfél által felügyelt kulcsok beállítása az Azure Sentinelben| Microsoft dokumentumok
description: Ismerje meg, hogyan állíthatja be az ügyfelek által felügyelt kulcsokat (CMK) az Azure Sentinelben.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: yelevin
ms.openlocfilehash: 5eed208ed79aeab4e46ed90dd4d340a8b445be96
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461633"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Az Azure Sentinel ügyféláltal felügyelt kulcsának beállítása


Ez a cikk háttér-információkat és lépéseket tartalmaz az Azure Sentinel ügyfél által felügyelt kulcsának (CMK) konfigurálásához. A CMK lehetővé teszi, hogy az Azure Sentinelnek mentett vagy elküldött összes adat titkosítva legyen az összes releváns tárolási erőforrásban az Ön által létrehozott vagy birtokolt Azure Key Vault-kulccsal.

> [!NOTE]
> -   Az Azure Sentinel CMK-funkció csak az **új** ügyfelek számára érhető el, és a funkcióhoz való hozzáférést az Azure-funkciók regisztrációja szabályozza.A kapcsolatfelvételkor kérhet azuresentinelCMK@microsoft.comhozzáférést, és a kapacitás rendelkezésre állása esetén a függőben lévő kérelmek et jóváhagyjuk.
> -   Az Azure Sentinel CMK-funkció csak az USA keleti régiójában, az USA nyugati régiójában 2 és az USA középső középső régióiban érhető el.
> -   A CMK funkció csak a naponta 1 TB-ot küldő ügyfelek számára érhető el. A további díjszabásról akkor kap tájékoztatást, amikor a Microsoftra alkalmazza a CMK azure-előfizetésében való kiépítését. További információ a Log Analytics-terhelésről. [Log Analytics](../azure-monitor/platform/customer-managed-keys.md#disclaimers)

## <a name="how-cmk-works"></a>Hogyan működik a CMK? 

Az Azure Sentinel-megoldás számos tárolási erőforrást használ a naplógyűjtéshez és a funkciókhoz, ezek közé tartozik a Log Analytics és más tárolási erőforrások. Az Azure Sentinel CMK-konfiguráció részeként a CMK-beállításokat is konfigurálnia kell a kapcsolódó tárolási erőforrásokon. A Log Analytics-en kívüli tárolási erőforrásokban mentett adatok is titkosítva lesznek.

> [!NOTE]
> Ha engedélyezi a CMK-t az Azure Sentinelen, a CMK-t nem támogató nyilvános előzetes verziójú szolgáltatások nem lesznek engedélyezve.

## <a name="enable-cmk"></a>CmK engedélyezése 

A CMK kiépítéséhez kövesse az alábbi lépéseket: 

1.  Hozzon létre egy Azure Key Vault és a kulcs tárolása.

2.  A CMK engedélyezése a Log Analytics-munkaterületen.

3.  Regisztráljon a Cosmos DB-re.

4.  Adjon hozzá egy hozzáférési szabályzatot az Azure Key Vault-példányhoz.

5.  CmK engedélyezése az Azure Sentinelben.

6.  Engedélyezze az Azure Sentinelt.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>1. LÉPÉS: Hozzon létre egy Azure Key Vault és a kulcs tárolása

1.  [Hozzon létre Azure Key Vault-erőforrást,](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)majd hozzon létre vagy importáljon egy adattitkosításhoz használandó kulcsot.
    > [!NOTE]
    >  Az Azure Key Vault kell konfigurálni, mint helyreállítható a kulcs és a hozzáférés védelme érdekében.

1.  [Helyreállítási beállítások bekapcsolása:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   Győződjön meg arról, hogy a [Lágy törlés](../key-vault/general/overview-soft-delete.md) be van kapcsolva.

    -   Kapcsolja be a [Védelem kiürítése](../key-vault/general/overview-soft-delete.md#purge-protection) parancsot a titkosfájl/trezor kényszerített törlésének elleni védelem érdekében, még a helyreállítható törlés után is.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>2. LÉPÉS: CmK engedélyezése a Log Analytics-munkaterületen

Kövesse az [Azure Monitor ügyfél által felügyelt kulcskonfigurációjában](../azure-monitor/platform/customer-managed-keys.md) található utasításokat, hogy hozzon létre egy CMK-munkaterületet, amely et az Azure Sentinel munkaterületként fogja használni a következő lépésekben.

### <a name="step-3-register-for-cosmos-db"></a>3. LÉPÉS: Regisztráljon a Cosmos DB-re

Az Azure Sentinel a Cosmos DB-vel együttműködve további tárolási erőforrásként működik. Győződjön meg róla, hogy regisztráljon a Cosmos DB.Make sure to register to Cosmos DB.

Kövesse a Cosmos DB utasítást [az Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) erőforrás-szolgáltató regisztrálásához az Azure-előfizetéshez.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>4. LÉPÉS: Hozzáférési szabályzat hozzáadása az Azure Key Vault-példányhoz

Győződjön meg arról, hogy hozzáférést a Cosmos DB az Azure Key Vault-példány. Kövesse a Cosmos DB [utasítást, hogy egy hozzáférési szabályzatot adjon hozzá az Azure Key Vault-példányhoz az](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) Azure Cosmos DB alapcsomaggal.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>5. LÉPÉS: CmK engedélyezése az Azure Sentinelben

Az Azure Sentinel CMK-funkció csak akkor érhető el az új ügyfelek számára, ha közvetlenül az Azure termékcsoporttól kapott hozzáférést. A Microsoftnál lévő partnereivel az Azure Sentinel csapatától kapott jóváhagyást a CMK-k engedélyezéséhez a megoldásban.

Miután megkapja a jóváhagyást, meg kell adnia a következő információkat a CMK funkció engedélyezéséhez.

-  Munkaterület-azonosító, amelyen engedélyezni szeretné a CMK-t

-  Key Vault URL-címe: Másolja a kulcs "kulcsazonosítóját" az utolsó perjelig:  
    

    ![kulcsazonosító](./media/customer-managed-keys/key-identifier.png)

    Az Azure Sentinel csapata engedélyezi az Azure Sentinel CMK funkciót a megadott munkaterülethez.

-  Az Azure Sentinel termékcsapatának ellenőrzése, hogy ön engedélyezte-e a funkció használatát. Ezt meg kell kapnod, mielőtt folytatod.

### <a name="step-6-enable-azure-sentinel"></a>6. LÉPÉS: Az Azure Sentinel engedélyezése


Nyissa meg az Azure Portalon, és engedélyezze az Azure Sentinelt azon a munkaterületen, amelyen a CMK-t beállította. További információ: [Azure Sentinel Onboarding](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Kulcstitkosítási kulcs visszavonása vagy törlése


Abban az esetben, ha a felhasználó visszavonja a kulcs titkosítási kulcsot, akár törlésével, akár az Azure Sentinel hozzáférésének eltávolításával, egy órán belül az Azure Sentinel tiszteletben tartja a változást, és úgy viselkedik, mintha az adatok már nem érhetők el. Ezen a ponton minden olyan művelet, amely állandó tárolási erőforrásokat használ, például az adatok betöltése, az állandó konfigurációmódosításai és az incidensek létrehozása, meg lesz akadályozva. A korábban tárolt adatok nem törlődnek, de nem érhetők el. A nem elérhető adatokat az adatmegőrzési szabályzat szabályozza, és az adott házirendnek megfelelően törlődnek.

A titkosítási kulcs visszavonása vagy törlése után csak a fiók törlése lehetséges.

Ha a hozzáférés visszavonás után helyreáll, az Azure Sentinel egy órán belül visszaállítja az adatokhoz való hozzáférést.

Ha többet szeretne megtudni arról, hogyan működik ez az Azure Monitorban, olvassa el az [Azure Monitor CMK-visszavonása.](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation)

## <a name="key-encryption-key-rotation"></a>Kulcstitkosítási kulcs elforgatása


Az Azure Sentinel és a Log Analytics támogatja a kulcsrotációt. Amikor egy felhasználó kulcselforgatást hajt végre a Key Vaultban, az Azure Sentinel egy órán belül támogatja az új kulcsot.

A Key Vaultban a kulcs elforgatását a kulcs új verziójának létrehozásával hajthatja végre:

![kulcs elforgatása](./media/customer-managed-keys/key-rotation.png)

Letilthatja a kulcs előző verzióját 24 óra elteltével, vagy az Azure Key Vault naplói után már nem jelennek meg olyan tevékenység, amely az előző verziót használja.

Ha ugyanazt a kulcsot használja az Azure Sentinelben és a Log Analytics-ben, kulcsrotációt kell végrehajtania, akkor explicit módon frissítenie kell a fürterőforrást a Log Analytics-ben az új Azure Key Vault-kulcsverzióval. További információ: [Azure Monitor CMK rotation](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation).

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan állíthat be egy ügyfél által felügyelt kulcsot az Azure Sentinelben. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)

