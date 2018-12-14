---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: 67b9c0ba2566206b0e70db51844b21e5d5d3c261
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344594"
---
Miután a a tartománynév-rekordok propagálása megtörtént, a böngésző használatával győződjön meg arról, hogy használható-e az egyéni tartománynév Azure App Service-ben a webalkalmazás eléréséhez képesnek kell lennie.

> [!NOTE]
> A CNAME rekord propagálása a DNS-rendszeren keresztül egy kis ideig is eltarthat. Használhat például egy szolgáltatás <a href="https://www.digwebinterface.com/"> https://www.digwebinterface.com/ </a> annak ellenőrzéséhez, hogy a CNAME érhető el.
> 
> 

Ha nem rendelkezik már hozzáadva a webalkalmazás a Traffic Manager-végpontként kell ehhez előtt a névfeloldás működik, mint a egyéni tartomány nevét, a Traffic Manager. A TRAFFIC Manager ezután továbbítja a webalkalmazás. Olvassa el az [telepítése és törlése végpontok](../articles/traffic-manager/traffic-manager-endpoints.md) hozzáadása a webes alkalmazás egy végpontot a Traffic Manager-profil az.

> [!NOTE]
> Ha a webalkalmazás nem találja a végpont hozzáadásakor, győződjön meg arról, hogy konfigurálva van a **Standard** App Service-csomag mód. Használjon **Standard** mód a webalkalmazás annak érdekében, hogy a Traffic Managerrel működnek.
> 
> 

1. A böngészőben nyissa meg a [az Azure Portal](https://portal.azure.com).
2. Az a **Web Apps** fülre, kattintson a nevére, a webalkalmazás válassza **beállítások**, majd válassza ki **egyéni tartományok**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Az a **egyéni tartományok** panelen kattintson a **gazdagépnév hozzáadása**.
4. Használja a **állomásnév** szövegmezők adja meg a webes alkalmazás társítása a Traffic Manager szolgáltatásbeli tartománynévre.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Kattintson a **ellenőrzése** a tartomány nevét konfigurációjának mentéséhez.
6. Gomb megnyomásakor **ellenőrzése** Azure elindít tartomány-ellenőrzés munkafolyamat. Ez a tartomány tulajdonjogának, valamint a rendelkezésre állás és a jelentés sikeres állomásnév vagy a hiba elhárításához követendő útmutatást a hiba részletei ellenőrzi.    
7. Sikeres ellenőrzés esetén **gazdagépnév hozzáadása** gomb aktívvá válik, és nem fogja tudni hozzárendelése gazdagépnevét. Most nyissa meg böngészőben az egyéni tartomány nevét. Meg kell jelennie az alkalmazás fut, az egyéni tartománynév használatával. 
   
   Konfiguráció befejezését követően az egyéni tartománynév jelenik meg a **tartománynevek** szakasz a webalkalmazás.

Ezen a ponton kell lennie a Traffic Manager szolgáltatásbeli tartománynévre adja meg a böngészőben, és tekintse meg, hogy sikeresen vesz igénybe, a webes alkalmazás.

