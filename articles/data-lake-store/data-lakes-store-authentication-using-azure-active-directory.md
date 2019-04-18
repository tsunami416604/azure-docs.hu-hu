---
title: Az Azure Active Directory hitelesítést az Azure Data Lake Storage Gen1 |} A Microsoft Docs
description: Az Azure Data Lake Storage Gen1 hitelesítése az Azure Active Directoryval
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
ms.openlocfilehash: f83cf183bee930dd07c707b0eb49125cecd70b84
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58884635"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Hitelesítés az Azure Data Lake Storage Gen1 Azure Active Directory használatával

Az Azure Data Lake Storage Gen1 az Azure Active Directory-hitelesítéshez. Mielőtt egy alkalmazás, amely együttműködik a Data Lake Storage Gen1 szerzői, el kell döntenie, hogyan hitelesítheti az alkalmazását az Azure Active Directoryval (Azure AD).

## <a name="authentication-options"></a>Hitelesítési lehetőségek

* **Végfelhasználói hitelesítés** -hitelesítés a Data Lake Storage Gen1 használhatók a végfelhasználók Azure hitelesítő adatait. A Data Lake Storage Gen1 együttműködve létre az alkalmazás kérni fogja a felhasználói hitelesítő adatokat. Ennek eredményeképpen a hitelesítési mechanizmus van *interaktív* és az alkalmazás fut, a bejelentkezett felhasználó környezetében. További információk és utasítások: [végfelhasználói hitelesítés a Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Szolgáltatások közötti hitelesítés** -használja ezt a beállítást, ha azt szeretné, hogy hitelesítse magát a Data Lake Storage Gen1 alkalmazás. Ezekben az esetekben hozzon létre egy Azure Active Directory (AD) alkalmazást, és a kulcsot az Azure AD-alkalmazás a Data Lake Storage Gen1-hitelesítésre használható. Ennek eredményeképpen a hitelesítési mechanizmus van *nem interaktív*. További információk és utasítások: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md).

Az alábbi táblázat mutatja be, hogyan végfelhasználói és a szolgáltatások közötti hitelesítési mechanizmusok támogatottak a Data Lake Storage Gen1. Itt látható, hogyan olvassa el a táblát.

* A ✔ * szimbólum azt jelzi, hogy támogatott hitelesítési lehetőséget, és hivatkozásokat tartalmaz a cikk azt mutatja be, hogyan használhatja a hitelesítési lehetőséget. 
* A ✔ szimbólum azt jelzi, hogy támogatott-e a hitelesítés lehetőséget. 
* Az üres cellákat jelöl, hogy a hitelesítési lehetőséget nem támogatott.


|Használja ezt a hitelesítési lehetőséget...                   |.NET         |Java     |PowerShell |Azure CLI | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Végfelhasználói (nélküli többtényezős hitelesítés **)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(elavult)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Végfelhasználói (a többtényezős hitelesítés)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Szolgáltatások (ügyfélkulcs használatával)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Szolgáltatások (az ügyféltanúsítványt használja) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Kattintson a <b>✔\* </b> szimbólum. Hivatkozás.</i><br>
<i>** A multi-factor authentication szolgáltatás rövidítése többtényezős hitelesítés</i>

Lásd: [hitelesítési forgatókönyvek az Azure Active Directory](../active-directory/develop/authentication-scenarios.md) további információ az Azure Active Directory használata a hitelesítéshez.

## <a name="next-steps"></a>További lépések

* [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Szolgáltatások közötti hitelesítés](data-lake-store-service-to-service-authenticate-using-active-directory.md)


