---
title: Az Azure AD Connect szinkronizálási szolgáltatás funkciók és konfiguráció |} A Microsoft Docs
description: Az Azure AD Connect szinkronizálási szolgáltatás szolgáltatás egymás szolgáltatásait ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 8d351e41eac3c820b9295b3b5cf314428bebc746
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242993"
---
# <a name="azure-ad-connect-sync-service-features"></a>Az Azure AD Connect szinkronizálási szolgáltatás funkciók
Az Azure AD Connect szinkronizálási szolgáltatás két összetevőből áll:

* A helyszíni összetevő nevű **Azure AD Connect szinkronizálási**, más néven is néven **szinkronizálási motor**.
* A szolgáltatás az Azure ad-ben más néven levő **az Azure AD Connect szinkronizálási szolgáltatás**

Ez a témakör ismerteti a következő funkciói a **az Azure AD Connect szinkronizálási szolgáltatás** munka, és hogy hogyan konfigurálhat Windows PowerShell-lel.

Ezek a beállítások szerint úgy vannak konfigurálva a [Azure Active Directory modul Windows Powershellhez készült](https://aka.ms/aadposh). Töltse le és telepítse külön-külön az Azure AD Connect. Ebben a témakörben a parancsmagokat a jelentek meg a [2016. márciusi kiadásban (build 9031.1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Ha nem rendelkezik a parancsmagok ebben a témakörben leírt, vagy azok nem tudott ugyanazt az eredményt, majd győződjön meg arról, a legújabb verziót futtatja.

Az Azure AD-címtár konfiguráció megtekintéséhez futtassa `Get-MsolDirSyncFeatures`.  
![Get-MsolDirSyncFeatures eredménye](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Számos beállítás csak az Azure AD Connect által módosítható.

Az alábbi beállításokat konfigurálhatja `Set-MsolDirSyncFeature`:

| DirSyncFeature | Megjegyzés |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Lehetővé teszi, hogy csatlakozzon az elsődleges SMTP-cím mellett userPrincipalName objektumok. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Lehetővé teszi, hogy a szinkronizálási motor frissítése a userPrincipalName attribútum a felügyelt/licenccel rendelkező felhasználók (nem összevont). |

Miután engedélyezte a funkciót, akkor nem lehet letiltani újra.

> [!NOTE]
> 2016. augusztus 24. a funkció *duplikált attribútummal kapcsolatos rugalmasság* alapértelmezés szerint engedélyezve van az új Azure AD-címtár. Ez a funkció is lehet egyik tagján jelennek meg és engedélyezve van ez a dátum előtt létrehozott címtárakat a. E-mail-értesítést fog kapni, amikor a címtár beszerzése a szolgáltatás nincs engedélyezve.
> 
> 

A következő beállításokat az Azure AD Connect által konfigurált, és nem módosíthatók az `Set-MsolDirSyncFeature`:

| DirSyncFeature | Megjegyzés |
| --- | --- |
| DeviceWriteback |[Az Azure AD Connect: Eszközvisszaírás engedélyezése](how-to-connect-device-writeback.md) |
| DirectoryExtensions |[Az Azure AD Connect szinkronizálása: címtárbővítmények](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Lehetővé teszi egy attribútumot karanténba kerül, ha az üzenet egy másolat egy másik objektum helyett a teljes objektumot meghiúsul az exportálás során. |
| Jelszókivonat szinkronizálása |[A Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás megvalósítása](how-to-connect-password-hash-synchronization.md) |
|Átmenő hitelesítés|[Felhasználói bejelentkezés az Azure Active Directory átmenő hitelesítésével](how-to-connect-pta.md)|
| UnifiedGroupWriteback |[Előzetes verzió: Csoportvisszaírás](how-to-connect-preview.md#group-writeback) |
| UserWriteback |Jelenleg nem támogatott. |

## <a name="duplicate-attribute-resiliency"></a>Ismétlődő attribútumok rugalmassága
A kiépítés sikertelen helyett objektumok az ismétlődő egyszerű felhasználónevek, illetve a proxyAddresses, a duplikált attribútummal "karanténba" és a egy ideiglenes érték van hozzárendelve. Az ütközés megoldásakor az ideiglenes egyszerű Felhasználónevet a megfelelő értékre automatikusan módosul. További részletekért lásd: [identitás identitásszinkronizálás és ismétlődő attribútumok rugalmassága](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>UserPrincipalName helyreállítható egyezés
Ha ez a funkció engedélyezve van, egyezéssel engedélyezve van-e az egyszerű felhasználónév mellett a [elsődleges SMTP-cím](https://support.microsoft.com/kb/2641663), amely mindig engedélyezve van. Az Azure ad-ben meglévő felhőbeli felhasználók egyezik a helyszíni felhasználók egyezéssel szolgál.

Ha szeretne egyezik a helyszíni AD-fiókokat a meglévő fiókokat, a felhőben létrehozott nem használ az Exchange online-hoz, majd ez a funkció akkor hasznos. Ebben a forgatókönyvben általában nem kell állítani az SMTP-attribútumot a felhőben okát.

Ez a funkció a alapértelmezés szerint újonnan jön létre az Azure AD-címtár. Ha ez a funkció engedélyezve van az Ön számára futtatásával tekintheti meg:  

```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Ha ez a funkció nincs engedélyezve az Azure AD-címtárhoz, majd engedélyezheti azt futtatásával:  

```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>UserPrincipalName frissítések szinkronizálása
Hagyományosan a UserPrincipalName attribútum a helyszínről a szinkronizálási szolgáltatás használata frissítések le van tiltva, kivéve, ha az alábbi két feltétel teljesül:

* A felhasználó felügyelt (nem összevont).
* A felhasználó nem lett hozzárendelve licenc.

További részletekért lásd: [az Office 365, Azure vagy Intune-ban a felhasználónevek nem egyeznek, a helyszíni egyszerű Felhasználónévvel vagy másodlagos bejelentkezési Azonosítót](https://support.microsoft.com/kb/2523192).

Ez a funkció lehetővé teszi, hogy a szinkronizálási motor frissítése a userPrincipalName, amikor megváltozott a helyszínen, és használhatja a Jelszókivonat-szinkronizálás. Ha összevonási használja, ez a funkció nem támogatott.

Ez a funkció a alapértelmezés szerint újonnan jön létre az Azure AD-címtár. Ha ez a funkció engedélyezve van az Ön számára futtatásával tekintheti meg:  

```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Ha ez a funkció nincs engedélyezve az Azure AD-címtárhoz, majd engedélyezheti azt futtatásával:  

```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

A funkció engedélyezése után a meglévő userPrincipalName értékeket marad,-van. A következő módosításakor a userPrincipalName attribútum a helyszínen a felhasználók a normál különbözeti szinkronizálás frissíteni fogja az egyszerű Felhasználónevet.  

## <a name="see-also"></a>Lásd még
* [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md)
* [A helyszíni identitások integrálása az Azure Active Directory](whatis-hybrid-identity.md).

