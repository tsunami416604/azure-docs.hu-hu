---
title: Tantermi Labs Azure Lab Servicesban – GYIK | Microsoft Docs
description: A Azure Lab Servicesban található osztálytermi laborokkal kapcsolatos gyakori kérdésekre adott válaszok.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: spelluru
ms.openlocfilehash: 261f337ac72a28bc2e1c74e23731242cc3bd0075
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68572029"
---
# <a name="classroom-labs-in-azure-lab-services---frequently-asked-questions-faq"></a>Tanterem Labs Azure Lab Servicesban – gyakori kérdések (GYIK)
Választ kaphat a Azure Lab Servicesban található osztálytermi laborokkal kapcsolatos leggyakoribb kérdésekre. 

## <a name="quotas"></a>Kvóták

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Felhasználónként vagy hetente, vagy a labor teljes időtartama alatt a kvóta? 
A laborhoz beállított kvóta minden tanuló számára a labor teljes időtartamára vonatkozik. A [virtuális gépek ütemezett futási ideje](how-to-create-schedules.md) nem számít bele a felhasználó számára kiosztott kvótába. A kvóta az ütemezett órákon kívüli időre szól, amelyet a tanuló a virtuális gépeken tölt.  A kvótákkal kapcsolatos további információkért lásd: [kvóták beállítása a felhasználók](how-to-configure-student-usage.md#set-quotas-for-users)számára.

## <a name="schedules"></a>Ütemezések

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>A laborban lévő összes virtuális gép automatikusan elindul, ha be van állítva egy ütemterv? 
Nem. Nem minden virtuális gép. Csak azok a virtuális gépek, amelyek egy adott időpontban vannak hozzárendelve a felhasználókhoz. A nem a felhasználóhoz rendelt virtuális gépek nem indulnak el automatikusan. Ez a tervezés. 

## <a name="lab-accounts"></a>Labor-fiókok

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Miért nem tudok labort létrehozni, mert a címtartomány nem áll rendelkezésre. 
A tanterem Labs létrehozhat laboratóriumi virtuális gépeket egy olyan IP-címtartomány keretein belül, amelyet a Azure Portal a labor-fiók létrehozásakor határoz meg. Ha címtartomány van megadva, a rendszer minden olyan labort hoz létre, amelyet a rendszer a labor virtuális gépek 512 IP-címeinek kiosztotta után hozott létre. A Lab-fiókhoz tartozó címtartomány elég nagynak kell lennie ahhoz, hogy az összes, a labor-fiókban létrehozni kívánt labort befogadja. 

Ha például egy blokk/19-10.0.0.0/19, ez a címtartomány 8192 IP-címet és 16 Labs-t (8192/512 = 16 Labs) foglal magában. Ebben az esetben a labor létrehozása sikertelen a 17. labor létrehozásakor.

## <a name="blog-post"></a>Blogbejegyzés
Fizessen elő a [Azure Lab Services blogra](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Frissítési értesítések
Fizessen elő a [labor Services frissítéseire](https://azure.microsoft.com/updates/?product=lab-services) , hogy értesüljön a DevTest Labs új funkcióiról.

## <a name="general"></a>Általános
### <a name="what-if-my-question-isnt-answered-here"></a>Mi a teendő, ha nem válaszolt a kérdésre?
Ha a kérdés nem szerepel a listán, tudassa velünk, hogy segítsen megtalálni a választ.

- Tegye fel kérdéseit a GYIK végén. 
- Ha szélesebb közönséget szeretne elérni, tegye fel kérdéseit a [Azure DEVTEST Labs MSDN-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). 
- A szolgáltatások kéréseinek elküldéséhez küldje el kérelmeit és ötleteit [Azure DevTest Labs felhasználói hangra](https://feedback.azure.com/forums/320373-azure-devtest-labs).

