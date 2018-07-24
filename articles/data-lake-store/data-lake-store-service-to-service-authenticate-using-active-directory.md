---
title: 'Szolgáltatások közötti hitelesítés: Azure Active Directoryval a Data Lake Store |} A Microsoft Docs'
description: Ismerje meg, hogyan érhet el a szolgáltatások közötti hitelesítés a Data Lake Store az Azure Active Directoryval
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
ms.openlocfilehash: 1e59ed093417d8761135b946e2fa3f183bb085c9
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215971"
---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Szolgáltatások közötti hitelesítés a Data Lake Store az Azure Active Directoryval
> [!div class="op_single_selector"]
> * [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Szolgáltatások közötti hitelesítés](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Az Azure Data Lake Store az Azure Active Directory-hitelesítéshez. Együttműködik az Azure Data Lake Store alkalmazás szerzői, előtt el kell döntenie, hogyan hitelesítheti az alkalmazását az Azure Active Directoryval (Azure AD). Két fő az elérhető lehetőségek:

* Végfelhasználói hitelesítés 
* Szolgáltatások közötti hitelesítés (Ez a cikk) 

Mindkét ezek a beállítások az alkalmazás éppen biztosított az OAuth 2.0 jogkivonat, amely kapcsolódik az Azure Data Lake Store felé irányuló kérések eredményez.

Ez a cikk ismerteti hogyan hozhat létre egy **szolgáltatások közötti hitelesítés az Azure AD-webalkalmazás**. Az Azure AD-alkalmazás beállítása végfelhasználói hitelesítésével kapcsolatos utasításokért lásd: [végfelhasználói hitelesítés a Data Lake Store az Azure Active Directoryval](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>1. lépés: Hozzon létre egy Active Directory-webalkalmazás

Hozzon létre, és a szolgáltatások közötti hitelesítés az Azure AD webes alkalmazás konfigurálása az Azure Data Lake Store az Azure Active Directoryval. Útmutatásért lásd: [hozzon létre egy Azure AD-alkalmazást](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Során megadott utasítások a fenti hivatkozással, győződjön meg arról, hogy ki **webalkalmazás / API** alkalmazás típusához, az alábbi képernyőképen látható módon:

![Webalkalmazás létrehozása](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "webes alkalmazás létrehozása")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>2. lépés: Alkalmazásazonosítót, a hitelesítési kulcsot és a bérlői azonosító beszerzése
Ha programozott módon jelentkezik be, az alkalmazás szükség van az azonosító. Ha az alkalmazás fut, a saját hitelesítő adatait, hitelesítési kulcs is szükséges.

* Az alkalmazás az Alkalmazásazonosító és hitelesítési kulcs (más néven az ügyfél titkos kulcsát) lekérésével útmutatásért lásd: [Get Alkalmazásazonosító és hitelesítési kulcs](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

* A Bérlőazonosító lekéréséhez kapcsolatos utasításokért lásd: [Bérlőazonosító beszerzése](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder"></a>3. lépés: Az Azure AD-alkalmazást az Azure Data Lake Store-fiók fájl vagy mappa hozzárendelése


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg az Azure Data Lake Store-fiók, amely a korábban létrehozott Azure Active Directory-alkalmazással társítaná.
2. A Data Lake Store-fiók panelén kattintson a **Data Explorer** (Adatkezelő) elemre.
   
    ![Könyvtárak létrehozása a Data Lake Store-fiókban](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "könyvtárak létrehozása a Data Lake-fiók")
3. Az a **adatkezelő** panelen kattintson a fájlra vagy mappára, amelyhez a hozzáférést az Azure AD-alkalmazást, és kattintson a kívánt **hozzáférés**. A fájlhoz történő hozzáférés konfigurálásához kattintson **hozzáférés** származó a **Fájlelőnézet** panelen.
   
    ![Állítsa be a hozzáférés-vezérlési listák Data Lake fájlrendszerben](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "beállítva hozzáférés-vezérlési listák Data Lake fájlrendszer")
4. A **hozzáférés** panel felsorolja a standard és egyéni hozzáférés már hozzá van rendelve a legfelső szintű. Kattintson a **Hozzáadás** ikonra kattintva adja hozzá az egyéni szintű hozzáférés-vezérlési listák.
   
    ![Standard és egyéni hozzáférési listán](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "szabványos és egyéni hozzáférési listázása")
5. Kattintson a **Hozzáadás** ikonra kattintva nyissa meg a **egyéni hozzáférés hozzáadása** panelen. Ezen a panelen kattintson a **felhasználó vagy csoport kiválasztása**, majd **felhasználó vagy csoport kiválasztása** panelen keresse meg a korábban létrehozott Azure Active Directory-alkalmazás. Ha sok csoport közötti kereséshez, a szövegmező felső szűréséhez használja a csoport nevére. Kattintson a csoport hozzáadása, és kattintson a kívánt **kiválasztása**.
   
    ![Csoport hozzáadása](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "csoport hozzáadása")
6. Kattintson a **engedélyek kiválasztása**, válassza ki a megfelelő engedélyeket, és az engedélyek hozzárendelése ACL alapértelmezés szerint szeretné, hogy hozzáférési ACL-t, vagy mindkettőt. Kattintson az **OK** gombra.
   
    ![Engedélyek csoporthoz való hozzárendelése](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "csoporthoz engedélyek hozzárendelése")
   
    A Data Lake Store és az alapértelmezett/hozzáférési ACL-ek engedélyekkel kapcsolatos további információkért lásd: [hozzáférés-vezérlés a Data Lake Store](data-lake-store-access-control.md).
7. Az a **egyéni hozzáférés hozzáadása** panelen kattintson a **OK**. Az újonnan hozzáadott csoportot a hozzá tartozó engedélyek szerepelnek a **hozzáférés** panelen.
   
    ![Engedélyek csoporthoz való hozzárendelése](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "csoporthoz engedélyek hozzárendelése")

> [!NOTE]
> Ha azt tervezi, hogy az Azure Active Directory-alkalmazás egy adott mappához korlátozása, akkor is kell adnia, hogy az Azure Active directory az alkalmazás **Execute** -engedélyt a legfelső szintű fájl létrehozási hozzáférés a .NET használatával SDK-T.

> [!NOTE]
> Ha azt szeretné, az SDK-k használatával hozzon létre egy Data Lake Store-fiókot, az Azure AD webes alkalmazást szerepkörként kell rendelnie ahhoz az erőforráscsoporthoz, amelyben a Data Lake Store-fiók létrehozása.
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
Ebben a cikkben létrehozott egy Azure AD-webalkalmazás, és a szükséges információkat az ügyfélalkalmazások számára a .NET SDK-t, a Java, Python, REST API-t és egyéb használatával szerzői gyűjtött. Most már folytathatja a következő cikkeket, hogy hogyan használható az Azure AD natív alkalmazással először hitelesítés a Data Lake Store, és végezze el a tároló más műveleteket beszélni.

* [Szolgáltatások közötti hitelesítés a Data Lake Store Java használatával](data-lake-store-service-to-service-authenticate-java.md)
* [Szolgáltatások közötti hitelesítés a Data Lake Store .NET SDK használatával](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Szolgáltatások közötti hitelesítés a Data Lake Store a pythonnal](data-lake-store-service-to-service-authenticate-python.md)
* [Szolgáltatások közötti hitelesítés a Data Lake Store REST API használatával](data-lake-store-service-to-service-authenticate-rest-api.md)


