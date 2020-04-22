---
title: Csatlakozás Azure Analysis Services-kiszolgálókhoz| Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakozhat az Azure-beli Analysis Services-kiszolgálóhoz, és hogyan szerezhet be adatokat.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cc671629934d80b3e727ca69f9026f534d05e160
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676650"
---
# <a name="connecting-to-servers"></a>Kapcsolódás kiszolgálókhoz

Ez a cikk adatmodellezési és -kezelési alkalmazások, például az SQL Server Management Studio (SSMS) vagy a Visual Studio Analysis Services-projektekkel, illetve ügyféljelentési alkalmazásokkal, például Microsoft Excel, Power BI Desktop vagy egyéni alkalmazásokkal való csatlakozást ismerteti. Az Azure Analysis Services-hez való kapcsolatok HTTPS-t használnak.

## <a name="client-libraries"></a>Ügyfélkódtárak

[A legújabb ügyfélkönyvtárak beszereznie](analysis-services-data-providers.md)

A kiszolgálóval való minden kapcsolat hoz létre, típustól függetlenül, frissített AMO,ADOMD.NET és OLEDB ügyféltárakat igényel az Analysis Services-kiszolgálóhoz való csatlakozáshoz és az azokkal való kapcsolathoz. Az SSMS, a Visual Studio, az Excel 2016 és újabb verziók, valamint a Power BI esetében a legújabb ügyféltárak telepítése vagy frissítése havi kiadásokkal történik. Bizonyos esetekben azonban előfordulhat, hogy egy alkalmazás nem rendelkezik a legújabb. Ha például a házirendek késleltetik a frissítéseket, vagy az Office 365-frissítések a Késleltetett csatornán vannak.

> [!NOTE]
> Az ügyfélkódtárak nem tudnak csatlakozni az Azure Analysis Services-hez olyan proxykiszolgálókon keresztül, amelyek felhasználónevet és jelszót igényelnek. 

## <a name="server-name"></a>Kiszolgálónév

Amikor létrehoz egy Analysis Services-kiszolgálót az Azure-ban, meg kell adnia egy egyedi nevet és azt a régiót, ahol a kiszolgálót létre kell hozni. Amikor a kiszolgáló nevét egy kapcsolatban adja meg, a kiszolgáló elnevezési séma:

```
<protocol>://<region>/<servername>
```
 Ahol a protokoll string **asazure**, régió az Uri, ahol a kiszolgáló t (például westus.asazure.windows.net) és a kiszolgálónév a régión belüli egyedi kiszolgáló neve.

### <a name="get-the-server-name"></a>A kiszolgáló nevének beszereznie

Az **Azure Portalon** > kiszolgáló > **overview** > **kiszolgáló neve,** másolja a teljes kiszolgáló nevét. Ha a szervezet más felhasználói is csatlakoznak ehhez a kiszolgálóhoz, megoszthatja velük ezt a kiszolgálónevet. Kiszolgálónév megadásakor a teljes elérési utat kell használni.

![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Az USA keleti régiójának protokollja az **aspaaseastus2.**

## <a name="connection-string"></a>Kapcsolati sztring

Ha az Azure Analysis Services hez a táblázatos objektummodell használatával csatlakozik, használja a következő kapcsolati karakterlánc-formátumokat:

###### <a name="integrated-azure-active-directory-authentication"></a>Integrált Azure Active Directory-hitelesítés

Az integrált hitelesítés felveszi az Azure Active Directory hitelesítő adatgyorsítótárát, ha elérhető. Ha nem, az Azure bejelentkezési ablak jelenik meg.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Azure Active Directory hitelesítés felhasználónévvel és jelszóval

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows-hitelesítés (integrált biztonság)

Használja az aktuális folyamatot futtató Windows-fiókot.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Csatlakozás .odc fájl használatával

Az Excel régebbi verzióival a felhasználók office-adatkapcsolati (.odc) fájl használatával csatlakozhatnak az Azure Analysis Services-kiszolgálóhoz. További információ: [Office-adatkapcsolat (.odc) fájl létrehozása.](analysis-services-odc.md)


## <a name="next-steps"></a>További lépések

[Csatlakozás az Excellel](analysis-services-connect-excel.md)    
[Csatlakozás a Power BI-val](analysis-services-connect-pbi.md)   
[A kiszolgáló kezelése](analysis-services-manage.md)   

