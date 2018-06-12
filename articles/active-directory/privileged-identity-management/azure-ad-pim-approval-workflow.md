---
title: Azure Privileged Identity Management jóváhagyási munkafolyamatok |} Microsoft Docs
description: További tudnivalók a jóváhagyási munkafolyamatok Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 6e14d91eae367ef62d1ff2e4cb6ddeb47c9fbbea
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261015"
---
# <a name="approvals"></a>Approvals

## <a name="overview"></a>Áttekintés

Privileged Identity Management-jóváhagyásokkal rendelkező jóváhagyás megkövetelése, az aktiváláshoz szerepkörök konfigurálása, és válasszon egy vagy több felhasználót, vagy delegált jóváhagyóknak csoportot. Megtudhatja, hogyan konfigurálhatók azok a szerepkörök, és válassza ki a jóváhagyóknak olvasási megtartása.


## <a name="new-terminology"></a>Új terminológia

*Jogosult szerepkör felhasználói* – az jogosult szerepkör felhasználó tulajdonképpen egy a szervezeten belül, amely szerint jogosult az Azure AD szerepkörhöz hozzárendelt felhasználó (szerepkör aktiválást igényel).

*Meghatalmazott jóváhagyó* – egy delegált jóváhagyó egy vagy több egyéni felhasználók vagy csoportok jóváhagyása felelős az Azure AD belül kér szerepkörök aktiválásához.

## <a name="scenarios"></a>Forgatókönyvek

A private Preview verziójára a következő szituációkat ismerteti:

**A kiemelt szerepkör rendszergazda (PRA), a következőket teheti:**

-   [Egyes szerepkörök jóváhagyás engedélyezése](#enable-approval-for-specific-roles)

-   [Adja meg a jóváhagyó felhasználók és/vagy a csoportok kérelmek jóváhagyása](#specify-approver-users-and/or-groups-to-approve-requests)

-   [Minden kiemelt szerepkört a kérelem és a jóváhagyási előzményeinek megtekintése](#view-request-and-approval-history-for-all-privileged-roles)

**Egy kijelölt jóváhagyó, mint a következő műveletek végezhetők el:**

-   [Függőben lévő jóváhagyások (kérelmek) megtekintése](#view-pending-approvals-requests)

-   [Jóváhagyhatja vagy elutasíthatja az (egyetlen és tömeges) szerepkör jogosultságszint-emelési kérések](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [Adja meg a jóváhagyási elutasítási indokát](#provide-justification-for-my-approval/rejection) 

**Jogosult szerepkör felhasználóként a következő műveletek végezhetők el:**

-   [A jóváhagyást igénylő szerepkört aktiválási kérelmeinek megadása](#request-activation-of-a-role-that-requires-approval)

-   [Aktiválja a kérelem állapotának megtekintése](#view-the-status-of-your-request-to-activate)

-   [A feladat befejezése az Azure AD, ha az aktiválás jóváhagyva](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>Navigálás

Frissítettük a navigációs jóváhagyások támogatásához

![](media/azure-ad-pim-approval-workflow/image001.png)

Az alapértelmezett kezdőlapján PIM és az új jóváhagyások dokumentációt kényelmes hozzáférést biztosít.

![](media/azure-ad-pim-approval-workflow/image002.png)

Azt is hozzáadott új szakasz PIM, "A naplózási előzmények" minden felhasználó részére. Itt található összes adatot személyazonosságát kapcsolódik. Ez magában foglalja a függőben lévő és befejezett kérések, bármely végrehajtott megoldása a kérelmekkel kapcsolatos döntések, és a múltbeli szerepkör aktiválások egy tetszés szerinti helyre.

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>Egyes szerepkörök jóváhagyás engedélyezése

Ahhoz, hogy az adott szerepkörhöz jóváhagyási, először válassza Directory szerepkörök a bal oldali navigációs sávon.

![](media/azure-ad-pim-approval-workflow/image004.png)

Keresse meg és állítsa be a Directory szerepkörök bal oldali navigációs

![](media/azure-ad-pim-approval-workflow/image006.png)

Válassza ki a kiemelt szerepköröket:

![](media/azure-ad-pim-approval-workflow/image009.png)

Válassza ki az "Engedélyezés" a a jóváhagyási szakasz megkövetelése:

![](media/azure-ad-pim-approval-workflow/image011.png)

Az engedélyezés után a panel a következő részleteket bővített:

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
Ha nem ad meg semmilyen jóváhagyóknak, a PRA(s) vált alapértelmezett leveleket a jóváhagyóknak. A szerepkör összes aktiválási kéréseket jóváhagyni PRA(s) lesz szükség.

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>Adja meg a jóváhagyó felhasználók és/vagy a csoportok kérelmek jóváhagyása

Jóváhagyási delegálása, kattintson a "Select jóváhagyóknak" lehetőség:

![](media/azure-ad-pim-approval-workflow/image015.png)

A Select jóváhagyóknak panel betöltésekor, előfordulhat, hogy keresse meg egy adott felhasználó vagy csoport használja a keresési sávon a lap tetején, vagy az előre megadott listából válassza, majd kattintson a "Select" befejezésekor:

![](media/azure-ad-pim-approval-workflow/image017.png)

Megjegyzés: Előfordulhat, hogy választania több felhasználó vagy csoport egyszerre.

A beállítás a kijelölt jóváhagyóknak listájában jelenik meg az alább látható módon:

![](media/azure-ad-pim-approval-workflow/image019.png)

Jóváhagyó eltávolításához egyszerűen kattintson az Eltávolítás gombra a neve mellett.

További jóváhagyóknak hozzáadásához ismételje meg a műveletet.

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>Minden kiemelt szerepkört a kérelem és a jóváhagyási előzményeinek megtekintése

Minden kiemelt szerepkört kérelem és a jóváhagyási előzményeinek megtekintéséhez jelölje ki a naplózási előzmények az irányítópultról:

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
Rendezze az adatokat a művelet, és keressen a "Jóváhagyott aktiválási"

### <a name="view-pending-approvals-requests"></a>Függőben lévő jóváhagyások (kérelmek) megtekintése

Mint egy delegált jóváhagyó kap értesítő e-mailek jóváhagyásra váró kérelem esetén. Ezeket a kéréseket a PIM portálon megtekintéséhez az irányítópult (az új navigációs) jelölje ki a bal oldali navigációs sávon a "Függőben lévő jóváhagyási kérelmek" lapon.

![](media/azure-ad-pim-approval-workflow/image023.png)

Ott függőben lévő jóváhagyási kérelmek listáját láthatja:

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>Jóváhagyhatja vagy elutasíthatja az (egyetlen és tömeges) szerepkör jogosultságszint-emelési kérések

Válassza ki a kívánt jóváhagyásához vagy elutasításához kérelmek, és kattintson a gombra a megfelelő döntés műveletsávon:

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>Adja meg a jóváhagyási elutasítási indokát

Ekkor megnyílik egy új panel jóváhagyásához vagy elutasításához egyszerre több kérést. Indoklásának beírásához ad helyet a döntést, és kattintson jóváhagyása (vagy megtagadása) alsó vagy a panel:

![](media/azure-ad-pim-approval-workflow/image029.png)

Ha a kérelem befejeződött, az állapotjelzőben hozott döntés fogja tartalmazni (ebben a példában a döntést az jóváhagyása):

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>A jóváhagyást igénylő szerepkört aktiválási kérelmeinek megadása

A jóváhagyást igénylő szerepkört aktiválást kezdeményezhet a régi PIM navigációs, vagy az új navigációs, szerepkör-aktiválási folyamat változatlan marad. Egyszerűen jelölje ki a szerepkör aktiválása szerepkörök közül:

![](media/azure-ad-pim-approval-workflow/image033.png)

Ha egy kiemelt szerepkörhöz többtényezős hitelesítést igényel, kéri, hogy a feladat végrehajtásához először:

![](media/azure-ad-pim-approval-workflow/image035.png)

A befejezést, aktiválás és a adja meg a indoklást (ha szükséges):

![](media/azure-ad-pim-approval-workflow/image037.png)

A kérelmező megjelenik egy értesítés, amelyben a kérés jóváhagyására vár:

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>Aktiválja a kérelem állapotának megtekintése

Aktiválja a függőben lévő kérelmek állapotának megtekintése az új navigációs sávon kell elérni. A bal oldali navigációs sávon válassza ki a "Saját kérések" lapon:

![](media/azure-ad-pim-approval-workflow/image041.png)

A kérelem állapotának alapértelmezett értéke: "függő"állapotba, de láthatja az összes visszaváltható vagy elutasított kérelmek.

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>A feladat befejezése az Azure AD, ha az aktiválás jóváhagyva

Ha elfogadja a kérelmet, a szerepkör aktív, és folytathatja a munkát az ehhez a szerepkörhöz szükséges munka.

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>További lépések

Visszajelzése fontos számunkra. Küldje el nyugodtan megosztása megjegyzések vagy visszajelzést szeretne küldeni nekünk Itt!
