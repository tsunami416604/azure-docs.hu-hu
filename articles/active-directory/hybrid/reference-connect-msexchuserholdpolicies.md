---
title: 'Azure AD Connect: msExchUserHoldPolicies és cloudMsExchUserHoldPolicies | Microsoft dokumentumok'
description: Ez a témakör az msExchUserHoldPolicies és a cloudMsExchUserHoldPolicies attribútumok viselkedését ismerteti
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
ms.openlocfilehash: f4c637a01825616334cda8faa594efd08f29de8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74213077"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect - msExchUserHoldPolicies és cloudMsExchUserHoldPolicies
A következő referenciadokumentum ismerteti az Exchange által használt attribútumokat és az alapértelmezett szinkronizálási szabályok szerkesztésének megfelelő módját.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Mik azok az msExchUserHoldPolicies és cloudMsExchUserHoldPolicies?
Az Exchange Server kiszolgálóhoz kétféle [raktér](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) áll rendelkezésre: peres eljárás miatti tartás és helybeni tartás. Ha a peres eljárás miatti tartás engedélyezve van, az összes postaláda várakoztatása várakoztatva lesz.  A helyben történő visszatartás csak azokat az elemeket őrzi meg, amelyek megfelelnek a helyben történő elektronikus adatfeltárás eszközzel meghatározott keresési lekérdezés feltételeinek.

Az MsExchUserHoldPolcies és a cloudMsExchUserHoldPolicies attribútumok lehetővé teszik a helyszíni AD és az Azure AD számára annak meghatározását, hogy mely felhasználók vannak várakoztatva attól függően, hogy helyszíni Exchange vagy Exchange on-line szolgáltatást használnak-e.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies szinkronizálási folyamat
Alapértelmezés szerint az MsExchUserHoldPolcies-t az Azure AD Connect közvetlenül a metaverzumban lévő msExchUserHoldPolicies attribútummal, majd az Azure AD msExchUserHoldPolices attribútumával szinkronizálja.

Az alábbi táblázatok a folyamatot ismertetik:

Bejövő bejövő a helyszíni Active Directoryból:

|Active Directory attribútum|Attribútum neve|Folyamat típusa|Metaverzum attribútum|Szinkronizálási szabály|
|-----|-----|-----|-----|-----|
|Helyszíni Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolices|Be az AD - Felhasználói exchange|

Kimenő Azure AD:

|Metaverzum attribútum|Attribútum neve|Folyamat típusa|Azure AD-attribútum|Szinkronizálási szabály|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|Kimenő AAD - UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies szinkronizálási folyamat
Alapértelmezés szerint cloudMsExchUserHoldPolicies szinkronizálja az Azure AD Connect közvetlenül a cloudMsExchUserHoldPolicies attribútum a metaverzumban. Ezután ha az msExchUserHoldPolices értéke nem null a metaverzumban, akkor a befelé irányuló attribútum kiáramlott az Active Directoryba.

Az alábbi táblázatok a folyamatot ismertetik:

Bejövő az Azure AD-ből:

|Active Directory attribútum|Attribútum neve|Folyamat típusa|Metaverzum attribútum|Szinkronizálási szabály|
|-----|-----|-----|-----|-----|
|Helyszíni Active Directory|cloudMsExchUserHoldPolicies|Direct|cloudMsExchUserHoldPolicies|Be az AAD - Felhasználói csere|

Kimenő szolgáltatás a helyszíni Active Directoryba:

|Metaverzum attribútum|Attribútum neve|Folyamat típusa|Azure AD-attribútum|Szinkronizálási szabály|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|HA(NEM NULL)|msExchUserHoldPolicies|Out to AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Az attribútum viselkedésére vonatkozó információk
Az msExchangeUserHoldPolicies egyetlen hatóságattribútum.  Egyetlen hatóságattribútum állítható be egy objektumon (ebben az esetben felhasználói objektumon) a helyszíni könyvtárban vagy a felhőkönyvtárban.  A Jogosultság kezdete szabályok azt diktálják, hogy ha az attribútum szinkronizálva van a helyszíni, majd az Azure AD nem lesz szabad frissíteni ezt az attribútumot.

Annak érdekében, hogy a felhasználók holdszabályzatot állíthassanak be egy felhasználói objektumon a felhőben, a cloudMSExchangeUserHoldPolicies attribútumot használja a rendszer. Ez az attribútum azért használatos, mert az Azure AD nem tudja beállítani msExchangeUserHoldPolicies közvetlenül a fent ismertetett szabályok alapján.  Ez az attribútum ezután szinkronizálja a helyszíni könyvtárba, ha az msExchangeUserHoldPolicies nem null értékű, és lecseréli az msExchangeUserHoldPolicies aktuális értékét.

Bizonyos körülmények között, például ha mindkettő a helyszínen és az Azure-ban egy időben, ez okozhat bizonyos problémákat.  

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
