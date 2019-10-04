---
title: 'Azure AD Connect: msExchUserHoldPolicies és cloudMsExchUserHoldPolicies | Microsoft Docs'
description: Ez a témakör a msExchUserHoldPolicies és a cloudMsExchUserHoldPolicies attribútumok attribútum-viselkedését ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d371ba2a09dda933bf14208a00535b757afea85
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014333"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect – msExchUserHoldPolicies és cloudMsExchUserHoldPolicies
Az alábbi dokumentum ismerteti ezeket az attribútumokat, amelyeket az Exchange használ, valamint az alapértelmezett szinkronizálási szabályok szerkesztésének megfelelő módját.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Mi a msExchUserHoldPolicies és a cloudMsExchUserHoldPolicies?
Az Exchange Serverhez két [](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) típusú tároló érhető el: A peres eljárás megtartására és helyben tartására. Ha a peres eljárás engedélyezve van, az összes postaláda minden elemet megtart.  A helyben tartott tárolók csak azokat az elemeket őrzik meg, amelyek megfelelnek a helyi elektronikus iratkezelési eszközzel megadott keresési lekérdezés feltételeinek.

A MsExchUserHoldPolcies és a cloudMsExchUserHoldPolicies attribútumok lehetővé teszik a helyszíni AD és az Azure AD számára annak meghatározását, hogy mely felhasználók tartanak fenn, attól függően, hogy helyszíni Exchange-et vagy Exchange-et használnak-e.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies szinkronizálási folyamata
Alapértelmezés szerint a MsExchUserHoldPolcies szinkronizálja Azure AD Connect közvetlenül a metaverse-ban található msExchUserHoldPolicies attribútummal, majd az Azure AD msExchUserHoldPolices attribútumával.

Az alábbi táblázatok ismertetik a folyamatot:

Helyszíni Active Directorytól bejövő:

|Active Directory attribútum|Attribútum neve|Folyamat típusa|Metaverse-attribútum|Szinkronizálási szabály|
|-----|-----|-----|-----|-----|
|Helyszíni Active Directory|msExchUserHoldPolicies|Közvetlen|msExchUserHoldPolices|Az AD-User Exchange-ből|

Kimenő az Azure AD-be:

|Metaverse-attribútum|Attribútum neve|Folyamat típusa|Azure AD-attribútum|Szinkronizálási szabály|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Közvetlen|msExchUserHoldPolicies|HRE – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies szinkronizálási folyamata
Alapértelmezés szerint a cloudMsExchUserHoldPolicies szinkronizálja Azure AD Connect közvetlenül a metaverse cloudMsExchUserHoldPolicies attribútumához. Ezt követően, ha a msExchUserHoldPolices nem null értékű a metaverse-ben, az attribútum a Active Directory.

Az alábbi táblázatok ismertetik a folyamatot:

Bejövő Azure AD-ből:

|Active Directory attribútum|Attribútum neve|Folyamat típusa|Metaverse-attribútum|Szinkronizálási szabály|
|-----|-----|-----|-----|-----|
|Helyszíni Active Directory|cloudMsExchUserHoldPolicies|Közvetlen|cloudMsExchUserHoldPolicies|HRE – felhasználói Exchange|

Kimenő – helyszíni Active Directory:

|Metaverse-attribútum|Attribútum neve|Folyamat típusa|Azure AD-attribútum|Szinkronizálási szabály|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF (NEM NULL)|msExchUserHoldPolicies|Ki az AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Az attribútum viselkedésére vonatkozó információk
A msExchangeUserHoldPolicies egyetlen szolgáltatói attribútum.  Egyetlen Authority attribútum állítható be egy objektumra (ebben az esetben a felhasználói objektumra) a helyszíni címtárban vagy a Felhőbeli címtárban.  A felügyeleti szabályok kezdete azt diktálja, hogy ha az attribútum a helyszíni rendszerből van szinkronizálva, akkor az Azure AD nem fogja tudni frissíteni ezt az attribútumot.

Annak lehetővé tétele, hogy a felhasználók a felhőben lévő felhasználói objektumon állítsanak be tartási szabályzatot, a rendszer a cloudMSExchangeUserHoldPolicies attribútumot használja. Ezt az attribútumot használja a rendszer, mert az Azure AD nem tudja közvetlenül beállítani a msExchangeUserHoldPolicies a fent ismertetett szabályok alapján.  Ezt az attribútumot ezután a rendszer visszaszinkronizálja a helyszíni címtárral, ha a msExchangeUserHoldPolicies nem null értékű, és lecseréli a msExchangeUserHoldPolicies aktuális értékét.

Bizonyos esetekben előfordulhat, hogy ha mindkettőt a helyszínen és az Azure-ban módosították egyszerre, akkor ez bizonyos problémákat okozhat.  

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
