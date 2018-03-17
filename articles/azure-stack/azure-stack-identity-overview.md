---
title: "Azure verem identitás áttekintése |} Microsoft Docs"
description: "További tudnivalók az Azure-verem használható identitáskezelő rendszerekkel."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: deebe5d8ff4c35c4507d2daf5c15025a1810d865
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="overview-of-identity-for-azure-stack"></a>Azure verem identitás áttekintése

Az Azure verem Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS), identitás-szolgáltatóként Active Directory által támogatott van szükség. A választott szolgáltató nem egy egyszeri döntés, amikor először telepít Azure verem. A fogalmakat és engedélyezési részletek ebben a cikkben segítségével válasszon az identitás-szolgáltatóktól. 

A választott vagy Azure AD vagy AD FS határozza meg, a Azure verem üzembe helyezésének módja: 
- Amikor telepít egy csatlakoztatott módban, vagy Azure AD vagy AD FS is használhatja. 
- Kapcsolat nélküli módban, az internetkapcsolat nélkül telepítésekor csak Active Directory összevonási szolgáltatások esetén támogatott.

További információ a beállításokat, amelyek Azure verem környezettől függ, tekintse meg a következő cikkeket:
- Az Azure verem központi telepítési csomag: [identitás szempontok](azure-stack-datacenter-integration.md#identity-considerations).
- Az Azure verem rendszerek integrált: [telepítési tervének kidolgozásához Azure verem integrált rendszerek](azure-stack-deployment-decisions.md).

 
## <a name="common-concepts-for-identity"></a>Az azonosító általánosan használt fogalmak
A következő szakaszok tárgyalják általánosan használt fogalmakat ismertető identitás-szolgáltatóktól és azok Azure verem használható.

![Az identitás-szolgáltatóktól terminológiája](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Directory bérlők és a szervezetek
A könyvtár olyan tároló, amely adatait tárolja *felhasználók*, *alkalmazások*, *csoportok*, és *szolgáltatás rendszerbiztonsági tagok*.
 
A directory-bérlő egy *szervezet*, például a Microsoft vagy a saját vállalati. 
- Az Azure AD támogatja több bérlő, és mindegyiket a saját könyvtár, több szervezet tudja támogatni. Ha az Azure AD használ, és több bérlő rendelkezik, meg lehet adni, alkalmazások és a felhasználók egy bérlő hozzáférés más ugyanazt a címtárat a bérlők számára.
- Az AD FS támogatja a csak egy egybérlős, ezért csak egyetlen szervezet. 

### <a name="users-and-groups"></a>Felhasználók és csoportok
Felhasználói fiókok (azonosítók) olyan szabványos fiókok, hitelesítéshez egyéni felhasználók számára egy felhasználói Azonosítót és jelszót használja. Csoportok tartalmazhatják, felhasználók vagy egyéb csoportok. 

Hozzon létre, és hogyan a felhasználók és csoportok kezelése attól függ, az identitás-megoldás használata. 

Azure-készletben, felhasználói fiókok: 
- Az hozott létre a  *username@domain*  formátumban. Bár az AD FS felhasználói fiókok leképezve az Active Directory-példányban, az AD FS nem használható a  *\<tartományi >\<alias >* formátumban. 
- Beállítható a multi-factor authentication használatával. 
- A könyvtárba, amennyiben először regisztrálják, amely a szervezet címtárához korlátozva.
- A helyszíni címtárakban lehet importálni. További információkért lásd: [integrálása a helyszíni címtárakat az Azure Active Directoryval](/azure/active-directory/connect/active-directory-aadconnect). 

Amikor bejelentkezik a szervezet bérlői portál, használhatja a  *https://portal.local.azurestack.external*  URL-CÍMÉT. 

### <a name="guest-users"></a>Vendégfelhasználók
Vendégfelhasználók számára, hogy a címtárban található erőforrások hozzáférés rendelkezik directory bérlőktől lévő felhasználói fiókok. Vendég felhasználók támogatására, használhatja az Azure Active Directory és a több-bérlős támogatásának engedélyezéséhez. Ha támogatása engedélyezve van, felajánlhatja a vendégfelhasználók a directory-bérlőjéhez, amely a következő funkciókat engedélyezi az együttműködés külső szervezetekkel erőforrásaihoz. 

A vendégfelhasználók meghívott, a felhő üzemeltetőinek és a felhasználók használható [Azure AD B2B együttműködés](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). A meghívott felhasználók férhetnek hozzá a dokumentumokhoz, erőforrások és alkalmazások a könyvtárból, és szabályozhatják a saját erőforrások és az adatok megmaradjanak. 

A Vendég felhasználó bejelentkezhet egy másik szervezet directory-bérlő. Ehhez a szervezet könyvtárnév hozzáfűzése a portál URL-CÍMÉT. Például, ha a Contoso szervezet tartozik, és jelentkezzen be a Fabrikam directory szeretne, használja https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>Alkalmazások
Alkalmazások az Azure AD vagy AD FS regisztrálhatja, és majd fogadja el az alkalmazásokat a felhasználók a szervezetben. 

Alkalmazás a következőket tartalmazza:
- **Webes alkalmazás**: például az Azure-portál és az Azure Resource Manager. Webes API-hívások támogatják. 
- **Natív ügyfél**: például az Azure PowerShell, a Visual Studio és az Azure parancssori felület.

Alkalmazások bérlős, két típusát tudja támogatni: 
- **Single-bérlő**: támogatja a felhasználók és szolgáltatások csak az ugyanabban a könyvtárban, ahol az alkalmazás regisztrálva van. 

  > [!NOTE]    
  > Mivel az AD FS támogatja a csak egy könyvtár, létrehoz egy AD FS topológia az alkalmazások van, úgy lett kialakítva, egyetlen bérlői alkalmazások.

- **Több-bérlős**: felhasználók és a könyvtárban, ahol az alkalmazás regisztrálva van-e és a további bérlői könyvtárak szolgáltatások használatát támogatja. A több-bérlős alkalmazásokhoz a bérlő címtár (egy másik Azure AD-bérlő) egy másik is jelentkeznek be az alkalmazást. 

  További információ a több-bérlős: [engedélyezze a több-bérlős](azure-stack-enable-multitenancy.md). 

  A több-bérlős alkalmazások fejlesztésével kapcsolatos további információkért lásd: [több-bérlős alkalmazásokhoz](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Amikor regisztrál egy alkalmazás, hozzon létre két objektum:

- **Alkalmazás objektumának**: az alkalmazás közötti minden bérlők globális ábrázolását. Ez a kapcsolat egy az egyhez típusú szoftverek alkalmazásával, és csak szerepel a könyvtár, amelyben az alkalmazás már regisztrálva van.

- **Szolgáltatás egyszerű objektum**: a könyvtárban, ahol az alkalmazás már regisztrálva van az alkalmazáshoz létrehozott hitelesítő adatokat. Egy egyszerű szolgáltatást minden további bérlő hol használják az alkalmazást a címtárban is létrejön. Ez a kapcsolat egy-a-többhöz a szoftver alkalmazással lehet. 

Alkalmazás és szolgáltatás egyszerű objektumok kapcsolatos további információkért lásd: [alkalmazás és szolgáltatás egyszerű objektumok az Azure Active Directoryban](/azure/active-directory/develop/active-directory-application-objects). 

### <a name="service-principals"></a>Szolgáltatás rendszerbiztonsági tagok 
Egy egyszerű szolgáltatást egy olyan *hitelesítő adatok* adott alkalmazás vagy szolgáltatás, amely Azure verem erőforrásokhoz való hozzáférést. Egy egyszerű szolgáltatás használatát választja el az alkalmazás alkalmazás a felhasználó engedélyeit.

Egy egyszerű szolgáltatást az egyes bérlők, ahol az alkalmazás használata jön létre. A szolgáltatás egyszerű hoz létre egy identitás-bejelentkezés és, hogy a bérlő által védett erőforrások (például a felhasználók) elérésére. 

- A single-bérlő alkalmazásnak csak egy egyszerű, a könyvtárban, ahol az első létrehozása. A szolgáltatás egyszerű jön létre, és hozzájárul az alkalmazás regisztrálása során használt. 
- Több-bérlős webalkalmazás vagy API rendelkezik, amely az egyes bérlők jön létre egy egyszerű ahol a felhasználó a bérlőre járul hozzá az alkalmazás használatát. 

Szolgáltatásnevekről tartozó hitelesítő adatokat vagy kell a kulcsot az Azure-portálon létrehozott tanúsítványt. Tanúsítvány használatát automatizálásra van alkalmas, mert a tanúsítványok nagyobb biztonságot nyújt, mint a kulcsok minősülnek. 


> [!NOTE]    
> Active Directory összevonási szolgáltatások használatakor egy Azure verem csak a rendszergazda szolgáltatásnevekről hozhat létre. Az AD FS szolgáltatás rendszerbiztonsági tagok tanúsítványt igényel, és a kiemelt végpont (EGP) keresztül jönnek létre. További információkért lásd: [alkalmazások elérést biztosíthat az Azure-verem](azure-stack-create-service-principals.md).

Azure verem szolgáltatásnevekről kapcsolatos további tudnivalókért lásd: [szolgáltatásnevekről létrehozása](azure-stack-create-service-principals.md).


### <a name="services"></a>Szolgáltatások
Az identitásszolgáltató együttműködő szolgáltatások Azure verem a identitásszolgáltatóval alkalmazásokként van regisztrálva. Alkalmazások, például a regisztrációs engedélyezi a szolgáltatás számára az azonosítási rendszer a hitelesítést. 

Az összes Azure-szolgáltatások használata [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protokollok és [JSON webes jogkivonatainak](/azure/active-directory/develop/active-directory-token-and-claims) a személyazonosságukat. Mivel Azure AD és az AD FS protokollok következetesen, képes használni [Azure Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) végeznek helyszíni hitelesítést vagy az Azure-ba (a csatlakoztatott forgatókönyvek). Az ADAL is használhatja eszközök, például az Azure PowerShell és az Azure parancssori felület a kereszt-felhőalapú és helyszíni erőforrás-kezelés. 


### <a name="identities-and-your-identity-system"></a>Identitások és az azonosítási rendszer 
Azure verem identitásainak közé tartozik a felhasználói fiókok, csoportok és szolgáltatásnevekről. 

Azure-vermet, számos beépített alkalmazások és szolgáltatások telepítésekor automatikusan regisztrálja az identitásszolgáltató a directory-bérlőben. Egyes szolgáltatások, amelyeket regisztrálni az adminisztrációs használnak. Más szolgáltatásokkal a felhasználók számára érhetők el. Az alapértelmezett regisztrációk adjon alapvető szolgáltatások identitásokat tartalmaz, amelyek léphetnek kapcsolatba egymással, mind az identitásokat tartalmaz, amelyek később fel.

Ha úgy konfigurálja az Azure AD a több-bérlős, egyes alkalmazások propagálódik az új könyvtárakat. 

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
 

### <a name="authentication-by-applications-and-users"></a>Alkalmazások és a felhasználók hitelesítése
  
![Azure verem rétegek közötti identitás](media/azure-stack-identity-overview/identity-layers.png)

Az alkalmazások és a felhasználók az Azure-verem architektúrája négy réteget írja le. Ezek a rétegek közötti interakció különböző hitelesítési használhatja.


|Réteg    |Rétegek közötti hitelesítés  |
|---------|---------|
|Eszközök és ügyfelek, például a felügyeleti portálon     | Eléréséhez, vagy módosítani az Azure-készletben, erőforrást eszközökhöz, és az ügyfelek használják a [JSON Web Token](/azure/active-directory/develop/active-directory-token-and-claims) hívás az Azure Resource Manager. <br>Az Azure Resource Manager ellenőrzi a JSON Web Token és betekintés az *jogcímek* a kibocsátott jogkivonat becsléséhez engedélyezési szinten, hogy a felhasználó vagy szolgáltatás egyszerű tartozik az Azure-készletben. |
|Az Azure Resource Manager és a hozzá tartozó alapvető szolgáltatások     |Az Azure Resource Manager szolgáltatók kommunikációs átvitele felhasználók kommunikál. <br> Használjon átviszi *közvetlen imperatív* hívások vagy *deklaratív* keresztül meghívja [Azure Resource Manager-sablonok](/azure/azure-stack/user/azure-stack-arm-templates.md).|
|Erőforrás-szolgáltatók     |Erőforrás-szolgáltatók számára továbbított hívások biztosított tanúsítványalapú hitelesítéssel. <br>Az Azure Resource Manager és az erőforrás-szolgáltató majd maradnak a kommunikáció egy API-n keresztül. Minden hívás, amely az Azure Resource Manager érkezik az erőforrás-szolgáltató érvényesíti a hívás a tanúsítvánnyal.|
|Infrastruktúra és az üzleti logika     |Erőforrás-szolgáltató üzleti logika és infrastruktúra való kommunikációhoz az általuk választott hitelesítési módot használ. Az alapértelmezett erőforrás-szolgáltató Azure verem rendszerrel szállított Windows-hitelesítés használatára a kommunikáció védelméhez.|

![A hitelesítéshez szükséges adatokat](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>Az Azure Resource Manager való hitelesítéséhez
Az identitásszolgáltató a hitelesítést, és megjelenik egy JSON Web Token, a következő információkat kell rendelkeznie: 
1.  **Az azonosítási rendszer (szolgáltató) URL-címe**: az URL-cím, ahol az identitásszolgáltató érhető el. Például  *https://login.windows.net* . 
2.  **App ID URI az Azure Resource Manager**: az Azure Resource Manager az identitásszolgáltató által regisztrált egyedi azonosítója. Azt is egyedi érték minden egyes Azure verem telepítés.
3.  **Hitelesítő adatok**: A hitelesítő adatok segítségével hitelesíti a identitásszolgáltatóval. 
4.  **URL-CÍMÉT az Azure Resource Manager**: az URL-cím az Azure erőforrás-kezelő szolgáltatás helyét. Például  *https://management.azure.com*  vagy  *https://management.local.azurestack.external* .

Ha egy rendszerbiztonsági tag (egy ügyfél, alkalmazás vagy felhasználó) hajt végre a hitelesítési kérelem elért egy erőforrást, a kérelem tartalmaznia kell:
- Az egyszerű hitelesítő adatokat.
- Az app ID URI, amely a tag hozzá kíván férni az erőforrás. 

A hitelesítő adatok az identitásszolgáltató által ellenőrzését. Az identitásszolgáltató is ellenőrzi, hogy az alkalmazás URI azonosítója a regisztrált alkalmazáshoz, és, hogy a rendszerbiztonsági tag az adott erőforrás jogkivonat beszerzése a megfelelő jogosultságokkal rendelkezik-e. Ha a kérelem érvényes, egy JSON webes jogkivonatot kap. 

A jogkivonat az Azure Resource Manager majd a kérelem fejlécében kell átadni. Az Azure Resource Manager hajtja végre a következő nem meghatározott sorrendben:
- Ellenőrzi a *kibocsátó* annak ellenőrzéséhez, hogy a jogkivonat a megfelelő identitásszolgáltató jogcím (iss). 
- Ellenőrzi a *célközönség* (és) jogcímet annak ellenőrzéséhez, hogy a jogkivonat az Azure Resource Manager ki. 
- Ellenőrzi, hogy a JSON Web Token van a tanúsítvánnyal aláírt OpenID keresztül konfigurált ismert az Azure Resource Manager. 
- Tekintse át a *kiállított* (iat) és *lejárati* (exp) annak ellenőrzéséhez, hogy a jogkivonat aktív jogcímek és az elfogadható. 

Ha minden ellenőrzés be nem fejeződik, Azure Resource Manager használ a *kifogásolt* (oid) és a *csoportok* jogcímeket a résztvevő által elérhető erőforrások listáját. 

![A token exchange protokoll ábrája](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés használata  
A Microsoft Azure-megvalósítást összhangban a szerepköralapú hozzáférés-vezérlés (RBAC) Azure-készletben. Kezelheti az erőforrásokhoz való hozzáférést, ha a megfelelő RBAC szerepkör hozzárendelése a felhasználók, csoportok és alkalmazások. Az RBAC használata Azure verem kapcsolatos információkért lásd: a következő cikkeket:
- [Ismerkedés a szerepköralapú hozzáférés-vezérlés az Azure portálon](/azure/active-directory/role-based-access-control-what-is).
- [Az Azure-előfizetés erőforrásokhoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérlés segítségével](/azure/active-directory/role-based-access-control-configure).
- [Hozzon létre egyéni szerepkörök átruházásához hozzáférés-vezérlési](/azure/active-directory/role-based-access-control-custom-roles).
- [Szerepköralapú hozzáférés-vezérlés kezelése](azure-stack-manage-permissions.md) Azure navigációs veremben.


### <a name="authenticate-with-azure-powershell"></a>A hitelesítést az Azure PowerShell  
Azure PowerShell használatával való hitelesítéshez szükséges Azure verem részleteit található [konfigurálása az Azure-verem felhasználói PowerShell környezet](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>A hitelesítést az Azure parancssori felület
Az Azure-verem való hitelesítéshez szükséges Azure PowerShell használatával kapcsolatos információkért lásd: [telepítése és konfigurálása az Azure CLI Azure verem való használatra](/azure/azure-stack/user/azure-stack-connect-cli.md).

## <a name="next-steps"></a>További lépések
- [Identitás-architektúra](azure-stack-identity-architecture.md)   
- [Adatközpont-integráció - identitás](azure-stack-integrate-identity.md)




