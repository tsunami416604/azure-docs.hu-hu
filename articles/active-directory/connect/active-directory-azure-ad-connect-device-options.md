---
title: 'Az Azure AD Connect: Eszközbeállítások |} Microsoft Docs'
description: Ez a dokumentum részletesen az Azure AD Connectben elérhető eszközök beállításai
services: active-directory
documentationcenter: ''
author: anandy
manager: samueld
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: anandy
ms.openlocfilehash: d592cbb55643099b6b628a44f182d0a079a6c71e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
#<a name="azure-ad-connect-device-options"></a>Az Azure AD Connect: Eszközbeállítások

Az alábbi dokumentáció az Azure AD Connectben elérhető különböző eszköz beállításokkal kapcsolatos információkat nyújt. Az Azure AD Connect segítségével konfigurálhatja a következő két műveleteket: 
* **Hibrid az Azure AD join**: Ha a környezetben egy helyszíni AD kockázatokat, és azt is szeretné az Azure Active Directory által biztosított képességek hasznos, hibrid csatlakozott az Azure AD-eszközöket is létrehozható. Ezek a nyomtatók is, a helyszíni Active Directory és az Azure Active Directory tartományhoz.
* **Eszközvisszaíró**: eszközvisszaíró alapján az AD FS-eszközök feltételes hozzáférésének engedélyezésére szolgál (2012 R2 vagy újabb) védett eszközök

## <a name="configure-device-options-in-azure-ad-connect"></a>Eszközbeállítások konfigurálása az Azure AD Connectben

1.  Futtassa az Azure AD Connect. Az a **további feladatok** lapon jelölje be **eszközbeállítások konfigurálása**.
    ![Eszközbeállítások konfigurálása](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    A Tovább gombra kattintva egy **áttekintése** lap is megjelenik, amely részletesen végrehajtható műveletek.
    ![Áttekintés](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > Adja meg új eszköz a beállításokat a rendszer csak a 1.1.819.0 verzió vagy újabb.

2.  Miután megadta a hitelesítő adatokat az Azure AD, a következőképpen választhatja ki az eszköz beállítások lapon kell elvégezni a műveletet.
    ![Eszköz műveletek](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>További lépések

* [Hibrid az Azure AD join beállítása](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Adja meg / tiltsa le az eszközök visszaírásához.](./active-directory-aadconnect-feature-device-writeback.md)

