---
title: E-mail értesítések az Azure AD tartományi szolgáltatásaihoz | Microsoft Dokumentumok"
description: Megtudhatja, hogy miként konfigurálhatja az e-mail értesítéseket az Azure Active Directory tartományi szolgáltatások által kezelt tartományban felmerülő problémákra való figyelmeztetésre
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 8c6d59889e572893877f2178cade57e07aa91413
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654793"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>E-mail értesítések konfigurálása az Azure Active Directory tartományi szolgáltatásokkal kapcsolatos problémákhoz

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) által felügyelt tartomány állapotát az Azure platform figyeli. Az Azure Portal állapotlapján látható a felügyelt tartomány riasztásai. Győződjön meg arról, hogy a problémák időben válaszolnak, az e-mail értesítések beállíthatók úgy, hogy jelentést készítsenek az állapotriasztásokról, amint az Azure AD DS felügyelt tartományában észlelik őket.

Ez a cikk bemutatja, hogyan konfigurálhatja az e-mail értesítés címzettjeit egy Azure AD DS felügyelt tartományhoz.

## <a name="email-notification-overview"></a>E-mail értesítés – áttekintés

Az Azure AD DS felügyelt tartományával kapcsolatos problémák riasztásához konfigurálhatja az e-mail értesítéseket. Ezek az e-mail értesítések határozzák meg az Azure AD DS felügyelt tartomány, amely a riasztás jelen van, valamint megadja az észlelési időt, és egy linket az állapotlap az Azure Portalon. Ezután a problémák megoldásához kövesse a mellékelt hibaelhárítási tanácsokat.

A következő példa e-mail értesítés azt jelzi, hogy kritikus figyelmeztetés vagy riasztás jött létre az Azure AD DS felügyelt tartományban:

![Példa e-mail értesítésre](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Mielőtt az üzenetben található hivatkozásokra kattintana, mindig győződjön meg arról, hogy az e-mail egy ellenőrzött Microsoft-feladótól származik. Az e-mail értesítések `azure-noreply@microsoft.com` mindig a címről származnak.

### <a name="why-would-i-receive-email-notifications"></a>Miért kapok e-mailes értesítéseket?

Az Azure AD DS e-mail értesítéseket küld a felügyelt tartomány fontos frissítéseiről. Ezek az értesítések csak a szolgáltatást érintő sürgős kérdésekre szolgálnak, és azokkal azonnal foglalkozni kell. Minden egyes e-mail értesítést az Azure AD DS felügyelt tartományának riasztása vált ki. A riasztások az Azure Portalon is megjelennek, és megtekinthetők az [Azure AD DS állapotlapján.][check-health]

Az Azure AD DS nem küld e-maileket hirdetésekhez, frissítésekhez vagy értékesítési célokra.

### <a name="when-will-i-receive-email-notifications"></a>Mikor kapok e-mailes értesítéseket?

Az értesítést azonnal elküldi, ha egy [új riasztás][troubleshoot-alerts] tetsző egy Azure AD DS felügyelt tartományban. Ha a riasztás nem oldódott fel, négynaponta további e-mail értesítéseket küldünk emlékeztetőként.

### <a name="who-should-receive-the-email-notifications"></a>Ki kapja meg az e-mailes értesítéseket?

Az Azure AD DS e-mail címzettjeinek listájának olyan személyekből kell állnia, akik felügyelhetik és módosíthatják a felügyelt tartományt. Ezt az e-mail listát úgy kell tekinteni, mint az "első válaszadók" az esetleges riasztások és problémák.

Legfeljebb öt további e-mail címzettet adhat hozzá az e-mailes értesítésekhez. Ha ötnél több címzettet szeretne az e-mailértesítésekhez, hozzon létre egy terjesztési listát, és adja hozzá helyette az értesítési listához.

Azt is beállíthatja, hogy az Azure AD-címtár összes *globális rendszergazdája* és az *AAD DC rendszergazdák* csoport minden tagja e-mail-értesítéseket kapjon. Az Azure AD DS csak legfeljebb 100 e-mail címre küld értesítést, beleértve a globális rendszergazdák és az AAD DC-rendszergazdák listáját.

## <a name="configure-email-notifications"></a>E-mail-értesítések konfigurálása

A meglévő e-mail értesítési címzettek áttekintéséhez vagy további címzettek hozzáadásához hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon keresse meg és válassza ki az **Azure AD tartományi szolgáltatásokat.**
1. Válassza ki az Azure AD DS felügyelt tartományát, például *a aaddscontoso.com.*
1. Az Azure AD DS erőforrásablak bal oldalán válassza az **Értesítési beállítások lehetőséget.** Megjelennek az e-mailértesítések meglévő címzettjei.
1. E-mail címzett hozzáadásához írja be az e-mail címet a további címzettek táblázatába.
1. Ha végzett, válassza a **Mentés** lehetőséget a felső navigációs sávon.

> [!WARNING]
> Az értesítési beállítások módosításakor a teljes Azure AD DS felügyelt tartomány értesítési beállításai frissülnek, nem csak saját maga.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>E-mailértesítést kaptam egy riasztásról, de amikor bejelentkeztem az Azure Portalra, nem volt riasztás. Mi történt?

Ha egy riasztás fel oldva van, a riasztás törlődik az Azure Portalon. A legvalószínűbb ok az, hogy valaki más, aki e-mail értesítéseket kap, megoldotta a riasztást az Azure AD DS felügyelt tartományban, vagy azt az Azure platform automatikusan feloldotta.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Miért nem tudom nem a beállítási beállításokat?

Ha nem tudja elérni az értesítési beállítások lapot az Azure Portalon, nem rendelkezik az Azure AD DS felügyelt tartomány ának szerkesztéséhez szükséges engedélyekkel. Az Azure AD DS-erőforrás szerkesztéséhez szükséges engedélyek beszerezéséhez globális rendszergazdához kell lépnie, vagy el kell távolítania a címzettek listájáról.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Úgy tűnik, nem kapok e-mail es értesítéseket annak ellenére, hogy megadtam az e-mail címemet. Hogy miért?

Ellenőrizze az e-mailben található levélszemét vagy levélszemét mappát `azure-noreply@microsoft.com`az értesítésért, és engedélyezze a feladónak a számára.

## <a name="next-steps"></a>További lépések

A jelenthető problémák hibáinak elhárításáról az [Azure AD DS által felügyelt tartományriasztásainak megoldása című][troubleshoot-alerts]témakörben talál további információt.

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
