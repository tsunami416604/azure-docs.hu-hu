---
title: "Kapcsolódás Azure Analysis Services |} Microsoft Docs"
description: "Megtudhatja, hogyan csatlakozhat, és adatokat lekérni az Analysis Services-kiszolgálóhoz, az Azure-ban."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: b37f70a0-9166-4173-932d-935d769539d1
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: e93544902d7ef435fb1b94e83700ae4fcd6f91f2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="connect-to-an-azure-analysis-services-server"></a>Egy Azure Analysis Services-kiszolgálóhoz kapcsolódni

Ez a cikk ismerteti az adatok modellezési és a felügyeleti alkalmazások, például az SQL Server Management Studio (SSMS) vagy SQL Server Data Tools (SSDT) használatával a kiszolgálóhoz való kapcsolódás. Vagy az ügyfél jelentés az alkalmazások, például a Microsoft Excel-, Power BI Desktop, vagy egyéni alkalmazások. Az Azure Analysis Services kapcsolatok HTTPS használatára.

## <a name="client-libraries"></a>Klienskódtárak
[A legújabb Klienskódtárak segítségével beolvasása](analysis-services-data-providers.md)

Egy kiszolgálóhoz, típusa, függetlenül az összes kapcsolat szükséges frissített AMO ADOMD.NET és OLEDB ügyféloldali kódtáraknál csatlakozni, és az Analysis Services kiszolgálóval illesztő. A SSMS, a SSDT, az Excel 2016 és a Power bi-ban a legújabb klienskódtárak segítségével van telepítve vagy havi kiadásainak frissítése. Azonban néhány esetben is lehet az alkalmazás nem rendelkezik a legújabb. Például amikor frissíti a házirendek késleltetést vagy az Office 365 frissítések esetén a késleltetett csatorna.

## <a name="server-name"></a>Kiszolgálónév

Egy Analysis Services-kiszolgálóhoz az Azure-ban létrehozásakor megadhatja egy egyedi nevet és a terület, ahol a kiszolgáló hozható létre. A kiszolgálónév megadása a kapcsolatot, a kiszolgáló elnevezési sémát esetén:

```
<protocol>://<region>/<servername>
```
 Ahol-jére protokoll **asazure**, régió az Uri, ahol a kiszolgáló létrejött (például westus.asazure.windows.net), és kiszolgálónév a régión belül egyedi kiszolgáló nevét.

### <a name="get-the-server-name"></a>A kiszolgáló nevét
A **Azure-portálon** > server > **áttekintése** > **kiszolgálónév**, másolja a teljes kiszolgálónevet. Ha a munkahely más felhasználóinak túl ehhez a kiszolgálóhoz kapcsolódik, ezt a kiszolgálónevet megoszthatja velük. Ha a kiszolgáló nevét, a teljes elérési útját kell használni.

![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-deploy/aas-deploy-get-server-name.png)


## <a name="connection-string"></a>Kapcsolati karakterlánc

Azure Analysis Services az objektum táblázatos modell használatával szeretne csatlakozni, használja a következő kapcsolati karakterlánc-formátumairól:

###### <a name="integrated-azure-active-directory-authentication"></a>Integrált Azure Active Directory-hitelesítés
Integrált hitelesítés szerzi be az Azure Active Directory hitelesítő adatok gyorsítótárában, ha elérhető. Ha nem, az Azure bejelentkezési ablak jelenik meg.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Az Azure Active Directory authentication felhasználónévvel és jelszóval

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows-hitelesítés (integrált biztonsági)
Az aktuális folyamat futó Windows-fiókot használjon.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```



## <a name="connect-using-an-odc-file"></a>Csatlakozás .odc fájl használatával
Az Excel régebbi verzióival felhasználók csatlakozhatnak az Azure Analysis Services-kiszolgáló Office Data Connection (.odc) fájl használatával. További tudnivalókért lásd: [Office Data Connection (.odc) fájl létrehozása](analysis-services-odc.md).


## <a name="next-steps"></a>További lépések
[Csatlakozás az Excel használatával](analysis-services-connect-excel.md)    
[Csatlakozás a Power BI](analysis-services-connect-pbi.md)   
[A kiszolgáló kezelése](analysis-services-manage.md)   

