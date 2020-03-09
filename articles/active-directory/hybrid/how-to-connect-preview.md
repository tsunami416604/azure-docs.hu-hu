---
title: 'Azure AD Connect: előzetes verzióban elérhető funkciók | Microsoft Docs'
description: Ez a témakör részletesen ismerteti a Azure AD Connect előzetes verziójában elérhető funkciókat.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376096"
---
# <a name="more-details-about-features-in-preview"></a>További információ az előzetes verzió szolgáltatásairól
Ez a témakör a jelenleg előzetes verzióban elérhető szolgáltatások használatát ismerteti.

## <a name="group-writeback"></a>Group writeback (Csoportvisszaíró)
A választható funkciókban a Group visszaírási beállítás lehetővé teszi, hogy az **Office 365-csoportokat** egy olyan erdőhöz visszaírási, amelyen telepítve van az Exchange. Ez egy olyan csoport, amely mindig a felhőben van elsajátítva. Ha helyszíni Exchange-kiszolgálóval rendelkezik, akkor a helyszíni Exchange-postaládával rendelkező felhasználók a csoportokból küldhetnek és fogadhatnak e-maileket.

További információt az Office 365-csoportokról és azok használatáról [itt](https://aka.ms/O365g)találhat.

Az Office 365-csoportok terjesztési csoportként jelennek meg a helyszíni AD DSban. Az új csoport típusának felismeréséhez a helyszíni Exchange-kiszolgálónak az Exchange 2013 8. kumulatív frissítésének (a 2015. márciusi kiadásban) vagy az Exchange 2016-nek kell lennie.

**Megjegyzések az előzetes verzióban**

* A címjegyzék attribútum jelenleg nincs feltöltve az előzetes verzióban. Ezen attribútum nélkül a csoport nem látható a GAL-ben. Az attribútum feltöltésének legegyszerűbb módja az Exchange PowerShell-parancsmag `update-recipient`használata.
* Csak az Exchange-sémával rendelkező erdők érvényes célok a csoportokhoz. Ha nem észlelt Exchange-t, a csoport visszaírási nem lehet engedélyezni.
* Jelenleg csak egyerdős Exchange-szervezet üzemelő példányok támogatottak. Ha több helyszíni Exchange-szervezettel is rendelkezik, akkor az ilyen csoportok számára helyszíni GALSync-megoldásra van szükség, hogy megjelenjenek a többi erdőben.
* A csoport visszaírási funkciója nem kezeli a biztonsági csoportokat és a terjesztési csoportokat.

> [!NOTE]
> A csoport visszaírási prémium szintű Azure AD-előfizetésre van szükség.
> 
>

## <a name="user-writeback"></a>Felhasználói visszaírási
> [!IMPORTANT]
> A felhasználói visszaírási előzetes verziójának funkcióját a rendszer a 2015-es augusztusi frissítésből törölte Azure AD Connect. Ha engedélyezte, tiltsa le ezt a funkciót.
>
>

## <a name="next-steps"></a>Következő lépések
Folytassa [Azure ad Connect egyéni telepítését](how-to-connect-install-custom.md).

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
