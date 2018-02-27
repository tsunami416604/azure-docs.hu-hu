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
ms.openlocfilehash: 609ea3300806f3cfed4a3285a096f59be491c684
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="overview-of-identity-for-azure-stack"></a>Az Azure verem identitás áttekintése

Azure verem igényel az Azure AD vagy az Active Directory külső szolgáltatások (AD FS) biztonsági által az Active Directory (AD) identitás-szolgáltatóként. A következő fogalmakat és engedélyezési részletek elősegíti, identitás-szolgáltatóktól közötti választás. A szolgáltató választott nem Azure verem első telepítésekor ügyeljen egyszeri döntést hoznak.  

A kiválasztott közötti Azure AD és az AD FS előfordulhat, hogy korlátozza a Azure verem üzembe helyezésének módja: 
- Egy csatlakoztatott módú telepítésekor használhatja az Azure AD vagy AD FS. 
- Az internetkapcsolat nélkül kapcsolat nélküli módban telepíti, csak az AD FS használata támogatott.

Ezeket a döntéseket kapcsolatos további információkért tekintse meg a következő cikkekben Azure verem környezettől függően:
- Az Azure verem Deployment Kit, lásd: [identitás szempontok](azure-stack-datacenter-integration.md#identity-considerations).
- Azure integrált verem rendszerek esetén lásd: [telepítési tervének kidolgozásához Azure verem integrált rendszerek](azure-stack-deployment-decisions.md).

 
## <a name="common-concepts-for-identity"></a>Az azonosító általánosan használt fogalmak
Az alábbi szakaszok ismertetik a általánosan használt fogalmakat ismertető identitás-szolgáltatóktól és azok Azure verem használható.
![Az identitás-szolgáltatóktól terminológiája](media/azure-stack-identity-overview/terminology.png)


### <a name="directories-tenants-and-organizations"></a>Könyvtárak bérlők és a szervezetek
A könyvtár olyan tároló, amely adatait tárolja *felhasználók*, *alkalmazások*, *csoportok*, és *szolgáltatás rendszerbiztonsági tagok*.
 
A directory-bérlő egy *szervezet*, például a Microsoft vagy a saját vállalati. 
- Az Azure AD támogatja több bérlő, és több a saját szervezet támogatja. Ha az Azure AD használ, és több bérlő rendelkezik, meg lehet adni, alkalmazások és a felhasználók egy bérlő hozzáférés más ugyanazt a címtárat a bérlők számára.
- Az AD FS támogatja a csak egy egybérlős, és ezért egyetlen szervezet. 

### <a name="users-and-groups"></a>Felhasználók és csoportok
Felhasználói fiókok (azonosítók) szabványos fiók, tudják hitelesíteni magukat egy felhasználói Azonosítót és jelszót használó egyéni felhasználók számára is. Csoportok tartalmazhatják, felhasználók vagy egyéb csoportok. 

Hozzon létre, és hogyan a felhasználók és csoportok kezelése attól függ, az identitás-megoldás használata. 

Azure-készletben, felhasználói fiókok: 
- Az hozott létre a  *username@domain*  formátumban. Bár az AD FS felhasználói fiókok leképezve az Active Directory, AD FS nem támogatja a  _&lt;tartományi >\<alias >_ formátumban. 
- Beállítható a multi-factor authentication használatával. 
- A könyvtárat, ha azokat először regisztrálnia, ami a szervezetek címtár korlátozódnak.
- A helyszíni címtárakban lehet importálni. További információkért lásd: [integrálása a helyszíni címtárakat az Azure Active Directoryval](/azure/active-directory/connect/active-directory-aadconnect) az Azure-dokumentációban.  

Amikor bejelentkezik a szervezetek bérlői portál, https://portal.local.azurestack.external használhatja. 

### <a name="guest-users"></a>Vendégfelhasználók
Vendégfelhasználók számára, hogy a címtárban található erőforrások hozzáférés rendelkezik directory bérlőktől lévő felhasználói fiókok. A Vendég felhasználói támogatásához, használhatja az Azure Active Directory és támogatja a több-bérlős engedélyezni kell. Támogatott, ha a directory-bérlőben, amely lehetővé teszi, hogy a külső szervezetek együttműködve felajánlhatja a Vendég felhasználó hozzáférését az erőforrásokhoz. 

A vendégfelhasználók meghívott, a felhő üzemeltetőinek és a felhasználók használható [Azure AD B2B együttműködés](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Meghívott felhasználók get dokumentumok, erőforrásokhoz és alkalmazásokhoz való hozzáférést a címtárban, amíg szabályozhatják a saját erőforrások és az adatok megmaradjanak. 

Vendégfelhasználóként egy jelentkezhet be egy másik szervezet directory-bérlő tartozik. Ennek érdekében, hogy a szervezetek könyvtárnév kell hozzáfűzése a portál URL-CÍMÉT.  Például ha contoso.com tartozik, de a Fabrikam directory bejelentkezni, https://portal.local.azurestack.external/fabrikam.onmicrosoft.com használja.

### <a name="applications"></a>Alkalmazások
Alkalmazások az Azure AD vagy AD FS regisztrálhatja, és ajánlani őket a felhasználók számára a szervezetében. 

Alkalmazás a következőket tartalmazza:
- **Webes alkalmazás** – például az Azure-portál és az Azure Resource Manager.  Webes API-hívások támogatják. 
- **Natív ügyfél** – például az Azure PowerShell, a Visual Studio és az Azure parancssori felület (CLI).

Alkalmazások bérlős, két típusát tudja támogatni: 
- **Single-bérlő** alkalmazások támogatják a felhasználók és szolgáltatások csak az ugyanabban a könyvtárban, ahol az alkalmazás regisztrálva van. 

  > [!NOTE]    
  > Mivel az AD FS támogatja a csak egy könyvtár, létrehoz egy AD FS topológia az alkalmazások kialakításuk szerint egy bérlői alkalmazások vannak.
- **Több-bérlős** felhasználók és a szolgáltatások mindkét a könyvtárban, ahol az alkalmazás regisztrálva van-e, és további bérlői címtárakban alkalmazások támogatási használja.  A több-bérlős alkalmazásokhoz a bérlő címtár (egy másik Azure AD-bérlő) egy másik is jelentkeznek be az alkalmazást.     

  További információ a több-bérlős: [engedélyezze a több-bérlős](azure-stack-enable-multitenancy.md).  

  A több-bérlős alkalmazások fejlesztésével kapcsolatos további információkért lásd: [több-bérlős alkalmazásokhoz](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Ha egy alkalmazás regisztrálása két objektumok jönnek létre:
- **Alkalmazás objektumának** – az application objektum nem az alkalmazás globális ábrázolását egyetlen bérlő számára. Ez a kapcsolat egy az egyhez típusú, az alkalmazás, és csak a könyvtárat, amelyben az alkalmazás első regiszterekben szerepel.

 
   
- **Szolgáltatás egyszerű objektum** – A szolgáltatás egyszerű a könyvtárban, ahol az alkalmazás már regisztrálva van az alkalmazáshoz létrehozott hitelesítő adatokat. Egy egyszerű szolgáltatást minden további bérlő hol használják az alkalmazást a címtárban is létrejön. Ez a kapcsolat lehet egy egy-a-többhöz a szoftver alkalmazással.   

Alkalmazás és szolgáltatás egyszerű objektumok kapcsolatos további információkért lásd: [alkalmazás és szolgáltatás egyszerű objektumok az Azure Active Directory (Azure AD)](/azure/active-directory/develop/active-directory-application-objects). 

### <a name="service-principals"></a>Szolgáltatás rendszerbiztonsági tagok 
Egy egyszerű szolgáltatást egy olyan *hitelesítő adatok* adott alkalmazás vagy szolgáltatás, amely Azure verem erőforrásokhoz való hozzáférést. Egy egyszerű szolgáltatás használata elkülöníti a felhasználó az alkalmazást használó alkalmazás engedélyeit.

Egy egyszerű szolgáltatást az egyes bérlők, ahol az alkalmazás használata jön létre. A szolgáltatás egyszerű hoz létre egy identitás-bejelentkezés és, hogy a bérlő által védett erőforrások (például a felhasználók) elérésére.   

- A single-bérlő alkalmazásnak csak egy egyszerű, a könyvtárban, ahol az első létrehozása.  A szolgáltatás egyszerű jön létre, és hozzájárul az alkalmazás regisztrálása során. 
- Egy több-bérlős webalkalmazás vagy API-val rendelkezik egy egyszerű szolgáltatás létrehozása az egyes bérlők ahol a felhasználó a bérlőre járul hozzá az alkalmazás használata.  

Hitelesítő szolgáltatás rendszerbiztonsági tagoknak lehet (hozza létre az Azure portálon keresztül) kulcsot vagy tanúsítvány.  Egy tanúsítvány használatát automatizálásra van alkalmas, mert a tanúsítványok minősülnek biztonságosabb, mint a kulcsok használatát. 


> [!NOTE]    
> Active Directory összevonási szolgáltatások használatakor egy Azure verem csak a rendszergazda szolgáltatásnevekről hozhat létre. Az AD FS szolgáltatás rendszerbiztonsági tagok tanúsítványt igényel, és a kiemelt végpont (EGP) keresztül jönnek létre. További információkért tekintse meg, [alkalmazások elérést biztosíthat az Azure-verem](azure-stack-create-service-principals.md).

Azure verem szolgáltatásnevekről kapcsolatos további tudnivalókért lásd: [szolgáltatásnevekről létrehozása](azure-stack-create-service-principals.md).


### <a name="services"></a>Szolgáltatások
Az identitásszolgáltató együttműködő szolgáltatások Azure verem a identitásszolgáltatóval alkalmazásokként van regisztrálva. Alkalmazások, például a regisztrációs engedélyezi a szolgáltatás számára az azonosítási rendszer a hitelesítést. 

Az összes Azure-szolgáltatások használata [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protokollok és [JSON webes jogkivonatainak](/azure/active-directory/develop/active-directory-token-and-claims) (JWT) a személyazonosságukat. Azure ad protokollok konzisztens használata miatt és Active Directory összevonási szolgáltatások, Azure használható [Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) végeznek helyszíni hitelesítést vagy az Azure-ba (a csatlakoztatott forgatókönyvek). Adal-t is lehetővé teszi, hogy például az Azure PowerShell és a parancssori felület használhat határokon felhőalapú és helyszíni erőforrás-kezelés. 


### <a name="identities-and-your-identity-system"></a>Identitások és az azonosítási rendszer 
Azure verem identitásainak közé tartozik a felhasználói fiókok, csoportok és szolgáltatásnevekről. Azure-vermet, számos beépített alkalmazások és szolgáltatások telepítésekor automatikusan regisztrálja az identitásszolgáltató a directory-bérlőben. Egyes szolgáltatások, amelyeket regisztrálni az adminisztrációs használnak. Más szolgáltatásokkal a felhasználók számára érhetők el. Az alapértelmezett regisztrációk adjon alapvető szolgáltatások identitásokat tartalmaz, amelyek kommunikálhatnak egymással, és az identitások hozzáadni.
Ha úgy konfigurálja az Azure AD a több-bérlős, egyes alkalmazások propagálódik az új könyvtárakat.  

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
 

### <a name="authentication-by-applications-and-users"></a>Alkalmazások és a felhasználók hitelesítése
  
![Azure verem rétegek közötti identitás](media/azure-stack-identity-overview/identity-layers.png)

Az alkalmazások és a felhasználók az Azure-verem architektúrája négy réteget írja le. Ezek a rétegek közötti interakció különböző hitelesítési használhatja.


|Réteg    |Rétegek közötti hitelesítés  |
|---------|---------|
|Eszközök és ügyfelek, például a felügyeleti portálon     | Eléréséhez, vagy módosítani az Azure-készletben, erőforrást eszközökhöz, és az ügyfelek használják a [JSON webes jogkivonatainak](/azure/active-directory/develop/active-directory-token-and-claims) (JWT) az Azure Resource Manager hívást.  <br><br> Az Azure Resource Manager ellenőrzi a jwt-t, és betekintés az *jogcímek* a kibocsátott jogkivonat becsléséhez engedélyezési szinten, hogy a felhasználó vagy szolgáltatás egyszerű tartozik az Azure-készletben.        |
|Az Azure Resource Manager és a hozzá tartozó alapvető szolgáltatások     |Az Azure Resource Manager szolgáltatók kommunikációs átvitele felhasználók kommunikál. <br><br> Használjon átviszi *közvetlen imperatív* hívások vagy *deklaratív* keresztül meghívja [Azure Resource Manager-sablonok](/azure/azure-stack/user/azure-stack-arm-templates.md).|
|Erőforrás-szolgáltatók     |Az erőforrás-szolgáltatók átadott hívások biztosított tanúsítványalapú hitelesítéssel. <br><br>Az Azure Resource Manager és az erőforrás-szolgáltató majd maradnak a kommunikáció egy API-n keresztül. Minden hívás érkezett az Azure Resource Manager az erőforrás-szolgáltató érvényesíti a hívás a tanúsítvánnyal.|
|Infrastruktúra- és üzleti logika     |Erőforrás-szolgáltató üzleti logika és infrastruktúra való kommunikációhoz az általuk választott hitelesítési módot használ. Az alapértelmezett erőforrás-szolgáltató, amely küldje el az Azure veremnek megfelelő Windows-hitelesítés használatára a kommunikáció védelméhez.|

![A hitelesítéshez szükséges adatokat](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>Az Azure Resource Manager való hitelesítéséhez
Az identitásszolgáltató a hitelesítést, és megjelenik a jwt-t, a következő információkat kell rendelkeznie: 
1.  **Az azonosítási rendszer (szolgáltató) URL-címe** – az URL-cím, ahol az identitásszolgáltató érhető el. Például  *&lt;https://login.windows.net>*. 
2.  **App ID URI az Azure Resource Manager** – az az Azure Resource Manager az identitásszolgáltató-ben regisztrált és egyedi, minden Azure verem telepítés egyedi azonosítóját.
3.  **Hitelesítő adatok** – Ez a hitelesítő adatok segítségével hitelesíti a identitásszolgáltatóval.  
4.  **URL-CÍMÉT az Azure Resource Manager** – az URL-cím az Azure erőforrás-kezelő szolgáltatás helyét. Például  *&lt;https://management.azure.com>* vagy  *&lt;https://management.local.azurestack.external>*.

Ha egy rendszerbiztonsági tag (egy ügyfél, alkalmazás vagy felhasználó) hajt végre a hitelesítési kérelem elért egy erőforrást, a kérésre tartalmaznia kell:
- Az egyszerű hitelesítő adatokat.
- Az elérni kívánt erőforrást App ID URI.  

A hitelesítő adatok az identitásszolgáltató által ellenőrzését. Az identitásszolgáltató is ellenőrzi, hogy a App ID URI egy regisztrált alkalmazáshoz, és, hogy a rendszerbiztonsági tag jogosult a helyes-e jogkivonat beszerzése az adott erőforráshoz. Ha a kérelem érvényes, egy JSON webes jogkivonatot kap. 

A jogkivonat az Azure erőforrás-kezelő majd a kérelem fejlécében kell átadni. Az Azure erőforrás-kezelő a következő ellenőrzések nem meghatározott sorrendben hajtja végre:
- Ellenőrizze a *kibocsátó* annak ellenőrzéséhez, hogy a jogkivonat a megfelelő identitásszolgáltató jogcím (iss). 
- Ellenőrizze a *célközönség* (és) jogcímet annak ellenőrzéséhez, hogy a jogkivonat az Azure erőforrás-kezelő ki. 
- Ellenőrizze, hogy a JWT OpenID keresztül konfigurált, és az Azure Resource Manager ismert, hogy a tanúsítvánnyal aláírt. 
- Tekintse át a *kiállított* (iat) és *lejárati* (exp) annak ellenőrzéséhez, hogy a jogkivonat aktív jogcímek és az elfogadható. 

Ha minden ellenőrzés be nem fejeződik, Azure Resource Manager használ a *kifogásolt* (oid) és a *csoportok* jogcímeket a résztvevő által elérhető erőforrások listáját. 

![A token exchange protokoll ábrája](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés használata  
A Microsoft Azure-megvalósítást összhangban a szerepköralapú hozzáférés-vezérlés (RBAC) Azure-készletben.  Kezelheti az erőforrásokhoz való hozzáférést, ha a megfelelő RBAC szerepkör hozzárendelése a felhasználók, csoportok és alkalmazások. Az RBAC használata Azure verem kapcsolatos információkért tekintse át a következő cikkeket:
- [Ismerkedés a szerepköralapú hozzáférés-vezérlés az Azure portálon](/azure/active-directory/role-based-access-control-what-is).
- [Az Azure-előfizetés erőforrásokhoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérlés segítségével](/azure/active-directory/role-based-access-control-configure).
- [Hozzon létre egyéni szerepkörök átruházásához hozzáférés-vezérlési](/azure/active-directory/role-based-access-control-custom-roles).
- [Szerepköralapú hozzáférés-vezérlés kezelése](azure-stack-manage-permissions.md) Azure navigációs veremben.


### <a name="authenticate-with-azure-powershell"></a>A hitelesítést az Azure PowerShell  
Azure PowerShell használatával való hitelesítéshez szükséges Azure verem részleteit található [konfigurálása az Azure-verem felhasználói PowerShell környezet](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>A hitelesítést az Azure parancssori felület
Azure PowerShell használatával való hitelesítéshez szükséges Azure verem részleteit található [telepítése és konfigurálása a CLI Azure verem való használatra](/azure/azure-stack/user/azure-stack-connect-cli.md).

## <a name="next-steps"></a>További lépések
- [Identitás-architektúra](azure-stack-identity-architecture.md)   
- [Adatközpont-integráció - identitás](azure-stack-integrate-identity.md)




