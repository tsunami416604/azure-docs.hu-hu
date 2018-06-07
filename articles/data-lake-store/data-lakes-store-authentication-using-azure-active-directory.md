---
title: Hitelesítés az Azure Active Directory használatával a Data Lake Store |} Microsoft Docs
description: Ismerje meg, hogyan hitelesítheti a Data Lake Store az Azure Active Directoryval
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
ms.openlocfilehash: bee65fbdc65807ac33ae425ed9d87dbf0c246d9d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625287"
---
# <a name="authentication-with-data-lake-store-using-azure-active-directory"></a>Hitelesítés a Data Lake Store az Azure Active Directoryval

Azure Data Lake Store az Azure Active Directory-hitelesítéshez. Egy alkalmazás, amely az Azure Data Lake Store szerzői előtt el kell döntenie, hogyan hitelesítheti az alkalmazás Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Hitelesítési lehetőségek

* **Végfelhasználói hitelesítési** – a végfelhasználó Azure hitelesítő adatait használja a Data Lake Store szolgáltatással való hitelesítésre. Az alkalmazás működéséhez a Data Lake Store hoz létre a felhasználói hitelesítő adatok megadását kéri. Ennek eredményeképpen a hitelesítési módszer van *interaktív* és az alkalmazás futtatása a bejelentkezett felhasználó környezetében. További információt és útmutatást lásd: [végfelhasználói hitelesítési a Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Szolgáltatások közötti hitelesítési** -használja ezt a beállítást, ha azt szeretné, hogy hitelesítse magát a Data Lake Store egy alkalmazást. Ilyen esetekben hozzon létre egy Azure Active Directory (AD) alkalmazást, és a Data Lake Store hitelesítéséhez a kulcsot az Azure AD-alkalmazás használ. Ennek eredményeképpen a hitelesítési módszer van *nem interaktív*. További információt és útmutatást lásd: [szolgáltatások közötti hitelesítés a Data Lake Store](data-lake-store-service-to-service-authenticate-using-active-directory.md).

A következő táblázat bemutatja, hogyan végfelhasználói és a szolgáltatások közötti hitelesítési mechanizmusok támogatottak a Data Lake Store. Ez hogyan olvassa el a táblában.

* ✔ * szimbólum azt jelzi, hogy támogatott hitelesítési lehetőséget, és csatolja a cikk bemutatja, hogyan használható a hitelesítés lehetőséget. 
* ✔ szimbólum azt jelzi, hogy a hitelesítési lehetőség. 
* Az üres cellák jelöl, hogy a hitelesítés nem támogatja.


|Használja ezt a hitelesítési lehetőséget...                   |.NET         |Java     |PowerShell |CLI 2.0 | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Végfelhasználói (nélkül MFA **)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(elavult)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Végfelhasználói (a multi-factor Authentication szolgáltatás)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Szolgáltatások (ügyfél-kulcs használatával)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Szolgáltatások (ügyfél-tanúsítvány használatával) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Kattintson a <b>✔\* </b> szimbólum. Egy hivatkozást is.</i><br>
<i>** MFA jelenti a többtényezős hitelesítés</i>

Lásd: [hitelesítési forgatókönyvek az Azure Active Directory](../active-directory/develop/active-directory-authentication-scenarios.md) hitelesítéshez Azure Active Directory használatáról további információt.

## <a name="next-steps"></a>További lépések

* [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Szolgáltatások közötti hitelesítés](data-lake-store-service-to-service-authenticate-using-active-directory.md)


