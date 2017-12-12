---
title: "Az Azure AD Connect: Preview szolgáltatásai |} Microsoft Docs"
description: "Ez a témakör ismerteti a további részletek funkciók még csak előzetes verziójúak, az Azure AD Connectben."
services: active-directory
documentationcenter: 
author: andkjell
manager: mtillman
editor: 
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: cba65b0fe2c2fa0ed92cd2f95464e300a29a7f54
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="more-details-about-features-in-preview"></a>További információt az előzetes funkciók
Ez a témakör ismerteti, hogyan jelenleg előzetes funkciók használni.

## <a name="group-writeback"></a>Csoportvisszaírás
A csoportok visszaírásához választható funkciók a beállítás lehetővé teszi a visszaírási **Office 365-csoportok** telepített Exchange az erdőhöz. Ez az egy csoportot, amely mindig értékűre a felhőben. Ha a helyszíni Exchange-hez, majd írhat vissza ezeket a csoportokat a helyszínen, a felhasználók egy helyszíni Exchange postaládával küldhet és fogadhat e-mailek ezekből a csoportokból.

További információ az Office 365-csoportokat és a használatukat található [Itt](http://aka.ms/O365g).

Az Office 365-csoportok egy jelenik meg a helyszíni terjesztési csoport Active Directory tartományi Szolgáltatásokban. A helyszíni Exchange server Exchange 2013-as összesítő frissítés 8 (2015. márciusi jelent) vagy az Exchange 2016 ismeri fel ezt az új csoporttípus kell lennie.

**Az előzetes megjegyzések**

* A cím könyv attribútum jelenleg nincs feltöltve a Preview. Ez az attribútum nélkül a csoport nincs a globális Címlista látható. Ez az attribútum feltöltéséhez legegyszerűbb módja a Exchange PowerShell-parancsmag `update-recipient`.
* Csak az Exchange-séma-erdők érvényes célok csoportokhoz. Ha nincs Exchange észlelt, csoportvisszaírásról akkor nem lehet engedélyezni.
* Csak egyerdős Exchange-szervezet telepítések jelenleg támogatottak. Ha több mint egy szervezet helyszíni Exchange-, majd ezekhez a csoportokhoz, a más erdőkben megjelenik egy helyszíni GALSync megoldásra van szüksége.
* A csoport visszaírási szolgáltatása nem kezeli a biztonsági csoportok vagy terjesztési csoportok.

> [!NOTE]
> Prémium szintű Azure AD-előfizetés szükség a csoportok visszaírásához.
> 
>

## <a name="user-writeback"></a>Felhasználó-visszaírás
> [!IMPORTANT]
> A felhasználó-visszaírás előzetes verziójú funkciók el lett távolítva a 2015. augusztus frissítés az Azure AD Connect. Ha engedélyezte azt, majd tiltsa le ezt a szolgáltatást.
>
>

## <a name="next-steps"></a>Következő lépések
Továbbra is a [az Azure AD Connect egyéni telepítési](active-directory-aadconnect-get-started-custom.md).

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
