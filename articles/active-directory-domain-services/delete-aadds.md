---
title: Az Azure Active Directory tartományi szolgáltatások törlése | Microsoft dokumentumok
description: Megtudhatja, hogy miként tilthatja le vagy törölheti az Azure Active Directory tartományi szolgáltatások által kezelt tartományt az Azure Portalon
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 595436daa2efbd8e706a539d0a89c3ea98be31ff
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655465"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Azure Active Directory tartományi szolgáltatások által kezelt tartomány törlése az Azure Portalon

Ha már nincs szüksége felügyelt tartományra, törölheti az Azure Active Directory tartományi szolgáltatások (Azure AD DS) példányait. Nincs lehetőség az Azure AD DS által felügyelt tartomány kikapcsolására vagy ideiglenes letiltására. Az Azure AD DS felügyelt tartomány törlése nem törli, vagy más módon hátrányosan befolyásolja az Azure AD-bérlő. Ez a cikk bemutatja, hogyan használhatja az Azure Portalon egy Azure AD DS felügyelt tartomány törléséhez.

> [!WARNING]
> **A törlés végleges, és nem fordítható vissza.**
> Az Azure AD DS felügyelt tartományának törlésekor a következő lépések lépnek be:
>   * A felügyelt tartomány tartományvezérlői kivannak építve, és törlődnek a virtuális hálózatról.
>   * A felügyelt tartomány adatai véglegesen törlődnek. Ezek az adatok magukban foglalnak egyéni operációs rendszereket, csoportházirend-szolgáltatókat, egyéni DNS-rekordokat, szolgáltatástagokat, GMSA-kat stb.
>   * A felügyelt tartományhoz csatlakozó gépek elveszítik a tartománnyal való bizalmi kapcsolatukat, és le kell választani őket a tartományról.
>       * Ezeka gépek nem jelentkezhetnek be vállalati AD-hitelesítő adatokhasználatával. Ehelyett a számítógép helyi rendszergazdai hitelesítő adatait kell használnia.

## <a name="delete-the-managed-domain"></a>A felügyelt tartomány törlése

Az Azure AD DS felügyelt tartományának törléséhez hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon keresse meg és válassza ki az **Azure AD tartományi szolgáltatásokat.**
1. Válassza ki az Azure AD DS felügyelt tartományának nevét, például *a aaddscontoso.com.*
1. Az **Áttekintés** oldalon válassza a **Törlés** elemet. A törlés megerősítéséhez írja be újra a felügyelt tartomány tartománynevét, majd kattintson a **Törlés gombra.**

Az Azure AD DS felügyelt tartományának törlése 15–20 percet vagy többet is igénybe vehet.

## <a name="next-steps"></a>További lépések

Fontolja meg a [visszajelzések megosztását][feedback] az Azure AD DS-ben látni kívánt funkciókhoz.

Ha újra el szeretné kezdeni az Azure AD DS szolgáltatást, olvassa el [az Azure Active Directory tartományi szolgáltatások példányának létrehozása és konfigurálása című témakört.][create-instance]

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
