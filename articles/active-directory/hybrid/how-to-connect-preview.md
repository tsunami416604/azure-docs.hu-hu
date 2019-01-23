---
title: 'Azure AD Connect: Az előzetes verzió szolgáltatásai |} A Microsoft Docs'
description: Ez a témakör ismerteti a több részletei funkciót, amely az Azure AD Connectben előzetes verzióként érhetők el.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 86c45379097f51569d734a46f50edd916428c48a
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473061"
---
# <a name="more-details-about-features-in-preview"></a>További információt az előzetes verzió szolgáltatásai
Ez a témakör ismerteti a szolgáltatások használatának jelenleg előzetes verzióban érhető el.

## <a name="group-writeback"></a>Group writeback (Csoportvisszaíró)
A csoportok visszaírásához a választható funkciók a beállítás lehetővé teszi a jelszóvisszaíró **Office 365-csoportok** telepített Exchange-erdőhöz. Ez az egy csoport, amely mindig van egyedalakzatokkal a felhőben. Ha a helyszíni Exchange, majd írhat vissza ezeket a csoportokat a helyszíni, egy a helyszíni Exchange-postaládájába rendelkező felhasználók küldhet és fogadhat e-mailek ezekből a csoportokból.

Office 365-csoportok és azok használatáról további információt találhat [Itt](https://aka.ms/O365g).

Office 365-csoport jelenik meg egy terjesztési csoportot a helyi Active Directory tartományi Szolgáltatásokban. Az a helyszíni Exchange-kiszolgáló az Exchange 2013 összegző frissítés 8 (kiadott a 2015. március) vagy az Exchange 2016 ismeri fel ezt az új csoporttípus kell lennie.

**Megjegyzések az előzetes verzióban**

* A cím könyv attribútum jelenleg nem töltődik be az előzetes verzióra. Ez az attribútum nélkül a csoport nem áll a globális Címlista látható. Töltse ki ezt az attribútumot a legegyszerűbb módja az, hogy az Exchange PowerShell-parancsmaggal `update-recipient`.
* Csak az Exchange-séma erdőkben csoportok érvényes céljainak. Ha nincs Exchange észlelt, majd csoportvisszaírás, nem lehet engedélyezni.
* Csak egyerdős Exchange-szervezet környezetek jelenleg támogatottak. Ha egynél több szervezet a helyszíni Exchange, akkor ezeket a csoportokat a többi erdőben lévő jelenik meg egy helyszíni GALSync megoldást kell.
* A csoport-visszaírási funkciót nem kezeli a biztonsági csoportok vagy terjesztési csoportok.

> [!NOTE]
> Prémium szintű Azure AD-előfizetés szükség a csoportok visszaírásához.
> 
>

## <a name="user-writeback"></a>Felhasználó-visszaírás
> [!IMPORTANT]
> A felhasználó-visszaírás előzetes verziójú funkció el lett távolítva a 2015 augusztus frissítés az Azure AD Connect. Ha ez engedélyezve van, majd tiltsa le ezt a szolgáltatást.
>
>

## <a name="next-steps"></a>További lépések
Továbbra is a [az Azure AD Connect egyéni telepítési](how-to-connect-install-custom.md).

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
