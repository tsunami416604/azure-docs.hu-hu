---
title: 'Az Azure AD Connect: Eszközbeállítások |} A Microsoft Docs'
description: Ez a dokumentum részletesen eszközbeállítások az Azure AD Connectben érhető el
services: active-directory
documentationcenter: ''
author: billmath
manager: samueld
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: e52f691c75d491897b06a4ebb492d87fda682e38
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917846"
---
#<a name="azure-ad-connect-device-options"></a>Az Azure AD Connect: Eszköz beállításai

Az alábbi dokumentáció az Azure AD Connectben elérhető különböző eszköz beállításokkal kapcsolatos információkat biztosít. Az Azure AD Connect segítségével konfigurálhatja az alábbi két műveletet: 
* **Hibrid Azure AD-csatlakozás**: Ha a környezetben egy helyszíni AD erőforrás-igényű, és azt is szeretné profitál az Azure Active Directory által biztosított képességek, a hibrid Azure AD-csatlakoztatott eszközök valósíthat meg. Ezek a is, amelyek a helyszíni Active Directory és az Azure Active Directory tartományhoz.
* **Eszközvisszaírás**: eszközvisszaírás alapján az AD FS-eszközök feltételes hozzáférésének engedélyezésére használja (2012 R2 vagy újabb) által védett eszközök

## <a name="configure-device-options-in-azure-ad-connect"></a>Eszközbeállítások konfigurálása az Azure AD Connectben

1.  Futtatás az Azure AD Connect. Az a **további feladatok** lapon jelölje be **eszközbeállítások konfigurálása**.
    ![Eszközbeállítások konfigurálása](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    Kattintás a Tovább gombra, a egy **áttekintése** lap is megjelenik, amely a végrehajtható műveletek részleteit.
    ![Áttekintés](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > Az új konfigurálás eszközbeállítások, csak a 1.1.819.0 verzió vagy újabb.

2.  Miután megadta a hitelesítő adatokat az Azure ad-hez, eldöntheti, az eszköz beállításai lapon kell elvégezni a műveletet.
    ![Eszközművelet](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>További lépések

* [Hibrid Azure AD-csatlakozás konfigurálása](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Konfigurálása / eszközvisszaírás letiltása](./active-directory-aadconnect-feature-device-writeback.md)

