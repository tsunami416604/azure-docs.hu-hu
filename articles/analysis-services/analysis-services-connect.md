---
title: Az Azure Analysis Services-kiszolgálók csatlakoztatása |} A Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat, és az Analysis Services-kiszolgáló, az Azure-ban adatok beolvasása.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 19f5fb7b779f538f46b1813f30795e01a75f065c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443256"
---
# <a name="connecting-to-servers"></a>Kapcsolódás kiszolgálókhoz

Ez a cikk ismerteti a kiszolgálóhoz való kapcsolódás, az adatmodellezést és felügyeleti alkalmazások, például az SQL Server Management Studio (SSMS) vagy SQL Server Data Tools (SSDT) használatával. Vagy az ügyfél jelentési rendszereiben, például Microsoft Excel, a Power BI Desktop vagy az egyéni alkalmazások. Az Azure Analysis Services kapcsolatok HTTPS használatára.

## <a name="client-libraries"></a>Ügyfélkódtárak
[A legújabb ügyfélkódtárak beszerzése](analysis-services-data-providers.md)

A kiszolgálón, függetlenül attól, írja be, minden kapcsolat frissített AMO ADOMD.NET és OLEDB ügyfélkódtárral is csatlakozhat, és felület az Analysis Services-kiszolgáló szükséges. Az ssms-ben, az SSDT, az Excel 2016 és a Power BI a legújabb klienskódtárak telepítésekor vagy havi kiadások frissül. Bizonyos esetekben célszerű azonban lehetséges egy alkalmazás nem rendelkezik a legújabb. Például amikor frissíti a késleltetési szabályzatok, vagy az Office 365-frissítések, amelyeket a késleltetett csatornán.

## <a name="server-name"></a>Kiszolgálónév

Az Azure Analysis Services-kiszolgáló létrehozásakor megadhatja egy egyedi nevet és a régióban, ahol a kiszolgáló hozható létre. A kiszolgálónév megadása kapcsolaton keresztül, a kiszolgáló elnevezési sémája a következő:

```
<protocol>://<region>/<servername>
```
 Ahol protokollja karakterlánc **asazure**, régió az URI-t, ahol a kiszolgáló létrejött (például westus.asazure.windows.net), és kiszolgálónév a régión belül egyedi kiszolgáló nevét.

### <a name="get-the-server-name"></a>A kiszolgáló nevének lekérése
A **az Azure portal** > kiszolgáló > **áttekintése** > **kiszolgálónév**, másolja ki a teljes kiszolgálónevet. Ha a szervezet más felhasználói túl ehhez a kiszolgálóhoz csatlakoznak, megoszthatja a kiszolgálónév őket. Adjon meg egy kiszolgálónevet, amikor a teljes elérési útját kell használni.

![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-deploy/aas-deploy-get-server-name.png)


## <a name="connection-string"></a>Kapcsolati sztring

Azure Analysis Services segítségével a táblázatos objektummodell való csatlakozáskor használja a következő kapcsolati karakterlánc-formátumok:

###### <a name="integrated-azure-active-directory-authentication"></a>Integrált Azure Active Directory-hitelesítés
Integrált hitelesítés szerzi be az Azure Active Directory hitelesítőadat-gyorsítótárban, ha elérhető. Ha nem, az Azure bejelentkezési ablak jelenik meg.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Az Azure Active Directory-hitelesítést a felhasználónévvel és jelszóval

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows-hitelesítést (beépített biztonság)
A jelenlegi folyamat futó Windows-fiókot használni.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```



## <a name="connect-using-an-odc-file"></a>Csatlakozás egy .odc-fájl használatával
Az Excel régebbi verziói a felhasználók Office Data Connection (.odc) fájl segítségével kapcsolódhat egy Azure Analysis Services-kiszolgálóra. További tudnivalókért lásd: [Office Data Connection (.odc) fájl létrehozása](analysis-services-odc.md).


## <a name="next-steps"></a>További lépések
[Csatlakozás az Excellel](analysis-services-connect-excel.md)    
[A Power BI](analysis-services-connect-pbi.md)   
[Kiszolgáló kezelése](analysis-services-manage.md)   

