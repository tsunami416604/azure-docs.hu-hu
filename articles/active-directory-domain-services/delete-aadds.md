---
title: Azure Active Directory Domain Services törlése | Microsoft Docs
description: Megtudhatja, hogyan tilthatja le vagy törölhet egy Azure Active Directory Domain Services felügyelt tartományt a Azure Portal használatával
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: bece22fa0b9d41f29f8829d47a26f4ae2075feac
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86040112"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Azure Active Directory Domain Services felügyelt tartomány törlése a Azure Portal használatával

Ha már nincs szüksége Azure Active Directory Domain Services (Azure AD DS) felügyelt tartományra, törölheti. Nincs lehetőség az Azure AD DS felügyelt tartomány kikapcsolására vagy ideiglenes letiltására. A felügyelt tartomány törlése nem törli vagy más módon nem befolyásolja az Azure AD-bérlőt.

Ez a cikk bemutatja, hogyan törölheti a felügyelt tartományokat a Azure Portal használatával.

> [!WARNING]
> **A törlés végleges, ezért nem vonható vissza.**
> 
> Felügyelt tartomány törlésekor a következő lépések történnek:
>   * A felügyelt tartományhoz tartozó tartományvezérlők kiépítve és el lettek távolítva a virtuális hálózatról.
>   * A felügyelt tartományon tárolt adatértékek véglegesen törlődnek. Ezek az adatok a létrehozott egyéni szervezeti egységeket, csoportházirend-objektumokat, egyéni DNS-rekordokat, egyszerű szolgáltatásokat, csoportosan felügyelt szolgáltatásfiókokat stb. tartalmazzák.
>   * A felügyelt tartományhoz csatlakozó gépek elveszítik megbízhatósági kapcsolataikat a tartománnyal, és nem kell a tartományhoz csatlakozniuk.
>       * Ezeket a gépeket nem lehet bejelentkezni a vállalati AD hitelesítő adataival. Ehelyett a számítógép helyi rendszergazdai hitelesítő adatait kell használnia.

## <a name="delete-the-managed-domain"></a>A felügyelt tartomány törlése

Felügyelt tartomány törléséhez hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza a **Azure ad Domain Services**lehetőséget.
1. Válassza ki a felügyelt tartomány nevét, például *aaddscontoso.com*.
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet. A törlés megerősítéséhez írja be újra a felügyelt tartomány tartománynevét, majd válassza a **Törlés**lehetőséget.

A felügyelt tartomány törlése 15-20 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések

Érdemes [megoszthatja][feedback] az Azure ad DSban megtekinteni kívánt funkciók visszajelzéseit.

Ha újra szeretné kezdeni az Azure AD DSét, tekintse meg a [Azure Active Directory Domain Services felügyelt tartomány létrehozása és konfigurálása][create-instance]című témakört.

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
