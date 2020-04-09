---
title: Az Azure AD Connect szinkronizálási szolgáltatásának szolgáltatásai és konfigurációja | Microsoft dokumentumok
description: Az Azure AD Connect szinkronizálási szolgáltatás szolgáltatásoldali szolgáltatásainak ismertetése.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3f6b698922440c6e3e9b488cca93ca8d98d9c59
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983075"
---
# <a name="azure-ad-connect-sync-service-features"></a>Az Azure AD Connect szinkronizálási szolgáltatás jellemzői

Az Azure AD Connect szinkronizálási szolgáltatása két összetevőből áll:

* Az **Azure AD Connect sync**nevű helyszíni összetevő , más néven **szinkronizálási motor.**
* Az Azure AD-ben található szolgáltatás, más néven **Az Azure AD Connect szinkronizálási szolgáltatása**

Ez a témakör bemutatja, hogyan működnek az **Azure AD Connect szinkronizálási szolgáltatás** következő funkciói, és hogyan konfigurálhatja őket a Windows PowerShell használatával.

Ezeket a beállításokat az [Azure Active Directory module for Windows PowerShell](https://aka.ms/aadposh)konfigurálja. Töltse le és telepítse az Azure AD Connecttől elkülönítve. A témában dokumentált parancsmagokat a [2016 márciusi kiadásban vezették be (build 9031.1).](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1) Ha a témakörben nem dokumentált parancsmagokkal rendelkezik, vagy nem ugyanazt az eredményt eredményezik, akkor győződjön meg arról, hogy a legújabb verziót futtatja.

A konfiguráció megtekintéséhez az Azure AD könyvtárban futtassa a futtassa a . `Get-MsolDirSyncFeatures`  
![Get-MsolDirSyncFeatures eredmény](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Ezek közül a beállítások közül sok csak az Azure AD Connect által módosítható.

A következő beállításokat `Set-MsolDirSyncFeature`a következő konkretinátkkal lehet konfigurálni:

| DirSyncFeature | Megjegyzés |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Lehetővé teszi, hogy az objektumok az elsődleges SMTP-cím mellett csatlakozzanak a userPrincipalName-hez. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Lehetővé teszi, hogy a szinkronizálási motor frissítse a userPrincipalName attribútumot a felügyelt/licencelt (nem összevont) felhasználók számára. |

Miután engedélyezett egy funkciót, az nem tiltható le újra.

> [!NOTE]
> 2016. augusztus 24-től a *Duplikált attribútum rugalmassága* alapértelmezés szerint engedélyezve van az új Azure AD-könyvtárakban. Ez a funkció is gördült ki, és engedélyezve van a könyvtárak előtt létrehozott ezt a dátumot. E-mailben értesítést kap, amikor a könyvtár a szolgáltatás engedélyezésére készül.
> 
> 

Az Azure AD Connect a következő beállításokat `Set-MsolDirSyncFeature`konfigurálja, és nem módosíthatók:

| DirSyncFeature | Megjegyzés |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: Eszköz-visszaírás engedélyezése](how-to-connect-device-writeback.md) |
| DirectoryExtensions (Címtárbővítmények) |[Azure AD Connect szinkronizálás: Címtárbővítmények](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Lehetővé teszi egy attribútum karanténba helyezése, ha egy másik objektum másolata, nem pedig a teljes objektum elmaradása az exportálás során. |
| Jelszókivonat szinkronizálása |[Jelszókivonat-szinkronizálás megvalósítása az Azure AD Connect szinkronizálásával](how-to-connect-password-hash-synchronization.md) |
|Átmenő hitelesítés|[Felhasználói bejelentkezés az Azure Active Directory átmenő hitelesítésével](how-to-connect-pta.md)|
| UnifiedGroupWriteback |[Előzetes verzió: Csoportos visszaírás](how-to-connect-preview.md#group-writeback) |
| UserWriteback |Jelenleg nem támogatott. |

## <a name="duplicate-attribute-resiliency"></a>Duplikált attribútumrugalmasság

Ahelyett, hogy nem a duplikált UPNs / proxyAddresses objektumok kiépítése, a duplikált attribútum "karanténba kerül", és egy ideiglenes érték van rendelve. Az ütközés feloldásakor az ideiglenes upn automatikusan a megfelelő értékre változik. További információt az [Identitás-szinkronizálás és az attribútumok rugalmasságának duplikálása](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>UserPrincipalName lágy egyezés

Ha ez a funkció engedélyezve van, az elsődleges [SMTP-cím](https://support.microsoft.com/kb/2641663)mellett engedélyezve van az upn-hoz való soft-match is, amely mindig engedélyezve van. A soft-match az Azure AD meglévő felhőfelhasználóinak és a helyszíni felhasználóknak való egyeztetéséhez használatos.

Ha a helyszíni AD-fiókokat a felhőben létrehozott meglévő fiókokkal kell egyeztetnie, és nem használja az Exchange Online-t, akkor ez a funkció hasznos. Ebben a forgatókönyvben általában nincs oka az SMTP attribútum beállítására a felhőben.

Ez a funkció alapértelmezés szerint be van kapcsolva az újonnan létrehozott Azure AD-könyvtárak esetében. A következő futtatásával láthatja, hogy ez a funkció engedélyezve van-e:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Ha ez a funkció nincs engedélyezve az Azure AD-címtárban, akkor a következő futtatásával engedélyezheti:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>UserPrincipalName frissítések szinkronizálása

A userprincipalname attribútum frissítései a helyszíni szinkronizálási szolgáltatás használatával korábban le vannak tiltva, kivéve, ha mindkét feltétel teljesül:

* A felhasználó felügyelt (nem összevont).
* A felhasználóhoz nincs licenc rendelve.

> [!NOTE]
> 2019 márciusától az upn-módosítások szinkronizálása az összevont felhasználói fiókokhoz engedélyezett.
> 

A szolgáltatás engedélyezése lehetővé teszi, hogy a szinkronizálási motor frissítse a userPrincipalName, ha a helyszínen módosul, és használja a jelszó kivonatoló szinkronizálás vagy átadó hitelesítés.

Ez a funkció alapértelmezés szerint be van kapcsolva az újonnan létrehozott Azure AD-könyvtárak esetében. A következő futtatásával láthatja, hogy ez a funkció engedélyezve van-e:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Ha ez a funkció nincs engedélyezve az Azure AD-címtárban, akkor a következő futtatásával engedélyezheti:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

A szolgáltatás engedélyezése után a meglévő userPrincipalName értékek változatlanok maradnak. A userPrincipalName attribútum következő módosításakor a helyszíni állapotban a felhasználók normál különbözeti szinkronizálása frissíti az egyszerű felhasználónevet.  

## <a name="see-also"></a>Lásd még

* [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md)
* [A helyszíni identitások integrálása az Azure Active Directoryval.](whatis-hybrid-identity.md)
