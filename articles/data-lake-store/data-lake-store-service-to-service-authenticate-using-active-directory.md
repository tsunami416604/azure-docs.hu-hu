---
title: 'Szolgáltatások közötti hitelesítés: Az Azure Data Lake Storage Gen1 az Azure Active Directoryhoz |} A Microsoft Docs'
description: Ismerje meg, hogyan érhet el a szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 Azure Active Directory használatával
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
ms.openlocfilehash: a7fdcf396f586a65efa17e489d002f1c8847a193
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884992"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 Azure Active Directory használatával
> [!div class="op_single_selector"]
> * [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Szolgáltatások közötti hitelesítés](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Az Azure Data Lake Storage Gen1 az Azure Active Directory-hitelesítéshez. Mielőtt egy alkalmazás, amely együttműködik a Data Lake Storage Gen1 szerzői, el kell döntenie, hogyan hitelesítheti az alkalmazását az Azure Active Directoryval (Azure AD). Két fő az elérhető lehetőségek:

* Végfelhasználói hitelesítés 
* Szolgáltatások közötti hitelesítés (Ez a cikk) 

Mindkét ezek a beállítások az alkalmazás éppen biztosított az OAuth 2.0 jogkivonat, amely a Data Lake Storage Gen1 felé irányuló kérések csatlakoztatható eredményez.

Ez a cikk ismerteti hogyan hozhat létre egy **szolgáltatások közötti hitelesítés az Azure AD-webalkalmazás**. Az Azure AD-alkalmazás beállítása végfelhasználói hitelesítésével kapcsolatos utasításokért lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 az Azure Active Directoryval](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>1. lépés: Az Active Directory-webalkalmazás létrehozása

Létrehozhat és konfigurálhat egy Azure AD webes alkalmazás szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 Azure Active Directory használatával. Útmutatásért lásd: [hozzon létre egy Azure AD-alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md).

Során megadott utasítások a fenti hivatkozással, győződjön meg arról, hogy ki **webalkalmazás / API** alkalmazás típusához, az alábbi képernyőképen látható módon:

![Webalkalmazás létrehozása](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "webes alkalmazás létrehozása")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>2. lépés: Alkalmazás azonosítója, a hitelesítési kulcsot és a bérlői azonosító beszerzése
Ha programozott módon jelentkezik be, az alkalmazás szükség van az azonosító. Ha az alkalmazás fut, a saját hitelesítő adatait, hitelesítési kulcs is szükséges.

* Az alkalmazás az Alkalmazásazonosító és hitelesítési kulcs (más néven az ügyfél titkos kulcsát) lekérésével útmutatásért lásd: [Get Alkalmazásazonosító és hitelesítési kulcs](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).

* A Bérlőazonosító lekéréséhez kapcsolatos utasításokért lásd: [Bérlőazonosító beszerzése](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>3. lépés: Rendelje hozzá az Azure AD-alkalmazást az Azure Data Lake Storage Gen1 fiók fájl vagy mappa


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg a Data Lake Storage Gen1 fiókot, amely a korábban létrehozott Azure Active Directory-alkalmazással társítaná.
2. A Data Lake Storage Gen1 fiók panelén kattintson **adatkezelő**.
   
    ![Könyvtárak létrehozása a Data Lake Storage Gen1 fiókban](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "könyvtárak létrehozása a Data Lake-fiók")
3. Az a **adatkezelő** panelen kattintson a fájlra vagy mappára, amelyhez a hozzáférést az Azure AD-alkalmazást, és kattintson a kívánt **hozzáférés**. A fájlhoz történő hozzáférés konfigurálásához kattintson **hozzáférés** származó a **Fájlelőnézet** panelen.
   
    ![Állítsa be a hozzáférés-vezérlési listák Data Lake fájlrendszerben](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "beállítva hozzáférés-vezérlési listák Data Lake fájlrendszer")
4. A **hozzáférés** panel felsorolja a standard és egyéni hozzáférés már hozzá van rendelve a legfelső szintű. Kattintson a **Hozzáadás** ikonra kattintva adja hozzá az egyéni szintű hozzáférés-vezérlési listák.
   
    ![Standard és egyéni hozzáférési listán](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "szabványos és egyéni hozzáférési listázása")
5. Kattintson a **Hozzáadás** ikonra kattintva nyissa meg a **egyéni hozzáférés hozzáadása** panelen. Ezen a panelen kattintson a **felhasználó vagy csoport kiválasztása**, majd **felhasználó vagy csoport kiválasztása** panelen keresse meg a korábban létrehozott Azure Active Directory-alkalmazás. Ha sok csoport közötti kereséshez, a szövegmező felső szűréséhez használja a csoport nevére. Kattintson a csoport hozzáadása, és kattintson a kívánt **kiválasztása**.
   
    ![Csoport hozzáadása](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "csoport hozzáadása")
6. Kattintson a **engedélyek kiválasztása**, válassza ki a megfelelő engedélyeket, és az engedélyek hozzárendelése ACL alapértelmezés szerint szeretné, hogy hozzáférési ACL-t, vagy mindkettőt. Kattintson az **OK** gombra.
   
    ![Engedélyek csoporthoz való hozzárendelése](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "csoporthoz engedélyek hozzárendelése")
   
    A Data Lake Storage Gen1 és alapértelmezett/hozzáférési ACL-ek engedélyekkel kapcsolatos további információkért lásd: [hozzáférés-vezérlés a Data Lake Storage Gen1](data-lake-store-access-control.md).
7. Az a **egyéni hozzáférés hozzáadása** panelen kattintson a **OK**. Az újonnan hozzáadott csoportot a hozzá tartozó engedélyek szerepelnek a **hozzáférés** panelen.
   
    ![Engedélyek csoporthoz való hozzárendelése](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "csoporthoz engedélyek hozzárendelése")

> [!NOTE]
> Ha azt tervezi, hogy az Azure Active Directory-alkalmazás egy adott mappához korlátozása, akkor is kell adnia, hogy az Azure Active directory az alkalmazás **Execute** -engedélyt a legfelső szintű fájl létrehozási hozzáférés a .NET használatával SDK-T.

> [!NOTE]
> Ha azt szeretné, az SDK-k használatával hozzon létre egy Data Lake Storage Gen1 fiókot, az Azure AD webes alkalmazást szerepkörként kell rendelnie ahhoz az erőforráscsoporthoz, amelyben a Data Lake Storage Gen1 fiókot hoz létre.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>4. lépés: Az OAuth 2.0 jogkivonat-végpont beolvasása (csak a Java-alapú alkalmazások esetén)

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) Active Directory kattintson a bal oldali ablaktáblán.

2. A bal oldali ablaktáblában kattintson **alkalmazásregisztrációk**.

3. Az alkalmazás regisztrációk panel tetején kattintson **végpontok**.

    ![OAuth jogkivonat-végpont](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "OAuth jogkivonat-végpont")

4. Másolja az OAuth 2.0 jogkivonat-végpont végpontok listája.

    ![OAuth jogkivonat-végpont](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "OAuth jogkivonat-végpont")   

## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott egy Azure AD-webalkalmazás, és a szükséges információkat az ügyfélalkalmazások számára a .NET SDK-t, a Java, Python, REST API-t és egyéb használatával szerzői gyűjtött. Most már folytathatja a következő cikkeket, amelyek beszélgetnek az Azure AD natív alkalmazással használatával először hitelesítés a Data Lake Storage Gen1, és végezze el a tároló egyéb műveleteket.

* [Szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 Java használatával](data-lake-store-service-to-service-authenticate-java.md)
* [Szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 Python használatával](data-lake-store-service-to-service-authenticate-python.md)
* [Szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 REST API használatával](data-lake-store-service-to-service-authenticate-rest-api.md)


