---
title: Hitelesítés a Data Lake Store az Azure Active Directory |} A Microsoft Docs
description: 'Útmutató: hitelesítés a Data Lake Store az Azure Active Directoryval'
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
ms.openlocfilehash: 7db48b03a6f34655917c82702c12dbc9bc4c987a
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494177"
---
# <a name="authentication-with-data-lake-store-using-azure-active-directory"></a>Hitelesítés a Data Lake Store az Azure Active Directoryval

Az Azure Data Lake Store az Azure Active Directory-hitelesítéshez. Együttműködik az Azure Data Lake Store alkalmazás szerzői, előtt el kell döntenie, hogyan hitelesítheti az alkalmazását az Azure Active Directoryval (Azure AD).

## <a name="authentication-options"></a>Hitelesítési lehetőségek

* **Végfelhasználói hitelesítés** – végfelhasználóval teszteljen Azure hitelesítő adatait használja a Data Lake Store-hitelesítésre. A Data Lake Store használata létre az alkalmazás kérni fogja a felhasználói hitelesítő adatokat. Ennek eredményeképpen a hitelesítési mechanizmus van *interaktív* és az alkalmazás fut, a bejelentkezett felhasználó környezetében. További információk és utasítások: [végfelhasználói hitelesítés a Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Szolgáltatások közötti hitelesítés** -használja ezt a beállítást, ha azt szeretné, hogy hitelesítse magát a Data Lake Store-alkalmazás. Ezekben az esetekben hozzon létre egy Azure Active Directory (AD) alkalmazást, és a kulcsot az Azure AD-alkalmazás a Data Lake Store-hitelesítésre használható. Ennek eredményeképpen a hitelesítési mechanizmus van *nem interaktív*. További információk és utasítások: [szolgáltatások közötti hitelesítés a Data Lake Store](data-lake-store-service-to-service-authenticate-using-active-directory.md).

Az alábbi táblázat mutatja be, hogyan végfelhasználói és a szolgáltatások közötti hitelesítési mechanizmusok támogatottak a Data Lake Store. Itt látható, hogyan olvassa el a táblát.

* A ✔ * szimbólum azt jelzi, hogy támogatott hitelesítési lehetőséget, és hivatkozásokat tartalmaz a cikk azt mutatja be, hogyan használhatja a hitelesítési lehetőséget. 
* A ✔ szimbólum azt jelzi, hogy támogatott-e a hitelesítés lehetőséget. 
* Az üres cellákat jelöl, hogy a hitelesítési lehetőséget nem támogatott.


|Használja ezt a hitelesítési lehetőséget...                   |.NET         |Java     |PowerShell |CLI 2.0 | Python   |REST     |
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


