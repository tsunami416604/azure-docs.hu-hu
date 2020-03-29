---
title: 'Azure AD Connect szinkronizálása: AD lomtár engedélyezése | Microsoft dokumentumok'
description: Ez a témakör az AD Lomtár szolgáltatás használatát javasolja az Azure AD Connect használatával.
services: active-directory
keywords: AD Lomtár, véletlen törlés, forráshorgony
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe7d3ea7d4f6d648438efc1a484d5909ade2f23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60382895"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect szinkronizálás: AD lomtár engedélyezése
Javasoljuk, hogy engedélyezze az AD Lomtár szolgáltatást a helyszíni aktív könyvtárakhoz, amelyek szinkronizálva vannak az Azure AD-vel. 

Ha véletlenül törölt egy helyszíni AD felhasználói objektumot, és visszaállítja azt a funkció használatával, az Azure AD visszaállítja a megfelelő Azure AD felhasználói objektumot.  Az AD Lomtár szolgáltatásról a [Törölt Active Directory-objektumok visszaállításáról szóló forgatókönyv áttekintése](https://technet.microsoft.com/library/dd379542.aspx)című cikkben olvashat.

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Az AD lomtár engedélyezésének előnyei
Ez a funkció az alábbi módon segíti az Azure AD felhasználói objektumok visszaállítását:

* Ha véletlenül törölt egy helyszíni AD felhasználói objektumot, a megfelelő Azure AD felhasználói objektum törlődik a következő szinkronizálási ciklusban. Alapértelmezés szerint az Azure AD 30 napig megőrzi a törölt Azure AD felhasználói objektumot.

* Ha engedélyezve van a helyszíni AD Lomtár szolgáltatás, a törölt helyszíni AD felhasználói objektumot a Forráshorgony értékének módosítása nélkül visszaállíthatja. Amikor a helyreállított helyszíni AD felhasználói objektum szinkronizálva van az Azure AD-vel, az Azure AD visszaállítja a megfelelő helyreállítható Azure AD felhasználói objektumot. A Forráshorgony attribútumról az [Azure AD Connect: Tervezési fogalmak](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor)című cikkben olvashat.

* Ha nincs engedélyezve a helyszíni AD Lomtár szolgáltatás, előfordulhat, hogy létre kell hoznia egy AD felhasználói objektumot a törölt objektum cseréjéhez. Ha az Azure AD Connect szinkronizálási szolgáltatás úgy van konfigurálva, hogy a rendszer által generált AD attribútum (például ObjectGuid) használatával használja a Forráshorgony attribútumot, az újonnan létrehozott AD felhasználói objektum nem rendelkezik a törölt AD felhasználói objektummal megegyező forráshorgony értékkel. Amikor az újonnan létrehozott AD felhasználói objektum szinkronizálva van az Azure AD-vel, az Azure AD egy új Azure AD felhasználói objektumot hoz létre a helyreállító Azure AD felhasználói objektum visszaállítása helyett.

> [!NOTE]
> Alapértelmezés szerint az Azure AD a törölt Azure AD felhasználói objektumokat 30 napig véglegesen törli. A rendszergazdák azonban felgyorsíthatják az ilyen objektumok törlését. Az objektumok végleges törlése után már nem lehet visszaőket tetszni, még akkor sem, ha a helyszíni AD Lomtár szolgáltatás engedélyezve van.

## <a name="next-steps"></a>További lépések
**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása](how-to-connect-sync-whatis.md)

* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
