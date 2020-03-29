---
title: 'Szolgáltatás-szolgáltatás hitelesítés: Azure Data Lake Storage Gen1 az Azure Active Directoryval | Microsoft dokumentumok'
description: Megtudhatja, hogy miként érheti el a szolgáltatás-szolgáltatás hitelesítést az Azure Data Lake Storage Gen1 használatával az Azure Active Directory használatával
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 3fbf2f2540e8f1ca84aad2759b9a1fc790e4065d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241372"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Szolgáltatás-szolgáltatás hitelesítés az Azure Data Lake Storage Gen1 szolgáltatással az Azure Active Directory használatával
> [!div class="op_single_selector"]
> * [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Szolgáltatások közötti hitelesítés](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Az Azure Data Lake Storage Gen1 az Azure Active Directoryt használja a hitelesítéshez. A Data Lake Storage Gen1-tel működő alkalmazás létrehozása előtt el kell döntenie, hogyan hitelesítheti az alkalmazást az Azure Active Directoryval (Azure AD). A két fő lehetőség a következő:

* Végfelhasználói hitelesítés 
* Szolgáltatás-szolgáltatás hitelesítése (ez a cikk) 

Mindkét beállítás eredményeképpen az alkalmazás egy OAuth 2.0-s jogkivonatot kap, amely a Data Lake Storage Gen1-hez küldött minden egyes kérelemhez csatlakozik.

Ez a cikk arról szól, hogyan hozhat létre egy **Azure AD webalkalmazást a szolgáltatás-szolgáltatás hitelesítéshez.** Az Azure AD-alkalmazás konfigurációjával kapcsolatos útmutatást a [Végfelhasználói hitelesítés a Data Lake Storage Gen1 használatával az Azure Active Directory használatával című](data-lake-store-end-user-authenticate-using-active-directory.md)témakörben találja.

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>1. lépés: Active Directory-webalkalmazás létrehozása

Hozzon létre és konfiguráljon egy Azure AD webalkalmazást a szolgáltatás-szolgáltatás hitelesítéshez az Azure Data Lake Storage Gen1 használatával az Azure Active Directory használatával. További információt az [Azure AD-alkalmazás létrehozása című témakörben talál.](../active-directory/develop/howto-create-service-principal-portal.md)

Az előző linken található utasításokat követve győződjön meg arról, hogy az alkalmazás típusához a **Web App / API lehetőséget** választja, ahogy az a következő képernyőképen látható:

![Webalkalmazás létrehozása](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Webalkalmazás létrehozása")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>2. lépés: Alkalmazásazonosító, hitelesítési kulcs és bérlőazonosító beszereznie
Amikor programozott módon bejelentkezik, szüksége van az alkalmazás azonosítójára. Ha az alkalmazás saját hitelesítő adatai alatt fut, hitelesítési kulcsra is szüksége van.

* Az alkalmazásazonosító és a hitelesítési kulcs (más néven ügyféltitok) lekérésével kapcsolatos tudnivalókat az [Alkalmazásazonosító és a hitelesítési kulcs beolvasása című](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)témakörben találja.

* A bérlői azonosító lekérésének módjáról a [Bérlőazonosító kérése](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)című témakörben talál.

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>3. lépés: Az Azure AD-alkalmazás hozzárendelése az Azure Data Lake Storage Gen1 fiókfájlhoz vagy -mappához


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg a Data Lake Storage Gen1 fiókot, amelyet a korábban létrehozott Azure Active Directory-alkalmazáshoz kíván társítani.
2. A Data Lake Storage Gen1 fiókpanelen kattintson az **Adatkezelő**elemre.
   
    ![Könyvtárak létrehozása a Data Lake Storage Gen1 fiókban](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Könyvtárak létrehozása a Data Lake-fiókban")
3. Az **Adatkezelő** panelen kattintson arra a fájlra vagy mappára, amelyhez hozzáférést szeretne biztosítani az Azure AD-alkalmazáshoz, majd kattintson az **Access gombra.** A fájlhoz való hozzáférés konfigurálásához kattintson az **Access** elemre a **Fájlelőnézet** panelen.
   
    ![A CL-ek beállítása a Data Lake fájlrendszeren](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "A CL-ek beállítása a Data Lake fájlrendszeren")
4. Az **Access** panel felsorolja a gyökérhez már hozzárendelt szabványos hozzáférést és egyéni hozzáférést. Egyéni szintű ACL-ok hozzáadásához kattintson a **Hozzáadás** ikonra.
   
    ![Szabványos és egyéni hozzáférés listázása](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Szabványos és egyéni hozzáférés listázása")
5. Kattintson a **Hozzáadás** ikonra az **Egyéni hozzáférés hozzáadása** panel megnyitásához. Ebben a panelen kattintson **a Felhasználó vagy csoport kiválasztása gombra,** majd a Felhasználó vagy csoport **kiválasztása** panelen keresse meg a korábban létrehozott Azure Active Directory-alkalmazást. Ha sok csoportból szeretne keresni, a felső szövegmezősegítségével szűrje a csoport nevét. Kattintson a hozzáadni kívánt csoportra, majd a **Kijelölés gombra.**
   
    ![Csoport hozzáadása](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Csoport hozzáadása")
6. Kattintson az **Engedélyek kiválasztása**gombra, válassza ki az engedélyeket, és hogy az engedélyeket alapértelmezett Hozzáférési kulcsként, hozzáférés-hozzáférési csoportként vagy mindkettőként kívánja-e hozzárendelni. Kattintson az **OK** gombra.
   
    ![Engedélyek hozzárendelése a csoporthoz](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Engedélyek hozzárendelése a csoporthoz")
   
    A Data Lake Storage Gen1 és az Default/Access ACL-ok engedélyeiről a [Hozzáférés-vezérlés a Data Lake Storage Gen1 alkalmazásban](data-lake-store-access-control.md)című témakörben olvashat bővebben.
7. Az **Egyéni hozzáférés hozzáadása** panelen kattintson az **OK**gombra. Az újonnan hozzáadott csoport a társított engedélyekkel az **Access** panelen található.
   
    ![Engedélyek hozzárendelése a csoporthoz](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Engedélyek hozzárendelése a csoporthoz")

> [!NOTE]
> Ha azt tervezi, hogy korlátozza az Azure Active Directory-alkalmazást egy adott mappára, akkor ugyanannak az Azure Active directory alkalmazásnak is meg kell adnia ugyanazt az Azure Active directory **alkalmazást, hogy engedélyezze** a fájllétrehozási hozzáférést a .NET SDK-n keresztül.

> [!NOTE]
> Ha az SDK-k használatával szeretne létrehozni egy Data Lake Storage Gen1-fiókot, az Azure AD webalkalmazást szerepkörként hozzá kell rendelnie ahhoz az erőforráscsoporthoz, amelyben létrehozza a Data Lake Storage Gen1-fiókot.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>4. lépés: Az OAuth 2.0 token végpontjának beszereznie (csak Java-alapú alkalmazások esetén)

1. Jelentkezzen be az [Azure Portalra,](https://portal.azure.com) és kattintson az Active Directory elemre a bal oldali ablaktáblából.

2. A bal oldali ablaktáblában kattintson az **Alkalmazásregisztrációk**elemre.

3. Az alkalmazásregisztrációk panel tetején kattintson a **Végpontok**elemre.

    ![OAuth token-végpont](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "OAuth token-végpont")

4. A végpontok listájából másolja az OAuth 2.0 token végpontját.

    ![OAuth token-végpont](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "OAuth token-végpont")   

## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott egy Azure AD webalkalmazást, és összegyűjtötte a szükséges információkat az ügyfélalkalmazásokban, amelyeket a .NET SDK, Java, Python, REST API stb. Most folytathatja a következő cikkeket, amelyek arról beszélnek, hogyan használhatja az Azure AD natív alkalmazás először hitelesíti a Data Lake Storage Gen1, majd egyéb műveleteket az áruházban.

* [Szolgáltatás-szolgáltatás hitelesítés a Data Lake Storage Gen1 használatával Java használatával](data-lake-store-service-to-service-authenticate-java.md)
* [Szolgáltatás-szolgáltatás hitelesítés a Data Lake Storage Gen1 használatával .](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Szolgáltatás-szolgáltatás hitelesítés a Data Lake Storage Gen1 használatával Python](data-lake-store-service-to-service-authenticate-python.md)
* [Szolgáltatás-szolgáltatás hitelesítés a Data Lake Storage Gen1 szolgáltatással REST API használatával](data-lake-store-service-to-service-authenticate-rest-api.md)


