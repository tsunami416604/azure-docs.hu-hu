---
title: Azure Information Protection-adatkapcsolatok csatlakoztatása az Azure Sentinel előzetes verziójához | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztatható Azure Information Protection-adatkapcsolat az Azure Sentinelben.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2019
ms.author: cabailey
ms.openlocfilehash: 0614d24b19ef39cebdf4cb47fdd2d44470ea59c0
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067745"
---
# <a name="connect-data-from-azure-information-protection"></a>Adatok összekapcsolásának Azure Information Protection

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Information Protection adatösszekötő konfigurálásával továbbíthatja a naplózási adatokat [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) az Azure sentinelbe. A Azure Information Protection segítségével szabályozhatja és biztonságossá teheti a bizalmas adatokat, függetlenül attól, hogy a felhőben vagy a helyszínen van tárolva.

Ha a [Azure Information Protection központi jelentéskészítése](https://docs.microsoft.com/azure/information-protection/reports-aip) már konfigurálva van, így a szolgáltatás naplózási adatai ugyanabban a log Analytics-munkaterületen tárolódnak, mint az Azure Sentinel esetében, akkor kihagyhatja a következő konfigurációt: Ez az adatösszekötő. A Azure Information Protection naplózási adatai már elérhetők az Azure Sentinel szolgáltatásban.

Ha azonban a Azure Information Protection naplózási adatai egy másik Log Analytics munkaterületre kerülnek, mint amit jelenleg az Azure Sentinel számára választott, tegye a következők egyikét:

- Az Azure Sentinelben kiválasztott munkaterület módosítása.

- Módosítsa Azure Information Protection munkaterület munkaterületét, amelyet az adatösszekötő konfigurálásával végezhet el.
    
    Ha módosítja a munkaterületet, a Azure Information Protection új jelentési adatai lesznek tárolva az Azure Sentinelhez használt munkaterületen, és az előzmények nem érhetők el az Azure Sentinelben. Továbbá, ha az előző munkaterület egyéni lekérdezésekhez, riasztásokhoz vagy REST API-khoz van konfigurálva, ezeket újra kell konfigurálni az Azure Sentinel-munkaterületen, ha a Azure Information Protection használatára szeretné használni őket. Azure Information Protection használó ügyfelek és szolgáltatások esetében nincs szükség újrakonfigurálásra.

## <a name="prerequisites"></a>Előfeltételek

- A bérlőhöz tartozó alábbi Azure AD-rendszergazdai szerepkörök egyike: Azure Information Protection rendszergazda, biztonsági rendszergazda vagy globális rendszergazda.
    
    > [!NOTE]
    > Ha a bérlő az [egyesített címkézési platformon](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)van, akkor nem használhatja a Azure Information Protection rendszergazdai szerepkört.

- Olvasási és írási engedélyek az Log Analytics-munkaterülethez, amelyet a Sentinel és a Azure Information Protection használ.

- Azure Information Protection hozzá lett adva a Azure Portalhoz. Ha segítségre van szüksége ezzel a lépéssel kapcsolatban, olvassa el [a Azure Information Protection hozzáadása a Azure Portalhoz](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)című témakört.

## <a name="connect-to-azure-information-protection"></a>Kapcsolódás Azure Information Protectionhoz

Ha nem konfigurált Log Analytics munkaterületet Azure Information Protectionhoz, akkor kövesse az alábbi utasításokat, vagy módosítania kell az Azure Information Protection naplózási adatait tároló munkaterületet. 

1. Az Azure Sentinelben válassza az **adatösszekötők**lehetőséget, majd **Azure Information Protection**.

2. A **Azure Information Protection** panelen válassza az **összekötő lap megnyitása**lehetőséget.

3. A következő panelen, a **konfiguráció** szakaszban válassza a **Azure Information Protection** lehetőséget a **Azure Information Protection Analytics**megnyitásához.

4. Az elérhető munkaterületek listájából válassza ki azt a munkaterületet, amelyet jelenleg az Azure Sentinelhez használ. Ha másik munkaterületet választ, a Azure Information Protection jelentési adatok nem lesznek elérhetők az Azure Sentinel számára.

5. Ha kiválasztott egy munkaterületet, kattintson az **OK gombra** , és az összekötő **állapota** most a **Connected**értékre vált.

6. A Azure Information Protectionból származó jelentéskészítési adatok a kiválasztott munkaterületen lévő **InformationProtectionLogs_CL** táblában tárolódnak. 
    
    A jelentéskészítési adatokat Azure Monitor vonatkozó sémájának használatához keresse meg a **InformationProtectionEvents**. Az Event functions szolgáltatással kapcsolatos további információkért tekintse meg a Azure Information Protection dokumentációjának az [Event functions-hez készült rövid séma-referenciája](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) című szakaszát.

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtanulta, hogyan csatlakozhat Azure Information Protection az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).
