---
title: Az Azure Security Centerben az Internet felé néző végpontok hozzáférésének korlátozása |} A Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan valósíthat meg az Azure Security Center javaslatait **internetről elérhető végponton keresztüli hozzáférés korlátozása**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 8e404651fa315f630ff190c9e70ccdd3eec7f117
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230002"
---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Az Azure Security Centerben az Internet felé néző végpontok hozzáférésének korlátozása
Az Azure Security Center javasolni fogja, hogy az Internet felé néző végpontok hozzáférésének korlátozása, ha bármely a hálózati biztonsági csoportok (NSG) egy vagy több bejövő szabályt, amely engedélyezi a hozzáférést a "bármely" forrás IP-címről. Nyissa meg a "bármely" való hozzáférést lehetővé teheti a támadók számára az erőforrások eléréséhez. A Security Center javasolni fogja, hogy a forrás IP-címek, amelyek ténylegesen hozzá kell férniük való hozzáférés korlátozása a bejövő szabályok szerkesztése.

Ez az ajánlás bármely nem webes porthoz, amely rendelkezik "bármely" forrás jön létre.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást. A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslatok megvalósítása
1. Az a **javaslatok panel**válassza **internetről elérhető végponton keresztüli hozzáférés korlátozása**.

   ![Internetről elérhető végponton keresztüli hozzáférés korlátozása][1]
2. Ekkor megnyílik a panel **internetről elérhető végponton keresztüli hozzáférés korlátozása**. Ezen a panelen a virtuális gépek (VM) a bejövő szabályok létrehozása egy potenciális biztonsági kockázatot sorolja fel. Válasszon egy virtuális Gépet.

   ![Válasszon ki egy virtuális Gépet][2]
3. A **NSG** panel jeleníti meg az információkat, kapcsolódó bejövő szabályok és a kapcsolódó virtuális gép hálózati biztonsági csoport. Válassza ki **bejövő szabályok szerkesztése** folytatásához egy bejövő szabály szerkesztését.

   ![Hálózati biztonsági csoport panel][3]
4. Az a **bejövő biztonsági szabályok** panelen adja meg a bejövő szabály szerkesztését. Ebben a példában válassza **AllowWeb**.

   ![Bejövő biztonsági szabály][4]

   Vegye figyelembe, választhatja **alapértelmezett szabályokkal** megtekintheti az összes NSG-k által tartalmazott alapértelmezett szabálykészletet. Az alapértelmezett szabályok nem törölhetők, de alacsonyabb prioritású hozzá vannak rendelve, mert azok az Ön által létrehozott szabályok szerint felülbírálható. Tudjon meg többet [alapértelmezett szabályokkal](../virtual-network/security-overview.md#default-security-rules).

   ![Alapértelmezett szabályok][5]
5. Az a **AllowWeb** panelen bejövő szabály tulajdonságainak szerkesztéséhez, hogy a **forrás** egy olyan IP-cím vagy IP-Címblokk. A bejövő szabály tulajdonságainak kapcsolatos további információkért lásd: [NSG-szabályok](../virtual-network/security-overview.md#security-rules).

   ![Bejövő szabály szerkesztése][6]

## <a name="see-also"></a>Lásd még
Ez a cikk bemutatta, hogyan valósíthat meg a Security Center javaslatait "Hozzáférés korlátozása az Internet felé néző végpont használatával." NSG-ket és szabályok engedélyezésével kapcsolatos további tudnivalókért tekintse meg a következőket:

* [Mi az a hálózati biztonsági csoport (NSG)?](../virtual-network/security-overview.md)
* [A hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági házirendek beállítása az Azure Security Centerben](security-center-policies.md) – Annak bemutatása, hogy miként konfigurálhat biztonsági házirendeket Azure-előfizetéseihez és az erőforráscsoportokhoz.
* [Biztonsági javaslatok kezelése az Azure Security Centerben](security-center-recommendations.md) – Miként könnyítik meg a javaslatok az Azure-erőforrások védelmét?
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](https://blogs.msdn.com/b/azuresecurity/) – Tájékozódás az Azure biztonságával kapcsolatos legfrissebb hírekről és információkról.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
