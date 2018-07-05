---
title: Azure Privileged Identity Management jóváhagyási munkafolyamatai |} A Microsoft Docs
description: További információ a jóváhagyási munkafolyamatokat a Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 025bcd0cde8d73cfdd4d79a77256a1705950f90a
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444646"
---
# <a name="approvals"></a>Approvals

## <a name="overview"></a>Áttekintés

A Privileged Identity Management jóváhagyási jóváhagyást kér az aktiválási szerepkörök konfigurálása, és válasszon egy vagy több felhasználót, vagy delegált jóváhagyók csoportot. Tartsa meg az olvasási megtudhatja, hogyan konfigurálhatja a szerepkörök és a jóváhagyók kiválasztása.


## <a name="new-terminology"></a>Új terminológiája

*Jogosult szerepkör-felhasználó* – egy jogosult szerepkör-felhasználó, jogosult Azure AD-szerepkörhöz van rendelve a szervezeten belüli felhasználók (a szerepkör aktiválása szükséges).

*Jóváhagyó delegált* – delegált jóváhagyó egy vagy több személyek vagy csoportok jóváhagyása felelős az Azure AD-ben kér szerepköröket aktiválni.

## <a name="scenarios"></a>Forgatókönyvek

A privát előzetes verzió a következő eseteket támogatja:

**Egy emelt szintű szerepkör rendszergazda (PRA), a következőket teheti:**

-   [az egyes szerepkörök jóváhagyás engedélyezése](#enable-approval-for-specific-roles)

-   [Adja meg a jóváhagyó felhasználók, illetve a csoportok kérések jóváhagyása](#specify-approver-users-and/or-groups-to-approve-requests)

-   [az összes kiemelt szerepkörökhöz tartozó kérelem és jóváhagyási előzmények megtekintése](#view-request-and-approval-history-for-all-privileged-roles)

**Egy kijelölt jóváhagyó, mint a következő műveletek végezhetők el:**

-   [függőben lévő jóváhagyások (kérelmek) megtekintése](#view-pending-approvals-requests)

-   [a szerepkör jogosultságszint-emeléshez (egyetlen és/vagy tömeges) kérelmeket](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [Adja meg a saját elutasításról indoklása](#provide-justification-for-my-approval/rejection) 

**Jogosult szerepkör-felhasználóként a következő műveletek végezhetők el:**

-   [jóváhagyást igénylő szerepkörök aktiválási kérelmeinek megadása](#request-activation-of-a-role-that-requires-approval)

-   [az aktiválási kérés állapotának megtekintése](#view-the-status-of-your-request-to-activate)

-   [A feladat befejezése az Azure ad-ben, ha az aktiválás jóváhagyva](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>Navigálás

Frissítettük a navigáció támogatására jóváhagyások

![](media/azure-ad-pim-approval-workflow/image001.png)

Az alapértelmezett kezdőlap PIM és az új jóváhagyások dokumentációt kényelmes hozzáférést biztosít.

![](media/azure-ad-pim-approval-workflow/image002.png)

Minden felhasználó részére a PIM, a "Saját naplózási előzmények" című új szakasszal is elérhetővé tettünk. Itt található összes adatot a személyazonosságát. Ez magában foglalja a függőben lévő és befejezett kérelmek, a végrehajtott megoldása a kérések kapcsolatos döntések, és minden az elmúlt szerepkör-aktiválások egy tetszés szerinti helyre.

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>Az egyes szerepkörök jóváhagyás engedélyezése

Jóváhagyási egy adott szerepkör esetében először jelöljön ki címtárbeli szerepkörök a bal oldali navigációs sávon.

![](media/azure-ad-pim-approval-workflow/image004.png)

Keresse meg és válassza a beállítások a címtárbeli szerepkörök bal oldali navigációs

![](media/azure-ad-pim-approval-workflow/image006.png)

Válassza ki a kiemelt szerepkörök:

![](media/azure-ad-pim-approval-workflow/image009.png)

Válassza ki az "Engedélyezés" a a jóváhagyási szakaszban megkövetelése:

![](media/azure-ad-pim-approval-workflow/image011.png)

Ha engedélyezve van, a panel a következő részleteket bővített:

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
Ha nem ad meg minden olyan jóváhagyónak, a PRA(s) válik az alapértelmezett eredményről. Ez a szerepkör az összes aktiválási kérelem jóváhagyása PRA(s) volna szükség.

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>Adja meg a jóváhagyó felhasználók, illetve a csoportok kérések jóváhagyása

Jóváhagyási delegálni, kattintson a "Select jóváhagyóknak" lehetőséget:

![](media/azure-ad-pim-approval-workflow/image015.png)

A jóváhagyók kiválasztása panel betöltésekor, előfordulhat, hogy keresse meg egy adott felhasználó vagy csoport a Keresősáv használatával tetején, vagy az előre megadott listából, majd kattintson a "Select" befejezése:

![](media/azure-ad-pim-approval-workflow/image017.png)

Megjegyzés: Választ, kiválaszthat több felhasználó vagy csoport egyszerre.

A kijelölt kijelölt jóváhagyók listájában jelenik meg az alább látható módon:

![](media/azure-ad-pim-approval-workflow/image019.png)

Jóváhagyó eltávolításához egyszerűen kattintson az Eltávolítás gombra a neve mellett.

További jóváhagyók hozzáadásához ismételje meg a folyamatot.

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>Az összes kiemelt szerepkörökhöz tartozó kérelem és jóváhagyási előzmények megtekintése

Az összes kiemelt szerepkörökhöz tartozó kérelem és jóváhagyási előzmények megtekintéséhez jelölje ki a naplózási előzmények az irányítópulton:

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
Rendezze az adatokat a művelet, és keressen az "Aktiválás Approved"

### <a name="view-pending-approvals-requests"></a>Függőben lévő jóváhagyások (kérelmek) megtekintése

Delegált jóváhagyónak Ha a kérelem jóváhagyásra váró kap e-mail-értesítések. Szeretné megtekinteni ezeket a kérelmeket a PIM-portál, az irányítópult (az új navigáció) jelölje ki a bal oldali navigációs sávon a "Függőben lévő jóváhagyási kérelmek" fülre.

![](media/azure-ad-pim-approval-workflow/image023.png)

Itt láthatja a függőben lévő jóváhagyási kérelmek listáját:

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>A szerepkör jogosultságszint-emeléshez (egyetlen és/vagy tömeges) kérelmeket

Válassza ki a kívánt jóváhagyása vagy elutasítása kérelmeket, és kattintson a gombra a művelet sáv, amely megfelel a döntéseiben:

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>Adja meg a saját elutasításról indoklása

Ekkor megnyílik egy új panel jóváhagyja vagy elutasítja a több kérés egyszerre. Adja meg a döntést, és kattintson jóváhagyása (vagy elutasítása) az alsó vagy a panelen:

![](media/azure-ad-pim-approval-workflow/image029.png)

A folyamat befejeződése után az állapotjelzőben díjainak hozott döntés (ebben a példában a döntést a jóváhagyása):

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>Jóváhagyást igénylő szerepkörök aktiválási kérelmeinek megadása

Kér jóváhagyást igénylő szerepkörök aktiválási is kezdeményezhető a régi PIM navigációs vagy az új navigáció, a szerepkör-aktiválási folyamat változatlan marad. Egyszerűen válasszon egy szerepkört a szerepkörök aktiválásához a listából:

![](media/azure-ad-pim-approval-workflow/image033.png)

Ha a kiemelt szerepkörű multi-factor Authentication hitelesítést igényel, a program felszólítja először végezze el ezt a feladatot:

![](media/azure-ad-pim-approval-workflow/image035.png)

Ha elkészült, kattintson az aktiválás, és indokolniuk (ha szükséges):

![](media/azure-ad-pim-approval-workflow/image037.png)

A kérelmező, amelyben a kérés függőben lévő jóváhagyási értesítést fog kapni:

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>Az aktiválási kérés állapotának megtekintése

Az új navigáció aktiválására vonatkozó, függő kérelem állapotának megtekintése kell elérhetők. A bal oldali navigációs sávon válassza a "Saját kérések" lapon:

![](media/azure-ad-pim-approval-workflow/image041.png)

A kérelem állapotának alapértelmezés szerint a "Függő", de válthat az összes megjelenítése, vagy a kérelem megtagadva.

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>A feladat befejezése az Azure ad-ben, ha az aktiválás jóváhagyva

Miután a kérelmet jóváhagyták, a szerepkör aktív, és előfordulhat, hogy folytatja a szerepkör szükséges munka.

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>További lépések

A visszajelzések fontosak számunkra. Küldheti el nekünk megjegyzéseket vagy visszajelzés nálunk megosztása Itt!
