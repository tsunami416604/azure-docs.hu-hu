---
title: Az Azure AD Connect 1.4. xx. x és az eszközök megszűnésének ismertetése | Microsoft Docs
description: Ez a dokumentum egy olyan problémát ismertet, amely a Azure AD Connect 1.4. xx. x verziójával felmerül.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 1ba17feea880bb55c3b4a14a06b3d803dba2350a
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316953"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Az Azure AD Connect 1.4. xx. x és az eszközön való megjelenés ismertetése
A Azure AD Connect 1.4. xx. x verziójával néhány ügyfél láthatja, hogy a Windows-eszközeik némelyike vagy mindegyike eltűnik az Azure AD-ből. Ez nem okoz gondot, mivel az Azure AD nem használja fel az eszköz identitásait a feltételes hozzáférés engedélyezésekor. Ez a változás nem törli azokat a Windows-eszközöket, amelyek megfelelően regisztrálva lettek az Azure AD-ben hibrid Azure AD-csatlakozáshoz.

Ha úgy látja, hogy az Azure AD-ban az eszközök objektumainak törlése meghaladja az Exportálás törlési küszöbértékét, javasoljuk, hogy az ügyfél engedélyezze a törlést. [Útmutató: a törlési folyamat engedélyezése, ha túllépik a törlési küszöbértéket](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Háttér
A hibrid Azure AD-hez regisztrált Windows-eszközök az Azure AD-ben, az eszköz objektumaiként jelennek meg. Ezeket az eszközöket feltételes hozzáféréshez használhatja. A Windows 10-es eszközök a felhőbe való szinkronizálása Azure AD Connect, a Down szintű Windows-eszközök közvetlen regisztrálása AD FS vagy zökkenőmentes egyszeri bejelentkezés használatával történik.

## <a name="windows-10-devices"></a>Windows 10-es eszközök
Azure AD Connect a felhőbe csak a hibrid Azure AD JOIN által konfigurált, meghatározott userCertificate attribútum értékkel rendelkező Windows 10-es eszközöket kell szinkronizálni. Azure AD Connect korábbi verzióiban ez a követelmény nem lett szigorúan kikényszerítve, ami az Azure AD-ben szükségtelen eszköz-objektumokat eredményezett. Az Azure AD-beli eszközök mindig "függő" állapotban maradnak, mivel ezek az eszközök nem lettek regisztrálva az Azure AD-ben. 

A Azure AD Connect ezen verziója csak a hibrid Azure AD-hez csatlakoztatott Windows 10-es eszközök szinkronizálását hajtja végre. Az Azure AD JOIN-specifikus userCertificate nem rendelkező Windows 10-es eszközök el lesznek távolítva az Azure AD-ből.

## <a name="down-level-windows-devices"></a>Régebbi verziójú Windows-eszközök
Azure AD Connect soha ne legyenek szinkronizálva a [Windows rendszerű eszközök](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Az Azure AD-ben korábban szinkronizált eszközök már nem megfelelően lesznek törölve az Azure AD-ből. Ha Azure AD Connect a [régebbi Windows-eszközök](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)törlését kísérli meg, akkor az eszköz nem a [Microsoft Workplace JOIN által a nem Windows 10 rendszerű számítógépek MSI-hez](https://www.microsoft.com/download/details.aspx?id=53554) készült, és a többi Azure ad-szolgáltatás nem tudja használni.

Előfordulhat, hogy néhány ügyfélnek újra [kell látogatnia a következőket: Tervezze meg a hibrid Azure Active Directory](../devices/hybrid-azuread-join-plan.md) csatlakoztatását a Windows-eszközök megfelelő regisztrálásához, és győződjön meg arról, hogy az ilyen eszközök teljes mértékben részt vehetnek az eszközön alapuló feltételes hozzáférésben. 

## <a name="next-steps"></a>További lépések
- [Azure AD Connect korábbi verziók](reference-connect-version-history.md)
