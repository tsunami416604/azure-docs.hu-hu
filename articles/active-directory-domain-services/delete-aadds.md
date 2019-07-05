---
title: Az Azure Active Directory Domain Services letiltása |} A Microsoft Docs
description: Tiltsa le az Active Directory Domain Servicest az Azure portal használatával
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
ms.openlocfilehash: 2f9b7f5342753c27bd8159056c8f162a5a4b78d3
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473552"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Tiltsa le az Active Directory Domain Servicest az Azure portal használatával
Ez a cikk bemutatja, hogyan tiltsa le az Azure Active Directory (AD) Domain Servicest az Azure AD-címtár az Azure portal használatával.

> [!WARNING]
> **Törlése végleges, és nem vonható vissza.**
> Legyen óvatos! Ha Ön a felügyelt tartomány törlése:
>   * A felügyelt tartományhoz tartozó tartományvezérlőket Szolgáltatáskulcs és a virtuális hálózatról.
>   * A felügyelt tartományban lévő adatok véglegesen törlődnek. Ez tartalmazza a egyéni szervezeti egységek, a csoportházirend-objektumok, az egyéni DNS-rekordok, a szolgáltatásnevek, a felügyelt tartományon létrehozott csoportosan felügyelt szolgáltatásfiókokat stb.
>   * A felügyelt tartományhoz csatlakoztatott gépek elveszíti a megbízhatósági kapcsolattal a tartományhoz, és kell leszerelni a tartományból.
>   * Ezek a gépek AD vállalati hitelesítő adatokkal, nem tud bejelentkezni. Ehelyett használja a helyi rendszergazdai hitelesítő adatokat a gép.
> A felügyelt tartomány törlése nem törli az Azure AD-címtár vagy egyéb negatív hatással lehet a címtárban.

Hajtsa végre az alábbi lépéseket az Azure AD Domain Services felügyelt tartomány törlése:
1. Keresse meg a [az Azure AD Domain Services-bővítmény](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) az Azure Portalon.
2. Kattintson a felügyelt tartomány nevére.

    ![Select domain to delete](./media/getting-started/domain-services-delete-select-domain.png)

3. Az a **áttekintése** lap, kattintson a **törlése** gombra.

    ![Delete domain](./media/getting-started/domain-services-delete-domain.png)

4. A törlés megerősítéséhez írja be a felügyelt tartomány DNS-tartománynév. Kattintson a **törlése** gombra, amikor elkészült.

    ![Megerősítés tartomány törlése](./media/getting-started/domain-services-delete-domain-confirm.png)

A felügyelt tartomány körülbelül 15 – 20 perc múlva törlődik.

Fontolja meg [visszajelzés megosztása](contact-us.md) segítsen megérteni, milyen funkciókat segít az Azure AD tartományi szolgáltatásokat a jövőben választotta. A visszajelzése segít fejlesztheti tovább a szolgáltatást, hogy jobban illeszkedjen az üzembe helyezésben, és a használati eseteket.
