---
title: Szerepkörök delegálása rendszergazdai feladat szerint – Azure Active Directory | Microsoft dokumentumok
description: Az Azure Active Directoryidentitás-feladatok delegálásához rendelt szerepkörök
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/03/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd24650c9bf0c4de155b5bfc8723cfa1fef01548
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81755409"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Rendszergazdai szerepkörök rendszergazdai feladat szerint az Azure Active Directoryban

Ebben a cikkben a felhasználó rendszergazdai engedélyeinek korlátozásához szükséges információkat az Azure Active Directory (Azure AD) legkevésbé kiemelt szerepkörök hozzárendelésével találja. A rendszergazdai feladatokat a szolgáltatásterület és az egyes feladatok végrehajtásához szükséges legkevésbé privilegizált szerepkör, valamint a feladat végrehajtására képes további nem globális rendszergazdai szerepkörök szerint rendezik.

## <a name="application-proxy"></a>Alkalmazásproxy

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Alkalmazásproxy-alkalmazás konfigurálása | Alkalmazás-rendszergazda | 
Összekötőcsoport tulajdonságainak konfigurálása | Alkalmazás-rendszergazda | 
Alkalmazásregisztráció létrehozása, ha a képesség le van tiltva az összes felhasználó számára | Alkalmazásfejlesztő | Felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda
Összekötőcsoport létrehozása | Alkalmazás-rendszergazda | 
Összekötőcsoport törlése | Alkalmazás-rendszergazda | 
Alkalmazásproxy letiltása | Alkalmazás-rendszergazda | 
Összekötő szolgáltatás letöltése | Alkalmazás-rendszergazda | 
Az összes konfiguráció olvasása | Alkalmazás-rendszergazda | 

## <a name="b2c"></a>B2C

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Azure AD B2C könyvtárak létrehozása | Minden nem vendég felhasználó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
B2C-alkalmazások létrehozása | Globális rendszergazda | 
Vállalati alkalmazások létrehozása | Felhőalkalmazás-rendszergazda | alkalmazás-rendszergazda
B2C-házirendek létrehozása, olvasása, frissítése és törlése | B2C IEF házirend-rendszergazda | 
Identitásszolgáltatók létrehozása, olvasása, frissítése és törlése | Külső identitásszolgáltató rendszergazdája | 
Jelszó-visszaállítási felhasználói folyamatok létrehozása, olvasása, frissítése és törlése | B2C felhasználói folyamat rendszergazdája | 
Profilszerkesztő felhasználói folyamatok létrehozása, olvasása, frissítése és törlése | B2C felhasználói folyamat rendszergazdája | 
Bejelentkezési felhasználói folyamatok létrehozása, olvasása, frissítése és törlése | B2C felhasználói folyamat rendszergazdája | 
Feliratkozási felhasználói folyamat létrehozása, olvasása, frissítése és törlése |B2C felhasználói folyamat rendszergazdája | 
Felhasználói attribútumok létrehozása, olvasása, frissítése és törlése | B2C felhasználói folyamat attribútumának rendszergazdája | 
Felhasználók létrehozása, olvasása, frissítése és törlése | Felhasználói rendszergazda
Az összes konfiguráció olvasása | Globális olvasó | 
B2C naplóinak olvasása | Globális olvasó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

> [!NOTE]
> Az Azure AD B2C globális olvasók nem rendelkeznek ugyanolyan engedélyekkel, mint az Azure AD globális rendszergazdák. Ha rendelkezik az Azure AD B2C globális rendszergazdai jogosultságokkal, győződjön meg arról, hogy egy Azure AD B2C könyvtárban van, és nem egy Azure AD-címtárban.

## <a name="company-branding"></a>Vállalati védjegyezés

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Vállalati arculat konfigurálása | Globális rendszergazda | 
Az összes konfiguráció olvasása | Címtárolvasók | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Vállalati ingatlanok

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Vállalat tulajdonságainak konfigurálása | Globális rendszergazda | 

## <a name="connect"></a>Kapcsolódás

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Áthaladási hitelesítés | Hibrid identitás-rendszergazda | 
Az összes konfiguráció olvasása | Globális olvasó | Hibrid identitás-rendszergazda |
Zökkenőmentes egyszeri bejelentkezés | Hibrid identitás-rendszergazda | 

## <a name="connect-health"></a>Connect Health

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Szolgáltatások hozzáadása vagy törlése | Tulajdonos ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Javítások alkalmazása szinkronizálási hibára | Közreműködő ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Tulajdonos
Értesítések konfigurálása | Közreműködő ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Tulajdonos
Beállítások konfigurálása | Tulajdonos ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Szinkronizálási értesítések konfigurálása | Közreműködő ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Tulajdonos
ADFS biztonsági jelentések olvasása | Biztonsági olvasó | Közreműködő, tulajdonos
Az összes konfiguráció olvasása | Olvasó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Olvasási szinkronizálási hibák | Olvasó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Szinkronizálási szolgáltatások olvasása | Olvasó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Mérőszámok és riasztások megtekintése | Olvasó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Mérőszámok és riasztások megtekintése | Olvasó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Szinkronizálási szolgáltatás mérőszámokkal és riasztásokkal való megtekintése | Olvasó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos

## <a name="custom-domain-names"></a>Egyéni tartománynevek

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Tartományok kezelése | Globális rendszergazda | 
Az összes konfiguráció olvasása | Címtárolvasók | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Tartományi szolgáltatások

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Azure AD tartományi szolgáltatások példányának létrehozása | Globális rendszergazda | 
Az Azure AD tartományi szolgáltatások összes feladatának végrehajtása | Azure AD DC rendszergazdák csoportja ([lásd a dokumentációt](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain)) | 
Az összes konfiguráció olvasása | Az AD DS-szolgáltatást tartalmazó Azure-előfizetés olvasója | 

## <a name="devices"></a>Eszközök

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Eszköz letiltása | Felhőeszköz-rendszergazda | 
Eszköz engedélyezése | Felhőeszköz-rendszergazda | 
Alapkonfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
BitLocker-kulcsok olvasása | Biztonsági olvasó | Jelszó-rendszergazda, biztonsági rendszergazda

## <a name="enterprise-applications"></a>Vállalati alkalmazások

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Hozzájárulás a delegált engedélyekhez | Felhőalkalmazás-rendszergazda | Alkalmazás-rendszergazda
Hozzájárulás az alkalmazásengedélyekhez a Microsoft Graph nélkül | Felhőalkalmazás-rendszergazda | Alkalmazás-rendszergazda
Hozzájárulás a Microsoft Graph alkalmazásengedélyeinek alkalmazásához | Kiemelt szerepkör-rendszergazda | 
Hozzájárulás a saját adatokhoz hozzáférő alkalmazásokhoz | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Vállalati alkalmazás létrehozása | Felhőalkalmazás-rendszergazda | Alkalmazás-rendszergazda
Alkalmazásproxy kezelése | Alkalmazás-rendszergazda | 
Felhasználói beállítások kezelése | Globális rendszergazda | 
Csoport vagy alkalmazás hozzáférési felülvizsgálatának olvasása | Biztonsági olvasó | Biztonsági rendszergazda, felhasználó
Az összes konfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Vállalati alkalmazás-hozzárendelések frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda
Vállalati alkalmazástulajdonosok frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda
Vállalati alkalmazástulajdonságok frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda
Vállalati alkalmazás-kiépítés frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda
Vállalati alkalmazás-önkiszolgáló frissítés | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda
Egyszeri bejelentkezési tulajdonságok frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda

## <a name="entitlement-management"></a>Jogosultságkezelés
Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Erőforrások hozzáadása katalógushoz | Rendszergazda | A jogosultságkezeléssel átruházhatja ezt a feladatot a katalógus tulajdonosának ([lásd a dokumentációt](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners))
SharePoint Online-webhelyek hozzáadása a katalógushoz | Globális rendszergazda


## <a name="groups"></a>Csoportok

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Licenc hozzárendelése | Rendszergazda | 
Csoport létrehozása | Rendszergazda | 
Csoport vagy alkalmazás hozzáférési felülvizsgálatának létrehozása, frissítése vagy törlése | Rendszergazda | 
Csoport lejáratának kezelése | Rendszergazda | 
Csoportbeállítások kezelése | Csoportok rendszergazdája | Felhasználói rendszergazda | 
Az összes konfiguráció olvasása (kivéve a rejtett tagságot) | Címtárolvasók | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Rejtett tagság olvasása | Csoporttag | Csoporttulajdonos, Jelszó-rendszergazda, Exchange-rendszergazda, SharePoint-rendszergazda, Teams-rendszergazda, Felhasználói rendszergazda
Rejtett tagsággal rendelkező csoportok tagságának olvasása | Ügyfélszolgálati rendszergazda | Felhasználói rendszergazda, Teams-rendszergazda
Licenc visszavonása | Licencrendszergazda | Rendszergazda
Csoporttagság frissítése | Csoport tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Rendszergazda
Csoporttulajdonosok frissítése | Csoport tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Rendszergazda
Csoport tulajdonságainak frissítése | Csoport tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Rendszergazda

## <a name="identity-protection"></a>Identity Protection

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Riasztási értesítések konfigurálása| Biztonsági rendszergazda | 
MFA-házirend konfigurálása és engedélyezése vagy letiltása| Biztonsági rendszergazda | 
Bejelentkezési kockázati házirend konfigurálása és engedélyezése vagy letiltása| Biztonsági rendszergazda | 
Felhasználói kockázati házirend konfigurálása és engedélyezése vagy letiltása | Biztonsági rendszergazda | 
Heti kivonatolás konfigurálása | Biztonsági rendszergazda| 
Az összes kockázatészlelés elvetése | Biztonsági rendszergazda | 
A biztonsági rés javítása vagy elvetése | Biztonsági rendszergazda | 
Az összes konfiguráció olvasása | Biztonsági olvasó | 
Olvassa el az összes kockázatészlelést | Biztonsági olvasó | 
Biztonsági rések olvasása | Biztonsági olvasó | 

## <a name="licenses"></a>Licencek

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Licenc hozzárendelése | Licencrendszergazda | Rendszergazda
Az összes konfiguráció olvasása | Címtárolvasók | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Licenc visszavonása | Licencrendszergazda | Rendszergazda
Próbálja ki vagy vásárolja meg az előfizetést | Számlázási rendszergazda | 


## <a name="monitoring---audit-logs"></a>Figyelés - Naplónaplók

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Naplónaplók olvasása | Jelentések olvasó | Biztonsági olvasó, biztonsági rendszergazda

## <a name="monitoring---sign-ins"></a>Figyelés - Bejelentkezések

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Bejelentkezési naplók olvasása | Jelentések olvasó | Biztonsági olvasó, biztonsági rendszergazda

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
A kijelölt felhasználók által létrehozott összes meglévő alkalmazásjelszó törlése | Globális rendszergazda | 
MFA letiltása | Globális rendszergazda | 
MFA engedélyezése | Globális rendszergazda | 
Az MFA-szolgáltatás beállításainak kezelése | Globális rendszergazda | 
A kijelölt felhasználók számára a kapcsolatfelvételi módszerek ismételt megadásának megkövetelése | Hitelesítési rendszergazda | 
Többtényezős hitelesítés visszaállítása az összes megjegyezteszközre  | Hitelesítési rendszergazda | 

## <a name="mfa-server"></a>MFA-kiszolgáló

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Felhasználók blokkolása/feloldása | Globális rendszergazda | 
Fiókzárolás konfigurálása | Globális rendszergazda | 
Gyorsítótárazási szabályok konfigurálása | Globális rendszergazda | 
Csalási riasztás konfigurálása | Globális rendszergazda
Értesítések konfigurálása | Globális rendszergazda | 
Egyszeri megkerülés konfigurálása | Globális rendszergazda | 
Telefonhívás-beállítások konfigurálása | Globális rendszergazda | 
Szolgáltatók konfigurálása | Globális rendszergazda | 
Kiszolgálóbeállítások konfigurálása | Globális rendszergazda | 
Tevékenységjelentés olvasása | Globális olvasó | 
Az összes konfiguráció olvasása | Globális olvasó | 
Kiszolgáló állapotának olvasása | Globális olvasó |  

## <a name="organizational-relationships"></a>Céges kapcsolatok

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Identitásszolgáltatók kezelése | Külső identitásszolgáltató rendszergazdája | 
Beállítások kezelése | Globális rendszergazda | 
Használati feltételek kezelése | Globális rendszergazda | 
Az összes konfiguráció olvasása | Globális olvasó | 

## <a name="password-reset"></a>Új jelszó létrehozása

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Hitelesítési módszerek konfigurálása | Globális rendszergazda |
Testreszabás konfigurálása | Globális rendszergazda |
Értesítés konfigurálása | Globális rendszergazda |
Helyszíni integráció konfigurálása | Globális rendszergazda |
Jelszó-visszaállítási tulajdonságok konfigurálása | Felhasználói rendszergazda | Globális rendszergazda
Regisztráció konfigurálása | Globális rendszergazda |
Az összes konfiguráció olvasása | Biztonsági rendszergazda | Felhasználói rendszergazda |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Felhasználók hozzárendelése szerepkörökhöz | Kiemelt szerepkör-rendszergazda | 
Szerepkör-beállítások konfigurálása | Kiemelt szerepkör-rendszergazda | 
Naplózási tevékenység megtekintése | Biztonsági olvasó | 
Szerepkör-tagságok megtekintése | Biztonsági olvasó | 

## <a name="roles-and-administrators"></a>Szerepkörök és rendszergazdák

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Szerepkör-hozzárendelések kezelése | Kiemelt szerepkör-rendszergazda | 
Azure AD-szerepkör hozzáférés-felülvizsgálatának olvasása  | Biztonsági olvasó | Biztonsági rendszergazda, Kiemelt szereprendszergazda
Az összes konfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Biztonság - Hitelesítési módszerek

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Hitelesítési módszerek konfigurálása | Globális rendszergazda | 
Az összes konfiguráció olvasása | Globális olvasó | 

## <a name="security---conditional-access"></a>Biztonság - feltételes hozzáférés

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
MFA-megbízható IP-címek konfigurálása | Feltételes hozzáférés rendszergazdája | 
Egyéni vezérlők létrehozása | Feltételes hozzáférés rendszergazdája | Biztonsági rendszergazda
Elnevezett helyek létrehozása | Feltételes hozzáférés rendszergazdája | Biztonsági rendszergazda
Szabályzatok létrehozása | Feltételes hozzáférés rendszergazdája | Biztonsági rendszergazda
Használati feltételek létrehozása | Feltételes hozzáférés rendszergazdája | Biztonsági rendszergazda
VPN-kapcsolati tanúsítvány létrehozása | Feltételes hozzáférés rendszergazdája | Biztonsági rendszergazda
Klasszikus házirend törlése | Feltételes hozzáférés rendszergazdája | Biztonsági rendszergazda
Használati feltételek törlése | Feltételes hozzáférés rendszergazdája | Biztonsági rendszergazda
VPN-kapcsolati tanúsítvány törlése | Feltételes hozzáférés rendszergazdája | Biztonsági rendszergazda
Klasszikus házirend letiltása | Feltételes hozzáférés rendszergazdája | Biztonsági rendszergazda
Egyéni vezérlők kezelése | Feltételes hozzáférés rendszergazdája | Biztonsági rendszergazda
Elnevezett helyek kezelése | Feltételes hozzáférés rendszergazdája | Biztonsági rendszergazda
Használati feltételek kezelése | Feltételes hozzáférés rendszergazdája | Biztonsági rendszergazda
Az összes konfiguráció olvasása | Biztonsági olvasó | Biztonsági rendszergazda
Elnevezett helyek olvasása | Biztonsági olvasó | Feltételes hozzáférés rendszergazda, biztonsági rendszergazda

## <a name="security---identity-security-score"></a>Biztonság – Identitásbiztonsági pontszám

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök | 
---- | --------------------- | ----------------
Az összes konfiguráció olvasása | Biztonsági olvasó | Biztonsági rendszergazda
Biztonsági pontszám olvasása | Biztonsági olvasó | Biztonsági rendszergazda
Eseményállapot frissítése | Biztonsági rendszergazda | 

## <a name="security---risky-sign-ins"></a>Biztonság – Kockázatos bejelentkezések

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Az összes konfiguráció olvasása | Biztonsági olvasó | 
Kockázatos bejelentkezések olvasása | Biztonsági olvasó | 

## <a name="security---users-flagged-for-risk"></a>Biztonság – Kockázatra megjelölt felhasználók

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Az összes esemény elvetését | Biztonsági rendszergazda | 
Az összes konfiguráció olvasása | Biztonsági olvasó | 
Kockázatra megjelölt felhasználók olvasása | Biztonsági olvasó | 

## <a name="users"></a>Felhasználók

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Felhasználó hozzáadása a címtárszerepkörhöz | Kiemelt szerepkör-rendszergazda | 
Felhasználó hozzáadása a csoporthoz | Rendszergazda | 
Licenc hozzárendelése | Licencrendszergazda | Rendszergazda
Vendégfelhasználó létrehozása | Vendég meghívó | Rendszergazda
Felhasználó létrehozása | Rendszergazda | 
Felhasználók törlése | Rendszergazda | 
Korlátozott rendszergazdák frissítési jogkivonatainak érvénytelenítése (lásd a dokumentációt) | Rendszergazda | 
Nem rendszergazdák frissítési jogkivonatainak érvénytelenítése (lásd a dokumentációt) | Jelszókezelő | Rendszergazda
Jogosultsággal rendelkező rendszergazdák frissítési jogkivonatainak érvénytelenítése (lásd a dokumentációt) | Kiemelt hitelesítési rendszergazda | 
Alapkonfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Korlátozott rendszergazdák jelszavának alaphelyzetbe állítása (lásd a dokumentációt) | Rendszergazda | 
Nem rendszergazdák jelszavának alaphelyzetbe állítása (lásd a dokumentációt) | Jelszókezelő | Rendszergazda
Kiemelt jogosultságú rendszergazdák jelszavának alaphelyzetbe állítása | Kiemelt hitelesítési rendszergazda | 
Licenc visszavonása | Licencrendszergazda | Rendszergazda
Az egyszerű felhasználónév kivételével az összes tulajdonság frissítése | Rendszergazda | 
Az egyszerű felhasználónév frissítése korlátozott rendszergazdák számára (lásd a dokumentációt) | Rendszergazda | 
Az egyszerű felhasználónév tulajdonság frissítése kiemelt rendszergazdáknál (lásd a dokumentációt) | Globális rendszergazda | 
Felhasználói beállítások frissítése | Globális rendszergazda | 


## <a name="support"></a>Támogatás

Tevékenység | Legkevésbé privilegizált szerepkör | További szerepkörök
---- | --------------------- | ----------------
Támogatási jegy beküldése | Szolgáltatás-rendszergazda | Alkalmazásrendszergazda, Azure Information Protection Administrator, Billing Administrator, Cloud Application Administrator, Compliance Administrator, Dynamics 365 administrator, Desktop Analytics Administrator, Exchange Administrator, Password Administrator, Intune Administrator, Skype Vállalati rendszergazda, Power BI-rendszergazda, kiemelt hitelesítési rendszergazda, SharePoint-rendszergazda, Teams kommunikációs rendszergazda, Teams-rendszergazda, Felhasználói rendszergazda, Workplace Analytics-rendszergazda

## <a name="next-steps"></a>További lépések

* [Az azure AD rendszergazdai szerepkörök hozzárendelése vagy eltávolítása](directory-manage-roles-portal.md)
* [Az Azure AD rendszergazdai szerepkörök hivatkozása](directory-assign-admin-roles.md)
