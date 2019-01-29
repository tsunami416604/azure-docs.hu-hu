---
title: Az Azure Active Directory B2C támogatása |} A Microsoft Docs
description: Fájl támogatási kéréseket az Azure Active Directory B2C módja.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c40c1357b74b05f54bb2c2837554d9375e06f246
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163364"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: Fájltámogatási kérések
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

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Ismert hiba: Egy támogatási kérést küldhet a B2C-bérlője kontextusában
Ha a fent vázolt 2 kimaradt, és hozzon létre egy támogatási kérést B2C-bérlője kontextusában próbálja, látni fogja a következő hiba.

> [!IMPORTANT]
> Ne kísérelje meg regisztrálhat egy új Azure-előfizetést a B2C-bérlőben.  
> 
> 

![Nincs előfizetés-támogatás –](./media/active-directory-b2c-support/support-no-sub.png)

