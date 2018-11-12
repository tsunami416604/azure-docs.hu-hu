---
title: Az Azure Active Directory B2C támogatása |} A Microsoft Docs
description: Fájl támogatási kéréseket az Azure Active Directory B2C módja.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 04eae00d40470d5f6c992d9a0c8c9b0b49d7495f
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007637"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Az Azure Active Directory B2C: Fájl támogatási kérelmek
A fájl támogatási kéréseket az Azure Active Directory (Azure AD) B2C az Azure Portalon, az alábbi lépéseket követve:

1. A B2C-bérlő válthat, amely az Azure-előfizetéssel társítva van egy másik bérlőben. Az utóbbi általában az alkalmazottak vagy az Ön számára létrehozott Azure-előfizetésre való regisztráció során alapértelmezett-bérlő. További tudnivalókért lásd: [hogyan egy Azure-előfizetések kapcsolata az Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
   
    ![Támogatás – kapcsoló bérlők](./media/active-directory-b2c-support/support-switch-dir.png)

3. Váltás a bérlők, után kattintson **súgó + támogatás**.
   
    ![Támogatás – súgó + támogatás](./media/active-directory-b2c-support/support-support.png)
    
4. Kattintson a **új támogatási kérelem**.
   
    ![Támogatás – új](./media/active-directory-b2c-support/support-new.png)
5. Az a **alapjai** panelen használja ezeket az adatokat, és kattintson a **tovább**.
   
   * **Probléma típusa** van **technikai**.
   * Válassza ki a megfelelő **előfizetés**.
   * **Szolgáltatás** van **Active Directory**.
   * Válassza ki a megfelelő **támogatási csomag**. Ha még nincs fiókja, akkor is regisztrálhatnak egy [Itt](https://azure.microsoft.com/support/plans/).
     
     ![Támogatás – alapvető tudnivalók](./media/active-directory-b2c-support/support-basics.png)
6. Az a **probléma** panelen használja ezeket az adatokat, és kattintson a **tovább**.
   
   * Válassza ki a megfelelő **súlyossági** szintjét.
   * **Probléma típusa** van **B2C**.
   * Válassza ki a megfelelő **kategória**.
   * Ismertesse a problémát az a **részletek** mező. Adja meg például a B2C bérlő neve, leírása: a probléma, hibaüzenetek, korrelációs azonosítók (ha elérhető), és így tovább.
   * Az a **időkeret** mezőben adja meg a dátuma és időpontja (beleértve időzóna), amely a probléma lépett fel.
   * A **fájlfeltöltés**, töltse fel az összes képernyőképeket, és úgy gondolja, hogy fájlokat segíti a megoldásban.
     
     ![Támogatás – hiba](./media/active-directory-b2c-support/support-problem.png)
7. Az a **kapcsolattartási adatok** panelen megadhatja a kapcsolattartási adatokat. Kattintson a **Create** (Létrehozás) gombra.
   
    ![Támogatás – ügyfél](./media/active-directory-b2c-support/support-contact.png)
8. Miután a támogatási kérelem küldése, nyomon követheti kattintva **súgó + támogatás** a kezdőpult, majd **támogatási kérelmek kezelése**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Ismert hiba: egy támogatási kérést küldhet a B2C-bérlője kontextusában
Ha a fent vázolt 2 kimaradt, és hozzon létre egy támogatási kérést B2C-bérlője kontextusában próbálja, látni fogja a következő hiba.

> [!IMPORTANT]
> Ne kísérelje meg regisztrálhat egy új Azure-előfizetést a B2C-bérlőben.  
> 
> 

![Nincs előfizetés-támogatás –](./media/active-directory-b2c-support/support-no-sub.png)

