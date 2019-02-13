---
title: 'Azure AD Connect: Eszközbeállítások |} A Microsoft Docs'
description: Ez a dokumentum részletesen eszközbeállítások az Azure AD Connectben érhető el
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
ms.openlocfilehash: b8f397401581cc1bc947f1b19ce248c17fba26f4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56160722"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: Eszközbeállítások

Az alábbi dokumentáció az Azure AD Connectben elérhető különböző eszköz beállításokkal kapcsolatos információkat biztosít. Az Azure AD Connect segítségével konfigurálhatja az alábbi két műveletet: 
* **Hibrid Azure AD-csatlakozás**: Ha a környezetben egy helyszíni AD erőforrás-igényű, és azt szeretné, az Azure AD előnyei, a hibrid Azure AD-csatlakoztatott eszközök valósíthat meg. Ezek az eszközök csatlakozva vannak, mind a helyszíni Active Directory és az Azure Active Directoryban.
* **Eszközvisszaírás**: Eszközvisszaírás alapján az AD FS-eszközök feltételes hozzáférésének engedélyezésére használja (2012 R2 vagy újabb) által védett eszközök

## <a name="configure-device-options-in-azure-ad-connect"></a>Eszközbeállítások konfigurálása az Azure AD Connectben

1.  Futtatás az Azure AD Connect. Az a **további feladatok** lapon jelölje be **eszközbeállítások konfigurálása**.  Kattintson a **tovább**.
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

