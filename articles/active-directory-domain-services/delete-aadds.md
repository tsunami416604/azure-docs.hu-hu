---
title: Azure Active Directory Domain Services letiltása | Microsoft Docs "
description: Megtudhatja, hogyan tilthatja le a Azure Active Directory Domain Servicest a Azure Portal használatával
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: iainfou
ms.openlocfilehash: 05c4e73c56b79c6e313ea15124bd0f3d17b2fc70
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842606"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Active Directory Domain Services letiltása a Azure Portal használatával

Ha már nincs szüksége felügyelt tartományra, törölheti Azure Active Directory Domain Services (Azure AD DS) példányát. Nincs lehetőség az Azure AD DS felügyelt tartomány kikapcsolására vagy ideiglenes letiltására. Az Azure AD DS felügyelt tartomány törlése nem törli vagy más módon nem befolyásolja az Azure AD-bérlőt. Ez a cikk bemutatja, hogyan törölhet egy Azure AD DS felügyelt tartományt a Azure Portal használatával.

> [!WARNING]
> **A törlés végleges, ezért nem vonható vissza.**
> Ha töröl egy Azure AD DS felügyelt tartományt, a következő lépések történnek:
>   * A felügyelt tartományhoz tartozó tartományvezérlők kiépítve és el lettek távolítva a virtuális hálózatról.
>   * A felügyelt tartományon tárolt adatértékek véglegesen törlődnek. Ezek az adatok a létrehozott egyéni szervezeti egységeket, csoportházirend-objektumokat, egyéni DNS-rekordokat, egyszerű szolgáltatásokat, csoportosan felügyelt szolgáltatásfiókokat stb. tartalmazzák.
>   * A felügyelt tartományhoz csatlakozó gépek elveszítik megbízhatósági kapcsolataikat a tartománnyal, és nem kell a tartományhoz csatlakozniuk.
>       * Ezeket a gépeket nem lehet bejelentkezni a vállalati AD hitelesítő adataival. Ehelyett a számítógép helyi rendszergazdai hitelesítő adatait kell használnia.

## <a name="delete-the-managed-domain"></a>A felügyelt tartomány törlése

Azure AD DS felügyelt tartomány törléséhez hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza a **Azure ad Domain Services**lehetőséget.
1. Válassza ki az Azure AD DS felügyelt tartományának nevét, például *contoso.com*.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet. A törlés megerősítéséhez írja be újra a felügyelt tartomány tartománynevét, majd válassza a **Törlés**lehetőséget.

Az Azure AD DS felügyelt tartományának törléséhez akár 15-20 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések

Érdemes [megoszthatja][feedback] az Azure ad DSban megtekinteni kívánt funkciók visszajelzéseit.

Ha újra szeretné kezdeni az Azure AD DSét, tekintse meg a [Azure Active Directory Domain Services példány létrehozása és konfigurálása][create-instance]című témakört.

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
