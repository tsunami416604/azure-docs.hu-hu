---
title: "Azure Active Directory tartományi szolgáltatások: Az egyszerű szolgáltatás beállításának hibakeresésére |} Microsoft Docs"
description: "Hibakeresés egyszerű szolgáltatásnév az Azure AD tartományi szolgáltatásokhoz"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: ergreenl
ms.openlocfilehash: e6144a4018f7fbe7dbf7b4693e3f41884e4a80a2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-service-principal-configuration"></a>Azure AD tartományi szolgáltatások - hibaelhárítás egyszerű konfiguráció

Szolgáltatás, kezelése és a tartomány frissítéséhez, Microsoft használ a különböző [Szolgáltatásnevekről](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects) kommunikálni a címtárban. Ha egy van konfigurálva, vagy törölték, azt a szolgáltatás a szüneteltetése okozhat.

## <a name="aadds102-service-principal-not-found"></a>AADDS102: Nem található egyszerű szolgáltatásnév

**Figyelmeztető üzenet:**

*Az Azure AD tartományi szolgáltatások megfelelő működéséhez szükséges egyszerű szolgáltatás az Azure AD-címtár törölve lett. Ez a konfiguráció hatással van a Microsoft képes figyeléséhez, kezeléséhez, a javítás, és a felügyelt tartományok szinkronizálása.*

Szolgáltatásnevekről olyan alkalmazások, amelyek Microsoft kezelése, frissítése és a felügyelt tartományok kezelése használja. Törlődnek, ha megszakítja a Microsoft a tartományi szolgáltatás képessége. Az alábbi lépések segítségével határozza meg, melyik szolgáltatás rendszerbiztonsági tagoknak kell újból létre kell hozni.

1. Keresse meg a [vállalati alkalmazások – összes alkalmazás](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) oldal az Azure portálon.
2. A Megjelenítés legördülő menüben válasszon **összes alkalmazás** kattintson **alkalmaz**.
3. Az alábbi táblázatban keresse meg a azonosító beillesztése a keresési mezőbe, majd nyomja le az egyes Alkalmazásazonosító adja meg. Ha a keresési eredmények üres, újra létre kell hoznia a "megoldási" oszlopban szereplő lépések a szolgáltatásnevet.

| Alkalmazásazonosító | Megoldás: |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Egy hiányzó szolgáltatás egyszerű PowerShell újbóli létrehozása](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Regisztrálja újra a Microsoft.AAD névtérhez](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Regisztrálja újra a Microsoft.AAD névtérhez](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Szolgáltatás rendszerbiztonsági Self javítása](#service-principals-that-self-correct) |

### <a name="recreate-a-missing-service-principal-with-powershell"></a>Hozza létre újra a hiányzó egyszerű PowerShell használatával

*Kövesse az alábbi lépéseket, hogy nem hiányoznak-azonosítók: 2565bd9d-da50-47d4-8b85-4c97f669dc36*

**Szervizelés:**

Azure AD PowerShell lépések elvégzéséhez szüksége. Azure AD PowerShell telepítésével kapcsolatos információkért lásd: [Ez a cikk](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

A probléma megoldására PowerShell ablakban írja be a következő parancsokat:
1. Install-modul AzureAD
2. Import-Module AzureAD
3. Ellenőrizze, hogy az Azure AD tartományi szolgáltatások számára szükséges egyszerű hiányzik a címtárban hajtja végre a következő PowerShell-parancsot:
      ```PowerShell
      Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
      ```
4. Az egyszerű szolgáltatás létrehozása a következő PowerShell-parancs beírásával:
     ```PowerShell
     New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
     ```
5. Miután létrehozta a hiányzó egyszerű, két óra várakozás után ellenőrizze a tartomány állapotát.


### <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Regisztrálja újra a Microsoft AAD névtérhez az Azure portál használatával

*Kövesse az alábbi lépéseket, hogy nem hiányoznak-azonosítók: 443155a6-77f3-45e3-882b-22b3a8d431fb és abba844e-bc0e-44b0-947a-dc74e5d09022*

**Szervizelés:**

Az alábbi lépések segítségével tartományi szolgáltatásokban a címtár-visszaállítás:

1. Keresse meg a [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oldal az Azure portálon.
2. Válassza ki az előfizetést a táblából, a felügyelt tartományok társított
3. A bal oldali navigációs sáv segítségével **erőforrás-szolgáltató**
4. Keresse meg "Microsoft.AAD" a táblázatban, és kattintson a **regisztrálja újra**
5. Ellenőrizze, hogy a riasztás oldódik meg, hogy a health riasztási a lapnak a megtekintésére két órában.


### <a name="service-principals-that-self-correct"></a>Szolgáltatás rendszerbiztonsági self javítása

*Kövesse az alábbi lépéseket, hogy nem hiányoznak-azonosítók: d87dcbc6-a371-462e-88e3-28ad15ec4e64*

**Szervizelés:**

Microsoft azonosíthatja, ha adott Szolgáltatásnevekről hiányoznak, nincs megfelelően konfigurálva, vagy törölték. A szolgáltatás gyors kijavításához Microsoft újból létre fogja hozni a Szolgáltatásnevekről magát. A tartomány állapotának ellenőrzése után győződjön meg arról, hogy a rendszerbiztonsági tag újból létrejött két órát.

## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory tartományi szolgáltatások termékért felelős csoport a [visszajelzés fájlmegosztás vagy a támogatáshoz](active-directory-ds-contact-us.md).
