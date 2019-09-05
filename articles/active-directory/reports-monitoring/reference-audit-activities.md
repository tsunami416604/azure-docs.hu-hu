---
title: Az Azure Active Directory (Azure AD) naplózott tevékenységeire vonatkozó referencia | Microsoft Docs
description: Tekintse át az Azure Active Directoryban (Azure AD) naplókban rögzíthető tevékenységeket.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: aaa60a7737b7781a21e23516d139332f10bdf448
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306003"
---
# <a name="azure-ad-audit-activity-reference"></a>Azure AD naplózott tevékenységekre vonatkozó referencia

A Azure Active Directory-(Azure AD-) jelentésekkel megtekintheti a környezete működésének meghatározásához szükséges információkat.

Az Azure AD jelentéskészítési architektúrája a következő elemekből áll:

- **Tevékenységgel kapcsolatos jelentések** 
    - [Bejelentkezések](concept-sign-ins.md) – információkat biztosít a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról
    - [Auditnaplók](concept-audit-logs.md) – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. 
    
- **Biztonsági jelentések** 
    - [Kockázatos bejelentkezések](concept-risky-sign-ins.md) – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. 
    - [Kockázatosként megjelölt felhasználók](concept-user-at-risk.md) – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. 

Ez a cikk a naplókban rögzíthető tevékenységeket sorolja fel.

## <a name="access-reviews"></a>Hozzáférési felülvizsgálatok

|Naplózási kategória|Tevékenység|
|---|---|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálat befejezve|
|Hozzáférési felülvizsgálatok|Jóváhagyó hozzáadása a kérelem-jóváhagyáshoz|
|Hozzáférési felülvizsgálatok|Felülvizsgáló hozzáadása a hozzáférési felülvizsgálathoz|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálat alkalmazása|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálat létrehozása|
|Hozzáférési felülvizsgálatok|Program létrehozása|
|Hozzáférési felülvizsgálatok|Kérelem-jóváhagyás létrehozása|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálat törlése|
|Hozzáférési felülvizsgálatok|Program törlése|
|Hozzáférési felülvizsgálatok|Programvezérlő csatolása|
|Hozzáférési felülvizsgálatok|Azure AD hozzáférési felülvizsgálatok előkészítése|
|Hozzáférési felülvizsgálatok|Felülvizsgáló eltávolítása a hozzáférési felülvizsgálatból|
|Hozzáférési felülvizsgálatok|Felülvizsgálat leállításának kérése|
|Hozzáférési felülvizsgálatok|Felülvizsgálati eredmény alkalmazásának kérése|
|Hozzáférési felülvizsgálatok|RBAC-szerepkörtagság felülvizsgálata|
|Hozzáférési felülvizsgálatok|Alkalmazás-hozzárendelés felülvizsgálata|
|Hozzáférési felülvizsgálatok|Csoporttagság felülvizsgálata|
|Hozzáférési felülvizsgálatok|Kérelem-jóváhagyási kérelem felülvizsgálata|
|Hozzáférési felülvizsgálatok|Programvezérlő leválasztása|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálat frissítése|
|Hozzáférési felülvizsgálatok|Az Azure AD hozzáférési felülvizsgálatok bevezetési állapotának frissítése|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálati e-mail-értesítési beállításainak frissítése|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálati ismétlődési szám beállításának frissítése|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálat ismétlődési időtartamának frissítése napokban beállítás|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálati ismétlődési típus beállításának frissítése|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálati ismétlődési típus beállításának frissítése|
|Hozzáférési felülvizsgálatok|Hozzáférési felülvizsgálati emlékeztető beállításainak frissítése|
|Hozzáférési felülvizsgálatok|Program frissítése|
|Hozzáférési felülvizsgálatok|Kérelem-jóváhagyás frissítése|
|Hozzáférési felülvizsgálatok|Felhasználó letiltva|

## <a name="account-provisioning"></a>Fiók kiépítése

|Naplózási kategória|Tevékenység|
|---|---|
|Alkalmazáskezelés|V2 alkalmazás engedélymegadásainak beolvasása|
|Alkalmazáskezelés|V2 alkalmazások szolgáltatásnevének lekérése az aktuális bérlőben|
|Alkalmazáskezelés|V1 alkalmazás frissítése|
|Alkalmazáskezelés|V2 alkalmazás frissítése|
|Alkalmazáskezelés|V2 alkalmazás engedélymegadásának frissítése|
|Alkalmazáskezelés|Oauth2Permissiongrant hozzáadása|
|Alkalmazáskezelés|Alkalmazásszerepkör-hozzárendelés hozzáadása a szolgáltatásnévhez|

## <a name="application-proxy"></a>Alkalmazásproxy

|Naplózási kategória|Tevékenység|
|---|---|
|Alkalmazáskezelés|Alkalmazás hozzáadása|
|Alkalmazáskezelés|Tulajdonos hozzáadása alkalmazáshoz|
|Alkalmazáskezelés|Tulajdonos hozzáadása szolgáltatásnévhez|
|Alkalmazáskezelés|Szabályzat hozzáadása szolgáltatásnévhez|
|Címtárkezelés|Szolgáltatásnév hozzáadása|
|Címtárkezelés|Szolgáltatásnév hitelesítő adatainak hozzáadása|
|Címtárkezelés|Hozzájárulás az alkalmazáshoz|
|Címtárkezelés|Alkalmazás törlése|
|Címtárkezelés|Alkalmazás végleges törlése|
|Címtárkezelés|Az Oauth2Permissiongrant eltávolítása|
|Címtárkezelés|Alkalmazásszerepkör-hozzárendelés eltávolítása a szolgáltatásnévből|
|Címtárkezelés|Tulajdonos eltávolítása az alkalmazásból|
|Resource|Tulajdonos eltávolítása az egyszerű szolgáltatásnévből|
|Resource|Szabályzat eltávolítása az egyszerű szolgáltatásnévből|
|Resource|Szolgáltatásnév eltávolítása|


## <a name="automated-password-rollover"></a>Automatizált jelszóváltás

|Naplózási kategória|Tevékenység|
|---|---|
|Alkalmazáskezelés|Egyszerű szolgáltatásnév hitelesítő adatainak eltávolítása|


## <a name="b2c"></a>B2C

|Naplózási kategória|Tevékenység|
|---|---|
|Alkalmazáskezelés|Alkalmazás visszaállítása|
|Alkalmazáskezelés|Jóváhagyás visszavonása|
|Alkalmazáskezelés|Az alkalmazás frissítése|
|Alkalmazáskezelés|Külső titkos kódok frissítése|
|Alkalmazáskezelés|Szolgáltatásnév frissítése|
|Alkalmazáskezelés|Hozzáférési jogkivonat kiadása az alkalmazásnak|
|Alkalmazáskezelés|Hitelesítési kód kiadása az alkalmazásnak|
|Alkalmazáskezelés|id_token kiadása az alkalmazásnak|
|Alkalmazáskezelés|Helyi fiók hitelesítő adatainak ellenőrzése|
|Alkalmazáskezelés|Felhasználóhitelesítés ellenőrzése|
|Alkalmazáskezelés|V2 alkalmazásengedélyek hozzáadása|
|Alkalmazáskezelés|ASCII-titkosításon alapuló kulcs hozzáadása CPIM-kulcstárolóhoz|
|Alkalmazáskezelés|Kulcs hozzáadása CPIM-kulcstárolóhoz|
|Alkalmazáskezelés|AdminPolicyDatas-SetResources|
|Alkalmazáskezelés|AdminUserJourneys-GetResources|
|Alkalmazáskezelés|AdminUserJourneys-RemoveResources|
|Authentication|AdminUserJourneys-SetResources|
|Authentication|IdentityProvider létrehozása|
|Authentication|V1 alkalmazás létrehozása|
|Authentication|V2 alkalmazás létrehozása|
|Authentication|Egyéni tartomány létrehozása a bérlőben|
|Authorization|Új AdminUserJourney létrehozása|
|Authorization|Honosított json-erőforrás létrehozása|
|Authorization|Új egyéni identitásszolgáltató létrehozása|
|Authorization|Új identitásszolgáltató létrehozása|
|Authorization|B2C címtárerőforrás létrehozása vagy frissítése|
|Authorization|Szabályzat létrehozása|
|Authorization|trustFramework szabályzat létrehozása|
|Authorization|Konfigurálható előtaggal rendelkező trustFramework szabályzat létrehozása|
|Authorization|Felhasználói attribútum létrehozása|
|Authorization|CreateTrustFrameworkPolicy|
|Authorization|Új AdminUserJourney létrehozása vagy frissítése|
|Authorization|Identitásszolgáltató törlése|
|Authorization|IdentityProvider törlése|
|Authorization|V1 alkalmazás törlése|
|Authorization|V2 alkalmazás törlése|
|Authorization|V2 alkalmazás engedélymegadásának törlése|
|Authorization|B2C címtárerőforrás törlése|
|Authorization|CPIM-kulcstároló törlése|
|Authorization|trustFramework szabályzat törlése|
|Authorization|Felhasználói attribútum törlése|
|Authorization|B2C funkció engedélyezése|
|Authorization|Előfizetéshez tartozó B2C címtárerőforrások lekérése|
|Authorization|Egyéni identitásszolgáltató lekérése|
|Authorization|Identitásszolgáltató lekérése|
|Authorization|V1 és V2 alkalmazások lekérése|
|Authorization|V1 alkalmazás lekérése|
|Authorization|V1 alkalmazások lekérése|
|Authorization|V2 alkalmazás lekérése|
|Authorization|V2 alkalmazások lekérése|
|Authorization|B2C-címtár erőforrásának beolvasása|
|Authorization|A bérlőben lévő egyéni tartományok listájának lekérése|
|Authorization|Felhasználói út lekérése|
|Authorization|A felhasználói út engedélyezett alkalmazásjogcímeinek lekérése|
|Authorization|A felhasználói út engedélyezett önellenőrzött jogcímeinek lekérése|
|Authorization|A szabályzat engedélyezett önellenőrzött jogcímeinek lekérése|
|Authorization|Elérhető kimeneti jogcímek listájának lekérése|
|Authorization|A felhasználói út tartalomdefinícióinak lekérése|
|Authorization|Adott rendszergazdai folyamathoz tartozó identitásszolgáltatók lekérése|
|Authorization|Kulcstároló aktív kulcsaihoz tartozó metaadatok lekérése JWK-ban|
|Authorization|Az összes rendszergazdai folyamat|
|Authorization|Az összes felhasználó összes rendszergazdai folyamatához tartozó címkék listájának lekérése|
|Authorization|Felhasználó bérlői listájának lekérése|
|Authorization|Helyi fiókok önellenőrző jogcímeinek lekérése|
|Authorization|Honosított json-erőforrás lekérése|
|Authorization|A Microsoft.AzureActiveDirectory erőforrás-szolgáltató műveleteinek lekérése|
|Authorization|Szabályzatok lekérése|
|Authorization|Szabályzat lekérése|
|Authorization|Bérlő erőforrás-tulajdonságainak lekérése|
|Authorization|Támogatott identitásszolgáltatók listájának lekérése|
|Authorization|A felhasználói út támogatott identitásszolgáltatói listájának lekérése|
|Authorization|Bérlői adatok lekérése|
|Authorization|Bérlő számára engedélyezett funkciók lekérése|
|Authorization|Bérlő által definiált egyéni identitásszolgáltatók listájának lekérése|
|Authorization|Bérlő által definiált identitásszolgáltatók listájának lekérése|
|Authorization|Bérlő által definiált helyi identitásszolgáltató-lista lekérése|
|Authorization|Felhasználó bérlői információinak lekérése erőforrások létrehozásához|
|Authorization|Bérlők listájának lekérése|
|Authorization|tenantDomains lekérése|
|Authorization|A CPIM alapértelmezett támogatott kultúrájának lekérése|
|Authorization|Rendszergazdai folyamathoz tartozó információk lekérése|
|Authorization|A bérlőhöz tartozó UserJourneys lista lekérése|
|Authorization|A CPIM-hez tartozó elérhető, támogatott kultúrák készletének lekérése|
|Authorization|TrustFramework szabályzat lekérése|
|Authorization|TrustFramework szabályzat lekérése xml-ként|
|Authorization|Felhasználói attribútum lekérése|
|Authorization|Felhasználói attribútumok lekérése|
|Authorization|Felhasználói utak listájának lekérése|
|Authorization|GetIEFPolicies|
|Authorization|GetIdentityProviders|
|Authorization|GetTrustFrameworkPolicy|
|Authorization|Lekérdez egy CPIM-kulcstárolót jwk formátumban|
|Authorization|Lekérdezi a bérlőben lévő kulcstárolók listáját|
|Authorization|Lekérdezi a bérlő típusát|
|Authorization|MigrateTenantMetadata|
|Authorization|IdentityProvider javítása|
|Authorization|PutTrustFrameworkPolicy|
|Authorization|PutTrustFrameworkpolicy|
|Authorization|Felhasználói út eltávolítása|
|Authorization|CPIM-kulcstároló biztonsági másolatának visszaállítása|
|Authorization|V2 alkalmazás engedélymegadásainak beolvasása|
|Authorization|V2 alkalmazások szolgáltatásnevének lekérése az aktuális bérlőben|
|Authorization|Egyéni identitásszolgáltató frissítése|
|Authorization|Identitásszolgáltató frissítése|
|Authorization|Helyi IDP frissítése|
|Authorization|V1 alkalmazás frissítése|
|Authorization|V2 alkalmazás frissítése|
|Authorization|V2 alkalmazás engedélymegadásának frissítése|
|Authorization|Szabályzat frissítése|
|Authorization|Felhasználói attribútum frissítése|
|Authorization|CPIM titkosított kulcs feltöltése|
|Authorization|Felhasználói hitelesítés: Az API le van tiltva a bérlői featureset|
|Authorization|Felhasználói hitelesítés: A felhasználó bérlői Admin' kapott hozzáférést|
|Authorization|Felhasználói hitelesítés: A felhasználó "hitelesített felhasználók" hozzáférési jogosultságokat kapott|
|Authorization|B2C funkció engedélyezésének ellenőrzése|
|Authorization|Annak ellenőrzése, hogy a szolgáltatás engedélyezve van-e|
|Authorization|Program létrehozása|
|Authorization|Program törlése|
|Authorization|Programvezérlő csatolása|
|Authorization|Azure AD hozzáférési felülvizsgálatok előkészítése|
|Authorization|Programvezérlő leválasztása|
|Authorization|Program frissítése|
|Authorization|Asztali egyszeri bejelentkezés letiltása|
|Authorization|Asztali egyszeri bejelentkezés letiltása adott tartományon|
|Authorization|Alkalmazásproxy letiltása|
|Authorization|Átmenő hitelesítés letiltása|
|Authorization|Asztali egyszeri bejelentkezés engedélyezése|
|Címtárkezelés|Asztali egyszeri bejelentkezés engedélyezése adott tartományhoz|
|Címtárkezelés|Alkalmazásproxy engedélyezése|
|Címtárkezelés|Átmenő hitelesítés engedélyezése|
|Címtárkezelés|Egyéni tartomány létrehozása a bérlőben|
|Címtárkezelés|B2C funkció engedélyezése|
|Címtárkezelés|A bérlőben lévő egyéni tartományok listájának lekérése|
|Címtárkezelés|Bérlő erőforrás-tulajdonságainak lekérése|
|Címtárkezelés|Bérlői adatok lekérése|
|Címtárkezelés|Bérlő számára engedélyezett funkciók lekérése|
|Címtárkezelés|tenantDomains lekérése|
|Kulcs|Lekérdezi a bérlő típusát|
|Kulcs|B2C funkció engedélyezésének ellenőrzése|
|Kulcs|Annak ellenőrzése, hogy a szolgáltatás engedélyezve van-e|
|Kulcs|Partner hozzáadása vállalathoz|
|Kulcs|Nem ellenőrzött tartomány hozzáadása|
|Kulcs|Ellenőrzött tartomány hozzáadása|
|Kulcs|Vállalat létrehozása|
|Kulcs|Vállalati beállítások létrehozása|
|Kulcs|Vállalati beállítások törlése|
|Kulcs|Partner lefokozása|
|Kulcs|Címtár törölve|
|Egyéb|Címtár véglegesen törölve|
|Egyéb|Ez a címtár törlésre van ütemezve|
|Resource|Vállalat előléptetése partnerré|
|Resource|Rights Management-tulajdonságok végleges törlése|
|Resource|Partner eltávolítása a vállalatból|
|Resource|Nem ellenőrzött tartomány eltávolítása|
|Resource|Ellenőrzött tartomány eltávolítása|
|Resource|Cégadatok beállítása|
|Resource|A DirSync funkció beállítása|
|Resource|A DirSyncEnabled jelölő beállítása|
|Resource|Partnerség beállítása|
|Resource|Véletlen törlés küszöbértékének beállítása|
|Resource|Vállalati adatok engedélyezett helyének beállítása|
|Resource|Vállalati nemzetközi szolgáltatás engedélyezésének beállítása|
|Resource|Címtárszolgáltatás beállítása a bérlőn|
|Resource|Tartomány hitelesítésének beállítása|
|Resource|Tartomány összevonási beállításainak beállítása|
|Resource|Jelszószabályzat beállítása|
|Resource|Rights Management tulajdonságainak beállítása|
|Resource|Vállalat frissítése|
|Resource|Vállalati beállítások frissítése|
|Resource|Tartomány frissítése|
|Resource|Tartomány ellenőrzése|
|Resource|E-mailek ellenőrzött tartományának ellenőrzése|
|Resource|Előkészítés|
|Resource|Riasztási beállítások frissítése|
|Resource|Heti összefoglaló beállításainak frissítése|
|Resource|Jelszóvisszaíró letiltása a címtárhoz|
|Resource|Jelszóvisszaíró engedélyezése a címtárhoz|
|Resource|Alkalmazásszerepkör-hozzárendelés hozzáadása csoporthoz|
|Resource|Csoport hozzáadása|
|Resource|Tag hozzáadása csoporthoz|
|Resource|Tulajdonos hozzáadása csoporthoz|
|Resource|Csoportbeállítások létrehozása|
|Resource|Csoport törlése|
|Resource|Csoportbeállítások törlése|
|Resource|Csoportalapú licenc felhasználókra való alkalmazásának befejezése|
|Resource|Csoport végleges törlése|
|Resource|Alkalmazás szerepkör-hozzárendelésének eltávolítása a csoportból|
|Resource|Tag eltávolítása a csoportból|
|Resource|Tulajdonos eltávolítása a csoportból|
|Resource|Csoport visszaállítása|
|Resource|Csoportlicenc beállítása|
|Resource|Csoport beállítása felhasználó által kezeltként|
|Resource|Csoportalapú licenc felhasználókra való alkalmazásának megkezdése|
|Resource|Csoportlicenc újraszámolásának aktiválása|
|Resource|Csoport frissítése|
|Resource|Csoportbeállítások frissítése|
|Resource|Tag hozzáadása|
|Resource|Csoport létrehozása|
|Resource|Csoport törlése|
|Resource|Tag eltávolítása|
|Resource|Csoport frissítése|
|Resource|Csoporthoz való csatlakoztatásra vonatkozó, függő kérelem jóváhagyása|
|Resource|Csoporthoz való csatlakoztatásra vonatkozó, függő kérelem visszavonása|
|Resource|Életciklus-kezelési szabályzat létrehozása|
|Resource|Csoporthoz való csatlakoztatásra vonatkozó, függő kérelem törlése|
|Resource|Csoporthoz való csatlakoztatásra vonatkozó, függő kérelem elutasítása|
|Resource|A csoport megújítása|
|Resource|Csoporttagság kérése|
|Resource|Dinamikus csoport tulajdonságainak beállítása|
|Resource|Életciklus-kezelési szabályzat frissítése|
|Resource|ASCII-titkosításon alapuló kulcs hozzáadása CPIM-kulcstárolóhoz|
|Resource|Kulcs hozzáadása CPIM-kulcstárolóhoz|
|Resource|CPIM-kulcstároló törlése|
|Resource|Kulcstároló törlése|
|Resource|Kulcstároló aktív kulcsaihoz tartozó metaadatok lekérése JWK-ban|
|Resource|Kulcstároló metaadatainak lekérése|
|Resource|Lekérdez egy CPIM-kulcstárolót jwk formátumban|
|Resource|Lekérdezi a bérlőben lévő kulcstárolók listáját|
|Resource|CPIM-kulcstároló biztonsági másolatának visszaállítása|
|Resource|Kulcstároló mentése|
|Resource|CPIM titkosított kulcs feltöltése|
|Resource|Hitelesítési kód kiadása az alkalmazásnak|
|Resource|id_token kiadása az alkalmazásnak|


## <a name="core-directory"></a>Alapvető könyvtár

|Naplózási kategória|Tevékenység|
|---|---|
|Adminisztratív egységek kezelése|Egyetlen kockázati észlelési típus letöltése|
|Adminisztratív egységek kezelése|Rendszergazdák és a heti összefoglalók letöltésének jóváhagyása|
|Adminisztratív egységek kezelése|Az összes kockázati észlelési típus letöltése|
|Adminisztratív egységek kezelése|Ingyenes felhasználói kockázati észlelések letöltése|
|Adminisztratív egységek kezelése|Kockázatosként megjelölt felhasználók letöltése|
|Alkalmazáskezelés|Feldolgozott kötegelt meghívók|
|Alkalmazáskezelés|Kötegelt meghívók feltöltve|
|Alkalmazáskezelés|Tulajdonos hozzáadása szabályzathoz|
|Alkalmazáskezelés|Szabályzat hozzáadása|
|Alkalmazáskezelés|Házirend törlése|
|Alkalmazáskezelés|Szabályzat hitelesítő adatainak eltávolítása|
|Alkalmazáskezelés|Szabályzat frissítése|
|Alkalmazáskezelés|Az MFA regisztrációs szabályzatának beállítása|
|Alkalmazáskezelés|Bejelentkezési kockázati szabályzat beállítása|
|Alkalmazáskezelés|Felhasználói kockázati szabályzat beállítása|
|Alkalmazáskezelés|Használati feltételek elfogadása|
|Alkalmazáskezelés|Használati feltételek létrehozása|
|Alkalmazáskezelés|Használati feltételek elutasítása|
|Alkalmazáskezelés|Használati feltételek törlése|
|Alkalmazáskezelés|Használati feltételek szerkesztése|
|Alkalmazáskezelés|Használati feltételek közzététele|
|Alkalmazáskezelés|Használati feltételek közzétételének visszavonása|
|Alkalmazáskezelés|Alkalmazás SSL-tanúsítványának hozzáadása|
|Alkalmazáskezelés|SSL-kötés törlése|
|Alkalmazáskezelés|Összekötő regisztrálása|
|Alkalmazáskezelés|AdminPolicyDatas-RemoveResources|
|Alkalmazáskezelés|AdminPolicyDatas-SetResources|
|Alkalmazáskezelés|AdminUserJourneys-GetResources|
|Címtárkezelés|AdminUserJourneys-RemoveResources|
|Címtárkezelés|AdminUserJourneys-SetResources|
|Címtárkezelés|IdentityProvider létrehozása|
|Címtárkezelés|Új AdminUserJourney létrehozása|
|Címtárkezelés|Honosított json-erőforrás létrehozása|
|Címtárkezelés|Új egyéni identitásszolgáltató létrehozása|
|Címtárkezelés|Új identitásszolgáltató létrehozása|
|Címtárkezelés|B2C címtárerőforrás létrehozása vagy frissítése|
|Címtárkezelés|Szabályzat létrehozása|
|Címtárkezelés|trustFramework szabályzat létrehozása|
|Címtárkezelés|Konfigurálható előtaggal rendelkező trustFramework szabályzat létrehozása|
|Címtárkezelés|Felhasználói attribútum létrehozása|
|Címtárkezelés|CreateTrustFrameworkPolicy|
|Címtárkezelés|Identitásszolgáltató törlése|
|Címtárkezelés|IdentityProvider törlése|
|Címtárkezelés|B2C címtárerőforrás törlése|
|Címtárkezelés|trustFramework szabályzat törlése|
|Címtárkezelés|Felhasználói attribútum törlése|
|Címtárkezelés|Erőforráscsoporthoz tartozó B2C címtárerőforrások lekérése|
|Címtárkezelés|Előfizetéshez tartozó B2C címtárerőforrások lekérése|
|Címtárkezelés|Egyéni identitásszolgáltató lekérése|
|Címtárkezelés|Identitásszolgáltató lekérése|
|Címtárkezelés|B2C-címtár erőforrásának beolvasása|
|Címtárkezelés|Felhasználói út lekérése|
|Címtárkezelés|A felhasználói út engedélyezett alkalmazásjogcímeinek lekérése|
|Címtárkezelés|A felhasználói út engedélyezett önellenőrzött jogcímeinek lekérése|
|Címtárkezelés|A szabályzat engedélyezett önellenőrzött jogcímeinek lekérése|
|Címtárkezelés|Elérhető kimeneti jogcímek listájának lekérése|
|Címtárkezelés|A felhasználói út tartalomdefinícióinak lekérése|
|Címtárkezelés|Adott rendszergazdai folyamathoz tartozó identitásszolgáltatók lekérése|
|Címtárkezelés|Az összes rendszergazdai folyamat|
|Címtárkezelés|Az összes felhasználó összes rendszergazdai folyamatához tartozó címkék listájának lekérése|
|Csoportkezelés|Csoporttagok tömeges letöltése – elindítva|
|Csoportkezelés|Csoporttagok tömeges letöltése – kész|
|Csoportkezelés|Csoporttagok csoportos importálása – elindítva|
|Csoportkezelés|Csoporttagok tömeges importálása – kész|
|Csoportkezelés|Csoporttagok tömeges eltávolítása – elindítva|
|Csoportkezelés|Csoporttagok tömeges eltávolítása – kész|
|Csoportkezelés|Csoportos letöltési csoportok – elindítva|
|Csoportkezelés|Csoportos letöltési csoportok – kész|
|Csoportkezelés|Felhasználó bérlői listájának lekérése|
|Csoportkezelés|Helyi fiókok önellenőrző jogcímeinek lekérése|
|Csoportkezelés|Honosított json-erőforrás lekérése|
|Csoportkezelés|A Microsoft.AzureActiveDirectory erőforrás-szolgáltató műveleteinek lekérése|
|Csoportkezelés|Szabályzatok lekérése|
|Csoportkezelés|Szabályzat lekérése|
|Csoportkezelés|Támogatott identitásszolgáltatók listájának lekérése|
|Csoportkezelés|A felhasználói út támogatott identitásszolgáltatói listájának lekérése|
|Csoportkezelés|Bérlő által definiált egyéni identitásszolgáltatók listájának lekérése|
|Csoportkezelés|Bérlő által definiált identitásszolgáltatók listájának lekérése|
|Csoportkezelés|Bérlő által definiált helyi identitásszolgáltató-lista lekérése|
|Csoportkezelés|Felhasználó bérlői információinak lekérése erőforrások létrehozásához|
|Csoportkezelés|A CPIM alapértelmezett támogatott kultúrájának lekérése|
|Csoportkezelés|Rendszergazdai folyamathoz tartozó információk lekérése|
|Csoportkezelés|A bérlőhöz tartozó UserJourneys lista lekérése|
|Csoportkezelés|A CPIM-hez tartozó elérhető, támogatott kultúrák készletének lekérése|
|Csoportkezelés|TrustFramework szabályzat lekérése|
|Csoportkezelés|TrustFramework szabályzat lekérése xml-ként|
|Csoportkezelés|Felhasználói attribútum lekérése|
|Szabályzatkezelés|Felhasználói attribútumok lekérése|
|Szabályzatkezelés|Felhasználói utak listájának lekérése|
|Szabályzatkezelés|GetIEFPolicies|
|Szabályzatkezelés|GetIdentityProviders|
|Szabályzatkezelés|GetTrustFrameworkPolicy|
|Resource|MigrateTenantMetadata|
|Resource|Erőforrások áthelyezése|
|Resource|IdentityProvider javítása|
|Resource|PutTrustFrameworkPolicy|
|Resource|PutTrustFrameworkpolicy|
|Resource|Felhasználói út eltávolítása|
|Resource|Egyéni identitásszolgáltató frissítése|
|Resource|Identitásszolgáltató frissítése|
|Resource|Helyi IDP frissítése|
|Resource|B2C címtárerőforrás frissítése|
|Resource|Szabályzat frissítése|
|Resource|Előfizetés állapotának frissítése|
|Szerepkörkezelés|Felhasználói attribútum frissítése|
|Szerepkörkezelés|Erőforrások áthelyezésének ellenőrzése|
|Szerepkörkezelés|Eszköz hozzáadása|
|Szerepkörkezelés|Eszközkonfiguráció hozzáadása|
|Szerepkörkezelés|Regisztrált felhasználó hozzáadása eszközhöz|
|Szerepkörkezelés|Regisztrált felhasználók hozzáadása eszközhöz|
|Szerepkörkezelés|Eszköz törlése|
|Szerepkörkezelés|Eszköz konfigurációjának törlése|
|Szerepkörkezelés|Az eszköz már nem megfelelő|
|Szerepkörkezelés|Az eszköz már nincs felügyelve|
|Felhasználókezelés|AccessReview_Review|
|Felhasználókezelés|AccessReview_Update|
|Felhasználókezelés|ActivationAborted|
|Felhasználókezelés|ActivationApproved|
|Felhasználókezelés|ActivationCanceled|
|Felhasználókezelés|ActivationRequested|
|Felhasználókezelés|Jogosult tag hozzáadása szerepkörhöz|
|Felhasználókezelés|Tag hozzáadása szerepkörhöz|
|Felhasználókezelés|Szerepkör-hozzárendelés hozzáadása szerepkör-definícióhoz|
|Felhasználókezelés|Szerepkör hozzáadása sablonból|
|Felhasználókezelés|Hatókörön belüli tag hozzáadása a szerepkörhöz|
|Felhasználókezelés|Hozzáadva|
|Felhasználókezelés|Hozzárendelés|
|Felhasználókezelés|Felhasználók tömeges létrehozása – elindítva|
|Felhasználókezelés|Felhasználók tömeges létrehozása – kész|
|Felhasználókezelés|Felhasználók tömeges törlése – elindítva|
|Felhasználókezelés|Felhasználók tömeges törlése – kész|
|Felhasználókezelés|Felhasználók tömeges letöltése – elindítva|
|Felhasználókezelés|Felhasználók tömeges letöltése – kész|
|Felhasználókezelés|Törölt felhasználók tömeges visszaállítása – elindítva|
|Felhasználókezelés|Törölt felhasználók tömeges visszaállítása – kész|
|Felhasználókezelés|Felhasználók tömeges meghívása – elindítva|
|Felhasználókezelés|Felhasználók tömeges meghívása – kész|
|Felhasználókezelés|Regisztrált felhasználó eltávolítása az eszközről|
|Felhasználókezelés|Regisztrált felhasználók eltávolítása az eszközről|
|Felhasználókezelés|Jogosult tag eltávolítása a szerepkörből|
|Felhasználókezelés|Tag eltávolítása a szerepkörből|
|Felhasználókezelés|Szerepkör-hozzárendelés eltávolítása a szerepkör-definícióból|
|Felhasználókezelés|Hatókörön belüli tag eltávolítása a szerepkörből|
|Felhasználókezelés|Eszköz frissítése|
|Felhasználókezelés|Eszköz konfigurációjának frissítése|
|Felhasználókezelés|Szerepkör frissítése|






## <a name="identity-protection"></a>Identitásvédelem

|Naplózási kategória|Tevékenység|
|---|---|
|Címtárkezelés|Jogosultságszint emelése|
|Címtárkezelés|Eltávolítva|
|Címtárkezelés|Szerepkör-beállítások módosításai|
|Egyéb|ScanAlertsNow|
|Egyéb|Regisztráció|
|Egyéb|Jogosultságszint csökkentése|
|Egyéb|UpdateAlertSettings|
|Egyéb|UpdateCurrentState|
|Szabályzatkezelés|Hozzáférési felülvizsgálat befejezve|
|Szabályzatkezelés|Jóváhagyó hozzáadása a kérelem-jóváhagyáshoz|
|Szabályzatkezelés|Felülvizsgáló hozzáadása a hozzáférési felülvizsgálathoz|
|Felhasználókezelés|Hozzáférési felülvizsgálat alkalmazása|
|Felhasználókezelés|Hozzáférési felülvizsgálat létrehozása|


## <a name="invited-users"></a>Meghívott felhasználók

|Naplózási kategória|Tevékenység|
|---|---|
|Egyéb|Kérelem-jóváhagyás létrehozása|
|Egyéb|Hozzáférési felülvizsgálat törlése|
|Felhasználókezelés|Felülvizsgáló eltávolítása a hozzáférési felülvizsgálatból|
|Felhasználókezelés|Felülvizsgálati eredmény alkalmazásának kérése|
|Felhasználókezelés|Felülvizsgálat leállításának kérése|
|Felhasználókezelés|Alkalmazás-hozzárendelés felülvizsgálata|
|Felhasználókezelés|Csoporttagság felülvizsgálata|
|Felhasználókezelés|RBAC-szerepkörtagság felülvizsgálata|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Naplózási kategória|Tevékenység|
|---|---|
|Csoportkezelés|Kérelem-jóváhagyási kérelem felülvizsgálata|
|Csoportkezelés|Hozzáférési felülvizsgálat frissítése|
|Csoportkezelés|Hozzáférési felülvizsgálati e-mail-értesítési beállításainak frissítése|
|Csoportkezelés|Hozzáférési felülvizsgálati ismétlődési szám beállításának frissítése|
|Csoportkezelés|Hozzáférési felülvizsgálat ismétlődési időtartamának frissítése napokban beállítás|
|Felhasználókezelés|Hozzáférési felülvizsgálati ismétlődési típus beállításának frissítése|
|Felhasználókezelés|Hozzáférési felülvizsgálati ismétlődési típus beállításának frissítése|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Naplózási kategória|Tevékenység|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Hozzáadva|
|PIM|AddedOutsidePIM|
|PIM|Hozzárendelés|
|PIM|DismissAlert|
|PIM|Jogosultságszint emelése|
|PIM|ReactivateAlert|
|PIM|Eltávolítva|
|PIM|RemovedOutsidePIM|
|PIM|Felülvizsgálat leállításának kérése|
|PIM|Szerepkör-beállítások módosításai|
|PIM|ScanAlertsNow|
|PIM|Regisztráció|
|PIM|Szüntesse|
|PIM|Jogosultságszint csökkentése|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Önkiszolgáló csoportkezelés

|Naplózási kategória|Tevékenység|
|---|---|
|Csoportkezelés|Új felhasználói jelszó kérése|
|Csoportkezelés|Felhasználó visszaállítása|
|Csoportkezelés|Felhasználói jelszó kényszerített módosításának beállítása|
|Csoportkezelés|Felhasználókezelő beállítása|
|Csoportkezelés|A felhasználók oath token metaadatainak engedélyezettre állítása|
|Csoportkezelés|StsRefreshTokenValidFrom időbélyeg frissítése|
|Csoportkezelés|Külső titkos kódok frissítése|
|Csoportkezelés|Felhasználó frissítése|
|Csoportkezelés|A rendszergazda ideiglenes jelszót hoz létre|


## <a name="self-service-password-management"></a>Önkiszolgáló jelszókezelés

|Naplózási kategória|Tevékenység|
|---|---|
|Címtárkezelés|A rendszergazda kötelezővé teszi a felhasználó számára az új jelszó kérését|
|Címtárkezelés|Külső felhasználó hozzárendelése alkalmazáshoz|
|Felhasználókezelés|E-mail nincs elküldve, a felhasználó leiratkozott|
|Felhasználókezelés|Külső felhasználó meghívása|
|Felhasználókezelés|Külső felhasználó meghívásának beváltása|
|Felhasználókezelés|Bérlő ugrásszerű létrehozása|
|Felhasználókezelés|Felhasználó ugrásszerű létrehozása|
|Felhasználókezelés|Felhasználói jelszó regisztrációja|
|Felhasználókezelés|Új felhasználói jelszó kérése|
|Felhasználókezelés|Új jelszó önkiszolgáló kérésének blokkolása|


## <a name="terms-of-use"></a>Használati feltételek

|Naplózási kategória|Tevékenység|
|---|---|
|Használati feltételek|Használati feltételek elfogadása|
|Használati feltételek|Használati feltételek létrehozása|
|Használati feltételek|Használati feltételek elutasítása|
|Használati feltételek|Törlési engedély|
|Használati feltételek|Használati feltételek törlése|
|Használati feltételek|Használati feltételek szerkesztése|
|Használati feltételek|Használati feltételek lejárta|
|Használati feltételek|A használati feltételek nehezen törölhető|
|Használati feltételek|Használati feltételek közzététele|
|Használati feltételek|Használati feltételek közzétételének visszavonása|


## <a name="next-steps"></a>További lépések

- [Azure ad-jelentések – áttekintés](overview-reports.md).
- [Naplózott jelentés](concept-audit-logs.md). 
- [Programozott hozzáférés az Azure AD-jelentésekhez](concept-reporting-api.md)
