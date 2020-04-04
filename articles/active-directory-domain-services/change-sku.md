---
title: Az Azure AD tartományi szolgáltatások termékváltozatának módosítása | Microsoft dokumentumok
description: Ismerje meg, hogyan kell a Termékváltozat-szintet egy Azure AD tartományi szolgáltatások által kezelt tartományban, ha az üzleti követelmények megváltoznak
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 32f8f157abaf5076911c3908a83be4a644e09656
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655580"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>Meglévő Azure AD tartományi szolgáltatások felügyelt tartományának termékváltozatának módosítása

Az Azure Active Directory tartományi szolgáltatásokban (Azure AD DS) a rendelkezésre álló teljesítmény és funkciók a termékváltozat típusától függenek. Ezek a szolgáltatáskülönbségek közé tartozik a biztonsági mentés gyakorisága vagy az egyirányú kimenő erdőszintű bizalmi kapcsolatok maximális száma (jelenleg előzetes verzióban). A felügyelt tartomány létrehozásakor kiválaszthat egy termékváltozatot, és a felügyelt tartomány üzembe helyezése után az üzleti igények változásakor fel- vagy leválthatja a termékváltozatokat. Az üzleti követelmények módosításai közé tartozhat a gyakoribb biztonsági mentések szükségessége, illetve további erdőszintű bizalmi kapcsolatok létrehozása. A különböző termékváltozatok korlátairól és díjszabásáról az [Azure AD DS Termékváltozat-fogalmak][concepts-sku] és az [Azure AD DS díjszabási lapjai][pricing] című témakörben talál további információt.

Ez a cikk bemutatja, hogyan módosíthatja a termékváltozat egy meglévő Azure AD DS felügyelt tartomány az Azure Portalon keresztül.

## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén töltse ki az oktatóanyagot [az Azure Active Directory tartományi szolgáltatások példányának létrehozásához és konfigurálásához.][create-azure-ad-ds-instance]

## <a name="sku-change-limitations"></a>Termékváltozat módosítási korlátai

Az Azure AD DS felügyelt tartomány üzembe helyezése után a skus-ok módosíthatók fel- vagy levethetők. Ha azonban egy erőforráserdőt használ (jelenleg előzetes verzióban), és egyirányú kimenő erdőszintű bizalmi kapcsolatokat hozott létre az Azure AD DS-ből egy helyszíni AD DS-környezetbe, vannak bizonyos korlátozások a termékváltozat módosítási műveletére vonatkozóan. A *prémium szintű* és a *nagyvállalati* ska-k korlátozzák a létrehozható bizalmi kapcsolatok számát. A jelenleg konfiguráltnál alacsonyabb maximális korláttal rendelkező termékváltozatra nem válthat.

Példa:

* Ha két erdőszintű megbízhatósági kapcsolatot hozott létre a *prémium szintű* termékváltozaton, nem módosíthatja a *standard* termékváltozatra. A *standard* termékváltozat nem támogatja az erdőszintű bizalmi kapcsolatokat.
* Vagy ha hét megbízhatósági kapcsolatot hozott létre a *prémium szintű* termékváltozaton, nem módosíthatja a *vállalati* termékváltozatra. A *vállalati* termékváltozat legfeljebb öt megbízhatósági kapcsolatot támogat.

Ezekről a korlátokról az [Azure AD DS Termékváltozat funkcióiés korlátai][concepts-sku]című témakörben talál további információt.

## <a name="select-a-new-sku"></a>Új termékváltozat kiválasztása

Egy Azure AD DS felügyelt tartomány termékváltozatának módosítása az Azure Portalon, hajtsa végre az alábbi lépéseket:

1. Az Azure Portal tetején keresse meg és válassza ki az **Azure AD tartományi szolgáltatásokat.** Válassza ki a felügyelt tartományt a listából, például *aaddscontoso.com.*
1. Az Azure AD DS lap bal oldalán található menüben válassza a **Beállítások > termékváltozat**lehetőséget.

    ![Válassza ki az Azure AD DS felügyelt tartományának Termékváltozat menübeállítását az Azure Portalon](media/change-sku/overview-change-sku.png)

1. A legördülő menüben válassza ki az Azure AD DS felügyelt tartományához kívánt termékváltozatot. Ha rendelkezik egy erőforráserdővel, nem választhatja ki *a szabványos* termékváltozatot, mivel az erdőszintű bizalmi kapcsolatok csak a *vállalati* termékváltozaton vagy magasabb szintű en érhetők el.

    Válassza ki a kívánt termékváltozatot a legördülő menüből, majd válassza a **Mentés parancsot.**

    ![Válassza ki a szükséges termékváltozatot az Azure Portal legördülő menüjéből](media/change-sku/change-sku-selection.png)

A termékváltozat típusának módosítása egy-két percet is igénybe vehet.

## <a name="next-steps"></a>További lépések

Ha rendelkezik egy erőforráserdővel, és további bizalmi kapcsolatokat szeretne létrehozni a termékváltozat módosítása után, olvassa [el a Kimenő erdőszintű bizalmi kapcsolat létrehozása egy helyszíni tartományban az Azure AD DS-ben (előzetes verzió)][create-trust]című témakört.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
