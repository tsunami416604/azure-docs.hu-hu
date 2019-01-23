---
title: 'Az Azure AD Connect szinkronizálása: AD lomtárának engedélyezése |} A Microsoft Docs'
description: Ez a témakör az Azure AD Connect az AD Lomtár funkció használatát javasolja.
services: active-directory
keywords: AD lomtárának véletlen törlés, a forráshorgony
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4836ffc8c6ab013ef5ad7a661db0df3254b6d4e1
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468755"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Az Azure AD Connect szinkronizálása: Az AD lomtárának engedélyezése
Javasoljuk, hogy a helyszíni Active címtárak, amely szinkronizálva vannak az Azure AD az AD Lomtár engedélyezése. 

Ha véletlenül törölte egy helyszíni AD-felhasználói objektum és a funkció használatát, az Azure AD helyreállítja a megfelelő Azure AD felhasználói objektum visszaállítása.  Az AD Lomtár funkció kapcsolatos információkért tekintse meg a cikk [forgatókönyv áttekintése a visszaállítás törölt Active Directory-objektumok](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Az AD Lomtár engedélyezése előnyei
Ez a szolgáltatás segít az Azure AD felhasználói objektumok visszaállításához a következő módon:

* Ha véletlenül törölte egy helyszíni AD-felhasználói objektumot, a megfelelő Azure AD felhasználói objektum törli a következő szinkronizálási ciklusban. Alapértelmezés szerint az Azure AD megtartja a törölt Azure AD felhasználói objektum 30 napig helyreállíthatóan törölt állapotban.

* Ha rendelkezik a helyszíni AD Recycle Bin szolgáltatás engedélyezve van, a törölt állíthatja vissza a helyszíni AD-felhasználói objektum a Forráshorgony értékét módosítása nélkül. Ha a helyreállított a helyszíni AD-felhasználói objektum szinkronizált Azure ad-hez, az Azure AD fog visszaállítást a megfelelő helyreállíthatóan törölt Azure AD felhasználói objektum. Forráshorgony-attribútumként kapcsolatos információkért tekintse meg a cikk [az Azure AD Connect: Tervezési alapelvek](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Ha nem rendelkezik a helyszíni AD Recycle Bin szolgáltatás engedélyezve van, előfordulhat, hogy az AD-felhasználói objektum cserélje le a törölt objektum létrehozásához. Ha a rendszer által létrehozott AD-attribútum (például az ObjectGuid) használja a Forráshorgony-attribútumként az Azure AD Connect szinkronizálási szolgáltatás van konfigurálva, az újonnan létrehozott AD felhasználói objektum nem fog a törölt AD user objektum Forráshorgony értékét. Az újonnan létrehozott AD felhasználói objektum szinkronizálva van az Azure AD, ha az Azure ad-ben létrehoz egy új Azure AD felhasználói objektum helyett a helyreállíthatóan törölt visszaállítása az Azure AD felhasználói objektum.

> [!NOTE]
> Alapértelmezés szerint az Azure AD tartja az Azure AD felhasználói objektumok helyreállíthatóan törölt állapotban törlése 30 napig, mielőtt véglegesen törlődnek. A rendszergazdák azonban gyorsítható fel az ilyen objektumok a törlést. Az objektumok véglegesen törlődnek, ha azok már nem állíthatók helyre, még akkor is, ha a helyszíni AD Lomtár funkció engedélyezve van.

>[!NOTE]
>Korábban, amikor egy felhasználó lett eltávolítva a helyszíni szinkronizálási hatókör, és törli a felhőben, a fiók a DirSyncEnabled állapota hibás állított be "false"értékre. Ha, hogy a felhasználó ezt követően manuálisan lett visszaállítva a az Azure AD Recycle Bin, szerint a "Csak felhőalapú" fiók egy nem megfelelő állapotát. Ez most már megoldották a problémát, és a DirSyncEnabled állapot értéke mindig megőrzi "True" egy felhasználó eltávolításakor az adatszinkronizálás hatóköre, majd helyreállíthatóan törölt, és manuálisan helyreállított az Azure AD Recycle Bin.

## <a name="next-steps"></a>További lépések
**Áttekintő témakör**

* [Az Azure AD Connect szinkronizálása: Megismerheti, és testre szabhatja a szinkronizálás](how-to-connect-sync-whatis.md)

* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
