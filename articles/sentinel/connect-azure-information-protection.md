---
title: Az Azure Information Protection csatlakoztatása az Azure Sentinelhez
description: Ismerje meg, hogyan kapcsolhatja össze az Azure Information Protection adatait az Azure Sentinelben.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 396fd7c4289c9d02d451b26f5fb6299acd31e2a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588552"
---
# <a name="connect-data-from-azure-information-protection"></a>Adatok csatlakoztatása az Azure Information Protection szolgáltatásból

> [!IMPORTANT]
> Az Azure Information Protection adatösszekötő az Azure Sentinel jelenleg nyilvános előzetes verzióban.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Az Azure Information Protection naplózási adatait az Azure [Sentinelbe](https://azure.microsoft.com/services/information-protection/) továbbíthatja az Azure Information Protection adatösszekötő konfigurálásával. Az Azure Information Protection segítségével szabályozhatja és biztonságossá teszi a bizalmas adatokat, függetlenül attól, hogy azok a felhőben vagy a helyszínen vannak-e tárolva.

Ha [az Azure Information Protection központi jelentéskészítési](https://docs.microsoft.com/azure/information-protection/reports-aip) szolgáltatása már be van állítva, így a szolgáltatásból származó naplózási adatok ugyanabban a Log Analytics-munkaterületen tárolódnak, amelyet az Azure Sentinel hez jelenleg kiválasztott, kihagyhatja az adatösszekötő konfigurációját. Az Azure Information Protection naplózási adatai már elérhetők az Azure Sentinel számára.

Ha azonban az Azure Information Protection től származó adatok naplózása az Azure Sentinelhez jelenleg kiválasztotttól eltérő Log Analytics-munkaterületre kerül, tegye a következők egyikét:

- Módosítsa az Azure Sentinelben kijelölt munkaterületet.

- Módosítsa az Azure Information Protection munkaterületét, amelyet az adatösszekötő konfigurálásával tehet meg.
    
    Ha módosítja a munkaterületet, az Azure Information Protection új jelentési adatai mostantól az Azure Sentinel hez használt munkaterületen tárolódnak, és a korábbi adatok nem érhetők el az Azure Sentinel számára. Emellett ha az előző munkaterület egyéni lekérdezésekhez, riasztásokhoz vagy REST API-khoz van konfigurálva, ezeket újra kell konfigurálni az Azure Sentinel-munkaterülethez, ha folytatni szeretné az Azure Information Protection számára. Nincs szükség újrakonfigurálásra az Azure Information Protection t használó ügyfelek és szolgáltatások esetében.

## <a name="prerequisites"></a>Előfeltételek

- A bérlő höz az alábbi Azure AD-rendszergazdai szerepkörök egyike: 
    - Az Azure information protection rendszergazdája
    - Biztonsági rendszergazda
    - Megfelelőségi rendszergazda
    - Megfelelőségi adatok rendszergazdája
    - Globális rendszergazda
    
    > [!NOTE]
    > Nem használhatja az Azure Information Protection rendszergazdai szerepkört, ha a bérlő az [egyesített címkézési platformon](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)van.
    
    Ezek a rendszergazdai szerepkörök csak az Azure Information Protection összekötő konfigurálásához szükségesek, és nem szükségesek, ha az Azure Sentinel csatlakozik az Azure Information Protectionhez.

- Az Azure Sentinel és az Azure Information Protection számára használt Log Analytics-munkaterület olvasására és írására vonatkozó engedélyek.

- Az Azure Information Protection hozzá lett adva az Azure Portalhoz. Ha segítségre van szüksége ezzel a lépéssel kapcsolatban, [olvassa el az Azure-információvédelem hozzáadása az Azure Portalhoz című témakört.](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)

## <a name="connect-to-azure-information-protection"></a>Csatlakozás az Azure Information Protection szolgáltatáshoz

Kövesse az alábbi utasításokat, ha még nem konfigurált a Log Analytics munkaterületet az Azure Information Protection számára, vagy módosítania kell az Azure Information Protection naplózási adatait tároló munkaterületet.

1. Az Azure Sentinelben válassza **az Adatösszekötők**lehetőséget, majd **az Azure Information Protection (Előzetes verzió) lehetőséget.**

2. Válassza **az Összekötő lap megnyitása lehetőséget**.

3. Az **Analytics konfigurálása (előzetes verzió)** panelen válassza ki az Azure Sentinel hez jelenleg használt munkaterületet. Ha másik munkaterületet választ, az Azure Information Protection jelentési adatai nem lesznek elérhetők az Azure Sentinel számára.

4. Miután kijelölt egy munkaterületet, válassza az **OK** gombot, és az összekötő **állapota** ekkor **átvált Connected -re.**

5. Az Azure Information Protection jelentési adatai a kiválasztott munkaterületen belüli **InformationProtectionLogs_CL** táblában tárolódnak. 
    
    Ha a jelentési adatokhoz az Azure Monitor megfelelő sémáját szeretné használni, keresse meg az **InformationProtectionEvents (Információvédelmi események)** kifejezést. Ezekről az eseményfüggvényekről az Azure Information Protection dokumentációjában található [Rövid sémahivatkozás az eseményfüggvények](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) című szakaszban olvashat.

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja az Azure Information Protectiont az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
