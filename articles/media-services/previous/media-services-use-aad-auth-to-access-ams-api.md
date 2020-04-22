---
title: Az Azure Media Services API elérése az Azure Active Directory-hitelesítéssel | Microsoft dokumentumok
description: Ismerje meg az Azure Active Directory (Azure AD) azure-beli Active Directory (Azure AD) azure Media Services API-hoz való hozzáférés hitelesítéséhez szükséges fogalmakat és lépéseket.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 8e1aeaf105ce371e965b433ac78e2b257f4bc18b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682048"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Az Azure Media Services API hozzáférése Azure AD-hitelesítéssel  

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Az Azure Media Services API egy RESTful API. Segítségével műveleteket hajthat végre a médiaerőforrásokon egy REST API használatával vagy a rendelkezésre álló ügyfél SDK-k használatával. Az Azure Media Services media services-ügyfél SDK-t kínál a Microsoft .NET számára. Ahhoz, hogy jogosult legyen a Media Services-erőforrások és a Media Services API elérésére, először hitelesítenie kell magát. 

A Media Services támogatja az [Azure Active Directory (Azure AD) alapú hitelesítést.](../../active-directory/fundamentals/active-directory-whatis.md) Az Azure Media REST-szolgáltatás megköveteli, hogy a felhasználó vagy alkalmazás, amely a REST API-kérelmek et, vagy a **közreműködői** vagy **a tulajdonos** szerepkör t az erőforrások eléréséhez. További információ: [A szerepköralapú hozzáférés-vezérlés az Azure Portalon](../../role-based-access-control/overview.md)című témakörben található.  

Ez a dokumentum áttekintést nyújt aRról, hogyan érheti el a Media Services API-t REST vagy .NET API-k használatával.

> [!NOTE]
> 2018. június 1-jén a hozzáférés-vezérlési engedély elavult.

## <a name="access-control"></a>Hozzáférés-vezérlés

Az Azure Media REST-kérelem sikerességéhez a hívó felhasználónak rendelkeznie kell egy közreműködői vagy tulajdonosi szerepkörrel a Media Services-fiókhoz, amelyhez hozzá próbál férni.  
Csak a Tulajdonos szerepkörrel rendelkező felhasználó adhat médiaerőforrás-hozzáférést (fióknak) új felhasználókhoz vagy alkalmazásokhoz. A közreműködői szerepkör csak a médiaerőforráshoz férhet hozzá.
A jogosulatlan kérelmek sikertelenek, 401-es állapotkóddal. Ha ezt a hibakódot látja, ellenőrizze, hogy a felhasználó rendelkezik-e a felhasználó Media Services-fiókjához rendelt Közreműködő i vagy tulajdonosi szerepkör.If you see this error code, check whether your user has the Contributor or Owner role as signed for the user's Media Services account. Ezt az Azure Portalon ellenőrizheti. Keresse meg a médiafiókot, majd kattintson a **Hozzáférés-vezérlés** fülre. 

![Hozzáférés-vezérlés lap](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>A hitelesítés típusai 
 
Ha Az Azure AD-hitelesítést az Azure Media Services szolgáltatással használja, két hitelesítési lehetőség közül választhat:

- **Felhasználói hitelesítés**. Hitelesítse azt a személyt, aki az alkalmazást használja a Media Services-erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználóhitelesítő adatait. Egy példa egy felügyeleti konzol alkalmazás által használt jogosult felhasználók kódolási feladatok figyelésére vagy élő streamelés. 
- **Egyszerű szolgáltatáshitelesítés**. Szolgáltatás hitelesítése. A hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyek démonszolgáltatásokat, középső rétegbeli szolgáltatásokat vagy ütemezett feladatokat futtatnak. Ilyenek például a webalkalmazások, a függvényalkalmazások, a logikai alkalmazások, az API és a mikroszolgáltatások.

### <a name="user-authentication"></a>Felhasználóhitelesítés 

A felhasználói hitelesítési módszert használó alkalmazások a natív alkalmazások felügyelete vagy figyelése: mobilalkalmazások, Windows-alkalmazások és konzolalkalmazások. Ez a megoldástípus akkor hasznos, ha emberi interakciót szeretne a szolgáltatással az alábbi esetek egyikében:

- Figyelési irányítópult a kódolási feladatokhoz.
- Az élő közvetítések figyelési irányítópultja.
- Felügyeleti alkalmazás asztali vagy mobilfelhasználók számára a Media Services-fiók erőforrásainak felügyeletéhez.

> [!NOTE]
> Ez a hitelesítési módszer nem használható a fogyasztók felé néző alkalmazásokhoz. 

Egy natív alkalmazás először be kell szereznie egy hozzáférési jogkivonatot az Azure AD-ből, és akkor használja azt, amikor HTTP-kérelmeket a Media Services REST API-t. Adja hozzá a hozzáférési jogkivonatot a kérelem fejlécéhez. 

Az alábbi ábrán egy tipikus interaktív alkalmazáshitelesítési folyamat látható: 

![Natív alkalmazások diagramja](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

Az előző ábrán a számok a kérelmek áramlását időrendi sorrendben jelölik.

> [!NOTE]
> Ha a felhasználói hitelesítési módszert használja, minden alkalmazás ugyanazt az (alapértelmezett) natív alkalmazásügyfél-azonosítót és a natív alkalmazásátirányítási URI-t használja. 

1. A felhasználótól hitelesítő adatokat kér.
2. Azure AD-hozzáférési jogkivonat kérése a következő paraméterekkel:  

   * Azure AD-bérlői végpont.

       A bérlői adatok az Azure Portalon lehívhatók. Helyezze a kurzort a bejelentkezett felhasználó neve fölé a jobb felső sarokban.
   * Media Services erőforrás-URI. 

       Ez az URI megegyezik az azonos Azure-környezetben (például\/https: /rest.media.azure.net) lévő Media Services-fiókok esetében.

   * Media Services (natív) alkalmazásügyfél-azonosító.
   * Media Services (natív) alkalmazás átirányítási URI.
   * A REST Media Services erőforrás-URI-ja.
        
       Az URI a REST API-végpontot https://test03.restv2.westus.media.azure.net/api/)jelöli (például .

     Ezeknek a paramétereknek az értékek beolvassa, [lásd: Az Azure Portal használata az Azure AD hitelesítési beállításainak eléréséhez](media-services-portal-get-started-with-aad.md) a felhasználói hitelesítési beállítás használatával.

3. Az Azure AD-hozzáférési jogkivonat az ügyfélnek kerül elküldésre.
4. Az ügyfél egy kérést küld az Azure Media REST API-t az Azure AD-hozzáférési jogkivonattal.
5. Az ügyfél visszakapja az adatokat a Media Services szolgáltatásból.

Ha tudni szeretné, hogyan kommunikálhat az Azure AD-hitelesítéssel a REST-kérelmekkel a Media Services .NET ügyfél SDK használatával, [olvassa el az Azure AD-hitelesítés használata a Media Services API.NET használatával történő eléréséhez](media-services-dotnet-get-started-with-aad.md)című témakört. 

Ha nem használja a Media Services .NET ügyfél SDK, manuálisan létre kell hoznia egy Azure AD-hozzáférési jogkivonat-kérelmet a 2. További információ: [Az Azure AD-hitelesítési könyvtár használata az Azure AD-jogkivonat beszerzése](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)című témakörben található.

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

A hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyek középszintű szolgáltatásokat és ütemezett feladatokat futtatnak: webalkalmazások, függvényalkalmazások, logikai alkalmazások, API-k és mikroszolgáltatások. Ez a hitelesítési módszer olyan interaktív alkalmazásokhoz is használható, amelyekben szolgáltatásfiókot érdemes használni az erőforrások kezeléséhez.

Ha a szolgáltatás egyszerű hitelesítési módszert használja a fogyasztói forgatókönyvek létrehozásához, a hitelesítés általában a középső rétegben (valamilyen API-n keresztül) kezeli a hitelesítést, és nem közvetlenül egy mobil- vagy asztali alkalmazásban. 

A módszer használatához hozzon létre egy Azure AD-alkalmazást és egyszerű szolgáltatást a saját bérlőjében. Az alkalmazás létrehozása után adjon hozzáférést az alkalmazás közreműködői vagy tulajdonosi szerepkörének a Media Services-fiókhoz. Ezt az Azure Portalon teheti meg az Azure CLI használatával vagy egy PowerShell-parancsfájllal. Egy meglévő Azure AD-alkalmazást is használhat. Az Azure AD-alkalmazást és egyszerű szolgáltatást regisztrálhatja és kezelheti [az Azure Portalon.](media-services-portal-get-started-with-aad.md) Ezt az [Azure CLI](media-services-use-aad-auth-to-access-ams-api.md) vagy a [PowerShell](media-services-powershell-create-and-configure-aad-app.md)használatával is megteheti. 

![Középső rétegbeli alkalmazások](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Az Azure AD-alkalmazás létrehozása után a következő beállításokat kapja meg. A hitelesítéshez ezekre az értékekre van szükség:

- Ügyfél-azonosító 
- Titkos ügyfélkulcs 

Az előző ábrán a számok a kérelmek áramlását időrendi sorrendben jelölik:
    
1. Egy középső rétegű alkalmazás (webes API vagy webalkalmazás) olyan Azure AD-hozzáférési jogkivonatot kér, amely a következő paraméterekkel rendelkezik:  

   * Azure AD-bérlői végpont.

       A bérlői adatok az Azure Portalon lehívhatók. Helyezze a kurzort a bejelentkezett felhasználó neve fölé a jobb felső sarokban.
   * Media Services erőforrás-URI. 

       Ez az URI megegyezik az azonos Azure-környezetben (például https:\//rest.media.azure.net) található Media Services-fiókok esetében.

   * A REST Media Services erőforrás-URI-ja.

       Az URI a REST API-végpontot https://test03.restv2.westus.media.azure.net/api/)jelöli (például .

   * Az Azure AD-alkalmazás értékei: az ügyfélazonosító és az ügyfél titkos.
    
     Ezeknek a paramétereknek az értékek beolvassa, [lásd: Az Azure Portal használata az Azure AD hitelesítési beállítások eléréséhez](media-services-portal-get-started-with-aad.md) a szolgáltatás egyszerű hitelesítési beállítás használatával.

2. Az Azure AD-hozzáférési jogkivonat a középső rétegre kerül elküldésre.
4. A középső réteg az Azure Media REST API-nak küldi a kérelmet az Azure AD-jogkivonattal.
5. A középső réteg visszakapja az adatokat a Media Servicesből.

Ha többet szeretne tudni arról, hogy miként kommunikálhat az Azure AD-hitelesítéssel a REST-kérelmekkel a Media Services .NET ügyfél SDK használatával, olvassa el az [Azure AD-hitelesítés használata az Azure Media Services API-hoz való hozzáféréshez a .NET](media-services-dotnet-get-started-with-aad.md)használatával című témakört. 

Ha nem használja a Media Services .NET ügyfél SDK, manuálisan létre kell hoznia egy Azure AD token kérelmet az 1. További információ: [Az Azure AD-hitelesítési könyvtár használata az Azure AD-jogkivonat beszerzése](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)című témakörben található.

## <a name="troubleshooting"></a>Hibaelhárítás

Kivétel: "A távoli kiszolgáló hibát adott vissza: (401) Jogosulatlan."

Megoldás: A Media Services REST-kérelem sikerességéhez a hívó felhasználónak közreműködői vagy tulajdonosi szerepkörnek kell lennie a hozzá férni próbáló Media Services-fiókban. További információt a [Hozzáférés-vezérlés](media-services-use-aad-auth-to-access-ams-api.md#access-control) című szakaszban talál.

## <a name="resources"></a>További források

Az alábbi cikkek az Azure AD-hitelesítési fogalmak áttekintései: 

- [Az Azure AD által felügyelt hitelesítési forgatókönyvek](../../active-directory/develop/authentication-scenarios.md)
- [Alkalmazás hozzáadása, frissítése vagy eltávolítása az Azure AD-ben](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)
- [Szerepköralapú hozzáférés-vezérlés konfigurálása és kezelése a PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>További lépések

* Az Azure Portal használatával elérheti az [Azure AD-hitelesítést az Azure Media Services API használatához.](media-services-portal-get-started-with-aad.md)
* Azure AD-hitelesítés sel érheti el az [Azure Media Services API-t a .NET használatával.](media-services-dotnet-get-started-with-aad.md)

