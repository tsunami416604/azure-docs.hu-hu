---
title: Az Azure Media Services API eléréséhez az Azure Active Directory-hitelesítéssel |} A Microsoft Docs
description: Ismerje meg a fogalmakat és lépéseket kell tennie, amellyel hitelesíti a hozzáférést az Azure Active Directory (Azure AD) használatával az Azure Media Services API.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: fa13ffe450b60bd8c896636911268a98b49bbc0f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104131"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Hozzáférés az Azure Media Services API Azure AD-hitelesítés  
 
Az Azure Media Services API egy olyan RESTful API. Használhatja az adathordozó-erőforrások műveletek végrehajtásához, egy REST API-val vagy a rendelkezésre álló ügyfél SDK-k használatával. Az Azure Media Services a Microsoft .NET-keretrendszerhez készült Media Services ügyféloldali SDK kínál. Azt, hogy a Media Services-erőforrások és a Media Services API eléréséhez, akkor először hitelesíteni kell. 

A Media Services támogatja a [Azure Active Directory (Azure AD)-alapú hitelesítés](../../active-directory/fundamentals/active-directory-whatis.md). Az Azure Media REST-szolgáltatást igényli, hogy a felhasználó vagy alkalmazás, amely lehetővé teszi a REST API-kérelmek van az egyik a **közreműködői** vagy **tulajdonosa** szerepkör az erőforrások eléréséhez. További információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure Portalon – első lépések](../../role-based-access-control/overview.md).  

> [!IMPORTANT]
> Jelenleg a Media Services az Azure Access Control service hitelesítési modellt támogatja. Azonban a hozzáférés-vezérlés engedélyezési érvénytelenné válnak 2018. június 1. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

Ez a dokumentum áttekintést a Media Services API elérése REST vagy a .NET API-k használatával.

## <a name="access-control"></a>Hozzáférés-vezérlés

Az Azure Media REST kérelem sikeres a hívó felhasználónak rendelkeznie kell egy közreműködői vagy tulajdonosi szerepkört, a Media Services-fiók elérésére tett kísérlet.  
Csak a tulajdonosi szerepkörrel rendelkező felhasználók is media erőforrás (fiók) hozzáférést biztosít az új felhasználók vagy alkalmazások. A közreműködői szerepkör hozzáférhet a média-erőforrás.
Jogosulatlan kérelmek 401-es állapotkód: sikertelen. Ha hibaüzenet jelenik meg, ellenőrizze, hogy a felhasználó rendelkezik-e a közreműködői vagy tulajdonosi szerepkört, és a felhasználó a Media Services-fiókjához hozzárendelt. Az Azure Portalon ellenőrizheti. Keresse meg az adathordozó-fiókot, és kattintson a **hozzáférés-vezérlés** fülre. 

![Hozzáférés-vezérlési lap](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Hitelesítési típusok 
 
Az Azure AD-hitelesítés használatakor az Azure Media Services két hitelesítési lehetősége van:

- **Felhasználói hitelesítés**. A személy, aki használja az alkalmazást az való kommunikációhoz a Media Services-erőforrások hitelesítéséhez. Az interaktív alkalmazást először kell kérni a felhasználót, a felhasználói hitelesítő adatokat. Ilyen például, a kódolási feladatok figyelésére, vagy az élő adások online közvetítése jogosult felhasználók által használt felügyeleti konzolalkalmazást. 
- **Egyszerű szolgáltatásnév hitelesítése**. Egy szolgáltatás hitelesítéséhez. Ezt a hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyeket démonszolgáltatásokat, a középső rétegű services vagy az ütemezett feladatok futtatásához. Példák a web apps, a függvényalkalmazások, a logic apps, API és mikroszolgáltatás-alapú.

### <a name="user-authentication"></a>Felhasználóhitelesítés 

Az alkalmazásokat, amelyek a hitelesítési módszert kell használnia a következők felügyeleti vagy natív alkalmazás megfigyelését: a mobile apps, a Windows-alkalmazások és a konzolalkalmazást. Az ilyen típusú megoldás akkor hasznos, ha azt szeretné, emberi beavatkozást igényel a szolgáltatással az alábbi esetek egyikében:

- Figyelési irányítópult a kódolási feladatokhoz.
- Az élő adatfolyamok figyelési irányítópult.
- Felügyeleti alkalmazás, asztali vagy mobil felhasználók számára a Media Services-fiók-erőforrásokat.

> [!NOTE]
> Ez a hitelesítési módszer nem használható felhasználók felé néző alkalmazások esetén. 

Egy natív alkalmazást kell először az Azure AD-ből hozzáférési jogkivonat beszerzése, és használja azt, hogy a HTTP-kérelmekre a Media Services REST API. Adja hozzá a hozzáférési jogkivonat a kérelem fejlécében. 

Az alábbi ábrán egy tipikus interaktív alkalmazás hitelesítési folyamatot: 

![A natív alkalmazások diagramja](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

A fenti ábrán a számok jelölik a folyamatot a kérelmek időrendi sorrendben.

> [!NOTE]
> Amikor a felhasználó a hitelesítési módszert használja, az összes alkalmazás ugyanazon (alapértelmezett) natív alkalmazás ügyfél-azonosító és a natív alkalmazás átirányítási URI-t megosztott. 

1. A felhasználótól a hitelesítő adatokat.
2. A kérelem egy Azure AD hozzáférési jogkivonatot a következő paraméterekkel:  

   * Az Azure AD-bérlő végpont.

       A bérlői kapcsolatos információkat az Azure Portalról kérhető. Vigye a kurzort az egérmutatót a bejelentkezett felhasználó felső sarokban.
   * Media Services-erőforrás URI-t. 

       Ez az URI megegyezik a Media Services-fiókok, amelyek ugyanabban az Azure környezetben (például https://rest.media.azure.net).

   * A Media Services (natív) alkalmazás ügyfél-azonosító.
   * A Media Services (natív) alkalmazás átirányítási URI-t.
   * Erőforrás-URI REST Media Services.
        
       Az URI-t jelöli a REST API-végpont (például https://test03.restv2.westus.media.azure.net/api/).

     Használatával lekérjük az értékeket a paraméterekhez, lásd: [az Azure portal használata az Azure AD-hitelesítési beállítások eléréséhez](media-services-portal-get-started-with-aad.md) a felhasználói hitelesítés lehetőséget.

3. Az Azure AD hozzáférési jogkivonatot az ügyfélnek zajlik.
4. Az ügyfél kérést küld az Azure Media REST API-t az Azure AD hozzáférési jogkivonatot.
5. Az ügyfél megkapja az adatokat a Media Services szolgáltatásból.

REST-kérelmeket a Media Services .NET client SDK használatával kommunikálnak az Azure AD-hitelesítés használatával kapcsolatos információkért lásd: [a .NET-keretrendszerrel történő Media Services API eléréséhez az Azure AD-hitelesítés](media-services-dotnet-get-started-with-aad.md). 

Ha a Media Services .NET client SDK nem használ, manuálisan kell létrehoznia egy Azure AD hozzáférési jogkivonat kérése a 2. lépésben ismertetett paraméterekkel. További információkért lásd: [jogkivonat beszerzése az Azure AD használata az Azure AD Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Ezt a hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyeket a középső rétegbeli szolgáltatásokat és ütemezett feladatok futtatása: web apps, a függvényalkalmazások, a logic apps, API-k és mikroszolgáltatások. Ez a hitelesítési módszer is interaktív alkalmazásokhoz, ahol előfordulhat, hogy használni kívánt szolgáltatásfiók-erőforrások kezeléséhez.

Ha a szolgáltatás egyszerű hitelesítési módszer használatával hozhat létre a fogyasztói forgatókönyveket, hitelesítési általában kezeli a középső réteg (API-n keresztül néhány) és nem közvetlenül a mobil- vagy asztali alkalmazás. 

Ezt a módszert használja, hozzon létre egy Azure AD-alkalmazás és szolgáltatás egyszerű, a saját bérlőjében. Miután létrehozta az alkalmazást, az alkalmazás közreműködő vagy tulajdonos szerepkör hozzáférést biztosíthat a Media Services-fiók. Ehhez az Azure Portalon az Azure CLI-vel, vagy egy PowerShell-parancsprogrammal. Meglévő Azure AD-alkalmazást is használhatja. Regisztráljon, és kezelheti az Azure AD-alkalmazás és egyszerű szolgáltatás [az Azure Portalon](media-services-portal-get-started-with-aad.md). Azt is teheti használatával [Azure CLI-vel](media-services-use-aad-auth-to-access-ams-api.md) vagy [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Középső rétegbeli alkalmazásokhoz](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Miután létrehozta az Azure AD-alkalmazást, kap értékeket az alábbi beállításokat. Ezekre az értékekre szüksége a hitelesítéshez:

- Ügyfél-azonosító 
- Titkos ügyfélkulcs 

A számok az előző ábrán mutatják be a folyamatot a kérelmek időrendi sorrendben:
    
1. A középső rétegbeli alkalmazás (webes API-t vagy webes alkalmazás), amely a következő paraméterekkel rendelkezik az Azure AD hozzáférési jogkivonatot kér:  

   * Az Azure AD-bérlő végpont.

       A bérlői kapcsolatos információkat az Azure Portalról kérhető. Vigye a kurzort az egérmutatót a bejelentkezett felhasználó felső sarokban.
   * Media Services-erőforrás URI-t. 

       Ez az URI megegyezik a Media Services-fiókok, amelyek ugyanabban az Azure-környezetben találhatók (például https://rest.media.azure.net).

   * Erőforrás-URI REST Media Services.

       Az URI-t jelöli a REST API-végpont (például https://test03.restv2.westus.media.azure.net/api/).

   * Az Azure AD-alkalmazás értékeire: az ügyfél-Azonosítóját és ügyfélkulcsát.
    
     Használatával lekérjük az értékeket a paraméterekhez, lásd: [az Azure portal használata az Azure AD-hitelesítési beállítások eléréséhez](media-services-portal-get-started-with-aad.md) a szolgáltatás egyszerű hitelesítési lehetőség használatával.

2. Az Azure AD hozzáférési jogkivonatot a középső réteg küld.
4. A középső réteg kérést küld az Azure Media REST API az Azure AD-jogkivonattal.
5. A középső réteg vissza az adatok lekérése a Media Services.

REST-kérelmeket a Media Services .NET client SDK használatával kommunikálnak az Azure AD-hitelesítés használatával kapcsolatos további információkért lásd: [el az Azure Media Services API .NET-keretrendszerrel történő hitelesítés használata az Azure AD](media-services-dotnet-get-started-with-aad.md). 

Ha a Media Services .NET client SDK nem használ, manuálisan kell létrehoznia egy Azure AD-jogkivonat kérése az 1. lépésben ismertetett paraméterekkel. További információkért lásd: [jogkivonat beszerzése az Azure AD használata az Azure AD Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Hibaelhárítás

Kivétel: "A távoli kiszolgáló hibát adott vissza: (401) nem engedélyezett."

Megoldás: A Media Services REST kérelem sikeres a hívó felhasználó próbál hozzáférni a Media Services-fiók közreműködői vagy tulajdonosi szerepkörhöz kell lennie. További információkért lásd: a [hozzáférés-vezérlés](media-services-use-aad-auth-to-access-ams-api.md#access-control) szakaszban.

## <a name="resources"></a>További források

Az alábbi cikkeket is az Azure AD-hitelesítés – fogalmak áttekintése: 

- [Hitelesítési forgatókönyvek tárgyalt Azure ad-ben](../../active-directory/develop/authentication-scenarios.md)
- [Hozzáadása, módosítása és alkalmazás eltávolítása az Azure ad-ben](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)
- [Konfigurálja és szerepköralapú hozzáférés-vezérlés kezelése a PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>További lépések

* Az Azure Portallal való [felhasználhatják őket az Azure Media Services API Azure AD-hozzáférésének hitelesítési](media-services-portal-get-started-with-aad.md).
* A hitelesítés használata az Azure AD [eléréséhez az Azure Media Services API .NET-tel](media-services-dotnet-get-started-with-aad.md).

