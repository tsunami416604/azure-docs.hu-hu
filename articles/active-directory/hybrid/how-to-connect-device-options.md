---
title: 'Az Azure AD Connect: Eszközbeállítások |} A Microsoft Docs'
description: Ez a dokumentum részletesen eszközbeállítások az Azure AD Connectben érhető el
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: c21b4b8996d7dae62c2aa1937c2876a66f6f82bd
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320560"
---
# <a name="azure-ad-connect-device-options"></a>Az Azure AD Connect: Eszköz beállításai

Az alábbi dokumentáció az Azure AD Connectben elérhető különböző eszköz beállításokkal kapcsolatos információkat biztosít. Az Azure AD Connect segítségével konfigurálhatja az alábbi két műveletet: 
* **Hibrid Azure AD-csatlakozás**: Ha a környezetben egy helyszíni AD erőforrás-igényű, és azt szeretné, az Azure AD előnyei, a hibrid Azure AD-csatlakoztatott eszközök valósíthat meg. Ezek az eszközök csatlakozva vannak, mind a helyszíni Active Directory és az Azure Active Directoryban.
* **Eszközvisszaírás**: eszközvisszaírás alapján az AD FS-eszközök feltételes hozzáférésének engedélyezésére használja (2012 R2 vagy újabb) által védett eszközök

## <a name="configure-device-options-in-azure-ad-connect"></a>Eszközbeállítások konfigurálása az Azure AD Connectben

1.  Futtatás az Azure AD Connect. Az a **további feladatok** lapon jelölje be **eszközbeállítások konfigurálása**.  Kattintson a **Tovább** gombra.
    ![Eszközbeállítások konfigurálása](./media/how-to-connect-device-options/deviceoptions.png) 

    A **áttekintése** lap részleteit jeleníti meg.
    ![Áttekintés](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > Az új konfigurálás eszközbeállítások, csak a 1.1.819.0 verzió vagy újabb.

2.  Miután megadta a hitelesítő adatokat az Azure ad-hez, eldöntheti, az eszköz beállításai lapon kell elvégezni a műveletet.
    ![Eszközművelet](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>További lépések

* [Hibrid Azure AD-csatlakozás konfigurálása](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Konfigurálása / eszközvisszaírás letiltása](how-to-connect-device-writeback.md)

