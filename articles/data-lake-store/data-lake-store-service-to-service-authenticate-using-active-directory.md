---
title: 'Szolgáltatások közötti hitelesítés: Data Lake Store az Azure Active Directoryhoz |} Microsoft Docs'
description: 'Útmutató: az Azure Active Directory használatával a Data Lake Store elérése a szolgáltatások közötti hitelesítés'
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 89ebc804aeabfa76309d37721576e7fe833ea2ce
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625899"
---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Szolgáltatások közötti hitelesítés a Data Lake Store az Azure Active Directoryval
> [!div class="op_single_selector"]
> * [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Szolgáltatások közötti hitelesítés](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Store az Azure Active Directory-hitelesítéshez. Egy alkalmazás, amely az Azure Data Lake Store szerzői előtt el kell döntenie, hogyan hitelesítheti az alkalmazás Azure Active Directory (Azure AD). A két fő elérhető lehetőségek a következők:

* Végfelhasználói hitelesítés 
* Szolgáltatások közötti hitelesítés (Ez a cikk) 

Mindkét ezeket a beállításokat az alkalmazás OAuth 2.0-tokenhez, lekérdezi csatolva az Azure Data Lake Store minden kérelmet alatt megadott eredményez.

Ez a cikk létrehozása bemutat egy **szolgáltatások közötti hitelesítés az Azure AD-webalkalmazás**. Az Azure AD alkalmazás konfigurációja végfelhasználói hitelesítéshez, lásd: [végfelhasználói hitelesítés az Azure Active Directory használatával a Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>1. lépés: Az Active Directory-webalkalmazás létrehozása

Hozzon létre, és a szolgáltatások közötti hitelesítéshez az Azure AD webes alkalmazások konfigurálása az Azure Data Lake Store az Azure Active Directoryval. Útmutatásért lásd: [hozzon létre egy Azure AD-alkalmazást](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Közben megadott utasítások a fenti hivatkozás, gondoskodjon róla, hogy **Web App / API** alkalmazás típusra, a következő képernyőfelvételen látható módon:

![A webalkalmazás létrehozása](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "webalkalmazás létrehozása")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>2. lépés: Le Alkalmazásazonosító, a hitelesítési kulcs és a bérlő azonosítója
Bejelentkezéskor programozott módon, a azonosító szükséges az alkalmazáshoz. Ha az alkalmazás fut, a saját hitelesítő adatait, akkor egy hitelesítési kulcs is szükség.

* Hogyan lehet lekérni az alkalmazás azonosítója és a hitelesítési kulcs (más néven a titkos ügyfélkulcs) az alkalmazáshoz, lásd: [Get alkalmazás Azonosítóját és a hitelesítési kulcs](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Hogyan lehet lekérni a bérlő azonosítója, lásd: [-bérlőazonosító beszerzése](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder"></a>3. lépés:, Rendelje hozzá az Azure AD-alkalmazás az Azure Data Lake Store-fiók fájl vagy mappa


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg az Azure Data Lake Store-fiókot az Azure Active Directory-alkalmazást a korábban létrehozott társítani kívánt.
2. A Data Lake Store-fiók panelén kattintson a **Data Explorer** (Adatkezelő) elemre.
   
    ![Könyvtárak létrehozása a Data Lake Store-fiók](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "könyvtárak létrehozása a Data Lake-fiókban")
3. Az a **adatkezelő** panelen kattintson a fájl vagy mappa számára biztosít hozzáférést az Azure AD-alkalmazást, és kattintson a kívánt **hozzáférés**. A fájlhoz való hozzáférés konfigurálásához kattintson **hozzáférés** a a **fájljának előnézete** panelen.
   
    ![Hozzáférés-vezérlési listák beállítása a Data Lake fájlrendszerben](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "beállítása ACL-ek Data Lake fájlrendszer")
4. A **hozzáférés** panel szabványos hozzáférési és egyéni hozzáférési már hozzá van rendelve a legfelső szintű sorolja fel. Kattintson a **Hozzáadás** ikonra kattintva adja hozzá az egyéni szintű hozzáférés-vezérlési listák.
   
    ![Normál és egyéni hozzáférési listában](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "normál és egyéni hozzáférési listában")
5. Kattintson a **Hozzáadás** ikonra kattintva nyissa meg a **egyéni hozzáférés hozzáadása** panelen. Ezen a panelen kattintson a **felhasználó vagy csoport kiválasztása**, majd a **felhasználó vagy csoport kiválasztása** panelen keresse meg a korábban létrehozott Azure Active Directory-alkalmazást. Ha a keresés sok csoportok, a szövegmező felső szűréséhez használja a csoport nevére. Kattintson a csoport hozzáadása, és kattintson a kívánt **válasszon**.
   
    ![Csoport hozzáadása](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "csoport hozzáadása")
6. Kattintson a **Select engedélyeket**, engedélyek kiválasztása, és szeretne hozzárendelni az engedélyeket ACL alapértelmezés szerint, hogy elérhetők a hozzáférés-vezérlési lista, vagy mindkettőt. Kattintson az **OK** gombra.
   
    ![Engedélyeket csoportosításához](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "csoportosításához engedélyek hozzárendelése")
   
    A Data Lake Store és a hozzáférés-vezérlési listákat alapértelmezett/hozzáférési engedélyekkel kapcsolatos további információkért lásd: [hozzáférés-vezérlés a Data Lake Store](data-lake-store-access-control.md).
7. Az a **egyéni hozzáférés hozzáadása** panelen kattintson a **OK**. Az újonnan létrehozott csoport a társított engedélyekkel láthatók a **hozzáférés** panelen.
   
    ![Engedélyeket csoportosításához](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "csoportosításához engedélyek hozzárendelése")

> [!NOTE]
> Ha azt tervezi, az Azure Active Directory-alkalmazás, egy adott mappához korlátozása, is szüksége lesz az adott azonos Azure Active directory-alkalmazás adhat **Execute** permisison a legfelső szintű fájl létrehozása hozzáférésének engedélyezése a. NET SDK.

> [!NOTE]
> Ha azt szeretné, az SDK-k használata a Data Lake Store-fiók létrehozásához, az erőforráscsoporthoz, amelyben a Data Lake Store-fiók létrehozása az Azure AD webalkalmazás szerepkörként kell megadnia.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>4. lépés: Az OAuth 2.0 token-végpont lekérése (csak a Java-alapú alkalmazások)

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) és a bal oldali ablaktáblán kattintson az Active Directory.

2. A bal oldali ablaktáblán kattintson a **App regisztrációk**.

3. Az alkalmazás regisztrációk panel felső részén kattintson **végpontok**.

    ![OAuth jogkivonat végpontjához](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "token OAuth-végpont")

4. Másolja az OAuth 2.0 token-végpont végpontok listáját.

    ![OAuth jogkivonat végpontjához](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "token OAuth-végpont")   

## <a name="next-steps"></a>További lépések
Ebben a cikkben az Azure AD webes alkalmazások és a gyűjtött szükséges információkat az ügyfél alkalmazásokban, hogy szerzőként használata a .NET SDK, Java, Python, REST API-t, stb. Most már folytathatja a következő cikkekben talál, amely először a Data Lake Store hitelesítéséhez, és végezze el más műveleteket végez a tároló az Azure AD-natív alkalmazás használatával kapcsolatban.

* [Szolgáltatások közötti hitelesítés a Data Lake Store Java használatával](data-lake-store-service-to-service-authenticate-java.md)
* [Szolgáltatások közötti hitelesítés a Data Lake Store .NET SDK használatával](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Szolgáltatások közötti hitelesítés a Data Lake Store pythonos környezetekben](data-lake-store-service-to-service-authenticate-python.md)
* [Szolgáltatások közötti hitelesítés a Data Lake Store REST API használatával](data-lake-store-service-to-service-authenticate-rest-api.md)


