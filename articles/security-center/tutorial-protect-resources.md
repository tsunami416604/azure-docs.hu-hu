---
title: Hozzáférés & Application Controls oktatóanyaghoz – Azure Security Center
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egy igény szerinti virtuálisgép-hozzáférési szabályzatot és egy alkalmazás-ellenőrzési házirendet.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 89d04588d07407be5dbd7ddbafd6543ed461462e
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945719"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Oktatóanyag: Erőforrások védelme az Azure Security Centerrel
A Security Center korlátozza a fenyegetéseknek való kitettségét azzal, hogy hozzáférés- és alkalmazásvezérlőket használ a kártékony tevékenységek blokkolására. Az igény szerinti (JIT) virtuálisgép-hozzáférés csökkenti a támadásoknak való kitettséget azáltal, hogy lehetővé teszi a virtuális gépek állandó hozzáférésének megtagadását. Az állandó hozzáférés helyett szabályozott és naplózott hozzáférést biztosít a virtuális gépekhez – csak akkor, ha szükség van rá. Az adaptív alkalmazásvezérlők segítenek felvértezni a virtuális gépeket a kártevők ellen azáltal, hogy szabályozzák, mely alkalmazások futhatnak rajtuk. Security Center a gépi tanulás segítségével elemzi a virtuális gépen futó folyamatokat, és segítséget nyújt az engedélyezési listákra vonatkozó szabályok alkalmazásában az intelligenciával.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Igény szerinti virtuálisgép-hozzáférési szabályzat konfigurálása
> * Alkalmazásvezérlési szabályzat konfigurálása

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyagban szereplő funkciók átlépéséhez engedélyeznie kell az Azure Defender használatát. Az Azure Defender szolgáltatás díjmentesen kipróbálható. További információ: az [Azure Defender kipróbálása](security-center-pricing.md).

## <a name="manage-vm-access"></a>Virtuális gépekhez való hozzáférés kezelése
A JIT VM-hozzáférés segítségével zárolhatja az Azure-beli virtuális gépek bejövő forgalmát, így csökkentve a támadásoknak való kitettséget, miközben könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges.

A felügyeleti portoknak nem kell mindig nyitva lenniük. Csak addig kell nyitva lenniük, amíg Ön csatlakozik a virtuális géphez, például azért, hogy felügyeleti vagy karbantartási feladatokat végezzen. Ha az igény szerinti hozzáférés engedélyezve van, Security Center a hálózati biztonsági csoport (NSG) szabályait használja, amelyek korlátozzák a felügyeleti portok elérését, hogy a támadók ne tudják megcélozni azokat.

Kövesse a [felügyeleti portok biztonságossá](security-center-just-in-time.md)tételének igény szerinti elérését ismertető útmutatót.

## <a name="harden-vms-against-malware"></a>Virtuális gépek kártevők elleni felvértezése
Az adaptív alkalmazásvezérlők segítségével meghatározhatja a konfigurált erőforráscsoportokon futtatható alkalmazások csoportját, ami többek között segít felvértezni virtuális gépeit a kártevők ellen. Security Center a gépi tanulás segítségével elemzi a virtuális gépen futó folyamatokat, és segítséget nyújt az engedélyezési listákra vonatkozó szabályok alkalmazásában az intelligenciával.

A [gépek támadási felületének csökkentése érdekében kövesse az adaptív alkalmazások használatának szabályozása](security-center-adaptive-application.md)című témakör útmutatását.

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan korlátozhatja a fenyegetéseknek való kitettségét a következőkkel:

> [!div class="checklist"]
> * Az igény szerinti virtuálisgép-hozzáférési szabályzat konfigurálása, amely a virtuális gépekhez csak szükség esetén felügyelt és naplózott hozzáférést biztosít
> * Adaptív alkalmazásvezérlési szabályzat konfigurálása, amely szabályozza, mely alkalmazások futhatnak a virtuális gépeken

Folytassa a következő oktatóanyaggal, amely a biztonsági incidensekre való válaszadást ismerteti.

> [!div class="nextstepaction"]
> [Oktatóanyag: Reagálás a biztonsági incidensekre](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
