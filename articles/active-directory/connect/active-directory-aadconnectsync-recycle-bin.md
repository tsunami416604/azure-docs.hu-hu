---
title: "Az Azure AD Connect szinkronizálási szolgáltatás: AD Lomtár engedélyezése |} Microsoft Docs"
description: "Ez a témakör azt javasolja, hogy az Azure AD Connect AD Lomtár funkciójának használatát."
services: active-directory
keywords: "Az AD Lomtár véletlen törlés, forráshorgony"
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: eb455477547f3db8245cf3601576eba9c6fdc56f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Az Azure AD Connect szinkronizálási szolgáltatás: AD Lomtár engedélyezése
Javasoljuk, hogy az AD Lomtár funkció engedélyezése a helyszíni Active címtárak, amely szinkronizálva az Azure AD. 

Ha véletlenül törölt egy helyszíni AD-felhasználói objektum és a visszaállítási funkcióval, az Azure AD helyreállítja a megfelelő Azure AD-felhasználói objektum.  Az AD Lomtár funkció kapcsolatos információkért tekintse meg a cikk [forgatókönyv áttekintése visszaállítása törölt Active Directory-objektumok](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Az AD Lomtár engedélyezése előnyei
Ez a szolgáltatás segít az Azure Active Directory-felhasználói objektumok visszaállítása a következő módon:

* Ha véletlenül törölt egy helyszíni AD-felhasználó objektumazonosító, a megfelelő Azure AD-felhasználói objektum törli a következő szinkronizálási ciklusban. Alapértelmezés szerint az Azure AD megtartja a törölt felhasználóobjektum az Azure AD 30 napig helyreállíthatóan törölt állapotban.

* Ha a helyszíni AD Recycle Bin szolgáltatás engedélyezve van, a törölt visszaállíthatja a helyszíni AD-felhasználói objektum Forráshorgony értékének módosítása nélkül. Ha a helyreállított a helyszíni AD-felhasználói objektum szinkronizálva az Azure AD, az Azure AD fog visszaállítása a megfelelő helyreállíthatóan törölt az Azure AD felhasználói objektum. Forráshorgony attribútum kapcsolatos információkért tekintse meg a cikk [az Azure AD Connect: tervezési alapelvek](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Ha nem rendelkezik a helyszíni AD Lomtár szolgáltatás engedélyezve van, akkor lehet szükség az AD-felhasználói objektum lecseréli a törölt objektum létrehozásához. Ha az Azure AD Connect szinkronizálási szolgáltatást a Forráshorgony attribútum a rendszer AD attribútum (például az ObjectGuid) használatára van konfigurálva, az újonnan létrehozott AD-felhasználói objektum nem lesz Forráshorgony értéke megegyezik az AD felhasználó objektum. Amikor az újonnan létrehozott AD-felhasználói objektum szinkronizálja az Azure AD, az Azure AD létrehoz egy új Azure AD felhasználói objektum helyett a helyreállíthatóan törölt visszaállítása az Azure AD felhasználói objektum.

> [!NOTE]
> Alapértelmezés szerint az Azure AD tartja törölni az Azure AD felhasználói objektumok helyreállíthatóan törölt állapotban 30 nap elteltével a rendszer véglegesen törli. Azonban a rendszergazdák az ilyen objektumok a törlés meggyorsíthatja. Az objektumok véglegesen törlődnek, ha azok már nem állíthatók helyre, még akkor is, ha a helyszíni AD Lomtár szolgáltatás engedélyezve van.



## <a name="next-steps"></a>Következő lépések
**Áttekintő témakör**

* [Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása](active-directory-aadconnectsync-whatis.md)

* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
