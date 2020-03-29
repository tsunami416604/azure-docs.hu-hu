---
title: Hitelesítés az Azure Data Lake Storage Gen1 szolgáltatásban az Azure Active Directory használatával | Microsoft dokumentumok
description: Megtudhatja, hogyan hitelesítheti magát az Azure Data Lake Storage Gen1 szolgáltatással az Azure Active Directory használatával
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60193594"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Hitelesítés az Azure Data Lake Storage Gen1 használatával az Azure Active Directory használatával

Az Azure Data Lake Storage Gen1 az Azure Active Directoryt használja a hitelesítéshez. A Data Lake Storage Gen1-tel működő alkalmazás létrehozása előtt el kell döntenie, hogyan hitelesítheti az alkalmazást az Azure Active Directoryval (Azure AD).

## <a name="authentication-options"></a>Hitelesítési lehetőségek

* Végfelhasználói hitelesítés – A végfelhasználó **Azure-hitelesítő** adatait a Data Lake Storage Gen1 hitelesítésére használják. A Data Lake Storage Gen1 használatával létrehozott alkalmazás kéri ezeket a felhasználói hitelesítő adatokat. Ennek eredményeképpen ez a hitelesítési mechanizmus *interaktív,* és az alkalmazás a bejelentkezett felhasználó környezetében fut. További információt és utasításokat a [Data Lake Storage Gen1 végfelhasználói hitelesítése című témakörben talál.](data-lake-store-end-user-authenticate-using-active-directory.md)

* **Szolgáltatás-szolgáltatás hitelesítése** – Ezt a beállítást akkor használja, ha azt szeretné, hogy egy alkalmazás hitelesítse magát a Data Lake Storage Gen1 szolgáltatással. Ilyen esetekben hozzon létre egy Azure Active Directory (AD) alkalmazást, és használja a kulcsot az Azure AD-alkalmazás ból a Data Lake Storage Gen1 hitelesítéséhez. Ennek eredményeképpen ez a hitelesítési mechanizmus *nem interaktív.* További információt és utasításokat a [Data Lake Storage Gen1 szolgáltatás-szolgáltatás hitelesítése című témakörben talál.](data-lake-store-service-to-service-authenticate-using-active-directory.md)

Az alábbi táblázat bemutatja, hogyan támogatottak a végfelhasználók és a szolgáltatások között a hitelesítési mechanizmusok a Data Lake Storage Gen1 számára. Így olvashatja az asztalt.

* A ✔* szimbólum azt jelzi, hogy a hitelesítési lehetőség támogatott, és egy olyan cikkre hivatkozik, amely bemutatja a hitelesítési beállítás használatát. 
* A ✔ szimbólum azt jelzi, hogy a hitelesítési beállítás támogatott. 
* Az üres cellák azt jelölik, hogy a hitelesítési beállítás nem támogatott.


|Használja ezt a hitelesítési lehetőséget...                   |.NET         |Java     |PowerShell |Azure CLI | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Végfelhasználó (MFA**nélkül)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(elavult)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Végfelhasználó (MFA-val)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Szolgáltatás-szolgáltatás (ügyfélkulcs használatával)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Szolgáltatás-szolgáltatás (ügyféltanúsítvány használatával) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Kattintson a <b>✔\* </b> szimbólum. Ez egy kapcsolat.</i><br>
<i>** Az MFA a többtényezős hitelesítést jelenti</i>

[Az Azure Active Directory hitelesítési forgatókönyvei](../active-directory/develop/authentication-scenarios.md) című témakörben további információt talál az Azure Active Directory hitelesítéshez való használatáról.

## <a name="next-steps"></a>További lépések

* [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Szolgáltatások közötti hitelesítés](data-lake-store-service-to-service-authenticate-using-active-directory.md)


