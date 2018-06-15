---
title: Az Azure Security Centerben Internet felé néző végpontok-en keresztüli hozzáférés korlátozása |} Microsoft Docs
description: Ez a dokumentum azt ismerteti, hogyan valósítja meg az Azure Security Center ajánlás **internetes végpont-en keresztüli hozzáférés korlátozása**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
ms.openlocfilehash: 92906d31f4db21f37094f192dadd080e28cc6e8e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363053"
---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Az Azure Security Centerben Internet felé néző végpontok-en keresztüli hozzáférés korlátozása
Azure Security Center javasolni fogja, az Internet felé néző végpontok-en keresztüli hozzáférés korlátozása, ha bármely, a hálózati biztonsági csoportok (NSG-k) egy vagy több bejövő szabályok, amelyek lehetővé teszik a hozzáférést a "bármely" forrás IP-cím. Megnyitása access "bármely" engedélyezheti, hogy a támadók a erőforrások eléréséhez. Security Center javasolni fogja, hogy a forrás IP-címek, amelyek ténylegesen hozzá kell férniük való hozzáférés korlátozása a bejövő szabályok szerkesztése.

Ez a javaslat bármely nem webes porthoz, amely rendelkezik "a" forrás jön létre.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást. A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
1. Az a **javaslatok panel**, jelölje be **internetes végpont-en keresztüli hozzáférés korlátozása**.

   ![Internetről elérhető végponton keresztüli hozzáférés korlátozása][1]
2. Ekkor megnyílik a panel **internetes végpont-en keresztüli hozzáférés korlátozása**. Ezen a panelen a virtuális gépek (VM) rendelkező bejövő szabályok létrehozása a potenciális biztonsági kockázatot listáját tartalmazza. Jelöljön ki egy virtuális Gépet.

   ![Jelöljön ki egy virtuális Gépet][2]
3. A **NSG** csempe megjeleníti a társított virtuális Gépet, a hálózati biztonsági csoport információkat és a kapcsolódó bejövő szabályok. Válassza ki **bejövő szabályok szerkesztése** egy bejövő forgalomra vonatkozó szabály szerkesztése gombra.

   ![Hálózati biztonsági csoport panel][3]
4. Az a **bejövő biztonsági szabályok** panelen válassza ki a bejövő forgalomra vonatkozó szabály szerkesztése. Ebben a példában most válasszon **AllowWeb**.

   ![Bejövő biztonsági szabályok][4]

   Vegye figyelembe, igény szerint kiválaszthatja **alapértelmezett szabályok** című szakaszban láthat az alapértelmezett szabályokat minden NSG-k által tartalmazott. Az alapértelmezett szabályokat nem lehet törölni, de hozzárendeli őket egy alacsonyabb prioritású virtuális gép, mert azok az Ön által létrehozott szabályok felülbírálható. További információ [alapértelmezett szabályok](../virtual-network/security-overview.md#default-security-rules).

   ![Alapértelmezett szabályok][5]
5. A a **AllowWeb** panelen a bejövő forgalomra vonatkozó szabály tulajdonságait szerkesztheti, hogy a **forrás** blokkot az IP-címek vagy IP-cím. A bejövő szabály tulajdonságait kapcsolatos további információkért lásd: [NSG-szabályok](../virtual-network/security-overview.md#security-rules).

   ![Bejövő forgalomra vonatkozó szabály szerkesztése][6]

## <a name="see-also"></a>Lásd még
Ez a cikk bemutatta megvalósításához a Security Center javaslat "Hozzáférés korlátozása az Internet felé néző végpont keresztül." Az NSG-k és szabályok engedélyezésével kapcsolatos további tudnivalókért olvassa el a következőket:

* [Mi az a hálózati biztonsági csoport (NSG)?](../virtual-network/security-overview.md)
* [A hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági házirendek beállítása az Azure Security Centerben](security-center-policies.md) – Annak bemutatása, hogy miként konfigurálhat biztonsági házirendeket Azure-előfizetéseihez és az erőforráscsoportokhoz.
* [Biztonsági javaslatok kezelése az Azure Security Centerben](security-center-recommendations.md) – Miként könnyítik meg a javaslatok az Azure-erőforrások védelmét?
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – Tájékozódás az Azure biztonságával kapcsolatos legfrissebb hírekről és információkról.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
