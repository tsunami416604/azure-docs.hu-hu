---
title: Azure Active Directory Domain Services letiltása | Microsoft Docs
description: Azure Active Directory Domain Services letiltása a Azure Portal használatával
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: d5917ad94212c8b18d4362528bdfbafb02aec808
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171972"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Active Directory Domain Services letiltása a Azure Portal használatával
Ez a cikk bemutatja, hogyan tilthatja le Azure Active Directory (AD) tartományi szolgáltatásokat az Azure AD-címtárhoz a Azure Portal használatával.

> [!WARNING]
> **A törlés végleges, és nem vonható vissza.**
> Körültekintően járjon el! A felügyelt tartomány törlése után:
>   * A felügyelt tartományhoz tartozó tartományvezérlők kiépítve és el lettek távolítva a virtuális hálózatról.
>   * A felügyelt tartományon tárolt adatértékek véglegesen törlődnek. Ide tartoznak a felügyelt tartományon létrehozott egyéni szervezeti egységek, csoportházirend-objektumok, egyéni DNS-rekordok, egyszerű szolgáltatások, csoportosan felügyelt szolgáltatásfiókokat stb.
>   * A felügyelt tartományhoz csatlakozó gépek elveszítik megbízhatósági kapcsolataikat a tartománnyal, és nem kell a tartományhoz csatlakozniuk.
>   * Ezeket a gépeket nem lehet bejelentkezni a vállalati AD hitelesítő adataival. Használja helyette a számítógép helyi rendszergazdai hitelesítő adatait.
> A felügyelt tartomány törlése nem törli az Azure AD-címtárat, vagy más módon hátrányosan befolyásolja a könyvtárat.

A Azure AD Domain Services felügyelt tartomány törléséhez hajtsa végre a következő lépéseket:
1. Navigáljon a Azure Portal [Azure ad Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) -bővítményéhez.
2. Kattintson a felügyelt tartomány nevére.

    ![Törlendő tartomány kiválasztása](./media/getting-started/domain-services-delete-select-domain.png)

3. Az **Áttekintés** lapon kattintson a **Törlés** gombra.

    ![Tartomány törlése](./media/getting-started/domain-services-delete-domain.png)

4. A törlés megerősítéséhez írja be a felügyelt tartomány DNS-tartománynevét. Ha elkészült, kattintson a **Törlés** gombra.

    ![Tartomány jóváhagyásának törlése](./media/getting-started/domain-services-delete-domain-confirm.png)

A felügyelt tartomány törlése 15-20 percet is igénybe vehet.

Érdemes [](contact-us.md) megfontolni a visszajelzések megosztását, hogy megértsük, milyen funkciók segíthetnek a Azure ad Domain Services jövőbeli kiszámításában. Ez a visszajelzés segít nekünk a szolgáltatás fejlesztésében, hogy jobban megfeleljen az üzembe helyezési igényeknek és a használati eseteknek.
