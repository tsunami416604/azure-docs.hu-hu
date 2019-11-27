---
title: Feltételes hozzáférési szabályzatok áttelepíteni – Azure Active Directory
description: Ismerje meg, hogy mit kell tudnia a klasszikus szabályzatok áttelepítéséhez a Azure Portalban.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75d664f6e61dbbaaf0b8ab74c392596a206ff644
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380556"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>Mi a szabályzat áttelepítése Azure Active Directory feltételes hozzáférésben? 

A [feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) az Azure Active Directory (Azure ad) egyik funkciója, amely lehetővé teszi annak vezérlését, hogy a jogosult felhasználók hozzáférjenek a felhőalapú alkalmazásokhoz. Habár a cél még mindig ugyanaz, az új Azure Portal kiadása jelentős újdonságokat vezetett be a feltételes hozzáférés működéséhez.

Érdemes áttelepíteni azokat a házirendeket, amelyeket nem a Azure Portal hozott létre, mert:

- Mostantól olyan forgatókönyveket is megadhat, amelyeket nem tudott kezelni.
- Csökkentheti a felügyelni kívánt szabályzatok számát.   
- A feltételes hozzáférési szabályzatokat egyetlen központi helyen kezelheti.
- A klasszikus Azure portál kivonásra kerül.   

Ez a cikk azt ismerteti, hogy mit kell tudnia a meglévő feltételes hozzáférési szabályzatok új keretrendszerre való átállításához.
 
## <a name="classic-policies"></a>Klasszikus szabályzatok

A [Azure Portal](https://portal.azure.com)a [feltételes](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) hozzáférési szabályzatok oldal a feltételes hozzáférési szabályzatok belépési pontja. Előfordulhat azonban, hogy a környezetében olyan feltételes hozzáférési szabályzatokat is tartalmazhat, amelyeket nem ezen a lapon hozott létre. Ezeket a szabályzatokat *klasszikus szabályzatoknak*nevezzük. A klasszikus szabályzatok feltételes hozzáférési szabályzatok, amelyeket a ben hozott létre:

- A klasszikus Azure portál
- A klasszikus Intune-portál
- A Intune App Protection portál

A **feltételes hozzáférés** lapon a klasszikus szabályzatok [ **(előzetes verzió)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) elemre kattintva érheti el a klasszikus házirendeket a **kezelés** szakaszban. 

![Azure Active Directory](./media/policy-migration/71.png)

A **klasszikus szabályzatok** nézet a következő lehetőségeket kínálja:

- A klasszikus szabályzatok szűrése.
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- Klasszikus szabályzatok letiltása.

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- Tekintse át a klasszikus szabályzat beállításait (és tiltsa le).

   ![Azure Active Directory](./media/policy-migration/74.png)

Ha letiltotta a klasszikus szabályzatot, többé nem tudja visszaállítani ezt a lépést. Ezért módosítható a csoporttagság egy klasszikus szabályzatban a **részletek** nézet használatával. 

![Azure Active Directory](./media/policy-migration/75.png)

A kiválasztott csoportok módosításával vagy adott csoportok kizárásával tesztelheti a letiltott klasszikus házirendek hatását néhány tesztelési felhasználóra, mielőtt letiltja a házirendet az összes befoglalt felhasználó és csoport számára. 

## <a name="azure-ad-conditional-access-policies"></a>Azure AD feltételes hozzáférési szabályzatok

A Azure Portal feltételes hozzáférésével egyetlen központi helyen kezelheti az összes házirendet. Mivel a feltételes hozzáférés változásának implementációja, a klasszikus szabályzatok áttelepítése előtt Ismerkedjen meg az alapvető fogalmakkal.

Lásd:

- [Mi a feltételes hozzáférés a Azure Active Directoryban](../active-directory-conditional-access-azure-portal.md) az alapvető fogalmak és a terminológia megismeréséhez.
- [Ajánlott eljárások a feltételes hozzáféréshez Azure Active Directory](best-practices.md) a feltételes hozzáférés üzembe helyezéséhez a szervezetben.
- Többtényezős [hitelesítés megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel](app-based-mfa.md) a Azure Portal felhasználói felületének megismeréséhez.
 
## <a name="migration-considerations"></a>Migrálási szempontok

Ebben a cikkben az Azure AD feltételes hozzáférési szabályzatait *új szabályzatoknak*is nevezzük.
A klasszikus szabályzatok továbbra is az új szabályzatokkal együtt működnek, amíg le nem tiltja vagy nem törli őket. 

A házirend-Összevonás kontextusában a következő szempontok fontosak:

- Habár a klasszikus szabályzatok egy adott felhőalapú alkalmazáshoz vannak kötve, az új szabályzatokban tetszőleges számú felhőalapú alkalmazást választhat.
- A klasszikus szabályzatok és a felhőalapú alkalmazások új szabályzatának szabályozása megköveteli az összes vezérlő (*és*) teljesítését. 
- Egy új szabályzatban a következőket teheti:
   - Ha a forgatókönyve megköveteli, több feltételt is egyesíteni kell. 
   - Válasszon több engedélyezési követelményt hozzáférés-vezérlésként, és kombinálja őket logikai *vagy* (a kijelölt vezérlők valamelyikének megkövetelése) vagy logikai *és* (az összes kijelölt vezérlő megkövetelése) beállítással.

   ![Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

Ha olyan klasszikus házirendeket szeretne áttelepíteni az **Office 365 Exchange Online** -hoz, amelyek **Exchange Active Sync** ügyfél-alkalmazási feltételként szerepelnek, előfordulhat, hogy nem tudja összevonni őket egy új szabályzatba. 

Ez például az az eset, ha az összes ügyfélalkalmazás-típust támogatni szeretné. Egy olyan új szabályzatban, amely **Exchange Active Sync** ügyfél-alkalmazási feltételként, nem választhat más ügyfélalkalmazások közül.

![Azure Active Directory](./media/policy-migration/64.png)

Ha a klasszikus szabályzat több feltételt is tartalmaz, akkor nem lehet konszolidálni egy új szabályzatot. Egy olyan új szabályzat, amely **Exchange Active Sync** ügyfél-alkalmazási feltétel konfigurálva, nem támogatja a többi feltételt:   

![Azure Active Directory](./media/policy-migration/08.png)

Ha van olyan új szabályzata, amely **Exchange Active Sync** ügyfél-alkalmazási feltétel konfigurálva van, akkor győződjön meg arról, hogy az összes többi feltétel sincs konfigurálva. 

![Azure Active Directory](./media/policy-migration/16.png)
 
Az Office 365 Exchange Online-hoz készült, [alkalmazáson alapuló](technical-reference.md#approved-client-app-requirement) klasszikus házirendek, amelyek tartalmazzák a **Exchange Active Sync** as Client apps-feltételt, lehetővé teszik a **támogatott** és nem **támogatott** [eszközök platformját](technical-reference.md#device-platform-condition) Habár a kapcsolódó új házirendekben nem konfigurálhat egyedi eszközöket, csak a [támogatott eszközökre](technical-reference.md#device-platform-condition) korlátozhatja a támogatást. 

![Azure Active Directory](./media/policy-migration/65.png)

Több klasszikus szabályzatot is összevonhat, amelyek a **Exchange Active Sync** ügyfél-alkalmazási feltételnek minősülnek, ha rendelkeznek a következőkkel:

- Csak **Exchange Active Sync** feltételként 
- A hozzáférés konfigurálásának számos követelménye

Az egyik gyakori forgatókönyv az alábbiak összevonása:

- A klasszikus Azure portál eszközön alapuló klasszikus szabályzata 
- Egy alkalmazás-alapú klasszikus szabályzat az Intune app Protection portálon 
 
Ebben az esetben a klasszikus szabályzatokat egy új házirendbe összevonhatja, amely mindkét követelménynek megfelel.

![Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Eszköz platformok

Az [alkalmazáson alapuló vezérlőkkel](technical-reference.md#approved-client-app-requirement) rendelkező klasszikus szabályzatok előre konfigurálva vannak az iOS és az Android [platformon az eszköz platformjának feltétele](technical-reference.md#device-platform-condition). 

Egy új szabályzatban ki kell választania a támogatni kívánt [eszközök platformját](technical-reference.md#device-platform-condition) .

![Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>További lépések

- Ha tudni szeretné, hogyan konfigurálhat egy feltételes hozzáférési szabályzatot, tekintse meg a többtényezős hitelesítés [megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel](app-based-mfa.md)című témakört.
- Ha készen áll a környezet feltételes hozzáférési házirendjeinek konfigurálására, tekintse meg az [ajánlott eljárásokat a feltételes hozzáféréshez Azure Active Directory](best-practices.md). 
