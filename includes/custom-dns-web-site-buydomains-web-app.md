---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ce949caa2b80c08f1015ee21c00197d6a95103c2
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564855"
---
Ha azt szeretné, hogy a tartományhoz, megvásárolhatja a tartományok [Azure felügyeleti portálján](https://portal.azure.com) közvetlenül. A következő lépések segítségével tartománynév vásárlása és hozzárendelése a webalkalmazáshoz.

1. A böngészőben nyissa meg a [Azure felügyeleti portálján](https://portal.azure.com).
2. Az a **Web Apps** fülre, kattintson a nevére, a webalkalmazás válassza **beállítások**, majd válassza ki **egyéni tartományok és SSL**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Az a **egyéni tartományok és SSL** panelen kattintson a **tartományok vásárlása**.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-1.png)
4. Az a **tartományok vásárlása** panelen adja meg a vásárolni kívánt tartomány nevét a szövegmezőbe használatával. A javasolt elérhető tartományok jelennek meg a szövegmező csak fújni. Válassza ki, milyen tartományra szeretne vásárolni.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-2.png)
5. Kattintson a **kapcsolattartási adatai** , és töltse ki a tartomány kapcsolattartási adatok képernyőn.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-3.png)
6. Kattintson a **kiválasztása** a **tartományok vásárlása** panelen, majd a vásárlási információk megjelenik az **vásárlás megerősítése** panelen. Ha fogadja el a jogi feltételeket, és kattintson a **vásárlása**, a rendszer elküldi a rendelést, és a vásárlási folyamat figyelemmel kísérheti a **értesítési**.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-4.png)
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-5.png)
7. Sikeresen megrendelt egy tartományhoz, ha a tartomány kezelése, és rendelje hozzá a webalkalmazáshoz. Kattintson a **"..."** tartományához jobb oldalán. Akkor **vásárlás** vagy **kezelés tartomány**. Kattintson a **kezelés tartomány**, akkor azt is kötése **altartomány** a web App on **kezelés tartományi** panelen.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-6.png)
   
    Konfiguráció befejezését követően az egyéni tartománynév jelenik meg a **állomásnévkötések** szakasz a webalkalmazás.

Ezen a ponton meg kell tudni az egyéni tartománynév megadása a böngészőben, és tekintse meg, hogy sikeresen vesz igénybe, a webes alkalmazás.

