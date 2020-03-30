---
title: 'Azure AD Connect: Funkciók előzetes verzióban | Microsoft dokumentumok'
description: Ez a témakör részletesebben ismerteti az Okat, amelyek előzetes verzióban az Azure AD Connect.
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
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7def733a80aea1be77825bb9069217f5f43e003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261280"
---
# <a name="more-details-about-features-in-preview"></a>További részletek az előzetes verzióban elérhető funkciókról
Ez a témakör az előzetes verzióban elérhető funkciók használatát ismerteti.

## <a name="group-writeback"></a>Group writeback (Csoportvisszaíró)
A választható funkciókban a csoportvisszaírás beállításával visszaírhatja az **Office 365-csoportokat** egy olyan erdőbe, amelyen telepítve van az Exchange. Ez egy olyan csoport, amely mindig elsajátította a felhőben. Ha az Exchange helyszíni, majd írhatja vissza ezeket a csoportokat a helyszíni, így a felhasználók egy helyszíni Exchange-postafiók küldhet és fogadhat e-maileket ezekből a csoportokból.

Az Office 365-csoportokról és azok használatáról itt olvashat [bővebben.](https://aka.ms/O365g)

Az Office 365-csoport a helyszíni AD DS-ben terjesztési csoportként jelenik meg. A helyszíni Exchange-kiszolgálónak az Exchange 2013 8.

**Jegyzetek az előnézet alatt**

* A címjegyzék attribútum a rendszerben jelenleg nincs feltöltve. E tulajdonság nélkül a csoport nem látható a gal-ban. Az attribútum feltöltésének legegyszerűbb módja az Exchange PowerShell-parancsmag `update-recipient`használata.
* Csak az Exchange-sémával rendelkező erdők érvényesek a csoportokszámára. Ha a rendszer nem észlelt Exchange-et, a csoportvisszaírás nem engedélyezhető.
* Jelenleg csak az egyerdős Exchange-szervezet telepítései támogatottak. Ha egynél több Exchange-szervezet van a helyszínen, akkor szüksége van egy helyszíni GALSync-megoldásra ahhoz, hogy ezek a csoportok megjelenjenek a többi erdőben.
* A Csoport visszaírása szolgáltatás nem kezeli a biztonsági csoportokat vagy a terjesztési csoportokat.

> [!NOTE]
> Az Azure AD Premium-előfizetés szükséges a csoport visszaírásához.
> 
>

## <a name="user-writeback"></a>Felhasználói visszaírás
> [!IMPORTANT]
> A felhasználói visszaírási előnézeti funkció az Azure AD Connect 2015 augusztusi frissítésében lett eltávolítva. Ha engedélyezte, akkor tiltsa le ezt a funkciót.
>
>

## <a name="next-steps"></a>További lépések
Folytassa [az Azure AD Connect egyéni telepítését.](how-to-connect-install-custom.md)

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
