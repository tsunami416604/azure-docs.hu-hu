---
title: "Az Azure AD Connect szinkronizálási szolgáltatások és konfigurációs |} Microsoft Docs"
description: "Az Azure AD Connect szinkronizálási szolgáltatás szolgáltatás ügyféloldali szolgáltatásait ismerteti."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: c2873510c280a2683c235cfdce3d2617c3b665cd
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-sync-service-features"></a>Az Azure AD Connect szinkronizálási szolgáltatások
Az Azure AD Connect szinkronizálási szolgáltatás két részből áll:

* A helyszíni összetevője **az Azure AD Connect szinkronizálási szolgáltatás**is néven **szinkronizálási motor**.
* A szolgáltatás, más néven Azure AD-ben található **az Azure AD Connect szinkronizálási szolgáltatás**

Ez a témakör azt ismerteti, hogyan a következő funkcióit a **az Azure AD Connect szinkronizálási szolgáltatás** munka, és hogyan konfigurálhatja azokat a Windows PowerShell használatával.

Ezek a beállítások szerint úgy vannak konfigurálva a [Active Directory modul Windows Powershellhez készült Azure](http://aka.ms/aadposh). Külön letölteni és telepíteni azt az Azure AD Connect. Rendszerben az ebben a témakörben a parancsmagokat a [2016. március kiadásban (build 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Ha ebben a témakörben a parancsmagokat nem rendelkezik, vagy nem tud létrehozni ugyanazt az eredményt, majd győződjön meg arról, a legújabb verzióját futtatják.

Az Azure AD-címtár konfiguráció megtekintéséhez futtassa `Get-MsolDirSyncFeatures`.  
![Get-MsolDirSyncFeatures eredménye](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

Számos beállítás csak módosíthatja az Azure AD Connect.

Az alábbi beállításokat konfigurálhatja `Set-MsolDirSyncFeature`:

| DirSyncFeature | Megjegyzés |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Lehetővé teszi, hogy csatlakozni a userPrincipalName elsődleges SMTP-cím mellett objektumokat. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Lehetővé teszi, hogy a szinkronizálási motor a userPrincipalName attribútum a kezelt vagy licenccel rendelkező felhasználók (nem összevont) frissítése. |

Miután engedélyezte a szolgáltatás, nem lehet letiltani újra.

> [!NOTE]
> 2016 augusztusától 24 a szolgáltatás *ismétlődő attribútum rugalmassági* alapértelmezés szerint engedélyezve van az új Azure AD könyvtárakban. Ez a szolgáltatás is lehet megkezdődött és engedélyezve van ez a dátum előtt létrehozott könyvtárak. E-mailben értesítést fog kapni, amikor a címtár arra készül, hogy ezt a szolgáltatást, engedélyezve van.
> 
> 

A következő beállításokat az Azure AD Connect vannak konfigurálva, és nem módosíthatják a `Set-MsolDirSyncFeature`:

| DirSyncFeature | Megjegyzés |
| --- | --- |
| DeviceWriteback |[Az Azure AD Connect: Eszközvisszaírás engedélyezése](active-directory-aadconnect-feature-device-writeback.md) |
| DirectoryExtensions |[Azure AD Connect szinkronizálása: címtárbővítmények](active-directory-aadconnectsync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Lehetővé teszi, hogy a karanténba kerül, ha egy másik objektum helyett az exportálás során a teljes objektumot sikertelen duplikált attribútum. |
| PasswordSync |[A jelszó-szinkronizálás és az Azure AD Connect-szinkronizálás megvalósítása](active-directory-aadconnectsync-implement-password-synchronization.md) |
| UnifiedGroupWriteback |[Előzetes verzió: A csoportvisszaírás](active-directory-aadconnect-feature-preview.md#group-writeback) |
| UserWriteback |Jelenleg nem támogatott. |

## <a name="duplicate-attribute-resiliency"></a>Ismétlődő attribútum rugalmasság
Ismétlődő egyszerű felhasználónevek objektumot hibás kiépítését / proxyAddresses, az ismétlődő attribútum "karanténba", és egy ideiglenes érték hozzá van rendelve. Amikor feloldja az ütközést, az ideiglenes UPN automatikusan megváltozik a megfelelő értéket. További részletekért lásd: [identitás-szinkronizálással és duplikált attribútum rugalmassági](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>UserPrincipalName enyhe egyezés
Ha ez a funkció engedélyezve van, soft-match kívül UPN engedélyezve van a [elsődleges SMTP-cím](https://support.microsoft.com/kb/2641663), amely mindig engedélyezve van. Soft-match felelnek meg a meglévő felhőalapú felhasználók az Azure AD helyszíni felhasználók szolgál.

Ha szeretné egyezés a helyszíni AD-fiókok a felhőben létrehozott meglévő fiókokhoz nem használ az Exchange Online, majd a szolgáltatás akkor hasznos. Ebben a forgatókönyvben általában nem rendelkezik SMTP attribútum beállítása a felhőben okát.

Ez a funkció a alapértelmezés szerint most hozták létre Azure AD-címtártól. Ha ezt a szolgáltatást, futtassa a látható:  

```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Ha ez a funkció nincs engedélyezve az Azure AD-címtárát, majd engedélyezheti azt futtatásával:  

```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>UserPrincipalName a frissítések szinkronizálása
Hagyományosan a UserPrincipalName attribútum a szinkronizálási szolgáltatás használatát a helyszíni frissítéseit le van tiltva, kivéve, ha az alábbi két feltétel teljesül:

* A felhasználó (nem összevont) kezeli.
* A felhasználó nincs hozzárendelve egy licencet.

További részletekért lásd: [Office 365, az Azure vagy az Intune-ban szereplő felhasználónevek nem egyeznek meg, a helyszíni egyszerű Felhasználónévvel vagy másodlagos bejelentkezési Azonosítóval](https://support.microsoft.com/kb/2523192).

Ez a funkció lehetővé teszi, hogy a szinkronizálási motor a userPrincipalName frissíteni, ha megváltozott a helyszíni és jelszó-szinkronizálást használ. Összevonási használja, ha ez a funkció nem támogatott.

Ez a funkció a alapértelmezés szerint most hozták létre Azure AD-címtártól. Ha ezt a szolgáltatást, futtassa a látható:  

```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Ha ez a funkció nincs engedélyezve az Azure AD-címtárát, majd engedélyezheti azt futtatásával:  

```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

A funkció engedélyezése után meglévő userPrincipalName értékek marad-van. A következő módosításakor a userPrincipalName attribútum helyszínen a normál eltérés szinkronizálása a felhasználók frissíteni fogja az egyszerű Felhasználónevet.  

## <a name="see-also"></a>Lásd még:
* [Az Azure AD Connect szinkronizálása](active-directory-aadconnectsync-whatis.md)
* [A helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).

