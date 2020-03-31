---
title: 'Azure AD Connect: Eszközbeállítások | Microsoft dokumentumok'
description: Ez a dokumentum részletezi az Azure AD Connectben elérhető eszközbeállításokat
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96ddcdb67ef079cfa23902a1dcb03b0ec61077fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109527"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: Eszközbeállítások

Az alábbi dokumentáció az Azure AD Connect ben elérhető különböző eszközbeállításokkal kapcsolatos információkat tartalmazza. Az Azure AD Connect segítségével konfigurálhatja a következő két műveletet: 
* **Hibrid Azure AD-csatlakozás:** Ha a környezet rendelkezik egy helyszíni AD lábnyom, és szeretné az Azure AD előnyeit, hibrid Azure AD-hez csatlakozott eszközök valósíthatja meg. Ezek az eszközök a helyszíni Active Directoryhoz és az Azure Active Directoryhoz is csatlakoznak.
* **Eszközvisszaírás:** Az eszközvisszaírás az AD FS (2012 R2 vagy újabb) védett eszközök (2012 R2 vagy újabb) eszközökön alapuló feltételes hozzáférés engedélyezésére szolgál

## <a name="configure-device-options-in-azure-ad-connect"></a>Eszközbeállítások konfigurálása az Azure AD Connectben

1.  Futtassa az Azure AD Connect et. A **További feladatok** lapon válassza az **Eszközbeállítások konfigurálása**lehetőséget.  Kattintson a **Tovább** gombra.
    ![Eszközbeállítások konfigurálása](./media/how-to-connect-device-options/deviceoptions.png) 

    Az **Áttekintés** lap megjeleníti a részleteket.
    ![Áttekintés](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > Az új Eszközkonfigurálás idáig csak az 1.1.819.0-s és újabb verzióban érhető el.

2.  Miután megadta az Azure AD hitelesítő adatait, az Eszközbeállítások lapon végrehajtandó műveletet választhatja ki.
    ![Eszközműveletek](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>További lépések

* [Hibrid Azure AD-csatlakozás konfigurálása](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Eszközvisszaírás konfigurálása / letiltása](how-to-connect-device-writeback.md)

