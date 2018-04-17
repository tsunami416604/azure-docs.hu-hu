---
title: Azure Media Services API elérni az Azure Active Directory-hitelesítéssel |} Microsoft Docs
description: További tudnivalók a fogalmakat és hogyan használja az Azure Active Directory (Azure AD) való hozzáférés hitelesítéséhez az Azure Media Services API.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 57f2680d6b3f06a88a13a09018e7d72afcb710a6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Hozzáférés az Azure Media Services API-t az Azure AD-alapú hitelesítés
 
Az Azure Media Services API RESTful API. Használhatja a média-erőforrások műveletek végrehajtásához, a REST API használatával, vagy elérhető ügyfél SDK-k használatával. Az Azure Media Services egy Media Services-ügyfél SDK kínál a Microsoft .NET-hez. Azt, hogy a Media Services-erőforrások és a Media Services API eléréséhez, akkor először hitelesíteni kell. 

Media Services szolgáltatásban [Azure Active Directory (Azure AD)-alapú hitelesítés](../active-directory/active-directory-whatis.md). Az Azure Media többi szolgáltatáshoz az szükséges, hogy a felhasználó vagy alkalmazás, amely lehetővé teszi a REST API-kérelmek teljesül a **közreműködő** vagy **tulajdonos** szerepkör az erőforrásokhoz való hozzáféréskor. További információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure-portálon az első lépései](../role-based-access-control/overview.md).  

> [!IMPORTANT]
> A Media Services jelenleg az Azure hozzáférés-vezérlés szolgáltatásmodell-hitelesítés. Azonban a 2018. június 1 elavulttá válik hozzáférés-vezérlés engedélyt. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

Ez a dokumentum áttekintést hogyan érhetők el a Media Services API REST vagy a .NET API-k használatával.

## <a name="access-control"></a>Hozzáférés-vezérlés

Az Azure Media REST kérelem sikeres a hívó felhasználónak rendelkeznie kell a Media Services-fiók elérésére tett kísérlet közreműködő vagy a tulajdonos szerepkört.  
Csak a tulajdonosi szerepkört rendelkező felhasználó media erőforrás (fiók) hozzáférést biztosíthat az új felhasználók vagy alkalmazások számára. A közreműködői szerepkör elér a média-erőforrás.
Jogosulatlan kérelem sikertelen lesz, a 401-es állapotkóddal. Ha a hiba kódja, ellenőrizze, hogy a felhasználó rendelkezik-e a felhasználó a Media Services-fiók közreműködői vagy a tulajdonos szerep megjelenik. Ezt ellenőrizheti az Azure portálon. Keresse meg az adathordozó-fiókját, és kattintson a **hozzáférés-vezérlés** fülre. 

![Hozzáférés-vezérlési lap](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Típusú hitelesítés 
 
Az Azure AD-alapú hitelesítés használatakor az Azure Media Services hitelesítési két lehetőség közül választhat:

- **Felhasználó hitelesítése**. Hitelesítés a személy, aki használja az alkalmazás interakciót Media Services-erőforrásokkal. Az interaktív alkalmazás először kell kérni a felhasználót a felhasználói hitelesítő adatokat. Példa: egy kódolási feladatok figyelése, vagy live streaming jogosult felhasználók által használt felügyeleti konzol alkalmazást. 
- **Szolgáltatás egyszerű hitelesítési**. A szolgáltatás hitelesítéséhez. Ezt a hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, démon szolgáltatások, a középső rétegbeli szolgáltatások vagy az ütemezett feladatok futtatásához. Többek között a webalkalmazások, függvény alkalmazások, a logic apps, API és mikroszolgáltatások létrehozására.

### <a name="user-authentication"></a>Felhasználóhitelesítés 

Alkalmazásokat, amelyek a hitelesítési módszert kell használnia: a felügyeleti vagy a natív alkalmazások figyeléséről: mobilalkalmazások, a Windows-alkalmazások és a konzol alkalmazások. Az ilyen típusú megoldást akkor hasznos, ha azt szeretné, hogy a szolgáltatás a következő helyzetek egyikében emberi beavatkozást igényel:

- Figyelési irányítópult a kódolási feladatokhoz.
- Az élő adatfolyamok a figyelési irányítópulton.
- Felügyeleti alkalmazás a Media Services-fiók erőforrások felügyelete asztali és mobil felhasználók számára.

> [!NOTE]
> Ezt a hitelesítési módszert felhasználók felé néző alkalmazásokban kell nem használhatók. 

Egy natív alkalmazás kell először szerezni az Azure ad-hozzáférési tokent, és használja azt, hogy a HTTP-kérelmek a Media Services REST API-t. A hozzáférési jogkivonat hozzáadása a kérelem fejlécében. 

Az alábbi ábrán egy tipikus interaktív alkalmazások hitelesítési folyamat: 

![Natív alkalmazások diagramja](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

A fenti ábrán számok jelölik a folyamatot, a kérelmek időrendi sorrendben.

> [!NOTE]
> Ha a felhasználó a hitelesítési módszert használja, minden alkalmazás osztják meg a natív alkalmazás ügyfél-azonosító ugyanazon (alapértelmezett) és a natív alkalmazás átirányítási URI-t. 

1. A felhasználók hitelesítő adatainak kéréséhez.
2. A kérelem egy Azure AD hozzáférési jogkivonat a következő paraméterekkel:  

    * Azure AD-bérlő végpont.

        A bérlői adatait az Azure portálról lehet beolvasni. Tegye oda a kurzort az első a bejelentkezett felhasználó neve sarokban.
    * A Media Services erőforrás URI azonosítója. 

        Ezt az URI megegyezik a Media Services-fiókok, amelyek azonos Azure környezetben (például https://rest.media.azure.net).

    * A Media Services (natív) alkalmazás ügyfél-azonosító.
    * Media Services (natív) alkalmazás átirányítási URI-címe.
    * Erőforrás URI azonosítója a többi Media Services szolgáltatásokhoz.
        
        Az URI a REST API-végpontot jelenti (például https://test03.restv2.westus.media.azure.net/api/).

    Értékek ezeket a paramétereket, megtekinteni [az Azure AD hitelesítési beállítások eléréséhez használja az Azure-portálon](media-services-portal-get-started-with-aad.md) a felhasználó-hitelesítési beállítás használatával.

3. Az Azure AD hozzáférési jogkivonatot az ügyfél küldi.
4. Az ügyfél kérést küld az Azure Media REST API az Azure AD hozzáférési jogkivonat.
5. Az ügyfél megkapja az adatokat a Media Services.

A Media Services .NET SDK ügyfél REST-kérelmek kommunikálni az Azure AD-alapú hitelesítés használatával kapcsolatos információkért lásd: [eléréséhez a Media Services API a .NET hitelesítés használata az Azure AD](media-services-dotnet-get-started-with-aad.md). 

Ha nem használ a Media Services .NET SDK ügyfél, manuálisan kell létrehoznia az Azure AD hozzáférési jogkivonat kérelem által a 2. lépésben ismertetett paraméterekkel. További információkért lásd: [hogyan használható az Azure AD Authentication Library az Azure AD-token beszerzése](../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Ezt a hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyeket a középső rétegbeli szolgáltatások és az ütemezett feladatok futtatásához: web apps, függvény alkalmazások, a logic apps, API-k és mikroszolgáltatások létrehozására. Ez a hitelesítési módszer is alkalmas interaktív alkalmazások, ahol előfordulhat, hogy használni kívánt szolgáltatásfiók-erőforrások kezeléséhez.

A szolgáltatás egyszerű hitelesítési módszert hozhat létre a fogyasztói forgatókönyveket használatakor hitelesítési általában kell kezelni a középső réteg (néhány API-n) keresztül, és nem közvetlenül a hordozható vagy asztali alkalmazás. 

Ezt a módszert használja, hozzon létre egy Azure AD-alkalmazást és a szolgáltatás egyszerű saját bérlőben. Miután létrehozta az alkalmazást, az alkalmazás közreműködő vagy a tulajdonos szerepkör hozzáférés biztosítják a Media Services-fiók. Ehhez az Azure-portálon az Azure parancssori felület használatával, vagy egy PowerShell-parancsfájlt. Egy Azure AD-alkalmazást is használhatja. Regisztrálhat és kezeli az Azure AD alkalmazás- és egyszerű szolgáltatás [az Azure portálon](media-services-portal-get-started-with-aad.md). Is ehhez a [Azure CLI 2.0](media-services-use-aad-auth-to-access-ams-api.md) vagy [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Középső rétegbeli alkalmazások](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Miután létrehozta az Azure AD-alkalmazást, a következő beállítások beolvasása értékeket. A hitelesítéshez kell ezeket az értékeket:

- Ügyfél-azonosító 
- Titkos ügyfélkulcs 

A számok az előző ábrán mutatják be a folyamatot, a kérelmek időrendi sorrendben:
    
1. A középső rétegbeli alkalmazás (webes API-t vagy webalkalmazás) kér egy Azure AD hozzáférési jogkivonatot, amely rendelkezik a következő paraméterekkel:  

    * Azure AD-bérlő végpont.

        A bérlői adatait az Azure portálról lehet beolvasni. Tegye oda a kurzort az első a bejelentkezett felhasználó neve sarokban.
    * A Media Services erőforrás URI azonosítója. 

        Ezt az URI megegyezik a Media Services fiókoknak ugyanabban az Azure környezetben (például https://rest.media.azure.net).

    * Erőforrás URI azonosítója a többi Media Services szolgáltatásokhoz.

        Az URI a REST API-végpontot jelenti (például https://test03.restv2.westus.media.azure.net/api/).

    * Az Azure AD alkalmazás értékek: az ügyfél-azonosító és a titkos ügyfélkódot.
    
    Értékek ezeket a paramétereket, megtekinteni [az Azure AD hitelesítési beállítások eléréséhez használja az Azure-portálon](media-services-portal-get-started-with-aad.md) a szolgáltatás egyszerű hitelesítés lehetőséget.

2. A középső réteg küld az Azure AD hozzáférési jogkivonat.
4. A középső réteg kérést küld az Azure Media REST API-t az Azure AD-tokenhez.
5. A középső réteg vissza az adatok lekérése a Media Services.

A Media Services .NET SDK ügyfél REST-kérelmek kommunikálni az Azure AD-alapú hitelesítés használatával kapcsolatos további információkért lásd: [elérni az Azure Media Services API a .NET hitelesítés használata az Azure AD](media-services-dotnet-get-started-with-aad.md). 

Ha nem használ a Media Services .NET SDK ügyfél, manuálisan kell létrehoznia az Azure AD-jogkivonatkérelem az 1. lépésben leírt paraméterek használatával. További információkért lásd: [hogyan használható az Azure AD Authentication Library az Azure AD-token beszerzése](../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Hibaelhárítás

Kivétel: "a távoli kiszolgáló hibát adott vissza: (401) nem engedélyezett."

Megoldás: A Media Services REST kérelem sikeres, a hívó felhasználónak kell lennie a Media Services-fiók elérésére tett kísérlet közreműködő vagy a tulajdonos szerepet. További információkért lásd: a [hozzáférés-vezérlés](media-services-use-aad-auth-to-access-ams-api.md#access-control) szakasz.

## <a name="resources"></a>További források

A következő cikkekben az Azure AD hitelesítési fogalmak áttekintése: 

- [Az Azure AD által érintett hitelesítési forgatókönyvek](../active-directory/develop/active-directory-authentication-scenarios.md#basics-of-authentication-in-azure-ad)
- [Hozzáadása, módosítása vagy az Azure AD alkalmazás eltávolítása](../active-directory/develop/active-directory-integrating-applications.md)
- [Konfigurálja és a szerepköralapú hozzáférés-vezérlés kezelése a PowerShell használatával](../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>További lépések

* Az Azure-portálon [hozzáférés az Azure AD hitelesítési Azure Media Services API felhasználásához](media-services-portal-get-started-with-aad.md).
* A hitelesítés használata az Azure AD [elérni az Azure Media Services API a .NET](media-services-dotnet-get-started-with-aad.md).

