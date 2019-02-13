---
title: Jelentkezzen be kapcsolatos hibák elhárítása Azure Active Directory-jelentések segítségével |} A Microsoft Docs
description: Ismerje meg, jelentkezzen be kapcsolatos hibák elhárítása Azure Active Directory-jelentések használata az Azure Portalon
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22126114f2d4fcb865485d9cebc69f0e35f70201
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198473"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Útmutató: Jelentkezzen be kapcsolatos hibák elhárítása Azure Active Directory-jelentések segítségével

A [bejelentkezésekre](concept-sign-ins.md) az Azure Active Directory (Azure AD) lehetővé teszi a szervezeten belül, az alkalmazásokhoz való hozzáférés kezelése körül kérdésekre adott válaszok többek között:

- Milyen egy adott felhasználó bejelentkezési mintázata?
- Hány felhasználó jelentkezett be egy adott héten?
- Milyen állapotúak ezek a bejelentkezések?


Emellett a bejelentkezési jelentések is segíthet a szervezet felhasználói bejelentkezési hibák elhárítása. Ebben az útmutatóban megismerheti, hogyan bejelentkezési hiba történt a bejelentkezések jelentés elkülönítésére, és ennek segítségével a hiba alapvető okának megismeréséhez.

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

* Prémium verzió (P1 vagy P2) licenccel rendelkező Azure AD-bérlővel. Lásd: [Ismerkedés az Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) az Azure Active Directory-kiadás frissítése.
* A felhasználó, aki az a **globális rendszergazdai**, **biztonsági rendszergazda**, **biztonsági olvasó** vagy **jelentést olvasó** szerepkör a bérlő számára. Emellett bármely felhasználó hozzáférhet a saját bejelentkezések. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>A bejelentkezések jelentés használatával bejelentkezési hibák elhárítása

1. Keresse meg a [az Azure portal](https://portal.azure.com) válassza ki azt a címtárat.
2. Válassza ki **Azure Active Directory** válassza **bejelentkezések** származó a **figyelés** szakaszban. 
3. A megadott szűrők segítségével leszűkítheti a hiba, a felhasználónév vagy objektumazonosító, az alkalmazás nevét vagy a dátum. Emellett válassza **hiba** a a **állapot** legördülő csak a sikertelen bejelentkezések megjelenítéséhez. 

    ![Szűrés eredményei](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Azonosítsa a sikertelen bejelentkezési, amelyet szeretne vizsgálni, és válassza ki azt. Ekkor megnyílik a további részletei ablak megnyitásához a sikertelen bejelentkezési kapcsolatos további információkat. Jegyezze fel a **bejelentkezési hiba kódja** és **a hiba oka**. 

    ![Rekord kiválasztása](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Ezt az információt is megtalálhatja a **hibaelhárítás és támogatás** a részleteket megjelenítő lapon.

    ![Hibaelhárítás és támogatás](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. A hiba okának leírja a hibát. Például ebben az esetben, a hiba oka van **érvénytelen felhasználónév vagy jelszó vagy érvénytelen helyszíni felhasználónév vagy jelszó**. Ez azt jelenti, hogy a felhasználó által megadott egy helytelen felhasználónév vagy jelszó jelentkezzen be az Azure Portalon. A javítás, hogy egyszerűen csak jelentkezzen be újra a helyes felhasználónevet és jelszót.

7. További információt a szervizeléshez, így a hibakódot, keressen rá **50126** ebben a példában a a [bejelentkezések hibakódjai referencia](reference-sign-ins-error-codes.md). 

8. Ha semmi más nem, vagy a probléma továbbra is fennáll, annak ellenére, hogy a javasolt művelet kurzust [hozzon létre egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) leírt lépések végrehajtásával a **hibaelhárítás és támogatás** fülre. 

## <a name="next-steps"></a>További lépések

* [Bejelentkezések hibakódok hivatkozhat.](reference-sign-ins-error-codes.md)
* [Bejelentkezések jelentés – áttekintés](concept-sign-ins.md)
